<properties
    pageTitle="Exemple de données dans les tables de la ruche HDInsight Azure | Microsoft Azure"
    description="Vers le bas de l’échantillonnage des données dans les Tables de la ruche Azure HDInsight (Hadopop)"
    services="machine-learning,hdinsight"
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

# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Exemple de données dans les tables de la ruche HDInsight Azure

Dans cet article, nous décrivons comment bas-exemple de données stockées dans les tables de la ruche HDInsight Azure à l’aide de requêtes de la ruche. Nous couvrir les trois méthodes d’échantillonnage communément utilisées :

* Échantillonnage aléatoire uniforme
* Sondage par groupes
* Échantillonnage stratifié

**Pourquoi échantillon de vos données ?**
Si vous envisagez d’analyser le groupe de données est volumineuse, il est généralement conseillé de bas-exemple les données afin de la réduire à une taille plus petite mais représentative et plus faciles à gérer. Cela facilite l’ingénierie de la fonctionnalité, l’exploration et la présentation des données. Son rôle dans le processus de Science de données équipe consiste à activer des prototypage rapide des fonctions de traitement de données et apprentissage des modèles de l’ordinateur.

Le **menu** ci-dessous des liens vers des rubriques qui décrivent l’échantillon de données de différents environnements de stockage.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Cette tâche d’échantillonnage est une étape dans le [Processus de Science données Team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="how-to-submit-hive-queries"></a>Comment soumettre des requêtes de la ruche
Ruche de requêtes peuvent être envoyées à partir de la console de ligne de commande Hadoop sur le nœud principal du cluster Hadoop. Pour ce faire, ouvrez une session dans le nœud de tête du cluster Hadoop, ouvrir la console de ligne de commande Hadoop et soumettre les requêtes de la ruche à partir de là. Pour obtenir des instructions sur l’envoi des requêtes de ruche dans la console de ligne de commande Hadoop, voir [Comment faire pour envoyer les requêtes de la ruche](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="uniform"></a>Échantillonnage aléatoire uniforme
Échantillonnage aléatoire uniforme signifie que chaque ligne du jeu de données a une chance égale d’échantillonnées. Cela peut être implémentée en ajoutant un champ supplémentaire de rand() au jeu de données dans la requête « select » interne et dans la requête « select » externe cette condition sur un champ aléatoire.

Voici un exemple de requête :

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Ici, `<sample rate, 0-1>` spécifie la proportion d’enregistrements que les utilisateurs souhaitent échantillonner.

## <a name="group"></a>Sondage par groupes

Lorsque les données catégoriques d’échantillonnage, vous souhaiterez inclure ou exclure toutes les instances d’une valeur particulière d’une variable catégorique. Il s’agit de signification de « échantillonnage par groupe ».
Par exemple, si vous avez une variable catégorique « État », qui a des valeurs NY, MA, autorité de certification, NJ, PA, etc., à laquelle les enregistrements d’un même état toujours être ensemble, si elles sont échantillonnés ou non.

Voici un exemple de requête qui échantillons par groupe :

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Échantillonnage stratifié

Échantillonnage aléatoire est moyen par rapport à une variable catégorique lorsque les échantillons obtenus ont des valeurs de que par catégorie qui se trouvent dans le même rapport que dans la population parent à partir duquel les échantillons ont été obtenus. En utilisant le même exemple que ci-dessus, supposons que les données contiennent des sous-populations par les États, par exemple NJ a 100 observations, NY a 60 observations et WA a 300 observations. Si vous spécifiez le taux d’échantillonnage stratifié à 0,5, puis l’échantillon obtenu doit avoir environ 50, 30 et 150 observations de NJ, NY et WA respectivement.

Voici un exemple de requête :

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
            rank() over (partition by state order by rand()) as state_rank
        from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Pour plus d’informations sur les méthodes d’échantillonnage plus avancées qui sont disponibles dans la ruche, reportez-vous à la section [D’échantillonnage de LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
