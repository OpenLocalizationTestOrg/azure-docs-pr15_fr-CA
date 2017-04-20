<properties
    pageTitle="Capturer une image d’un Windows Azure VM | Microsoft Azure"
    description="Capturer une image d’un ordinateur virtuel de Azure Windows créé avec le modèle de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

#<a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capturer une image d’un ordinateur virtuel de Azure Windows créé avec le modèle de déploiement classique.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour plus d’informations de modèle de gestionnaire de ressources, consultez [créer une copie virtuelle de Windows en cours d’exécution dans Azure](virtual-machines-windows-vhd-copy.md).


Cet article vous montre comment capturer une machine virtuelle Azure exécutant Windows afin que vous puissiez sous forme d’image pour créer d’autres machines virtuelles. Cette image inclut le disque du système d’exploitation et tous les disques de données associés à l’ordinateur virtuel. Il n’inclut pas les configurations réseau, vous devez les configurer, lorsque vous créez d’autres machines virtuelles qui utilisent l’image.

Azure stocke l’image sous **Mes Images**. Il s’agit de l’endroit même où sont stockées les images que vous avez téléchargé. Pour plus d’informations sur les images, consultez [à propos des images des machines virtuelles](virtual-machines-linux-classic-about-images.md).

##<a name="before-you-begin"></a>Avant de commencer##

Ces étapes supposent que vous avez déjà créé un ordinateur virtuel Azure et configuré le système d’exploitation, y compris l’attachement des disques de données. Si vous n’avez pas ce fait encore, consultez ces instructions :

- [Créer un ordinateur virtuel à partir d’une image](virtual-machines-windows-classic-createportal.md)
- [Comment attacher un disque de données à une machine virtuelle](virtual-machines-windows-classic-attach-disk.md)
- Assurez-vous que les rôles de serveur sont pris en charge avec Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.WARNING] Ce processus supprime l’ordinateur virtuel d’origine une fois qu’il est capturé. 

Avant de caputuring une image d’un ordinateur virtuel Azure, il est recommandé de sauvegarder l’ordinateur virtuel cible. Les machines virtuelles Azure peuvent être sauvegardés à l’aide de la sauvegarde d’Azure. Pour plus d’informations, voir [sauvegarder des ordinateurs virtuels Azure](../backup/backup-azure-vms.md). D’autres solutions sont disponibles auprès des partenaires certifiés. Pour savoir ce qui est actuellement disponible, recherchez l’Azure Marketplace.


##<a name="capture-the-virtual-machine"></a>Capture de la machine virtuelle

1. Dans [Azure portal classique](http://manage.windowsazure.com), **se connecter** à l’ordinateur virtuel. Pour obtenir des instructions, reportez-vous à la section [comment se connecter à un ordinateur virtuel exécutant Windows Server] [].

2.  En tant qu’administrateur, ouvrez une fenêtre d’invite de commande.

3.  Basculez vers le répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.

4.  La boîte de dialogue **Outil de préparation système** s’affiche. Effectuez le des opérations suivantes :

    - Dans l' **Action de nettoyage du système**, sélectionnez **entrer le système Out-of-Box Experience (OOBE)** et assurez-vous que l’option **Generalize** est activée. Pour plus d’informations sur l’utilisation de Sysprep, consultez [Comment faire pour utiliser Sysprep : An Introduction][].

    - Dans les **Options d’arrêt**, sélectionnez **Arrêter le système**.

    - Cliquez sur **OK**.

    ![Exécutez Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.  Sysprep s’arrête l’ordinateur virtuel, ce qui modifie l’état de la machine virtuelle dans Azure portal classique **s’est arrêté**.

8.  Dans le portail Azure classique, cliquez sur des **Machines virtuelles** et sélectionnez l’ordinateur virtuel que vous souhaitez capturer.

9.  Dans la barre de commande, cliquez sur **capturer**.

    ![Capture de machine virtuelle](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

    La boîte de dialogue **capturer l’ordinateur virtuel** s’affiche.

10. Dans la zone **Nom de l’Image**, tapez un nom pour la nouvelle image.

11. Avant d’ajouter une image de Windows Server à votre ensemble d’images personnalisés, il doit être généralisée en exécutant Sysprep comme indiqué dans les étapes précédentes. Cliquez sur **J’ai exécuté Sysprep sur l’ordinateur virtuel** pour indiquer que vous l’avez fait.

12. Cliquez sur la case à cocher pour capturer l’image. La nouvelle image est maintenant disponible sous **les Images**.

    ![Capture de l’image réussie](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##<a name="next-steps"></a>Étapes suivantes

L’image est prêt à être utilisé pour créer des machines virtuelles. Pour ce faire, vous allez créer un ordinateur virtuel à l’aide de l’élément de menu **De la galerie** et sélectionnez l’image que vous venez de créer. Pour obtenir des instructions, voir [Création d’une machine virtuelle à partir d’une image](virtual-machines-windows-classic-createportal.md).



[Comment faire pour se connecter à un ordinateur virtuel exécutant Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Comment faire pour utiliser Sysprep : Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
