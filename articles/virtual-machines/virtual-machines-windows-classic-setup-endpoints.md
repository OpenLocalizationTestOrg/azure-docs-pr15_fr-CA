<properties
    pageTitle="Configurer des points de terminaison sur une machine virtuelle de Windows classique | Microsoft Azure"
    description="Apprenez à configurer les points de terminaison pour un ordinateur virtuel Windows Azure portail classique pour permettre la communication avec une machine virtuelle Windows dans Azure."
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
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Comment configurer des points de terminaison sur une machine de virtuelle Windows classique dans Azure


Toutes les fenêtres d’ordinateurs virtuels que vous créez dans Azure en utilisant le modèle de déploiement classique peut automatiquement communiquent sur privé canal de réseau avec les autres ordinateurs virtuels dans le même service en nuage ou de réseau virtuel. Toutefois, des ordinateurs sur Internet ou d’autres réseaux virtuels nécessitent des points de terminaison pour diriger le trafic réseau entrant sur une machine virtuelle. Cet article est également disponible pour les [ordinateurs virtuels Linux](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Dans le modèle de déploiement **Gestionnaire de ressources** , les points de terminaison sont configurés à l’aide de **Groupes de sécurité réseau (NSGs)**. Pour plus d’informations, reportez-vous à la section [autoriser l’accès externe à votre machine virtuelle via le portail Azure] (virtual-machines-windows-nsg-quickstart-portal.md).

Lorsque vous créez une machine virtuelle Windows Azure portail classique, les points d’extrémité communs tels que ceux pour le Bureau à distance et l’accès distant de Windows PowerShell sont généralement créées pour vous automatiquement. Vous pouvez configurer les points de terminaison supplémentaires lors de la création de la machine virtuelle, ou par la suite selon vos besoins.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser une applet de commande PowerShell de Azure pour configurer un point de terminaison de machine virtuelle, consultez [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* Pour une applet de commande PowerShell de Azure permet de gérer une liste ACL sur un point de terminaison, consultez la [liste de contrôle d’accès de gestion (ACL) pour les points de terminaison à l’aide de PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Si vous avez créé un ordinateur virtuel dans le modèle de déploiement du Gestionnaire de ressources, vous pouvez utiliser PowerShell d’Azure pour [créer des groupes de sécurité de réseau](../virtual-network/virtual-networks-create-nsg-arm-ps.md) pour contrôler le trafic de la machine virtuelle.
