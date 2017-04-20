<properties
    pageTitle="Configurer un écouteur ILB de toujours sur les groupes de disponibilité | Microsoft Azure"
    description="Ce didacticiel utilise les ressources créées avec le modèle de déploiement classique et crée un toujours sur Disponibilité groupe écouteur dans Azure à l’aide d’un équilibreur de charge interne (ILB)."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurer un écouteur ILB pour toujours sur les groupes de disponibilité dans Azure

> [AZURE.SELECTOR]
- [Port d’écoute interne](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Port d’écoute externe](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="overview"></a>Vue d’ensemble

Cette rubrique vous indique comment configurer un écouteur pour un toujours sur le groupe de disponibilité à l’aide d’un **Équilibreur de charge interne (ILB)**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour configurer un écouteur ILB pour un groupe de disponibilité permanente dans le modèle de gestionnaire de ressources, consultez [configurer un équilibreur de charge interne pour un groupe de disponibilité permanente dans Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).


Votre groupe de disponibilité peuvent contenir des réplicas locaux uniquement, Azure, ou s’étendent à la fois sur site et Azure pour les configurations hybride. Les réplicas Azure peuvent se trouver dans la même région ou dans plusieurs régions à l’aide de plusieurs réseaux virtuels (VNets). Les étapes suivantes supposent que vous avez déjà [configuré un groupe de disponibilité](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) mais que vous n’avez pas configuré un port d’écoute.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Instructions et limitations pour les écouteurs internes
Notez les instructions suivantes sur l’écouteur du groupe de disponibilité dans Azure à l’aide de ILB :

- L’écouteur de groupe de disponibilité est pris en charge sur Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.

- Écouteur de groupe interne de disponibilité qu’un seul est pris en charge par le service en nuage, car le port d’écoute est configuré pour le ILB, et il y en a qu’un seul ILB par le service en nuage. Toutefois, il est possible de créer plusieurs écouteurs externes. Pour plus d’informations, consultez [configurer un écouteur externe pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

- Il n’est pas pris en charge pour créer un port d’écoute interne dans le service cloud même où vous avez également un écouteur externe à l’aide de VIP de public du service nuage.

## <a name="determine-the-accessibility-of-the-listener"></a>Déterminer l’accessibilité de l’écouteur

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Cet article se concentre sur la création d’un port d’écoute utilisant un **Équilibreur de charge interne (ILB)**. Si vous avez besoin d’un écouteur publique/externe, consultez la version de cet article explique comment configurer un [écouteur externe](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Créer des points de terminaison de machine virtuelle avec équilibrage de charge avec serveur direct retour

Pour ILB, vous devez d’abord créer l’équilibreur de charge interne. Pour cela, utilisez le script ci-dessous.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Pour **ILB**, vous devez affecter une adresse IP statique. Tout d’abord, examinez la configuration actuelle de VNet en exécutant la commande suivante :

        (Get-AzureVNetConfig).XMLConfiguration

1. Notez le nom de **sous-réseau** pour le sous-réseau qui contient les ordinateurs virtuels qui hébergent les réplicas. Il sera utilisé dans le paramètre **$SubnetName** dans le script.

1. Notez le nom de la **VirtualNetworkSite** et **l’AddressPrefix** départ pour le sous-réseau qui contient les ordinateurs virtuels qui hébergent les réplicas. Recherchez une adresse IP disponible en passant les deux valeurs à la commande **Test-AzureStaticVNetIP** et en examinant **AvailableAddresses**. Par exemple, si le VNet a été nommé *MyVNet* et une adresse de sous-réseau plage qui ont démarré à *172.16.0.128*, la commande suivante énumère les adresses disponibles :

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Choisissez l’une des adresses disponibles et l’utiliser dans le paramètre **$ILBStaticIP** du script suivant.

3. Copiez le script PowerShell ci-dessous dans un éditeur de texte et définissez les valeurs de variable en fonction de votre environnement (Notez que les valeurs par défaut ont été prévues pour certains paramètres). Notez que des déploiements existants qui utilisent des groupes d’affinité ne peut pas ajouter de ILB. Pour plus d’informations sur les exigences de ILB, consultez [l’Équilibreur de charge interne](../load-balancer/load-balancer-internal-overview.md). En outre, si votre groupe de disponibilité s’étend sur les régions d’Azure, vous devez exécuter une fois le script dans chaque centre de données pour le service en nuage et les nœuds qui résident dans ce centre de données.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

1. Une fois que vous avez défini les variables, copiez le script à partir de l’éditeur de texte dans votre session PowerShell d’Azure pour l’exécuter. Si l’invite indique toujours >>, tapez de nouveau sur ENTRÉE pour vous assurer que le script s’exécute. Remarque

>[AZURE.NOTE] Le portail classique Azure ne prend pas charge l’équilibreur de charge interne à ce stade, vous ne verrez pas les points de terminaison dans Azure portal classique ou de l’ILB. Toutefois, **Get-AzureEndpoint** renvoie une adresse IP interne si l’équilibreur de charge est en cours d’exécution dessus. Sinon, elle retourne null.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Vérifiez que KB2854082 est installé si nécessaire

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Ouvrir les ports du pare-feu dans les nœuds de groupe de disponibilité

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Créer l’écouteur de groupe de disponibilité

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Pour ILB, vous devez utiliser l’adresse IP de l’équilibreur de charge interne (ILB) créé précédemment. Utilisez le script suivant pour obtenir cette adresse IP dans PowerShell.

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. Sur l’un des ordinateurs virtuels, copiez le script PowerShell pour votre système d’exploitation dans un éditeur de texte et définir les variables pour les valeurs que vous avez notée précédemment.

    Pour Windows Server 2012 ou version ultérieure, utilisez le script suivant :

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
        
    Pour Windows Server 2008 R2, utilisez le script suivant :

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
    

1. Une fois vous avez défini les variables, ouvrez une fenêtre Windows PowerShell avec élévation de privilèges, puis copiez le script dans l’éditeur de texte et collez dans votre session PowerShell d’Azure pour l’exécuter. Si l’invite indique toujours >>, tapez de nouveau sur ENTRÉE pour vous assurer que le script s’exécute.

2. Répétez cette procédure sur chaque ordinateur virtuel. Ce script configure la ressource d’adresse IP avec l’adresse IP du service cloud et définit d’autres paramètres tels que le port de la sonde. Lorsque la ressource d’adresse IP est mise en ligne, il peut ensuite répondre à l’interrogation sur le port de la sonde du point de terminaison avec équilibrage de charge créé précédemment dans ce didacticiel.

## <a name="bring-the-listener-online"></a>Mettre le récepteur en ligne

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Éléments de suivi

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Test de l’écouteur du groupe de disponibilité (au sein de la même VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
