<properties
    pageTitle="Déplacer les données à partir d’une de SQL Server sur site vers SQL Azure avec Azure Data Factory | Azure"
    description="Permet de paramétrer un pipeline de définition d’application qui se compose des deux activités de migration des données qui déplacent ensemble des données sur une base quotidienne entre bases de données sur site et dans le cloud."
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
    ms.date="09/14/2016"
    ms.author="bradsev" />


# <a name="move-data-from-an-on-premise-sql-server-to-sql-azure-with-azure-data-factory"></a>Déplacement des données à partir d’un serveur SQL sur site de SQL Azure avec Azure Data Factory

Cette rubrique indique comment déplacer des données d’une base de données de site sur SQL Server à une base de données de SQL Azure via le stockage Blob Azure à l’aide de la fabrique de données Azure (ADF).

Les liens de **menu** suivants vers les rubriques qui décrivent comment faire pour ajouter des données dans les environnements cibles où les données peuvent être stockées et traitées au cours du processus de Science de données Team.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="intro"></a>Introduction : Nouveautés CAD et quand il convient pour migrer des données ?

Azure Data Factory est un service d’intégration des données entièrement géré en nuage qui orchestre et automatise le déplacement et la transformation des données. Le concept clé dans le modèle de définition d’application est le pipeline. Un pipeline est un regroupement logique des activités, chacune d’elles définit les actions à exécuter sur les données contenues dans des groupes de données. Services liés sont utilisés pour définir les informations nécessaires pour le Factory de données pour se connecter aux ressources de données.

Avec chargeur automatique de documents, les services de traitement de données existants peuvent être composées dans des pipelines de données qui sont hautement disponibles et géré dans le nuage. Ces pipelines de données peuvent être planifiées à réceptionner, préparer, transformer, analyser et publier des données et chargeur automatique de documents, gère et orchestre les données complexes et le traitement des dépendances. Les solutions peuvent être générées et déployées dans le nuage, se connecter à un nombre croissant de locaux rapidement et de sources de données en nuage.

Envisagez d’utiliser le chargeur automatique de documents :

- Lorsque les données doivent être migrées en permanence dans un scénario hybride qui accède à des ressources à la fois sur site et le nuage 
- Lorsque les données sont traitées ou doivent être modifié ou disposer d’une logique métier pour l’ajouter en cours de migration. 

