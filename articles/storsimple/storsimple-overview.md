<properties 
   pageTitle="Quel est StorSimple ? | Microsoft Azure" 
   description="Décrit des StorSimple la hiérarchisation, le périphérique, périphérique virtuel, services et gestion du stockage et présente les termes clés utilisés dans StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="10/05/2016"
   ms.author="v-sharos@microsoft.com"/>

# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Série 8000 de StorSimple : une solution de stockage cloud hybride

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans Microsoft Azure StorSimple, une solution de stockage intégrée qui gère les tâches de stockage entre les périphériques locaux et de stockage en nuage Microsoft Azure. StorSimple est une solution de réseau SAN de zone de stockage efficace, rentable et facilement gérable qui élimine un grand nombre des problèmes et des dépenses liés à la protection de données et de stockage d’entreprise. Il utilise le périphérique série 8000 de StorSimple propriétaire, s’intègre avec les services cloud et fournit un ensemble d’outils de gestion pour une vue transparente du stockage d’entreprise tous, y compris le stockage en nuage. (Les informations de déploiement StorSimple publiées sur le site Web de Microsoft Azure concernent uniquement les périphériques série 8000 de StorSimple. Si vous utilisez un périphérique de la gamme StorSimple 5000/7000, accéder à [l’Aide de StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple utilise la [hiérarchisation du stockage](#automatic-storage-tiering) pour gérer les données stockées sur différents supports de stockage. Le jeu de travail en cours est stockée en local sur le disques SSD (SSD), les données qui sont utilisées moins fréquemment sont stockées sur des disques durs (HDD) et données d’archivage sont poussées vers le nuage. En outre, StorSimple utilise la déduplication et la compression pour réduire la quantité de stockage qui utilise des données. Pour plus d’informations, accédez à la [déduplication et la compression](#deduplication-and-compression). Pour les définitions d’autres termes et concepts clés utilisés dans la documentation de la série 8000 de StorSimple, passez à [la terminologie de StorSimple](#storsimple-terminology) à la fin de cet article.

Avec la mise à jour de StorSimple 2, vous pouvez identifier des volumes appropriés comme *épinglés localement* afin de garantir que les données principales restent locales sur le périphérique et ne pas niveau vers le nuage. Cela vous permet d’exécuter des charges de travail qui sont sensibles à la latence du cloud, tels que SQL et la machine virtuelle des charges de travail sur les volumes localement épinglé, tout en continuant à utiliser le nuage pour la sauvegarde. Pour plus d’informations sur les volumes ajoutés localement, voir [utiliser le service de gestionnaire de StorSimple pour gérer les volumes](storsimple-manage-volumes-u2.md). 

Mise à jour 2 vous permet également de créer des périphériques virtuels StorSimple qui tirent parti de la faible latence et les hautes performances fourni par le stockage Azure premium. Pour plus d’informations sur les périphériques virtuels StorSimple premium, consultez [déployer et gérer un périphérique virtuel de StorSimple dans Azure](storsimple-virtual-device-u2.md). Pour plus d’informations sur le stockage Azure premium, accédez à [prime stockage : stockage hautes performances pour les charges de travail de Machine virtuelle Azure](../storage/storage-premium-storage.md).

En plus de la gestion du stockage, fonctions de protection des données StorSimple permettent de créer à la demande et les sauvegardes planifiées et les stocker localement ou dans le nuage. Les sauvegardes sont effectuées sous la forme d’instantanés incrémentiels, ce qui signifie qu’ils peuvent être créés et restaurer rapidement. Instantanés de nuage peuvent être extrêmement importantes dans les scénarios de récupération après sinistre, car ils remplacement des systèmes de stockage secondaire (par exemple, une sauvegarde sur bande) et vous permettent de restaurer les données à votre centre de données ou sur d’autres sites si nécessaire.

![icône de vidéo](./media/storsimple-overview/video_icon.png) Regardez la vidéo de présentation de Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## <a name="why-use-storsimple"></a>Pourquoi utiliser StorSimple ?

Le tableau suivant décrit certains des principaux avantages que Microsoft Azure StorSimple fournit.

| Fonctionnalité | Avantages |
|---------|---------|
|Intégration transparente | Microsoft Azure StorSimple utilise le protocole iSCSI pour lier des installations de stockage des données de manière invisible. Cela garantit que les données stockées dans le cloud, dans le centre de données, ou sur des serveurs distants semble être stocké à un emplacement unique.|
|Coûts de stockage réduits|Microsoft Azure StorSimple alloue suffisamment local ou stockage en nuage pour répondre aux besoins actuels et étend le stockage en nuage uniquement lorsque cela est nécessaire. Il réduit encore les besoins en stockage et dépenses en éliminant les versions des mêmes données (déduplication) redondantes et en utilisant la compression.|
|Gestion simplifiée du stockage|Microsoft Azure StorSimple fournit des outils d’administration que vous pouvez utiliser pour configurer et gérer les données stockées en local, sur un serveur distant et dans le cloud de système. En outre, vous pouvez gérer la sauvegarde et restauration des fonctions à partir d’un composant logiciel enfichable Microsoft Management Console (MMC). StorSimple fournit une interface distincte, facultative que vous pouvez utiliser pour étendre les services de protection des données et de gestion de StorSimple au contenu stocké sur des serveurs SharePoint. |
|Reprise après sinistre améliorées et respect de la réglementation|Microsoft Azure StorSimple ne nécessite pas de temps de restauration. Au lieu de cela, il restaure les données qu’il est nécessaire. Cela signifie que les opérations normales peuvent continuer avec une interruption minimale. En outre, vous pouvez configurer des stratégies pour spécifier des plannings de sauvegarde et de conservation des données.
|Mobilité des données|Les données téléchargées sur les services en nuage Microsoft Azure sont accessibles à partir d’autres sites à des fins de restauration et la migration. En outre, vous pouvez utiliser StorSimple pour configurer les périphériques virtuels StorSimple sur les machines virtuelles (VM) Microsoft Azure. Les ordinateurs virtuels peuvent ensuite utiliser périphériques virtuels pour accéder aux données stockées à des fins de test ou de récupération.|
|Prise en charge d’autres fournisseurs de services de cloud |La série 8000 de StorSimple avec le logiciel de mise à jour 1 ou ultérieure prend en charge Amazon S3 avec les enregistrements de ressources, HP et OpenStack cloud services, ainsi que Microsoft Azure. (Vous devez toujours un compte de stockage Microsoft Azure à des fins de gestion de périphérique.) Pour plus d’informations, accédez à [ce qui est nouveau dans la mise à jour 1.2](storsimple-update1-release-notes.md#whats-new-in-update-12).|
|Continuité d’activité | Mise à jour 1 ou version ultérieure fournit une nouvelle fonctionnalité de migration qui autorise des utilisateurs de série 5000-7000 à migrer leurs données vers un périphérique de la gamme 8000 de StorSimple StorSimple.|
|Disponibilité dans le portail du gouvernement Azure | Mise à jour de StorSimple 1 ou version ultérieure est disponible dans le portail de l’administration d’Azure. Pour plus d’informations, voir [déployer votre périphérique de StorSimple locaux dans le portail de l’administration](storsimple-deployment-walkthrough-gov.md).|
|Disponibilité et protection des données | La série 8000 de StorSimple avec la mise à jour 1 ou version ultérieure prend en charge la Zone redondants stockage (ZRS), en plus du stockage de stockage localement redondants (LRS) et Geo redondant (GRS). Reportez-vous à [cet article sur les options de redondance de stockage Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) pour plus d’informations ZRS.|
|Prise en charge pour les applications critiques | Avec 2 StorSimple, vous pouvez identifier les volumes appropriés épinglés que localement. Cette fonctionnalité garantit que les données requises par les applications critiques ne sont pas monté en cascade dans le nuage. Volumes ajoutés localement ne sont pas soumis à des latences de nuage ou de problèmes de connectivité. Pour plus d’informations sur les volumes ajoutés localement, voir [utiliser le service de gestionnaire de StorSimple pour gérer les volumes](storsimple-manage-volumes-u2.md).|
|Faible latence et les hautes performances | StorSimple mise à jour 2 vous permet de créer des périphériques virtuels qui profitent des performances élevées, faible temps de latence des fonctionnalités de stockage de prime Azure. Pour plus d’informations sur les périphériques virtuels StorSimple premium, consultez [déployer et gérer un périphérique virtuel de StorSimple dans Azure](storsimple-virtual-device-u2.md).|

![icône de vidéo](./media/storsimple-overview/video_icon.png) visionnez [cette vidéo](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) pour une vue d’ensemble des fonctionnalités de la série 8000 de StorSimple et des avantages.

## <a name="storsimple-components"></a>Composants StorSimple

La solution Microsoft Azure StorSimple inclut les composants suivants :

- **Périphérique de Microsoft Azure StorSimple** – une baie de stockage sur site hybride contenant SSDs et disques durs, ainsi que les contrôleurs redondants et des fonctions de basculement automatique. Les contrôleurs de gestion du stockage, la hiérarchisation, plaçant actuellement utilisées (ou active) des données sur le stockage local (sur les serveurs périphériques ou sur site), tout en déplaçant moins de données fréquemment utilisés vers le nuage.
- **Périphérique virtuel de StorSimple** , également appelé l’Appliance virtuelle StorSimple, il s’agit d’une version du logiciel du périphérique StorSimple qui réplique l’architecture et la plupart des fonctionnalités du périphérique de stockage physique hybride. Le périphérique virtuel StorSimple s’exécute sur un nœud dans une machine virtuelle Azure. Les périphériques virtuels Premium, qui tirent parti du stockage de prime Azure, sont disponibles dans la mise à jour 2 et les versions ultérieures.
- **Service Gestionnaire de StorSimple** – une extension du portail Azure classique qui permet de gérer un dispositif de StorSimple ou d’un périphérique virtuel de StorSimple à partir d’une interface web unique. Vous pouvez utiliser le service Gestionnaire de StorSimple pour créer et gérer les services, afficher et gérer les périphériques, afficher les alertes, gérer les volumes et afficher et gérer les stratégies de sauvegarde et le catalogue de sauvegarde.
- **Windows PowerShell pour StorSimple** – une interface de ligne de commande que vous pouvez utiliser pour gérer le périphérique StorSimple. Windows PowerShell pour StorSimple dispose de fonctionnalités qui vous permettent d’inscrire votre appareil StorSimple, configurer l’interface réseau sur votre périphérique, installer certains types de mises à jour, résoudre les problèmes de votre périphérique en accédant à la prise en charge session et modifier l’état du périphérique. Vous pouvez accéder à Windows PowerShell pour StorSimple en vous connectant à la console série ou à l’aide de communication à distance de Windows PowerShell.
- **Les applets de commande azure PowerShell StorSimple** – une collection d’applets de commande Windows PowerShell qui vous permettent d’automatiser les tâches de niveau de service et de la migration à partir de la ligne de commande. Pour plus d’informations sur les applets de commande PowerShell de Azure pour StorSimple, accédez à la [référence de l’applet de commande](https://msdn.microsoft.com/library/dn920427.aspx).
- **Gestionnaire de snapshots StorSimple** – un composant logiciel enfichable MMC qui utilise des groupes de volumes et le Service cliché instantané de Volume Windows pour générer des sauvegardes cohérentes avec les applications. En outre, vous pouvez utiliser StorSimple Gestionnaire de snapshots pour créer des clones et des plannings de sauvegarde ou de restauration des volumes. 
- **Carte StorSimple pour SharePoint** – un outil qui étend de manière transparente Microsoft Azure StorSimple stockage et protection des données pour les batteries de serveurs SharePoint Server, tout en StorSimple stockage visible et facile à gérer à partir du portail de l’Administration centrale de SharePoint.

Le schéma ci-dessous donne une vue d’ensemble de l’architecture de Microsoft Azure StorSimple et les composants.

![Architecture de StorSimple](./media/storsimple-overview/overview-big-picture.png)

Les sections suivantes décrivent chacun de ces composants en détail et expliquent comment la solution réorganise les données alloue le stockage et facilite la gestion du stockage et protection des données. La dernière section fournit des définitions pour certains termes importants et des concepts liés aux composants de StorSimple et de leur gestion.

## <a name="storsimple-device"></a>Dispositif de StorSimple

Le périphérique de Microsoft Azure StorSimple est une baie de stockage sur site hybride qui fournit le stockage principal et iSCSI des accès aux données stockées sur celui-ci. Il gère la communication avec le stockage en nuage et aide à garantir la sécurité et la confidentialité de toutes les données stockées sur la solution Microsoft Azure StorSimple.

Le périphérique StorSimple inclut SSDs et disques durs de lecteurs de disque dur, ainsi que la prise en charge pour le basculement automatique et de mise en cluster. Il contient un processeur partagé, le stockage partagé et deux contrôleurs en miroir. Chaque contrôleur offre les fonctionnalités suivantes :

- Connexion à un ordinateur hôte
- Jusqu'à six ports réseau pour se connecter au réseau local (LAN)
- Surveillance du matériel
- Mémoire vive de non volatile (NVRAM), qui conserve des informations même si l’alimentation est interrompue
- Cluster prenant en charge les mise à jour pour gérer les mises à jour logicielles sur les serveurs dans un cluster de basculement afin que les mises à jour offrent peu ou pas d’effet sur la disponibilité du service
- Service de cluster, qui fonctionne comme un cluster back-end, offrant une haute disponibilité et en réduisant les effets négatifs potentiels susceptibles de se produire si un disque dur SSD tombe en panne ou est mis hors connexion

Qu’un seul contrôleur est actif à n’importe quel point dans le temps. Si le contrôleur actif échoue, le deuxième contrôleur devient automatiquement actif. 

Pour plus d’informations, accédez à [l’état et des composants matériels de StorSimple](storsimple-monitor-hardware-status.md).

## <a name="storsimple-virtual-device"></a>Périphérique virtuel de StorSimple

Vous pouvez utiliser StorSimple pour créer un périphérique virtuel qui réplique l’architecture et les fonctionnalités du périphérique de stockage physique hybride. Périphérique virtuel StorSimple (également connu comme le StorSimple Appliance virtuelle) s’exécute sur un nœud dans une machine virtuelle Azure. (Un périphérique virtuel peut uniquement être créé sur une machine virtuelle Azure. Vous ne pouvez pas créer une sur un serveur local ou sur un périphérique StorSimple.) 

Le périphérique virtuel présente les caractéristiques suivantes :

- Il se comporte comme un matériel physique et peut offrir une interface iSCSI aux ordinateurs virtuels dans le nuage. 
- Vous pouvez créer un nombre illimité de périphériques virtuels dans le nuage et activer ou désactiver selon vos besoins. 
- Il permet de simuler les environnements sur site dans les scénarios de test, de développement et de reprise après sinistre et peut vous aider à la récupération au niveau de l’élément à partir de sauvegardes. 

Avec la mise à jour 2 et versions ultérieures, le périphérique virtuel StorSimple est disponible en deux modèles : le périphérique 8010 (anciennement appelé le modèle 1100) et le périphérique 8020. Le périphérique 8010 a une capacité maximale de 30 To. Le périphérique 8020, qui tire parti du stockage de prime Azure, a une capacité maximale de 64 To. (Au niveau local, stockage de prime Azure stocke les données sur SSDs que stockage standard stocke les données sur les disques durs.) Notez que vous devez disposer d’un compte de stockage Azure premium pour utiliser le stockage de la prime. Pour plus d’informations sur le stockage de la prime, accédez à [prime stockage : stockage hautes performances pour les charges de travail de Machine virtuelle Azure](../storage/storage-premium-storage.md).

Pour plus d’informations sur le périphérique virtuel StorSimple, accédez à [déployer et gérer un périphérique virtuel de StorSimple dans Azure](storsimple-virtual-device-u2.md).

## <a name="storsimple-manager-service"></a>Service Gestionnaire de StorSimple

Microsoft Azure StorSimple fournit une interface utilisateur basée sur le web (le service Gestionnaire de StorSimple) qui vous permet de gérer le centre de données et de stockage en nuage de façon centralisée. Vous pouvez utiliser le service Gestionnaire de StorSimple pour effectuer les tâches suivantes :

- Configurer les paramètres système pour les périphériques StorSimple.
- Configurer et gérer les paramètres de sécurité pour les périphériques StorSimple.
- Configurez les propriétés et les informations d’identification du nuage.
- Configuration et gestion des volumes sur un serveur.
- Configurer des groupes de volumes.
- Sauvegarder et restaurer des données.
- Surveiller les performances.
- Passez en revue les paramètres système et identifier les problèmes possibles.

Vous pouvez utiliser le service Gestionnaire de StorSimple pour effectuer toutes les tâches d’administration, à l’exception de celles qui nécessitent le système, telles que la configuration initiale et l’installation des mises à jour.

Pour plus d’informations, accédez à [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell pour StorSimple

Windows PowerShell pour StorSimple fournit une interface de ligne de commande que vous pouvez utiliser pour créer et gérer le service de Microsoft Azure StorSimple et configurer et surveiller les périphériques du StorSimple. Il s’agit d’une interface de ligne de commande basé sur Windows PowerShell, qui inclut des applets de commande dédiée pour la gestion de votre périphérique StorSimple. Windows PowerShell pour StorSimple dispose de fonctionnalités qui vous permettent de :

- Inscrire un appareil.
- Configurer l’interface réseau sur un périphérique.
- Installer certains types de mises à jour.
- Dépannage de votre périphérique en accédant à la session de prise en charge.
- Modifier l’état du périphérique.

Vous pouvez accéder à Windows PowerShell pour StorSimple à partir d’une console série (sur un ordinateur hôte connecté directement au périphérique) ou à distance à l’aide de communication à distance de Windows PowerShell. Notez que certains de Windows PowerShell pour les tâches StorSimple, telles que l’enregistrement des périphériques initiaux, ne peut être effectuée que sur la console série. 

Pour plus d’informations, accédez à [Windows PowerShell d’utilisation de StorSimple administrer votre périphérique](storsimple-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Applets de commande PowerShell StorSimple Azure

Les applets de commande Azure PowerShell StorSimple sont une collection d’applets de commande Windows PowerShell qui vous permettent d’automatiser les tâches de niveau de service et de la migration à partir de la ligne de commande. Pour plus d’informations sur les applets de commande PowerShell de Azure pour StorSimple, accédez à la [référence de l’applet de commande](https://msdn.microsoft.com/library/dn920427.aspx).

## <a name="storsimple-snapshot-manager"></a>Gestionnaire de capture instantanée StorSimple

StorSimple Gestionnaire de snapshots est un composant logiciel enfichable Microsoft Management Console (MMC) qui vous permet de créer cohérente, des copies de sauvegarde de point-à-temps des locaux et les données de nuage. Le composant logiciel enfichable s’exécute sur un hôte Windows Server. Vous pouvez utiliser le Gestionnaire de capture instantanée de StorSimple à :

- Configurer, sauvegarder et supprimer des volumes.
- Configurer des groupes de volumes afin de garantir que les données sauvegardées est cohérent pour l’application.
- Gérer les stratégies de sauvegarde afin que les données soient sauvegardées selon une planification prédéfinie et stockées dans un emplacement désigné (localement ou dans le nuage).
- Restauration des volumes et des fichiers individuels.

Les sauvegardes sont capturés sous forme d’instantanés, qui enregistrent uniquement les modifications apportées depuis le dernier instantané a été pris et nécessitent beaucoup moins d’espace de stockage que les sauvegardes complètes. Vous pouvez créer des plannings de sauvegarde ou effectuer des sauvegardes immédiates en fonction des besoins. En outre, vous pouvez utiliser Gestionnaire de snapshots de StorSimple pour établir des stratégies de rétention qui contrôlent le nombre de captures instantanées seront enregistrées. Si vous avez besoin par la suite à restaurer les données à partir d’une sauvegarde, vous permet de StorSimple Gestionnaire de snapshots vous sélectionnez à partir du catalogue local ou les snapshots de nuage. 

Si un sinistre se produit ou si vous devez restaurer des données pour une autre raison, Gestionnaire de snapshots StorSimple restaure incrémentielle qu’il est nécessaire. Restauration des données ne nécessite pas que vous arrêtez l’ensemble du système lors de restaurer un fichier, remplacez le matériel ou de déplacer des opérations vers un autre site.

Pour plus d’informations, consultez [qu’est le Gestionnaire de snapshots StorSimple ?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Carte StorSimple pour SharePoint

Microsoft Azure StorSimple comprend la carte de StorSimple pour SharePoint, un composant optionnel qui étend en toute transparence des fonctions de protection des données et de stockage StorSimple aux batteries de serveurs SharePoint. La carte fonctionne avec un fournisseur de stockage d’objets Blob distants (RBS) et de la fonctionnalité de RBS de SQL Server, ce qui vous permet de déplacer des objets BLOB vers un serveur sauvegardé par le système Microsoft Azure StorSimple. Microsoft Azure StorSimple stocke les données BLOB localement ou dans le nuage, basée sur l’utilisation.

La carte StorSimple pour SharePoint est gérée depuis le portail de l’Administration centrale de SharePoint. Par conséquent, SharePoint administration reste centralisée, et tout le stockage apparaît dans la batterie de serveurs SharePoint.

Pour plus d’informations, accédez à la [Carte de StorSimple pour SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Technologies de gestion de stockage
 
Le périphérique StorSimple dédié, périphérique virtuel et autres composants, Microsoft Azure StorSimple utilise les technologies suivantes de logiciels pour fournir un accès rapide aux données et de réduire la consommation de stockage :

- [Hiérarchisation du stockage automatique](#automatic-storage-tiering) 
- [Provisionnement fin](#thin-provisioning) 
- [Déduplication et compression](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Hiérarchisation du stockage automatique

Microsoft Azure StorSimple Réorganise automatiquement les données en niveaux logiques selon l’usage actuel, l’âge et la relation à d’autres données. Les données qui ne sont plus actives sont stockées localement, tandis que les données les moins actives et inactives sont automatiquement migrées vers le nuage. Le diagramme suivant illustre cette approche.
 
![Niveaux de stockage StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Pour permettre un accès rapide, StorSimple stocke les données très actives (données à chaud) sur SSDs dans le périphérique StorSimple. Il stocke les données qui sont utilisées parfois (à chaud des données) sur des disques durs dans le périphérique ou sur des serveurs dans le centre de données. Il déplace les données inactives, les données de sauvegarde, et les données conservées pour archivage ou à des fins de conformité dans le nuage. 

>[AZURE.NOTE] Mise à jour 2 ou version ultérieure, vous pouvez spécifier un volume épinglé que localement, auquel cas les données restent sur le périphérique local et ne sont pas monté en cascade dans le nuage. 

StorSimple ajuste et réorganise les données et modifier les affectations de stockage en tant que modèles d’utilisation. Par exemple, certaines informations peuvent devenir moins actifs au fil du temps. Dès qu’elles seront progressivement moins actif, il migré à partir de SSDs aux disques durs, puis vers le nuage. Si ces mêmes données redevient actives, il est migré vers le périphérique de stockage.

Le processus de hiérarchisation du stockage se déroule comme suit :

1. Un administrateur système définit un compte de stockage cloud Microsoft Azure.
2. L’administrateur utilise la console série et le service de gestionnaire de StorSimple (qui exécute dans Azure portal classique) pour configurer le serveur de fichier et du périphérique, création de règles de protection des données et des volumes. Ordinateurs locaux (par exemple, des serveurs de fichiers) permet d’accéder au périphérique StorSimple Internet Small Computer System Interface (iSCSI).
3. Initialement, StorSimple stocke les données sur la couche SSD rapide du périphérique.
4. La couche SSD approche de capacité, StorSimple deduplicates et compresse les blocs de données plus anciennes et les déplace vers le niveau du disque dur.
5. La capacité du disque dur couche approches StorSimple crypte les plus anciens blocs de données et les envoie en toute sécurité sur le compte de stockage Microsoft Azure via HTTPS.
6. Microsoft Azure crée plusieurs répliques des données dans son centre de données et dans un centre de données à distance, garantissant que les données peuvent être récupérées en cas de sinistre. 
7. Lorsque le serveur de fichiers demande des données stockées dans le nuage, StorSimple retourne en toute transparence et stocke une copie sur la couche SSD du périphérique StorSimple.

### <a name="thin-provisioning"></a>Provisionnement fin

Provisionnement fin est une technologie de virtualisation dans lequel stockage disponible semble dépasser les ressources physiques. Au lieu de la réservation d’espace de stockage suffisant à l’avance, StorSimple utilise le provisionnement fin à allouer juste assez d’espace pour répondre aux besoins actuels. La nature élastique de stockage en nuage facilite cette approche car StorSimple peut augmenter ou diminuer le stockage en nuage pour satisfaire aux demandes changeantes. 

>[AZURE.NOTE] Les volumes ajoutés localement ne sont pas exactement provisionnés. Stockage alloué pour un volume local seul est mis en service dans son intégralité lorsque le volume est créé.

### <a name="deduplication-and-compression"></a>Déduplication et compression

Microsoft Azure StorSimple utilise la compression des données et de déduplication afin de réduire davantage les besoins de stockage.

Déduplication permettant de réduire le montant global des données stockées en éliminant la redondance dans l’ensemble de données stockée. Comme les informations changent, StorSimple ignore les données non modifiées et capture uniquement les modifications. De plus, StorSimple permet de réduire la quantité de données stockées en identifiant et en supprimant les informations inutiles. 

>[AZURE.NOTE] Données sur les volumes ajoutés localement ne sont pas dédupliquées ou compressées. Toutefois, les sauvegardes de volumes ajoutés localement sont dédupliqués et compressés.

## <a name="storsimple-workload-summary"></a>Charge de travail StorSimple résumé

Un résumé des charges de travail StorSimple pris en charge est sous forme de tableau ci-dessous.

| Scénario                  | Charge de travail                | Prise en charge |  Restrictions                                  | Version              |
|---------------------------|-------------------------|-----------|------------------------------------------------|----------------------|
| Collaboration             | Le partage de fichiers            | Oui       |                                                | Toutes les versions         |
| Collaboration             | Le partage de fichiers distribués| Oui       |                                                | Toutes les versions         |
| Collaboration             | SharePoint              | Oui *      |Prise en charge uniquement les volumes ajoutés localement      | Mise à jour 2 et versions ultérieur   |
| Archivage                  | L’archivage de fichiers simple   | Oui       |                                                | Toutes les versions         |
| Virtualisation            | Ordinateurs virtuels        | Oui *      |Prise en charge uniquement les volumes ajoutés localement      | Mise à jour 2 et versions ultérieur   |
| Base de données                  | SQL                     | Oui *      |Prise en charge uniquement les volumes ajoutés localement      | Mise à jour 2 et versions ultérieur   |
| Vidéo-surveillance        | Vidéo-surveillance      | Oui *       |Prise en charge lorsque StorSimple est dédié uniquement à cette charge de travail| Mise à jour 2 et versions ultérieur   |
| Sauvegarde                    | Sauvegarde de la cible principale | Oui *      |Prise en charge lorsque StorSimple est dédié uniquement à cette charge de travail| Mise à jour 3 et version ultérieure |
| Sauvegarde                    | Sauvegarde de la cible secondaire | Oui *      |Prise en charge lorsque StorSimple est dédié uniquement à cette charge de travail| Mise à jour 3 et version ultérieure |

*Oui & #42 ; -Restrictions et les instructions de la solution doivent être appliquées.*

Les charges de travail suivants ne sont pas pris en charge par les périphériques série 8000 de StorSimple. Si déployé sur StorSimple, ces charges de travail entraînera une configuration non prise en charge.

-  Imagerie médicale
-  Exchange
-  VDI
-  Oracle
-  SAP
-  Données volumineuses
-  Distribution de contenu
-  Démarrage à partir du SCSI

Voici une liste des composants d’infrastructure de StorSimple pris en charge. 

| Scénario | Charge de travail      | Prise en charge |  Restrictions                                 | Version      |
|----------|---------------|-----------|-----------------------------------------------|--------------|
| Général  | Voie Express | Oui       |                                               |Toutes les versions |
| Général  | DataCore FC   | Oui *       |Prise en charge avec DataCore SANsymphony            | Toutes les versions |
| Général  | DFSR          | Oui *      |Prise en charge uniquement les volumes ajoutés localement     | Toutes les versions |
| Général  | L’indexation      | Oui *       |Pour les volumes hiérarchisés, seules les métadonnées d’indexation est pris en charge (aucune donnée).<br>Pour les volumes ajoutés localement, une indexation complète est prise en charge.| Toutes les versions |
| Général  | Protection contre les virus    | Oui *       |Pour les volumes hiérarchisés, uniquement analyse lors de l’ouverture et fermeture est pris en charge.<br> Pour les volumes ajoutés localement, l’analyse complète est prise en charge.| Toutes les versions |

*Oui & #42 ; -Restrictions et les instructions de la solution doivent être appliquées.*

## <a name="storsimple-terminology"></a>Terminologie de la StorSimple 

Avant de déployer votre solution Microsoft Azure StorSimple, nous vous conseillons d’étudier les définitions et les termes suivants.

### <a name="key-terms-and-definitions"></a>Les définitions et les termes clés

| Terme (acronyme ou abréviation) | Description |
| ------------------------------ | ---------------- |
| enregistrement du contrôle d’accès (ACR)    | Un enregistrement associé à un volume sur votre appareil Microsoft Azure StorSimple qui détermine quels hôtes peuvent s’y connecter. La détermination est basée sur l’iSCSI nom qualifié (IQN) des hôtes qui sont connectent à votre périphérique de StorSimple (contenues dans l’ACR).|
| AES-256                        | Un algorithme Advanced Encryption Standard (AES) 256 bits pour crypter les données lorsqu’il se déplace vers et à partir du cloud. |
| taille d’unité d’allocation (Australie)     | Systèmes de fichiers la plus petite quantité d’espace disque qui peut être alloué à un fichier dans Windows. Si une taille de fichier n’est pas un multiple pair de la taille de cluster, un espace supplémentaire doit être utilisé pour contenir le fichier (jusqu’au multiple suivant de la taille de cluster) qui en résulte dans l’espace perdu et la fragmentation du disque dur. <br>L’Australie recommandée pour Azure StorSimple volumes est de 64 Ko, car il fonctionne bien avec les algorithmes de déduplication.|
| hiérarchisation du stockage automatisé      | Déplace automatiquement les données les moins actives de SSDs aux disques durs, puis vers un niveau dans le nuage et puis l’activation de la gestion de tout le stockage à partir d’une interface utilisateur centrale.|
| catalogue de sauvegarde | Collection des sauvegardes, généralement liées par le type d’application qui a été utilisé. Cette collection est affichée dans la page catalogue de sauvegarde du service Gestionnaire de StorSimple l’interface utilisateur.|
| fichier sauvegarde de catalogue             | Un fichier contenant une liste des snapshots disponibles actuellement stockés dans la base de données de sauvegarde du Gestionnaire de capture instantanée StorSimple. |
| stratégie de sauvegarde                   | Sélection de volumes, le type de sauvegarde et un calendrier qui vous permet de créer des sauvegardes selon une planification prédéfinie.|
| objets binaires volumineux (BLOB)    | Une collection de données binaires stockées sous la forme d’une entité unique dans un système de gestion de base de données. Objets BLOB est généralement des images, audio ou autres objets multimédias, bien que le code exécutable parfois binaire est stocké comme un objet BLOB.|
| Challenge Handshake Authentication Protocol (CHAP) | Protocole utilisé pour authentifier l’homologue d’une connexion, basée sur l’hôte partage un mot de passe ou de secret. CHAP peut être unidirectionnelle ou mutuelle. Avec l’authentification CHAP unidirectionnelle, la cible authentifie l’initiateur. Le protocole CHAP mutuel requiert que la cible authentifie l’initiateur et que l’initiateur authentifie la cible. | 
| Clone                          | Copie d’un volume. |
|Cloud comme une couche (CaaT)          | Stockage intégré sous la forme d’un niveau au sein de l’architecture de stockage de sorte que tout le stockage apparaît comme faisant partie du réseau de stockage d’une entreprise en nuage.|
| fournisseur de services de cloud (CSP)   | Un fournisseur de services informatiques en nuage.|
| capture instantanée du nuage                 | Copie point-à-temps des données d’un volume qui sont stockées dans le nuage. Un instantané du nuage est équivalent à une capture instantanée répliquée sur un système de stockage hors site différents. Les instantanés de nuage sont particulièrement utiles dans les scénarios de récupération d’urgence.|
| clé de cryptage de stockage cloud   | Un mot de passe ou une clé utilisée par l’appareil de StorSimple pour accéder aux données cryptées envoyées par votre appareil vers le nuage.|
| mise à jour prenant en charge les clusters         | Gestion des mises à jour logicielles sur les serveurs dans un cluster de basculement, afin que les mises à jour offrent peu ou pas d’effet sur la disponibilité du service.|
| DataPath                       | Une collection d’unités fonctionnelles qui effectuent des opérations de traitement des données reliées entre elles.|
| désactiver                     | Une action permanente qui rompt la connexion entre le périphérique StorSimple et le service en nuage associé. Instantanés de nuage du périphérique restent après ce processus et peuvent être clonées ou utilisés pour la reprise après sinistre.|
| la mise en miroir de disque                 | Réplication des volumes de disque logique sur séparé dur lecteurs en temps réel afin de garantir une disponibilité permanente.|
| la mise en miroir de disque dynamique         | Réplication de volumes de disque logiques sur des disques dynamiques.|
| disques dynamiques                  | Un format de volume de disque qui utilise le Gestionnaire de disque logique (LDM) pour stocker et gérer les données sur plusieurs disques physiques. Les disques dynamiques peuvent être agrandies afin de fournir davantage d’espace libre.|
| Étendue boîtier de grappe de disques (EBOD) | Un boîtier secondaire de votre périphérique Microsoft Azure StorSimple qui contient des disques de disque dur supplémentaire de stockage supplémentaire.|
| mise en service en matières grasses               | Un conventionnelle du provisionnement du stockage dans le stockage est alloué en fonction de prévoir les besoins (et est généralement au-delà des besoins actuels). Voir aussi le *provisionnement fin*.|
| lecteur de disque dur (HDD)          | Un lecteur qui utilise des plateaux pivotant pour stocker des données.|
| stockage en nuage hybride           | Une architecture de stockage qui utilise des ressources locales et hors site, y compris le stockage en nuage.|
| Internet Small Computer System Interface (iSCSI) | Une stockage basé sur le protocole Internet IP – norme de communication réseau pour relier les équipements de stockage de données ou des installations.|
| initiateur iSCSI                 | Un composant logiciel qui permet à un ordinateur hôte exécutant Windows pour se connecter à un réseau de stockage iSCSI externe.|
| iSCSI nom qualifié (IQN)      | Un nom unique qui identifie un initiateur ou une cible iSCSI.|
| cible iSCSI                    | Un composant logiciel qui fournit des sous-systèmes de disques dans les réseaux de stockage SAN iSCSI centralisé.|
| Live d’archivage                  | Une approche de stockage dans lequel les données archivage sont accessibles tout le temps (il n'est pas stocké hors site sur bande, par exemple). Microsoft Azure StorSimple utilise l’archivage direct.|
|volume localement épinglé | un volume qui réside sur le périphérique et n’est jamais monté en cascade dans le nuage. |
| instantané local                  | Copie de données d’un volume qui sont stockées sur le périphérique Microsoft Azure StorSimple point-à-temps.|
| Microsoft Azure StorSimple      | Une solution puissante, consistant en un équipement de stockage de centre de données et un logiciel qui permet aux départements informatiques de tirer parti de stockage en nuage comme s’il s’agissait de stockage du centre de données. StorSimple simplifie la protection des données et gestion des données tout en réduisant les coûts. La solution consolide la reprise après sinistre, d’archivage, sauvegarde et stockage récupération principale (DR) grâce à l’intégration transparente avec le nuage. En associant la gestion des données SAN de stockage et de nuage sur une plate-forme d’entreprise, les périphériques de StorSimple permettent de vitesse, de simplicité et de fiabilité pour tous les besoins en matière de stockage.|
| Alimentation et refroidissement Module (PCM)  | Composants matériels de votre périphérique StorSimple comprenant les blocs d’alimentation et le ventilateur de refroidissement, par conséquent, le nom de puissance et module de refroidissement. Le boîtier principal du périphérique a deux 764W PCMs que le boîtier EBOD a deux 580 w PCMs.|
| boîtier principal               | Boîtier principal de votre périphérique StorSimple qui contient les contrôleurs de plate-forme d’application.|
| objectif de temps de récupération (RTO)   | La quantité maximale de temps qui doit être consenti avant un processus métier ou un système est entièrement restaurée après un sinistre.| 
|série connecté SCSI (SAS)       | Type de lecteur de disque dur (HDD).|
| clé de cryptage de données de service     | Une clé est mises à la disposition de tout équipement StorSimple nouveau inscrit avec le service Gestionnaire de StorSimple. Les données de configuration transférées entre le service Gestionnaire de StorSimple et le périphérique sont cryptées à l’aide d’une clé publique et peuvent ensuite être décryptées uniquement sur le périphérique à l’aide d’une clé privée. Clé de cryptage de données service permet au service obtenir cette clé privée pour le déchiffrement.|
| clé d’enregistrement de service        | Une clé qui permet d’enregistrer le périphérique StorSimple avec le service Gestionnaire de StorSimple pour qu’il apparaisse dans le portail Azure classique pour de nouvelles actions de gestion.|
| Small Computer System Interface (SCSI) | Un ensemble de normes pour physiquement connecter les ordinateurs et passage de données entre eux.|
| lecteur de mémoire Flash (SSD)         | Un disque qui ne contient aucuns pièces mobiles ; par exemple, un lecteur flash.|
| compte de stockage                 | Un ensemble d’informations d’identification d’accès lié à votre compte de stockage pour un fournisseur de services de cloud donné.| 
| Carte StorSimple pour SharePoint| Un composant de Microsoft Azure StorSimple qui étend de manière transparente StorSimple stockage et protection des données pour les batteries de serveurs SharePoint.|
| Service Gestionnaire de StorSimple      | Une extension du portail Azure classique qui permet de gérer vos périphériques virtuels et les StorSimple d’Azure sur site.|
| Gestionnaire de capture instantanée StorSimple     | Un composant logiciel enfichable Microsoft Management Console (MMC) pour gérer les opérations de sauvegarde et de restauration de Microsoft Azure StorSimple.|
| effectuer la sauvegarde                     | Une fonctionnalité qui permet à l’utilisateur de prendre une sauvegarde interactive d’un volume. Il s’agit d’une autre façon d’une sauvegarde manuelle d’un volume au lieu de prendre une sauvegarde automatisée via une stratégie définie.|
| provisionnement fin               | Une méthode d’optimisation de l’efficacité avec laquelle l’espace de stockage disponible est utilisée dans les systèmes de stockage. Dans le contrôle de l’approvisionnement, le stockage est alloué entre plusieurs utilisateurs en fonction de l’espace minimum requis par chaque utilisateur à un moment donné. Voir aussi *fat mise en service*.|
| montage en cascade | Organisation des données dans des regroupements logiques, en fonction de l’usage actuel, l’âge et la relation à d’autres données. StorSimple organise automatiquement les données de niveaux.   |
| volume                          | Zones de stockage logique présentés sous la forme de disques. StorSimple volumes correspondent aux volumes montés par l’hôte, y compris ceux découverts à l’aide d’iSCSI et un dispositif de StorSimple.|
 | conteneur de volume                | Un groupe de volumes et les paramètres qui s’y appliquent. Tous les volumes de votre périphérique StorSimple sont regroupés dans des conteneurs de volume. Les paramètres de conteneur de volume incluent les comptes de stockage, les paramètres de cryptage pour les données envoyées vers le cloud avec des clés de chiffrement associées et de la bande passante utilisée pour les opérations impliquant le nuage.|
| groupe de volumes                    | Dans le Gestionnaire de snapshots de StorSimple, un groupe de volumes est une collection de volumes configurés pour faciliter le processus de sauvegarde.|
| Service de cliché instantané de volume (VSS)| Un service de système d’exploitation Windows Server qui facilite la cohérence des applications en communiquant avec des applications prenant en charge VSS pour coordonner la création d’instantanés incrémentiels. VSS permet de s’assurer que les applications sont temporairement inactives lorsque des instantanés.|
| Windows PowerShell pour StorSimple | Une interface de ligne de commande basé sur Windows PowerShell utilisée pour gérer et exploiter votre périphérique StorSimple. Tout en conservant certaines fonctionnalités de base de Windows PowerShell, cette interface a dédié applets de commande supplémentaires qui s’adressent à la gestion d’un périphérique StorSimple.|


## <a name="next-steps"></a>Étapes suivantes

Obtenir des informations sur la [sécurité de StorSimple](storsimple-security.md).




 

 
