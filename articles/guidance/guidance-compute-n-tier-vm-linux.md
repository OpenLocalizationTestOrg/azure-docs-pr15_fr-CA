<properties
   pageTitle="Exécution des VM de Linux pour une architecture à N niveaux sur Azure | Microsoft Azure"
   description="Comment faire pour exécuter des ordinateurs virtuels de Linux pour une architecture multicouche de Microsoft Azure."
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
   ms.date="10/20/2016"
   ms.author="mwasson"/>

# <a name="running-linux-vms-for-an-n-tier-architecture-on-azure"></a>Exécution des VM de Linux pour une architecture à N niveaux sur Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]


> [AZURE.SELECTOR]
- [Exécution des VM de Linux pour une architecture à N niveaux sur Azure](guidance-compute-n-tier-vm-linux.md)
- [Exécute les ordinateurs virtuels de Windows d’une architecture à N niveaux sur Azure](guidance-compute-n-tier-vm.md)

Cet article présente un ensemble de pratiques éprouvées pour l’exécution des machines virtuelles de Linux (VMs) pour une application avec une architecture à N niveaux. Il s’appuie sur l’article [exécutant plusieurs ordinateurs virtuels sur Azure][multi-vm].

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cet article utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Il existe des variations des architectures à N niveaux. Pour l’essentiel, les différences ne devrait pas poser aux fins de ces recommandations. Cet article suppose une application classique à 3 niveaux web :

- **Niveau du Web.** Gère les requêtes HTTP entrantes. Les réponses sont renvoyées par le biais de ce niveau.

- **Niveau de l’entreprise.** Processus d’entreprise met en œuvre et d’autres logiques fonctionnel pour le système.

- **Niveau de la base de données.** Permet le stockage de données persistantes, à l’aide de Apache Cassandra pour une haute disponibilité.

> Un document Visio qui contient ce diagramme d’architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur « Compute - page de multi niveau (Linux).

![[0]][0]

- **Jeux de disponibilité.** Créer un [Jeu de disponibilité] [ azure-availability-sets] pour chaque niveau et de configurer au moins deux des ordinateurs virtuels dans chaque couche. Cette approche est nécessaire pour atteindre la disponibilité [SLA] [ vm-sla] pour les machines virtuelles.

- **Sous-réseaux.** Créer un sous-réseau distinct pour chaque couche. Spécifiez l’adresse plage et masque de sous-réseau à l’aide de la notation [CIDR] . 

- **Équilibreurs de charge.** Utiliser un [équilibreur de charge pour Internet] [ load-balancer-external] à distribuer le trafic Internet entrant et la couche web, un [équilibreur de charge interne] [ load-balancer-internal] pour répartir le trafic de réseau à partir de la couche web pour la couche métier.

- **Jumpbox**. Un _jumpbox_, également appelé un [hôte bastion]est un ordinateur virtuel sur le réseau pour que les administrateurs utilisent pour se connecter aux autres ordinateurs virtuels. Le jumpbox a un NSG qui autorise le trafic distant uniquement à partir de la liste d’autorisation des adresses IP publiques. Le NSG doit autoriser le trafic SSH (secure shell).

- La **surveillance**. Surveillance des logiciels tels que [Nagios], [Zabbix]ou [Icinga] peut vous donner une idée des temps de réponse, temps de fonctionnement de machine virtuelle et la santé générale de votre système. Installez le logiciel de surveillance sur un ordinateur virtuel qui se trouve dans un sous-réseau distinct de gestion.

- **NSGs**. Utiliser des [groupes de sécurité réseau] [ nsg] (NSGs) pour limiter le trafic réseau au sein de la VNet. Par exemple, dans l’architecture à 3 niveaux indiqué ici, la couche de base de données n’accepte pas le trafic web front-end, uniquement à partir de la couche d’entreprise et le sous-réseau de gestion.

