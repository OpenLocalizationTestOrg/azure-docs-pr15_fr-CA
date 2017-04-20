<properties
    pageTitle="Personnaliser des Clusters HDInsight à l’aide des actions de script | Microsoft Azure"
    description="Apprenez à personnaliser des clusters HDInsight à l’aide des actions de Script."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Personnaliser les clusters basés sur Windows de HDInsight à l’aide des actions de Script

**Action de script** peut être utilisée pour appeler des [scripts personnalisés](hdinsight-hadoop-script-actions.md) pendant le processus de création de cluster pour l’installation de logiciels supplémentaires sur un cluster.

Les informations contenues dans cet article sont spécifiques aux clusters de HDInsight de basées sur Windows. Pour les clusters sous Linux, voir [clusters basés sur Linux de personnaliser la HDInsight à l’aide des actions de Script](hdinsight-hadoop-customize-cluster-linux.md). 

HDInsight clusters peuvent être personnalisées de plusieurs autres manières, notamment les comptes de stockage Azure supplémentaires, modifier le Hadoop (core-site.xml, site.xml à la ruche, etc.) des fichiers de configuration, ou ajout de bibliothèques (ruche, Oozie) partagées dans les emplacements courants dans le cluster. Ces personnalisations peuvent être effectuées par le biais de PowerShell d’Azure, le Kit de développement .NET de HDInsight Azure ou le portail Azure. Pour plus d’informations, consultez [Hadoop de créer des clusters dans HDInsight][hdinsight-provision-cluster].

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Action de script dans le processus de création du cluster

Action de script est uniquement utilisée lorsqu’un clusters est en cours de création. Le diagramme suivant illustre lorsque l’Action de Script est exécutée pendant le processus de création :

![Personnalisation de cluster HDInsight et les étapes de création d’un cluster][img-hdi-cluster-states]

Lorsque le script est exécuté, le cluster insère l’étape **ClusterCustomization** . À ce stade, le script est exécuté sous le compte administrateur système, en parallèle sur tous les nœuds spécifiés dans le cluster et fournit des privilèges d’administrateur complets sur les nœuds.

> [AZURE.NOTE] Car vous avez des privilèges d’administrateur sur les nœuds de cluster au cours de la phase de **ClusterCustomization** , vous pouvez utiliser le script pour effectuer des opérations telles que l’arrêt et le démarrage des services, y compris des services d’Hadoop. En tant que partie du script, vous devez vous assurer que les services Ambari et autres services Hadoop sont en cours d’exécution avant la fin du script en cours d’exécution. Ces services sont requis pour correctement évaluer la santé et l’état du cluster pendant qu’il est en cours de création. Si vous modifiez n’importe quelle configuration sur le cluster qui a une incidence sur ces services, vous devez utiliser les fonctions d’assistance sont fournies. Pour plus d’informations sur les fonctions d’assistance, consultez [scripts de développer une Action de Script de HDInsight][hdinsight-write-script].

La sortie et les journaux d’erreurs du script sont stockées dans le compte de stockage par défaut que vous avez spécifié pour le cluster. Les journaux sont stockés dans une table avec le nom **u < \cluster-name-fragment >< \time-stamp > setuplog**. Il s’agit de journaux d’agrégation à partir du script à exécuter sur tous les nœuds (nœud de tête et les nœuds de travail) dans le cluster.

Chaque cluster peut accepter plusieurs actions de script qui sont appelées dans l’ordre dans lequel elles sont définies. Un script peut être exécuté sur le nœud de tête, les nœuds de travail ou les deux.

HDInsight propose plusieurs scripts pour installer les composants suivants sur des clusters de HDInsight :

