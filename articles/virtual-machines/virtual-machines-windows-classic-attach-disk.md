<properties
    pageTitle="Connectez un disque à une machine virtuelle | Microsoft Azure"
    description="Attacher un disque de données à un ordinateur virtuel de Windows créé avec le modèle classique de déploiement et de l’initialiser."
    services="virtual-machines-windows, storage"
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
    ms.date="06/27/2016"
    ms.author="cynthn"/>

# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Attacher un disque de données à un ordinateur virtuel de Windows créé avec le modèle de déploiement classique

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Si vous souhaitez utiliser le nouveau portail, reportez-vous à la section [comment attacher un disque de données à un ordinateur virtuel Windows Azure portail](virtual-machines-windows-attach-disk-portal.md).

Si vous avez besoin d’un disque de données supplémentaires, vous pouvez attacher un disque vide ou un disque existant avec les données d’une machine virtuelle. Dans les deux cas, les disques sont des fichiers .vhd qui résident dans un compte de stockage Azure. Dans le cas d’un nouveau disque, après avoir attaché le disque, vous devrez également initialiser afin qu’il soit prêt à être utilisé par une machine virtuelle de Windows.

Pour plus d’informations sur les disques, reportez-vous à la section [sur les disques et les disques durs virtuels pour les Machines virtuelles](virtual-machines-windows-about-disks-vhds.md).


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>Initialiser le disque

1. Se connecter à l’ordinateur virtuel. Pour obtenir des instructions, voir [comment ouvrir une session sur un ordinateur virtuel exécutant Windows Server][logon].

2. Une fois que vous ouvrez une session sur l’ordinateur virtuel, ouvrez le **Gestionnaire de serveur**. Dans le volet gauche, sélectionnez **Services de stockage et de fichiers**.

    ![Ouvrir le Gestionnaire de serveur](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Développez le menu et sélectionnez des **disques**.

4. La section **disques** répertorie les disques. Dans la plupart des cas, elle aura le disque 0, disque 1 et disque 2. Le disque 0 est le disque du système d’exploitation, le disque 1 est le disque temporaire et disque 2 est le disque de données que vous venez d’attacher à la machine virtuelle. Le nouveau disque de données répertorie la Partition comme **inconnu**. Cliquez sur le disque, puis sélectionnez **initialiser**.

5.  Vous êtes averti que toutes les données seront effacées lorsque le disque est initialisé. Cliquez sur **Oui** pour accuser réception de l’avertissement et initialiser le disque. Une fois terminé, la partition sera répertoriée comme **GPT**. Le disque avec le bouton droit à nouveau et sélectionnez **Nouveau Volume**.

6.  Terminez l’Assistant en utilisant les valeurs par défaut. Lorsque l’Assistant est terminé, la section de **Volumes** répertorie le nouveau volume. Le disque est désormais en ligne et prêt à stocker des données.

    ![Volume a été initialisée](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] La taille de la machine virtuelle détermine combien de disques vous pouvez attacher à celui-ci. Pour plus d’informations, reportez-vous à la section [tailles pour les machines virtuelles](virtual-machines-linux-sizes.md).

## <a name="additional-resources"></a>Ressources supplémentaires

[Comment faire pour détacher un disque à partir d’une machine virtuelle Windows](virtual-machines-windows-classic-detach-disk.md)

[Sur les disques et les disques durs virtuels pour les machines virtuelles](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md
