<properties
    pageTitle="Configurer des points de terminaison sur une VM Linux classique | Microsoft Azure"
    description="Apprendre à définir des points de terminaison pour une VM Linux dans Azure portal classique pour permettre la communication avec une machine virtuelle Linux dans Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/13/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Comment configurer des points de terminaison sur une machine virtuelle Linux la classique dans Azure

Tous les ordinateurs virtuels Linux que vous créez dans Azure en utilisant le modèle de déploiement classique peut communiquer automatiquement sur un canal de réseau privé avec d’autres machines virtuelles dans le même service en nuage ou de réseau virtuel. Toutefois, des ordinateurs sur Internet ou d’autres réseaux virtuels nécessitent des points de terminaison pour diriger le trafic réseau entrant sur une machine virtuelle. Cet article est également disponible pour les [machines virtuelles Windows](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Dans le modèle de déploiement **Gestionnaire de ressources** , les points de terminaison sont configurés à l’aide de **Groupes de sécurité réseau (NSGs)**. Pour plus d’informations, reportez-vous à la section [ouverture de ports et points de terminaison] (virtuel-machines-linux-nsg-quickstart.md).

Lorsque vous créez une machine virtuelle Linux dans Azure portal classique, un point de terminaison de SSH (Secure Shell) est généralement automatiquement créé pour vous. Vous pouvez configurer les points de terminaison supplémentaires lors de la création de la machine virtuelle, ou par la suite selon vos besoins.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez également créer un point de terminaison de machine virtuelle à l’aide de l' [Interface de ligne de commande de Azure](../virtual-machines-command-line-tools.md). Exécutez la commande de **Création de point de terminaison azure vm** .

* Si vous avez créé un ordinateur virtuel dans le modèle de déploiement du Gestionnaire de ressources, vous pouvez utiliser la CLI Azure en mode Gestionnaire de ressources pour [créer des groupes de sécurité de réseau](../virtual-network/virtual-networks-create-nsg-arm-cli.md) pour contrôler le trafic de la machine virtuelle.