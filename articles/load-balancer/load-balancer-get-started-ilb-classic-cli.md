<properties
   pageTitle="Créer un équilibreur de charge interne à l’aide de la CLI d’Azure dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge interne à l’aide de la CLI d’Azure dans le modèle de déploiement classique"
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

# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Démarrer la création d’un équilibreur de charge interne (standard) à l’aide de la CLI d’Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Pour créer un équilibreur de charge interne pour les machines virtuelles

Pour créer un jeu d’équilibreur de charge interne et les serveurs qui enverra le trafic au il, vous devez effectuer les opérations suivantes :

1. Créez une instance d’interne l’équilibrage de charge qui sera le point de terminaison du trafic entrant à charge équilibrée entre les serveurs d’un ensemble équilibré en charge.

1. Ajouter des points de terminaison correspondant aux ordinateurs virtuels qui recevront le trafic entrant.

1. Configurez les serveurs qui enverra le trafic à charge équilibrée pour envoyer leur trafic vers l’adresse IP virtuelle (VIP) de l’instance de l’équilibrage de charge interne.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Création d’un équilibreur de charge interne à l’aide de la CLI étape par étape

Ce guide montre comment créer un équilibreur de charge interne en fonction du scénario ci-dessus.

1. Si vous n’avez jamais utilisé CLI d’Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécutez la commande **mode config azure** pour basculer vers le mode classique, comme illustré ci-dessous.

        azure config mode asm

    Sortie attendue :

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Créer le point de terminaison et l’équilibrage de la charge

Le scénario suppose que les ordinateurs virtuels « DB1 » et « DB2 » dans un service en nuage appelé « mytestcloud ». Les deux ordinateurs virtuels utilisent un réseau virtuel appelé « Mon testvnet » sous-réseau « sous-réseau-1".

Ce guide crée un jeu d’équilibreur de charge interne utilise le port 1433 en tant que port privé et 1433 en tant que port local.

Il s’agit d’un scénario courant dans lequel vous avez des machines virtuelles SQL sur le serveur principal à l’aide d’un équilibreur de charge interne pour garantir que les serveurs de base de données ne sera pas exposés directement à l’aide d’une adresse IP publique.


### <a name="step-1"></a>Étape 1

Créer un équilibreur de charge interne à l’aide de `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Paramètres utilisés :

**-r** - nom du service cloud<BR>
**-n** - nom d’équilibreur de charge interne<BR>
**-t** - nom du sous-réseau (même sous-réseau par les machines virtuelles que vous ajouterez à l’équilibreur de charge interne)<BR>
**-un** - (facultatif) ajoute une adresse IP privée<BR>

Extraire `azure service internal-load-balancer --help` pour plus d’informations.

Vous pouvez vérifier les propriétés d’équilibrage de la charge interne à l’aide de la commande `azure service internal-load-balancer list` *nom de service cloud*.

Cet exemple suit un exemple de la sortie :

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>Étape 2

Vous configurez le jeu d’équilibreur de charge interne lorsque vous ajoutez le premier point de terminaison. Vous associez le point de terminaison, la machine virtuelle et le port de sonde à l’ensemble d’équilibreur de charge interne dans cette étape.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Paramètres utilisés :

**-k** - port de la VM locale<BR>
**-t** - port de sonde<BR>
**-r** - protocole de sonde<BR>
**-e** - intervalle de sonde en secondes<BR>
**-f** - délai d’expiration en secondes <BR>
**-i** - nom d’équilibreur de charge interne <BR>


## <a name="step-3"></a>Étape 3

Vérifier la configuration d’équilibrage de la charge à l’aide `azure vm show` *nom de machine virtuelle*

    azure vm show DB1

Le résultat sera :

        azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK


## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Créer un point de terminaison de bureau à distance pour un ordinateur virtuel

Vous pouvez créer un point de terminaison de bureau à distance pour transférer le trafic réseau à partir d’un port public à un port local pour un ordinateur virtuel spécifique à l’aide de `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Supprimer la machine virtuelle de l’équilibreur de charge

Vous pouvez supprimer un ordinateur virtuel à partir d’un équilibreur de charge interne défini en supprimant le point de terminaison associé. Une fois le point de terminaison est supprimé, l’ordinateur virtuel n’appartiennent à l’équilibreur de charge définir plus.

 À l’aide de l’exemple ci-dessus, vous pouvez supprimer le point de terminaison créé pour la machine virtuelle « DB1 » à partir de l’équilibreur de charge interne « ilbset » à l’aide de la commande `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


Extraire `azure vm endpoint --help` pour plus d’informations.


## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de la charge l’affinité d’IP source](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)