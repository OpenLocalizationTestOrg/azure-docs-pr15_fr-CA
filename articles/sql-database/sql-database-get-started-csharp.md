<properties
    pageTitle="Try de la base de données SQL : Utilisation C# pour créer une base de données SQL | Microsoft Azure"
    description="Essayez de la base de données SQL pour le développement d’applications SQL et C# et créer une base de données de SQL Azure avec C# à l’aide de la bibliothèque de base de données SQL pour .NET."
    keywords="Essayez sql, sql c#"   
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="10/04/2016"
   ms.author="sstein"/>

# <a name="try-sql-database-use-c-to-create-a-sql-database-with-the-sql-database-library-for-net"></a>Essayez de la base de données SQL : Utiliser C# pour créer une base de données SQL avec la bibliothèque de base de données SQL pour .NET


> [AZURE.SELECTOR]
- [Azure portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Découvrez comment utiliser C# pour créer une base de données Azure SQL avec la [Bibliothèque de gestion des SQL Microsoft Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Cet article décrit comment créer une base de données unique avec SQL et C#. Pour créer les pools élastique de la base de données, voir [créer un pool d’élasticité de la base de données](sql-database-elastic-pool-create-csharp.md).

La bibliothèque de gestion de base de données SQL Azure pour .NET fournit un [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)-en fonction des API qui encapsule l' [API basée sur le Gestionnaire de ressources d ' reste de base de données SQL](https://msdn.microsoft.com/library/azure/mt163571.aspx).

>[AZURE.NOTE] De nombreuses nouvelles fonctionnalités de base de données de SQL sont pris en charge uniquement lorsque vous utilisez le [modèle de déploiement d’Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md), donc vous devez toujours utiliser plus tard **bibliothèque de gestion de base de données de SQL Azure pour .NET ([documents](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. L' ancien [modèle de déploiement classique en fonction des bibliothèques](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) sont pris en charge pour la compatibilité ascendante, nous vous recommandons d’utiliser les bibliothèques de gestionnaire de ressources en fonction de plus récentes.

Pour terminer la procédure décrite dans cet article, vous devez les éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Compte gratuit** en haut de cette page et puis de revenir à la fin de cet article.
- Visual Studio. Pour obtenir un exemplaire gratuit de Visual Studio, consultez la page [Téléchargements de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .

>[AZURE.NOTE] Cet article crée une nouvelle base de données SQL vide. Modifiez la méthode *CreateOrUpdateDatabase(...)* dans l’exemple suivant pour copier les bases de données, faire évoluer les bases de données, créer une base de données dans un pool. Pour plus d’informations, consultez les classes [DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx) et [DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx) .



## <a name="create-a-console-app-and-install-the-required-libraries"></a>Créer une application console et installer les bibliothèques requises

1. Démarrez Visual Studio.
2. Cliquez sur **le fichier** > **Nouveau** > **projet**.
3. Créez une **Application Console** C# et nommez-la : *SqlDbConsoleApp*


Pour créer une base de données SQL avec C#, charger les bibliothèques de gestion requis (à l’aide de la [console du Gestionnaire de package](http://docs.nuget.org/Consume/Package-Manager-Console)) :

1. Cliquez sur **Outils** > **du Gestionnaire de package de NuGet** > **Console du Gestionnaire de package**.
2. Type de `Install-Package Microsoft.Azure.Management.Sql –Pre` pour installer la dernière version de [Bibliothèque de gestion de SQL Azure Microsoft](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Type de `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` pour installer la [Bibliothèque de gestionnaire de ressources de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Type de `Install-Package Microsoft.Azure.Common.Authentication –Pre` pour installer la [Bibliothèque d’authentification commun de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 



> [AZURE.NOTE] Les exemples de cet article utilisent un formulaire synchrone de chaque demande d’API et bloquent jusqu'à ce que l’achèvement du reste d’appeler sur le service sous-jacent. Les méthodes asynchrones sont disponibles.


## <a name="create-a-sql-database-server-firewall-rule-and-sql-database---c-example"></a>Créer un serveur de base de données de SQL, les règles de pare-feu et base de données SQL - exemple C#

L’exemple suivant crée un groupe de ressources, serveur, règle de pare-feu et une base de données SQL. Afficher, [créer un service principal pour accéder aux ressources](#create-a-service-principal-to-access-resources) pour obtenir les `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` les variables.

Remplacez le contenu de **Program.cs** par celui-ci et mettre à jour le `{variables}` avec les valeurs de votre application (n’incluez pas le `{}`).


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbConsoleApp
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

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


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

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
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



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
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
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
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
Maintenant que vous avez essayé de la base de données SQL, et configurer une base de données avec C#, vous êtes prêt pour les articles suivants :

- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Classe de base de données](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)




<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png
