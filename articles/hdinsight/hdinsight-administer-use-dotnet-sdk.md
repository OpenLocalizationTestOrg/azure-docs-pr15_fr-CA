<properties
    pageTitle="Gérer les clusters Hadoop dans HDInsight avec le Kit de développement .NET | Microsoft Azure"
    description="Découvrez comment effectuer des tâches administratives pour les clusters Hadoop dans HDInsight à l’aide du Kit de développement .NET HDInsight."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Gérer les clusters Hadoop dans HDInsight à l’aide du Kit de développement .NET

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Découvrez comment gérer les clusters HDInsight à l’aide du [Kit de développement logiciel HDInsight.NET](https://msdn.microsoft.com/library/mt271028.aspx).


**Conditions préalables**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


##<a name="connect-to-azure-hdinsight"></a>Se connecter à HDInsight Azure

Vous devez les packages Nuget suivants :

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.HDInsight

L’exemple de code suivant vous montre comment se connecter à Azure avant de pouvoir administrer les clusters HDInsight sous votre abonnement Azure.

    using System;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.HDInsight;
    using Microsoft.Azure.Management.HDInsight.Models;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    namespace HDInsightManagement
    {
        class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
            // Replace with your AAD tenant ID if necessary
            private const string TenantId = UserTokenProvider.CommonTenantId; 
            private const string SubscriptionId = "<Your Azure Subscription ID>";
            // This is the GUID for the PowerShell client. Used for interactive logins in this example.
            private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

            static void Main(string[] args)
            {
                // Authenticate and get a token
                var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                // Flag subscription for HDInsight, if it isn't already.
                EnableHDInsight(authToken);
                // Get an HDInsight management client
                _hdiManagementClient = new HDInsightManagementClient(authToken);

                // insert code here

                System.Console.WriteLine("Press ENTER to continue");
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

Est qu’une invite s’affiche lorsque vous exécutez ce programme.  Si vous ne souhaitez pas voir l’invite de commandes, consultez [créer des applications de HDInsight de .NET de l’authentification non interactive](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

##<a name="create-clusters"></a>Créer des clusters

Consultez [basé sur Linux de créer des clusters dans HDInsight à l’aide du Kit de développement .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

##<a name="list-clusters"></a>Liste des clusters

L’extrait de code suivant répertorie les clusters et certaines propriétés :

    var results = _hdiManagementClient.Clusters.List();
    foreach (var name in results.Clusters) {
        Console.WriteLine("Cluster Name: " + name.Name);
        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
        Console.WriteLine("\t Cluster location: " + name.Location);
        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
    }

##<a name="delete-clusters"></a>Supprimer de clusters

L’extrait de code suivant permet de supprimer un cluster de façon synchrone ou asynchrone : 

    _hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
    _hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
            
##<a name="scale-clusters"></a>Clusters d’échelle
La fonctionnalité de mise à l’échelle de cluster vous permet de modifier le nombre de nœuds de traitement utilisé par un cluster qui exécute dans Azure HDInsight sans avoir à recréer le cluster.

>[AZURE.NOTE] Les clusters uniquement avec HDInsight version 3.1.3 ou plus sont pris en charge. Si vous êtes incertain de la version de votre cluster, vous pouvez vérifier la page Propriétés.  Voir la [liste et afficher des clusters](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

L’impact de la modification du nombre de nœuds de données pour chaque type de cluster prises en charge par HDInsight :

- Hadoop

    Vous pouvez facilement augmenter le nombre de nœuds de traitement d’un cluster d’Hadoop qui est en cours d’exécution sans impact sur les tâches en attente ou en cours d’exécution. Nouveaux travaux peuvent également être soumises lorsque l’opération est en cours. Échecs lors d’une opération de mise à l’échelle sont correctement gérées afin que le cluster reste toujours en état de fonctionnement.

    Lorsqu’un cluster Hadoop est réduite par la réduction du nombre de nœuds de données, les services du cluster sont redémarrés. Ainsi, en cours d’exécution et en attente de travaux d’échec à la fin de l’opération de mise à l’échelle. Vous pouvez, toutefois, renvoyer les travaux une fois l’opération terminée.

- HBase

    Vous pouvez parfaitement ajouter ou supprimer des nœuds de votre cluster de HBase en cours d’exécution. Les serveurs régionaux sont équilibrées automatiquement après quelques minutes de la fin de l’opération de mise à l’échelle. Toutefois, vous pouvez également manuellement équilibrer les serveurs régionaux en établissant une session dans le headnode de cluster et exécute les commandes suivantes à partir d’une fenêtre d’invite de commande :

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Orage

    Vous pouvez en toute transparence d’ajouter ou de supprimer des nœuds de données à votre cluster tempête pendant son exécution. Mais après la réussite de l’opération de mise à l’échelle, vous devrez rééquilibrer la topologie.

    Rééquilibrage peut être réalisé de deux manières :

    * Interface utilisateur web de tempête
    * Outil de l’interface de ligne de commande (CLI)

    Reportez-vous à la [documentation de tempête d’Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) pour plus de détails.

    L’interface utilisateur du web de Storm est disponible sur le cluster de HDInsight :

    ![rééquilibrage d’échelle hdinsight tempête](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Voici un exemple de l’utilisation de la commande CLI pour rééquilibrer la topologie Storm :

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

L’extrait de code suivant montre comment redimensionner un cluster de façon synchrone ou asynchrone :

    _hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    _hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    

##<a name="grantrevoke-access"></a>Accès de GRANT ou revoke

HDInsight clusters ont les services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Par défaut, ces services sont accordées pour l’accès. Vous pouvez révoquer/accorder l’accès. Pour révoquer :

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = false,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);

Pour accorder :

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = enable,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);


>[AZURE.NOTE] Par attribution/révocation de l’accès, vous allez réinitialiser le nom d’utilisateur de cluster et d’un mot de passe.

Il est également possible via le portail. Reportez-vous à [Administrer les HDInsight à l’aide du portail Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Mettre à jour les informations d’identification utilisateur HTTP

Il s’agit de la même procédure que [l’accès HTTP de Grant ou revoke](#grant/revoke-access). Si le cluster a été accordé l’accès HTTP, vous devez tout d’abord le révoquez pas.  Puis accorder l’accès avec les nouvelles informations d’identification utilisateur HTTP.


##<a name="find-the-default-storage-account"></a>Recherchez le compte de stockage par défaut

L’extrait de code suivant montre comment obtenir le nom de compte de stockage par défaut et la clé de compte de stockage par défaut pour un cluster.

    var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
    foreach (var key in results.Configuration.Keys)
    {
        Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
    }


##<a name="submit-jobs"></a>Soumettre des travaux

**Pour soumettre les tâches de MapReduce**

Consultez les [exemples d’exécuter le Hadoop MapReduce dans HDInsight](hdinsight-hadoop-run-samples-linux.md).

**Pour soumettre les tâches de la ruche** 

Consultez [la ruche exécuter des requêtes à l’aide du Kit de développement .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md).

**Pour soumettre les tâches de porc**

Voir [porc d’exécuter des travaux à l’aide du Kit de développement .NET](hdinsight-hadoop-use-pig-dotnet-sdk.md).

**Pour soumettre les tâches de Sqoop**

Reportez-vous à la section [Sqoop utilisation avec HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md).

**Pour soumettre les tâches de Oozie**

Reportez-vous à la section [Oozie utilisation avec Hadoop pour définir et exécuter un flux de travail dans HDInsight](hdinsight-use-oozie-linux-mac.md).

##<a name="upload-data-to-azure-blob-storage"></a>Télécharger les données vers le stockage des objets Blob Azure
Voir [télécharger les données pour HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Voir aussi
* [Documentation de référence de HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)
* [Administrer des HDInsight à l’aide du portail Azure][hdinsight-admin-portal]
* [Administrer les HDInsight à l’aide d’une interface de ligne de commande][hdinsight-admin-cli]
* [Créer des clusters HDInsight][hdinsight-provision]
* [Téléchargement des données vers HDInsight][hdinsight-upload-data]
* [Mise en route de HDInsight d’Azure][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


