<properties
   pageTitle="Obtenir les valeurs requises pour l’authentification d’une application à accéder de la base de données SQL à partir de code | Microsoft Azure"
   description="Créez un principal de service pour accéder à la base de données SQL à partir du code."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Obtenir les valeurs requises pour l’authentification d’une application à accéder de la base de données SQL à partir du code

Pour créer et gérer de la base de données SQL à partir du code, vous devez inscrire votre application dans le domaine d’Azure Active Directory (DAS) dans l’abonnement où vos ressources Azure ont été créés.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Création d’un service principal pour accéder aux ressources d’une application

Vous devez disposer de la dernière [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) installé et en cours d’exécution. Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

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




## <a name="see-also"></a>Voir aussi

- [Créer une base de données SQL avec C#](sql-database-get-started-csharp.md)
- [Connexion à la base de données SQL à l’aide de l’authentification Azure Active Directory](sql-database-aad-authentication.md)