Nom | Script
----- | -----
**Installation d’allumage** | https://hdiconfigactions.BLOB.Core.Windows.NET/sparkconfigactionv03/Spark-installer-v03.ps1. Reportez-vous à la section [installation et utilisation et sur les clusters de HDInsight][hdinsight-install-spark].
**Installation de R** | https://hdiconfigactions.BLOB.Core.Windows.NET/rconfigactionv02/r-installer-v02.ps1. Reportez-vous à la section [installation et utilisation R sur les clusters de HDInsight][hdinsight-install-r].
**Installer le mode série sur LAN.r** | https://hdiconfigactions.BLOB.Core.Windows.NET/solrconfigactionv01/solr-installer-v01.ps1. Voir [installation et l’utilisation des clusters de mode série sur LAN.r sur HDInsight](hdinsight-hadoop-solr-install.md).
- **Installation de Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/giraphconfigactionv01/giraph-installer-v01.ps1. Voir [installation et l’utilisation des clusters Giraph sur HDInsight](hdinsight-hadoop-giraph-install.md).
| **Avant de charger les bibliothèques de ruche** | https://hdiconfigactions.BLOB.Core.Windows.NET/setupcustomhivelibsv01/Setup-customhivelibs-v01.ps1. Voir [Ajouter la ruche de bibliothèques sur des clusters de HDInsight](hdinsight-hadoop-add-hive-libraries.md) |


## <a name="call-scripts-using-the-azure-portal"></a>Scripts d’appel en utilisant le portail Azure

**À partir du portail Azure**

