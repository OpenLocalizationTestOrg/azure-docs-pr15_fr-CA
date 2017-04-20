<properties 
    pageTitle="Traitement des données à partir de SQL Azure | Microsoft Azure" 
    description="Données de processus à partir de SQL Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Données de processus dans SQL Server Virtual Machine sur Azure

Ce document décrit comment Explorer des données et de générer des fonctionnalités pour les données stockées dans un ordinateur virtuel de SQL Server sur Azure. Ceci est possible en données batailler avec l’aide de SQL ou à l’aide d’un langage de programmation comme les Python.


> [AZURE.NOTE] Les exemples d’instructions SQL dans ce document supposent que les données sont dans SQL Server. Si elle n’est pas le cas, veuillez vous reporter à la cartographie des processus nuage données scientifiques pour savoir comment migrer vos données vers SQL Server.

##<a name="SQL"></a>À l’aide de SQL

Nous décrire les tâches de wrangling de données suivantes dans cette section à l’aide de SQL :

1. [Exploration de données](#sql-dataexploration)
2. [Génération de la fonctionnalité](#sql-featuregen)

###<a name="sql-dataexploration"></a>Exploration de données
Voici quelques exemples de scripts SQL qui peuvent être utilisés pour parcourir les magasins de données dans SQL Server.


> [AZURE.NOTE] Pour un exemple pratique, vous pouvez utiliser les [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) et reportez-vous à la IPNB intitulé [NYC données batailler avec à l’aide du bloc-notes de IPython et de SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pour une procédure de bout en bout.

1. Obtenir le nombre d’observations par jour

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Obtenir les niveaux d’une colonne par catégorie

    `select  distinct <column_name> from <databasename>`

3. Obtenir le nombre de niveaux dans la combinaison de deux colonnes catégorielles 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obtenir la distribution pour des colonnes numériques

    `select <column_name>, count(*) from <tablename> group by <column_name>`


###<a name="sql-featuregen"></a>Génération de la fonctionnalité

Dans cette section, nous décrivent des méthodes permettant de générer des fonctions à l’aide de SQL :  

1. [Nombre en fonction de génération de la fonctionnalité](#sql-countfeature)
2. [Génération de la fonctionnalité de groupement](#sql-binningfeature)
3. [Les fonctionnalités de déploiement à partir d’une seule colonne](#sql-featurerollout)


> [AZURE.NOTE] Une fois que vous générez des fonctionnalités supplémentaires, vous pouvez les ajouter en tant que colonnes dans la table existante ou créer une nouvelle table avec les fonctionnalités supplémentaires et d’une clé primaire, qui peut être jointes à la table d’origine. 

###<a name="sql-countfeature"></a>Nombre en fonction de génération de la fonctionnalité

Ce document illustre deux manières de générer des fonctionnalités de nombre. La première méthode utilise la somme conditionnelle et la deuxième méthode utilise la clause 'where'. Ils peuvent être joints avec la table d’origine (à l’aide de la clé primaire) pour que le nombre des fonctionnalités ainsi que les données d’origine.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

###<a name="sql-binningfeature"></a>Génération de la fonctionnalité de groupement

L’exemple suivant montre comment générer des fonctionnalités binned par MOYENNAGE (à l’aide de 5 emplacements) une colonne numérique qui peut être utilisée à la place comme une fonction :

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


###<a name="sql-featurerollout"></a>Les fonctionnalités de déploiement à partir d’une seule colonne

Dans cette section, nous apprendre à la déployer une seule colonne d’une table pour générer des fonctionnalités supplémentaires. L’exemple suppose qu’il existe une colonne de latitude ou de longitude de la table à partir de laquelle vous souhaitez générer des fonctionnalités.

Voici une brève introduction sur les données de localisation latitude/longitude (ressource de stackoverflow [comment mesurer la précision de la latitude et la longitude ?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Il est utile de comprendre avant de featurizing le champ emplacement :

- Le signe nous indique que nous sommes Nord ou le Sud, le Moyen-Orient ou l’ouest de la planète.
- Une centaines de zéro chiffre nous indique que nous utilisons de longitude et latitude pas !
- Dizaines à chiffres donne une position à environ 1 000 kilomètres. Il nous donne des informations utiles sur le continent ou d’un océan, nous sommes sur.
- Le chiffre des unités (un degré décimal) donne une position 111 kilomètres (60 milles marins, environ 69 miles). Il peut nous plus ou moins grand état ou pays, dans que nous sommes.
- La première décimale vaut jusqu'à 11.1 km : il peut faire la distinction entre la position d’une grande ville à partir d’une grande ville voisine.
- La deuxième décimale vaut jusqu'à 1.1 km : il peut séparer un village du suivant.
- La troisième décimale vaut jusqu'à 110 m qu'il peut identifier un grand champ agricole ou un campus institutionnel.
- La quatrième décimale vaut jusqu'à 11 m, il peut identifier une parcelle de terrain. Il est comparable à la précision par défaut d’une unité GPS non corrigée avec aucune interférence.
- La cinquième décimale vaut il m: 1.1 à distinguer des arbres de l’autre. Précision à ce niveau avec les unités GPS commerciales est possible uniquement avec correction différentielle.
- La sixième décimale vaut jusqu'à 0,11 m: que vous pouvez l’utiliser pour la pose de structures en détail, pour la conception de paysages, création de routes. Il devrait être plus suffisamment bonne pour le suivi des mouvements de cours d’eau et d’enneigées. Ceci est possible en prenant des mesures attestera avec GPS, comme un GPS DIFFERENTIELLE corrigé.

Les informations d’emplacement peuvent être featurized comme suit, séparant la région, l’emplacement et les informations de ville. Notez que vous pouvez également un point de fin reste comme API Bing Maps disponible à appeler pour obtenir les informations de district/de la région de [trouver un emplacement par Point](https://msdn.microsoft.com/library/ff701710.aspx) .

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1       
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Les fonctionnalités basées sur l’emplacement ci-dessus plus utilisable pour générer des fonctionnalités de nombre supplémentaires comme décrit précédemment. 


> [AZURE.TIP] Vous pouvez insérer par programme des enregistrements à l’aide de la langue de votre choix. Il se pouvez que vous deviez insérer les données en segments pour améliorer l’efficacité d’écriture (pour obtenir un exemple illustrant comment effectuer cette opération à l’aide de pyodbc, consultez [exemple HelloWorld de A à accéder à SQL Server avec les python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Une autre solution consiste à insérer des données dans la base de données à l’aide de l' [utilitaire BCP](https://msdn.microsoft.com/library/ms162802.aspx).

###<a name="sql-aml"></a>Connexion à l’apprentissage automatique Azure

La fonctionnalité qui vient d’être générée peut ajoutée sous la forme d’une colonne à une table existante ou stockée dans une nouvelle table et jointes à la table d’origine pour l’apprentissage de l’ordinateur. Fonctions pouvant être générées ou accessible si déjà créé, en utilisant les [Données d’importation] [ import-data] module d’apprentissage de Machine Azure comme indiqué ci-dessous :

![lecteurs d’azureml][1] 

##<a name="python"></a>À l’aide d’un langage de programmation comme les Python

Python pour Explorer les données et de générer des fonctionnalités lorsque les données sont dans SQL Server est similaire au traitement des données BLOB Azure utilisant les Python comme indiqué dans [les données Blob Azure de processus dans l’environnement informatique données](machine-learning-data-science-process-data-blob.md). Les données doivent être chargées à partir de la base de données dans une trame de données pandas et peuvent ensuite être traitées ultérieurement. Nous documentons le processus de connexion à la base de données et de chargement des données dans le cadre de données dans cette section.

Au format de chaîne de connexion suivant peut être utilisé pour se connecter à une base de données SQL Server à partir de Python à l’aide de pyodbc (remplacer nom_serveur, dbname, nom d’utilisateur et mot de passe avec vos valeurs spécifiques) :

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [bibliothèque de Pandas](http://pandas.pydata.org/) dans les Python fournit un ensemble complet d’outils d’analyse de données et des structures de données pour la manipulation de données pour la programmation de Python. Le code ci-dessous lit les résultats renvoyés à partir d’une base de données SQL Server dans une trame de données Pandas :

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Maintenant vous pouvez travailler avec la trame de données Pandas comme décrit dans l’article [les données Blob Azure de processus dans l’environnement informatique données](machine-learning-data-science-process-data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Science de données Azure dans exemple d’Action

Pour obtenir un exemple de procédure pas à pas de bout en bout du processus de Science de données Azure à l’aide d’un groupe de données publique, consultez [Processus de Science de données Azure en Action](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
