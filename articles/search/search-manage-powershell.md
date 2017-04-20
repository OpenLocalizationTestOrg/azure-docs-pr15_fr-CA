<properties 
    pageTitle="Gérer Azure recherche avec scripts Powershell | Microsoft Azure | Service de recherche de nuage hébergé" 
    description="Gérer votre service de recherche d’Azure avec scripts PowerShell. Créer ou mettre à jour un service de recherche d’Azure et gérer les clés de recherche d’Azure admin" 
    services="search" 
    documentationCenter="" 
    authors="seansaleh" 
    manager="mblythe" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="search" 
    ms.devlang="na" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="powershell" 
    ms.date="08/15/2016" 
    ms.author="seasa"/>

# <a name="manage-your-azure-search-service-with-powershell"></a>Gérer votre service de recherche d’Azure avec PowerShell
> [AZURE.SELECTOR]
- [Portail](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

Cette rubrique décrit les commandes PowerShell pour effectuer la plupart des tâches de gestion de services de recherche d’Azure. Nous étudierons la création d’un service de recherche, redimensionnement et la gestion de ses clés de l’API.
Ces commandes sont parallèles les options de gestion disponibles dans l' [API REST de Azure recherche gestion](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Conditions préalables
 
- Vous devez avoir Azure PowerShell 1.0 ou supérieure. Pour obtenir des instructions, reportez-vous à la section [installer et configurer Azure PowerShell](../powershell-install-configure.md).
- Vous devez être connecté à votre abonnement Azure dans PowerShell comme décrit ci-dessous.

Vous devez tout d’abord, connexion vers Azure avec cette commande :

    Login-AzureRmAccount

Spécifiez l’adresse e-mail de votre compte Azure et son mot de passe dans la boîte de dialogue de connexion de Microsoft Azure.

Vous pouvez également [connecter en mode non interactif avec un service principal](../resource-group-authenticate-service-principal.md).

Si vous avez plusieurs abonnements Azure, vous devez définir votre abonnement Azure. Pour afficher la liste de vos abonnements actuels, exécutez la commande suivante.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Pour spécifier l’abonnement, exécutez la commande suivante. Dans l’exemple suivant, le nom de l’abonnement est `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Commandes pour vous aider à démarrer

    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1
    
    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
    
    # View your resource
    $resource
    
    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key
    
    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
        
    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource
    
    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource
    
## <a name="next-steps"></a>Étapes suivantes
    
Maintenant que votre service est créé, vous pouvez passer aux étapes suivantes : création d’un [index](search-what-is-an-index.md), [un index de requête](search-query-overview.md)et enfin créer et gérer votre propre application de recherche qui utilise la recherche d’Azure.

- [Créer un index de recherche d’Azure dans le portail Azure](search-create-index-portal.md)

- [Interroger un index de recherche d’Azure à l’aide de l’Explorateur recherche dans le portail Azure](search-explorer.md)

- [Programme d’installation un indexeur pour charger des données provenant d’autres services](search-indexer-overview.md)

- [Comment faire pour utiliser la recherche d’Azure dans .NET](search-howto-dotnet-sdk.md)

- [Analyser votre trafic Azure](search-traffic-analytics.md)