- **Base de données de Apache Cassandra**. Assure une disponibilité élevée au niveau de la couche données, grâce à la réplication et le basculement.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être configuré de différentes façons. Nous avons fourni un modèle Azure Resource Manager pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence suivre ces recommandations, à moins d’avoir une recommandation ne correspondant pas à un besoin spécifique.

### <a name="vnet--subnets"></a>VNet / sous-réseaux

Lorsque vous créez le VNet, allouer suffisamment d’espace adresse pour les sous-réseaux que vous aurez besoin. Spécifier la VNet adresse plage et masque de sous-réseau à l’aide de la notation [CIDR] . Utiliser un espace d’adressage qui se situe dans le standard [des blocs d’adresses IP privées][private-ip-space], qui sont 10.0.0.0/8, 172.16.0.0/12 et 192.168.0.0/16.

Choisissez une plage d’adresses qui ne se chevauche pas avec votre réseau sur site, dans le cas où vous devez configurer une passerelle entre le VNet et votre réseau sur site ultérieurement. Une fois que vous créez le VNet, vous ne pouvez pas modifier la plage d’adresses.

Concevoir les sous-réseaux avec les exigences en matière de fonctionnalité et la sécurité à l’esprit. Tous les ordinateurs virtuels dans le même niveau ou le rôle doit être placé dans le même sous-réseau, qui peut être une limite de sécurité. Pour plus d’informations sur la conception des VNets et des sous-réseaux, consultez [planifier et concevoir des réseaux virtuels Azure][plan-network].

Pour chaque sous-réseau, spécifiez l’espace d’adressage de sous-réseau en notation CIDR. Par exemple, « 10.0.0.0/24 » crée une plage de 256 adresses IP. (Permettent de 251 de ces ordinateurs virtuels, cinq sont réservés. Consultez le [Forum aux questions de réseau virtuel][vnet faq].) Vérifiez que les plages d’adresses ne se chevauchent pas dans des sous-réseaux.

### <a name="load-balancers"></a>Équilibreurs de charge

L’équilibreur de charge externe répartit le trafic Internet vers la couche web. Créer une adresse IP publique pour l’équilibrage de la charge. Reportez-vous à la section [Création d’un équilibreur de charge pour Internet][lb-external-create].

L’équilibreur de charge interne répartit le trafic de réseau à partir de la couche web pour la couche métier. Pour donner une adresse IP privée à cet équilibrage de la charge, créer la configuration IP d’un site Web frontal et l’associer avec le sous-réseau pour la couche métier. Reportez-vous à la section [créer un équilibreur de charge interne][lb-internal-create].

### <a name="cassandra"></a>Cassandra

Nous vous recommandons [d’Entreprise de DataStax] [ datastax] pour la production de l’utilisation, mais ces recommandations s’appliquent à n’importe quelle édition de Cassandra. Pour plus d’informations sur l’exécution de DataStax dans Azure, consultez le [Guide de déploiement d’Enterprise DataStax pour Azure][cassandra-in-azure]. 

Placez les ordinateurs virtuels d’un cluster Cassandra dans un jeu de disponibilité, afin de garantir que les réplicas Cassandra sont répartis sur plusieurs domaines d’erreur et mise à niveau des domaines. Pour plus d’informations sur les domaines de la panne et mise à niveau, voir [Gérer la disponibilité des machines virtuelles][availability-sets-manage]. 

Configurer des domaines d’erreur 3 (le maximum) par l’ensemble de la disponibilité. 

Configurer les domaines de mise à niveau 18 par jeu de disponibilité. Cela vous donne le nombre maximal de domaines de mise à niveau que peuvent encore être distribuées uniformément sur les domaines de la panne.   

Configurer des nœuds en mode compatible rack. Mapper des domaines d’erreur à rayons dans le `cassandra-rackdc.properties` fichier.

Vous n’avez pas besoin un équilibreur de charge devant le cluster. Le client se connecte directement à un nœud dans le cluster.

