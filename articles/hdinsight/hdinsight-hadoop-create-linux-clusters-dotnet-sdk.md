<properties
    pageTitle="Créer des clusters Hadoop, HBase, tempête ou étincelle sur Linux dans HDInsight à l’aide du Kit de développement .NET HDInsight | Microsoft Azure"
    description="Apprenez à créer des clusters Hadoop, HBase, tempête ou étincelle sous Linux pour HDInsight à l’aide du Kit de développement .NET HDInsight."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/02/2016"
    ms.author="jgao"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-the-net-sdk"></a>Créer des grappes de serveurs Linux dans HDInsight à l’aide du Kit de développement .NET

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Le Kit de développement .NET HDInsight fournit les bibliothèques clientes .NET qui le rendent plus facile à manipuler de HDInsight à partir d’une application.NET Framework. Ce document montre comment créer un cluster basé sur Linux de HDInsight à l’aide du Kit de développement .NET.

> [AZURE.IMPORTANT] Les étapes décrites dans ce document créent un cluster avec un nœud d’un travailleur. Si vous prévoyez de plus de 32 nœuds de travailleur, lors de la création du cluster ou en redimensionnant le cluster après sa création, vous devez sélectionner une taille de nœud de tête avec au moins 8 cœurs et 14 Go de ram.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, reportez-vous à la section [HDInsight de tarification](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Visual Studio 2013 ou 2015__

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Créer des clusters

1. Ouvrez Visual Studio 2013 ou 2015.

2. Créer un nouveau projet de Visual Studio avec les paramètres suivants

  	|Propriété|Valeur|
  	|--------|-----|
  	|Modèle|Application de la Console/Windows/modèles/Visual C#|
  	|Nom|CreateHDICluster|

5. Dans le menu **Outils** , cliquez sur **Gestionnaire de package de Nuget**, puis cliquez sur **Console de Gestionnaire de package**.

6. Exécutez la commande suivante dans la console pour installer les packages :

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

    Ces commandes ajoutent les bibliothèques .NET et les références correspondantes au projet Visual Studio actuel.

6. À partir de l’Explorateur de solutions, double-cliquez sur **Program.cs** pour l’ouvrir, collez le code suivant et fournir des valeurs pour les variables :

        using System;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Net.Http;
        using Newtonsoft.Json;
        using System.Collections.Generic;

        namespace CreateHDInsightCluster
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;

                // Replace with your AAD tenant ID if necessary
                private const string TenantId = UserTokenProvider.CommonTenantId; 
                private const string SubscriptionId = "<Your Azure Subscription ID>";
                // This is the GUID for the PowerShell client. Used for interactive logins in this example.
                private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

                private static string SubscriptionId = "<Enter Your Subscription ID>";
                private const string ExistingResourceGroupName = "<Enter Resource Group Name>";
                private const string ExistingStorageName = "<Enter Default Storage Account Name>.blob.core.windows.net";
                private const string ExistingStorageKey = "<Enter Default Storage Account Key>";
                private const string ExistingBlobContainer = "<Enter Default Bob Container Name>";
                private const string NewClusterName = "<Enter HDInsight Cluster Name>";
                private const int NewClusterNumNodes = 2;
                private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
                private const OSType NewClusterOSType = OSType.Linux;
                private const string NewClusterType = "Hadoop";
                private const string NewClusterVersion = "3.2";
                private const string NewClusterUsername = "admin";
                private const string NewClusterPassword = "<Enter HTTP User Password>";
                private const string NewClusterSshUserName = "sshuser";
                private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
                    Comment: ""rsa-key-20150731""
                    AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
                    gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
                    yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
                    WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
                    pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
                    zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
                    ---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

                static void Main(string[] args)
                {
                    System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

                    // Authenticate and get a token
                    var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                    // Flag subscription for HDInsight, if it isn't already.
                    EnableHDInsight(authToken);
                    // Get an HDInsight management client
                    _hdiManagementClient = new HDInsightManagementClient(authToken);

                    // Set parameters for the new cluster
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        UserName = NewClusterUsername,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOSType,
                        Version = NewClusterVersion,

                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingBlobContainer,

                        Password = NewClusterPassword,
                        Location = NewClusterLocation,

                        SshUserName = NewClusterSshUserName,
                        SshPublicKey = NewClusterSshPublicKey
                    };
                    // Create the cluster
                    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

                    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
                    System.Console.ReadLine();
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
            }
        }
    
