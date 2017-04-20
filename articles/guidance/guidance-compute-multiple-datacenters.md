<properties
   pageTitle="Exécute les ordinateurs virtuels de Windows dans plusieurs régions pour une haute disponibilité | Architecture de référence | Microsoft Azure"
   description="Comment déployer des ordinateurs virtuels dans plusieurs régions sur Azure pour la haute disponibilité et la résilience."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/21/2016"
   ms.author="mwasson"/>

# <a name="running-windows-vms-in-multiple-regions-for-high-availability"></a>Exécute les ordinateurs virtuels de Windows dans plusieurs régions pour une haute disponibilité

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Exécute les ordinateurs virtuels de Linux dans plusieurs régions pour une haute disponibilité](guidance-compute-multiple-datacenters-linux.md)
- [Exécute les ordinateurs virtuels de Windows dans plusieurs régions pour une haute disponibilité](guidance-compute-multiple-datacenters.md)

Dans cet article, nous vous recommandons d’un ensemble de pratiques, d’exécuter des machines virtuelles de Windows (VMs) dans plusieurs régions d’Azure, pour obtenir une disponibilité et une infrastructure de reprise après sinistre efficace.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource groups] et classique. Cet article utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Une architecture de plusieurs région peut fournir une disponibilité plus élevée que le déploiement d’une seule région. Si une panne régionale affecte la zone principale, vous pouvez utiliser le [Gestionnaire de trafic] [ traffic-manager] pour basculer vers la zone secondaire. Cette architecture permet également en cas de défaillance d’un sous-système individuel de l’application. 

Il existe plusieurs approches pour atteindre une disponibilité élevée au sein de centres de données :

- Actif/passif en veille à chaud. Du trafic se fait en une seule région, tout en l’autre attend en veille. Ordinateurs virtuels dans la zone secondaire sont alloués et en cours d’exécution à tout moment.

- Actif/passif avec l’opération de secours. Le même, mais les ordinateurs virtuels dans la zone secondaire ne sont pas alloués tant que nécessaire pour le basculement. Cette approche coûte moins cher à exécuter, mais aura généralement plus de l’indisponibilité lors d’une défaillance.

- Actif/actif. Les deux régions sont actifs et requêtes sont réparties entre eux. Si un centre de données devient indisponible, il est retiré de rotation. 

Cette architecture se concentre sur actif/passif en veille à chaud, à l’aide du Gestionnaire de trafic pour le basculement. Notez que vous pouvez déployer un petit nombre d’ordinateurs virtuels de secours à chaud et puis évoluer en fonction des besoins.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant s’appuie sur l’architecture de la [fiabilité d’ajout à une architecture à N niveaux dans Azure](guidance-compute-n-tier-vm.md).

> Un document Visio qui contient ce diagramme d’architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur « Compute - page de région (Windows) multiples.

[! [0]][0]

- **Les zones principales et secondaires**. Cette architecture utilise deux régions pour garantir une disponibilité supérieure. L’une est la principale région. Pendant des opérations normales, le trafic réseau est routé vers la zone primaire. Mais si qui n’est plus disponible, le trafic est acheminé sur la zone secondaire.

