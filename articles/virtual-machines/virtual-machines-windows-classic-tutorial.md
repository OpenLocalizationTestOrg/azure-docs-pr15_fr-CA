<properties
    pageTitle="Créer un ordinateur virtuel dans le portail classique | Microsoft Azure"
    description="Créer une machine virtuelle Windows Azure portail classique."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="cynthn"/>

# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Créer un ordinateur virtuel exécutant Windows Azure portail classique

> [AZURE.SELECTOR]
- [Azure portal classique](virtual-machines-windows-classic-tutorial.md)
- [PowerShell : Déploiement standard](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de déploiement du Gestionnaire de ressources](virtual-machines-windows-hero-tutorial.md) à l’aide du **nouveau portail Azure**. 

Ce didacticiel vous montre comment créer une Azure machine virtuelle (VM) exécute Windows Azure portail classique. Nous allons utiliser une image Windows Server comme exemple, mais qui est une des nombreuses images Qu'azure offre. Notez que votre choix d’images dépendre de votre abonnement. Par exemple, les images de bureau Windows peuvent être disponibles pour les abonnés MSDN.

Cette section vous montre comment utiliser l’option **De la galerie** dans le portail classique Azure pour créer l’ordinateur virtuel. Cette option offre plusieurs choix de configuration que l’option **Création rapide** . Par exemple, si vous souhaitez joindre un ordinateur virtuel à un réseau virtuel, vous devez utiliser l’option **De la galerie** .

Vous pouvez également créer des ordinateurs virtuels à l’aide de [vos propres images](virtual-machines-windows-classic-createupload-vhd.md). Pour en savoir plus à ce sujet et autres méthodes, consultez les [différentes façons de créer une machine virtuelle Windows](virtual-machines-windows-creation-choices.md).



## <a name="video-walkthrough"></a>Procédure pas à pas vidéo

Voici une procédure pas à pas de ce didacticiel.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Créer l’ordinateur virtuel

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un ordinateur virtuel en utilisant le modèle de déploiement du Gestionnaire de ressources](virtual-machines-windows-hero-tutorial.md) dans le nouveau portail Azure. 

- Ouvrez une session sur l’ordinateur virtuel. Pour obtenir des instructions, voir [se connecter à un ordinateur virtuel exécutant Windows Server](virtual-machines-windows-classic-connect-logon.md).

- Attacher un disque pour stocker des données. Vous pouvez associer les disques vides et les disques qui contiennent des données. Pour obtenir des instructions, reportez-vous à l' [attacher à une machine virtuelle de Windows créé avec le modèle de déploiement classique, un disque de données](virtual-machines-windows-classic-attach-disk.md).
