<properties 
   pageTitle="Résolution pour les machines virtuelles et d’Instances de rôle"
   description="Scénarios de résolution de noms pour Azure IaaS, solutions hybrides, entre les services en nuage différent, Active Directory et à l’aide de votre propre serveur DNS "
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="telmos" />

# <a name="name-resolution-for-vms-and-role-instances"></a>Résolution de noms pour les ordinateurs virtuels et des Instances de rôle

Selon l’utilisation Azure pour héberger IaaS, PaaS et hybrides des solutions, vous devrez peut-être autoriser les ordinateurs virtuels et les instances de rôles que vous créez pour communiquer entre eux. Bien que cette communication peut être effectuée à l’aide d’adresses IP, il est beaucoup plus simple d’utiliser des noms qui peuvent être faciles à retenir et ne changent pas. 

Instances de rôles et de machines virtuelles hébergées dans Azure nécessaire de résoudre des noms de domaine pour les adresses IP internes, ils peuvent utiliser une des deux méthodes :

- [Résolution de noms fournis par Azure](#azure-provided-name-resolution)

- [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) (qui peut rediriger les requêtes vers les serveurs DNS fournis par Azure) 

Le type de résolution de noms que vous utilisez dépend de comment vos ordinateurs virtuels et les instances de rôles doivent communiquer entre eux.

**Le tableau suivant illustre des scénarios et des solutions de résolution de nom correspondant :**

| **Scénario** | **Solution** | **Suffixe** |
|--------------|--------------|----------|
| Résolution de nom entre les instances de rôles ou d’ordinateurs virtuels situés dans le même service de cloud ou d’un réseau virtuel | [Résolution de noms fournis par Azure](#azure-provided-name-resolution)| nom d’hôte ou nom de domaine complet |
| Résolution de nom entre les instances de rôles ou d’ordinateurs virtuels situés dans différents réseaux virtuels | Clients gérés par des serveurs DNS redirection des requêtes entre vnets pour la résolution par Azure (proxy DNS).  consultez [résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server)| Nom de domaine complet uniquement |
| Résolution des noms de service et ordinateur local à partir des instances de rôle ou des ordinateurs virtuels dans Azure | Client-gérer les serveurs DNS (sur site par exemple, domaine, contrôleur de domaine local d’en lecture seule ou un DNS secondaire synchronisée à l’aide de transferts de zone).  Consultez [résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server)|Nom de domaine complet uniquement |
| Résolution de noms d’hôtes Azure à partir d’ordinateurs de sur site | Transférer des requêtes à un serveur de proxy DNS gérés par le client dans le vnet correspondant, le serveur proxy transfère des requêtes vers Azure pour la résolution. Consultez [résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server)| Nom de domaine complet uniquement |
| La recherche DNS inversée pour les adresses IP internes | [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) | n/a |
| Résolution de noms entre les ordinateurs virtuels ou les instances de rôles situés dans des services de nuage différent, pas dans un réseau virtuel| Ne s’applique pas. La connectivité entre les ordinateurs virtuels et des instances de rôle des services de nuage différent n’est pas pris en charge à l’extérieur d’un réseau virtuel.| n/a |



## <a name="azure-provided-name-resolution"></a>Résolution de noms fournis par Azure

Ainsi que de la résolution des noms DNS publics, Azure fournit la résolution de noms internes pour les ordinateurs virtuels et les instances de rôles qui résident dans le même réseau virtuel ou d’un service en nuage.  VM/instances dans un service cloud partagent le même suffixe DNS (de sorte que le nom d’hôte seul suffit) mais dans un nuage différent de classique de réseaux virtuels services ont des suffixes DNS différents afin que le nom de domaine complet est nécessaire pour résoudre les noms entre services de nuage différent.  Dans les réseaux virtuels dans le modèle de déploiement du Gestionnaire de ressources, le suffixe DNS est cohérent sur le réseau virtuel (de sorte que le nom de domaine complet n’est pas nécessaire) et les noms DNS peuvent être affectées pour les ordinateurs virtuels et les cartes réseau. Bien que la résolution de noms fournis par Azure ne nécessite pas de configuration, il n’est pas le choix approprié pour tous les scénarios de déploiement, comme indiqué dans le tableau ci-dessus.

> [AZURE.NOTE] Pour les rôles web et worker, vous pouvez également accéder les adresses IP internes des instances de rôle basées sur le rôle nom et numéro d’instance à l’aide de l’API REST de Azure Service Management. Pour plus d’informations, consultez [Référence de l’API Service gestion reste](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### <a name="features-and-considerations"></a>Fonctions et considérations

**Fonctionnalités :**

- Facilité d’utilisation : aucune configuration n’est requise pour utiliser la résolution de noms fournis par Azure.

- Le service de résolution de nom Azure fourni est hautement disponible, l’enregistrement de la nécessité de créer et de gérer des clusters de vos propres serveurs DNS.

- Peut être utilisé conjointement avec vos propres serveurs DNS pour résoudre les noms d’hôte à la fois sur site et Azure.

- Résolution de noms est fournie entre instances/ordinateurs virtuels de rôle au sein du même service de cloud sans recourir à un nom de domaine complet.

- Résolution de noms est fournie entre des ordinateurs virtuels dans des réseaux virtuels qui utilisent le modèle de déploiement Gestionnaire de ressources, sans avoir besoin du nom de domaine complet. Réseaux virtuels dans le modèle de déploiement classique nécessitent le nom de domaine complet lors de la résolution de noms dans les services de nuage différent. 

- Vous pouvez utiliser des noms d’hôtes qui décrivent le mieux vos déploiements, plutôt que d’utiliser les noms générés automatiquement.

**Considérations relatives à :**

- Le suffixe DNS d’Azure créée ne peut pas être modifié.

- Vous ne pouvez pas enregistrer manuellement vos propres enregistrements.

- WINS et NetBIOS ne sont pas pris en charge. (Vous ne pouvez pas voir vos ordinateurs virtuels dans l’Explorateur Windows.)

- Nom d’hôte doit être compatible avec le DNS (ils doivent utiliser uniquement 0-9, a-z et '-' et ne peut pas commencer ou se terminer par un '-'. Reportez-vous à la section 2 de la RFC 3696.)

- Le trafic des requêtes DNS est limité pour chaque machine virtuelle. Cela ne devrait pas affecter la plupart des applications.  Si la limitation de requêtes est respectée, assurez-vous que la mise en cache côté client est activée.  Pour plus d’informations, reportez-vous à [tirer le meilleur parti d’une résolution de nom Azure fourni](#Getting-the-most-from-Azure-provided-name-resolution).

- Uniquement les ordinateurs virtuels dans les services en 180 nuage premier sont enregistrés pour chaque réseau virtuel dans un modèle de déploiement classique. Cela ne s’applique pas à des réseaux virtuels dans le Gestionnaire de ressources des modèles de déploiement.


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obtenir le maximum de la résolution de noms fournis par Azure
**La mise en cache côté client :**

Pas toutes les requêtes DNS doivent être envoyé sur le réseau.  Mise en cache côté client permet de réduire la latence et améliorer la résistance aux blips de réseau par la résolution des requêtes DNS périodiques à partir d’un cache local.  Enregistrements DNS contiennent un Time-To-Live (TTL) qui permet le cache stocker l’enregistrement pour aussi longtemps que possible sans aucune incidence sur la fraîcheur de l’enregistrement, afin que la mise en cache côté client est adaptée à la plupart des situations.

La valeur par défaut du Client DNS Windows possède un cache DNS intégré.  Certaines solutions Linux distros n’incluent pas la mise en cache par défaut, il est recommandé qu’un soit ajoutée à chaque VM Linux (après vérification qu’il n’est pas un cache local déjà).

Il existe un certain nombre de DNS mise en cache des packages différents disponibles, par exemple, dnsmasq, voici la procédure d’installation de dnsmasq sur la distros la plus courante :

- **Ubuntu (utilise resolvconf)**:
    - Installez le package dnsmasq (« sudo apt-get install dnsmasq »).
- **SUSE (utilise netconf)**:
    - Installez le package dnsmasq (« sudo zypper installation dnsmasq ») 
    - Activer le service dnsmasq (« systemctl activer dnsmasq.service ») 
    - Démarrer le service dnsmasq (« systemctl démarrer dnsmasq.service ») 
    - modifier « / etc/sysconfig/network/config » et modifier NETCONFIG_DNS_FORWARDER = " » à « dnsmasq »
    - mise à jour resolv.conf (« netconfig mise à jour ») pour définir le cache de la résolution DNS locale
- **OpenLogic (utilise NetworkManager)**:
    - Installez le package dnsmasq (« sudo yum installation dnsmasq »)
    - Activer le service dnsmasq (« systemctl activer dnsmasq.service »)
    - Démarrer le service dnsmasq (« systemctl démarrer dnsmasq.service »)
    - ajouter « ajouter des serveurs de nom de domaine 127.0.0.1 ; » à « /etc/dhclient-eth0.conf »
    - Redémarrez le service de réseau (« service réseau redémarrage ») pour définir le cache de la résolution DNS locale

> [AZURE.NOTE] Le package « dnsmasq » n’est qu’un des nombreux caches DNS disponibles pour Linux.  Avant de l’utiliser, vérifiez son aptitude à vos besoins particuliers, et que le cache n’est pas installé.

**Tentatives de côté client :**

DNS est principalement un protocole UDP.  Comme le protocole UDP ne garantit pas la remise des messages, logique des nouvelles tentatives est géré dans le protocole DNS lui-même.  Chaque client DNS (système d’exploitation) peut présenter la logique des nouvelles tentatives différents en fonction de la préférence de créateurs :

 - Systèmes d’exploitation Windows puis réessayez après 1 deuxième, puis à nouveau un autre 2, 4 et l’autre 4 secondes. 
 - Les tentatives de configuration par défaut Linux après 5 secondes.  Il est recommandé de modifier cette tentative 5 fois intervalles de 1 seconde.  

Pour vérifier les paramètres en cours sur une VM Linux, « cat /etc/resolv.conf » et examinez la ligne « options », par exemple :

    options timeout:1 attempts:5

Le fichier resolv.conf est généralement généré automatiquement et ne doit pas être modifié.  Les étapes spécifiques permettant d’ajouter la ligne 'options' varient par distro :

- **Ubuntu** (utilise resolvconf) :
    - Ajoutez la ligne options de ' / etc/resolveconf/resolv.conf.d/head' 
    - Exécutez 'resolvconf -u' mise à jour
- **SUSE** (utilise netconf) :
    - Ajouter « tentatives de timeout:1 : 5 » à la NETCONFIG_DNS_RESOLVER_OPTIONS = " » paramètre dans '/ etc/sysconfig/network/config' 
    - Exécutez 'netconfig la mise à jour' pour mettre à jour
- **OpenLogic** (utilise NetworkManager) :
    - Ajouter « écho « options timeout:1 tentatives : 5 » » à ' / etc/NetworkManager/dispatcher.d/11-dhclient' 
    - Exécutez « redémarrage du service réseau » pour mettre à jour

## <a name="name-resolution-using-your-own-dns-server"></a>Résolution de noms à l’aide de votre propre serveur DNS
Il existe un certain nombre de situations où vos besoins de résolution de nom peuvent aller au-delà des fonctionnalités fournies par Azure, par exemple lorsque l’utilisation domaines Active Directory, ou lorsque vous avez besoin de la résolution DNS entre des réseaux virtuels (vnets).  Pour couvrir ces scénarios, Azure offre la possibilité d’utiliser vos propres serveurs DNS.  

Serveurs DNS au sein d’un réseau virtuel peuvent transférer des requêtes DNS pour les programmes de résolution d’Azure récursive pour résoudre les noms d’hôtes au sein de ce réseau virtuel.  Par exemple, un contrôleur de domaine (DC) en cours d’exécution dans Azure peut répondre aux requêtes DNS pour ses domaines et transférer toutes les autres requêtes vers Azure.  Ainsi, les ordinateurs virtuels voir vos ressources sur site (via le contrôleur de domaine) et les noms d’hôtes fournis par Azure (via le redirecteur).  Accès aux programmes de résolution récursive d’Azure est fourni via l’IP virtuel 168.63.129.16.

Redirection DNS Active la résolution DNS inter-vnet également et permet à vos ordinateurs sur site résoudre les noms d’hôtes fournis par Azure.  Afin de résoudre le nom de l’hôte d’un ordinateur virtuel, le serveur DNS machine virtuelle doit résider sur le même réseau virtuel et être configuré pour les requêtes de nom d’hôte directe vers Azure.  Comme le suffixe DNS est différent dans chaque vnet, vous pouvez utiliser les règles de la redirection conditionnelle pour envoyer des requêtes DNS à la vnet approprié pour la résolution.  L’image suivante montre deux vnets et un réseau sur site effectuant la résolution DNS inter-vnet à l’aide de cette méthode.  Un redirecteur DNS d’exemple est disponible dans la [Galerie de modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) et [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Inter-vnet DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Lorsque vous utilisez la résolution de nom Azure fourni, un suffixe DNS interne (*. internal.cloudapp.net) est fourni pour chaque machine virtuelle à l’aide de DHCP.  Cela permet la résolution de nom d’hôte comme les enregistrements de nom d’hôte dans la zone internal.cloudapp.net.  Lorsque vous utilisez votre propre solution de résolution de nom, le suffixe HMO n’est pas fourni aux ordinateurs virtuels parce qu’il interfère avec d’autres architectures DNS (comme les scénarios à un domaine).  Au lieu de cela, nous fournissons un espace réservé non fonctionnement (reddog.microsoft.com).  

Si nécessaire, le suffixe DNS interne peut être déterminé à l’aide de PowerShell ou l’API :

-  Pour les réseaux virtuels dans les modèles de déploiement Gestionnaire de ressources, le suffixe est disponible via les ressources de la [carte d’interface réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx) ou via l’applet de commande [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) .    
-  Dans les modèles de déploiement classique, le suffixe est disponible via l’appel de [l’API de déploiement obtenir](https://msdn.microsoft.com/library/azure/ee460804.aspx) ou la [Get-AzureVM-Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx) applet de commande.


Si la redirection des requêtes vers Azure ne convenance pas, vous devrez fournir votre propre solution DNS.  Votre solution DNS devra :

-  Fournir la résolution de nom d’hôte approprié, par exemple via [DDNS](virtual-networks-name-resolution-ddns.md).  Notez que si vous utilisez le DDNS, vous devrez peut-être désactiver le nettoyage des enregistrements DNS que les baux DHCP de d’Azure est très longs et nettoyage peut supprimer les enregistrements DNS prématurément. 
-  Fournissent une résolution récursive appropriée pour autoriser la résolution de noms de domaine externes.
-  Être accessible (TCP et UDP sur le port 53) à partir des clients qu’il dessert et être en mesure d’accéder à internet.
-  Être protégé contre tout accès à partir d’internet, pour atténuer les menaces posées par des agents extérieurs.

> [AZURE.NOTE] Pour des performances optimales, lorsque vous utilisez des ordinateurs virtuels d’Azure comme serveurs DNS, IPv6 doit être désactivé, et une [Adresse IP publique d’au niveau de l’Instance](virtual-networks-instance-level-public-ip.md) doit être affecté à chaque machine virtuelle du serveur DNS.  Si vous choisissez d’utiliser Windows Server comme votre serveur DNS, [cet article](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) fournit des optimisations et analyse des performances supplémentaires.


### <a name="specifying-dns-servers"></a>Spécification de serveurs DNS

Lorsque vous utilisez vos propres serveurs DNS, Azure offre la possibilité de spécifier plusieurs serveurs DNS par le réseau virtuel ou par l’interface réseau (responsable de ressources) ou de cloud service (classique).  Les serveurs DNS spécifiés pour une interface réseau/service de cloud Obtient la priorité sur ceux qui sont spécifiés pour le réseau virtuel.

> [AZURE.NOTE] Les propriétés de connexion réseau, telles que le serveur DNS IPs, ne doivent pas être modifiés directement dans des ordinateurs virtuels de Windows qu’ils peuvent obtenir effacés durant service correction lorsque la carte réseau virtuelle est remplacée. 


Lorsque vous utilisez le modèle de déploiement du Gestionnaire de ressources, vous peuvent spécifier les serveurs DNS dans le portail, API/modèles ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [carte d’interface réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx)) ou PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [carte d’interface réseau](https://msdn.microsoft.com/library/mt619370.aspx)).

Lorsque vous utilisez le modèle de déploiement classique, les serveurs DNS pour le réseau virtuel peuvent être spécifiés dans le portail ou [le fichier de *Configuration de réseau* ](https://msdn.microsoft.com/library/azure/jj157100).  Pour les services en nuage, les serveurs DNS sont spécifiés via [le fichier de *Configuration de Service* ](https://msdn.microsoft.com/library/azure/ee758710) ou dans PowerShell ([New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [AZURE.NOTE] Si vous modifiez les paramètres DNS pour un ordinateur de réseau/virtuel qui est déjà déployée, vous devez redémarrer chaque VM concerné pour que les modifications prennent effet.


## <a name="next-steps"></a>Étapes suivantes

Modèle de déploiement du Gestionnaire de ressources :

- [Créer ou mettre à jour un réseau virtuel](https://msdn.microsoft.com/library/azure/mt163661.aspx)
- [Créer ou mettre à jour d’une carte d’interface réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx)
- [Nouvelle-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
- [Nouvelle-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

 
Modèle de déploiement classique :

- [Schéma de Configuration de Service Azure](https://msdn.microsoft.com/library/azure/ee758710)
- [Schéma de Configuration de réseau virtuel](https://msdn.microsoft.com/library/azure/jj157100)
- [Configurer un réseau virtuel à l’aide d’un fichier de Configuration de réseau](virtual-networks-using-network-configuration-file.md) 

