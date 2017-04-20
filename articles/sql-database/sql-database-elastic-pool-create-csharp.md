<properties
    pageTitle="Créer un pool de la base de données élastique avec C# | Microsoft Azure"
    description="Utilisez les techniques de développement C# de base de données pour créer un pool élastique évolutive de la base de données dans la base de données de SQL Azure, vous pouvez partager des ressources entre plusieurs bases de données."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="create-an-elastic-database-pool-with-cx23"></a>Créer un pool d’élasticité de la base de données avec C & #x 23 ;

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Cet article explique comment utiliser C# pour créer un pool d’élasticité de la base de données Azure SQL avec la [Bibliothèque de base de données SQL Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Pour créer une base de données SQL autonome, consultez [utiliser C# pour créer une base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md).

La bibliothèque de base de données SQL Azure pour .NET fournit un [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)-en fonction des API qui encapsule l' [API basée sur le Gestionnaire de ressources d ' reste de base de données SQL](https://msdn.microsoft.com/library/azure/mt163571.aspx).

>[AZURE.NOTE] De nombreuses nouvelles fonctionnalités de base de données de SQL sont pris en charge uniquement lorsque vous utilisez le [modèle de déploiement d’Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md), donc vous devez toujours utiliser plus tard **bibliothèque de gestion de base de données de SQL Azure pour .NET ([documents](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. L' ancien [modèle de déploiement classique en fonction des bibliothèques](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) sont pris en charge pour la compatibilité ascendante, nous vous recommandons d’utiliser les bibliothèques de gestionnaire de ressources en fonction de plus récentes.

Pour terminer la procédure décrite dans cet article, vous devez les éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Compte gratuit** en haut de cette page et puis de revenir à la fin de cet article.
- Visual Studio. Pour obtenir un exemplaire gratuit de Visual Studio, consultez la page [Téléchargements de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="create-a-console-app-and-install-the-required-libraries"></a>Créer une application console et installer les bibliothèques requises

1. Démarrez Visual Studio.
2. Cliquez sur **le fichier** > **Nouveau** > **projet**.
3. Créez une **Application Console** C# et nommez-la : *SqlElasticPoolConsoleApp*


Pour créer une base de données SQL avec C#, charger les bibliothèques de gestion requis (à l’aide de la [console du Gestionnaire de package](http://docs.nuget.org/Consume/Package-Manager-Console)) :

1. Cliquez sur **Outils** > **du Gestionnaire de package de NuGet** > **Console du Gestionnaire de package**.
2. Type de `Install-Package Microsoft.Azure.Management.Sql –Pre` pour installer la [Bibliothèque de gestion de SQL Azure Microsoft](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Type de `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` pour installer la [Bibliothèque de gestionnaire de ressources de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Type de `Install-Package Microsoft.Azure.Common.Authentication –Pre` pour installer la [Bibliothèque d’authentification commun de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 



> [AZURE.NOTE] Les exemples de cet article utilisent un formulaire synchrone de chaque demande d’API et bloquent jusqu'à ce que l’achèvement du reste d’appeler sur le service sous-jacent. Les méthodes asynchrones sont disponibles.


## <a name="create-a-sql-elastic-database-pool---c-example"></a>Créer un pool d’élastique de la base de données SQL - exemple C#

L’exemple suivant crée un groupe de ressources, le serveur, la règle de pare-feu, la pool élastique, puis crée une base de données SQL dans le pool. Afficher, [créer un service principal pour accéder aux ressources](#create-a-service-principal-to-access-resources) pour obtenir les `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` les variables.

Remplacez le contenu de **Program.cs** par celui-ci et mettre à jour le `{variables}` avec les valeurs de votre application (n’incluez pas le `{}`).


```
using Microsoft.Azure;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.Azure.Management.Sql;
using Microsoft.Azure.Management.Sql.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace SqlElasticPoolConsoleApp
{
    class Program
        {

        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _poolName = "{pool-name}";
        static string _poolEdition = "{Standard}";
        static int _poolDtus = {100};
        static int _databaseMinDtus = {10};
        static int _databaseMaxDtus = {100};

        static string _databaseName = "{elasticdbfromcsarticle}";



        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Elastic pool...");
            ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);
            Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static ElasticPoolCreateOrUpdateResponse CreateOrUpdateElasticDatabasePool(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string poolName, string poolEdition, int poolDtus, int databaseMinDtus, int databaseMaxDtus)
        {
            // Retrieve the server that will host this elastic pool
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus,
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlMgmtClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }




        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string poolName)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    ElasticPoolName = poolName
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
    }
}
```





## <a name="create-a-service-principal-to-access-resources"></a>Création d’un service principal pour accéder aux ressources

Le script PowerShell suivant crée l’application d’Active Directory (AD) et l’entité du service dont nous avons besoin pour authentifier notre application C#. Le script renvoie les valeurs que nous avons besoin pour l’exemple C# précédent. Pour plus d’informations, voir [Utilisation des PowerShell de Azure pour créer un service principal pour accéder aux ressources](../resource-group-authenticate-service-principal.md).

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret


  

## <a name="next-steps"></a>Étapes suivantes

- [Gérer votre pool](sql-database-elastic-pool-manage-csharp.md)
- [Créer des travaux élastiques](sql-database-elastic-jobs-overview.md): travaux élastiques vous permettent d’exécuter les scripts T-SQL par rapport à n’importe quel nombre de bases de données dans un pool.
- [Mise à l’échelle avec la base de données de SQL Azure](sql-database-elastic-scale-introduction.md): utiliser des outils de base de données élastique à l’échelle.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [API de gestion de ressources Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
