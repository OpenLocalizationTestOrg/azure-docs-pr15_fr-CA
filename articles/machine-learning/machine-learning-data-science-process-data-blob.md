<properties 
    pageTitle="Traiter les données blob Azure avec analytique avancé | Microsoft Azure" 
    description="Données de processus dans le stockage des objets Blob Azure." 
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
    ms.date="09/19/2016"
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Traiter les données blob Azure avec analytique avancée

Ce document traite d’exploration de données et des fonctionnalités de génération à partir de données stockées dans le stockage des objets Blob Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Charger les données dans une trame de données Pandas
Pour découvrir et manipuler un groupe de données, elle doit être téléchargé à partir de la source de l’objet blob dans un fichier local qui peut alors être chargé dans une trame de données Pandas. Voici les étapes à suivre pour cette procédure :

1. Télécharger les données à partir d’Azure blob avec le code Python suivant à l’aide du service d’objet blob. Remplacez la variable dans le code ci-dessous avec vos valeurs spécifiques : 

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


##<a name="blob-dataexploration"></a>Exploration de données

Voici quelques exemples de plusieurs méthodes d’exploration de données à l’aide de Pandas :

1. Contrôler le nombre de lignes et de colonnes 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. Vérifiez que les premier ou le derniers premières lignes dans le dataset, comme indiqué ci-dessous :

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Vérifiez le type de données que chaque colonne a été importé à l’aide de l’exemple de code suivant
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Vérifier les statistiques de base pour les colonnes dans le jeu de données comme suit.
 
        dataframe_blobdata.describe()
    
5. Examinez le nombre d’entrées pour chaque valeur de colonne comme suit.

        dataframe_blobdata['<column_name>'].value_counts()

6. Compter des valeurs manquantes par rapport au nombre réel d’entrées dans chaque colonne à l’aide de l’exemple de code suivant

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Si vous avez des valeurs manquantes pour une colonne particulière dans les données, vous pouvez les supprimer comme suit :

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    Une autre façon de remplacer des valeurs manquantes est avec la fonction de mode :
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Créer un tracé de l’histogramme à l’aide d’un nombre variable d’emplacements pour tracer la distribution d’une variable 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Examinez les corrélations entre les variables à l’aide d’un scatterplot ou à l’aide de la fonction de corrélation intégrée

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    
    
##<a name="blob-featuregen"></a>Génération de la fonctionnalité
    
Nous pouvons générer des fonctionnalités utilisant les Python comme suit :

###<a name="blob-countfeature"></a>Valeur de l’indicateur en fonction de génération de la fonctionnalité

Les fonctionnalités par catégorie peuvent être créées comme suit :

1. Contrôler la distribution de la colonne par catégorie :
    
        dataframe_blobdata['<categorical_column>'].value_counts()

2. Générer des valeurs d’indicateur pour chaque valeur de colonne

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Joindre la colonne indicateur avec la trame de données d’origine 
 
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Supprimer la variable d’origine :

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    
###<a name="blob-binningfeature"></a>Génération de la fonctionnalité de groupement

Pour générer des fonctionnalités binned, nous avons procéder comme suit :

1. Ajouter une séquence de colonnes à l’emplacement d’une colonne numérique
 
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
        
2. Convertir la sélection à une séquence de variables de type boolean

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    
3. Enfin, rejoindre les variables factices à la trame de données d’origine

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool) 


##<a name="sql-featuregen"></a>Inscrire des données sur les blob Azure et la consommation dans l’apprentissage automatique de Azure

Une fois que vous avez exploré les données et créé les fonctionnalités nécessaires, vous pouvez charger les données (échantillonné ou featurized) à un Azure blob et le consommer dans l’apprentissage automatique de Azure à l’aide de la procédure suivante : Notez que les fonctionnalités supplémentaires peuvent être créées dans Azure Machine Learning Studio ainsi. 
1. Écrire la trame de données au fichier local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Téléchargez les données blob Azure comme suit :

        from azure.storage.blob import BlobService
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

3. Les données peuvent être lues à partir du blob à l’aide de la formation de Machine Azure [Importer des données] à présent[ import-data] module, comme indiqué dans l’écran ci-dessous :
 
![blob de lecteur][1]

[1]: ./media/machine-learning-data-science-process-data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
