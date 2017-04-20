<properties
   pageTitle="Machines virtuelles de Windows en cours d’exécution pour une architecture à N niveaux | Architecture de référence | Microsoft Azure"
   description="Comment implémenter une architecture à plusieurs niveaux sur Azure, payeur une attention particulière à la sécurité de la gestion, la sécurité, l’évolutivité et disponibilité."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
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

# <a name="running-windows-vms-for-an-n-tier-architecture-on-azure"></a>Exécute les ordinateurs virtuels de Windows d’une architecture à N niveaux sur Azure

> [AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Exécution des VM de Linux pour une architecture à N niveaux sur Azure](guidance-compute-n-tier-vm-linux.md)
- [Exécute les ordinateurs virtuels de Windows d’une architecture à N niveaux sur Azure](guidance-compute-n-tier-vm.md)

Cet article présente un ensemble de pratiques éprouvées pour l’exécution des machines virtuelles de Windows (VMs) pour une application avec une architecture à N niveaux. Il s’appuie sur l’article [exécutant plusieurs ordinateurs virtuels sur Azure][multi-vm].

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cet article utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Il existe des variations des architectures à N niveaux. Pour l’essentiel, les différences ne devrait pas poser aux fins de ces recommandations. Cet article suppose une application classique à 3 niveaux web :

- **Niveau du Web.** Gère les requêtes HTTP entrantes. Les réponses sont renvoyées par le biais de ce niveau.

- **Niveau de l’entreprise.** Processus d’entreprise met en œuvre et d’autres logiques fonctionnel pour le système.

- **Niveau de la base de données.** Permet le stockage de données persistantes, à l’aide de [SQL Server toujours sur les groupes de disponibilité] [ sql-alwayson] pour une haute disponibilité.

> Un document Visio qui contient ce diagramme d’architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur « Compute - page de multi niveau (Windows).

![[0]][0]

- **Jeux de disponibilité.** Créer un [Jeu de disponibilité] [ azure-availability-sets] pour chaque niveau et de configurer au moins deux des ordinateurs virtuels dans chaque couche. Cette approche est nécessaire pour atteindre la disponibilité [SLA] [ vm-sla] pour les machines virtuelles.

- **Sous-réseaux.** Créer un sous-réseau distinct pour chaque couche. Spécifiez l’adresse plage et masque de sous-réseau à l’aide de la notation [CIDR] . 

- **Équilibreurs de charge.** Utiliser un [équilibreur de charge pour Internet] [ load-balancer-external] à distribuer le trafic Internet entrant et la couche web, un [équilibreur de charge interne] [ load-balancer-internal] pour répartir le trafic de réseau à partir de la couche web pour la couche métier.

- **Jumpbox**. Un _jumpbox_, également appelé un [hôte bastion]est un ordinateur virtuel sur le réseau pour que les administrateurs utilisent pour se connecter aux autres ordinateurs virtuels. Le jumpbox a un NSG qui autorise le trafic distant uniquement à partir de la liste d’autorisation des adresses IP publiques. Le NSG doit autoriser le trafic du Bureau à distance (RDP).

- La **surveillance**. Surveillance des logiciels tels que [Nagios], [Zabbix]ou [Icinga] peut vous donner une idée des temps de réponse, temps de fonctionnement de machine virtuelle et la santé générale de votre système. Installez le logiciel de surveillance sur un ordinateur virtuel qui se trouve dans un sous-réseau distinct de gestion.

- **NSGs**. Utiliser des [groupes de sécurité réseau] [ nsg] (NSGs) pour limiter le trafic réseau au sein de la VNet. Par exemple, dans l’architecture à 3 niveaux indiqué ici, la couche de base de données n’accepte pas le trafic web front-end, uniquement à partir de la couche d’entreprise et le sous-réseau de gestion.

- **SQL Server toujours sur le groupe de disponibilité.** Assure une disponibilité élevée au niveau de la couche données, grâce à la réplication et le basculement.

