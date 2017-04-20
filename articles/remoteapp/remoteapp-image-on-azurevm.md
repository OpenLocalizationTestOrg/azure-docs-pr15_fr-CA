<properties
    pageTitle="Créer une image d’Azure RemoteApp basée sur une machine virtuelle d’Azure | Microsoft Azure"
    description="Apprenez à créer une image pour Azure RemoteApp à partir d’un ordinateur virtuel Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Créer une image d’Azure RemoteApp basée sur une machine virtuelle Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Vous pouvez créer des images d’Azure RemoteApp (qui contiennent les applications que vous partagez dans votre collection) à partir d’un ordinateur virtuel Azure. Vous pouvez également choisir d’utiliser une image de machine virtuelle que nous avons ajouté à la galerie d’image Azure VM qui répond à toutes les exigences d’image Azure RemoteApp - vous pouvez utiliser cette image de machine virtuelle comme point de départ pour votre propre machine virtuelle, si vous le souhaitez. Recherchez simplement l’image « Windows Server à distance hôte de Session bureau » dans la bibliothèque.

Il y a deux étapes pour créer votre propre image basée sur une machine virtuelle d’Azure - créer l’image, puis de le télécharger à partir de la bibliothèque d’Azure VM pour Azure RemoteApp.

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Créer une image personnalisée basée sur une machine virtuelle d’Azure

Suivez ces étapes pour créer une image basée sur une machine virtuelle d’Azure.

1. Créer un ordinateur virtuel Azure. Vous pouvez utiliser le « Windows Server à distance hôte de Session bureau » ou de l’image « Windows Server à distance Bureau Session hôte avec Microsoft Office 365 ProPlus » à partir de la galerie d’image Azure VM. Cette image répond à toutes les exigences d’image modèle Azure RemoteApp.

    Pour plus d’informations, consultez [créer un ordinateur virtuel exécutant Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

2. Se connecter à la machine virtuelle et installer et configurer les applications que vous souhaitez partager par le biais de RemoteApp. Veillez à effectuer toutes les configurations Windows supplémentaires requises par vos applications.

    Pour plus d’informations, consultez [comment ouvrir une session sur un ordinateur virtuel exécutant Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md).

3. Si vous utilisez l’une des images de l’hôte de Session Bureau à distance Windows Server, il existe un script de validation inclus afin de garantir votre machine virtuelle répond à la RemoteApp pre-reqs. Pour exécuter un script, double-cliquez sur **ValidateRemoteAppImage** sur le bureau. Assurez-vous que toutes les erreurs signalées par le script sont résolus avant de passer à l’étape suivante.

4. SYSPREP generalize et capturer l’image. Pour obtenir des instructions, consultez [la Capture d’une Machine virtuelle de Windows à utiliser comme modèle](../virtual-machines/virtual-machines-windows-classic-capture-image.md) .



## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Importer l’image dans la bibliothèque d’images Azure RemoteApp

Pour importer la nouvelle image dans Azure RemoteApp, suivez cette procédure :

1. Dans l’onglet **Images de modèle** :
    - Si vous avez des images existantes, cliquez sur **télécharger ou d’importer une Image du modèle**.
    - Si vous disposez déjà d’au moins une image, cliquez sur **+** pour ajouter une nouvelle image.

2. Sélectionnez la bibliothèque **d’importation d’une image à partir de vos ordinateurs virtuels** , puis cliquez sur **suivant**.

3. Sur la page suivante, sélectionnez votre image personnalisée à partir de la liste et confirmez que vous avez suivi les étapes indiquées lors de la création de votre image. Cliquez sur **suivant**.
4. Entrez un nom pour la nouvelle image de RemoteApp et l’emplacement de prélèvement, puis cliquez sur la case à cocher pour démarrer le processus d’importation.

> [AZURE.NOTE] Vous pouvez importer des images à partir de n’importe quel emplacement Azure pris en charge par Azure Virtual Machines à n’importe quel emplacement Azure pris en charge par Azure RemoteApp. En fonction des emplacements, l’importation peut prendre jusqu'à 25 minutes.

Vous êtes maintenant prêt à créer le votre nouvelle collection, soit une collection de [nuage](remoteapp-create-cloud-deployment.md) ou un [hybride](remoteapp-create-hybrid-deployment.md), selon vos besoins.
