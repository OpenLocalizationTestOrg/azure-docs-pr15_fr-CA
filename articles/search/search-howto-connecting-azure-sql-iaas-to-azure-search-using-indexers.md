<properties 
    pageTitle="Configurer une connexion à partir d’un indexeur de recherche d’Azure à SQL Server sur une machine virtuelle Azure | Microsoft Azure | Indexeurs" 
    description="Activer des connexions chiffrées et configurer le pare-feu pour autoriser les connexions à SQL Server sur une machine virtuelle Azure (VM) à partir d’un indexeur de recherche d’Azure." 
    services="search" 
    documentationCenter="" 
    authors="jack4it" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/26/2016" 
    ms.author="jackma"/>

# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurer une connexion à partir d’un indexeur de recherche d’Azure à SQL Server sur un ordinateur virtuel d’Azure

Comme indiqué dans la [Connexion de la base de données de SQL Azure à la recherche d’Azure à l’aide d’indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), création d’indexeurs par rapport à **SQL Server sur Azure VM** (ou **SQL Azure VM** en abrégé) est pris en charge par recherche d’Azure, mais il existe quelques conditions requises liées à la sécurité pour prendre en charge de la première. 

**Durée de la tâche :** 30 minutes environ, en supposant que vous déjà installé un certificat sur l’ordinateur virtuel.

## <a name="enable-encrypted-connections"></a>Activer des connexions chiffrées

Recherche Azure requiert un canal crypté pour toutes les demandes d’indexeur sur une connexion internet publique. Cette section répertorie les étapes pour que cela fonctionne.

