<properties
    pageTitle="Configurer un port d’écoute externe pour toujours sur les groupes de disponibilité | Microsoft Azure"
    description="Ce didacticiel vous guide tout au long des étapes de création d’un toujours sur Disponibilité groupe écouteur dans Azure qui est accessible de l’extérieur à l’aide de l’adresse IP virtuelle publique du service nuage associé."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurer un écouteur externe pour toujours sur les groupes de disponibilité dans Azure

> [AZURE.SELECTOR]
- [Port d’écoute interne](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Port d’écoute externe](virtual-machines-windows-classic-ps-sql-ext-listener.md)

Cette rubrique vous indique comment configurer un écouteur pour un toujours sur le groupe de disponibilité qui est accessible en externe sur internet. Cela est rendu possible en associant des adresse **Publique IP virtuelle (VIP)** du service cloud à l’écouteur.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Votre groupe de disponibilité peuvent contenir des réplicas locaux uniquement, Azure, ou s’étendent à la fois sur site et Azure pour les configurations hybride. Les réplicas Azure peuvent se trouver dans la même région ou dans plusieurs régions à l’aide de plusieurs réseaux virtuels (VNets). Les étapes suivantes supposent que vous avez déjà [configuré un groupe de disponibilité](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) mais que vous n’avez pas configuré un port d’écoute.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Instructions et limitations pour les écouteurs externes

Notez les instructions suivantes à propos de l’écouteur du groupe de disponibilité dans Azure lorsque vous déployez à l’aide de l’adresse VIP nuage du service répertorié :

- L’écouteur de groupe de disponibilité est pris en charge sur Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.

- L’application cliente doit résider sur un service en nuage différent que celui qui contient le groupe de disponibilité des machines virtuelles. Azure ne supporte pas le retour de serveur direct avec le client et le serveur dans le même service de cloud.

- Par défaut, les étapes décrites dans cet article montrent comment configurer un port d’écoute pour utiliser l’adresse IP virtuelle (VIP) de service cloud. Toutefois, il est possible de réserver et de créer plusieurs adresses VIP pour votre service cloud. Cela vous permet de suivre les étapes de cet article pour créer plusieurs écouteurs qui sont associés à une adresse IP virtuelle différente. Pour plus d’informations sur la création de plusieurs adresses VIP, voir [Plusieurs VIP par le service en nuage](../load-balancer/load-balancer-multivip.md).

- Si vous créez un écouteur pour un environnement hybride, le réseau local doit avoir une connexion à Internet public en plus de la connexion VPN de site à site avec le réseau virtuel Azure. Lorsque le sous-réseau Azure, l’écouteur de groupe de disponibilité est accessible uniquement par l’adresse IP publique du service nuage respectifs.

- Il n’est pas pris en charge pour créer un port d’écoute externe dans le même service de cloud où vous avez également un port d’écoute interne à l’aide de l’équilibreur de charge interne (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Déterminer l’accessibilité de l’écouteur

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Cet article se concentre sur la création d’un port d’écoute qui utilise **l’équilibrage de charge externe**. Si vous souhaitez un écouteur qui est le réseau virtuel privé, consultez la version de cet article explique comment configurer un [écouteur de ILB](virtual-machines-windows-classic-ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Créer des points de terminaison de machine virtuelle avec équilibrage de charge avec serveur direct retour

L’équilibrage de charge externe utilise le serveur virtuel l’adresse IP virtuelle publique du service cloud qui héberge vos ordinateurs virtuels. Ainsi, vous n’avez pas besoin créer ou configurer l’équilibreur de charge dans ce cas.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copiez le script PowerShell ci-dessous dans un éditeur de texte et définissez les valeurs de variable en fonction de votre environnement (valeurs par défaut ont été fournis pour certains paramètres). Notez que si votre groupe de disponibilité s’étend sur les régions d’Azure, vous devez exécuter le script une seule fois dans chaque centre de données pour le service en nuage et les nœuds qui résident dans ce centre de données.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

1. Une fois que vous avez défini les variables, copiez le script à partir de l’éditeur de texte dans votre session PowerShell d’Azure pour l’exécuter. Si l’invite indique toujours >>, tapez de nouveau sur ENTRÉE pour vous assurer que le script s’exécute.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Vérifiez que KB2854082 est installé si nécessaire

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Ouvrir les ports du pare-feu dans les nœuds de groupe de disponibilité

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Créer l’écouteur de groupe de disponibilité

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Pour l’équilibrage de charge externe, vous devez obtenir l’adresse IP virtuelle publique du service cloud qui contient vos duplications. Connectez-vous au portail classique Azure. Accédez au service de cloud qui contient votre machine virtuelle du groupe de disponibilité. Afficher le **tableau de bord** .

3. Notez l’adresse indiquée sous **les adresse IP publique virtuelle (VIP)**. Si votre solution couvre VNets, répétez cette étape pour chaque service de cloud qui contient un ordinateur virtuel qui héberge un réplica.

4. Sur l’un des ordinateurs virtuels, copiez le script PowerShell ci-dessous dans un éditeur de texte et définir les variables pour les valeurs que vous avez notée précédemment.

        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

        Import-Module FailoverClusters

        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Une fois vous avez défini les variables, ouvrez une fenêtre Windows PowerShell avec élévation de privilèges, puis copiez le script dans l’éditeur de texte et collez dans votre session PowerShell d’Azure pour l’exécuter. Si l’invite indique toujours >>, tapez de nouveau sur ENTRÉE pour vous assurer que le script s’exécute.

1. Répétez cette procédure sur chaque ordinateur virtuel. Ce script configure la ressource d’adresse IP avec l’adresse IP du service cloud et définit d’autres paramètres tels que le port de la sonde. Lorsque la ressource d’adresse IP est mise en ligne, il peut ensuite répondre à l’interrogation sur le port de la sonde du point de terminaison avec équilibrage de charge créé précédemment dans ce didacticiel.

## <a name="bring-the-listener-online"></a>Mettre le récepteur en ligne

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Éléments de suivi

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Test de l’écouteur du groupe de disponibilité (au sein de la même VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Test de l’écouteur de groupe de disponibilité (sur internet)

Pour accéder à l’écouteur à partir de l’extérieur du réseau virtuel, vous devez utiliser publique/externe équilibrage (décrites dans cette rubrique) au lieu de ILB, qui est uniquement accessible au sein de la même VNet. La chaîne de connexion, vous spécifiez le nom de service cloud. Par exemple, si vous aviez un service cloud avec le nom *mycloudservice*, l’instruction sqlcmd serait comme suit :

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Contrairement à l’exemple précédent, l’authentification SQL doit être utilisée, car l’appelant ne peut pas utiliser l’authentification windows sur internet. Pour plus d’informations, consultez [toujours sur le groupe de disponibilité dans Azure VM : scénarios de connectivité Client](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Lorsque vous utilisez l’authentification SQL, assurez-vous que vous créez la même connexion d’accès sur les deux réplicas. Pour plus d’informations sur la résolution des noms d’accès avec les groupes de disponibilité, consultez [comment mapper les connexions ou utiliser contenus utilisateur de base de données SQL pour vous connecter à d’autres réplicas et la mapper à des bases de données de disponibilité](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Si les réplicas toujours sur sont dans des sous-réseaux différents, les clients doivent spécifier **MultisubnetFailover = True** dans la chaîne de connexion. Ainsi, les tentatives de connexion parallèle aux réplicas dans des sous-réseaux différents. Notez que ce scénario inclut un déploiement toujours sur le groupe de disponibilité de cross-région.

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
