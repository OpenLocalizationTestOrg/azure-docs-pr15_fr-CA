<properties
   pageTitle="Commencer à créer un Internet face à équilibrage de la charge en mode classique, à l’aide de PowerShell | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge en mode classique, à l’aide de PowerShell d’ouvert sur Internet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Commencer à créer un Internet face à l’équilibrage de la charge (classic) dans PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [apprendre à créer un équilibreur de charge à l’aide du Gestionnaire de ressources Azure d’ouvert sur Internet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## <a name="set-up-load-balancer-using-powershell"></a>Configuration d’équilibrage de la charge à l’aide de PowerShell

Pour configurer un équilibreur de charge à l’aide de powershell, suivez les étapes ci-dessous :

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Comment faire pour installer et configurer le PowerShell Azure](../../articles/powershell-install-configure.md) et suivez les instructions jusqu'à la fin pour signer dans Azure et sélectionnez votre abonnement.


2. Après avoir créé un ordinateur virtuel, vous pouvez utiliser les applets de commande PowerShell pour ajouter un équilibreur de charge à une machine virtuelle au sein du même service de cloud.

Dans l’exemple suivant, vous allez ajouter qu'un équilibreur de charge défini appelé « webfarm » vers le cloud service « mytestcloud » (ou myctestcloud.cloudapp.net), en ajoutant les points de terminaison pour l’équilibreur de charge pour les ordinateurs virtuels nommés « web1 » et « web2 ». L’équilibreur de charge reçoit le trafic du réseau sur le port 80 et l’équilibre de la charge entre les machines virtuelles définies par le point de terminaison local (dans ce cas le port 80) à l’aide de TCP.


### <a name="step-1"></a>Étape 1
Créer un point de terminaison d’équilibrage de la charge de la machine virtuelle la première « web1 »

    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### <a name="step-2"></a>Étape 2

Créer un autre point de terminaison pour la VM deuxième « web2 » en utilisant le même nom de jeu d’équilibrage de la charge

    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Supprimer un ordinateur virtuel à partir d’un équilibreur de charge

Supprimer-AzureEndpoint permet de supprimer un point de terminaison de machine virtuelle de l’équilibreur de charge

    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également [commencer à créer un équilibreur de charge interne](load-balancer-get-started-ilb-classic-ps.md) et de configurer le type de [mode de distribution](load-balancer-distribution-mode.md) d’un comportement de trafic de réseau especific charge équilibrage.

Si votre application a besoin conserver les connexions actives pour les serveurs situés derrière un équilibreur de charge, vous pouvez savoir plus sur les [paramètres de délai d’attente TCP inactives pour un équilibreur de charge](load-balancer-tcp-idle-timeout.md). Il vous aide à en savoir plus sur le comportement des connexions inactives lorsque vous utilisez l’équilibrage de la charge Azure.

