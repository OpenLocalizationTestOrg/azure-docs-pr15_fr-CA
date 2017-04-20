<properties
   pageTitle="Utiliser les applets de commande PowerShell avec Azure RemoteApp | Microsoft Azure"
   description="Apprenez à utiliser des applets de commande Windows PowerShell dans Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>



# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Utiliser les applets de commande Windows PowerShell avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

 Vous pouvez utiliser les applets de commande PowerShell de RemoteApp Azure d’administrer et de gérer vos collections. Utilisez les informations suivantes pour commencer.

## <a name="get-the-cmdlets"></a>Obtenir les applets de commande 
-------------
Tout d’abord télécharger les applets de commande Powershell de Azure [ici](http://go.microsoft.com/?linkid=9811175), l’applets de commande sont inclus de RemoteApp. 

Consultez l' [aide de l’applet de commande Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx).

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>Configurer des applets de commande Azure pour utiliser votre abonnement
------------------
Suivez [ce guide](../powershell-install-configure.md) afin de pouvoir utiliser les applets de commande par rapport à votre abonnement Azure.

Vous pouvez utiliser ces étapes pour démarrer rapidement :

1.  Téléchargez et installez les [applets de commande PowerShell d’Azure](http://go.microsoft.com/?linkid=9811175).
2.  Lancez Microsoft Azure PowerShell.
3.  Exécutez **Add-AzureAccount** pour s’authentifier sur votre abonnement Azure. Lorsque vous y êtes invité, entrez le même nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter au portail Azure.  
4.  Exécutez **Get-AzureSubscription** pour les abonnements associés à votre compte d’utilisateur de la liste. 
5.  Exécutez **Select-AzureSubscription** et spécifiez le nom de l’abonnement ou l’ID à utiliser dans la console PowerShell.

Félicitations, votre console Azure PowerShell est configuré et prêt à l’emploi. Gardez à l’esprit que vous devrez Répétez étapes 2 à 5 chaque fois que vous démarrez la la console PowerShell d’Azure.  

## <a name="create-a-cloud-collection"></a>Créer une collection de nuage
--------------------
Il est simple, exécutez la commande suivante :

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

La commande ci-dessus publie automatiquement les applications Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio et Word).

Création de la collection peut prendre 30 minutes ou plus longtemps. Par conséquent, cette commande retourne un code de suivi que vous pouvez utiliser comme suit :


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Une fois la collection terminée, vous pouvez ajouter des utilisateurs à la collection avec la commande suivante :

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

Et vous avez terminé ! Cet utilisateur doit être en mesure de se connecter à l’application en utilisant le client Azure RemoteApp trouvé [ici](https://www.remoteapp.windowsazure.com/).

## <a name="available-cmdlets"></a>Les applets de commande disponibles
Il y a beaucoup d’autres commandes que nous, la documentation pour les viendront bientôt :

Applets de commande RemoteApp Collection de base : 

- Nouvelle-AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Ensemble-AzureRemoteAppCollection
- Mise à jour-AzureRemoteAppCollection
- Supprimer-AzureRemoteAppCollection
- Ajouter-AzureRemoteAppUser
- Get-AzureRemoteAppUser
- Supprimer-AzureRemoteAppUser
- Get-AzureRemoteAppSession
- Déconnecter-AzureRemoteAppSession
- Appel de code non-AzureRemoteAppSessionLogoff
- Envoyer-AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- Publication-AzureRemoteAppProgram
- Annuler la publication-AzureRemoteAppProgram
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

Applets de commande RemoteApp réseau virtuel :

- Nouvelle-AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Ensemble-AzureRemoteAppVNet
- Supprimer-AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get - AzureRemoteAppVpnDeviceConfigScript
- Réinitialisation-AzureRemoteAppVpnSharedKey

RemoteApp modèle image applets de commande :

- Nouvelle-AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Changement de nom-AzureRemoteAppTemplateImage
- Supprimer-AzureRemoteAppTemplateImage

RemoteApp d’autres applets de commande :

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Ensemble-AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 
