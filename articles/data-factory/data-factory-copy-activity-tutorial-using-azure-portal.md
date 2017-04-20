<properties 
    pageTitle="Didacticiel : Créer un pipeline avec une activité de copie à l’aide du portail Azure | Microsoft Azure" 
    description="Dans ce didacticiel, vous créez un tuyau fabrique de données Azure avec une activité de copie à l’aide de l’éditeur de la fabrique de données dans le portail Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/16/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-portal"></a>Didacticiel : Créer un pipeline avec une activité de copie à l’aide du portail Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble et des conditions préalables](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Assistant copie](data-factory-copy-data-wizard-tutorial.md)
- [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modèle de gestionnaire de ressources Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)



Ce didacticiel vous montre comment créer et surveiller une fabrique de données Azure via le portail Azure. Le pipeline de la fabrique de données utilise une activité de copie pour copier des données depuis le stockage Blob Azure à une base de données de SQL Azure.

Voici les étapes que vous effectuez dans le cadre de ce didacticiel :

Étape | Description
-----| -----------
[Créer une fabrique de données Azure](#create-data-factory) | Dans cette étape, vous créez une fabrique de données Azure nommée **ADFTutorialDataFactory**.  
[Créer des services liés](#create-linked-services) | Dans cette étape, vous créez deux services liés : **AzureStorageLinkedService** et **AzureSqlLinkedService**. <br/><br/>La AzureStorageLinkedService des liens du stockage Azure et AzureSqlLinkedService des liens de la base de données Azure SQL à la ADFTutorialDataFactory. Les données d’entrée pour le pipeline se trouve dans un conteneur d’objet blob dans les données de stockage et de sortie blob Azure stockées dans une table dans la base de données SQL d’Azure. Par conséquent, vous ajoutez ces deux banques de données en tant que services liés à l’usine de données.      
[Créez d’entrée et de sortie des groupes de données](#create-datasets) | À l’étape précédente, vous avez créé des services liés qui font référence à des magasins de données qui contiennent des données d’entrée/sortie. Dans cette étape, vous définissez deux groupes de données ( **InputDataset** et **OutputDataset** ) qui représentent les données d’entrée/sortie qui sont stockées dans les banques de données. <br/><br/>Pour le InputDataset, vous indiquez le conteneur blob qui contient un objet blob avec la source de données et pour le OutputDataset, vous spécifiez la table SQL qui stocke les données de sortie. Vous également spécifiez d’autres propriétés telles que la structure, de disponibilité et de stratégie. 
[Créer un pipeline](#create-pipeline) | Dans cette étape, vous créez un pipeline nommé **ADFTutorialPipeline** dans le ADFTutorialDataFactory. <br/><br/>Vous ajoutez une **activité de copie** au pipeline copie d’entrée des données à partir de l’Azure BLOB à la table SQL Azure de sortie. L’activité de copie effectue le déplacement des données dans Azure Data Factory. Il est alimenté par un service global disponible qui peut copier des données entre différentes banques de données de manière sécurisée, fiable et évolutive. Reportez-vous à l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) pour plus d’informations sur l’activité de copie. 
[Pipeline de moniteur](#monitor-pipeline) | Dans cette étape, vous surveillez des tranches de tables d’entrée et de sortie à l’aide d’Azure portal.

## <a name="prerequisites"></a>Conditions préalables 
Conditions préalables répertoriées dans l’article de [Vue d’ensemble](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) avant de procéder à ce didacticiel.

## <a name="create-data-factory"></a>Création de la fabrique de données
Dans cette étape, vous utilisez le portail Azure pour créer une fabrique de données Azure nommée **ADFTutorialDataFactory**.

1.  Après la connexion au [portail Azure](https://portal.azure.com/), cliquez sur **Nouveau**, sélectionnez **Intelligence + Analytique**et cliquez sur le **Factory de données**. 

    ![Nouveau -> DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)  

6. Dans la **nouvelle fabrique de données** blade :
    1. Entrez **ADFTutorialDataFactory** pour le **nom**. 
    
        ![Nouvelle lame en usine](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)

        Le nom de la fabrique de données Azure doit être **globalement unique**. Si vous recevez le message d’erreur suivant, modifiez le nom de la fabrique de données (par exemple, yournameADFTutorialDataFactory), puis essayez de créer à nouveau. Voir la rubrique [Data Factory - règles d’appellation](data-factory-naming-rules.md) pour les règles d’affectation de noms pour les artefacts de données usine.
    
            Data factory name “ADFTutorialDataFactory” is not available  
     
        ![Nom d’usine de données n’est pas disponible](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
    2. Sélectionnez votre **abonnement**d' Azure.
    3. Pour le groupe de ressources, effectuez l’une des étapes suivantes :
        1. Sélectionnez **utiliser l’existant**et sélectionnez un groupe de ressource existant dans la liste déroulante. 
        2. Sélectionnez **Créer nouveau**et entrez le nom d’un groupe de ressources.   
    
            Certaines des étapes de ce didacticiel supposent que vous utilisez le nom : **ADFTutorialResourceGroup** pour le groupe de ressources. Pour obtenir des informations sur les groupes de ressources, reportez-vous [à l’aide des groupes de ressources à gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
    4. Sélectionnez l' **emplacement** de la fabrique de données. Seules les régions pris en charge par le service Factory de données sont affichées dans la liste déroulante.
    5. Sélectionnez le **code Pin à Startboard**.     
    6. Cliquez sur **créer**.

        > [AZURE.IMPORTANT] Pour créer des instances de la fabrique de données, vous devez être un membre du rôle [Collaborateur usine](../active-directory/role-based-access-built-in-roles.md/#data-factory-contributor) au niveau du groupe d’abonnement/de la ressource.
        >  
        >  Le nom de la fabrique de données peut être enregistré sous la forme d’un nom DNS à l’avenir et, par conséquent, devenir visible publiquement.              
9.  Pour afficher les messages de notification d’état, cliquez sur l’icône de la clochette sur la barre d’outils. 

    ![Messages de notification](./media/data-factory-copy-activity-tutorial-using-azure-portal/Notifications.png) 
10. Une fois la création terminée, vous voyez la lame **Factory de données** comme indiqué dans l’image.

    ![Page d’accueil de fabrique de données](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Créer des services liés
Services liés lier des magasins de données ou le calculent des services à une usine de données Azure. Consultez les [banques de données pris en charge](data-factory-data-movement-activities.md##supported-data-stores-and-formats) pour toutes les sources et les récepteurs pris en charge par l’activité de copie. Voir [services liés de traitement](data-factory-compute-linked-services.md) pour la liste des services de calcul prises en charge par Data Factory. Dans ce didacticiel, vous n’utilisez pas un service de calcul. 

Dans cette étape, vous créez deux services liés : **AzureStorageLinkedService** et **AzureSqlLinkedService**. AzureStorageLinkedService lié des liens de service un compte de stockage Azure et AzureSqlLinkedService des liens une base de données Azure SQL à **ADFTutorialDataFactory**. Vous créez un pipeline plus loin dans ce didacticiel qui copie les données à partir d’un conteneur d’objet blob dans AzureStorageLinkedService à une table SQL dans AzureSqlLinkedService.

### <a name="create-a-linked-service-for-the-azure-storage-account"></a>Création d’un service lié pour le compte de stockage Azure
1.  Dans la lame **Factory de données** , cliquez sur **auteur et déployer** mosaïque pour lancer l' **éditeur** de la fabrique de données.

    ![Créer et déployer la mosaïque](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
5. Dans l' **éditeur**, cliquez sur le bouton **nouveau magasin de données** et sélectionner le **stockage Azure** dans le menu déroulant. Vous devez voir le modèle JSON pour la création d’un service de stockage Azure lié dans le volet droit. 

    ![Nouveau bouton de magasin de données éditeur](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
6. Remplacer `<accountname>` et `<accountkey>` avec le nom de compte et les valeurs de clé de compte pour votre compte de stockage Azure. 

    ![Stockage des objets Blob éditeur JSON](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png) 
6. Dans la barre d’outils, cliquez sur **déployer** . Vous devez voir déploiement **AzureStorageLinkedService** dans l’arborescence maintenant. 

    ![Stockage des objets Blob éditeur déployer](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

> [AZURE.NOTE]
> Pour plus d’informations sur les propriétés JSON, consultez [déplacer des données depuis/vers Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) .

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Création d’un service lié pour la base de données de SQL Azure
1. Dans l' **Éditeur de fabrique de données**, cliquez sur **nouveau magasin de données** de bouton dans la barre d’outils et sélectionnez la **Base de données de SQL Azure** à partir du menu déroulant. Vous devez voir le modèle JSON pour créer le service SQL Azure lié dans le volet droit.
2. Remplacer `<servername>`, `<databasename>`, `<username>@<servername>`, et `<password>` avec le nom de votre serveur Azure SQL, base de données, compte d’utilisateur et mot de passe. 
3. Sur la barre d’outils pour créer et déployer **AzureSqlLinkedService**, cliquez sur **déployer** .
4. Vérifiez que vous consultez **AzureSqlLinkedService** dans l’arborescence. 

> [AZURE.NOTE]
> Pour plus d’informations sur les propriétés JSON, consultez [déplacer des données depuis/vers des base de données de SQL Azure](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) .

## <a name="create-datasets"></a>Créer des groupes de données
Dans l’étape précédente, vous avez créé des services liés, **AzureStorageLinkedService** et **AzureSqlLinkedService** pour lier un compte de stockage Azure et de la base de données de Azure SQL sur le factory de données : **ADFTutorialDataFactory**. Dans cette étape, vous définissez deux groupes de données ( **InputDataset** et **OutputDataset** ) qui représentent les données d’entrée/sortie qui sont stockées dans les banques de données visées respectivement par AzureStorageLinkedService et AzureSqlLinkedService. Pour InputDataset, vous indiquez le conteneur blob qui contient un objet blob avec la source de données et de OutputDataset, vous spécifiez la table SQL qui stocke les données de sortie. 

### <a name="create-input-dataset"></a>Créer le groupe de données d’entrée 
Dans cette étape, vous créez un groupe de données nommé **InputDataset** qui pointe vers un conteneur d’objet blob dans le stockage Azure représenté par le service **AzureStorageLinkedService** lié.

1. Dans l' **éditeur** de la fabrique de données, cliquez sur **... Plus**, cliquez sur **Nouveau groupe de données**et sur le **stockage des objets Blob Azure** dans le menu déroulant. 

    ![Nouveau menu du groupe de données](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Remplacez JSON dans le volet droit l’extrait suivant de JSON : 

        {
          "name": "InputDataset",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
              "folderPath": "adftutorial/",
              "fileName": "emp.txt",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }
        
     Notez les points suivants : 
    
    - le groupe de données **type** est définie sur **AzureBlob**.
    - **linkedServiceName** est défini sur **AzureStorageLinkedService**. Vous avez créé ce service lié à l’étape 2.
    - **folderPath** est définie avec le conteneur de **adftutorial** . Vous pouvez également spécifier le nom d’un objet blob dans le dossier à l’aide de la propriété de **nom de fichier** . Étant donné que vous ne spécifiez pas le nom de l’objet blob, les données à partir de tous les objets BLOB dans le conteneur sont considérées comme des données d’entrée.  
    - format de **type** a la valeur **TextFormat**
    - Il y a deux champs dans le fichier texte, **FirstName** et **LastName** , séparé par une virgule (**columnDelimiter**) 
    - La **disponibilité** est définie pour **toutes les heures** (**fréquence** est réglée sur **l’heure** et **l’intervalle** est défini à **1**). Par conséquent, Data Factory recherche les données d’entrée toutes les heures dans le dossier racine du conteneur blob (**adftutorial**) que vous avez spécifié. 
    
    Si vous ne spécifiez pas un **nom de fichier** pour un groupe de données **d’entrée** , tous les fichiers BLOB à partir du dossier d’entrée (**folderPath**) sont considérées comme entrées. Si vous spécifiez un nom de fichier dans le JSON, uniquement le spécifié fichier/blob est considérée comme entrée d’asn.
 
    Si vous ne spécifiez pas de **nom de fichier** pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : données. &lt;Guid\&gt ;. txt (exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Pour définir les **folderPath** et dynamiquement en fonction du temps de **SliceStart** de **nom de fichier** , utilisez la propriété **partitionedBy** . Dans l’exemple suivant, folderPath utilise l’année, mois et jour à partir de le SliceStart (heure de début de la section en cours de traitement) et nom de fichier utilise l’heure à partir de la SliceStart. Par exemple, si une tranche est fabriquée pour 2016-09-20T08:00:00, NomDossier est définie à wikidatagateway/wikisampledataout/2016/09/20 et le nom de fichier est définie sur 08.csv. 

            "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": 
            [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
            ],
2. Dans la barre d’outils pour créer et déployer le dataset **InputDataset** , cliquez sur **déployer** . Vérifiez que vous consultez **InputDataset** dans l’arborescence.

> [AZURE.NOTE]
> Pour plus d’informations sur les propriétés JSON, consultez [déplacer des données depuis/vers Azure Blob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) .

### <a name="create-output-dataset"></a>Créer le dataset de sortie
Dans cette partie de l’étape, vous créez un dataset de sortie nommé **OutputDataset**. Ce groupe de données pointe vers une table SQL dans la base de données SQL d’Azure représenté par **AzureSqlLinkedService**. 

1. Dans l' **éditeur** de la fabrique de données, cliquez sur **... Plus**et cliquez sur **Nouveau groupe de données**, cliquez sur **Azure SQL** dans le menu déroulant. 
2. Remplacez JSON dans le volet droit l’extrait suivant de JSON :

        {
          "name": "OutputDataset",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }
        
     Notez les points suivants : 
    
    - le groupe de données **type** est définie sur **AzureSQLTable**.
    - **linkedServiceName** est défini sur **AzureSqlLinkedService** (vous avez créé ce service lié à l’étape 2).
    - **TableName** est définie sur **emp**.
    - Il existe trois colonnes : **ID**, **FirstName**et **LastName** – dans la table emp dans la base de données. ID est une colonne d’identité, vous devez spécifier ici des seulement **FirstName** et **LastName** .
    - La **disponibilité** est définie pour **toutes les heures** (**fréquence** définie sur **l’heure** et **intervalle** la valeur **1**).  Le service Data Factory génère une tranche de données sortie toutes les heures dans la table **emp** dans la base de données SQL d’Azure.

3. Dans la barre d’outils pour créer et déployer le dataset **OutputDataset** , cliquez sur **déployer** . Vérifiez que vous consultez **OutputDataset** dans l’arborescence. 

> [AZURE.NOTE]
> Pour plus d’informations sur les propriétés JSON, consultez [déplacer des données depuis/vers des base de données de SQL Azure](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) .

## <a name="create-pipeline"></a>Créer des opportunités
Dans cette étape, vous créez un tuyau avec une **Activité de copie** qui utilise **InputDataset** comme entrée et **OutputDataset** en tant que sortie.

1. Dans l' **éditeur** de la fabrique de données, cliquez sur **... Plus**, cliquez sur le **nouveau pipeline**. Vous pouvez également droit des **Pipelines** dans l’arborescence et cliquez sur le **nouveau pipeline de**.
2. Remplacez JSON dans le volet droit l’extrait suivant de JSON : 
        
        {
          "name": "ADFTutorialPipeline",
          "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
              {
                "name": "CopyFromBlobToSQL",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "InputDataset"
                  }
                ],
                "outputs": [
                  {
                    "name": "OutputDataset"
                  }
                ],
                "typeProperties": {
                  "source": {
                    "type": "BlobSource"
                  },
                  "sink": {
                    "type": "SqlSink",
                    "writeBatchSize": 10000,
                    "writeBatchTimeout": "60:00:00"
                  }
                },
                "Policy": {
                  "concurrency": 1,
                  "executionPriorityOrder": "NewestFirst",
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2016-07-12T00:00:00Z",
            "end": "2016-07-13T00:00:00Z"
          }
        } 

    Notez les points suivants :

    - Dans la section activités, il n'existe qu’une seule activité dont le **type** est définie pour la **copie**.
    - **InputDataset** a la valeur entrée pour l’activité et la sortie de l’activité est définie sur **OutputDataset**.
    - Dans la section **typeProperties** , **BlobSource** est spécifié comme type de source et **SqlSink** est spécifié comme type de récepteurs.

    Remplacez la valeur de la propriété **start** avec la valeur actuelle de jour et à la **fin** avec le jour suivant. Vous pouvez spécifier uniquement la partie de date et ignorer la partie heure de l’heure. Par exemple, « 2016-02-03 », qui est équivalent à « 2016-02-03T00:00:00Z »
    
    Les deux début et dates/heures de fin doivent être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2016-10-14T16:32:41Z. L’heure de **fin** est facultative, mais nous l’utilisons dans ce didacticiel. 
    
    Si vous ne spécifiez pas de valeur pour la propriété **end** , il est calculé en tant que «**début + 48 heures**». Pour exécuter le pipeline indéfiniment, spécifier la valeur de la propriété **end** **9999-09-09** .
    
    Dans l’exemple précédent, il existe des tranches de données 24 chaque tranche de données se produit toutes les heures.
    
4. Sur la barre d’outils pour créer et déployer **ADFTutorialPipeline**, cliquez sur **déployer** . Vérifiez que vous consultez le pipeline dans l’arborescence. 
5. À présent, fermez la lame de **l’éditeur** en cliquant sur **X**. Cliquez sur **X** pour voir la page d’accueil de **Data Factory** pour **ADFTutorialDataFactory**.

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, services liés, tables et un tuyau et planifiée du pipeline.   
 
### <a name="view-the-data-factory-in-a-diagram-view"></a>Afficher le factory de données dans un affichage de tâches 
1. De la lame de la **Fabrique de données** , cliquez sur **diagramme**.

    ![Lame en usine - mosaïque de diagramme](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Vous devez voir le diagramme image semblable à la suivante : 

    ![Affichage des diagrammes](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)

    Vous pouvez effectuer un zoom avant, zoom arrière, zoom à 100 %, zoom pour ajuster automatiquement placer les tables et les pipelines et afficher les informations de lignage (met en surbrillance les éléments en amont et en aval des éléments sélectionnés).  Vous pouvez double-cliquer sur un objet (table d’entrée/sortie ou pipeline) pour afficher les propriétés. 
3. Droit **ADFTutorialPipeline** dans l’affichage de tâches, puis cliquez sur **ouverte**. 

    ![Opportunités ouvertes](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)
4. Vous devez voir les activités dans le pipeline, ainsi que des groupes de données d’entrée et de sortie pour les activités. Dans ce didacticiel, vous avez uniquement une activité dans le pipeline (activité de copie) avec InputDataset en tant que groupe de données d’entrée et OutputDataset en tant que dataset de sortie.   

    ![Ouvrir la vue du tuyau](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenedPipeline.png)
5. Dans l’arborescence de navigation dans le coin supérieur gauche pour revenir à l’affichage de tâches, cliquez sur **Data factory** . La vue diagramme affiche tous les tuyaux. Dans cet exemple, vous avez seulement créé un tuyau.   
 

## <a name="monitor-pipeline"></a>Pipeline de moniteur
Dans cette étape, vous utilisez le portail Azure pour contrôler ce qui se passe dans une usine de données Azure. 

### <a name="monitor-pipeline-using-diagram-view"></a>Tuyau de moniteur à l’aide de la vue Affichage des diagrammes

1. Cliquez sur **X** pour fermer l’affichage du **diagramme** pour afficher la page d’accueil de Data Factory de la fabrique de données. Si vous avez fermé le navigateur web, procédez comme suit : 
    2. Accédez au [portail Azure](https://portal.azure.com/). 
    2. Double-cliquez sur **ADFTutorialDataFactory** sur la **Startboard** (ou) et sur les **fabriques de données** dans le menu de gauche, recherchez ADFTutorialDataFactory. 
3. Vous devriez voir le nombre et les noms de tables et de pipeline que vous avez créé sur cette blade.

    ![page d’accueil avec des noms](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png)
4. Maintenant, cliquez sur mosaïque de **groupes de données** .
5. De la lame de **groupes de données** , cliquez sur **InputDataset**. Ce dataset est le dataset d’entrée pour **ADFTutorialPipeline**.

    ![Groupes de données avec InputDataset sélectionné](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Cliquez sur **... (sélection)** Pour voir toutes les tranches de données.

    ![Toutes les entrées des tranches de données](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  

    Notez que toutes les tranches de données à l’heure actuelle sont **prêts** , car le fichier **emp.txt** existe tout le temps dans le conteneur d’objet blob : **adftutorial\input**. Vérifiez qu’aucuns tranches n’apparaissent dans la section **tranches récemment ayant échouées** en bas.

    Listes à la fois **récemment mis à jour de tranches** et **récemment échec tranches** sont triés par l' **heure de la dernière mise à jour**. 
    
    Cliquez sur **le filtre** dans la barre d’outils pour filtrer les tranches.  
    
    ![Tranches d’entrée de filtre](./media/data-factory-copy-activity-tutorial-using-azure-portal/filter-input-slices.png)
6. Fermez les lames jusqu'à ce que vous voyiez la lame de **groupes de données** . Cliquez sur **OutputDataset**. Ce dataset est le dataset de sortie pour **ADFTutorialPipeline**.

    ![lame de jeux de données](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png)
6. Vous devez voir la lame **OutputDataset** comme indiqué dans l’image suivante :

    ![lame de table](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png) 
7. Notez que les tranches de données à l’heure actuelle ont déjà été produits et qu’ils sont **prêts**. Pas de tranches s’affichent dans la section **tranches de problème** en bas.
8. Cliquez sur **... (Sélection)** Pour afficher toutes les sections.

    ![lame de tranches de données](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png)
9. Cliquez sur une tranche de données à partir de la liste, et vous devriez voir la lame de la **tranche de données** .

    ![lame de tranche de données](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
  
    Si la section n’est pas dans l’état **prêt** , vous pouvez voir les tranches en amont qui ne sont pas prêtes et bloquent la tranche en cours d’exécution dans la liste **des tranches en amont qui ne sont pas prêtes** .
11. La lame de la **Tranche de données** , doit apparaître dans toutes les activités qui s’exécute dans la liste du bas. Cliquez sur une **activité exécutée** pour voir la lame de **l’activité, détails de l’exécution** . 

    ![Activité exécuter des détails](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)
12. Cliquez sur **X** pour fermer toutes les blades jusqu'à ce que vous reveniez à la blade de base pour **ADFTutorialDataFactory**.
14. (facultatif) Cliquez sur **canalisations** sur la page d’accueil pour **ADFTutorialDataFactory**et cliquez sur **ADFTutorialPipeline** dans la lame de **Pipelines** , effectuer des recherches dans les tables d’entrée (**utilisé**) ou sortie (**produit**).
15. Lancement de **SQL Server Management Studio**, vous connecter à la base de données de SQL Azure et vérifiez que les lignes sont insérées dans la table **emp** dans la base de données.

    ![résultats de la requête SQL](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Surveiller le tuyau à l’aide de moniteur et une application de gestion
Vous pouvez également utiliser le moniteur et gérer l’application à surveiller vos pipelines. Pour plus d’informations sur l’utilisation de cette application, reportez-vous à la section [moniteur et de gérer les pipelines Azure Data Factory à l’aide de surveillance et gestion App](data-factory-monitor-manage-app.md).

1. Cliquez sur une mosaïque **de moniteurs et de gérer** sur la page d’accueil de votre usine de données.

    ![Contrôler et gérer la mosaïque](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Vous devez voir **l’application Moniteur et de la gestion**. Modifier **l’heure de début** et **heure de fin** pour inclure le début (12-07-2016) et fin (2016-07-13) de votre pipeline et cliquez sur **Appliquer**. 

    ![Contrôler et gérer les applications](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png) 
3. Sélectionnez une fenêtre de l’activité dans la liste **Activité Windows** pour consulter les détails à ce sujet. 
    ![Détail des opérations de fenêtre](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

## <a name="summary"></a>Résumé 
Dans ce didacticiel, vous avez créé une fabrique de données Azure pour copier des données à partir d’un Azure blob dans une base de données Azure SQL. Le portail Azure vous permet de créer l’usine de données, services liés, des groupes de données et un tuyau. Voici les principales étapes que vous avez effectuées dans ce didacticiel :  

1.  Permet de créer une **fabrique de données**d' Azure.
2.  Permet de créer des **services liés**:
    1. Un service de **Stockage Azure** lié à lier votre compte de stockage Azure qui contient les données d’entrée.    
    2. Un service **Azure SQL** lié pour lier votre base de données Azure SQL qui contient les données de sortie. 
3.  Permet de créer **des groupes de données** qui décrivent les données d’entrée et sortie des pipelines.
4.  Créé un **pipeline** avec une **Activité de copie** avec **BlobSource** comme source et **SqlSink** en tant que récepteur.  


## <a name="see-also"></a>Voir aussi
| Rubrique | Description |
| :---- | :---- |
| [Activités de déplacement des données](data-factory-data-movement-activities.md) | Cet article fournit des informations détaillées sur l’activité de copie que vous avez utilisé dans le didacticiel. |
| [Planification et exécution](data-factory-scheduling-and-execution.md) | Cet article explique les aspects de la planification et l’exécution du modèle d’application Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory. |
| [Groupes de données](data-factory-create-datasets.md) | Cet article vous aide à comprendre les groupes de données dans Azure Data Factory.
| [Surveiller et gérer les pipelines à l’aide de la surveillance de l’application](data-factory-monitor-manage-app.md) | Cet article décrit comment faire pour contrôler, gérer et déboguer des pipelines à l’aide du contrôle et application de gestion. 


