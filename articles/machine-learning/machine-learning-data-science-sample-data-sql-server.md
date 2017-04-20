<properties 
    pageTitle="Exemple de données dans SQL Server sur Azure | Microsoft Azure" 
    description="Exemple de données dans SQL Server sur Azure" 
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
    ms.date="09/19/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Exemple de données dans SQL Server sur Azure


Ce document montre comment échantillonner les données stockées dans SQL Server sur Azure en utilisant SQL ou le langage de programmation Python. Il montre également comment déplacer des données échantillonnées dans Azure Machine Learning en enregistrant dans un fichier et téléchargez-le dans un blob Azure lecture puis dans Azure Machine Learning Studio.

L’échantillonnage de Python utilise la bibliothèque de [pyodbc](https://code.google.com/p/pyodbc/) ODBC pour se connecter à SQL Server sur Azure et la bibliothèque [Pandas](http://pandas.pydata.org/) pour le prélèvement.

>[AZURE.NOTE] L’exemple de code SQL dans ce document part du principe que les données sont dans un SQL Server sur Azure. S’il n’est pas le cas, reportez-vous à la rubrique de [données vers SQL Server sur Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) pour obtenir des instructions sur la façon de déplacer vos données vers SQL Server sur Azure.

**Pourquoi échantillon de vos données ?**
Si vous envisagez d’analyser le groupe de données est volumineuse, il est généralement conseillé de bas-exemple les données afin de la réduire à une taille plus petite mais représentative et plus faciles à gérer. Cela facilite l’ingénierie de la fonctionnalité, l’exploration et la présentation des données. Son rôle dans le [Processus de Science données Team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) est de permettre des prototypage rapide des fonctions de traitement de données et apprentissage des modèles de l’ordinateur.

Le **menu** ci-dessous des liens vers des rubriques qui décrivent l’échantillon de données de différents environnements de stockage. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Cette tâche d’échantillonnage est une étape dans le [Processus de Science données Team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

##<a name="SQL"></a>À l’aide de SQL

Cette section décrit plusieurs méthodes pour effectuer un échantillonnage aléatoire simple sur les données dans la base de données à l’aide de SQL. Veuillez choisir une méthode basée sur la taille des données et de sa distribution.

Les deux éléments ci-dessous illustrent l’utilisation de newid dans SQL Server pour effectuer l’échantillonnage. La méthode que vous choisissez dépend de comment aléatoire vous souhaitez que l’échantillon soit (pk_id dans l’exemple de code ci-dessous est supposé pour être une clé primaire générée automatiquement).

1. Moins strict échantillon aléatoire

        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())

2. Échantillon plus aléatoire 

        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

TABLESAMPLE peut être utilisée pour l’échantillonnage ainsi qu’illustré ci-dessous. Cela peut être une meilleure approche si vos données sont volumineuses (en supposant que les données sur des pages différentes ne sont pas corrélées) et de la requête dans un délai raisonnable.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] Vous pouvez Explorer et générer des fonctions à partir de ces données échantillonnées en les stockant dans une nouvelle table


###<a name="sql-aml"></a>Connexion à l’apprentissage automatique Azure

Vous pouvez utiliser directement les exemples de requêtes ci-dessus dans la formation de Machine Azure [Importer des données] [ import-data] module échantillons vers le bas les données à la volée et importez-les dans une expérience d’apprentissage automatique de Azure. Une capture d’écran de l’utilisation du module de lecteur pour lire des données échantillonnées est illustrée ci-dessous :
   
![lecteur sql][1]

##<a name="python"></a>À l’aide du langage de programmation Python 

Cette section illustre l’utilisation de la [bibliothèque de pyodbc](https://code.google.com/p/pyodbc/) pour établir une application ODBC se connecter à une base de données du serveur SQL dans les Python. La chaîne de connexion de base de données est comme suit : (remplacez nom_serveur, dbname, nom d’utilisateur et mot de passe de configuration) :

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La bibliothèque de [Pandas](http://pandas.pydata.org/) dans les Python fournit un ensemble complet d’outils d’analyse de données et des structures de données pour la manipulation de données pour la programmation de Python. Le code ci-dessous lit un échantillon de 0,1 % des données d’une table de base de données SQL d’Azure dans les données de Pandas :

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Vous pouvez maintenant travailler avec des données échantillonnées dans la trame de données Pandas. 

###<a name="python-aml"></a>Connexion à l’apprentissage automatique Azure

L’exemple de code suivant vous permet d’enregistrer les données sous-échantillonnée dans un fichier et téléchargez-le dans un blob Azure. Lire les données dans le blob peuvent directement dans une expérience de formation de Machine Azure en utilisant les [Données d’importation] [ import-data] module. Les étapes sont les suivantes : 

1. Écrire la trame de données pandas dans un fichier local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Télécharger le fichier local au blob Azure

        from azure.storage import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Lire les données blob Azure Azure Machine Learning, [Importer des données] à l’aide de[ import-data] module comme illustré dans la capture d’écran ci-dessous :
 
![blob de lecteur][2]

## <a name="the-team-data-science-process-in-action-example"></a>Le processus de Science de données équipe dans exemple d’Action

Pour obtenir un exemple de procédure pas à pas de bout en bout du processus informatique de données équipe un à l’aide d’un groupe de données publique, reportez-vous à la section [des processus Science de données d’équipe en Action : à l’aide de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 [import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
