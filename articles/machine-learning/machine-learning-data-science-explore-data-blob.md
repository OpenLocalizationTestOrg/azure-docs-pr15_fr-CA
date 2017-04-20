<properties 
    pageTitle="Explorer les données dans le stockage blob Azure avec Pandas | Microsoft Azure" 
    description="Comment Explorer des données qui sont stockées dans le conteneur de blob Azure à l’aide de Pandas." 
    services="machine-learning,storage" 
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

#<a name="explore-data-in-azure-blob-storage-with-pandas"></a>Explorer les données dans le stockage blob Azure avec Pandas

Ce document explique comment Explorer des données qui sont stockées dans le conteneur d’objet blob Azure utilisant les Python [Pandas](http://pandas.pydata.org/) package.

Les liens de **menu** suivants vers les rubriques qui décrivent comment utiliser les outils pour explorer des données à partir de différents environnements de stockage. Cette tâche est une étape dans le [Processus de données scientifiques]().

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous avez :

* Créer un compte de stockage Azure. Si vous avez besoin d’instructions, reportez-vous à la section [Création d’un compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Vos données sont stockées dans un compte de stockage blob Azure. Si vous avez besoin d’instructions, voir [déplacement des données vers et depuis le stockage Azure](../storage/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Charger les données dans un DataFrame de Pandas
Pour découvrir et manipuler un groupe de données, elle doit tout d’abord être téléchargée à partir de la source de l’objet blob dans un fichier local, qui peut alors être chargé dans un DataFrame de Pandas. Voici les étapes à suivre pour cette procédure :

1. Télécharger les données à partir d’Azure blob avec les Python code ci-après à l’aide du service d’objet blob. Remplacez la variable dans le code suivant avec vos valeurs spécifiques : 

        from azure.storage.blob import BlobService
        import tables
        
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Lire les données d’une trame de données Pandas à partir du fichier téléchargé.

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Vous êtes maintenant prêt à Explorer les données et générer des fonctionnalités sur ce groupe de données.

##<a name="blob-dataexploration"></a>Exemples d’exploration de données à l’aide de Pandas

Voici quelques exemples de plusieurs méthodes d’exploration de données à l’aide de Pandas :

1. Contrôler le **nombre de lignes et de colonnes** 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **Inspecter** le premier ou le dernier premières **lignes** dans le jeu de données suivant :

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Vérifiez le **type de données** de que chaque colonne a été importé à l’aide de l’exemple de code suivant
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Vérifier les **statistiques de base** pour les colonnes dans le jeu de données comme suit.
 
        dataframe_blobdata.describe()
    
5. Examinez le nombre d’entrées pour chaque valeur de colonne comme suit.

        dataframe_blobdata['<column_name>'].value_counts()

6. **Compter les valeurs manquantes** par rapport au nombre réel d’entrées dans chaque colonne à l’aide de l’exemple de code suivant

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Si vous avez **des valeurs manquantes** pour une colonne particulière dans les données, vous pouvez les supprimer comme suit :

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    Une autre façon de remplacer des valeurs manquantes est avec la fonction de mode :
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Créer un tracé de **l’histogramme** à l’aide d’un nombre variable d’emplacements pour tracer la distribution d’une variable 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Examinez les **corrélations** entre les variables à l’aide d’un scatterplot ou à l’aide de la fonction de corrélation intégrée

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
