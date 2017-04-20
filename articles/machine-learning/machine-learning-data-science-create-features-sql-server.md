<properties
    pageTitle="Créer des fonctions de données dans SQL Server à l’aide de SQL et les Python | Microsoft Azure"
    description="Données de processus à partir de SQL Azure"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev;fashah;garye" />


# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Créer des fonctions de données dans SQL Server à l’aide de SQL et les Python


Ce document montre comment générer des fonctionnalités pour les données stockées dans un ordinateur virtuel de SQL Server sur Azure et d’apprendre des algorithmes plus efficacement à partir des données. Ceci est possible à l’aide de SQL ou à l’aide d’un langage de programmation comme les Python, qui sont illustrées ici.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Ce **menu** liens vers des rubriques qui décrivent comment créer des fonctions pour les données dans différents environnements. Cette tâche est une étape dans le [Processus de Science données Team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [AZURE.NOTE] Pour obtenir un exemple pratique, vous pouvez consulter le [groupe de données NYC Taxi](http://www.andresmh.com/nyctaxitrips/) et reportez-vous à la IPNB intitulé [NYC données batailler avec à l’aide du bloc-notes de IPython et de SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pour une procédure de bout en bout.


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous avez :

* Créer un compte de stockage Azure. Si vous avez besoin d’instructions, reportez-vous à la section [Création d’un compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Vos données sont stockées dans SQL Server. Si vous ne l’avez pas, consultez [déplacer des données d’une base de données de SQL Azure pour l’apprentissage de Machine Azure](machine-learning-data-science-move-sql-azure.md) pour obtenir des instructions sur la façon d’y déplacer les données.


## <a name="sql-featuregen"></a>Génération de la fonctionnalité avec SQL

Dans cette section, nous décrivent des méthodes permettant de générer des fonctions à l’aide de SQL :  

1. [Nombre en fonction de génération de la fonctionnalité](#sql-countfeature)
2. [Génération de la fonctionnalité de groupement](#sql-binningfeature)
3. [Les fonctionnalités de déploiement à partir d’une seule colonne](#sql-featurerollout)


> [AZURE.NOTE] Une fois que vous générez des fonctionnalités supplémentaires, vous pouvez les ajouter en tant que colonnes dans la table existante ou créer une nouvelle table avec les fonctionnalités supplémentaires et d’une clé primaire, qui peut être jointes à la table d’origine.

### <a name="sql-countfeature"></a>Nombre en fonction de génération de la fonctionnalité

Ce document illustre deux manières de générer des fonctionnalités de nombre. La première méthode utilise la somme conditionnelle et la deuxième méthode utilise la clause 'where'. Ils peuvent être joints avec la table d’origine (à l’aide de la clé primaire) pour que le nombre des fonctionnalités ainsi que les données d’origine.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Génération de la fonctionnalité de groupement

L’exemple suivant montre comment générer des fonctionnalités binned par MOYENNAGE (à l’aide de 5 emplacements) une colonne numérique qui peut être utilisée à la place comme une fonction :

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Les fonctionnalités de déploiement à partir d’une seule colonne

Dans cette section, nous apprendre à la déployer une seule colonne d’une table pour générer des fonctionnalités supplémentaires. L’exemple suppose qu’il existe une colonne de latitude ou de longitude de la table à partir de laquelle vous souhaitez générer des fonctionnalités.

Voici une brève introduction sur les données de localisation latitude/longitude (ressource de stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Il est utile de comprendre avant de featurizing le champ emplacement :

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

Les informations d’emplacement peuvent peut être featurized, comme suit, séparant la région, l’emplacement et les informations de ville. Notez qu’une seule fois peut également appeler un point de fin reste comme API Bing Maps, disponible à l’adresse `https://msdn.microsoft.com/library/ff701710.aspx` pour obtenir les informations de district/de la région.

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

Les fonctionnalités selon l’emplacement ci-dessus plus utilisable pour générer des fonctionnalités de nombre supplémentaires comme décrit précédemment.


> [AZURE.TIP] Vous pouvez insérer par programme des enregistrements à l’aide de la langue de votre choix. Vous devrez insérer les données en segments pour améliorer l’efficacité d’écriture [extraction de l’exemple de cette procédure à l’aide de pyodbc ici](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
Une autre solution consiste à insérer des données dans la base de données à l’aide [d’utilitaire BCP](https://msdn.microsoft.com/library/ms162802.aspx)

### <a name="sql-aml"></a>Connexion à l’apprentissage automatique Azure

La fonctionnalité qui vient d’être générée peut ajoutée sous la forme d’une colonne à une table existante ou stockée dans une nouvelle table et jointes à la table d’origine pour l’apprentissage de l’ordinateur. Fonctions pouvant être générées ou accessible si déjà créé, en utilisant le module [Importation de données](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) dans Azure ML comme indiqué ci-dessous :

![lecteurs d’azureml](./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>À l’aide d’un langage de programmation comme les Python

Il est similaire au traitement des données BLOB Azure utilisant les Python comme indiqué dans [les données Blob Azure de processus dans l’environnement informatique données](machine-learning-data-science-process-data-blob.md)d’utilisant les Python pour générer des fonctionnalités lorsque les données sont dans SQL Server. Les données doivent être chargées à partir de la base de données dans une trame de données pandas et peuvent ensuite être traitées ultérieurement. Nous documentons le processus de connexion à la base de données et de chargement des données dans le cadre de données dans cette section.

Au format de chaîne de connexion suivant peut être utilisé pour se connecter à une base de données SQL Server à partir de Python à l’aide de pyodbc (remplacer nom_serveur, dbname, nom d’utilisateur et mot de passe avec vos valeurs spécifiques) :

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [bibliothèque de Pandas](http://pandas.pydata.org/) dans les Python fournit un ensemble complet d’outils d’analyse de données et des structures de données pour la manipulation de données pour la programmation de Python. Le code ci-dessous lit les résultats renvoyés à partir d’une base de données SQL Server dans une trame de données Pandas :

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Maintenant vous pouvez travailler avec la trame de données Pandas comme décrit dans les rubriques [créer des fonctions pour les données de stockage Azure blob à l’aide de Panda](machine-learning-data-science-create-features-blob.md).