- **Serveurs de (services AD DS) de Services de domaine active Directory**. Les Services de domaine Active Directory (AD DS) stocke les données de l’annuaire et gère la communication entre les utilisateurs et les domaines, y compris les processus d’ouverture de session utilisateur, l’authentification et les recherches de répertoire. Un contrôleur de domaine Active Directory est un serveur qui exécute les services AD DS. Avant 2016 de serveur Windows, toujours sur les groupes de disponibilité doit être joint à un domaine. C’est parce que les groupes de disponibilité dépendent de la technologie de Cluster de basculement serveur Windows (WSFC). Windows Server 2016 introduit la possibilité de créer un Cluster de basculement sans Active Directory. Pour plus d’informations, consultez [Nouveautés de Clustering avec basculement dans Windows Server 2016][wsfc-whats-new]

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être configuré de différentes façons. Nous avons fourni un modèle Azure Resource Manager pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence suivre ces recommandations, à moins d’avoir une recommandation ne correspondant pas à un besoin spécifique.

### <a name="vnet--subnets"></a>VNet / sous-réseaux

Lorsque vous créez le VNet, allouer suffisamment d’espace adresse pour les sous-réseaux que vous aurez besoin. Spécifier la VNet adresse plage et masque de sous-réseau à l’aide de la notation [CIDR] . Utiliser un espace d’adressage qui se situe dans le standard [des blocs d’adresses IP privées][private-ip-space], qui sont 10.0.0.0/8, 172.16.0.0/12 et 192.168.0.0/16.

Choisissez une plage d’adresses qui ne se chevauche pas avec votre réseau sur site, dans le cas où vous devez configurer une passerelle entre le VNet et votre réseau sur site ultérieurement. Une fois que vous créez le VNet, vous ne pouvez pas modifier la plage d’adresses.

Concevoir les sous-réseaux avec les exigences en matière de fonctionnalité et la sécurité à l’esprit. Tous les ordinateurs virtuels dans le même niveau ou le rôle doit être placé dans le même sous-réseau, qui peut être une limite de sécurité. Pour plus d’informations sur la conception des VNets et des sous-réseaux, consultez [planifier et concevoir des réseaux virtuels Azure][plan-network].

Pour chaque sous-réseau, spécifiez l’espace d’adressage de sous-réseau en notation CIDR. Par exemple, « 10.0.0.0/24 » crée une plage de 256 adresses IP. (Permettent de 251 de ces ordinateurs virtuels, cinq sont réservés. Consultez le [Forum aux questions de réseau virtuel][vnet faq].) Vérifiez que les plages d’adresses ne se chevauchent pas dans des sous-réseaux.

### <a name="network-security-groups"></a>Groupes de sécurité de réseau

Règles NSG permet de restreindre le trafic entre les différents niveaux. Par exemple, dans l’architecture à 3 niveaux ci-dessus, la couche web ne communique pas directement avec la couche de base de données. Pour appliquer ce, le niveau de la base de données doit bloquer le trafic entrant à partir du sous-réseau de la couche web.  

  1. Créer un NSG et l’associer au sous-réseau de la couche de base de données.

  2. Ajouter une règle qui refuse tout le trafic entrant à partir de la VNet. (Utilisez le `VIRTUAL_NETWORK` la balise dans la règle.) 

  3. Ajoutez une règle avec une priorité plus élevée qui autorise le trafic entrant à partir du sous-réseau de niveau entreprise. Cette règle substitue à la règle précédente et permet à la couche d’entreprise communiquer avec la couche de base de données.

  4. Ajouter une règle qui autorise le trafic entrant dans le sous-réseau de niveau de base de données elle-même. Cette règle permet la communication entre les ordinateurs virtuels dans la couche de base de données, ce qui est nécessaire pour la réplication de la base de données et la reprise.

  5. Ajouter une règle qui autorise le trafic RDP le sous-réseau jumpbox. Cette règle permet aux administrateurs de se connecter à la couche de base de données à partir de la jumpbox.

  > [AZURE.NOTE] Un NSG a des [règles par défaut] [ nsg-rules] qui autorise le trafic entrant à partir de la VNet. Ces règles ne peut pas être supprimés, mais vous pouvez les substituer en créant des règles de priorité plus élevée.

### <a name="load-balancers"></a>Équilibreurs de charge

