<properties 
    pageTitle="Déployer et gérer des concentrateurs de Notification à l’aide de PowerShell" 
    description="Comment faire pour créer et gérer des concentrateurs de Notification à l’aide de PowerShell pour l’Automation" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Déployer et gérer des concentrateurs de Notification à l’aide de PowerShell

##<a name="overview"></a>Vue d’ensemble

Cet article vous montre comment utiliser de créer et gérer les concentrateurs Notification Azure à l’aide de PowerShell. Les tâches d’automation courantes suivantes figurent dans cette rubrique.

+ Créer un concentrateur de Notification
+ Définir les informations d’identification

Si vous devez également créer un espace de noms du nouveau service bus pour vos concentrateurs de notification, reportez-vous à la section [Gestion des Bus de Service avec PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Gestion des Notifications de concentrateurs ne sont pas pris en charge directement par les applets de commande incluses avec PowerShell d’Azure. La meilleure approche à partir de PowerShell doit faire référence à l’assembly Microsoft.Azure.NotificationHubs.dll. L’assembly est distribué avec le [package Microsoft Azure Notification concentrateurs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).


## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme basée sur abonnement. Pour plus d’informations sur l’obtention d’un abonnement, consultez les [Options d’achat], [L’offre de membre]ou [Version d’évaluation gratuite].

- Un ordinateur avec PowerShell d’Azure. Pour obtenir des instructions, reportez-vous à la section [installer et configurer Azure PowerShell].

- Une connaissance générale des scripts PowerShell, packages NuGet et le.NET Framework.


## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Inclusion d’une référence à l’assembly .NET pour le Bus de Service

Gestion des concentrateurs de Notification Azure ne sont pas encore inclus dans les applets de commande PowerShell dans Azure PowerShell. Pour provisionner des concentrateurs de notification, vous pouvez utiliser le client .NET fourni dans le [package Microsoft Azure Notification concentrateurs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Vérifiez d’abord que votre script peut localiser l’assembly **Microsoft.Azure.NotificationHubs.dll** , qui est installé dans un package NuGet dans un projet Visual Studio. Pour être flexible, le script procède comme suit :

1. Détermine le chemin d’accès auquel elle a été appelée.
2. Parcourt le chemin d’accès jusqu'à ce qu’il trouve un dossier nommé `packages`. Ce dossier est créé lorsque vous installez les packages NuGet pour les projets Visual Studio.
3. Recherche de manière récursive la `packages` dossier pour un assembly nommé **Microsoft.Azure.NotificationHubs.dll**.
4. Référence de l’assembly afin que les types sont disponibles pour une utilisation ultérieure.

Voici comment ces étapes sont implémentés dans un script PowerShell :

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Créez la classe NamespaceManager

Pour provisionner des concentrateurs de Notification, créez une instance de la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) dans le Kit de développement. 

Vous pouvez utiliser l’applet de commande [Get-AzureSBAuthorizationRule] inclus dans Azure PowerShell pour récupérer d’une règle d’autorisation qui est utilisée pour fournir une chaîne de connexion. Nous allons stocker une référence à le `NamespaceManager` d’instance dans le `$NamespaceManager` variable. Nous allons utiliser `$NamespaceManager` pour mettre en service un concentrateur de la notification.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Mise en service d’un nouveau concentrateur de Notification 

Pour provisionner un nouveau concentrateur de notification, utilisez l' [API .NET pour les concentrateurs de la Notification].

Dans cette partie du script vous définir quatre variables locales. 

1. `$Namespace`: Définir le nom de l’espace de noms dans lequel vous souhaitez créer un concentrateur de la notification.
2. `$Path`: Permet de ce chemin d’accès au nom du nouveau concentrateur de notification.  Par exemple, « MyHub ».    
3. `$WnsPackageSid`: Définir le package SID vous application Windows à partir du [Centre de développement Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: Définir la clé secrète pour vous Windows App à partir du [Centre de développement Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Ces variables sont utilisées pour se connecter à votre espace de noms et de créer un nouveau concentrateur de Notification configuré pour gérer les notifications de Notification Services de Windows (WNS) avec les informations d’identification WNS pour une application Windows. Pour plus d’informations sur l’obtention du package Voir SID et clé secrète, le didacticiel [Mise en route avec les concentrateurs de la Notification](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) . 

+ L’extrait de code de script utilise le `NamespaceManager` objet pour vérifier si le concentrateur de Notification identifié par `$Path` existe.

+ Si elle n’existe pas, le script crée un `NotificationHubDescription` avec WNS les informations d’identification et les passer à la `NamespaceManager` classe `CreateNotificationHub` méthode.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>Ressources supplémentaires

- [Gérer le Bus de Service avec PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Comment faire pour créer des files d’attente, des rubriques et des abonnements à l’aide d’un script PowerShell Bus des services](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Comment créer un Namespace de Bus de Service et un concentrateur d’événements à l’aide d’un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Certains scripts prêts à l’emploi sont également disponibles pour téléchargement :
- [Scripts PowerShell de Bus de service](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Options d’achat]: http://azure.microsoft.com/pricing/purchase-options/
[Offres de membre]: http://azure.microsoft.com/pricing/member-offers/
[Version d’évaluation gratuite]: http://azure.microsoft.com/pricing/free-trial/
[Installer et configurer Azure PowerShell]: ../powershell-install-configure.md
[API .NET pour moyeux de Notification]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 
