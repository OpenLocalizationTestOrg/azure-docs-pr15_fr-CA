<properties
    pageTitle="Utilisation de PowerShell pour gérer les ressources de Service Bus et concentrateurs d’événement | Microsoft Azure"
    description="Utilisation de PowerShell pour créer et gérer des ressources de Service Bus et concentrateurs d’événement"
    services="service-bus,event-hubs"
    documentationCenter=".NET"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="use-powershell-to-manage-service-bus-and-event-hubs-resources"></a>Utilisation de PowerShell pour gérer les ressources de Service Bus et concentrateurs d’événement

Microsoft Azure PowerShell est un environnement de script qui vous permet de contrôler et d’automatiser le déploiement et la gestion des services Azure. Cet article décrit comment utiliser PowerShell pour provisionner et gérer les entités de Bus des services, tels que les espaces de noms, les files d’attente et les concentrateurs d’événement à l’aide d’une console d’Azure PowerShell locale.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, vous aurez besoin des éléments suivants :

- Un abonnement Azure. Azure est une plateforme basée sur abonnement. Pour plus d’informations sur l’obtention d’un abonnement, voir [options d’achat][], [offre des membres][]ou [compte gratuit][].

- Un ordinateur avec PowerShell d’Azure. Pour obtenir des instructions, reportez-vous à la section [installer et configurer Azure PowerShell][].

- Une connaissance générale des scripts PowerShell, packages NuGet et le.NET Framework.

## <a name="include-a-reference-to-the-net-assembly-for-service-bus"></a>Inclure une référence à l’assembly .NET pour le Bus de Service

Il existe un nombre limité d’applets de commande PowerShell pour la gestion des Bus de Service. Pour provisionner des entités qui ne sont pas exposées via les applets de commande existant, vous pouvez utiliser le client .NET pour le Bus de Service à partir de PowerShell en référençant le [package NuGet de Bus de Service].

Tout d’abord, assurez-vous que le script peut localiser l’assembly **Microsoft.ServiceBus.dll** , qui est installé avec le package NuGet. Pour être flexible, le script procède comme suit :

1. Détermine le chemin d’accès à partir duquel il a été appelé.
2. Parcourt le chemin d’accès jusqu'à ce qu’il trouve un dossier nommé `packages`. Ce dossier est créé lorsque vous installez les packages NuGet.
3. Recherche de manière récursive la `packages` dossier pour un assembly nommé **Microsoft.ServiceBus.dll**.
4. Référence de l’assembly afin que les types sont disponibles pour une utilisation ultérieure.

Voici comment ces étapes sont implémentés dans un script PowerShell :

```powershell

try
{
    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}

```

## <a name="provision-a-service-bus-namespace"></a>Fourniture d’un espace de noms du Bus des services

Lorsque vous travaillez avec des espaces de noms de Bus des services, il existe deux applets de commande que vous pouvez utiliser au lieu du Kit de développement .NET : [Get-AzureSBNamespace][] et [New-AzureSBNamespace][].

Cet exemple crée quelques variables locales dans le script ; `$Namespace` and `$Location`.

- `$Namespace`est le nom de l’espace de noms Bus de Service avec laquelle nous voulons travailler.
- `$Location`identifie le centre de données dans lequel nous configurera l’espace de noms.
- `$CurrentNamespace`enregistre l’espace de noms de référence qui nous récupérer (ou créer).

Dans un script réel, `$Namespace` et `$Location` peuvent être passés comme paramètres.

Cette partie du script effectue les opérations suivantes :

1. Tente de récupérer un espace de noms du Bus de Service avec le nom fourni.
2. Si l’espace de noms est trouvé, il indique ce qui a été trouvé.
3. Si l’espace de noms n’est pas trouvé, il crée l’espace de noms, puis récupère l’espace de noms qui vient d’être créé.

    ``` powershell

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
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```
Pour configurer les autres entités de Bus de Service, créez une instance de le `NamespaceManager` objet à partir du Kit de développement. Vous pouvez utiliser l’applet de commande [Get-AzureSBAuthorizationRule][] pour récupérer d’une règle d’autorisation qui est utilisée pour fournir une chaîne de connexion. Cet exemple montre comment stocke une référence à le `NamespaceManager` d’instance dans le `$NamespaceManager` variable. Le script utilise ultérieurement `$NamespaceManager` pour configurer d’autres entités.

    ``` powershell
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    # Create the NamespaceManager object to create the Event Hub
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
    ```