1. Démarrer la création d’un cluster comme décrit au [Hadoop de créer des clusters dans HDInsight](hdinsight-provision-clusters.md#portal).
2. Sous Configuration facultative, de la lame **d’Actions de Script** , cliquez sur **Ajouter une action de script** pour fournir plus d’informations sur l’action du script, comme indiqué ci-dessous :

    ![Action de Script utilisé pour personnaliser un cluster] (./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Action de Script utilisé pour personnaliser un cluster")

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Spécifiez un nom pour l’action du script.</td></tr>
        <tr><td>URI de script</td>
            <td>Spécifiez l’URI du script qui est appelée pour personnaliser le cluster. s</td></tr>
        <tr><td>Tête/travailleur</td>
            <td>Spécifiez les noeuds (**chef** ou **travail**) sur lequel est exécuté le script de personnalisation. </b>.
        <tr><td>Paramètres</td>
            <td>Spécifiez les paramètres, si nécessaire par le script.</td></tr>
    </table>

    Appuyez sur ENTRÉE pour ajouter plus d’une action de script pour installer plusieurs composants sur le cluster.

3. Cliquez sur **Sélectionner** pour enregistrer la configuration de l’action script et poursuivre la création du cluster.

## <a name="call-scripts-using-azure-powershell"></a>Appeler des scripts à l’aide de PowerShell d’Azure

Ce script PowerShell suivant montre comment installer un allumage sur cluster HDInsight en fonction de Windows.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    
    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
    
    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"
    
    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    
    #############################################################
    # Connect to Azure
    #############################################################
    
    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID
    
    #############################################################
    # Prepare the dependent components
    #############################################################
    
    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext
    
    #############################################################
    # Create cluster with ApacheSpark
    #############################################################
    
    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey 
                
    
    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
    
    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Pour installer d’autres logiciels, vous devez remplacer le fichier de script dans le script :


Lorsque vous y êtes invité, entrez les informations d’identification pour le cluster. Elle peut prendre plusieurs minutes avant que le cluster est créé.

## <a name="call-scripts-using-net-sdk"></a>Appeler des scripts à l’aide du Kit de développement .NET 

L’exemple suivant montre comment installer un allumage sur cluster HDInsight en fonction de Windows. Pour installer d’autres logiciels, vous devez remplacer le fichier de script dans le code.

**Pour créer un cluster HDInsight avec allumage** 

1. Créer une application de console C# dans Visual Studio.
2. À partir de la Console du Gestionnaire de package Nuget, exécutez la commande suivante.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

2. Utilisez la commande suivante à l’aide des instructions dans le fichier Program.cs :

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;

3. Placez le code dans la classe par ce qui suit :

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }


4. Appuyez sur **F5** pour exécuter l’application.


## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Prise en charge des logiciels open source utilisés sur des clusters de HDInsight
Le service Microsoft Azure HDInsight est une plate-forme flexible qui vous permet de créer des applications de données volumineuses dans le cloud à l’aide d’un écosystème de technologies open source formé autour d’Hadoop. Microsoft Azure fournit un niveau général de prise en charge pour les technologies open source, comme expliqué dans la section **Étendue de prise en charge** du <a href="http://azure.microsoft.com/support/faq/" target="_blank">site Web du Forum aux questions sur Azure prend en charge</a>. Le service HDInsight fournit un niveau supplémentaire de prise en charge de certains composants, comme décrit ci-dessous.

Il existe deux types de composants open source qui sont disponibles dans le service HDInsight :

- **Composants intégrés** - ces composants est pré-installé sur les clusters de HDInsight et fournir des fonctionnalités de base du cluster. Par exemple, fils de ResourceManager, le langage de requête de ruche (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. Une liste complète des composants de cluster est disponible dans [Nouveautés dans les versions de cluster Hadoop fournies par HDInsight ?](hdinsight-component-versioning.md) </a>.
- Les **composants personnalisés** - vous, en tant qu’utilisateur du cluster, installez ou utilisez dans votre charge de travail de tous les composants disponibles dans la Communauté ou créés par vous.

Composants intégrés sont entièrement supportés, et Support de Microsoft vous permet d’isoler et de résoudre les problèmes liés à ces composants.

> [AZURE.WARNING] Les composants fournis avec le cluster HDInsight sont entièrement gérés et Support de Microsoft vous permet d’isoler et de résoudre les problèmes liés à ces composants.
>
> Composants assistance commercialement raisonnables pour vous aider à résoudre le problème. Cela peut provoquer de résolution du problème ni à vous demander d’engager les canaux disponibles pour les technologies open source où se trouve une grande expertise pour cette technologie. Par exemple, de nombreux sites de la Communauté qui peuvent être utilisés, comme : [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Également les projets Apache ont des sites de projets sur [http://apache.org](http://apache.org), par exemple : [Hadoop](http://hadoop.apache.org/), [commandé](http://spark.apache.org/).

Le service de HDInsight fournit plusieurs moyens d’utiliser des composants personnalisés. Quelle que soit la manière dont un composant est utilisé ou installé sur le cluster, le même niveau de prise en charge s’applique. Vous trouverez ci-dessous la liste des méthodes plus courantes que les composants personnalisés peuvent être utilisés sur des clusters de HDInsight :

1. La soumission de travaux - Hadoop ou autres types de travaux à exécuter ou utilisent des composants personnalisés peut être soumise au cluster.
2. Personnalisation de cluster - lors de la création du cluster, vous pouvez spécifier des paramètres supplémentaires et les composants personnalisés qui seront installés sur les nœuds de cluster.
3. Exemples - pour les composants personnalisés, Microsoft et d’autres peuvent prévoir des exemples d’utilisation de ces composants peuvent sur les clusters HDInsight. Ces exemples sont fournis sans prise en charge.

## <a name="develop-script-action-scripts"></a>Développer des scripts d’Action de Script

Consultez [scripts de développer une Action de Script de HDInsight][hdinsight-write-script].


## <a name="see-also"></a>Voir aussi

- [Créer des clusters d’Hadoop dans HDInsight] [ hdinsight-provision-cluster] fournit des instructions sur la création d’un cluster de HDInsight à l’aide d’autres options personnalisées.
- [Développer des scripts d’Action de Script pour HDInsight][hdinsight-write-script]
- [Installer et utiliser l’allumage sur des clusters de HDInsight][hdinsight-install-spark]
- [Installer et utiliser R sur les clusters de HDInsight][hdinsight-install-r]
- [L’installation et l’utilisation des clusters de mode série sur LAN.r sur HDInsight](hdinsight-hadoop-solr-install.md).
- [Installer et utiliser les clusters Giraph sur HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Étapes de création d’un cluster"
