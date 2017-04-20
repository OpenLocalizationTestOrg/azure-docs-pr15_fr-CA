<properties
    pageTitle="Résoudre les problèmes liés à la suppression des comptes de stockage Azure, les conteneurs ou les disques durs virtuels dans un déploiement classique | Microsoft Azure"
    description="Résoudre les problèmes liés à la suppression des comptes de stockage Azure, les conteneurs ou les disques durs virtuels dans un déploiement classique"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="tysonn"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="genli"/>

# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Résoudre les problèmes liés à la suppression des comptes de stockage Azure, les conteneurs ou les disques durs virtuels dans un déploiement classique

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Vous pouvez recevoir des erreurs lorsque vous essayez de supprimer le compte de stockage Azure, le conteneur ou le disque dur virtuel dans [Azure portal](https://portal.azure.com/) ou l' [Azure portal classique](https://manage.windowsazure.com/). Les problèmes peuvent être causés par les circonstances suivantes :

-   Lorsque vous supprimez une machine virtuelle, le disque et le disque dur virtuel ne sont pas supprimés automatiquement. C’est peut-être la raison de l’échec de suppression de compte de stockage. Nous ne supprimez pas le disque afin que vous pouvez utiliser le disque pour monter une autre MV.

-   Il existe toujours un bail sur un disque ou le blob associé le disque.

Si votre problème Azure n’est pas abordée dans cet article, consultez les forums Azure sur [MSDN et le débordement de la pile](https://azure.microsoft.com/support/forums/). Vous pouvez valider votre problème sur ces forums ou @AzureSupport sur Twitter. Également, vous pouvez archiver une demande de prise en charge Azure en sélectionnant **obtenir de l’aide** sur le site de [support d’Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Symptômes

La section suivante répertorie les erreurs courantes que vous pouvez recevoir lorsque vous essayez de supprimer les comptes de stockage Azure, les conteneurs ou les disques durs virtuels.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Scénario 1 : Impossible de supprimer un compte de stockage

Lorsque vous naviguez vers le compte de stockage [Azure portal](https://portal.azure.com/) ou [Azure portal classique](https://manage.windowsazure.com/) et sélectionnez **Supprimer**, vous pouvez voir le message d’erreur suivant :

*Compte de stockage StorageAccountName contient des Images de machine virtuelle. Assurez-vous que ces Images de machine virtuelle sont supprimés avant de supprimer ce compte de stockage.*

Cette erreur peut également s’afficher :

**Portail sur l’Azure**:

*Impossible de supprimer le compte de stockage < vm-stockage-nom de compte >. Impossible de supprimer le compte de stockage < vm-stockage-nom de compte > : « compte de stockage < vm-stockage-nom de compte > a certaines images actives et/ou l’ou les disques. Assurez-vous que ces images et/ou les disques sont supprimés avant de supprimer ce compte de stockage. ».*

**Portail de classique sur le Azure**:

*Compte de stockage < vm-stockage-nom de compte > a certaines images actives et/ou l’ou les disques, par exemple, xxxxxxxxx-xxxxxxxxx-O-209490240936090599. S’assurer ces images disques sont supprimés avant de supprimer ce compte de stockage.*

Ou

**Portail sur l’Azure**:

Compte de stockage *< vm-stockage-nom de compte > a 1 conteneur (s) ayant une image active et/ou des artefacts de disque. Assurez-vous que ces artefacts sont supprimés du référentiel image avant de supprimer ce compte de stockage*.

**Portail de classique sur le Azure**:

Compte de soumettre le stockage n’a pas pu *< vm-stockage-nom de compte > a 1 conteneur (s) ayant une image active et/ou des artefacts de disque. Assurez-vous que ces artefacts sont supprimés du référentiel image avant de supprimer ce compte de stockage. Lorsque vous essayez de supprimer un compte de stockage et des disques toujours actif associé, vous verrez un message vous indiquant que des disques actifs qui doivent être supprimées sont*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Scénario 2 : Impossible de supprimer un conteneur

Lorsque vous essayez de supprimer le conteneur de stockage, vous pouvez voir le message d’erreur suivant :

Impossible de supprimer le conteneur de stockage * <container name>. Erreur : ' il est actuellement un contrat de location sur le conteneur et aucun ID de location a été spécifié dans la demande de*.

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Scénario 3 : Impossible de supprimer un disque dur virtuel

Une fois que vous supprimez un ordinateur virtuel, puis réessayez de supprimer les objets BLOB pour les disques durs virtuels associés, vous pouvez recevoir le message suivant :

*Impossible de supprimer le blob ' chemin d’accès/XXXXXX-XXXXXX-os-1447379084699.vhd'. Erreur : « il existe actuellement un bail sur le blob et aucun ID de location a été spécifié dans la demande.*

## <a name="solution"></a>Solution
Pour résoudre les problèmes les plus courants, essayez la méthode suivante :

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Étape 1 : Supprimer tous les disques du système d’exploitation qui empêchent la suppression du compte de stockage, du conteneur ou du disque dur virtuel

1. Basculez vers [Azure portal classique](https://manage.windowsazure.com/).
2. Sélectionnez **l’ordinateur virtuel** > **disques**.

    ![Image de disques de machines virtuelles sur Azure portal classique.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Recherchez les disques qui sont associés avec le compte de stockage, un conteneur ou un disque dur virtuel que vous souhaitez supprimer. Lorsque vous vérifiez l’emplacement du disque, vous trouverez le compte de stockage associé, un conteneur ou un disque dur virtuel.

    ![Image qui affiche des informations sur l’emplacement des disques sur Azure portal classique](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Confirmez qu’aucun ordinateur virtuel n’est répertorié sur le champ **associé à** des disques et supprimez les disques.

    > [AZURE.NOTE] Si un disque est connecté à un ordinateur virtuel, vous ne serez pas en mesure de le supprimer. Les disques sont détachés à partir d’un ordinateur virtuel supprimé en mode asynchrone. Il peut prendre quelques minutes après la suppression de ce champ Supprimer la machine virtuelle.

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Étape 2 : Supprimer les Images de machine virtuelle qui empêchent la suppression du compte de stockage ou du conteneur

1. Basculez vers [Azure portal classique](https://manage.windowsazure.com/).
2. Sélectionnez **l’ordinateur virtuel** > **d’IMAGES**, puis supprimez les images qui sont associés au compte de stockage, conteneur ou VHD.

    Ensuite, essayez à nouveau de supprimer le compte de stockage, un conteneur ou un disque dur virtuel.

> [AZURE.WARNING] Veillez à sauvegarder tout ce que vous voulez enregistrer avant de supprimer le compte. Il n’est pas possible de restaurer un compte de stockage supprimé ou de récupérer le contenu qu’elle contenait avant la suppression. Cela est également vrai pour toutes les ressources dans le compte : lorsque vous supprimez un disque dur virtuel, blob, table, file d’attente ou un fichier, il est définitivement supprimé. Assurez-vous que la ressource n’est pas en cours d’utilisation.

## <a name="about-the-stopped-deallocated-status"></a>À propos de l’état arrêté (libérée)

Ordinateurs virtuels qui ont été créées dans le modèle de déploiement classique et qui ont été conservés aura l’état **Stopped (libéré)** sur le [portail Azure](https://portal.azure.com/) ou [Azure portal classique](https://manage.windowsazure.com/).

**Azure portal classique**:

![Arrêté (Deallocated) état pour les machines virtuelles sur Azure portal.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Azure portal**:

![Statut (libéré) arrêté pour les machines virtuelles sur Azure portal classique.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Un état « Arrêté (libéré) » libère les ressources de l’ordinateur, telles que le processeur, la mémoire et le réseau. Les disques, cependant, sont toujours conservés afin que vous pouvez rapidement créer de nouveau la machine virtuelle si nécessaire. Ces disques sont créées sur des disques durs virtuels, qui sont sauvegardées par le stockage Azure. Le compte de stockage dispose de ces disques durs virtuels et les disques possèdent des baux sur les disques durs virtuels.

## <a name="next-steps"></a>Étapes suivantes

- [Supprimer un compte de stockage](storage-create-storage-account.md#delete-a-storage-account)
- [Comment diviser le bail verrouillé du stockage blob dans Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