## <a name="provisioning-other-service-bus-entities"></a>Mise en service des autres entités de Bus des services

Pour configurer d’autres entités, telles que les files d’attente, des rubriques et des concentrateurs de l’événement, vous pouvez utiliser l' [API .NET pour le Bus de Service][]. Des exemples plus détaillés, y compris d’autres entités, sont référencés à la fin de cet article.

### <a name="create-an-event-hub"></a>Créer un concentrateur d’événements

Cette partie du script crée quatre variables locales plus. Ces variables sont utilisées pour instancier un `EventHubDescription` objet. Le script effectue les opérations suivantes :

1. À l’aide de la `NamespaceManager` objet, vérifiez si le concentrateur d’événements identifié par `$Path` existe.
2. Si elle n’existe pas, créez un `EventHubDescription` et le transmettre à la `NamespaceManager` classe `CreateEventHubIfNotExists` méthode.
3. Après avoir déterminé que le concentrateur de l’événement est disponible, créer un groupe de consommateur à l’aide de `ConsumerGroupDescription` et `NamespaceManager`.

    ``` powershell

    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"

    # Check if the Event Hub already exists
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

### <a name="create-a-queue"></a>Créer une file d’attente

Pour créer une file d’attente ou une rubrique, effectuer une vérification de l’espace de noms dans la section précédente.

    if ($NamespaceManager.QueueExists($Path))
    {
        Write-Output "The [$Path] queue already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
        $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
        $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
        $QueueDescription.EnableExpress = $EnableExpress
        $QueueDescription.EnablePartitioning = $EnablePartitioning
        $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
        $QueueDescription.ForwardTo = $ForwardTo
        $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        if ($LockDuration -gt 0)
        {
            $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
        }
        $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
        $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        $QueueDescription.RequiresSession = $RequiresSession
        if ($EnablePartitioning)
        {
            $QueueDescription.SupportOrdering = $False
        }
        else
        {
            $QueueDescription.SupportOrdering = $SupportOrdering
        }
        $QueueDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateQueue($QueueDescription);
    }

### <a name="create-a-topic"></a>Créer une rubrique

    if ($NamespaceManager.TopicExists($Path))
    {
        Write-Output "The [$Path] topic already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
        $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
        $TopicDescription.EnableExpress = $EnableExpress
        $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
        $TopicDescription.EnablePartitioning = $EnablePartitioning
        $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        if ($EnablePartitioning)
        {
            $TopicDescription.SupportOrdering = $False
        }
        else
        {
            $TopicDescription.SupportOrdering = $SupportOrdering
        }
        $TopicDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateTopic($TopicDescription);
    }

## <a name="next-steps"></a>Étapes suivantes

Cet article vous fourni avec un flux de travail pour l’approvisionnement des entités de Bus des services à l’aide de PowerShell. Bien qu’il existe un nombre limité d’applets de commande PowerShell de gestion des Bus de Service messagerie des entités, en référençant l’assembly Microsoft.ServiceBus.dll, pratiquement toutes les opérations vous pouvez effectuer avec les bibliothèques clientes .NET que vous pouvez également effectuer dans un script PowerShell.

Il existe des exemples plus détaillés dans ces billets de blog :

- [Comment faire pour créer des files d’attente, des rubriques et des abonnements à l’aide d’un script PowerShell Bus des services](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Comment créer un Namespace de Bus de Service et un concentrateur d’événements à l’aide d’un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Certains scripts prêts à l’emploi sont également disponibles pour téléchargement :

- [Scripts PowerShell de Bus de service](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[options d’achat]: http://azure.microsoft.com/pricing/purchase-options/
[offres de membre]: http://azure.microsoft.com/pricing/member-offers/
[compte gratuit]: http://azure.microsoft.com/pricing/free-trial/
[Package NuGet de Bus de service]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Nouvelle-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET pour le Bus de Service]: https://msdn.microsoft.com/en-us/library/azure/mt419900.aspx
[Installer et configurer Azure PowerShell]: ../powershell-install-configure.md
