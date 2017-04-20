<properties
   pageTitle="Commencer à créer un Internet face à équilibrage de la charge dans le modèle de déploiement classique à l’aide de la CLI Azure | Microsoft Azure"
   description="Apprenez à créer un interréseau en vis-à-vis d’équilibrage de la charge dans le modèle de déploiement classique à l’aide de la CLI d’Azure"
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
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>Commencer à créer un Internet face à l’équilibrage de la charge (classic) dans l’infrastructure du langage commun Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [apprendre à créer un équilibreur de charge à l’aide du Gestionnaire de ressources Azure d’ouvert sur Internet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="step-by-step-creating-an-internet-facing-load-balancer-using-cli"></a>Étape par étape en créant un équilibreur de charge à l’aide de la CLI d’ouvert sur Internet

Ce guide montre comment créer un équilibreur de charge Internet en fonction du scénario ci-dessus.

1. Si vous n’avez jamais utilisé CLI d’Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécutez la commande **mode config azure** pour basculer vers le mode classique, comme illustré ci-dessous.

        azure config mode asm

    Sortie attendue :

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Créer le point de terminaison et l’équilibrage de la charge

Le scénario suppose que les ordinateurs virtuels « web1 » et « web2 » ont été créés.
Ce guide va créer un ensemble d’équilibrage de la charge à l’aide du port 80 comme port public et le port 80 comme port local. Un port de sonde est également configuré sur le port 80 et nommé le jeu d’équilibrage de la charge « lbset ».


### <a name="step-1"></a>Étape 1

À l’aide de l’équilibrage de charge et de créer le premier point de terminaison `azure network vm endpoint create` pour l’ordinateur virtuel « web1 ».

    azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset

Paramètres utilisés :

**-k** - port de la VM locale<br>
**-o** - protocole<BR>
**-t** - port de sonde<BR>
**-b** - nom d’équilibrage de la charge<BR>

## <a name="step-2"></a>Étape 2

Ajoutez un deuxième ordinateur virtuel « web2 » à l’ensemble d’équilibrage de la charge.

    azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## <a name="step-3"></a>Étape 3

Vérifier la configuration d’équilibrage de la charge à l’aide `azure vm show` .

    azure vm show web1

Le résultat sera :

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Créer un point de terminaison de bureau à distance pour un ordinateur virtuel

Vous pouvez créer un point de terminaison de bureau à distance pour transférer le trafic réseau à partir d’un port public à un port local pour un ordinateur virtuel spécifique à l’aide de `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Supprimer la machine virtuelle de l’équilibreur de charge

Vous devez supprimer le point de terminaison associé à l’équilibreur de charge à partir de l’ordinateur virtuel. Une fois le point de terminaison est supprimé, l’ordinateur virtuel n’appartient pas à l’équilibreur de charge définir plus.

 À l’aide de l’exemple ci-dessus, vous pouvez supprimer le point de terminaison créé pour l’ordinateur virtuel « web1 » équilibrage de la charge à l’aide de la commande « lbset » `azure vm endpoint delete`.

    azure vm endpoint delete web1 tcp-80-80


>[AZURE.NOTE] Vous pouvez explorer plus d’options pour gérer les points de terminaison à l’aide de la commande`azure vm endpoint --help`


## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)

