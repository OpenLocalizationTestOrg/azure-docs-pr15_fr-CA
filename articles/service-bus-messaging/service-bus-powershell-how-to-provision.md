<properties
    pageTitle="Gérer le Bus de Service avec PowerShell | Microsoft Azure"
    description="Gérer le Bus de Service avec PowerShell scripts"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="sethm"/>

# <a name="manage-service-bus-with-powershell"></a>Gérer le Bus de Service avec PowerShell

## <a name="overview"></a>Vue d’ensemble

Microsoft Azure PowerShell est un environnement de script qui vous permet de contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cet article décrit comment utiliser PowerShell pour provisionner et gérer les entités de Bus des services, tels que les espaces de noms, les files d’attente et les concentrateurs d’événement à l’aide d’une console d’Azure PowerShell locale.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer les conditions préalables suivantes :

- Un abonnement Azure. Azure est une plateforme basée sur abonnement. Pour plus d’informations sur l’obtention d’un abonnement, consultez les [Options d’achat][], [L’offre de membre][]ou [Version d’évaluation gratuite][].

- Un ordinateur avec PowerShell d’Azure. Pour obtenir des instructions, reportez-vous à la section [installer et configurer Azure PowerShell][].

- Une connaissance générale des scripts PowerShell, packages NuGet et le.NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Inclusion d’une référence à l’assembly .NET pour le Bus de Service

Il existe un nombre limité d’applets de commande PowerShell pour la gestion des Bus de Service. Pour provisionner des entités qui ne sont pas exposées via les applets de commande existant, vous pouvez utiliser le client .NET pour le Bus de Service dans le [package NuGet de Bus de Service][].

Tout d’abord, assurez-vous que le script peut localiser l’assembly **Microsoft.ServiceBus.dll** , qui est installé avec le package NuGet. Pour être flexible, le script procède comme suit :

1. Détermine le chemin d’accès auquel elle a été appelée.
2. Parcourt le chemin d’accès jusqu'à ce qu’il trouve un dossier nommé `packages`. Ce dossier est créé lorsque vous installez les packages NuGet.
3. Recherche de manière récursive la `packages` dossier pour un assembly nommé **Microsoft.ServiceBus.dll**.
4. Référence de l’assembly afin que les types sont disponibles pour une utilisation ultérieure.

Voici comment ces étapes sont implémentés dans un script PowerShell :

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Fourniture d’un espace de noms du Bus des services

Deux applets de commande PowerShell prend en charge les opérations d’espace de noms de Service Bus. Au lieu de l’API .NET SDK, vous pouvez utiliser [Get-AzureSBNamespace][] et [New-AzureSBNamespace][].

Cet exemple crée quelques variables locales dans le script ; `$Namespace` and `$Location`.

- `$Namespace`est le nom de l’espace de noms du Bus de Service qui nous intéresse.
- `$Location`identifie le centre de données dans laquelle le script configure l’espace de noms.
- `$CurrentNamespace`enregistre l’espace de noms de référence qui le script récupère (ou crée).

Dans un script réel, `$Namespace` et `$Location` peuvent être passés comme paramètres.

Cette partie du script effectue les tâches suivantes :

1. Tente de récupérer un espace de noms du Bus de Service avec le nom fourni.
2. Si l’espace de noms est trouvé, il indique ce qui a été trouvé.
3. Si l’espace de noms n’est pas trouvé, il crée l’espace de noms, puis récupère l’espace de noms qui vient d’être créé.

    ```
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
    
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
    
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Pour configurer les autres entités de Bus de Service, créez une instance de la classe [NamespaceManager][] dans le Kit de développement.
Vous pouvez utiliser l’applet de commande [Get-AzureSBAuthorizationRule][] pour récupérer d’une règle d’autorisation qui est utilisée pour fournir une chaîne de connexion. Nous allons stocker une référence à le `NamespaceManager` d’instance dans le `$NamespaceManager` variable. Nous allons utiliser `$NamespaceManager` plus loin dans le script à configurer d’autres entités.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Mise en service des autres entités de Bus des services

Afin de prévoir d’autres entités, telles que les files d’attente, des rubriques et des concentrateurs d’événement, utilisez l' [API .NET pour le Bus de Service][]. Cet article se concentre uniquement sur les concentrateurs d’événement, mais les étapes pour d’autres entités sont similaires. En outre, des exemples plus détaillés, y compris d’autres entités, sont référencés à la fin de cet article.

Cette partie du script crée quatre variables locales plus. Ces variables sont utilisées pour instancier un `EventHubDescription` objet. Le script effectue les tâches suivantes :

1. À l’aide de la `NamespaceManager` objet, vérifiez si le concentrateur d’événements identifié par `$Path` existe.
2. Si elle n’existe pas, créez un `EventHubDescription` et le transmettre à la `NamespaceManager` classe `CreateEventHubIfNotExists` méthode.
3. Après avoir déterminé que le concentrateur de l’événement est disponible, créer un groupe de consommateur à l’aide de `ConsumerGroupDescription` et `NamespaceManager`.

    ```
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
        
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
        Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
        
    # Create the consumer group if it doesn't exist
    Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
    Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Migrer un espace de noms à un autre abonnement Azure

La séquence de commandes suivante déplace un espace de noms à partir d’un abonnement Azure à un autre. Pour exécuter cette opération, l’espace de noms doit être déjà active, et l’utilisateur qui exécute les commandes PowerShell doit être un administrateur sur les abonnements à la source et la cible.

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Étapes suivantes

Cet article fourni une structure de base pour l’approvisionnement des entités de Bus des services à l’aide de PowerShell. Tout ce que vous pouvez faire en utilisant les bibliothèques clientes .NET, vous pouvez également faire dans un script PowerShell.

Il existe des exemples plus détaillés sur ces billets de blog :

- [Comment faire pour créer des files d’attente, des rubriques et des abonnements à l’aide d’un script PowerShell Bus des services](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Comment créer un Namespace de Bus de Service et un concentrateur d’événements à l’aide d’un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Certains scripts prêts à l’emploi sont également disponibles pour téléchargement :
- [Scripts PowerShell de Bus de service](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Options d’achat]: http://azure.microsoft.com/pricing/purchase-options/
[Offres de membre]: http://azure.microsoft.com/pricing/member-offers/
[Version d’évaluation gratuite]: http://azure.microsoft.com/pricing/free-trial/
[Installer et configurer Azure PowerShell]: ../powershell-install-configure.md
[Package NuGet de Bus de service]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Nouvelle-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET pour le Bus de Service]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
