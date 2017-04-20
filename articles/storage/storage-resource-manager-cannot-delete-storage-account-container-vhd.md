<properties
    pageTitle="Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, les conteneurs ou les disques durs virtuels dans un gestionnaire de ressources de déploiement | Microsoft Azure"
    description="Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, les conteneurs ou les disques durs virtuels dans un gestionnaire de ressources de déploiement"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="genli"/>

# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, les conteneurs ou les disques durs virtuels dans un gestionnaire de ressources de déploiement

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Vous pouvez recevoir des erreurs lorsque vous essayez de supprimer un compte de stockage Azure, le conteneur ou le disque dur virtuel (VHD) dans [Azure portal](https://portal.azure.com). Cet article fournit des conseils de dépannage pour vous aider à résoudre le problème dans un déploiement d’Azure le Gestionnaire de ressources.

Si cet article ne répond pas à votre problème d’Azure, visitez les forums Azure sur [MSDN et le débordement de pile](https://azure.microsoft.com/support/forums/). Vous pouvez valider votre problème sur ces forums ou @AzureSupport sur Twitter. Également, vous pouvez archiver une demande de prise en charge Azure en sélectionnant **obtenir de l’aide** sur le site de [support d’Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Symptômes

### <a name="scenario-1"></a>Scénario 1

Lorsque vous essayez de supprimer un disque dur virtuel dans un compte de stockage dans un gestionnaire de ressources de déploiement, le message d’erreur suivant s’affiche :

**Impossible de supprimer le blob 'vhds/BlobName.vhd'. Erreur : Il est actuellement un contrat de location sur le blob et aucun ID de location a été spécifié dans la demande.**

Ce problème peut se produire car une machine virtuelle (VM) a un bail sur le disque dur virtuel que vous voulez supprimer.

### <a name="scenario-2"></a>Scénario 2

Lorsque vous essayez de supprimer un conteneur dans un compte de stockage dans un gestionnaire de ressources de déploiement, le message d’erreur suivant s’affiche :

**Impossible de supprimer le conteneur de stockage « virtuels ». Erreur : Il existe actuellement un bail sur le conteneur et aucun ID de location a été spécifié dans la demande.**

Ce problème peut se produire car le conteneur possède un disque dur virtuel qui est verrouillé dans l’état du bail.

### <a name="scenario-3"></a>Scénario 3

Lorsque vous essayez de supprimer un compte de stockage dans un gestionnaire de ressources de déploiement, le message d’erreur suivant s’affiche :

**Impossible de supprimer le compte de stockage 'StorageAccountName'. Erreur : Le compte de stockage ne peut pas être supprimé en raison de ses artefacts en cours d’utilisation.**

Ce problème peut se produire parce que le compte de stockage contient un disque dur virtuel qui se trouve dans l’état du bail.

## <a name="solution"></a>Solution

Pour résoudre ces problèmes, vous devez identifier le disque dur virtuel qui est à l’origine de l’erreur et la machine virtuelle associée. Ensuite, détacher le disque dur virtuel à partir de la machine virtuelle (pour les disques de données) ou supprimez l’ordinateur virtuel qui utilise le disque dur virtuel (pour les disques du système d’exploitation). Cela supprime le bail à partir du disque dur virtuel et lui permet d’être supprimés.

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>Étape 1 : Identifier le problème disque dur virtuel et l’ordinateur virtuel associé


1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de **concentrateur** , sélectionnez **toutes les ressources**. Accédez au compte de stockage que vous souhaitez supprimer et sélectionnez **BLOB** > **les disques durs virtuels**.

    ![Capture d’écran du portail, avec le compte de stockage et le conteneur « disques durs virtuels » mis en surbrillance](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

3. Vérifiez les propriétés de chaque disque dur virtuel dans le conteneur. Localisez le disque dur virtuel qui est à l’état **loué** . Ensuite, déterminer qui utilise la mémoire virtuelle du disque dur virtuel. En règle générale, vous pouvez déterminer l’ordinateur virtuel contenant le disque dur virtuel en vérifiant le nom du disque dur virtuel :

    - Les disques du système d’exploitation généralement respectent cette convention d’affectation de noms : VMNameYYYYMMDDHHMMSS.vhd
    - Disques de données généralement respectent cette convention : VMName-AAAAMMJJ-HHMMSS.vhd

    ![Capture d’écran des informations sur le portail, conteneur avec le nom de la machine virtuelle, l’état de « Verrouillé » et l’état de bail de « Loué » mis en surbrillance](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>Étape 2 : Supprimer le bail à partir du disque dur virtuel

Pour supprimer l’ordinateur virtuel qui utilise le disque dur virtuel (pour les disques du système d’exploitation) :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Dans le menu de **concentrateur** , sélectionnez **Machines virtuelles**.
3.  Sélectionnez la machine virtuelle qui détient un bail sur le disque dur virtuel.
4.  Assurez-vous que rien n’utilise l’ordinateur virtuel, et que vous n’avez plus la machine virtuelle.
5.  En haut de la lame de **Détails de la machine virtuelle** , sélectionnez **Supprimer**, puis cliquez sur **Oui** pour confirmer.
6.  La machine virtuelle doit être supprimée, mais le disque dur virtuel doit être conservé. Toutefois, le disque dur virtuel ne devant plus avoir un bail sur celui-ci. Il peut prendre quelques minutes pour le bail doit être publié. Pour vérifier que le bail est libéré, accédez à **toutes les ressources** > **Nom de compte de stockage** > **BLOB** > **les disques durs virtuels**. Dans le volet des **Propriétés de l’objet Blob** , la valeur de **l’État** doit être **déverrouillé**.

Pour détacher le disque dur virtuel à partir de la machine virtuelle utilise (pour les disques de données) :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Dans le menu de **concentrateur** , sélectionnez **Machines virtuelles**.
3.  Sélectionnez la machine virtuelle qui détient un bail sur le disque dur virtuel.
4.  Sélectionnez les **disques** sur la lame de **Détails de la machine virtuelle** .
5.  Sélectionnez le disque de données qui détient un bail sur le disque dur virtuel. Vous pouvez déterminer quel disque dur virtuel est attaché en le disque en vérifiant l’URL de ce dernier.
6.  Déterminer avec certitude que rien n’est activement à l’aide du disque de données.
7.  Cliquez sur **Détacher** de la blade de **détails d’un disque** .
8.  Le disque doit maintenant être détaché de la machine virtuelle, et le disque dur virtuel ne devant plus avoir un bail sur celui-ci. Il peut prendre quelques minutes pour le bail doit être publié. Pour vérifier que le bail a été libéré, accédez à **toutes les ressources** > **Nom de compte de stockage** > **BLOB** > **les disques durs virtuels**. Dans le volet des **Propriétés de l’objet Blob** , la valeur de **l’État** doit être **déverrouillé**.

## <a name="what-is-a-lease"></a>En quoi consiste un bail ?

Un contrat de location est un verrou qui peut être utilisé pour contrôler l’accès à un objet blob (par exemple, un disque dur virtuel). Lorsqu’un objet blob est loué, seuls les propriétaires du bail peuvent accéder au blob. Un bail est important pour les raisons suivantes :

-   Il empêche la corruption des données si vous essaient d’écrire sur la même partie de l’objet blob en même temps plusieurs propriétaires.
-   Il empêche le blob d’être supprimé si quelque chose est activement l’utiliser (par exemple, une machine virtuelle).
-   Il empêche le compte de stockage d’être supprimé si quelque chose est activement l’utiliser (par exemple, une machine virtuelle).



## <a name="next-steps"></a>Étapes suivantes

- [Supprimer un compte de stockage](storage-create-storage-account.md#delete-a-storage-account)
- [Comment diviser le bail verrouillé du stockage blob dans Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
