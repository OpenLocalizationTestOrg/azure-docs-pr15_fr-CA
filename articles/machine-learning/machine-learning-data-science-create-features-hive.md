<properties
    pageTitle="Créer des fonctions pour les données dans un cluster Hadoop à l’aide de requêtes de ruche | Microsoft Azure"
    description="Exemples de requêtes de ruche qui génèrent des fonctionnalités dans les données stockées dans un cluster Azure HDInsight Hadoop."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />


#<a name="create-features-for-data-in-an-hadoop-cluster-using-hive-queries"></a>Créer des fonctions pour les données dans un cluster Hadoop à l’aide de requêtes de la ruche

Ce document montre comment créer des fonctions pour les données stockées dans un cluster Azure HDInsight Hadoop à l’aide de requêtes de la ruche. Ces requêtes de ruche utilisent la ruche utilisateur défini les fonctions incorporées (UDF), les scripts pour lesquels figurent.

Les opérations nécessaires à la création de fonctionnalités peuvent être nécessite beaucoup de mémoire. Les performances des requêtes de ruche devient plus important dans ce cas et peuvent être améliorée par certains paramètres de réglage. Le réglage de ces paramètres est abordé dans la dernière section.

Exemples de requêtes qui sont présentées sont spécifiques aux [Données de voyage de Taxi NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) scénarios sont également disponibles dans [le référentiel de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Ces requêtes déjà le schéma de données spécifié et sont prêts à être soumis à l’exécution. Dans la dernière section, les paramètres que les utilisateurs peuvent régler afin que les performances des requêtes de ruche peuvent être améliorées sont également abordées.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Ce **menu** liens vers des rubriques qui décrivent comment créer des fonctions pour les données dans différents environnements. Cette tâche est une étape dans le [Processus de Science données Team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous avez :

* Créer un compte de stockage Azure. Si vous avez besoin d’instructions, reportez-vous à la section [Création d’un compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Mise en service d’un cluster d’Hadoop personnalisée avec le service HDInsight.  Si vous avez besoin d’instructions, reportez-vous à la section [Personnaliser Azure HDInsight Hadoop Clusters avancée d’Analytique](machine-learning-data-science-customize-hadoop-cluster.md).
* Les données ont été téléchargées vers les tables de ruche dans Azure HDInsight Hadoop clusters. Si ce n’est pas le cas, veuillez suivre [créer et charger des données à des tables de la ruche](machine-learning-data-science-move-hive-tables.md) pour télécharger les données vers les tables de la ruche tout d’abord.
* Activer l’accès distant au cluster. Si vous avez besoin d’instructions, reportez-vous à la section [accès le nœud de tête de Hadoop Cluster](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


##<a name="hive-featureengineering"></a>Génération de la fonctionnalité

Décrites dans cette section, plusieurs exemples de la façon dont fonctionnalités peuvent générer à l’aide de requêtes de la ruche. Une fois que vous avez généré des fonctionnalités supplémentaires, vous pouvez les ajouter en tant que colonnes dans la table existante ou créer une nouvelle table avec les fonctionnalités supplémentaires et d’une clé primaire, qui peut ensuite être jointes à la table d’origine. Voici les exemples présentés :

1. [Fréquence en fonction de génération de la fonctionnalité](#hive-frequencyfeature)
2. [Risques de Variables par catégorie de Classification binaire](#hive-riskfeature)
3. [Extraire les fonctionnalités Datetime Field](#hive-datefeatures)
4. [Extraire les fonctionnalités à partir du champ de texte](#hive-textfeatures)
5. [Calculer la distance entre les coordonnées GPS](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Fréquence en fonction de génération de la fonctionnalité

Il est souvent utile de calculer les fréquences des niveaux d’une variable catégorique, ou les fréquences de certaines combinaisons de niveaux à partir de plusieurs variables par catégorie. Les utilisateurs peuvent utiliser le script suivant pour calculer ces fréquences :

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


###<a name="hive-riskfeature"></a>Risques de Variables par catégorie de Classification binaire

Classement binaire, nous devons convertir les variables par catégorie non numériques en fonctions numériques lorsque les modèles utilisés uniquement prennent les fonctions numériques. Pour ce faire, vous devez remplacer chaque niveau non numérique avec un risque numérique. Dans cette section, nous afficher certaines requêtes ruche génériques qui permettent de calculer les valeurs de risque (journal probable) d’une variable catégorique.


        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

Dans cet exemple, les variables `smooth_param1` et `smooth_param2` sont définies pour lisser les valeurs risque calculés à partir des données. Risques ont une plage comprise entre -Inf et Inf. Risques > 0 indique que la probabilité que la cible est égale à 1 est supérieure à 0,5.

Après le risque table est calculé, les utilisateurs peuvent attribuer des niveaux de risque à une table par l’intégration avec la table risque. La requête de jonction de ruche a été fournie dans la section précédente.

###<a name="hive-datefeatures"></a>Extraire les fonctionnalités Datetime Fields

Ruche est livré avec un ensemble de fichiers UDF pour le traitement des champs datetime. Dans une ruche, le format datetime par défaut est « yyyy-MM-JJ 00:00:00 ' ('01 / 01/1970 12:21:32 ' par exemple). Dans cette section, nous montrent des exemples d’extraire le jour du mois, mois à partir d’un champ date/heure et autres exemples de convertir une chaîne en datetime dans un format autre que le format par défaut pour une chaîne datetime format par défaut.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Cette requête de ruche suppose que le *& #60 ; champ datetime >* est au format de datetime par défaut.

Si un champ date/heure n’est pas dans le format par défaut, vous devez convertir le champ date/heure date et l’heure Unix tout d’abord et l’horodatage Unix puis la convertir en une chaîne datetime qui est dans le format par défaut. Lorsque le format est par défaut la valeur datetime, les utilisateurs peuvent appliquer le datetime incorporé UDF pour extraire les fonctionnalités.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Dans cette requête, si la *& #60 ; champ datetime >* a le modèle comme *26/03/2015 12:04:39*, la *' & #60 ; modèle du champ datetime >'* doit être `'MM/dd/yyyy HH:mm:ss'`. Pour la tester, les utilisateurs peuvent exécuter.

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

La *hivesampletable* dans cette requête est préinstallé sur tous les clusters Azure HDInsight Hadoop par défaut lorsque les clusters sont mis en service.


###<a name="hive-textfeatures"></a>Extraire les fonctionnalités à partir des champs de texte

Lorsque la table de la ruche possède un champ de texte qui contient une chaîne de mots qui sont délimitées par des espaces, la requête suivante extrait la longueur de la chaîne et le nombre de mots dans la chaîne.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Calculer les distances entre les jeux de coordonnées GPS

La requête figure dans cette section peut être appliquée directement aux données de voyage de Taxi NYC. L’objectif de cette requête est de montrer comment appliquer des fonctions mathématiques incorporé dans la ruche pour générer des fonctionnalités.

Les champs qui sont utilisés dans cette requête sont les coordonnées GPS des emplacements de prélèvement et de cette chute, nommés *collecte\_longitude*, *capteur\_latitude*, *cette chute\_longitude*, et *cette chute\_latitude*. Les requêtes qui calculent la distance directe séparant les coordonnées de prélèvement et de cette chute sont les suivantes :

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

Les équations mathématiques qui permettent de calculer la distance entre deux coordonnées GPS vous trouverez sur le site <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> , créé par Peter Lapisu. Dans son Javascript, la fonction `toRad()` est simplement *lat_or_lon*pi/180*, qui convertit des degrés en radians. Ici, *lat_or_lon * est la latitude ou la longitude. Étant donné que la ruche ne fournit pas de la fonction `atan2`, mais fournit la fonction `atan`, le `atan2` fonction est implémentée par `atan` fonction dans la requête de ruche ci-dessus à l’aide de la définition fournie dans <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Créer l’espace de travail](./media/machine-learning-data-science-create-features-hive/atan2new.png)

Une liste complète de ruche UDF incorporés se trouve dans la section **Des fonctions intégrées** sur le <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wiki de la ruche Apache</a>).  

## <a name="tuning"></a>Rubriques avancées : réglage des paramètres de la ruche pour améliorer la vitesse de requête

Les paramètres par défaut de la ruche de cluster n’est peut-être pas appropriés pour les requêtes de la ruche et les données de traitement des requêtes. Dans cette section, nous aborderons certains paramètres que les utilisateurs peuvent régler qui améliorent les performances des requêtes de la ruche. Les utilisateurs doivent ajouter le paramètre réglage des requêtes avant les requêtes de traitement de données.

1. **Espace du tas Java**: pour les requêtes impliquant la jointure de grands ensembles de données ou le traitement des enregistrements de longue, **espace du tas insuffisant** est égale à l’erreur courante. Cela peut être ajustée en définissant les paramètres *mapreduce.map.java.opts* et *mapreduce.task.io.sort.mb* aux valeurs de votre choix. Voici un exemple :

        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;


    Ce paramètre alloue de la mémoire de 4 Go d’espace pour le tas Java et permet également de trier plus efficace en allouant plus de mémoire pour elle. Il est judicieux de jouer avec ces allocations, s’il y a des erreurs d’échec liés à l’espace de tas n’importe quelle tâche.

2. **Taille de bloc de DFS** : ce paramètre définit la plus petite unité de données contenant le système de fichiers. Par exemple, si la taille de bloc DFS est de 128 Mo, puis toutes les données de taille inférieure à et jusqu'à 128 Mo sont stockée dans un seul bloc, pendant que les données qui sont supérieure à 128 Mo alloué des blocs supplémentaires. Vous avez choisi une taille de bloc de très petite, frais généraux de grande taille dans Hadoop car le nœud du nom doit traiter bien plus de demandes pour trouver le bloc pertinent relatives au fichier. Un paramètre recommandé lorsque la matière de gigaoctets (ou plus) sont de données :

        set dfs.block.size=128m;

3. **Optimisation joindre l’opération dans la ruche** : tandis que dans le cadre de mappage/réduction des opérations de jointure sont généralement effectuées lors de la phase de réduction, parfois, des gains énormes est possible en planifiant des jointures dans la phase de mappage (également appelée « mapjoins »). Pour diriger la ruche à cela dès que possible, nous pouvons définir :

        set hive.auto.convert.join=true;

4. **Spécifiant le nombre de mappeurs de ruche** : Hadoop tandis que permet à l’utilisateur de définir le nombre de réducteurs, le nombre de mappeurs est en général ne pas être défini par l’utilisateur. Une astuce permettant un que certain degré de contrôle sur ce numéro consiste à choisir les variables Hadoop, *mapred.min.split.size* et *mapred.max.split.size* en tant que la taille de chaque tâche de mappage est déterminée par :

        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

    En général, la valeur par défaut de *mapred.min.split.size* est 0, celle de *mapred.max.split.size* est **Long.MAX** et celle de *dfs.block.size* est de 64 Mo. Comme nous pouvons le voir, compte tenu de la taille des données, ces paramètres par « paramètre » de réglage les permet d’ajuster le nombre de mappeurs utilisé.

5. Quelques autres davantage d' **options avancées** pour l’optimisation des performances de la ruche sont présentés ci-dessous. Ils vous permettent de définir la mémoire allouée pour mapper et réduire les tâches et peuvent être utiles pour le réglage des performances. Gardez à l’esprit que le *mapreduce.reduce.memory.mb* ne peut pas être supérieure à la taille de la mémoire physique de chaque nœud du cluster Hadoop travailleur.

        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;
