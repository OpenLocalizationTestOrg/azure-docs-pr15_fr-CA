<properties
   pageTitle="Référence de l’Architecture de Windows Azure - IaaS : Extension d’Active Directory vers Azure | Microsoft Azure"
   description="Comment implémenter une architecture de réseau sécurisée hybride avec autorisation d’Active Directory dans Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="telmos"/>

# <a name="extending-active-directory-directory-services-adds-to-azure"></a>Extension des Services d’annuaire Active Directory (ADDS) pour Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les méthodes conseillées pour l’extension de votre environnement Active Directory (AD) pour Azure, pour fournir des services d’authentification distribuée à l’aide des [Services de domaine Active Directory (AD DS)][active-directory-domain-services]. Cette architecture étend qui décrits dans les articles de [mise en œuvre d’une architecture de réseau sécurisée hybride dans Azure] [ implementing-a-secure-hybrid-network-architecture] et [mise en œuvre d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Vous utilisez les services AD DS pour authentifier les identités. Ces identités peuvent appartenir à des utilisateurs, des ordinateurs, des applications ou d’autres ressources qui font partie d’un domaine de sécurité. Vous pouvez héberger des services AD DS sur site, mais dans un scénario hybride où se trouvent les éléments d’une application dans Azure, il peut être plus efficace pour répliquer cette fonctionnalité et le référentiel d’Active Directory vers le nuage. Cette approche permet de réduire le temps de latence dû à l’envoi de l’authentification et les demandes d’autorisation locale à partir du cloud dans les services AD DS en cours d’exécution sur site. 

Il existe deux façons d’héberger vos services d’annuaire dans Azure :

1. Vous pouvez utiliser [Azure Active Directory (DAS)] [ azure-active-directory] pour créer un nouveau domaine Active Directory dans le nuage et le lier à un site de domaine Active Directory. L’installation puis [Azure Connect d’AD] [ azure-ad-connect] promesses à répliquer des identités dans le le référentiel local dans le nuage. Notez que le répertoire dans le nuage n’est **pas** une extension du système local, il s’agit plutôt une copie qui contient la même identité. Les modifications apportées à ces identités sur site est copiés vers le nuage, mais les modifications apportées dans le nuage **ne sera pas** être répliquées vers le domaine local. Par exemple, des réinitialisations de mot de passe doivent être effectuée sur site et Azure Connect d’Active Directory permet de copier les modifications vers le nuage. En outre, notez que la même instance de DAS peut être liée à plus d’une instance des services AD DS ; DAS contient l’identité de chaque référentiel Active Directory auquel il est lié.

    DAS est utile dans les situations où les locaux réseau et Azure virtuel hébergeant les ressources de cloud ne sont pas directement liés à l’aide d’un tunnel VPN ou un circuit de ExpressRoute. Bien que cette solution est simple, est peut-être pas approprié pour les systèmes où les composants peuvent migrer au-delà de la limite de nuage/le locaux comme ceci peut demander de reconfiguration des DAS. En outre, DAS gère uniquement l’authentification des utilisateurs plutôt que l’authentification des ordinateurs. Certaines applications et certains services, tels que SQL Server, peuvent nécessiter une authentification d’ordinateur auquel cas cette solution n’est pas appropriée.

2. Vous pouvez déployer un ordinateur virtuel qui exécute les services AD DS en tant que contrôleur de domaine dans Azure, d’étendre votre infrastructure Active Directory existante à partir de votre centre de données sur site. Cette approche est plus courante pour les scénarios où le réseau des locaux et Azure réseau virtuel sont connectés par une connexion VPN ou ExpressRoute. Cette solution prend également en charge bidirectionnelles réplication permettant de vous apporter des modifications dans le nuage et sur site, chaque fois qu’il est plus approprié. En fonction de vos exigences de sécurité, l’installation d’Active Directory dans le nuage peut être :
    - partie du même domaine que ce détenues sur site
    - un nouveau domaine dans une forêt partagée
    - une forêt distincte

<!-- we might want to add info on how to choose from the three options above -->

Cette architecture se concentre sur la solution 2, à l’aide de la même domaine AD DS dans Azure et sur site.

Scénarios d’utilisation classiques de cette architecture sont les suivantes :

- Les applications hybride dans lequel exécuter des charges de travail partiellement sur site et partiellement dans Azure.