L’équilibreur de charge externe répartit le trafic Internet vers la couche web. Créer une adresse IP publique pour l’équilibrage de la charge. Reportez-vous à la section [Création d’un équilibreur de charge pour Internet][lb-external-create].

L’équilibreur de charge interne répartit le trafic de réseau à partir de la couche web pour la couche métier. Pour donner une adresse IP privée à cet équilibrage de la charge, créer la configuration IP d’un site Web frontal et l’associer avec le sous-réseau pour la couche métier. Reportez-vous à la section [créer un équilibreur de charge interne][lb-internal-create].

### <a name="sql-server-always-on-availability-groups"></a>SQL Server toujours sur les groupes de disponibilité

Nous vous recommandons de [Toujours sur les groupes de disponibilité] [ sql-alwayson] pour la haute disponibilité de SQL Server. Toujours sur les groupes de disponibilité nécessitent un contrôleur de domaine. Tous les nœuds dans le groupe de disponibilité doivent être dans le même domaine Active Directory.

Autres niveaux de se connecter à la base de données via un [port d’écoute de disponibilité groupe][sql-alwayson-listeners]. L’écouteur permet à un client SQL pour vous connecter sans connaître le nom de l’instance physique de SQL Server. Machines virtuelles que la base de données access doit être joint au domaine. Le client (dans ce cas, une autre couche) utilise DNS pour résoudre le nom de réseau virtuel de l’écouteur en adresses IP.

Configurez SQL Server Always On comme suit :

- Créer un cluster de Windows Server avec basculement Clustering (WSFC) et un groupe de disponibilité de SQL Server Always On. Pour plus d’informations, consultez [Mise en route avec toujours sur les groupes de disponibilité][sql-alwayson-getting-started].

- Créer un équilibreur de charge interne avec une adresse IP privée.

- Créez un écouteur de groupe de disponibilité et mapper le nom DNS de l’écouteur à l’adresse IP d’un équilibreur de charge interne. 

- Créer une règle d’équilibrage de la charge pour le port d’écoute de SQL Server (le port TCP 1433 par défaut). La règle d’équilibrage de la charge doit activer _flottante IP_, également appelée directement le serveur renvoie. Ainsi, la machine virtuelle pour répondre directement au client, qui permet une connexion directe à un réplica principal.

    > [AZURE.NOTE] Lorsque la variable IP est activé, le numéro de port frontal doit être le même que le numéro de port back-end dans la règle d’équilibrage de la charge.

Lorsqu’un client SQL tente de se connecter, l’équilibreur de charge achemine la demande de connexion au réplica qui est le serveur principal actuel. S’il existe un basculement vers un autre réplica, l’équilibreur de charge achemine automatiquement les demandes suivantes dans le nouveau réplica principal. Pour plus d’informations, voir [configurer l’équilibreur pour SQL de charge toujours sur][sql-alwayson-ilb].

Lors d’un basculement, les connexions client existantes sont fermées. Une fois le basculement terminé, les nouvelles connexions doivent être routées vers le nouveau réplica principal.

Si votre application effectue beaucoup plus de lectures que d’écritures, vous pouvez décharger certaines des requêtes à un réplica secondaire en lecture seule. Consultez [à l’aide d’un port d’écoute pour se connecter à un secondaire en lecture seule réplica (en lecture seule de routage)][sql-alwayson-read-only-routing].

Tester votre déploiement en [forçant un basculement manuel][sql-alwayson-force-failover].

### <a name="jumpbox"></a>Jumpbox

N’autorisent pas l’accès RDP à partir de l’Internet public pour les ordinateurs virtuels qui s’exécutent de la charge de travail d’application. Au lieu de cela, tous les accès RDP/SSH à ces ordinateurs virtuels doivent être placés par l’intermédiaire de la jumpbox. Un administrateur se connecte à la jumpbox et se connecte ensuite à l’autre ordinateur virtuel à partir de la jumpbox. Le jumpbox permet le trafic RDP à partir d’Internet, mais uniquement les adresses IP connus, liste d’autorisation.

Placer le jumpbox dans le VNet de même que les autres ordinateurs virtuels, mais dans un sous-réseau distinct de gestion.

Créer une [adresse IP publique] pour le jumpbox.

Utiliser une petite taille de la mémoire virtuelle pour le jumpbox, par exemple A1 Standard.