### <a name="jumpbox"></a>Jumpbox

Placer le jumpbox dans le VNet de même que les autres ordinateurs virtuels, mais dans un sous-réseau distinct de gestion.

Créer une [adresse IP publique] pour le jumpbox.

Utiliser une petite taille de la mémoire virtuelle pour le jumpbox, par exemple A1 Standard.

Configurer les NSGs de la couche web, couche d’entreprise et sous-réseaux de niveau de base de données pour permettre l’administration (SSH) le passage du trafic du sous-réseau de gestion.

Pour sécuriser la jumpbox, créez un NSG et l’appliquer au sous-réseau jumpbox. Ajouter une règle NSG qui permet d’identifier les connexions SSH uniquement à partir d’un jeu d’autorisation d’adresses IP publiques.

Le NSG peut être associé pour le sous-réseau ou le jumpbox de carte réseau. Dans ce cas, nous vous recommandons d’y attacher à la carte réseau, afin que le trafic SSH est autorisé uniquement pour le jumpbox, même si vous ajoutez d’autres ordinateurs virtuels sur le même sous-réseau.

N’autorisent pas l’accès SSH à partir de l’Internet public pour les ordinateurs virtuels qui s’exécutent de la charge de travail d’application. Au lieu de cela, tous les accès SSH à ces ordinateurs virtuels doivent être placés par l’intermédiaire de la jumpbox. Un administrateur se connecte à la jumpbox et se connecte ensuite à l’autre ordinateur virtuel à partir de la jumpbox. Le jumpbox permet le trafic SSH à partir d’Internet, mais uniquement les adresses IP connus, liste d’autorisation.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Placez chaque couche ou un rôle VM dans un ensemble distinct de disponibilité. Ne placez les ordinateurs virtuels à partir de différents niveaux dans le même jeu de disponibilité. 

Au niveau de la base de données, ayant plusieurs ordinateurs virtuels ne traduit pas automatiquement dans une base de données hautement disponible. Pour une base de données relationnelle, vous devez généralement utiliser la réplication et basculement pour une haute disponibilité.  

Si vous avez besoin d’une plus grande disponibilité du [SLA Azure pour les machines virtuelles] [ vm-sla] fournit, répliquer l’application entre les deux régions et utiliser le Gestionnaire de trafic Azure pour le basculement. Pour plus d’informations, consultez [Exécution des ordinateurs virtuels Linux dans plusieurs régions pour une haute disponibilité][multi-dc].  

## <a name="security-considerations"></a>Considérations sur la sécurité

Règles NSG permet de restreindre le trafic entre les différents niveaux. Par exemple, dans l’architecture à 3 niveaux ci-dessus, la couche web ne communique pas directement avec la couche de base de données. Pour appliquer ce, le niveau de la base de données doit bloquer le trafic entrant à partir du sous-réseau de la couche web.  

  1. Créer un NSG et l’associer au sous-réseau de la couche de base de données.

  2. Ajouter une règle qui refuse tout le trafic entrant à partir de la VNet. (Utilisez le `VIRTUAL_NETWORK` la balise dans la règle.) 

  3. Ajoutez une règle avec une priorité plus élevée qui autorise le trafic entrant à partir du sous-réseau de niveau entreprise. Cette règle substitue à la règle précédente et permet à la couche d’entreprise communiquer avec la couche de base de données.

  4. Ajouter une règle qui autorise le trafic entrant dans le sous-réseau de niveau de base de données elle-même. Cette règle permet la communication entre les ordinateurs virtuels dans la couche de base de données, ce qui est nécessaire pour la réplication de la base de données et la reprise.

  5. Ajouter une règle qui autorise le trafic du sous-réseau jumpbox SSH. Cette règle permet aux administrateurs de se connecter à la couche de base de données à partir de la jumpbox.

  > [AZURE.NOTE] Un NSG a des [règles par défaut] [ nsg-rules] qui autorise le trafic entrant à partir de la VNet. Ces règles ne peut pas être supprimés, mais vous pouvez les substituer en créant des règles de priorité plus élevée.