10. Remplacez les valeurs de membre de classe.

7. Appuyez sur **F5** pour exécuter l’application. Une fenêtre de console doit ouvrir et afficher l’état de l’application. Vous devrez également entrer vos informations d’identification de compte Azure. Elle peut prendre plusieurs minutes pour créer un cluster HDInsight, normalement environ 15.

## <a name="use-bootstrap"></a>Utilisation des données d’amorçage

Pour plus d’informations, voir [clusters de HDInsight de personnaliser à l’aide des données d’amorçage](hdinsight-hadoop-customize-cluster-bootstrap.md).

Modifiez l’exemple de [la création de clusters](#create-clusters) pour configurer un paramètre de ruche :

    static void Main(string[] args)
    {
        System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

        // Authenticate and get a token
        var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
        // Flag subscription for HDInsight, if it isn't already.
        EnableHDInsight(authToken);
        // Get an HDInsight management client
        _hdiManagementClient = new HDInsightManagementClient(authToken);

        // Set parameters for the new cluster
        var extendedParameters = new ClusterCreateParametersExtended
        {
            Location = NewClusterLocation,
            Properties = new ClusterCreateProperties
            {
                ClusterDefinition = new ClusterDefinition
                {
                    ClusterType = NewClusterType.ToString()
                },
                ClusterVersion = NewClusterVersion,
                OperatingSystemType = NewClusterOSType
            }
        };

        var coreConfigs = new Dictionary<string, string>
        {
            {"fs.defaultFS", string.Format("wasbs://{0}@{1}", ExistingBlobContainer, ExistingStorageName)},
            {
                string.Format("fs.azure.account.key.{0}", ExistingStorageName),
                ExistingStorageKey
            }
        };

        // bootstrap
        var hiveConfigs = new Dictionary<string, string>
        {
            { "hive.metastore.client.socket.timeout", "90"}
        };

        var gatewayConfigs = new Dictionary<string, string>
        {
            {"restAuthCredential.isEnabled", "true"},
            {"restAuthCredential.username", NewClusterUsername},
            {"restAuthCredential.password", NewClusterPassword}
        };

        var configurations = new Dictionary<string, Dictionary<string, string>>
        {
            {"core-site", coreConfigs},
            {"gateway", gatewayConfigs},
            {"hive-site", hiveConfigs}
        };

        var serializedConfig = JsonConvert.SerializeObject(configurations);
        extendedParameters.Properties.ClusterDefinition.Configurations = serializedConfig;

        var sshPublicKeys = new List<SshPublicKey>();
        var sshPublicKey = new SshPublicKey
        {
            CertificateData =
                string.Format("ssh-rsa {0}", NewClusterSshPublicKey)
        };
        sshPublicKeys.Add(sshPublicKey);

        var headNode = new Role
        {
            Name = "headnode",
            TargetInstanceCount = 2,
            HardwareProfile = new HardwareProfile
            {
                VmSize = "Large"
            },
            OsProfile = new OsProfile
            {
                LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
                {
                    UserName = NewClusterSshUserName,
                    Password = NewClusterSshPassword //,
                    // When use a SSH pulbic key, make sure to remove comments, headers and trailers, and concatenate the key into one line 
                    //SshProfile = new SshProfile
                    //{
                    //    SshPublicKeys = sshPublicKeys
                    //}
                }
            }
        };

        var workerNode = new Role
        {
            Name = "workernode",
            TargetInstanceCount = NewClusterNumNodes,
            HardwareProfile = new HardwareProfile
            {
                VmSize = "Large"
            },
            OsProfile = new OsProfile
            {
                LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
                {
                    UserName = NewClusterSshUserName,
                    Password = NewClusterSshPassword //,
                    //SshProfile = new SshProfile
                    //{
                    //    SshPublicKeys = sshPublicKeys
                    //}
                }
            }
        };

        extendedParameters.Properties.ComputeProfile = new ComputeProfile();
        extendedParameters.Properties.ComputeProfile.Roles.Add(headNode);
        extendedParameters.Properties.ComputeProfile.Roles.Add(workerNode);

        _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, extendedParameters);

        System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
        System.Console.ReadLine();
    }


