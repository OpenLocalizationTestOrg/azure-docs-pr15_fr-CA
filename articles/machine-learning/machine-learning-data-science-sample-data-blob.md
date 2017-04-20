<properties 
    pageTitle="Stockage de blob de données d’exemple dans Azure | Microsoft Azure" 
    description="Exemple de données dans le stockage Blob Azure" 
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

#<a name="heading"></a>Stockage de blob de données d’exemple dans Azure


Ce document traite des données d’échantillonnage stockées dans le stockage blob Azure en téléchargeant par programme et puis d’échantillonnage à l’aide des procédures écrites dans les Python.

**Pourquoi échantillon de vos données ?**
Si vous envisagez d’analyser le groupe de données est volumineuse, il est généralement conseillé de bas-exemple les données afin de la réduire à une taille plus petite mais représentative et plus faciles à gérer. Cela facilite l’ingénierie de la fonctionnalité, l’exploration et la présentation des données. Son rôle dans le processus Analytique Cortana doit activer le prototypage rapide des fonctions de traitement de données et apprentissage des modèles de l’ordinateur.

Le **menu** ci-dessous des liens vers des rubriques qui décrivent l’échantillon de données de différents environnements de stockage. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Cette tâche d’échantillonnage est une étape dans le [Processus de Science données Team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="download-and-down-sample-data"></a>Téléchargement et vers le bas-exemples de données
1. Télécharger les données depuis le stockage blob Azure en utilisant le service d’objet blob à partir de l’exemple de code Python suivant : 

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

2. Lire les données dans une trame de données Pandas à partir du fichier téléchargé.

        import pandas as pd

        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Bas-exemple les données à l’aide de la `numpy`de `random.choice` comme suit :

        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Maintenant vous pouvez travailler avec la trame de données ci-dessus avec l’exemple 1 pour cent pour une exploration plus approfondie et de génération de la fonctionnalité.

##<a name="heading"></a>Télécharger les données et lire dans Azure Machine Learning

Vous pouvez utiliser l’exemple de code suivant pour les échantillons vers le bas les données et l’utiliser directement dans Azure ML :

1. Écrire la trame de données dans un fichier local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Télécharger le fichier local dans un blob Azure à l’aide de l’exemple de code suivant :

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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Lire les données à partir du blob Azure à l’aide de ML d’Azure, [Importer les données](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) comme indiqué dans l’image ci-dessous :
 
![blob de lecteur](./media/machine-learning-data-science-sample-data-blob/reader_blob.png)

 