Envisagez d’ajouter une appliance virtuelle du réseau (à couloirs) pour créer un réseau de périmètre entre le réseau Internet public et le réseau virtuel Azure. À couloirs est un terme générique pour une appliance virtuelle qui les tâches liées au réseau, tels que des pare-feu, l’inspection des paquets, l’audit, routage personnalisé ou une série d’autres actions. Pour plus d’informations, consultez [implémentation d’un réseau de périmètre entre Internet et les Azure][dmz].

## <a name="scalability-considerations"></a>Évolutivité

Les équilibreurs de charge distribuer le trafic réseau vers les web et les niveaux métier. Mettre à l’échelle horizontalement en ajoutant de nouvelles instances de la machine virtuelle. Notez que vous pouvez mettre à l’échelle les web et les niveaux métier indépendamment, en fonction de la charge. Pour réduire les complications possibles dues à la nécessité de maintenir l’affinité du client, les ordinateurs virtuels dans la couche web doivent être sans état. Les ordinateurs virtuels qui héberge la logique métier doivent également être sans état.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Simplifier la gestion de l’ensemble du système à l’aide des outils d’administration centralisée, comme [l’Automatisation d’Azure][azure-administration], [Microsoft Operations Management Suite][operations-management-suite], [Chef][chef], ou [Marionnette][puppet]. Ces outils peuvent consolider les informations de diagnostic et de la santé capturées à partir de plusieurs ordinateurs virtuels pour fournir une vue d’ensemble du système.

## <a name="solution-deployment"></a>Déploiement de la solution

Un déploiement d’une architecture de référence qui implémente ces recommandations est disponible sur [Github][github-folder]. Cette architecture de référence comprend une couche web, couche d’entreprise et une couche de données.

1. Cliquez sur le bouton ci-dessous.  
[! [« Déploiement d’Azure »] [1]][2]

2. Une fois le lien ouvert dans le portail d’Azure, entrez les valeurs suivantes : 
  - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : cliquez sur **Créer** et saisissez `ra-ntier-sql-network-rg` dans la zone de texte.
  - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
  - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
  - Cliquez sur le bouton **achat** .

3. Vérifier la notification de portail Azure pour un message, le déploiement est terminée.

4. Les fichiers de paramètres incluent un codée de manière irréversible les noms d’utilisateur administrateur et les mots de passe et il est fortement recommandé que vous modifiez immédiatement sur tous les ordinateurs virtuels. Cliquez sur chaque ordinateur virtuel dans le portail d’Azure, puis cliquez sur **Réinitialiser le mot de passe** dans la lame **de prise en charge + de résolution des problèmes** . Sélectionnez **Réinitialiser le mot de passe** dans la zone de liste déroulante **en Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et un **mot de passe**. Cliquez sur le bouton de **mise à jour** pour rendre persistant le nouveau nom d’utilisateur et le mot de passe.

## <a name="next-steps"></a>Étapes suivantes

Pour atteindre une haute disponibilité de cette architecture de référence, nous vous recommandons de [déployer plusieurs régions][multi-dc].

<!-- links -->

[azure-administration]: ../automation/automation-intro.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[availability-sets-manage]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[hôte bastion]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-consistency-usage]: http://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-in-azure]: https://docs.datastax.com/en/datastax_enterprise/4.5/datastax_enterprise/install/installAzure.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[routage inter-domaines sans classe]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[datastax]: http://www.datastax.com/products/datastax-enterprise
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters-linux.md
[multi-vm]: guidance-compute-multi-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[adresse IP publique]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[0]: ./media/blueprints/compute-n-tier-linux.png "Architecture à N niveaux utilisant Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier%2Fazuredeploy.json


