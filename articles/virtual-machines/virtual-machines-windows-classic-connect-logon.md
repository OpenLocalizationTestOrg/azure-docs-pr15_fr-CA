<properties
    pageTitle="Ouvrez une session sur un classique Azure VM | Microsoft Azure"
    description="Le portail classique Azure permet d’ouvrir une session sur un ordinateur virtuel de Windows créé avec le modèle de déploiement classique."
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
    ms.date="07/28/2016"
    ms.author="cynthn"/>


# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Ouvrez une session sur un ordinateur virtuel de Windows en utilisant le portail classique Azure

Dans le portail Azure classique, vous utilisez le bouton **connexion** pour démarrer une session de bureau à distance et d’ouvrir une session sur un ordinateur virtuel de Windows.

Voulez vous connecter à une VM Linux ? Découvrez [comment ouvrir une session sur un ordinateur virtuel exécutant Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Découvrez comment [exécuter ces étapes à l’aide du nouveau portail Azure](virtual-machines-windows-connect-logon.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 

## <a name="video-walkthrough"></a>Procédure pas à pas vidéo

Voici une procédure pas à pas la vidéo des étapes de ce didacticiel. Il couvre également les points de terminaison et les ports publics et privés, utilisés pour la connexion à un ordinateur virtuel Windows Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## <a name="connect-to-the-virtual-machine"></a>Se connecter à l’ordinateur virtuel

1. Connectez-vous au portail Azure classique.

2. Cliquez sur des **Machines virtuelles**et sélectionnez l’ordinateur virtuel.

3. Dans la barre au bas de la page, cliquez sur **se connecter**.

    ![Ouvrez une session sur l’ordinateur virtuel](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
    
> [AZURE.TIP] Si le bouton **connexion** n’est pas disponible, consultez les conseils de dépannage à la fin de cet article.

## <a name="log-on-to-the-virtual-machine"></a>Ouvrez une session sur l’ordinateur virtuel

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Étapes suivantes

-   Si le bouton **se connecter** est inactif ou si vous rencontrez d’autres problèmes avec la connexion Bureau à distance, réinitialisez la configuration. Dans le tableau de bord de la machine virtuelle sous l' **Aperçu rapide**, cliquez sur **Réinitialiser la configuration à distance**.
-   Pour les problèmes avec votre mot de passe, essayez de rétablir. Dans le tableau de bord de machine virtuelle, sous **Aperçu rapide**, cliquez sur **Réinitialiser le mot de passe**.

Si ces conseils ne fonctionnent pas ou ne sont pas de ce dont vous avez besoin, consultez [résoudre les problèmes de bureau à distance, connexions à un Windows Azure Machine virtuelle](virtual-machines-windows-troubleshoot-rdp-connection.md). Cet article vous guide à travers le diagnostic et la résolution des problèmes courants.


