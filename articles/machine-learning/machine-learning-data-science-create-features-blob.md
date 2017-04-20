<properties
    pageTitle="Créer des fonctions pour les données de stockage Azure blob à l’aide de Panda | Microsoft Azure"
    description="Comment faire pour créer des fonctions pour les données qui sont stockées dans le conteneur d’objet blob Azure avec le package les Python Panda."
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
    ms.author="bradsev;garye" />

#<a name="create-features-for-azure-blob-storage-data-using-panda"></a>Créer des fonctions pour les données de stockage Azure blob à l’aide de Panda

Ce document montre comment créer des fonctions pour les données qui sont stockées dans le conteneur d’objet blob Azure en utilisant le package les Python [Pandas](http://pandas.pydata.org/) . Après décrivant comment charger des données dans une trame de données Panda, il montre comment générer des fonctionnalités par catégorie à l’aide de scripts de Python avec les valeurs de l’indicateur et sélection des fonctionnalités.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Ce **menu** liens vers des rubriques qui décrivent comment créer des fonctions pour les données dans différents environnements. Cette tâche est une étape dans le [Processus de Science données Team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous avez créé un compte de stockage blob Azure et si vous avez stocké vos données. Si vous avez besoin d’instructions pour configurer un compte, voir [Création d’un compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account)


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

##<a name="blob-featuregen"></a>Génération de la fonctionnalité

Les deux sections suivantes indiquent comment générer des fonctionnalités par catégorie avec les valeurs de l’indicateur et mise au rebus à l’aide de scripts de Python.

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

3. Désormais, les données peuvent être lues à partir du blob à l’aide du module d’apprentissage de Machine d’Azure, [Importer des données](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , comme indiqué dans l’écran ci-dessous :

![blob de lecteur](./media/machine-learning-data-science-process-data-blob/reader_blob.png)
