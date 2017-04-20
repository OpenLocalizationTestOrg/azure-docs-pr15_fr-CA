<properties
    pageTitle="Se connecter à un ordinateur virtuel du SQL Server (standard) | Microsoft Azure"
    description="Apprenez à vous connecter à SQL Server en cours d’exécution sur une Machine virtuelle dans Azure. Cette rubrique utilise le modèle de déploiement classique. Les scénarios diffèrent en fonction de la configuration réseau et l’emplacement du client."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Se connecter à un ordinateur de virtuel de SQL Server sur Azure (déploiement classique)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-connect.md)
- [Classique](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Vue d’ensemble

Cette rubrique explique comment se connecter à votre instance de SQL Server en cours d’exécution sur une machine virtuelle Azure. Elle couvre des [scénarios de connectivité général](#connection-scenarios) et fournit [la procédure détaillée pour la configuration de la connectivité de SQL Server dans un ordinateur virtuel d’Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Si vous utilisez des ordinateurs virtuels de gestionnaire de ressources, voir [se connecter à un ordinateur de virtuel SQL Server sur Azure à l’aide du Gestionnaire de ressources](virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scénarios de connexion

La manière dont un client se connecte à SQL Server en cours d’exécution sur une Machine virtuelle varie selon l’emplacement du client et la configuration de l’ordinateur ou au réseau. Ces scénarios sont les suivants :

- [Se connecter à SQL Server dans le même service de cloud](#connect-to-sql-server-in-the-same-cloud-service)
- [Se connecter à SQL Server via internet](#connect-to-sql-server-over-the-internet)
- [Se connecter à SQL Server sur le même réseau virtuel](#connect-to-sql-server-in-the-same-virtual-network)

>[AZURE.NOTE] Avant de vous connecter avec une de ces méthodes, vous devez suivre les [étapes de cet article pour configurer la connectivité](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Se connecter à SQL Server dans le même service de cloud

Plusieurs machines virtuelles peuvent être créés dans le même service de cloud. Pour comprendre ce scénario d’ordinateurs virtuels, voir [comment connecter les ordinateurs virtuels avec un service de nuage ou de réseau virtuel](virtual-machines-windows-classic-connect-vms.md#connect-vms-in-a-standalone-cloud-service). Ce scénario est lorsqu’un client sur une machine virtuelle tente de se connecter à SQL Server en cours d’exécution sur un autre ordinateur virtuel dans le même service de cloud.

Dans ce scénario, vous pouvez vous connecter à l’aide de la machine virtuelle **nom** (également indiquée en tant que **Nom d’ordinateur** ou **nom d’hôte** dans le portail). Il s’agit du nom que vous avez fourni pour la machine virtuelle lors de la création. Par exemple, si vous avez nommé votre SQL VM **mysqlvm**, un client VM dans le même service de cloud peut utiliser la chaîne de connexion pour vous connecter :

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Se connecter à SQL Server via Internet

Si vous souhaitez vous connecter à votre moteur de base de données SQL Server à partir d’Internet, vous devez créer un point de terminaison de machine virtuelle pour les communications TCP entrantes. Cette étape de configuration Azure, dirige le trafic du port TCP entrant pour un port TCP qui est accessible sur l’ordinateur virtuel.

Pour vous connecter sur internet, vous devez utiliser le nom DNS de l’ordinateur et le numéro de port du point de terminaison VM (configuré plus loin dans cet article). Pour trouver le nom de DNS, accédez au portail Azure et sélectionnez les **ordinateurs virtuels (classique)**. Sélectionnez ensuite votre machine virtuelle. Le **nom DNS** est indiqué dans la section **vue d’ensemble** .

Par exemple, considérez un ordinateur virtuel classique nommé **mysqlvm** avec le nom DNS de **mysqlvm7777.cloudapp.net** et d’un point de terminaison VM de **57500**. En supposant que la connectivité correctement configurée, la chaîne de connexion suivant peut servir à accéder à l’ordinateur virtuel depuis n’importe où sur internet :

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Même si cela permet la connectivité des clients sur internet, cela ne signifie pas que tout utilisateur peut se connecter à votre SQL Server. En dehors de clients ont le nom d’utilisateur correct et un mot de passe. Pour plus de sécurité, n’utilisez pas le port connu 1433 pour le point de terminaison public virtual machine. Et, si possible, pensez à utiliser une liste ACL sur votre point de terminaison pour limiter le trafic uniquement aux clients que vous autorisez. Pour obtenir des instructions sur l’utilisation des ACL avec les points de terminaison, voir [gérer le ACL sur un point de terminaison](virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

>[AZURE.NOTE] Il est important de noter que lorsque vous utilisez cette technique pour communiquer avec SQL Server, toutes les données sortantes à partir du centre de données Azure dépend de la normale [aux prix pour les transferts de données sortantes](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Se connecter à SQL Server sur le même réseau virtuel

[Réseau virtuel](../virtual-network/virtual-networks-overview.md) permet des scénarios supplémentaires. Vous pouvez connecter des ordinateurs virtuels sur le même réseau virtuel, même si ces machines virtuelles existent dans les services de nuage différent. Et avec un [VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md), vous pouvez créer une architecture hybride qui connecte des ordinateurs virtuels avec les ordinateurs et les réseaux locaux.

Les réseaux virtuels vous permet également de connecter vos ordinateurs virtuels d’Azure à un domaine. Il s’agit de la seule façon d’utiliser l’authentification Windows pour SQL Server. Les autres scénarios de connexion nécessitent l’authentification SQL avec les noms d’utilisateur et les mots de passe.

Si vous souhaitez configurer un environnement de domaine et l’authentification Windows, vous n’avez pas besoin utiliser les étapes de cet article pour configurer le point de terminaison public ou que l’authentification de SQL et les connexions d’accès. Dans ce scénario, vous pouvez vous connecter à votre instance de SQL Server en spécifiant le nom de la machine virtuelle de SQL Server dans la chaîne de connexion. L’exemple suivant suppose que l’authentification Windows a également été configurée et que l’utilisateur a accès à l’instance de SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Étapes de configuration de la connectivité de SQL Server dans un ordinateur virtuel d’Azure

Les étapes suivantes expliquent comment se connecter à l’instance de SQL Server sur internet à l’aide de SQL Server Management Studio (SSMS). Toutefois, les mêmes étapes s’appliquent à faire de votre machine virtuelle de SQL Server accessible pour vos applications, en cours d’exécution à la fois sur site et dans Azure.

Avant de vous connecter à l’instance de SQL Server à partir d’une autre VM ou internet, vous devez effectuer les tâches suivantes comme décrit dans les sections qui suivent :

- [Créer un point de terminaison TCP pour la machine virtuelle](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Ports TCP ouverts dans le pare-feu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurer SQL Server pour écouter sur le protocole TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurer SQL Server pour l’authentification en mode mixte](#configure-sql-server-for-mixed-mode-authentication)
- [Créer des connexions de l’authentification SQL Server](#create-sql-server-authentication-logins)
- [Déterminer le nom DNS de l’ordinateur virtuel](#determine-the-dns-name-of-the-virtual-machine)
- [Se connecter au moteur de base de données à partir d’un autre ordinateur](#connect-to-the-database-engine-from-another-computer)

Le chemin de connexion est résumé par le diagramme suivant :

![Connexion à un ordinateur virtuel de SQL Server](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic Steps](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous envisagez également d’utiliser des groupes de disponibilité AlwaysOn de haute disponibilité et reprise après sinistre, vous devez envisager d’implémenter un port d’écoute. Clients de base de données se connectent à l’écouteur, plutôt que directement à une des instances de SQL Server. L’écouteur dirige les clients vers réplica principal dans le groupe de disponibilité. Pour plus d’informations, consultez [configurer un écouteur ILB pour les groupes de disponibilité AlwaysOn dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Il est important de passer en revue toutes les méthodes conseillées de sécurité pour SQL Server s’exécutant sur une machine virtuelle Azure. Pour plus d’informations, consultez [Considérations de sécurité pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-security.md).

[Explorer le chemin d’accès de la formation](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur des machines virtuelles Azure. 

Pour les autres rubriques relatives à l’exécution de SQL Server dans Azure VM, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
