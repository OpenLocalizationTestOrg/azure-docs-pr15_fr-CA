<properties
    pageTitle="Généraliser un disque dur virtuel de Windows | Microsoft Azure"
    description="Apprenez à utiliser Sysprep pour généraliser une VM Windows à utiliser avec le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
    
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Généraliser une machine virtuelle de Windows à l’aide de Sysprep

Cette section vous indique comment generalize votre machine Windows pour une utilisation en tant qu’image. Sysprep supprime toutes vos informations de compte personnelles, entre autres choses et prépare l’ordinateur pour être utilisés en tant qu’image. Pour plus d’informations sur Sysprep, consultez [Comment faire pour utiliser Sysprep : An Introduction](http://technet.microsoft.com/library/bb457073.aspx).

Assurez-vous que les rôles de serveur en cours d’exécution sur l’ordinateur sont prises en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

>[AZURE.IMPORTANT] Si vous exécutez Sysprep avant le téléchargement de votre disque dur virtuel vers Azure pour la première fois, vérifiez que vous avez [préparé votre machine virtuelle](virtual-machines-windows-prepare-for-upload-vhd-image.md) avant d’exécuter Sysprep. 

1. Connectez-vous à la machine virtuelle Windows.

2. Ouvrez la fenêtre d’invite de commande en tant qu’administrateur. Modifiez le répertoire **%windir%\system32\sysprep**et exécutez `sysprep.exe`.

3. Dans la boîte de dialogue **Outil de préparation système** , sélectionnez **entrer le système Out-of-Box Experience (OOBE)**et assurez-vous que la case à cocher **Generalize** est sélectionnée.

4. Dans les **Options d’arrêt**, sélectionnez **Arrêter le système**.

5. Cliquez sur **OK**.

    ![Démarrez Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

6. Lorsque Sysprep s’achève, il arrête la machine virtuelle. 

## <a name="next-steps"></a>Étapes suivantes

- Si la machine virtuelle est locale, vous pouvez à présent [Télécharger le disque dur virtuel vers Azure](virtual-machines-windows-upload-image.md).
- Si la machine virtuelle est déjà dans Azure, vous pouvez désormais [créer une image à partir de la machine virtuelle généralisée](virtual-machines-windows-capture-image.md).