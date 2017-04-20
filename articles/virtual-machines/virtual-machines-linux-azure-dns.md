<properties 
   pageTitle="Options de résolution de nom DNS pour les machines virtuelles de Linux dans Azure"
   description="Les services DNS de scénarios pour les machines virtuelles de Linux dans Azure IaaS, y compris les noms résolution, serveur hybride DNS et mettre votre propre externe."
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="rclaus" />

# <a name="dns-name-resolution-options-for-linux-vms-in-azure"></a>Options de résolution de nom DNS pour les machines virtuelles de Linux dans Azure

Azure fournit la résolution de noms DNS par défaut pour tous les ordinateurs virtuels contenus dans un seul réseau virtuel. Vous êtes en mesure d’implémenter votre propre solution de résolution de nom DNS en configurant vos propres services DNS sur votre Azure VM hébergé. Les scénarios suivants vous aideront à choisir celui qui fonctionne le mieux pour votre cas particulier.

- [Résolution de noms fournis par Azure](#azure-provided-name-resolution)

- [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) 

Le type de résolution de noms que vous utilisez dépend de comment vos ordinateurs virtuels et les instances de rôles doivent communiquer entre eux.

**Le tableau suivant illustre des scénarios et des solutions de résolution de nom correspondant :**

| **Scénario** | **Solution** | **Suffixe** |
|--------------|--------------|----------|
| Résolution de nom entre les instances de rôles ou d’ordinateurs virtuels situés sur le même réseau virtuel | [Résolution de noms fournis par Azure](#azure-provided-name-resolution)| nom d’hôte ou nom de domaine complet |
| Résolution de nom entre les instances de rôles ou d’ordinateurs virtuels situés dans différents réseaux virtuels | Clients gérés par des serveurs DNS redirection des requêtes entre vnets pour la résolution par Azure (proxy DNS).  consultez [résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server)| Nom de domaine complet uniquement |
| Résolution des noms de service et ordinateur local à partir des instances de rôle ou des ordinateurs virtuels dans Azure | Client-gérer les serveurs DNS (par exemple, sur site contrôleur de domaine, contrôleur de domaine local d’en lecture seule ou un DNS secondaire synchronisée à l’aide de transferts de zone).  Consultez [résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server)|Nom de domaine complet uniquement |
| Résolution de noms d’hôtes Azure à partir d’ordinateurs de sur site | Transférer des requêtes à un serveur de proxy DNS gérés par le client dans le vnet correspondant, le serveur proxy transfère des requêtes vers Azure pour la résolution. Consultez [résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server)| Nom de domaine complet uniquement |
| La recherche DNS inversée pour les adresses IP internes | [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) | n/a |

## <a name="azure-provided-name-resolution"></a>Résolution de noms fournis par Azure

Ainsi que de la résolution des noms DNS publics, Azure fournit la résolution de noms internes pour les ordinateurs virtuels et les instances de rôles qui résident au sein du même réseau virtuel.  Dans les réseaux virtuels basés sur ARM, le suffixe DNS est cohérent sur le réseau virtuel (de sorte que le nom de domaine complet n’est pas nécessaire) et les noms DNS peuvent être affectées pour les ordinateurs virtuels et les cartes réseau. Bien que la résolution de noms fournis par Azure ne nécessite pas de configuration, il n’est pas le choix approprié pour tous les scénarios de déploiement, comme indiqué dans le tableau précédent.

### <a name="features-and-considerations"></a>Fonctions et considérations

**Fonctionnalités :**

- Facilité d’utilisation : aucune configuration n’est requise pour utiliser la résolution de nom Azure fourni.

- Le service de résolution de nom Azure fourni est hautement disponible, l’enregistrement de la nécessité de créer et de gérer des clusters de vos propres serveurs DNS.

- Permet, avec vos propres serveurs DNS pour résoudre les noms d’hôte à la fois sur site et Azure.

- Résolution de noms est fournie entre des ordinateurs virtuels dans des réseaux virtuels sans avoir besoin du nom de domaine complet. 

- Vous pouvez utiliser des noms d’hôtes qui décrivent le mieux vos déploiements, plutôt que d’utiliser les noms générés automatiquement.

**Considérations relatives à :**

- Le suffixe DNS d’Azure créée ne peut pas être modifié.

- Vous ne pouvez pas enregistrer manuellement vos propres enregistrements.

- WINS et NetBIOS ne sont pas pris en charge.

- Nom d’hôte doit être compatible avec le DNS (ils doivent utiliser uniquement 0-9, a-z et '-' et ne peut pas commencer ou se terminer par un '-'. Reportez-vous à la section 2 de la RFC 3696.)

- Le trafic des requêtes DNS est limité pour chaque machine virtuelle. Cela ne devrait pas affecter la plupart des applications.  Si la limitation de requêtes est respectée, assurez-vous que la mise en cache côté client est activée.  Pour plus d’informations, reportez-vous à [tirer le meilleur parti d’une résolution de nom Azure fourni](#Getting-the-most-from-Azure-provided-name-resolution).


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obtenir le maximum de la résolution de noms fournis par Azure
**La mise en cache côté client :**

Pas chaque requête DNS est envoyée sur le réseau.  Mise en cache côté client permet de réduire la latence et améliorer la résistance aux blips de réseau par la résolution des requêtes DNS périodiques à partir d’un cache local.  Enregistrements DNS contiennent un Time-To-Live (TTL) qui permet le cache stocker l’enregistrement pour aussi longtemps que possible sans affecter la fraîcheur enregistrement.  De ce fait, la mise en cache côté client est adapté à la plupart des situations.

Certaines distros de Linux n’incluent pas la mise en cache par défaut.  Il est recommandé de que vous ajoutez à chaque VM Linux (après vérification qu’il n’est pas un cache local déjà).

Il existe plusieurs DNS cache packages différents disponibles, par exemple dnsmasq, voici la procédure d’installation de dnsmasq sur la distros la plus courante :

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

> [AZURE.NOTE]: Le package « dnsmasq » n’est qu’un des nombreux caches DNS disponibles pour Linux.  Avant de l’utiliser, vérifiez son aptitude à vos besoins particuliers, et que le cache n’est pas installé.

**Tentatives de côté client :**

DNS est principalement un protocole UDP.  Comme le protocole UDP ne garantit pas la remise des messages, logique des nouvelles tentatives est géré dans le protocole DNS lui-même.  Chaque client DNS (système d’exploitation) peut présenter la logique des nouvelles tentatives différents en fonction de la préférence de créateurs :

 - Systèmes d’exploitation Windows puis réessayez une deuxième, puis à nouveau après un autre deux, quatre et un autre quatre secondes. 
 - Les tentatives de configuration par défaut Linux après cinq secondes.  Vous devez modifier cette option pour recommencer cinq fois à intervalles d’une seconde.  

Pour vérifier les paramètres en cours sur une VM Linux, « cat /etc/resolv.conf » et examinez la ligne « options », par exemple :

    options timeout:1 attempts:5

Le fichier resolv.conf est généré automatiquement et ne doit pas être modifié.  Les étapes spécifiques permettant d’ajouter la ligne 'options' varient par distro :

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
Il existe plusieurs situations où vos besoins de résolution de nom peuvent aller au-delà de fourni par Azure, par exemple lorsque vous avez besoin la résolution DNS entre des réseaux virtuels (vnets).  Pour couvrir ce scénario, Azure offre la possibilité d’utiliser vos propres serveurs DNS.  

Serveurs DNS au sein d’un réseau virtuel peuvent transférer des requêtes DNS pour les programmes de résolution d’Azure récursive pour résoudre les noms d’hôtes au sein de ce réseau virtuel.  Par exemple, un serveur DNS exécutant dans Azure peut répondre aux requêtes DNS pour ses propres fichiers de zone DNS et transférer toutes les autres requêtes vers Azure.  Ainsi, les ordinateurs virtuels Voir les deux vos entrées dans les fichiers de zone et Azure fournie de noms d’hôtes (via le redirecteur).  Accès aux programmes de résolution récursive d’Azure est fourni via l’IP virtuel 168.63.129.16.

Redirection DNS Active la résolution DNS inter-vnet également et permet à vos ordinateurs sur site résoudre les noms d’hôtes fournis par Azure.  Pour résoudre le nom de l’hôte d’un ordinateur virtuel, le serveur DNS machine virtuelle doit résider sur le même réseau virtuel et être configuré pour les requêtes de nom d’hôte directe vers Azure.  Comme le suffixe DNS est différent dans chaque vnet, vous pouvez utiliser les règles de la redirection conditionnelle pour envoyer des requêtes DNS à la vnet approprié pour la résolution.  L’image suivante montre deux vnets et un réseau sur site effectuant la résolution DNS inter-vnet à l’aide de cette méthode :

![Inter-vnet DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Lorsque vous utilisez la résolution de noms fournis par Azure, le suffixe DNS interne est fourni pour chaque machine virtuelle à l’aide de DHCP.  Lorsque vous utilisez votre propre solution de résolution de nom, ce suffixe n’est pas fourni aux ordinateurs virtuels parce qu’il interfère avec d’autres architectures DNS.  Pour faire référence aux ordinateurs par le nom de domaine complet, ou pour configurer le suffixe sur vos ordinateurs virtuels, le suffixe peut être déterminé à l’aide de PowerShell ou l’API :

-  Pour la gestion des ressources Azure managed vnets, le suffixe est disponible via les ressources de la [carte d’interface réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx) ou vous pouvez exécuter la commande `azure network public-ip show <resource group> <pip name>` pour afficher les détails de votre adresse IP publique, y compris le nom de domaine complet de la carte réseau.    


Si la redirection des requêtes vers Azure ne suffit pas, vous devez fournir votre propre solution DNS.  Votre solution DNS doit :

-  Fournir la résolution de nom d’hôte approprié, par exemple via [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md).  Notez que si vous utilisez le DDNS, vous devrez peut-être désactiver le nettoyage des enregistrements DNS que les baux DHCP de d’Azure est très longs et nettoyage peut supprimer les enregistrements DNS prématurément. 
-  Fournissent une résolution récursive appropriée pour autoriser la résolution de noms de domaine externes.
-  Être accessible (TCP et UDP sur le port 53) à partir des clients qu’il dessert et être en mesure d’accéder à internet.
-  Être protégé contre tout accès à partir d’internet, pour atténuer les menaces posées par des agents extérieurs.

> [AZURE.NOTE] Pour des performances optimales, lorsque vous utilisez des ordinateurs virtuels d’Azure comme serveurs DNS, IPv6 doit être désactivé, et une [Adresse IP publique d’au niveau de l’Instance](../virtual-network/virtual-networks-instance-level-public-ip.md) doit être affecté à chaque machine virtuelle du serveur DNS.  