Chargeur automatique de documents permet la planification et le suivi des tâches à l’aide de simples scripts JSON qui gèrent le transfert des données sur une base périodique. Chargeur automatique de documents dispose également d’autres fonctionnalités telles que la prise en charge pour les opérations complexes. Pour plus d’informations sur le chargeur automatique de documents, consultez la documentation sur le [Factory de données Azure (ADF)](https://azure.microsoft.com/services/data-factory/).


## <a name="scenario"></a>Le scénario

Nous allons configurer un pipeline de définition d’application qui se compose des deux activités de migration des données. Ils déplacent ensemble données sur une base quotidienne entre une base de données SQL sur site et une base de données de SQL Azure dans le nuage. Les deux activités sont les suivantes :

* copier des données à partir d’une base de données de SQL Server sur site dans un compte de stockage des objets Blob Azure
* copier des données à partir du compte de stockage des objets Blob Azure à une base de données de SQL Azure.

>[AZURE.NOTE] Les étapes présentées ici ont été adaptées à partir du didacticiel plus détaillé fourni par l’équipe du chargeur automatique de documents : [déplacer des données entre sources de locaux et cloud avec la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md) des références aux sections correspondantes de cette rubrique sont fournies lorsque c’est approprié.


## <a name="prereqs"></a>Conditions préalables
Ce didacticiel suppose que vous avez :

* Un **abonnement Azure**. Si vous ne disposez pas d’un abonnement, vous pouvez vous inscrire pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Un **compte de stockage Azure**. Vous utilisez un compte de stockage Azure pour stocker les données dans ce didacticiel. Si vous n’avez pas un compte de stockage Azure, voir l’article [créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) . Après avoir créé le compte de stockage, vous devez obtenir la clé du compte utilisée pour accéder au stockage. Reportez-vous à la section [Affichage, de copie et de touches d’accès de stockage régénérer](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Accès à une **base de données SQL Azure**. Si vous devez configurer une base de données de SQL Azure, la phrase sujet [Mise en route de la base de données SQL de Microsoft Azure](../sql-database/sql-database-get-started.md) fournit des informations sur la façon de configurer une nouvelle instance d’une base de données SQL Azure.
* Installation et configuration **Azure PowerShell** localement. Pour obtenir des instructions, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

> [AZURE.NOTE] Cette procédure utilise le [portail Azure](https://portal.azure.com/).


##<a name="upload-data"></a>Télécharger les données à votre de SQL Server sur site

Le [groupe de données NYC Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/) nous permet de montrer le processus de migration. Le groupe de données NYC Taxi est disponible, comme indiqué dans ce poste, sur Azure blob [NYC Taxi données](http://www.andresmh.com/nyctaxitrips/)de stockage. Les données ont deux fichiers, le fichier trip_data.csv, qui contient des informations de voyage, et le fichier trip_far.csv, qui contient les détails du tarif payé pour chaque séjour. Un exemple et une description de ces fichiers sont fournis dans la [Description de Dataset NYC Taxi voyages](machine-learning-data-science-process-sql-walkthrough.md#dataset).


Vous pouvez adapter la procédure fournie ici à un ensemble de vos propres données ou suivez les étapes décrites à l’aide du groupe de données NYC Taxi. Pour charger le dataset NYC Taxi dans votre base de données de SQL Server sur site, suivez la procédure décrite dans les [Données d’importation en bloc dans la base de données de SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Ces instructions sont destinées à un SQL Server sur une Machine virtuelle de Azure, mais la procédure de téléchargement vers le SQL Server sur site est le même.


##<a name="create-adf"></a>Créer une fabrique de données Azure

Les instructions pour la création d’une nouvelle fabrique de données Azure et un groupe de ressources dans le [portail Azure](https://portal.azure.com/) sont fournies à [créer une fabrique de données Azure](../data-factory/data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory). Nom de l' instance de nouveau chargeur automatique de documents *adfdsp* et le nom du groupe créé de ressources *adfdsprg*.


## <a name="install-and-configure-up-the-data-management-gateway"></a>Installation et configuration de la passerelle de gestion des données

Pour activer vos pipelines dans une usine de données Azure pour travailler avec un de SQL Server sur site, vous devez l’ajouter sous la forme d’un Service lié sur le factory de données. Pour créer un Service lié pour un de SQL Server sur site, vous devez :

- Téléchargez et installez la passerelle de gestion des données de Microsoft sur l’ordinateur sur site. 
- configurer le service lié de la source de données locale utiliser la passerelle. 

La passerelle de gestion des données sérialise et désérialise les données source et le récepteur sur l’ordinateur où il est hébergé.

Pour plus d’informations sur la passerelle de gestion des données et les instructions d’installation, voir [déplacer des données entre sources de locaux et cloud avec la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)


## <a name="adflinkedservices"></a>Créer des services liés à se connecter aux ressources de données

Un service lié définit les informations nécessaires pour Azure Data Factory pour se connecter à une ressource de données. La procédure pas à pas pour la création de services liés est fournie dans la [Création de services liés](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-2-create-linked-services).

Nous avons trois ressources dans ce scénario pour lequel les services liés sont nécessaires.

1. [Service sur site SQL Server liée](#adf-linked-service-onprem-sql)
2. [Service lié pour le stockage des objets Blob Azure](#adf-linked-service-blob-store)
3. [Service de base de données Azure SQL lié](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Services liés de la base de données de SQL Server sur site

Pour créer le service lié pour le SQL Server sur site :

- Cliquez sur le **Magasin de données** dans la page de lancement de chargeur automatique de documents sur le portail classique d’Azure 
- Sélectionnez **SQL** et entrez les informations d’identification de *nom d’utilisateur* et le *mot de passe* pour le SQL Server sur site. Vous devez entrer le nom du serveur sous la forme d’un **nom d’instance NomServeur barre oblique inverse (nomserveur\nominstance) complet**. Nom de *l’adfonpremsql*services liés.

###<a name="adf-linked-service-blob-store"></a>Services liés pour les Blob

Pour créer le service lié pour le compte de stockage des objets Blob Azure :

- Cliquez sur le **Magasin de données** dans la page de lancement de chargeur automatique de documents sur le portail classique d’Azure
- Sélectionnez le **Compte de stockage Azure** 
- Entrez le nom de clé et le conteneur de compte le stockage Blob Azure. Nom de *l’adfds*services liés.

###<a name="adf-linked-service-azure-sql"></a>Service de base de données Azure SQL lié

Pour créer le service lié pour la base de données de SQL Azure :

- Cliquez sur le **Magasin de données** dans la page de lancement de chargeur automatique de documents sur le portail classique d’Azure
- Sélectionnez **Les SQL Azure** et entrez les informations d’identification de *nom d’utilisateur* et le *mot de passe* pour la base de données de SQL Azure. *nom d’utilisateur* doit être spécifié en tant que *user@servername*.   


##<a name="adf-tables"></a>Définir et créer des tables pour spécifier l’accès aux groupes de données

Créer des tables qui spécifient la structure, l’emplacement et la disponibilité des jeux de données, avec les procédures suivantes en fonction de script. Fichiers JSON sont utilisés pour définir les tables. Pour plus d’informations sur la structure de ces fichiers, consultez [groupes de données](../data-factory/data-factory-create-datasets.md).

> [AZURE.NOTE]  Vous devez exécuter la `Add-AzureAccount` applet de commande avant d’exécuter l’applet de commande [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) pour confirmer que l’abonnement Azure droite est sélectionné pour l’exécution de la commande. Pour la documentation de cette applet de commande, consultez [Add-AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

Les définitions basé sur JSON dans les tables utilisent les noms suivants :

* le **nom de la table** sur le serveur SQL sur site est *nyctaxi_data*
* le **nom du conteneur** dans le compte de stockage des objets Blob Azure est *containername*  

Trois définitions de table sont nécessaires pour ce pipeline du chargeur automatique de documents :

1. [Table sur site SQL](#adf-table-onprem-sql)
2. [Table Blob](#adf-table-blob-store)
3. [SQL Azure Table](#adf-table-azure-sql)

> [AZURE.NOTE]  Ces procédures utilisent Azure PowerShell pour définir et créer les activités de chargeur automatique de documents. Mais ces tâches peuvent également être effectuées à l’aide du portail Azure. Pour plus d’informations, consultez [créer d’entrée et de sortie des groupes de données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>Table sur site SQL

La définition de table pour le SQL Server sur site est spécifiée dans le fichier JSON suivant :

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }

Les noms de colonne n’étaient pas incluses ici. Vous pouvez sélectionner des noms de colonnes en les incluant ici (pour plus d’informations, vérifiez la rubrique de la [documentation du chargeur automatique de documents](../data-factory/data-factory-data-movement-activities.md ) .

Copier la définition de JSON de la table dans un fichier appelé *onpremtabledef.json* et l’enregistrer à un emplacement connu (ici valeur par défaut est *C:\temp\onpremtabledef.json*). Créez la table dans le chargeur automatique de documents avec l’applet de commande PowerShell de Azure suivante :

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Table Blob
Définition de la table pour l’emplacement de blob de sortie est la suivante (cela mappe les données ingérées au blob Azure sur site) :

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

Copier la définition de JSON de la table dans un fichier appelé *bloboutputtabledef.json* et l’enregistrer à un emplacement connu (ici valeur par défaut est *C:\temp\bloboutputtabledef.json*). Créez la table dans le chargeur automatique de documents avec l’applet de commande PowerShell de Azure suivante :

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>SQL Azure Table
Définition de la table pour le SQL Azure de sortie est la suivante (ce schéma mappe les données provenant de l’objet blob) :

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

Copier la définition de JSON de la table dans un fichier appelé *AzureSqlTable.json* et l’enregistrer à un emplacement connu (ici valeur par défaut est *C:\temp\AzureSqlTable.json*). Créez la table dans le chargeur automatique de documents avec l’applet de commande PowerShell de Azure suivante :

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


##<a name="adf-pipeline"></a>Définir et créer le pipeline

Spécifier les activités qui appartiennent au pipeline et créer le pipeline avec les procédures suivantes en fonction de script. Un fichier JSON est utilisé pour définir les propriétés du pipeline.

* Le script suppose que le **nom de pipeline** *AMLDSProcessPipeline*.
* Notez également que nous avons défini la périodicité du pipeline pour être exécutée sur une base quotidienne et utiliser le temps d’exécution par défaut de la tâche (12 : 00 UTC).

> [AZURE.NOTE]Les procédures suivantes utilisent Azure PowerShell pour définir et créer le pipeline du chargeur automatique de documents. Mais cette tâche peut également être réalisée à l’aide du portail Azure. Pour plus d’informations, voir [créer et exécuter un pipeline](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-4-create-and-run-a-pipeline).

En utilisant les définitions de table fournies précédemment, la définition de pipeline pour le chargeur automatique de documents est spécifiée comme suit :

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premise SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",             
                            }           
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

Copiez cette définition JSON du tuyau dans un fichier appelé *pipelinedef.json* et l’enregistrer à un emplacement connu (ici valeur par défaut est *C:\temp\pipelinedef.json*). Créer le pipeline dans le chargeur automatique de documents avec l’applet de commande PowerShell de Azure suivante :

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Vérifiez que vous pouvez voir le pipeline sur le chargeur automatique de documents dans le portail classique Azure s’affichent comme suit (lorsque vous cliquez sur le schéma)

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)


##<a name="adf-pipeline-start"></a>Démarrer le Pipeline
Le pipeline peut maintenant être exécuté à l’aide de la commande suivante :

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Les valeurs de paramètre *startdate* et *enddate* doivent être remplacées par les dates réelles entre lesquels vous souhaitez que le pipeline pour exécuter.

Une fois que le pipeline s’exécute, il se peut que vous devez être en mesure de voir les données apparaissent dans le conteneur sélectionné pour le blob, un seul fichier par jour.

Notez que nous n'avons pas tiré parti de la fonctionnalité fournie par le chargeur automatique de documents pour les données de tuyau progressivement. Pour plus d’informations sur la façon de faire cela et autres fonctionnalités fournies par le chargeur automatique de documents, consultez la [documentation du chargeur automatique de documents](https://azure.microsoft.com/services/data-factory/).
