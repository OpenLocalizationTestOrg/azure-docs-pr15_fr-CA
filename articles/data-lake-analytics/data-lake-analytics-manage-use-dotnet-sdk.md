<properties 
   pageTitle="Gérer l’Analytique de LAC de données Azure à l’aide du Kit de développement .NET Azure | Azure" 
   description="Apprenez à gérer les tâches de données lac Analytique, les sources de données, les utilisateurs. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="jgao"/>

# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gérer l’Analytique de LAC de données Azure à l’aide du Kit de développement .NET Azure

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Découvrez comment gérer des comptes d’Azure données lac Analytique, sources de données, les utilisateurs et les tâches à l’aide du Kit de développement .NET Azure. Pour afficher les rubriques de gestion à l’aide d’autres outils, cliquez sur l’onglet, sélectionnez ci-dessus.

**Conditions préalables**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>Se connecter à des données Azure lac Analytique

Vous devez les packages Nuget suivants :

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


L’exemple de code suivant vous montre comment se connecter à Azure et répertorier les comptes de données lac Analytique existants sous votre abonnement Azure.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>Gérer les comptes

Avant d’exécuter les tâches de données lac Analytique, vous devez disposer d’un compte Analytique lac de données. À la différence d’Azure, HDInsight, vous payez un compte Analytique lorsqu’il n’est pas exécuté un travail.  Vous ne payez que pour le temps que lorsqu’il exécute une tâche.  Pour plus d’informations, voir [Vue d’ensemble de Azure données lac Analytique](data-lake-analytics-overview.md).  

###<a name="create-accounts"></a>Créer des comptes

Il vous faut un groupe de gestion des ressources Azure et d’un compte de banque de données lac avant de pouvoir exécuter l’exemple suivant.

Le code suivant montre comment créer un groupe de ressources :

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

Le code suivant montre comment créer un compte de banque de données lac :

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

Le code suivant montre comment créer un compte Analytique lac de données :

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

###<a name="list-accounts"></a>Liste des comptes

Voir [se connecter à des données Azure lac Analytique](#connect_to_azure_data_lake_analytics).

###<a name="find-an-account"></a>Rechercher un compte

Une fois que vous obtenez un objet d’une liste de comptes de données lac Analytique, vous pouvez utiliser les éléments suivants pour rechercher un compte :

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

###<a name="delete-data-lake-analytics-accounts"></a>Supprimer des comptes de données lac Analytique

L’extrait de code suivant supprime un compte Analytique lac de données :

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gérer les sources de données de compte

Données lac Analytique prend actuellement en charge les sources de données suivantes :

- [Stockage de LAC de données Azure](../data-lake-store/data-lake-store-overview.md)
- [Stockage Azure](../storage/storage-introduction.md)

Lorsque vous créez un compte Analytique, vous devez désigner un compte de stockage Azure LAC pour être le compte de stockage par défaut. Le compte de la banque de données lac par défaut permet de stocker les journaux de d’audit de métadonnées et une tâche de travail. Après avoir créé un compte Analytique, vous pouvez ajouter des comptes supplémentaires de stockage de données LAC et/ou le compte de stockage Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Trouver le compte de la banque de données lac par défaut

Reportez-vous à la section recherche un compte dans cet article pour rechercher le compte Analytique lac de données. Utilisez la commande suivante :

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>Utiliser les groupes du Gestionnaire de ressources Azure

Les applications sont généralement composées de nombreux composants, par exemple une application web, de base de données, serveur de base de données, stockage et services de tiers 3e. Azure Resource Manager vous permet de travailler avec les ressources de votre application en tant que groupe, visé un groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans une opération unique et coordonnée. Vous utilisez un modèle de déploiement, et ce modèle peut fonctionner dans différents environnements comme test, intermédiaire et production. Vous pouvez clarifier la facturation pour votre organisation en affichant les coûts reportées pour le groupe entier. Pour plus d’informations, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md). 

Un service de données lac Analytique peut inclure les éléments suivants :

- Compte Analytique de LAC de données Azure
- Compte de stockage Azure lac par défaut requis
- Comptes de stockage complémentaire Azure données lac
- Comptes de stockage Azure supplémentaires

Vous pouvez créer tous ces composants dans un groupe de gestion des ressources afin de les rendre plus faciles à gérer.

![Stockage et compte Analytique de LAC de données azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un compte Analytique lac de données et les comptes de stockage dépendant doivent être placés dans le même centre de données Azure.
Toutefois, le groupe de gestion des ressources peut se trouver dans un autre centre de données.  

##<a name="see-also"></a>Voir aussi 

- [Vue d’ensemble de Microsoft Azure données lac Analytique](data-lake-analytics-overview.md)
- [Mise en route de données Analytique de LAC avec Azure portal](data-lake-analytics-get-started-portal.md)
- [Gérer l’Analytique de LAC de données Azure à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
- [Surveiller et résoudre les problèmes de travaux Azure données lac Analytique à l’aide d’Azure portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