Configurer les NSGs de la couche web, couche d’entreprise et sous-réseaux de niveau de base de données pour permettre l’administration (RDP) le passage du trafic du sous-réseau de gestion.

Pour sécuriser la jumpbox, créez un NSG et l’appliquer au sous-réseau jumpbox. Ajouter une règle NSG qui permet des connexions RDP uniquement à partir d’un jeu d’autorisation d’adresses IP publiques.

Le NSG peut être associé pour le sous-réseau ou le jumpbox de carte réseau. Dans ce cas, nous vous recommandons d’y attacher à la carte réseau, afin que le trafic RDP est autorisé uniquement à la jumpbox, même si vous ajoutez d’autres ordinateurs virtuels sur le même sous-réseau.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Placez chaque couche ou un rôle VM dans un ensemble distinct de disponibilité. Ne placez les ordinateurs virtuels à partir de différents niveaux dans le même jeu de disponibilité. 

Au niveau de la base de données, ayant plusieurs ordinateurs virtuels ne traduit pas automatiquement dans une base de données hautement disponible. Pour une base de données relationnelle, vous devez généralement utiliser la réplication et basculement pour une haute disponibilité. Pour SQL Server, nous vous recommandons d’utiliser [Toujours sur les groupes de disponibilité][sql-alwayson]. 

Si vous avez besoin d’une plus grande disponibilité du [SLA Azure pour les machines virtuelles] [ vm-sla] fournit, répliquer l’application entre les deux régions et utiliser le Gestionnaire de trafic Azure pour le basculement. Pour plus d’informations, consultez [Exécution des machines virtuelles Windows dans plusieurs régions pour une haute disponibilité][multi-dc].   

## <a name="security-considerations"></a>Considérations sur la sécurité

Crypter les données au repos. Utilisez [Azure clé coffre-fort] [ azure-key-vault] pour gérer les clés de chiffrement de base de données. Clé de stockage en chambre forte peut stocker des clés de cryptage dans les modules de sécurité matérielle (HSM). Pour plus d’informations, voir [Configurer l’intégration Azure clé de coffre-fort pour SQL Server sur Azure VM] [ sql-keyvault] il a également recommandé de stocker des secrets de l’application, telles que les chaînes de connexion de base de données, dans la chambre forte de clé.

Envisagez d’ajouter une appliance virtuelle du réseau (à couloirs) pour créer un réseau de périmètre entre le réseau Internet public et le réseau virtuel Azure. À couloirs est un terme générique pour une appliance virtuelle qui les tâches liées au réseau, tels que des pare-feu, l’inspection des paquets, l’audit, routage personnalisé ou une série d’autres actions. Pour plus d’informations, consultez [implémentation d’un réseau de périmètre entre Internet et les Azure][dmz].

## <a name="scalability-considerations"></a>Évolutivité

Les équilibreurs de charge distribuer le trafic réseau vers les web et les niveaux métier. Mettre à l’échelle horizontalement en ajoutant de nouvelles instances de la machine virtuelle. Notez que vous pouvez mettre à l’échelle les web et les niveaux métier indépendamment, en fonction de la charge. Pour réduire les complications possibles dues à la nécessité de maintenir l’affinité du client, les ordinateurs virtuels dans la couche web doivent être sans état. Les ordinateurs virtuels qui héberge la logique métier doivent également être sans état.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Simplifier la gestion de l’ensemble du système à l’aide des outils d’administration centralisée, comme [l’Automatisation d’Azure][azure-administration], [Microsoft Operations Management Suite][operations-management-suite], [Chef][chef], ou [Marionnette][puppet]. Ces outils peuvent consolider les informations de diagnostic et de la santé capturées à partir de plusieurs ordinateurs virtuels pour fournir une vue d’ensemble du système.

## <a name="solution-deployment"></a>Déploiement de la solution

Un déploiement d’une architecture de référence qui implémente ces recommandations est disponible sur [Github][github-folder]. Cette architecture de référence comprend une couche web, couche d’entreprise, une couche de données, ainsi qu’un jumpbox les contrôleurs de domaine Active Directory et de la machine virtuelle.

L’architecture de référence peut être déployé en suivant les instructions ci-dessous : 

