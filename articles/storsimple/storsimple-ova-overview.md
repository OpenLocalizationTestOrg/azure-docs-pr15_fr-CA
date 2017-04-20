<properties
   pageTitle="Vue d’ensemble du tableau virtuel de StorSimple | Microsoft Azure"
   description="Décrit le tableau virtuel StorSimple, une solution de stockage intégrée qui gère les tâches de stockage entre un périphérique virtuel de locaux et d’un stockage en nuage Microsoft Azure."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="introduction-to-the-storsimple-virtual-array"></a>Introduction au tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans le tableau virtuel Microsoft Azure StorSimple, une solution de stockage intégrée qui gère les tâches de stockage entre un périphérique virtuel sur site s’exécutant dans un hyperviseur et le stockage en nuage Microsoft Azure. Le tableau virtuel (également connu comme le StorSimple sur site périphérique virtuel) est un serveur de fichier efficace, rentable et facilement gérable ou une solution de serveur iSCSI élimine un grand nombre des problèmes et des dépenses liés à la protection de données et de stockage d’entreprise. Le tableau virtuel est particulièrement bien adapté aux scénarios de bureaux distants/succursales (bureaux distants/succursales).

Cette présentation se concentre sur le tableau virtuel. 

- Pour une vue d’ensemble de la série 8000 de StorSimple, accédez à [série 8000 de StorSimple : une solution de cloud hybride](storsimple-overview.md). 

