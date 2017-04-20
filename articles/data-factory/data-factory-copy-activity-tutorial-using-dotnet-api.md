<properties 
    pageTitle="Didacticiel : Créer un pipeline avec une activité de copie à l’aide de l’API .NET | Microsoft Azure" 
    description="Dans ce didacticiel, vous créez un tuyau fabrique de données Azure avec une activité de copie à l’aide de l’API .NET." 
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
    ms.date="10/27/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Didacticiel : Créer un pipeline avec une activité de copie à l’aide de l’API .NET
> [AZURE.SELECTOR]
- [Vue d’ensemble et des conditions préalables](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Assistant copie](data-factory-copy-data-wizard-tutorial.md)
- [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modèle de gestionnaire de ressources Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Ce didacticiel vous montre comment créer et surveiller une fabrique de données Azure à l’aide de l’API .NET. Le pipeline de la fabrique de données utilise une activité de copie pour copier des données depuis le stockage Blob Azure à une base de données de SQL Azure.

L’activité de copie effectue le déplacement des données dans Azure Data Factory. L’activité est alimentée par un service global disponible qui peut copier des données entre différentes banques de données de manière sécurisée, fiable et évolutive. Reportez-vous à l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) pour plus d’informations sur l’activité de copie.   

> [AZURE.NOTE] 
> Cet article ne couvre pas toutes les API .NET fabrique les données. Pour plus d’informations sur le Kit de développement .NET Data Factory, voir [Données fabrique de référence sur l’API .NET](https://msdn.microsoft.com/library/mt415893.aspx) . 

## <a name="prerequisites"></a>Conditions préalables
- Aller à [vue d’ensemble et des composants requis](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir une vue d’ensemble du didacticiel et suivez les étapes de la **condition préalable** . 
- Visual Studio 2012 ou 2013 ou 2015
- Téléchargez et installez le [Kit de développement .NET Azure](http://azure.microsoft.com/downloads/)
- PowerShell Azure. Suivez les instructions dans l’article de [procédure pour installer et configurer Azure PowerShell](../powershell-install-configure.md) pour installer PowerShell d’Azure sur votre ordinateur. Azure PowerShell vous permet de créer une application Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Création d’une application dans Azure Active Directory
Créer une application Azure Active Directory, créez un principal de service pour l’application et l’assigner au rôle **Collaborateur en usine** .  

1. Lancer **PowerShell**. 
1. Exécutez la commande suivante, puis entrez le nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter au portail Azure.
    
        Login-AzureRmAccount   
2. Exécutez la commande suivante pour afficher tous les abonnements pour ce compte.

        Get-AzureRmSubscription 
3. Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacer ** &lt;NameOfAzureSubscription** &gt; avec le nom de votre abonnement Azure. 

        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

    > [AZURE.IMPORTANT] Notez le **SubscriptionId** et **TenantId** à partir de la sortie de cette commande. 
4. Créer un groupe de ressources Azure nommé **ADFTutorialResourceGroup** en exécutant la commande suivante dans PowerShell.  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Si le groupe de ressources existe déjà, vous spécifiez s’il faut mettre à jour (Y) ou conservez-le comme (N). 

    Si vous utilisez un groupe de ressources différent, vous devez utiliser le nom de votre groupe de ressources à la place de ADFTutorialResourceGroup dans ce didacticiel.
5. Créer une application Azure Active Directory. 

        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

    Si vous obtenez l’erreur suivante, spécifiez une autre URL et réexécutez la commande. 

        Another object with the same value for property identifierUris already exists.

6. Créer l’annonce de principal du service. 

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. Ajouter principal de service pour le rôle de **Collaborateur en usine** . 

        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. Obtenir l’ID d’application.

        $azureAdApplication

    Notez l’ID de l’application (**applicationID** à partir de la sortie).

Vous devez avoir suivant quatre valeurs à partir de ces étapes : 

- ID de clients
- ID de l’abonnement
- ID de l’application 
- Mot de passe (spécifié dans la première commande)   

## <a name="walkthrough"></a>Procédure pas à pas
1. À l’aide de Visual Studio 2012/2013/2015, créez une application console C# .NET.
    1. Lancez **Visual Studio** 2012/2013/2015.
    2. Cliquez sur **fichier**, pointez sur **Nouveau**, puis **projet**.
    3. Développez de **modèles**, puis sélectionnez **Visual C#**. Dans cette procédure pas à pas, vous utilisez C#, mais vous pouvez utiliser n’importe quel langage .NET.
    4. Sélectionnez **Application Console** à partir de la liste des types de projet à droite.
    5. Entrez **DataFactoryAPITestApp** pour le nom.
    6. Sélectionnez **C:\ADFGetStarted** pour l’emplacement.
    7. Cliquez sur **OK** pour créer le projet.
2. Cliquez sur **Outils**, pointez sur **Gestionnaire de package de Nuget**et cliquez sur **La Console du Gestionnaire de package**.
3.  Dans la **Console du Gestionnaire de package**, procédez comme suit : 
    1.  Exécutez la commande suivante pour installer le package d’usine de données :`Install-Package Microsoft.Azure.Management.DataFactories`       
    2.  Exécutez la commande suivante pour installer le package Azure Active Directory (vous utilisez Active Directory API dans le code) :`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
6. Ajoutez la section suivante de la **appSetttings** dans le fichier **App.config** . Ces paramètres sont utilisés par la méthode d’assistance : **GetAuthorizationHeader**. 

    Remplacez les valeurs pour ** &lt;ID de l’Application&gt;**, ** &lt;mot de passe&gt;**, ** &lt;ID d’abonnement&gt;**, et ** &lt;ID de client&gt; ** avec vos propres valeurs. 

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
            </startup>
            <appSettings>
                <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
                <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
                <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

                <add key="ApplicationId" value="your application ID" />
                <add key="Password" value="Password you used while creating the AAD application" />
                <add key="SubscriptionId" value= "Subscription ID" />
                <add key="ActiveDirectoryTenantId" value="Tenant ID" />
            </appSettings>
        </configuration>
6. Ajoutez les instructions **using** suivantes au fichier source (Program.cs) dans le projet.

        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
        
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
        
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. Ajoutez le code suivant crée une instance de la classe **DataPipelineManagementClient** pour la méthode **Main** . Cet objet vous permet de créer une fabrique de données, un service lié, jeux de données d’entrée et de sortie et un tuyau. Cet objet permet également de surveiller des tranches d’un groupe de données au moment de l’exécution.    

            // create data factory management client
            string resourceGroupName = "ADFTutorialResourceGroup";
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

    > [AZURE.IMPORTANT] 
    > Remplacez la valeur de **resourceGroupName** par le nom de votre groupe de ressources Azure. 
    > 
    > Mise à jour du nom de la fabrique de données (**dataFactoryName**) à être unique. Nom de la fabrique de données doit être globalement unique. Voir la rubrique [Data Factory - règles d’appellation](data-factory-naming-rules.md) pour les règles d’affectation de noms pour les artefacts de données usine. 

7. Ajoutez le code suivant qui crée une **fabrique de données** pour la méthode **Main** .

            // create a data factory
            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties() { }
                    }
                }
            );

8. Ajoutez le code suivant crée un **stockage Azure lié le service** à la méthode **Main** . 

    > [AZURE.IMPORTANT] Remplacer **storageaccountname** et **accountkey** avec le nom et la clé de votre compte de stockage Azure. 

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Ajoutez le code suivant crée un **Azure SQL liée de service** à la méthode **Main** .
 
    > [AZURE.IMPORTANT] Remplacez **servername**, **databasename**, **nom d’utilisateur**et **mot de passe** dont le nom de votre serveur Azure SQL, base de données, utilisateur et mot de passe.  

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Ajoutez le code suivant qui crée les **groupes de données d’entrée et de sortie** pour la méthode **Main** . 

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

11. Ajoutez le code suivant qui **crée et Active un pipeline** à la méthode **Main** . Ce pipeline a un **CopyActivity** qui prend l' **BlobSource** en tant que source et **BlobSink** comme un récepteur.

            // create a pipeline
            Console.WriteLine("Creating a pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "BlobToAzureSql",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    TypeProperties = new CopyActivity()
                                    {
                                        Source = new BlobSource(),
                                        Sink = new BlobSink()
                                        {
                                            WriteBatchSize = 10000,
                                            WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                        }
                                    }
                                }
                            },
                        }
                    }
                }); 

12. Ajoutez le code suivant à la méthode **Main** pour obtenir l’état d’une tranche de données du groupe de données de sortie. Il est uniquement la tranche prévue pour cet exemple.   
 
            // Pulling status within a timeout threshold
            DateTime start = DateTime.Now;
            bool done = false;

            while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
            {
                Console.WriteLine("Pulling the slice status");
                // wait before the next status check
                Thread.Sleep(1000 * 12);

                var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                    new DataSliceListParameters()
                    {
                        DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                        DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                    });

                foreach (DataSlice slice in datalistResponse.DataSlices)
                {
                    if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                    {
                        Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                        done = true;
                        break;
                    }
                    else
                    {
                        Console.WriteLine("Slice status is: {0}", slice.State);
                    }
                }
            }

14. Ajoutez le code suivant pour obtenir des détails de l’exécution pour une tranche de données à la méthode **Main** .

            Console.WriteLine("Getting run details of a data slice");

            // give it a few minutes for the output slice to be ready
            Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
            Console.ReadKey();

            var datasliceRunListResponse = client.DataSliceRuns.List(
                    resourceGroupName,
                    dataFactoryName,
                    Dataset_Destination,
                    new DataSliceRunListParameters()
                    {
                        DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                    }
                );

            foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
            {
                Console.WriteLine("Status: \t\t{0}", run.Status);
                Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
                Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
                Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
                Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
                Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
                Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
            }

            Console.WriteLine("\nPress any key to exit.");
            Console.ReadKey();

13. Ajoutez la méthode d’assistance suivante, utilisée par la méthode **Main** à la classe **Program** .  
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  


15. Dans l’Explorateur de solutions, développez le projet (**DataFactoryAPITestApp**), cliquez sur **références**et cliquez sur **Ajouter une référence**. Sélectionnez la case à cocher pour l’assembly «**System.Configuration**» et cliquez sur **OK**. 
16. Générez l’application console. Cliquez sur **Générer** dans le menu, puis cliquez sur **Générer la Solution**. 
16. Confirmez qu’au moins un fichier existe dans le conteneur **adftutorial** dans le stockage blob Azure. Si ce n’est pas le cas, créez le fichier de **Emp.txt** dans le bloc-notes avec le contenu suivant et le télécharger dans le conteneur adftutorial.

        John, Doe
        Jane, Doe
     
17. Exécuter l’exemple en cliquant sur **Déboguer** -> **Démarrer le débogage** dans le menu. Lorsque vous consultez les **détails d’une tranche de données lors de l’obtention de l’exécution**, attendez quelques minutes et appuyez sur **entrée**. 
18. Le portail Azure permet de vérifier que le factory de données **APITutorialFactory** est créé avec les artefacts suivants : 
    - Lié service : **LinkedService_AzureStorage** 
    - DataSet : **DatasetBlobSource** et **DatasetBlobDestination**.
    - Pipeline : **PipelineBlobSample** 
18. Vérifiez que les enregistrements des deux employés sont créés dans la table «**emp**» dans la base de données Azure SQL spécifiée.

## <a name="next-steps"></a>Étapes suivantes

- Lisez l’article [d’Activités de déplacement des données](data-factory-data-movement-activities.md) , qui fournit des informations détaillées sur l’activité de copie que vous avez utilisé dans le didacticiel.
- Pour plus d’informations sur le Kit de développement .NET Data Factory, voir [Données fabrique de référence sur l’API .NET](https://msdn.microsoft.com/library/mt415893.aspx) . Cet article ne couvre pas toutes les API .NET fabrique les données. 

 