1. Cliquez sur le bouton ci-dessous.  
[! [« Déploiement d’Azure »] [1]][2]

2. Une fois le lien ouvert dans le portail d’Azure, entrez les valeurs suivantes : 
  - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : cliquez sur **Créer** et saisissez `ra-ntier-sql-network-rg` dans la zone de texte.
  - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
  - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
  - Cliquez sur le bouton **achat** .

3. Vérifier la notification de portail Azure pour un message, le déploiement est terminée.

4. Cliquez sur le bouton ci-dessous.  
[! [« Déploiement d’Azure »] [1]][3]

5. Une fois le lien ouvert dans le portail d’Azure, entrez les valeurs suivantes : 
  - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres, donc **Utiliser existant** et tapez `ra-ntier-sql-workload-rg` dans la zone de texte.
  - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
  - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
  - Cliquez sur le bouton **achat** .

6. Vérifier la notification de portail Azure pour un message, le déploiement est terminée.

7. Cliquez sur le bouton ci-dessous.  
[! [« Déploiement d’Azure »] [1]][4]

8. Une fois le lien ouvert dans le portail d’Azure, entrez les valeurs suivantes : 
  - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : cliquez sur **Créer** et saisissez `ra-ntier-sql-network-rg` dans la zone de texte.
  - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
  - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
  - Cliquez sur le bouton **achat** .

9. Vérifier la notification de portail Azure pour un message, le déploiement est terminée.

10. Les fichiers de paramètres incluent un codée de manière irréversible les noms d’utilisateur administrateur et les mots de passe et il est fortement recommandé que vous modifiez immédiatement sur tous les ordinateurs virtuels. Cliquez sur chaque ordinateur virtuel dans le portail d’Azure, puis cliquez sur **Réinitialiser le mot de passe** dans la lame **de prise en charge + de résolution des problèmes** . Sélectionnez **Réinitialiser le mot de passe** dans la zone de liste déroulante **en Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et un **mot de passe**. Cliquez sur le bouton de **mise à jour** pour rendre persistant le nouveau nom d’utilisateur et le mot de passe. 

Pour plus d’informations sur les autres façons de déployer cette architecture de référence, consultez le fichier readme dans le [Guide-unique-vm] [ github-folder] Github dossier. 

## <a name="next-steps"></a>Étapes suivantes

Pour atteindre une haute disponibilité de cette architecture de référence, nous vous recommandons de [déployer plusieurs régions][multi-dc].

<!-- links -->

[arm-templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[azure-administration]: ../automation/automation-intro.md
[azure-audit-logs]: ../resource-group-audit.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-key-vault]: https://azure.microsoft.com/services/key-vault.md
[azure-load-balancer]: ../load-balancer/load-balancer-overview.md
[hôte bastion]: https://en.wikipedia.org/wiki/Bastion_host
[routage inter-domaines sans classe]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier-sql
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters.md
[multi-vm]: guidance-compute-multi-vm.md
[n-tier]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[adresse IP publique]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[sql-alwayson]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/en-us/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/en-us/library/gg509118.aspx
[sql-alwayson-ilb]: https://blogs.msdn.microsoft.com/igorpag/2016/01/25/configure-an-ilb-listener-for-sql-server-alwayson-availability-groups-in-azure-arm/
[sql-alwayson-listeners]: https://msdn.microsoft.com/en-us/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/en-us/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: ../virtual-machines/virtual-machines-windows-ps-sql-keyvault.md
[vm-planned-maintenance]: ../virtual-machines/virtual-machines-windows-planned-maintenance.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/deploy-reference-architecture.sh
[vnet-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/virtualNetwork.parameters.json
[vnet-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/networkSecurityGroups.parameters.json
[nsg-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/webTier.parameters.json
[webtier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/webTier.parameters.json
[businesstier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/businessTier.parameters.json
[businesstier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/businessTier.parameters.json
[datatier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/dataTier.parameters.json
[datatier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/dataTier.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/compute-n-tier.png "Architecture à N niveaux utilisant Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2FvirtualNetwork.azuredeploy.json
[3]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fworkload.azuredeploy.json
[4]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fsecurity.azuredeploy.json