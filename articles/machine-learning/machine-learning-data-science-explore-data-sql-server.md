<properties 
    pageTitle="Explorer des données dans SQL Server Virtual Machine sur Azure | Microsoft Azure" 
    description="Comment Explorer des données qui sont stockées dans un ordinateur virtuel de SQL Server sur Azure." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorer des données dans SQL Server Virtual Machine sur Azure


Ce document explique comment Explorer des données qui sont stockées dans un ordinateur virtuel de SQL Server sur Azure. Ceci est possible en données batailler avec l’aide de SQL ou à l’aide d’un langage de programmation comme les Python.

Les liens de **menu** suivants vers les rubriques qui décrivent comment utiliser les outils pour explorer des données à partir de différents environnements de stockage. Cette tâche est une étape dans le processus Analytique Cortana (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


> [AZURE.NOTE] Les exemples d’instructions SQL dans ce document supposent que les données sont dans SQL Server. Si elle n’est pas le cas, reportez-vous à la cartographie des processus nuage données scientifiques pour savoir comment migrer vos données vers SQL Server.



## <a name="sql-dataexploration"></a>Exploration de données SQL avec les scripts SQL

Voici quelques exemples de scripts SQL qui peuvent être utilisés pour parcourir les magasins de données dans SQL Server.

1. Obtenir le nombre d’observations par jour

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Obtenir les niveaux d’une colonne par catégorie

    `select  distinct <column_name> from <databasename>`

3. Obtenir le nombre de niveaux dans la combinaison de deux colonnes catégorielles 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obtenir la distribution pour des colonnes numériques

    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE] Pour un exemple pratique, vous pouvez utiliser les [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) et reportez-vous à la IPNB intitulé [NYC données batailler avec à l’aide du bloc-notes de IPython et de SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pour une procédure de bout en bout.

##<a name="python"></a>Exploration de données SQL avec les Python

L’utilisation de Python pour Explorer les données et de générer des fonctionnalités lorsque les données sont dans SQL Server est similaire au traitement des données BLOB Azure à l’aide de Python, comme indiqué dans [les données Blob Azure de processus dans votre environnement informatique des données](machine-learning-data-science-process-data-blob.md). Les données doivent être chargées à partir de la base de données dans un DataFrame de pandas et puis seront traitées ultérieurement. Nous documentons le processus de connexion à la base de données et de chargement des données dans la DataFrame de cette section.

Au format de chaîne de connexion suivant peut être utilisé pour se connecter à une base de données SQL Server à partir de Python à l’aide de pyodbc (remplacer nom_serveur, dbname, nom d’utilisateur et mot de passe avec vos valeurs spécifiques) :

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [bibliothèque de Pandas](http://pandas.pydata.org/) dans les Python fournit un ensemble complet d’outils d’analyse de données et des structures de données pour la manipulation de données pour la programmation de Python. Le code suivant lit les résultats renvoyés à partir d’une base de données SQL Server dans une trame de données Pandas :

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Maintenant vous pouvez travailler avec le DataFrame de Pandas comme décrit dans la rubrique [des données Blob Azure de processus dans votre environnement informatique des données](machine-learning-data-science-process-data-blob.md).

## <a name="cortana-analytics-process-in-action-example"></a>Processus Analytique de Cortana dans l’exemple d’Action

Pour obtenir un exemple de procédure pas à pas de bout en bout du processus Analytique Cortana à l’aide d’un groupe de données publique, reportez-vous à la section [du processus Science données d’équipe en action : à l’aide de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

 
