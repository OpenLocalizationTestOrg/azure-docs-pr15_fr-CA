<properties
    pageTitle="Se connecter à un ordinateur virtuel du SQL Server (Gestionnaire de ressources) | Microsoft Azure"
    description="Apprenez à vous connecter à SQL Server en cours d’exécution sur une Machine virtuelle dans Azure. Cette rubrique utilise le modèle de déploiement classique. Les scénarios diffèrent en fonction de la configuration réseau et l’emplacement du client."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Se connecter à un ordinateur de virtuel de SQL Server sur Azure (responsable de ressources)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-connect.md)
- [Classique](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Vue d’ensemble

Cette rubrique explique comment se connecter à votre instance de SQL Server en cours d’exécution sur une machine virtuelle Azure. Elle couvre des [scénarios de connectivité général](#connection-scenarios) et fournit [la procédure détaillée pour la configuration de la connectivité de SQL Server dans un ordinateur virtuel d’Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique. Pour afficher la version classique de cet article, reportez-vous à la section [connexion à un ordinateur de virtuel de SQL Server sur Azure classique](virtual-machines-windows-classic-sql-connect.md).

Si vous préférez qu’une vue d’ensemble complète de provisionnement et de connectivité, reportez-vous à la section [mise en service d’une Machine de virtuelle de SQL Server sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Scénarios de connexion

La manière dont un client se connecte à SQL Server en cours d’exécution sur une Machine virtuelle varie selon l’emplacement du client et la configuration de l’ordinateur ou au réseau. Ces scénarios sont les suivants :

- [Se connecter à SQL Server via internet](#connect-to-sql-server-over-the-internet)
- [Se connecter à SQL Server sur le même réseau virtuel](#connect-to-sql-server-in-the-same-virtual-network)

### <a name="connect-to-sql-server-over-the-internet"></a>Se connecter à SQL Server via Internet

Si vous souhaitez vous connecter à votre moteur de base de données SQL Server à partir d’Internet, il existe en plusieurs étapes, telles que la configuration du pare-feu, activation de l’authentification SQL et la configuration de votre groupe de sécurité réseau que vous devez configurer une règle de groupe de sécurité de réseau pour autoriser le trafic TCP sur le port 1433.

Si vous utilisez le portail pour fournir une image de machine virtuelle de SQL Server avec le Gestionnaire de ressources, ces étapes sont effectuées pour vous, lorsque vous sélectionnez **Public** pour l’option de connectivité SQL :

![Option de connectivité SQL publique au cours de la mise en service](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Si ce n’était pas un au cours de la mise en service, puis vous pouvez configurer manuellement vos machines virtuelles et SQL Server en suivant les [étapes de cet article pour configurer manuellement la connectivité](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE] L’image de l’ordinateur virtuel pour SQL Server Express n’active pas automatiquement le protocole TCP/IP. Pour l’édition Express, vous devez utiliser le Gestionnaire de Configuration SQL Server pour [activer manuellement le protocole TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) après la création de la machine virtuelle.

Une fois cette opération effectuée, tout client disposant d’un accès à internet peut se connecter à l’instance de SQL Server en spécifiant l’adresse IP publique de l’ordinateur virtuel ou le nom DNS attribué à cette adresse IP. Si le port SQL Server est 1433, vous n’avez pas besoin de le spécifier dans la chaîne de connexion.

    "Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Même si cela permet la connectivité des clients sur internet, cela ne signifie pas que tout utilisateur peut se connecter à votre SQL Server. En dehors de clients ont le nom d’utilisateur correct et un mot de passe. Pour plus de sécurité, vous pouvez éviter le port connu 1433. Par exemple, si vous configuré SQL Server pour écouter sur le port 1500 et établi les pare-feu et les règles de groupe de sécurité de réseau, vous pourriez se connecter en ajoutant le numéro de port au nom du serveur, comme dans l’exemple suivant :

    "Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] Il est important de noter que lorsque vous utilisez cette technique pour communiquer avec SQL Server, toutes les données sortantes à partir du centre de données Azure dépend de la normale [aux prix pour les transferts de données sortantes](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Se connecter à SQL Server sur le même réseau virtuel

[Réseau virtuel](../virtual-network/virtual-networks-overview.md) permet des scénarios supplémentaires. Vous pouvez connecter des ordinateurs virtuels sur le même réseau virtuel, même si ces machines virtuelles existent dans différents groupes de ressources. Et avec un [VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md), vous pouvez créer une architecture hybride qui connecte des ordinateurs virtuels avec les ordinateurs et les réseaux locaux.

Les réseaux virtuels vous permet également de connecter vos ordinateurs virtuels d’Azure à un domaine. Il s’agit de la seule façon d’utiliser l’authentification Windows pour SQL Server. Les autres scénarios de connexion nécessitent l’authentification SQL avec les noms d’utilisateur et les mots de passe.

Si vous utilisez le portail pour fournir une image de machine virtuelle de SQL Server avec le Gestionnaire de ressources, les règles de pare-feu appropriées pour la communication sur le réseau virtuel sont le programme d’installation lorsque vous sélectionnez **privé** pour l’option de connectivité SQL. Si ce n’était pas un au cours de la mise en service, puis vous pouvez configurer manuellement vos machines virtuelles et SQL Server en suivant les [étapes de cet article pour configurer manuellement la connectivité](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Mais si vous envisagez de configurer un environnement de domaine et l’authentification Windows, vous n’avez pas besoin utiliser les étapes de cet article pour configurer des connexions d’accès et l’authentification SQL. Également, vous n’avez pas besoin de configurer des règles de groupe de sécurité de réseau pour l’accès via internet.

>[AZURE.NOTE] L’image de l’ordinateur virtuel pour SQL Server Express n’active pas automatiquement le protocole TCP/IP. Pour l’édition Express, vous devez utiliser le Gestionnaire de Configuration SQL Server pour [activer manuellement le protocole TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) après la création de la machine virtuelle.

En supposant que vous avez configuré DNS de votre réseau virtuel, vous pouvez vous connecter à votre instance de SQL Server en spécifiant le nom de l’ordinateur SQL Server VM dans la chaîne de connexion. L’exemple suivant suppose également que l’authentification Windows a également été configurée et que l’utilisateur a accès à l’instance de SQL Server.

    "Server=mysqlvm;Integrated Security=true"

Notez que dans ce scénario, vous pouvez également spécifier l’adresse IP de la machine virtuelle.

## <a name="steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm"></a>Étapes de configuration manuelle de connectivité de SQL Server dans un ordinateur virtuel d’Azure

Les étapes suivantes expliquent comment configurer manuellement la connectivité à l’instance de SQL Server et puis également pour vous connecter via internet à l’aide de SQL Server Management Studio (SSMS). Notez que le nombre de ces opérations sont effectuées automatiquement lorsque vous sélectionnez les options de connectivité de SQL Server appropriées dans le portail.

Avant de vous connecter à l’instance de SQL Server à partir d’une autre VM ou internet, vous devez effectuer les tâches suivantes comme décrit dans les sections qui suivent :

- [Ports TCP ouverts dans le pare-feu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurer SQL Server pour écouter sur le protocole TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurer SQL Server pour l’authentification en mode mixte](#configure-sql-server-for-mixed-mode-authentication)
- [Créer des connexions de l’authentification SQL Server](#create-sql-server-authentication-logins)
- [Configurer une règle de trafic entrant du groupe de sécurité réseau](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [Configurer un nom DNS pour l’adresse IP publique](#configure-a-dns-label-for-the-public-ip-address)
- [Se connecter au moteur de base de données à partir d’un autre ordinateur](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour voir la mise en service des instructions avec ces étapes de connectivité, reportez-vous à [une machine de virtuelle de SQL Server sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Explorer le chemin d’accès de la formation](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur des machines virtuelles Azure.

Pour les autres rubriques relatives à l’exécution de SQL Server dans Azure VM, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
