<properties 
    pageTitle="Redéployer les machines virtuelles Windows | Microsoft Azure" 
    description="Explique comment redéployer des machines virtuelles de Windows pour limiter les problèmes de connexion RDP." 
    services="virtual-machines-windows" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-windows" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>


# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Redéployer l’ordinateur virtuel pour le nouveau nœud Azure

Si vous avez été confronté à des difficultés résolution des problèmes de bureau à distance (RDP) connexion ou application l’accès à Windows Azure machine virtuelle (VM), redéploiement de la machine virtuelle peut vous aider. Lorsque vous redéployez un ordinateur virtuel, il déplace de la machine virtuelle vers un nouveau nœud au sein de l’infrastructure Azure et il se met sous tension, en conservant tous vos options de configuration et de ressources associées. Cet article vous explique comment redéployer une machine virtuelle à l’aide de PowerShell d’Azure ou le portail Azure.

> [AZURE.NOTE] Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques liées à l’interface réseau virtuelle sont mis à jour. 

## <a name="using-azure-powershell"></a>À l’aide de PowerShell Azure

Vérifiez que vous disposez de la dernière PowerShell Azure 1.x installé sur votre ordinateur. Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Cette commande PowerShell d’Azure permet de redéployer votre machine virtuelle :

    Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes de connexion à votre ordinateur virtuel, vous trouverez une aide spécifique sur la [résolution des problèmes de connexions RDP](virtual-machines-windows-troubleshoot-rdp-connection.md) ou [RDP détaillée des étapes de dépannage](virtual-machines-windows-detailed-troubleshoot-rdp.md). Si vous ne peut pas accéder à une application en cours d’exécution sur votre ordinateur virtuel, vous pouvez également lire les [problèmes de résolution des problèmes d’application](virtual-machines-windows-troubleshoot-app-connection.md).