- ** [Azure Traffic Manager] [ traffic-manager] ** achemine les demandes entrantes vers la région principale. Si cette zone n’est plus disponible, Gestionnaire de trafic bascule sur la zone secondaire. Pour plus d’informations, consultez la section [Configuration d’un gestionnaire de trafic](#configuring-traffic-manager).

- **Groupes de ressources**. Créer des [groupes de ressources] distincts[ resource groups] pour la zone principale, la zone secondaire et pour le Gestionnaire de trafic. Cela vous donne la possibilité de gérer chaque région comme un ensemble unique de ressources. Par exemple, vous pourriez redéployer une seule région, sans arrêter autre. [Lier les groupes de ressources][resource-group-links], de sorte que vous pouvez exécuter une requête pour répertorier toutes les ressources de l’application.

- **VNets**. Créer un VNet distinct pour chaque région. Vérifiez que les espaces d’adressage ne se chevauchent pas. 

- **SQL Server toujours sur le groupe de disponibilité**. Si vous utilisez SQL Server, nous vous recommandons de [SQL toujours sur Availabilty groupes] [ sql-always-on] pour une haute disponibilité. Créer un groupe de disponibilité unique qui contient les instances de SQL Server dans les deux régions. Pour plus d’informations, consultez la section [configuration du groupe de disponibilité de SQL Server Always On](#configuring-the-sql-server-alwayson-availability-group ).

> [AZURE.NOTE] Envisagez également de [Base de données de SQL Azure][azure-sql-db], qui fournit une base de données relationnelle comme un service en nuage. La base de données SQL vous n’avez pas besoin de configurer un groupe de disponibilité ou de gérer le basculement.  

- **Les passerelles VPN**: créer une [passerelle VPN] [ vpn-gateway] dans chaque VNet et configurer une [connexion de VNet à VNet][vnet-to-vnet], pour permettre le trafic réseau entre les deux VNets. Ceci est nécessaire pour le SQL toujours sur le groupe de disponibilité.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être configuré de différentes façons. Nous avons fourni un modèle Azure Resource Manager pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence suivre ces recommandations, à moins d’avoir une recommandation ne correspondant pas à un besoin spécifique.

### <a name="regional-pairing"></a>Appariement régionaux

Chaque région Azure est associée à une autre région au sein de la même région. En règle générale, choisissez régions dans la même paire régionale (par exemple, US orientale 2 et nous Central). Cela présente les avantages suivants :

- S’il existe une panne large, récupération d’au moins une région de chaque paire est une priorité.

- Mises à jour planifiées système Azure sont présentées aux régions appariées séquentiellement, afin de réduire les temps morts possibles.

- Paires de résident dans le même emplacement géographique, pour répondre aux exigences en matière de délégation de compétences de données.

Toutefois, assurez-vous que les deux régions prennent en charge tous les services nécessaires à votre application Azure (voir [Services par région][services-by-region]). Pour plus d’informations sur les paires régionaux, consultez [Business continuité d’activité et reprise après sinistre (BCDR) : régions associés d’Azure][regional-pairs].

### <a name="traffic-manager-configuration"></a>Configuration du Gestionnaire de trafic

Considérez les points suivants lors de la configuration du trafic manager pour votre scénario :

- **Le routage.** Traffic Manager prend en charge plusieurs [algorithmes de routage][tm-routing]. Pour le scénario décrit dans cet article, utiliser la _priorité de_ routage (anciennement appelé routage de _basculement_ ). Avec ce paramètre, Traffic Manager envoie toutes les demandes à la zone principale, à moins que la zone primaire deviendrait inaccessible. À ce stade, il bascule automatiquement sur la zone secondaire. Reportez-vous à la section [méthode de routage de configurer le basculement de][tm-configure-failover].

- **Sonde de santé.** Traffic Manager utilise une [sonde] de HTTP (ou HTTPS)[ tm-monitoring] pour surveiller la disponibilité de chaque région. La sonde vérifie une réponse HTTP 200 pour un chemin d’accès d’URL spécifié. Pour obtenir les meilleurs résultats, créez un point de terminaison qui signale l’état de santé global de l’application et utiliser ce point de terminaison de la sonde de santé. Dans le cas contraire, la sonde peut signaler un point de terminaison « sain » lorsque les parties critiques de l’application sont réellement une défaillance. Pour plus d’informations, consultez [Modèle de surveillance de point de terminaison de santé][health-endpoint-monitoring-pattern].   

Lors de la bascule sur le Traffic Manager, il existe une période de temps lorsque le client ne peut pas accéder à l’application, qui peut être de plusieurs minutes. Deux facteurs affectent la durée totale :

- La sonde de santé doit détecter que le centre de données principal est inaccessible.

- Les serveurs DNS doivent mettre à jour les enregistrements DNS mis en cache pour l’adresse IP, dont dépend la DNS time-to-live (TTL). La durée de vie par défaut est 300 secondes (5 minutes), mais vous pouvez configurer cette valeur lorsque vous créez le profil du Gestionnaire de trafic.

Pour plus d’informations, consultez [à propos du contrôle de Traffic Manager][tm-monitoring]. 

Si la bascule de Traffic Manager, nous vous recommandons d’effectuer un retour arrière manuel, plutôt que d’effectuer automatiquement une reprise. Vérifiez que tous les sous-systèmes d’application sont tout d’abord en bon état. Dans le cas contraire, vous pouvez créer une situation où l’application retourne en arrière entre les centres de données.

Par défaut, le Gestionnaire de trafic est automatiquement restauré. Pour éviter ce problème, réduisez manuellement la priorité de la zone primaire après un événement de basculement. Par exemple, supposons que la zone primaire est la priorité 1 et l’image secondaire est de priorité 2. Après un basculement, définissez la région principale à la priorité 3, afin d’empêcher la restauration automatique. Lorsque vous êtes prêt à revenir, mettez à jour la priorité 1.

La commande CLI d’Azure suivante met à jour la priorité :

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

Une autre façon d’éviter la bascule est de désactiver temporairement le point de terminaison :

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```

Selon la cause d’un basculement, vous devrez peut-être redploy les ressources au sein d’une région. Avant l’échec précédent, effectuez un test de disponibilité opérationnelle. Le test doit vérifier les éléments tels que :

- Ordinateurs virtuels sont configurés correctement. (Tous les logiciels requis sont installés, IIS est en cours d’exécution, etc.).

- Sous-systèmes d’application sont en bon état. 

- Tests de fonctionnement. (Par exemple, la couche de base de données est accessible à partir de la couche web.)

### <a name="sql-server-always-on-configuration"></a>Configuration de SQL Server Always On

Groupes de disponibilité de SQL Server Always On besoin d’un contrôleur de domaine. Tous les nœuds dans le groupe de disponibilité doivent être dans le même domaine Active Directory. Les points suivants fournissent des instructions concernant la configuration d’un groupe de disponibilité de SQL Server Always On :

- Au minimum, placez deux contrôleurs de domaine dans chaque région. 

- Donner une adresse IP statique à chaque contrôleur de domaine.

- Créer une connexion VNet à VNet pour activer la communication entre les VNets.

- Pour chaque VNet, ajoutez les adresses IP des contrôleurs de domaine (les deux régions) à la liste des serveurs DNS. Vous pouvez utiliser la commande CLI suivante. Plus d’informations, voir [Gérer les serveurs utilisés par un réseau virtuel (VNet)][vnet-dns].

```bat
azure network vnet set --resource-group dc01-rg --name dc01-vnet --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
```

- Créer un [Cluster de basculement Windows Server] [ wsfc] cluster (WSFC) qui contient les instances de SQL Server dans les deux régions. 

- Créer un groupe de disponibilité SQL Server Always On incluant les instances de SQL Server dans les régions principales et secondaires. Pour les étapes, reportez-vous à la section [Extension toujours sur le groupe de disponibilité pour Azure centre de données distant (PowerShell)](https://blogs.msdn.microsoft.com/sqlcat/2014/09/22/extending-alwayson-availability-group-to-remote-azure-datacenter-powershell/) . 

- Placez le réplica principal dans la région principale.

- Placez un ou plusieurs réplicas secondaires dans la région principale. Configurer pour utiliser la validation synchrone avec basculement automatique.

- Placez un ou plusieurs réplicas secondaires dans la zone secondaire. Configurer pour utiliser une validation *asynchrone* , pour des raisons de performances. (Dans le cas contraire, toutes les transactions SQL doivent attendre lors d’un aller-retour sur le réseau à la zone secondaire.) 

> [AZURE.NOTE] Les réplicas de validation asynchrone ne supportent pas le basculement automatique. 

Pour plus d’informations, consultez [Exécution des machines virtuelles Windows d’une architecture à N niveaux sur Azure](guidance-compute-n-tier-vm.md#SQL-AlwaysOn-Availability-Group).

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Avec une application N-tier complexe, vous devrez pas répliquer l’ensemble de l’application dans la zone secondaire. Au lieu de cela, vous pouvez uniquement répliquer un sous-système essentiel qui est nécessaire pour prendre en charge de la continuité d’activité.

Traffic Manager est un point de défaillance dans le système. Si le service échoue, les clients ne peuvent pas accéder à votre application pendant l’interruption. Passez en revue le [Contrat SLA de Traffic Manager][tm-sla]et de déterminer si l’utilisation du Gestionnaire de trafic seul répond à vos besoins de haute disponibilité. Si ce n’est pas le cas, envisagez d’ajouter une autre solution de gestion de trafic comme un retour arrière. Si le service Gestionnaire de trafic Azure échoue, modifiez vos enregistrements CNAME dans DNS pour pointer vers l’autre service de gestion du trafic. (Cette étape doit être effectuée manuellement, et votre application n’est pas disponible jusqu'à ce que les modifications DNS sont propagées). 

Pour le cluster SQL Server, il existe deux scénarios de basculement :

1. Tous les réplicas dans la région principale SQL échouent. Par exemple, cela peut se produire suite à une panne régionale. Dans ce cas, vous devez basculer manuellement sur le groupe de disponibilité SQL, même si le Gestionnaire de trafic bascule automatiquement sur le front-end. Suivez les étapes dans [effectuer un basculement manuel forcé d’un groupe de disponibilité de SQL Server](https://msdn.microsoft.com/library/ff877957.aspx), qui explique comment effectuer un basculement forcé à l’aide de PowerShell, Transact-SQL ou SQL Server Management Studio dans SQL Server 2016. 

    > [AZURE.WARNING] Avec basculement forcé, il existe un risque de perte de données. Une fois que la zone primaire est en ligne, prendre un instantané de la base de données et utiliser [tablediff] pour rechercher les différences.

2. Gestionnaire de trafic bascule sur la zone secondaire, mais que le réplica SQL principal est toujours disponible. Par exemple, la couche frontale peut échouer, sans affecter les ordinateurs virtuels de SQL. Dans ce cas, le trafic Internet est routé vers la zone secondaire, et cette région peut toujours se connecter au réplica SQL principal. Toutefois, il y aura une latence accrue, car les connexions SQL passent par régions. Dans ce cas, vous devez effectuer un basculement manuel comme suit : 

    - Basculer temporairement un réplica SQL dans la zone secondaire pour validation *synchrone* . Cela garantit l’aura pas de perte de données lors du basculement.

    - Basculer vers ce réplica SQL. 
    
    - Lorsque vous ne parvenez pas à la région principale, restaurer le paramètre de validation asynchrone. 

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Lorsque vous mettez à jour votre déploiement, mettre à jour une seule région à la fois, afin de réduire le risque d’un échec global à partir d’une configuration incorrecte ou une erreur dans l’application.

Tester la résilience du système aux pannes. Voici quelques scénarios courants de défaillance pour tester :

- Arrêter les instances de la machine virtuelle.

- Ressources de pression, tels que le processeur et la mémoire.

- Réseau de déconnecter ou de retard.

- Bloquer le processus.

- Expiration des certificats.

- Simuler des défaillances matérielles.

- Arrêtez le service DNS sur les contrôleurs de domaine.

Mesurer les temps de récupération et vérifier qu’ils répondent aux besoins de votre entreprise. Tester des combinaisons de modes de défaillance, ainsi.

## <a name="next-steps"></a>Étapes suivantes

Cette série a porte sur les déploiements de nuage pure. Scénarios d’entreprise nécessitent souvent un réseau hybride, la connexion d’un réseau local avec un réseau virtuel Azure. Pour savoir comment créer un réseau hybride de ce type, consultez [implémentation d’une Architecture de réseau hybride avec Azure et sur site VPN][hybrid-vpn].

<!-- Links -->

[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-always-on]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[tablediff]: https://msdn.microsoft.com/en-us/library/ms162843.aspx
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[vnet-to-vnet]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[wsfc]: https://msdn.microsoft.com/en-us/library/hh270278.aspx
[0]: ./media/blueprints/compute-multi-dc.png "Architecture de réseau hautement disponible pour les applications multicouches Azure"