- Pour plus d’informations sur le périphérique de la gamme StorSimple 5000/7000, accédez à [L’aide en ligne StorSimple](http://onlinehelp.storsimple.com/).

Le tableau virtuel prend en charge l’iSCSI ou le protocole de bloc de Message serveur (SMB). Il s’exécute sur votre infrastructure de l’hyperviseur et fournit la hiérarchisation du nuage, sauvegarde de nuage, restauration rapide, au niveau de l’élément de récupération, les fonctions de reprise après sinistre.

Le tableau suivant récapitule les fonctionnalités importantes du tableau virtuel.

| Fonctionnalité | Tableau virtuel |
| ------- | ------------- |
|Configuration d’installation requise | Utilise l’infrastructure de virtualisation (Hyper-V ou VMware)|
| Disponibilité | Nœud unique |
| Capacité totale (y compris le nuage) |Jusqu'à 64 To de capacité utilisable par périphérique virtuel |
| Capacités locales | 390 Go de capacité utilisable de 6,4 To par périphérique virtuel (qui est nécessaire pour configurer des 500 Go, jusqu'à 8 To d’espace disque)|
| Protocoles natifs | iSCSI ou SMB |
| Objectif de temps de récupération (RTO) | iSCSI : moins de 2 minutes quelle que soit la taille |
| Objectif de point de récupération (RPO) | Les sauvegardes quotidiennes et à la demande |
| Hiérarchisation du stockage | Utilise le mappage pour déterminer quelles données doivent être monté en cascade ou de chaleur |
| Prise en charge | Infrastructure de virtualisation pris en charge par le fournisseur |
| Performances | Varie en fonction de l’infrastructure sous-jacente |
| Mobilité des données | Peut restaurer vers le même périphérique ou effectuez l’élément au niveau de restauration (serveur de fichiers) |
| Niveaux de stockage | Nuage et stockage de l’hyperviseur local |
| Taille de partage |Niveaux : jusqu'à 20 To ; localement épinglé : jusqu'à 2 To |
| Taille du volume |Niveaux : jusqu'à 5 to ; localement épinglé : jusqu'à 500 Go |
| Snapshots | Cohérence de Crash |
| Restauration au niveau élément | Oui ; les utilisateurs peuvent restaurer à partir de partages |

## <a name="why-use-storsimple"></a>Pourquoi utiliser StorSimple ?

StorSimple connecte les utilisateurs et les serveurs de stockage Azure en quelques minutes, sans modification des applications.

Le tableau suivant décrit certains des principaux avantages que la solution tableau virtuel fournit.

| Fonctionnalité | Avantages |
|---------|---------|
| Intégration transparente | Le tableau virtuel prend en charge l’iSCSI ou le protocole SMB. Le déplacement des données entre le niveau local et le niveau de nuage est rationnel et transparent pour l’utilisateur.|
| Coûts de stockage réduits | Avec StorSimple, vous devez configurer stockage local suffisant pour satisfaire les demandes en cours pour les données à chaud le plus utilisées. Comme les besoins de stockage augmentent, stockage cloud StorSimple niveaux ces données à moindre coût. Les données sont dédupliquées et compressées avant de l’envoyer vers le nuage afin de réduire davantage les dépenses en matière de stockage.|
| Gestion simplifiée du stockage | StorSimple fournit une gestion centralisée dans le nuage en utilisant le Gestionnaire de StorSimple pour gérer plusieurs périphériques.| 
| Reprise après sinistre améliorées et respect de la réglementation | StorSimple facilite la reprise après sinistre rapide en restaurant les métadonnées immédiatement et restaurer les données en fonction des besoins. Cela signifie que les opérations normales peuvent continuer avec une interruption minimale.|
| Mobilité des données | Niveaux de données dans le nuage est accessible à partir d’autres sites à des fins de restauration et la migration. Notez que vous pouvez restaurer les données uniquement pour le tableau virtuel d’origine. Toutefois, vous utilisez les fonctions de reprise après sinistre pour restaurer l’intégralité du tableau virtuel à un autre tableau virtuel.|

## <a name="storsimple-workload-summary"></a>Charge de travail StorSimple résumé

Un résumé des charges de travail StorSimple pris en charge est sous forme de tableau ci-dessous.

| Scénario                | Charge de travail              | Prise en charge |  Restrictions                                  | Version              |
|-------------------------|-----------------------|-----------|------------------------------------------------|----------------------|
| Collaboration de bureaux distants/succursales      | Le partage de fichiers          | Oui       | Consultez [les limites maximales pour serveur de fichiers](storsimple-ova-limits.md). <br>Consultez la [configuration système requise pour les versions prises en charge de SMB](storsimple-ova-system-requirements.md).   | Toutes les versions      |


## <a name="workflows"></a>Flux de travail

Le tableau virtuel StorSimple est particulièrement approprié pour les flux de travail suivant :

- [Gestion du stockage basée sur le nuage](#cloud-based-storage-management)
- [Sauvegarde indépendante de l’emplacement](#location-independent-backup)
- [Récupération après incident et de protection des données](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gestion du stockage basée sur le nuage

Vous pouvez utiliser le service Gestionnaire de StorSimple en cours d’exécution dans le portail classique Azure pour gérer les données stockées sur plusieurs périphériques et dans plusieurs emplacements. Ceci est particulièrement utile dans les scénarios de succursales distribués. Notez que vous devez créer des instances distinctes du service pour gérer les réseaux virtuels et des périphériques physiques de StorSimple Gestionnaire de StorSimple. 

![gestion du stockage basée sur le nuage](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Sauvegarde indépendante de l’emplacement

Avec le tableau virtuel, nuage instantanés fournissent une copie indépendante de l’emplacement, point-à-temps d’un volume ou d’un partage. Instantanés de nuage sont activés par défaut et ne peut pas être désactivés. Tous les volumes et partages sont sauvegarder en même temps via une stratégie de sauvegarde quotidienne unique, et vous pouvez effectuer des sauvegardes ad hoc supplémentaires lorsque cela est nécessaire.

### <a name="data-protection-and-disaster-recovery"></a>Récupération après incident et de protection des données

Le tableau virtuel prend en charge les données protection et reprise après sinistre restauration scénarios suivants :

- **Volume ou partage de restauration** – utilisation de la restauration en tant que nouveau flux de travail pour récupérer un volume ou un partage. Cette approche permet de récupérer la totalité du volume ou le partage.
- **Restauration au niveau élément** – actions autorisent un accès simplifié aux sauvegardes récentes. Vous pouvez facilement récupérer un fichier individuel à partir d’un dossier spécial .backup disponible dans le nuage. Cette fonction de restauration est exécutée par l’utilisateur et aucune intervention de l’administrateur n’est requis.
- **Reprise après sinistre** – utilisation de la fonctionnalité de basculement pour récupérer tous les volumes ou les partages vers un nouveau tableau virtuel. Vous créez le nouveau tableau virtuel et l’inscrire avec le service Gestionnaire de StorSimple, puis basculer sur le tableau virtuel d’origine. Le nouveau tableau virtuel puis suppose que les ressources de mise en service. 

## <a name="virtual-array-components"></a>Composants de la baie virtuelles

Le tableau virtuel inclut les composants suivants :

- [Tableau virtuel](#virtual-array) – un périphérique de stockage cloud hybride basé sur un ordinateur virtuel configuré dans votre environnement virtualisé ou l’hyperviseur.  
- [Gestionnaire de StorSimple service](#storsimple-manager-service) – une extension du portail classique Azure qui vous permet de gérer un ou plusieurs périphériques de StorSimple à partir d’une interface web unique auquel vous pouvez accéder à partir de différents emplacements géographiques. Vous pouvez utiliser le service Gestionnaire de StorSimple pour créer et gérer les services, afficher et gérer les périphériques et les alertes et gérer des volumes, des partages et des instantanés existants.
- [Interface utilisateur web local](#local-web-user-interface) – une interface utilisateur web qui est utilisée pour configurer le périphérique afin qu’il peut se connecter au réseau local et ensuite enregistrer le périphérique avec le service Gestionnaire de StorSimple. 
- [Interface de ligne de commande](#command-line-interface) – une interface Windows PowerShell que vous pouvez utiliser pour démarrer une session de prise en charge sur le tableau virtuel.
Les sections suivantes décrivent chacun de ces composants en détail et expliquent comment la solution réorganise les données alloue le stockage et facilite la gestion du stockage et protection des données.

### <a name="virtual-array"></a>Tableau virtuel

Le tableau virtuel est une solution de stockage d’un seul nœud qui fournit le stockage principal, gère la communication avec le stockage en nuage et aide à garantir la sécurité et la confidentialité de toutes les données qui est stockée sur le périphérique.

Le tableau virtuel est disponible en un seul modèle disponible pour téléchargement. La baie de stockage a une capacité maximale de 6,4 To sur le périphérique (avec une exigence de stockage sous-jacent de 8 To) et notamment de 64 To de stockage en nuage. 

Le tableau virtuel présente les caractéristiques suivantes :

- Il est rentable. Il facilite l’utilisation de votre infrastructure de virtualisation et peut être déployé sur votre hyperviseur Hyper-V ou VMware existant.
- Il se trouve dans le centre de données et peut être configuré comme un serveur iSCSI ou un serveur de fichiers. 
- Il est intégré avec le nuage.
- Les sauvegardes sont stockées dans le cloud, ce qui peut faciliter la reprise après sinistre et de simplifier la récupération de niveau d’élément (ILR). 
- Vous pouvez mettre à jour le tableau virtuel, comme appliquerait à un périphérique physique.

>[AZURE.NOTE] Un tableau virtuel ne peut pas être développé. Par conséquent, il est important de provisionnement du stockage adéquat lorsque vous créez le périphérique virtuel. 

### <a name="storsimple-manager-service"></a>Service Gestionnaire de StorSimple

Microsoft Azure StorSimple fournit une interface utilisateur basée sur le web (le service Gestionnaire de StorSimple) qui vous permet de gérer le centre de données et de stockage en nuage de façon centralisée. Vous pouvez utiliser le service Gestionnaire de StorSimple pour effectuer les tâches suivantes :

- Gestion de plusieurs baies de Virtual StorSimple à partir d’un seul service. 
- Configurer et gérer les paramètres de sécurité pour les périphériques StorSimple. (Cryptage dans le nuage est dépendant de l’API de Microsoft Azure).
- Configurer les propriétés et les informations d’identification du compte de stockage.
- Configurer et gérer des volumes ou des partages.
- Sauvegarder et restaurer des données sur des volumes ou des partages.
- Surveiller les performances.
- Passez en revue les paramètres système et identifier les problèmes possibles.

Le service Gestionnaire de StorSimple vous permet d’effectuer des tâches d’administration quotidiennes de votre tableau virtuel.

Pour plus d’informations, accédez à [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interface utilisateur web local

Le tableau virtuel inclut une interface utilisateur web qui est utilisée pour une configuration unique et l’inscription de l’appareil avec le service Gestionnaire de StorSimple. Vous pouvez l’utiliser pour arrêter et redémarrer le tableau virtuel, exécuter les tests de diagnostic, mise à jour de logiciel, modifier le mot de passe administrateur, permet d’afficher les journaux système et contacter le Support Microsoft pour une demande de service de fichiers. 

Pour plus d’informations sur l’utilisation de l’interface utilisateur basée sur le web, passez à [utilisation de l’interface utilisateur de basée sur le web pour administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interface de ligne de commande

L’interface de Windows PowerShell inclus vous permet de lancer une session de prise en charge par le Support Microsoft, afin qu’ils peuvent vous aider à résoudre les problèmes que vous pouvez rencontrer sur votre périphérique virtuel.

## <a name="storage-management-technologies"></a>Technologies de gestion de stockage

En plus du tableau virtuel et d’autres composants, la solution de StorSimple utilise les technologies logicielles suivantes pour fournir un accès rapide aux données importantes, réduire la consommation de stockage et protéger les données stockées sur votre tableau virtuel :

- [Hiérarchisation du stockage automatique](#automatic-storage-tiering) 
- [Volumes et partages ajoutés localement](#locally-pinned-shares-and-volumes)
- [Déduplication et compression de données hiérarchisé ou sauvegardés vers le nuage](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [Sauvegardes planifiées et à la demande](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Hiérarchisation du stockage automatique

Le tableau virtuel utilise un nouveau mécanisme de hiérarchisation pour gérer les données stockées sur la baie virtuelle et le nuage. Il n’y a que deux niveaux : le tableau virtuel et l’Azure local de stockage cloud. Le tableau virtuel StorSimple Réorganise automatiquement les données en niveaux basés sur une carte thermique, qui effectue le suivi de l’usage actuel, l’âge et relations à d’autres données. Données qui ne sont plus actives (ou plus) est stocké localement, tandis que les données les moins actives et inactives sont automatiquement migrées vers le nuage. (Toutes les sauvegardes sont stockées dans le nuage). StorSimple ajuste et réorganise les données et modifier les affectations de stockage en tant que modèles d’utilisation. Par exemple, certaines informations peuvent devenir moins actifs au fil du temps. Dès qu’elles seront progressivement moins actif, il est monté en cascade vers le nuage. Si ces mêmes données redevient actives, il est monté en cascade dans la baie de stockage.

Les données d’un volume ou un partage hiérarchisé particulier sont garanties son propre espace de niveau local. (environ 10 % de l’espace total mis en service pour le partage ou le volume). Alors que cela permet de réduire l’espace de stockage disponible sur le périphérique virtuel de ce partage ou le volume, il garantit que la hiérarchisation d’un partage ou le volume n'est pas affectée par les besoins de montage en cascade d’autres volumes ou les partages. Ainsi, une charge de travail très occupé sur un partage ou un volume ne peut pas forcer toutes les autres charges de travail vers le nuage. 

![hiérarchisation du stockage automatique](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE] Vous pouvez spécifier un volume épinglé que localement, auquel cas les données restent sur le tableau virtuel et ne sont jamais monté en cascade dans le nuage. Pour plus d’informations, consultez épinglée [localement](#locally-pinned-shares-and-volumes)des partages et des volumes.

### <a name="locally-pinned-shares-and-volumes"></a>Volumes et partages ajoutés localement

Vous pouvez créer des partages appropriés et volumes épinglés que localement. Cette fonctionnalité garantit que les données requises par les applications critiques restent dans le tableau virtuel et ne sont jamais monté en cascade dans le nuage. Volumes et partages localement épinglés présentent les fonctionnalités suivantes : 

- Ils ne sont pas soumis à des latences de nuage ou de problèmes de connectivité.
- Elles bénéficient toujours des fonctionnalités de récupération des sauvegarde et de reprise après sinistre de StorSimple cloud.

Vous pouvez restaurer un partage localement épinglé ou volume comme niveaux ou hiérarchisé ou volume localement épinglé. 

Pour plus d’informations sur les volumes ajoutés localement, accédez à [utiliser le service de gestionnaire de StorSimple pour gérer les volumes](storsimple-manage-volumes-u2.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Déduplication et compression de données hiérarchisé ou sauvegardés vers le nuage

StorSimple utilise la compression des données et de la déduplication pour réduire davantage les besoins de stockage dans le nuage. Déduplication permettant de réduire le montant global des données stockées en éliminant la redondance dans l’ensemble de données stockée. Comme les informations changent, StorSimple ignore les données non modifiées et capture uniquement les modifications. De plus, StorSimple permet de réduire la quantité de données stockées en identifiant et en supprimant les informations en double. 

>[AZURE.NOTE] Les données stockées sur la baie virtuelle ne sont pas dédupliquées ou compressées. Tous les déduplication et la compression se produit juste avant que les données sont envoyées vers le nuage.

### <a name="scheduled-and-on-demand-backups"></a>Sauvegardes planifiées et à la demande

Fonctionnalités de protection des données StorSimple permettent de créer des sauvegardes à la demande. En outre, un planning de sauvegarde par défaut garantit que les données sont sauvegardées quotidiennement. Les sauvegardes sont effectuées sous la forme d’instantanés incrémentiels, qui sont stockées dans le nuage. Des instantanés, qui enregistrent uniquement les modifications apportées depuis la dernière sauvegarde, peuvent être créées et restaurées rapidement. Ces captures instantanées peuvent être extrêmement importantes dans les scénarios de récupération après sinistre, car ils remplacement des systèmes de stockage secondaire (par exemple, une sauvegarde sur bande) et vous permettent de restaurer les données à votre centre de données ou sur d’autres sites si nécessaire.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [préparer le portail de tableau virtuel](storsimple-ova-deploy1-portal-prep.md).