1. Vérifiez les propriétés du certificat pour vérifier que le nom du sujet est le nom de domaine complet (FQDN) de la machine virtuelle d’Azure. Vous pouvez utiliser un outil tel que CertUtils ou le composant logiciel enfichable Certificats pour afficher les propriétés. Vous pouvez obtenir le nom de domaine complet à partir de la section Essentials de la lame service machine virtuelle, dans le champ **adresse IP publique adresses/DNS nom d’étiquette** , dans le [portail Azure](https://portal.azure.com/).

    - Pour les machines virtuelles créées en utilisant le modèle de **Gestionnaire de ressources** plus récent, le nom de domaine complet est mis en forme en tant que `<your-VM-name>.<region>.cloudapp.azure.com`. 

    - Pour les machines virtuelles plus anciens comme une **classique** VM, le nom de domaine complet est mis en forme en tant que `<your-cloud-service-name.cloudapp.net>`. 

2. Configurer SQL Server pour utiliser le certificat à l’aide de l’Éditeur du Registre (regedit). 

    Bien que le Gestionnaire de Configuration SQL Server est souvent utilisé pour cette tâche, vous ne pouvez pas l’utiliser pour ce scénario. Elle ne trouvera pas le certificat importé, car le nom de domaine complet de la machine virtuelle sur Azure ne correspond pas au nom de domaine complet tel que déterminé par la machine virtuelle (il identifie le domaine en tant que l’ordinateur local ou de domaine du réseau auquel il est rattaché). Lorsque les noms ne correspondent pas, utilisez regedit pour spécifier le certificat.

    - Dans regedit, accédez à cette clé de Registre : `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
    Le `[MSSQL13.MSSQLSERVER]` partie varie en fonction du nom de version et d’instance. 

    - La valeur de la clé de **certificat** sur l' **empreinte numérique** du certificat SSL que vous avez importé de la machine virtuelle.

    Il existe plusieurs façons d’obtenir l’empreinte numérique, certains mieux que d’autres. Si vous le copiez dans le composant logiciel enfichable **certificats** dans la console MMC, vous probablement chercher un invisible des caractères [comme décrit dans cet article de la prise en charge](https://support.microsoft.com/kb/2023869/), qui se traduit par une erreur lors de la tentative de connexion. Il existe plusieurs solutions de contournement pour résoudre ce problème. Le plus simple est de reculer et retapez le premier caractère de l’empreinte numérique pour supprimer le caractère de début dans le champ valeur de la clé dans regedit. Également, vous pouvez utiliser un autre outil pour copier l’empreinte numérique.

3. Accorder des autorisations au compte de service. 

    Assurez-vous que le compte de service SQL Server bénéficie des autorisations appropriées sur la clé privée du certificat SSL. Si vous négligez cette étape, SQL Server ne démarre pas. Vous pouvez utiliser le composant logiciel enfichable **certificats** ou **CertUtils** pour cette tâche.

4. Redémarrez le service SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurer la connectivité de SQL Server dans la machine virtuelle

Après avoir configuré la connexion cryptée nécessaire par recherche d’Azure, étapes de configuration supplémentaires sont intrinsèques à SQL Server sur Azure VM. Si vous ne l’avez pas déjà fait, l’étape suivante consiste à terminer la configuration à l’aide de l’un de ces articles :

- Pour un **Gestionnaire de ressources** machine virtuelle, voir [se connecter à un ordinateur de virtuel SQL Server sur Azure à l’aide du Gestionnaire de ressources](../virtual-machines/virtual-machines-windows-sql-connect.md). 

- Pour un **classique** VM, voir [se connecter à un ordinateur de virtuel de SQL Server sur Azure classique](../virtual-machines/virtual-machines-windows-classic-sql-connect.md).

En particulier, consultez la section de chaque article pour « connexion via internet ».

## <a name="configure-the-network-security-group-nsg"></a>Configurer le groupe de sécurité réseau (NSG)

Il n’est pas rare configurer les NSG et le point de terminaison correspondant Azure ou liste de contrôle d’accès (ACL) pour faciliter l’accès à d’autres parties de votre Azure VM. Sans doute que vous avez fait auparavant pour permettre à votre propre logique de l’application pour se connecter à votre ordinateur virtuel de SQL Azure. Il n’est pas différente de connexion recherche d’Azure à votre machine virtuelle de SQL Azure. 

Les liens ci-dessous fournissent des instructions sur la configuration de NSG pour les déploiements d’ordinateurs virtuels. Utilisez les instructions suivantes à l’ACL d’un point de terminaison Azure recherche basée sur son adresse IP.

> [AZURE.NOTE] Pour informations, consultez [ce qu’est un groupe de sécurité réseau ?](../virtual-network/virtual-networks-nsg.md)

- Pour un **Gestionnaire de ressources** machine virtuelle, consultez [comment créer des NSGs pour les déploiements de ARM](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

- Pour un **classique** VM, consultez [comment créer des NSGs pour des déploiements standard](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Adressage IP risquerait de poser quelques difficultés qui sont facilement résolues si vous êtes conscient du problème et des solutions de contournement. Les sections restantes fournissent des recommandations pour la gestion des problèmes liés aux adresses IP dans la liste ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Restreindre l’accès à l’adresse IP du service recherche

Il est vivement recommandé que vous restreigniez l’accès à l’adresse IP de votre service de recherche dans la liste ACL au lieu de faire vos ordinateurs virtuels de SQL Azure ouvert à toute demande de connexion. Vous pouvez facilement trouver l’adresse IP en exécutant la commande ping le nom de domaine complet (par exemple, `<your-search-service-name>.search.windows.net`) de votre service de recherche.

#### <a name="managing-ip-address-fluctuations"></a>Gestion des variations d’adresse IP

Si votre service de recherche n’a qu’une seule unité de recherche (c'est-à-dire, un réplica et une partition), l’adresse IP change lors d’un redémarrage du service de la routine, invalidant une ACL existante avec l’adresse IP de votre service recherche.

Pour éviter l’erreur de connectivité suivantes est d’utiliser plus d’un réplica et une partition dans Azure recherche. Cela augmente le coût, mais il permet également de résoudre le problème d’adresse IP. Recherche d’Azure, les adresses IP ne changent pas lorsque vous avez plus d’une unité de recherche.

Une deuxième approche est pour permettre la connexion et échec, puis reconfigurez les ACL dans le NSG. En moyenne, attendez-vous à des adresses IP pour modifier toutes les semaines. Pour les clients qui achètent l’indexation contrôlé irrégulier, cette approche peut être viable.

Une troisième approche viable (mais pas particulièrement sécurisée) doit spécifier la plage d’adresses IP de la zone Azure où votre service de recherche est mis en service. La liste de plages IP à partir de laquelle les adresses IP publiques sont affectés à des ressources Azure est publiée dans [les plages IP de centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Inclure les adresses IP de portail Azure recherche

Si vous utilisez le portail Azure pour créer un indexeur, logique de portail de recherche d’Azure doit également avoir accès à votre machine virtuelle de SQL Azure pendant la création. Adresses IP portails de recherche Azure sont accessibles en exécutant la commande ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Étapes suivantes

Avec la configuration de, vous pouvez désormais spécifier un SQL Server sur Azure VM en tant que source de données pour un indexeur de recherche d’Azure. Pour plus d’informations, reportez-vous à la section [Connexion de la base de données de SQL Azure à la recherche d’Azure à l’aide d’indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) .