## <a name="use-script-action"></a>Utilisez l’Action de Script

Pour plus d’informations, consultez [clusters basés sur Linux de personnaliser la HDInsight à l’aide des actions de Script](hdinsight-hadoop-customize-cluster-linux.md).

Modifier l’exemple dans les [clusters de créer](#create-clusters) pour appeler une Action de Script pour installer R:

    static void Main(string[] args)
    {
        System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

        // Authenticate and get a token
        var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
        // Flag subscription for HDInsight, if it isn't already.
        EnableHDInsight(authToken);
        // Get an HDInsight management client
        _hdiManagementClient = new HDInsightManagementClient(authToken);

        // Set parameters for the new cluster
        var parameters = new ClusterCreateParameters
        {
            ClusterSizeInNodes = NewClusterNumNodes,
            Location = NewClusterLocation,
            ClusterType = NewClusterType,
            OSType = NewClusterOSType,
            Version = NewClusterVersion,

            DefaultStorageAccountName = ExistingStorageName,
            DefaultStorageAccountKey = ExistingStorageKey,
            DefaultStorageContainer = ExistingBlobContainer,

            UserName = NewClusterUsername,
            Password = NewClusterPassword,
            SshUserName = NewClusterSshUserName,
            SshPublicKey = NewClusterSshPublicKey
        };

        ScriptAction rScriptAction = new ScriptAction("Install R",
            new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

        parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
        parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });
        
        _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

        System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
        System.Console.ReadLine();
    }

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un cluster HDInsight, utilisez ce qui suit pour apprendre à travailler avec votre cluster. 

###<a name="hadoop-clusters"></a>Hadoop clusters

* [Utilisez la ruche avec HDInsight](hdinsight-use-hive.md)
* [Utilisez des porcs avec HDInsight](hdinsight-use-pig.md)
* [Utilisez MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>Clusters HBase

* [Mise en route de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clusters de tempête

* [Développer des topologies de Java pour tempête sur HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilisation des composants de Python de tempête sur HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déployer et surveiller des topologies avec tempête sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###<a name="spark-clusters"></a>Clusters d’allumage

* [Créez une application autonome à l’aide de Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance sur un cluster d’allumage à l’aide de Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Allumage avec BI : effectuer l’analyse interactive des données à l’aide d’étincelle dans HDInsight avec les outils d’analyse Décisionnelle](hdinsight-apache-spark-use-bi-tools.md)
* [Allumage avec apprentissage automatique : allumage utilisation de HDInsight pour prédire les résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Diffusion en continu de l’allumage : Allumage d’utilisation dans HDInsight pour générer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

### <a name="run-jobs"></a>Exécution de tâches

- [Exécution de tâches de la ruche dans la HDInsight à l’aide du Kit de développement .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md)
- [Exécuter des travaux de porcs dans HDInsight à l’aide du Kit de développement .NET](hdinsight-hadoop-use-pig-dotnet-sdk.md)
- [Exécuter des tâches de Sqoop de HDInsight à l’aide du Kit de développement .NET](hdinsight-hadoop-use-sqoop-dotnet-sdk.md)
- [Exécuter des tâches de Oozie dans HDInsight](hdinsight-use-oozie.md)