- Les applications et les services qui effectuent l’authentification à l’aide d’Active Directory.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le schéma suivant met en évidence les éléments importants de cette architecture (*Cliquez pour agrandir*). Pour plus d’informations sur les éléments grisé, lire la [mise en œuvre d’une architecture de réseau sécurisée hybride dans Azure] [ implementing-a-secure-hybrid-network-architecture] et [mise en œuvre d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Réseau de locaux.** Le réseau local inclut les serveurs Active Directory locales qui peuvent effectuer l’authentification et l’autorisation pour les composants situés sur site.

- **Serveurs Active Directory.** Il s’agit de contrôleurs de domaine mise en œuvre des services d’annuaire (AD DS) en cours d’exécution en tant qu’ordinateurs virtuels dans le nuage. Ces serveurs peuvent fournir l’authentification des composants en cours d’exécution sur votre réseau virtuel Azure.

- **Sous-réseau de Active Directory.** Les serveurs de service d’annuaire Active Directory sont hébergées dans un sous-réseau distinct. Règles NSG protéger les serveurs de service d’annuaire Active Directory et peuvent fournir un pare-feu contre le trafic de sources inconnues.

- **Synchronisation azure passerelle et AD.**. La passerelle Azure fournit une connexion entre le réseau local et de le VNet d’Azure. Cela peut être une [connexion VPN] [ azure-vpn-gateway] ou [ExpressRoute d’Azure][azure-expressroute]. Toutes les demandes de synchronisation entre les serveurs de publicités dans le nuage et sur site traversent par la passerelle. Les itinéraires définis par l’utilisateur (UDRs) gérer le routage du trafic de synchronisation qui transmet directement au serveur Active Directory dans le nuage et ne passe pas par le réseau virtuel matériels existants (NVAs) utilisé dans ce scénario.

    Pour plus d’informations sur la configuration des NVAs et UDRs, consultez [implémentation d’une architecture de réseau sécurisée hybride dans Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Recommandations

Cette section résume les recommandations pour implémenter des services AD DS dans Azure, couvrant :

- Recommandations de la machine virtuelle.

- Recommandations de mise en réseau.

- Recommandations de sécurité. 

- Recommandations de Active Directory Site.

- Recommandations de sélection élective actives Directory FSMO.

>[AZURE.NOTE] Le document [instructions de déploiement de Windows Server Active Directory Azure machines virtuelles en fonctionnement] [ ad-azure-guidelines] contient des informations plus détaillées sur un grand nombre de ces points.

### <a name="vm-recommendations"></a>Recommandations de machine virtuelle

Créer des ordinateurs virtuels avec des ressources suffisantes pour gérer le volume prévu du trafic. Utiliser la taille des ordinateurs hébergeant des services AD DS sur site comme point de départ. Surveiller l’utilisation des ressources ; Vous pouvez redimensionner les ordinateurs virtuels et les mettre à l’échelle vers le bas si elles sont trop grandes. Pour plus d’informations sur le dimensionnement des contrôleurs de domaine AD DS, voir [Planification de la capacité pour les Services de domaine Active Directory][capacity-planning-for-adds].

Créer un disque de données virtuel séparé pour le stockage de la base de données, des journaux et SYSVOL pour Active Directory. Ne pas stocker ces éléments sur le même disque que le système d’exploitation. Notez que par défaut, les disques de données associés à une machine virtuelle utilisent écriture via la mise en cache. Toutefois, ce formulaire de mise en cache peut entrer en conflit avec les exigences des services AD DS. Pour cette raison, définissez le paramètre de *Préférence de Cache hôte* sur le disque de données *None*. Pour plus d’informations, reportez-vous à la section [emplacement de la base de données du service d’annuaire Windows Server Active Directory et SYSVOL][adds-data-disks].

Déployez au moins deux des ordinateurs virtuels exécutant des services AD DS en tant que contrôleurs de domaine à votre réseau virtuel Azure pour des raisons de [disponibilité](#Availability-considerations) .

### <a name="networking-recommendations"></a>Recommandations de mise en réseau

Configurer l’interface réseau pour chacun des ordinateurs virtuels qui héberge les services AD DS avec des adresses IP statiques privées. Cette configuration mieux prend en charge le DNS sur chacun des ordinateurs virtuels AD. Pour plus d’informations, consultez [comment définir une adresse IP privée statique dans Azure portal][set-a-static-ip-address].

> [AZURE.NOTE] Ne donnez pas les adresses IP publiques de machines virtuelles du service d’annuaire Active Directory. Reportez-vous à la section [Considérations relatives à la sécurité] [ security-considerations] pour plus de détails.

Vous devez vous assurer que le trafic peut circuler librement entre les serveurs de publicités dans le nuage et sur site :

- Ajouter des règles NSG au sous-réseau AD qui autorisent le trafic entrant du site. Pour obtenir des informations détaillées sur les ports qui utilisent des services AD DS, voir [Active Directory et Active Directory domaine Services Port][ad-ds-ports].

- Assurez-vous que les tables UDR n’achemine pas de trafic services AD DS par le biais des NVAs utilisée dans ce scénario. Pour vos propres déploiements, selon les NVAs que vous utilisez, vous souhaiterez rediriger le trafic.

### <a name="security-recommendations"></a>Recommandations de sécurité

Serveurs de service d’annuaire Active Directory gèrent l’authentification et sont donc des éléments très sensibles. Éviter l’exposition directe des serveurs AD DS à Internet. Placez les serveurs de service d’annuaire Active Directory dans un sous-réseau distinct, avec son propre pare-feu. Assurez-vous que les ports nécessaires pour utiliser les services AD DS pour l’authentification et l’autorisation et pour les serveurs de partiels restent ouvertes, mais ferment tous les autres ports. Pour plus d’informations, voir [Active Directory et Active Directory domaine Services Port][ad-ds-ports]. Les [composants de la Solution] [ solution-components] section plus loin dans ce document indique que l’exemple de solution utilise pour ouvrir les ports des règles du NSG.

Règles NSG vous permet de créer un pare-feu simple. Si vous avez besoin d’une protection plus complète vous pouvez implémenter un périmètre de sécurité autour des serveurs à l’aide d’une paire de sous-réseaux et NVAs, comme décrit dans le document de [mise en œuvre d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

Chiffrement de disque BitLocker ou sur Azure permet de crypter le disque hébergeant la base de données AD DS.

### <a name="active-directory-site-recommendations"></a>Recommandations de Site du répertoire actives

Vous pouvez utiliser des sites dans les services AD DS sur des contrôleurs de domaine ensemble de groupe qui sont connectés par une liaison rapide. Contrôleurs de domaine dans le même site AD DS répliquent leurs modifications de répertoire automatiquement, et une configuration minimale est nécessaire pour gérer la réplication.

POUR contrôler le trafic de réplication entre Azure et votre centre de données local, il est recommandé d’ajouter un site distinct de services AD DS pour représenter l’espace d’adressage utilisé par Azure. Vous pouvez configurer un lien de site entre votre site AD DS sites et contrôler la réplication intersite de manière plus efficace.

Séparation du site vous permet également d’appliquer différents objets stratégie de groupe (GPO) à des ordinateurs connectés dans Azure et de tirer parti des applications qui sont « site prenant en charge », tel que System Center Configuration Manager.

### <a name="active-directory-fsmo-placement-recommendations"></a>Recommandations de sélection élective actives Directory FSMO

Flexible serveurs de protection continue des données (FSMO, Single Master Operation) sont des contrôleurs de domaine spécialisé, reposnsible la cohérence des données entre les différents paramètres dans les services AD DS, répertoriés ci-dessous.

- **Contrôleur de schéma**. Il s’agit d’un rôle à l’échelle de la forêt qui conserve la structure du schéma utilisé par les services AD DS.

- **Maître de nommage de domaine**. Il s’agit d’un rôle à l’échelle de la forêt qui gère les informations sur les noms de domaine dans la forêt.

- **Le contrôleur de domaine principal (PDC)**. Il s’agit d’un rôle au niveau du domaine. Le contrôleur principal de domaine gère les mises à jour du mot de passe et verrouillage des comptes. Il est consulté par d’autres contrôleurs de domaine lorsque les demandes d’authentification de service ont des mots de passe. Le contrôleur principal de domaine gère les mises à jour de la stratégie de groupe et est la contrôleur de domaine cible pour les applications héritées et des outils d’administration qui effectuent certaines opérations en écriture.

- **Maître des ID relatifs (RID)**. RID est utilisés pour aider à identifier des objets dans un répertoire. Ce serveur est responsable de la génération d’un pool à l’échelle du domaine de RID pour une utilisation par tous les serveurs Active Directory au sein du domaine.

- **Rôle de l’infrastructure**. Un objet d’un domaine peut faire référence à un objet dans un autre domaine. Ce rôle de domaine est responsable de la mise à jour le SID d’un objet et le nom unique d’une référence d’objet interdomaine. Notez qu’un implémentation de ce rôle de serveur ne peut pas agir également comme un serveur de catalogue Global (GC).

Pour plus d’informations, consultez [rôles Active Directory FSMO dans Windows][AD-FSMO-roles-in-windows].

Dans ce scénario, nous vous recommandons de que vous éviter de déployer des rôles FSMO sur les contrôleurs de domaine dans Azure. 

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Créer une disponibilité pour les serveurs de publicités. Vérifiez qu’il existe au moins deux serveurs dans le jeu. Les serveurs de publicités dans le nuage doivent être dans le même domaine, les contrôleurs de domaine. Vous activez la réplication automatique entre les serveurs.

Également, vous devez désigner des serveurs en tant que [maître d’opérations en attente] [ standby-operations-masters] en cas d’échec de la connexion à un serveur ayant un rôle FSMO.

## <a name="security-considerations"></a>Considérations sur la sécurité

Si toutes les tâches d’administration de domaine sont susceptibles d’être effectuées à l’aide de contrôleurs de domaine local, envisagez de faire des contrôleurs de domaine dans le nuage en lecture seule. Un contrôleur de domaine en lecture seule uniquement gère un sous-ensemble d’informations d’identification des utilisateurs (suffisante pour effectuer une authentification locale) et peut être configuré en cache les informations uniquement pour des utilisateurs spécifiques. Par conséquent, si un contrôleur de domaine en lecture seule est compromis, seules les informations pour les utilisateurs mis en cache seront affectées, plutôt que les détails de chaque compte dans le domaine. Pour plus d’informations, voir [Contrôleurs de domaine en lecture seule][read-only-dc].

Afin de minimiser la vulnérabilité des comptes d’utilisateur individuels et de déjouer les tentatives d’intrusion, procédez comme meilleure pratique pour la définition et la gestion des mots de passe utilisateurs dans Active Directory. Pour plus d’informations, consultez [Méthodes conseillées pour appliquer les stratégies de mot de passe][best_practices_ad_password_policy]. En outre, veillez aux groupes auxquels affecter des utilisateurs. Par exemple, ne faites pas de membres utilisateurs ordinaires du groupe des administrateurs de l’entreprise, groupe d’administrateurs du schéma et Admins du domaine.

## <a name="scalability-considerations"></a>Évolutivité

Active Directory est automatiquement évolutive pour les contrôleurs de domaine qui font partie du même domaine. Les demandes sont distribuées sur tous les contrôleurs d’un domaine. Vous pouvez ajouter un autre contrôleur de domaine, et il se synchronisera automatiquement avec le domaine. Ne configurez pas un équilibreur de charge séparé pour diriger le trafic vers les contrôleurs dans le domaine. Assurez-vous que tous les contrôleurs de domaine disposent de suffisamment de mémoire et des ressources de stockage pour gérer la base de données du domaine ; dans l’idéal, que le contrôleur de domaine tous les ordinateurs virtuels de la même taille.

## <a name="management-considerations"></a>Considérations relatives à la gestion

Ne copiez pas les fichiers de disque dur virtuel de contrôleurs de domaine au lieu d’effectuer des sauvegardes régulières, car leur restauration peut entraîner des incohérences dans l’état entre les contrôleurs de domaine.

Arrêter et redémarrer un ordinateur virtuel qui exécute le rôle de contrôleur de domaine dans Azure dans le système d’exploitation invité au lieu d’utiliser l’option Arrêter dans le portail Azure. À l’aide du portail Azure pour arrêter un ordinateur virtuel entraîne la machine virtuelle d’être libérée. Cette action réinitialise la machine virtuelle-GenerationID, ce qui n’est pas souhaitable pour un contrôleur de domaine. Lorsque la machine virtuelle-GenerationID est réinitialisé, l’ID d’invocation du référentiel AD est également réinitialisé, le pool RID est ignoré et SYSVOL est marqué comme non forcée.

## <a name="monitoring-considerations"></a>Considérations relatives au contrôle

Ne parvient pas à analyser et à gérer un réseau de serveurs Active Directory peuvent engendrer des problèmes tels que :

- **Échec de l’ouverture de session**. Échec de connexion peut se produire dans le domaine ou la forêt si une résolution de nom ou de la relation d’approbation échoue, ou si un serveur de catalogue global ne peut pas déterminer l’appartenance au groupe universel.

- **Le verrouillage de compte**. Comptes d’utilisateurs et de service peuvent devenir verrouillés si le contrôleur de domaine principal n’est pas disponible, ou Échec de la réplication entre plusieurs contrôleurs de domaine.

- **Défaillance du contrôleur de domaine**. Si le lecteur contenant le fichier Ntds.dit manque d’espace disque, le contrôleur de domaine peut cesser de fonctionner.

- **Échec de l’application**. Les applications qui sont essentielles à votre activité, telles que Microsoft Exchange ou une autre application de messagerie électronique, peuvent échouer si l’échec des requêtes de carnet d’adresse dans le répertoire.

- **Données de l’annuaire incohérentes**. Si la réplication échoue pendant une longue période de temps, les objets dupliqués peuvent être créés dans le répertoire et amené à établir un diagnostic approfondi et temps pour éliminer.

- **Échec de création de compte**. Un contrôleur de domaine ne peut pas créer les comptes d’utilisateur ou un ordinateur si il épuise son approvisionnement d’ID relatifs et le maître RID n’est pas disponible.

- **Échec de stratégie de sécurité**. Si le dossier partagé SYSVOL ne réplique pas correctement, les objets de stratégie de groupe et les stratégies de sécurité ne sont pas correctement appliquées aux clients.

Analyser attentivement les serveurs de publicités et soyez prêt à prendre rapidement des mesures correctives. Créer une liste de contrôle de surveillance des tâches à exécuter à un intervalle approprié. Vous pourriez par exemple planifier quotidiennement les tâches cruciales suivantes :

- Examiner et résoudre les alertes signalées par les contrôleurs de domaine, 

- Vérifiez que tous les contrôleurs de domaine peuvent communiquer et que la réplication fonctionne.

- Garantir que SYSVOL partagé.

- Résoudre les problèmes de synchronisation de l’heure.

- Vérifiez l’espace disque sur les disques physiques utilisés par Active Directory.

Vous pouvez effectuer les autres tâches de routine moins fréquemment. Par exemple, vous pourriez consulter les relations d’approbation vérifier toutes les semaines pour les approbations obsolètes ou endommagées et vérifiez que tous les contrôleurs de domaine exécutent à l’aide des mêmes service packs et les correctifs du correctif mensuel.

Pour plus d’informations, consultez [Surveillance Active Directory][monitoring_ad]. Vous pouvez installer des outils tels que [Microsoft Systems Center] [ microsoft_systems_center] sur le serveur d’analyse (voir le [diagramme de l’architecture][architecture]) pour aider à effectuer ces tâches. 

## <a name="solution-components"></a>Composants de la solution

La solution fournie pour cette architecture crée un réseau hybride sécurisé, comme décrit dans les documents de [mise en œuvre d’une architecture de réseau sécurisée hybride dans Azure] [ implementing-a-secure-hybrid-network-architecture] et [mise en œuvre d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access], mais avec l’ajout des éléments suivants :

- Un groupe de ressources Azure nommé *basename*- dns-rg, où *basename* est un préfixe vous spécifiez lors du déploiement de la solution.

- Deux Azure VM appelée *basename*-ad1-vm et *basename*-ad2-machine virtuelle, créé dans le groupe de ressources de *nom de base*- dns-rg. Ces ordinateurs virtuels sont configurés comme serveurs d’Active Directory avec les Services d’annuaire et de serveur DNS installé et configuré.

- Un NSG nommé *basename*-ad-nsg dans le groupe de ressources Azure *basename*- ntwk-rg. Ce groupe de ressources fait partie de l’infrastructure qui constituent le réseau sécurisé hybride, mais le nouveau NSG est un complément qui définit des règles de sécurité entrante pour les serveurs Active Directory, comme indiqué dans le tableau suivant :


    Priorité|Nom|Source|Destination|Service|Action|
    --------|----|------|-----------|-------|------|
    170|vnet-à-port53|10.0.0.0/16|Tout|Custom(Any/53)|Autoriser|
    180|vnet-à-port88|10.0.0.0/16|Tout|Custom(Any/88)|Autoriser|
    190|vnet-à-port135|10.0.0.0/16|Tout|Custom(Any/135)|Autoriser|
    200|vnet-port137-9|10.0.0.0/16|Tout|Custom(Any/137-139)|Autoriser|
    210|vnet-à-port389|10.0.0.0/16|Tout|Custom(Any/389)|Autoriser|
    220|vnet-à-port464|10.0.0.0/16|Tout|Custom(Any/464)|Autoriser|
    230|vnet-rpc-dynamique|10.0.0.0/16|Tout|Custom(Any/49152-65535)|Autoriser|
    240|onprem-ad-port53|192.168.0.0/24|Tout|Custom(Any/53)|Autoriser|
    250|onprem-ad-port88|192.168.0.0/24|Tout|Custom(Any/88)|Autoriser|
    260|onprem-ad-port135|192.168.0.0/24|Tout|Custom(Any/135)|Autoriser|
    270|onprem-ad-port389|192.168.0.0/24|Tout|Custom(Any/389)|Autoriser|
    280|onprem-ad-port464|192.168.0.0/24|Tout|Custom(Any/464)|Autoriser|
    290|gestion-rdp-autoriser|10.0.0.128/25|Tout|Custom(Any/3389)|Autoriser|
    300|permettre à la passerelle|10.0.255.224/27|Tout|Custom(Any/Any)|Autoriser|
    310|Autoriser automatique|10.0.255.192/27|Tout|Custom(Any/Any)|Autoriser|
    320|vnet-refuser|Tout|Tout|Custom(Any/Any)|Autoriser|

    Les services AD DS utilise les ports 53, 89, 135, 389 et 464 à accepter le trafic d’authentification et réplication entrante. Dans ce tableau, le contrôleur de domaine local est le 192.168.0.0/24 espace adresse (votre espace d’adressage peut varier, vous spécifiez ces informations en tant que paramètre aux modèles déployés par la solution.

    Le NSG définit également les règles de sécurité sortante suivantes, qui permettent la synchronisation et l’autorisation le flux du trafic sur le réseau local :

    Priorité|Nom|Source|Destination|Service|Action|
    --------|----|------|-----------|-------|------|
    100|out-port53|Tout|192.168.0.0/24|Custom(Any/53)|Autoriser|
    110|out-port88|Tout|192.168.0.0/24|Custom(Any/88)|Autoriser|
    120|out-port135|Tout|192.168.0.0/24|Custom(Any/135)|Autoriser|
    130|out-port389|Tout|192.168.0.0/24|Custom(Any/389)|Autoriser|
    140|out-port445|Tout|192.168.0.0/24|Custom(Any/445)|Autoriser|
    150|out-port464|Tout|192.168.0.0/24|Custom(Any/464)|Autoriser|
    160|out-rpc-dynamique|Tout|192.168.0.0/24|Custom(Any/49152-65535)|Autoriser|

Le script fourni avec la solution a également effectuer les tâches suivantes :

- Il ajoute le *nom de base*-ad1-vm et *basename*-ad2-serveurs vm en tant que contrôleurs de domaine pour le domaine. Vous pouvez afficher ces serveurs dans la console *ordinateurs et utilisateurs Active Directory* dans le contrôleur de domaine local :

![[1]][1]

- Il crée un nouveau sous-réseau (10.0.0.0/16) pour un site Active Directory nommé Azure-VNet-Ad-Site pour le domaine. Ce site contient le *nom de base*-ad1-vm et *basename*-ad2-serveurs vm. 

- Il ajoute des paramètres de transport intersite IP configurer l’intervalle de réplication entre le site local et les contrôleurs de domaine dans le nuage. Vous pouvez voir les paramètres de sous-réseau, des sites et des paramètres de transport dans la console de *serveurs et Sites Active Directory* dans le contrôleur de domaine local :

![[2]][2]

## <a name="deployment"></a>Déploiement

La solution de l’échantillon a la prerequsites suivante :

- Vous avez déjà configuré votre domaine local, et que vous avez configuré DNS et installé les services de routage et d’accès distant pour prendre en charge d’un réseau privé virtuel se connecter à la passerelle VPN d’Azure.


- Vous avez installé la dernière version de l’interface CLI d’Azure. [Suivez ces instructions pour plus de détails][cli-install].

- Si vous déployez la solution à partir de Windows, vous devez installer un outil qui fournit un shell bash, comme [Bureau de GitHub][github-desktop].

>[AZURE.NOTE] Si vous n’avez pas accès à un domaine local existant, vous pouvez créer un environnement de test à l’aide de [onpremdeploy.sh] [ onpremdeploy] bash script. Ce script crée un réseau et une machine virtuelle dans le nuage qui simule une installation très simple en local. Modifiez ce script avant d’exécuter et de définir les variables suivantes définies au début du fichier :
>
> - **BASE_NAME**. Un préfixe défini par l’utilisateur pour le groupe de ressources et de la machine virtuelle créée par le script. Cet élément doit **pas comporter plus de 5 caractères** sinon que le script tente de générer une machine virtuelle avec un nom non valide et l’échec.
> 
> - **Abonnement**. Votre ID d’abonnement Azure. Le groupe de ressources sera créé dans cet abonnement.
> 
> - **Emplacement**. L’emplacement dans lequel créer le groupe de ressources, tels que *eastus* ou *westus*Azure.
> 
> - **ADMIN_USER_NAME**. Le nom à utiliser pour le compte administrateur de l’ordinateur virtuel.
> 
> - **ADMIN_PASSWORD**. Le mot de passe pour le compte administrateur.

Effectuez les étapes suivantes pour créer la solution de l’échantillon :

1. télécharger et modifier [azuredeploy.sh] [ azuredeploy] de script et de définir les paramètres suivants au début du fichier :

    - **BASE_NAME**. Un préfixe défini par l’utilisateur pour les groupes de ressources et les machines virtuelles créées par le script. Comme précédemment, cet élément doit être **pas plus de 5 caractères**.

    - **Abonnement**. Votre ID d’abonnement Azure.

    - **Type_système_exploitation**. Le système d’exploitation (*Windows* ou *Linux*) à utiliser pour la couche d’accès aux ordinateurs virtuels web, métier et données. Notez que tous les serveurs Active Directory créés par le script a exécuter Windows Server 2012, indépendamment de ce paramètre.

    - **Nom_domaine**. Le nom du domaine local. Notez que si vous utilisez l’environnement créé par le script onpremdeploy.sh, il doit être de *contoso.com*.

    - **Emplacement**. L’emplacement dans lequel créer les groupes de ressources Azure.

    - **ADMIN_USER_NAME**. Le nom à utiliser pour les comptes d’administrateur sur les ordinateurs virtuels différents.

    - **ADMIN_PASSWORD**. Le mot de passe pour le compte administrateur.

    - **ON_PREMISES_PUBLIC_IP**. L’adresse IP publique de l’ordinateur VPN sur site.

    - **ON_PREMISES_ADDRESS_SPACE**. L’espace d’adressage interne du réseau local. Si vous utilisez l’environnement créé par le script onpremdeploy.sh, il doit s’agir 192.168.0.0/16.

    - **VPN_IPSEC_SHARED_KEY**. La clé partagée IPSec utilisée pour établir la connexion VPN entre le réseau local et de la passerelle VPN d’Azure.

    - **ON_PREMISES_DNS_SERVER_ADDRESS**. L’adresse IP du serveur DNS local. Si vous utilisez l’environnement créé par le script onpremdeploy.sh, ce doit être 192.168.0.4

    - **ON_PREMISES_DNS_SUBNET_PREFIX** Le préfixe de l’adresse du sous-réseau local. Si vous utilisez l’environnement créé par le script onpremdeploy.sh, il doit s’agir 192.168.0.0/24.

    >[AZURE.NOTE] Pour gagner du temps et ressources, le script ne crée pas les niveaux d’accès métier ou de données. Si vous avez besoin de ces éléments, vous pouvez ne commentez pas la section suivante dans le script azuredeploy.sh :
    >
    >
    > ```
    > #### # create biz tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_BIZ_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${BIZ_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${BIZ_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_BIZ_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > #### 
    > #### # create db tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_DB_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${DB_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${DB_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_DB_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ```

2. Ouvrez une invite de shell bash et déplacer vers le dossier contenant le script azuredeploy.sh.

3. Connectez-vous à votre compte Azure. Dans le bash shell, entrez la commande suivante :

    ```
    azure login
    ```

    Suivez les instructions pour vous connecter à Azure.

4. Exécutez la commande `./azuredeploy.sh`, puis patientez pendant que le script crée l’infrastructure réseau.

5. À l’invite, *Vérifiez que le VNet a été créé*, utilisez le portail Azure pour vérifier qu’un groupe de ressources nommé *basename*- ntwk-rg a été créé et qu’il contient des éléments similaires à ceux indiqués dans l’image suivante :

    ![[3]][3]

    >[AZURE.NOTE] Dans les exemples, *basename* a été définie pour le *nuage* lorsque le script a été exécuté.

    Cliquez sur le *nom de base*- vnet VNet et cliquez sur *sous-réseaux*, vérifiez que vous ont créés les sous-réseaux illustrés ci-dessous :

    ![[4]][4]

6. À l’invite dans la fenêtre du shell bash, appuyez sur une touche et patientez pendant le niveau web et un équilibreur de charge sont créés.

7. À l’invite, *Vérifiez que la couche Web a été créée correctement*, utilisez le portail Azure pour vérifier qu’un groupe de ressources appelé *basename*web-couche-rg a été créé et qu’il contient des éléments similaires à ceux indiqués ci-dessous :

    ![[5]][5]

8. À l’invite dans la fenêtre du shell bash, appuyez sur une touche et patientez pendant les NVAs sont créés.

9. À l’invite, *Vérifiez que l’à couloirs a été correctement créé*, utilisez le portail Azure pour vérifier qu’un groupe de ressources appelé *basename*gestion-rg a été créé avec le contenu suivant :

    ![[6]][6]

10. À l’invite dans la fenêtre du shell bash, appuyez sur une touche et patientez pendant la jumpbox est créé.

11. À l’invite, *Vérifiez que le jumpbox a été créé correctement*, utilisez le portail Azure pour vérifier que les éléments suivants ont été ajoutés au groupe de ressources *nomdebase*gestion-rg :

    - Un ensemble de disponibilité appelé *basename*- jb-en tant que.

    - Un ordinateur virtuel nommé *basename*- jb-vm.

    - Une interface réseau appelée *basename*- jb-carte réseau.

12. À l’invite dans la fenêtre du shell bash, appuyez sur une touche et patientez pendant la passerelle Azure VPN et connexion sont créés. Notez que cette étape peut prendre jusqu'à 30 minutes.

13. À l’invite, *Vérifiez que la passerelle VPN a été correctement créée*, utilisez le portail Azure pour vérifier que les éléments suivants ont été ajoutés au groupe de ressources *nomdebase*- ntwk-rg :

    - Une passerelle réseau local appelé lgw locaux.
    
    - Une passerelle réseau virtuel appelé *basename*- vpngw.

    - Une connexion passerelle nommé *basename*- vnet-vpnconn. Notez que l’état de cette connexion peut être *pas connecté* si vous n’avez pas encore configuré la fin en local de la connexion ; vous répondra à cela plus tard.

14. À l’invite dans la fenêtre du shell bash, appuyez sur une touche et patientez pendant la création d’ordinateurs virtuels et autres ressources pour la zone DMZ.

15. À l’invite, *Vérifiez que la zone DMZ a été correctement créée*, utilisez le portail Azure pour vérifier qu’un groupe de ressources appelé dmz - de *basename*-rg a été créé avec le contenu suivant :

    ![[7]][7]

16. À l’invite dans la fenêtre du shell bash, appuyez sur une touche. Les messages suivants s’affiche :

    ```text
    Manual Step...

    Please configure your on-premises network to connect to the Azure VNet

    Make sure that you can connect to the on-premises AD server from the Azure VMs
    ```

    Connectez-vous à votre ordinateur local qui se connecte à la passerelle Azure et configurer la connexion de manière appropriée. Ajouter des itinéraires statiques à la passerelle locale qui dirige la plage d’adresses 10.0.0.0/16 par l’intermédiaire de la passerelle pour le VNet de requestsfor. Les étapes de cette procédure varient en fonction de la façon de vous connecter. Consultez [implémentation d’une Architecture de réseau hybride avec Azure et sur site VPN] [ implementing-a-hybrid-network-architecture-with-vpn] pour plus d’informations.

    Notez que vous pouvez trouver l’adresse IP publique de la passerelle VPN d’Azure à l’aide du portail Azure pour examiner la passerelle vpngw - de *basename*dans le groupe de ressources *nomdebase*- ntwk-rg :

    ![[8]][8]

    Vous pouvez déterminer si la connexion a été établie correctement en consultant l’état de la connexion - vnet-vpnconn *nom de base*. Il doit indiquer *connecté*.

    Pour tester la connexion, ouvrez une connexion Bureau à distance pour le jumpbox (10.0.0.254) à partir d’un ordinateur de votre réseau local.

17. À l’invite dans la fenêtre du shell bash, appuyez sur une touche. À la prochaine invite, *Appuyez sur une touche pour mettre à jour le paramètre VNet pour la VNet pointer vers les DNS local*, appuyez sur une touche et patienter pendant que les paramètres DNS pour le VNet sont mis à jour à la valeur que vous avez spécifié comme paramètre dans le script de azuredeploy.sh **ON_PREMISES_DNS_SERVER_ADDRESS** .

18. À l’invite, *Vérifiez que le paramètre de serveur DNS sur le VNet a été mis à jour*, utilisez le portail Azure pour examiner la définition de *serveurs DNS* de la VNet *basename*- vnet dans le groupe de ressources *nomdebase*- ntwk-rg. Il doit être défini sur *Personnalisé DNS*et le *serveur DNS principal* doit être l’adresse du serveur DNS local :

    ![[9]][9]

19. À l’invite *Appuyez sur une touche quelconque pour créer le groupe de ressources pour les serveurs de publicités* dans la fenêtre du shell bash, appuyez sur une touche et patientez pendant la création du groupe de ressources pour maintenir les serveurs de publicités dans le nuage.

20. À l’invite *Appuyez sur une touche quelconque pour créer les ordinateurs virtuels pour les serveurs de publicités* dans la fenêtre du shell bash, appuyez sur une touche et que les ordinateurs virtuels être créés et ajoutés au groupe de ressources.

21. Lorsque la *Appuyez sur n’importe quelle touche pour joindre les ordinateurs virtuels au domaine local* s’affiche, accédez au portail Azure et vérifiez qu’un groupe appelé *basename*- dns-rg a été créé et qu’il contient deux VM (*basename*-ad1-vm et *basename*-ad2-vm) :

    ![[10]][10]

22. Dans le groupe de ressources *nomdebase*- ntwk-rg, vérification qu’un NSG a été créé appelé *basename*-ad-nsg. Examiner les règles de sécurité entrante et sortante pour cette NSG. Elles doivent correspondre à ceux indiqués dans les tableaux dans les [composants de la Solution] [ solution-components] section.

23. À l’invite dans la fenêtre du shell bash, appuyez sur une touche et attendez que les ordinateurs virtuels soient ajoutés au domaine local.

24. À la *Veuillez consulter le site AD server pour vérifier que les ordinateurs ont été ajoutés aux domaines* invite, se connecter à votre ordinateur local et utiliser la console *ordinateurs et utilisateurs Active Directory* pour vérifier que les deux ordinateurs virtuels ont été ajoutés au domaine :

    ![[11]][11]

25. À l’invite dans la fenêtre du shell bash, appuyez sur une touche et patienter pendant que le site de la réplication AD est créé dans le domaine.

26. À la *Veuillez consulter le site AD server pour vérifier que le site de réplication a été créé* invite, utilisez la console *Services et Sites Active Directory* pour vérifier qu’un site de réplication nommé *Azure-Vnet-Ad-Site* a été créé avec succès, ainsi qu’une liaison de transport intersite IP appelée *AzureToOnpremLink*et un sous-réseau qui fait référence à la VNet :

    ![[12]][12]

27. À l’invite dans la fenêtre du shell bash, appuyez sur une touche et patienter pendant que le script installe les Services d’annuaire et de DNS sur chacun des ordinateurs virtuels AD.

28. Lorsque l’invite, *Connectez-vous à chaque serveur AD Azure pour vérifier que les Services d’annuaire a été configuré avec succès* s’affiche, ouvrez une connexion Bureau à distance à partir d’un ordinateur local à la jumpbox (jb - de*basename*-vm), puis ouvrez une autre connexion Bureau à distance à partir de la jumpbox pour le premier serveur AD (*basename*-ad1-vm). Connectez-vous en utilisant le **nom_domaine**, **ADMIN_USER_NAME**et **ADMIN_PASSWORD** que vous avez spécifié dans le script azuredeploy.sh. À l’aide du Gestionnaire de serveur, vérifiez que les rôles de domaine Active Directory et DNS ont été ajoutés. Répétez cette procédure pour le second serveur AD (*basename*-ad2-vm).

29. À l’invite dans la fenêtre du shell bash, appuyez sur une touche. Lorsque l’invite *Appuyez sur n’importe quelle touche pour définir les paramètres DNS de VNet Azure pour pointer vers le serveur DNS dans Azure* s’affiche, appuyez sur une touche et permettre le script mettre à jour les paramètres de le VNet DNS.

30. Lorsque l’invite *Vérifiez que le DNS VNet paramètre a été mis à jour la référence Azure VM DNS serveurs* s’affiche, à l’aide de la vérification de portail Azure le paramètre des *Serveurs DNS* de la VNet *basename*- vnet dans le groupe de ressources *nomdebase*- ntwk-rg. Les serveurs DNS principales et secondaires doivent maintenant faire référence à deux ordinateurs virtuels AD :

    ![[13]][13]

31. Redémarrez chacun des ordinateurs virtuels annonce avant de continuer. Cette étape est nécessaire pour s’assurer qu’ils récupérant les paramètres DNS corrects à partir de Azure. Attendez que les deux ordinateurs virtuels sont en cours d’exécution avant de poursuivre.

32. À l’invite dans la fenêtre du shell bash, appuyez sur une touche. À l’invite suivante, *Appuyez sur une touche pour appliquer la passerelle UDR au sous-réseau passerelle (il a peut-être été supprimé)*, appuyez sur une touche et permettre au script d’actualisation de la passerelle UDR.

33. Vérifiez que le script se termine avec succès.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les meilleures pratiques pour la [Création d’une forêt de ressources ajoute] [ adds-resource-forest] dans Azure.

- Découvrez les meilleures pratiques pour la [Création d’une infrastructure ADFS] [ adfs] dans Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[architecture]: #architecture_diagram
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[capacity-planning-for-adds]: http://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048(v=ws.11).aspx
[powershell-ad]: https://technet.microsoft.com/en-us/library/ee617195.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[cli-install]: https://azure.microsoft.com/documentation/articles/xplat-cli-install
[github-desktop]: https://desktop.github.com/
[sssd-and-active-directory]: https://help.ubuntu.com/lts/serverguide/sssd-ad.html
[set-a-static-ip-address]: https://azure.microsoft.com/documentation/articles/virtual-networks-static-private-ip-arm-pportal/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[AD-FSMO-recommendations]: #active_directory_FSMO_placement_recommendations
[AD-FSMO-roles-in-windows]: https://support.microsoft.com/en-gb/kb/197132
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[transfer-FSMO-roles]: https://technet.microsoft.com/library/cc816946(v=ws.10).aspx
[view-fsmo-roles]: https://technet.microsoft.com/library/cc816893(v=ws.10).aspx
[read-only-dc]: https://technet.microsoft.com/library/cc732801(v=ws.10).aspx
[AD-sites-and-subnets]: https://blogs.technet.microsoft.com/canitpro/2015/03/03/step-by-step-setting-up-active-directory-sites-subnets-site-links/
[sites-overview]: https://technet.microsoft.com/library/cc782048(v=ws.10).aspx
[implementing-adfs]: ./guidance-iaas-ra-secure-vnet-adfs.md
[onpremdeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/onpremdeploy.sh
[azuredeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/azuredeploy.sh
[solution-components]: #solution_components

[0]: ./media/guidance-iaas-ra-secure-vnet-ad/figure1.png "Architecture de réseau hybride avec Active Directory sécurisée"
[1]: ./media/guidance-iaas-ra-secure-vnet-ad/figure2.png "La console ordinateurs et utilisateurs Active Directory répertorie deux ordinateurs en tant que serveurs virtuels Azure"
[2]: ./media/guidance-iaas-ra-secure-vnet-ad/figure3.png "La console Services et Sites Active Directory affiche les paramètres de réplication pour le site dans le nuage"
[3]: ./media/guidance-iaas-ra-secure-vnet-ad/figure4.png "Le contenu du groupe de la ressource de nom de base-ntwk-rg"
[4]: ./media/guidance-iaas-ra-secure-vnet-ad/figure5.png "Les sous-réseaux dans le VNet basename-vnet"
[5]: ./media/guidance-iaas-ra-secure-vnet-ad/figure6.png "Les éléments de la couche web"
[6]: ./media/guidance-iaas-ra-secure-vnet-ad/figure7.png "Les NVAs dans le groupe de ressources de basename-gestion-rg"
[7]: ./media/guidance-iaas-ra-secure-vnet-ad/figure8.png "Les ressources du groupe de ressources de basename-dmz-rg"
[8]: ./media/guidance-iaas-ra-secure-vnet-ad/figure9.png "Recherche de l’adresse IP publique de la passerelle VPN d’Azure"
[9]: ./media/guidance-iaas-ra-secure-vnet-ad/figure10.png "Les paramètres de serveur DNS pour la * basename *-vnet VNet"
[10]: ./media/guidance-iaas-ra-secure-vnet-ad/figure11.png "Le * nom de base *-dns-rg groupe de ressources contenant l’ordinateurs virtuels du serveur Active Directory"
[11]: ./media/guidance-iaas-ra-secure-vnet-ad/figure12.png "La console Active Directory utilisateurs et ordinateurs répertoriant l’ordinateurs virtuels du serveur Active Directory en tant que membres du domaine"
[12]: ./media/guidance-iaas-ra-secure-vnet-ad/figure13.png "La console Active Directory Sites et Services affiche le site de réplication pour les serveurs de publicités Azure"
[13]: ./media/guidance-iaas-ra-secure-vnet-ad/figure14.png "Les paramètres du serveur DNS pour la VNet vnet basename référençant les serveurs Active Directory dans le nuage"