<properties
   pageTitle="Conseils techniques : récupération de défaillances locales dans Azure | Microsoft Azure"
   description="L’article sur la présentation et conception résistante et très performante, applications à tolérance de pannes, ainsi que de planification de reprise après sinistre sur les défaillances locales dans Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-local-failures-in-azure"></a>Guide technique de résilience Azure : récupération de défaillances locales dans Azure

Il existe deux menaces principales à la disponibilité des applications :

* La défaillance des périphériques, tels que les lecteurs et les serveurs
* L’épuisement des ressources critiques telles que compute dans des conditions de charge de pointe

Azure fournit une combinaison de gestion des ressources, l’élasticité, l’équilibrage de charge et de partitionnement pour activer la haute disponibilité dans ces circonstances. Certaines de ces fonctionnalités sont effectuées automatiquement pour tous les services Azure. Cependant, dans certains cas, le développeur d’application doit effectuer un travail supplémentaire de les exploiter.

##<a name="cloud-services"></a>Services en nuage

Les Services en nuage Azure se compose de collections d’un ou plusieurs rôles web ou travailleur. Une ou plusieurs instances d’un rôle peuvent s’exécuter simultanément. La configuration détermine le nombre d’instances. Instances de rôle sont contrôlés et gérés via un composant appelé contrôleur de tissu. Le contrôleur de tissu détecte et répond automatiquement aux défaillances de logiciels et de matériel.

Chaque instance de rôle s’exécute dans sa propre machine virtuelle (VM) et communique avec son contrôleur de tissu via un agent de l’invité. L’agent de l’invité collecte les métriques de ressource et le nœud, y compris la machine virtuelle l’utilisation, état, journaux, utilisation des ressources, des exceptions et les conditions d’échec. Le contrôleur de tissu interroge l’agent invité à intervalles configurables et redémarrage de la machine virtuelle si l’agent de l’invité ne répond pas. En cas de défaillance matérielle, le contrôleur de tissu associé déplace toutes les instances de rôle affecté à un nœud de matériel nouveau et reconfigure le réseau pour acheminer le trafic il.

Pour bénéficier de ces fonctionnalités, les développeurs doivent s’assurer que tous les rôles du service évitent le stockage de l’état sur les instances de rôle. Au lieu de cela, toutes les données persistantes doivent être accessibles à partir d’un stockage durable, par exemple stockage Azure ou SQL Azure. Ainsi, tous les rôles traiter les demandes. Cela signifie également que les instances de rôle peuvent tomber en panne à tout moment sans créer des incohérences dans l’état transitoire ou permanente du service.

L’obligation de stocker l’état en externe aux rôles a plusieurs conséquences. Par exemple, cela signifie que toutes les modifications associées à une table de stockage Azure doivent être modifiées en une seule transaction de groupe à l’entité, si possible. Bien entendu, il n’est pas toujours possible d’apporter toutes les modifications en une seule transaction. Vous devez prendre soin de s’assurer que les défaillances d’instance de rôle ne provoquent pas de problèmes lorsqu’ils interrompent le fonctionnement des longues qui couvrent deux ou plusieurs des mises à jour de l’état persistant du service. Si un autre rôle tente de relancer une opération de ce type, il doit anticiper et gérer le cas où le travail a été partiellement effectué.

Par exemple, considérez un service qui répartit les données sur plusieurs magasins. Si un rôle worker tombe en panne pendant qu’il est déplacement un partagé, la délocalisation de l’éclater peut ne pas termine. Ou la délocalisation peut être répétée à l’origine par un rôle de travail différents, ce qui peut provoquer des données orphelines ou corruption des données. Pour éviter tout problème, les opérations de longue durée qui doivent être une ou les deux opérations suivantes :

 * *Idempotent*: reproductibles sans effets secondaires. Pour être idempotent, une opération de longue durée qui doit avoir le même effet, quel que soit le nombre de fois qu’il est exécuté, même si elle est interrompue pendant son exécution.
 * *Redémarrage de façon incrémentielle*: puisse continuer à partir du point d’échec plus récent. Pour être incrémentielle peut être redémarrée, une opération de longue durée qui doit comporter une séquence du plus petit des opérations atomiques. Il doit également enregistrer sa progression dans le stockage durable afin que chaque appel suivant prélève où son prédécesseur s’est arrêté.

Enfin, toutes les opérations de longue durée qui doivent être appelées à plusieurs reprises jusqu'à ce qu’ils réussissent. Par exemple, une opération de mise en service peut être placée dans une file d’attente Azure et ensuite supprimée de la file d’attente par un rôle worker uniquement quand elle réussit. Le garbage collection peut être nécessaire de créer des données qui l’interruption des opérations de nettoyage.

###<a name="elasticity"></a>Élasticité

Nombre d’instances en cours d’exécution pour chaque rôle initial est déterminé dans chaque configuration des rôles. Les administrateurs doivent configurer initialement de chaque rôle pour exécuter avec deux instances ou plus en fonction de la charge attendue. Mais vous pouvez faire évoluer des instances de rôle ou vers le bas comme l’utilisation de modèles de modifier. Vous pouvez le faire manuellement dans Azure portal, ou vous pouvez automatiser le processus à l’aide de Windows PowerShell, l’API de gestion des services ou des outils tiers. Pour plus d’informations, reportez-vous [à l’échelle d’une application](../cloud-services/cloud-services-how-to-scale.md).

###<a name="partitioning"></a>Partitionnement

Le contrôleur de tissu Azure utilise deux types de partitions :

* Une *mise à jour de domaine* est utilisé pour mettre à niveau les instances de rôle d’un service dans les groupes. Azure déploie des instances de service dans plusieurs domaines de la mise à jour. Pour une mise à jour en place, le contrôleur de tissu entraîne dans le domaine d’une mise à jour toutes les instances, les met à jour, puis redémarre les avant de passer à la mise à jour domaine suivant. Cette approche empêche l’ensemble du service indisponible pendant le processus de mise à jour.
* Un *domaine d’erreur* définit les points potentiels de défaillance matérielle ou de réseau. Pour n’importe quel rôle qui a plus d’une instance, le contrôleur de tissu garantit que les instances sont distribués sur plusieurs domaines d’erreur, afin d’éviter les défaillances matérielles isolé d’interrompre le service. Domaines d’erreur régissent toute exposition à des défaillances des serveurs et cluster.

L' [Azure accord de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/) garantit que lorsque deux ou plusieurs instances de rôle web sont déployés sur les différentes pannes et mise à niveau des domaines, ils vous disposer de connectivité externe au moins 99,95 % du temps. Contrairement aux domaines de la mise à jour, il n’existe aucun moyen de contrôler le nombre de domaines d’erreur. Azure alloue des domaines d’erreur automatiquement et distribue des instances de rôle entre eux. Au moins deux premières instances de chaque rôle sont placés dans différentes pannes et mise à niveau des domaines pour garantir que n’importe quel rôle avec au moins deux instances satisfera le contrat SLA. Ceci est représenté dans le diagramme suivant.

![Vue simplifiée de pannes du domaine](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png)

###<a name="load-balancing"></a>Équilibrage de la charge

Tout le trafic entrant à un rôle web passe par un équilibreur de charge sans état, ce qui distribue les requêtes des clients entre les instances de rôle. Les instances de rôle individuel n’ont pas d’adresses IP publiques, et ils ne sont pas directement adressables à partir d’Internet. Rôles Web sont sans état, afin que toute demande de client peut être routé vers n’importe quelle instance de rôle. Un événement [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) est déclenché toutes les 15 secondes. Vous pouvez l’utiliser pour indiquer si le rôle est prêt à recevoir du trafic, ou s’il est occupé et devez être retiré de la rotation de l’équilibrage de la charge.

##<a name="virtual-machines"></a>Ordinateurs virtuels

Les Machines virtuelles Azure diffère de plate-forme que service (PaaS) calcul des rôles à plusieurs égards, en ce qui concerne la haute disponibilité. Dans certains cas, vous devez effectuer un travail supplémentaire pour garantir une haute disponibilité.

###<a name="disk-durability"></a>Durabilité de disque

Contrairement aux instances de rôle PaaS, les données stockées sur les disques de l’ordinateur virtuel sont persistantes même lorsque l’ordinateur virtuel est déplacé. Les machines virtuelles Azure utiliser des disques VM qui existent en tant que BLOB dans le stockage Azure. En raison des caractéristiques de disponibilité du stockage Azure, les données stockées sur les disques d’un ordinateur virtuel sont également hautement disponibles.

Notez que le lecteur D (dans les ordinateurs virtuels de Windows) est l’exception à cette règle. Le lecteur D est le stockage en réalité physique sur le serveur en rack qui héberge l’ordinateur virtuel et ses données seront perdues si la machine virtuelle est recyclée. Le lecteur D est conçu pour le stockage temporaire uniquement. Sous Linux, Azure « généralement » (mais pas toujours) expose le disque temporaire local en tant que périphérique en mode bloc /dev/sdb. Il est souvent monté par l’Agent de Linux Azure comme points de montage/mnt ou de /mnt/resource (configurables via /etc/waagent.conf).

###<a name="partitioning"></a>Partitionnement

Azure en mode natif comprend les couches dans une application de plate-forme PaaS (les rôles web et worker) et par conséquent peut correctement les distribuer sur plusieurs domaines de panne et de la mise à jour. En revanche, les niveaux dans une infrastructure comme une application de service (IaaS) doivent être définies manuellement par le biais de jeux de disponibilité. Jeux de disponibilité sont nécessaires pour un contrat SLA sous IaaS.

![Disponibilité définit pour les machines virtuelles Azure](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png)

Dans le diagramme précédent, la couche de Internet Information Services (IIS) (qui fonctionne comme un niveau d’application web) et la couche SQL (qui fonctionne comme une couche de données) sont affectés à des ensembles différents de disponibilité. Cela garantit que toutes les instances de chaque niveau disposent d’une redondance matérielle en distribuant des machines virtuelles entre les domaines d’erreur, et que tout niveaux n’est pas prises au cours d’une mise à jour vers le bas.

###<a name="load-balancing"></a>Équilibrage de la charge

Si les ordinateurs virtuels doivent avoir le trafic réparti sur les, vous devez regrouper les ordinateurs virtuels dans une application et charge le solde sur un point de terminaison TCP ou UDP spécifique. Pour plus d’informations, reportez-vous à la section [équilibrage de machines virtuelles](../virtual-machines/virtual-machines-linux-load-balance.md). Si les ordinateurs virtuels recevoir des entrées à partir d’une autre source (par exemple, un mécanisme de files d’attente), un équilibreur de charge n’est pas nécessaire. L’équilibreur de charge utilise un bilan de santé de base pour déterminer si le trafic doit être envoyé au nœud. Il est également possible de créer vos propres sondes pour implémenter des mesures de fonctionnement de l’application spécifiques qui déterminent si la machine virtuelle doit recevoir le trafic.

##<a name="storage"></a>Stockage

Le stockage Azure est le service de données durables planifié pour Azure. Il fournit des blob, table, file d’attente et le stockage de disque de machine virtuelle. Il utilise une combinaison de la réplication et la gestion des ressources pour assurer une haute disponibilité dans un centre de données unique. La disponibilité du stockage Azure SLA qui garantisse au moins 99,9 % du temps :

* Demandes correctement mis en forme à ajouter, supprimer des données, lecture et mise à jour seront correctement et correctement traitées.
* Comptes de stockage aura une connectivité à la passerelle Internet.

###<a name="replication"></a>Réplication

Stockage Azure facilite la durabilité des données en conservant plusieurs copies de toutes les données sur des disques différents sur des sous-systèmes de stockage physique totalement indépendante dans la région. Les données sont répliquées de manière synchrone, et toutes les copies sont validées avant l’écriture est reconnue. Stockage Azure est fortement cohérent, c'est-à-dire que les lectures sont garanties pour refléter l’écriture la plus récente. En outre, les copies de données sont analysés en permanence afin de détecter et de réparer le bit rot, une menace souvent sous-estimés pour l’intégrité des données stockées.

Services des avantages de la réplication en utilisant le stockage Azure. Le développeur du service n’a pas besoin d’effectuer un travail supplémentaire pour récupérer d’une défaillance locale.

###<a name="resource-management"></a>Gestion des ressources

Les comptes de stockage créées après mai 2014, peut atteindre jusqu'à 500 To (le maximum précédent était de 200 To). Si l’espace supplémentaire est nécessaire, les applications doivent être conçues pour utiliser plusieurs comptes de stockage.

###<a name="virtual-machine-disks"></a>Disques des machines virtuelles

Les disques d’un ordinateur virtuel sont stocké comme un objet blob de page dans le stockage Azure, en lui donnant les mêmes propriétés de durabilité et de l’évolutivité en tant que stockage d’objets Blob. Cette conception permet des données sur les disques d’un ordinateur virtuel persistantes, même si la défaillance du serveur exécutant la machine virtuelle et de la machine virtuelle doit être redémarrée sur un autre serveur.

##<a name="database"></a>Base de données

###<a name="sql-database"></a>Base de données SQL

Une base de données SQL Azure fournit une base de données en tant que service. Il permet aux applications de provisionner rapidement, d’insérer des données dans et des bases de données relationnelles de requête. Il fournit la plupart des familière de SQL Server les fonctionnalités, tout en s’extrayant de la charge de matériels, de configuration, de correction et de résilience.

>[AZURE.NOTE] Une base de données SQL Azure ne fournit pas de parité fonctionnalité un à un avec SQL Server. Il est conçu pour répondre à une série d’exigences--qui a unique adaptée aux applications en nuage (évolutivité élastique, la base de données en tant que service pour réduire les coûts de maintenance, etc.). Pour plus d’informations, consultez [Choisir un nuage option de SQL Server : le SQL Azure (PaaS) de base de données ou de SQL Server sur Azure VM (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

####<a name="replication"></a>Réplication

Une base de données SQL Azure offre intégrée résilience aux défaillances de niveau de nœud. Toutes les écritures dans une base de données sont automatiquement répliquées sur deux ou plusieurs nœuds d’arrière-plan via une technique de validation de quorum. (Principal et secondaire au moins un doivent confirmer que l’activité est écrit dans le journal des transactions avant que la transaction est considérée comme réussie et retourne.) En cas de panne de nœud, la base de données bascule automatiquement vers un réplica secondaire. Cela provoque une interruption de la connexion en régime transitoire pour les applications clientes. Pour cette raison, tous les clients de la base de données de SQL Azure doivent implémenter une forme de traitement des connexions en régime transitoire. Pour plus d’informations, reportez-vous à la section [Réessayer les orientations spécifiques de service](../best-practices-retry-service-specific.md).

####<a name="resource-management"></a>Gestion des ressources

Une fois créé, chaque base de données est configuré avec une limite de taille maximale. La taille maximale disponible est de 1 To (taille limites varient en fonction de votre niveau de service, voir [les niveaux de service et de performances des bases de données SQL Azure](../sql-database/sql-database-resource-limits.md#service-tiers-and-performance-levels). Lorsqu’une base de données atteint sa limite de taille maximale, il rejette les commandes d’insertion ou de mise à jour supplémentaires. (Recherche et suppression de données sont toujours possible.)

Dans une base de données, base de données de SQL Azure utilise un tissu pour gérer les ressources. Toutefois, au lieu d’un contrôleur de fabric, il utilise une topologie en anneau pour détecter les pannes. Chaque réplica dans un cluster a deux voisins et est chargé de détecter lorsqu’ils sont en. Lorsqu’un réplica tombe en panne, ses voisins déclenchent un agent reconfiguration pour recréer sur un autre ordinateur. La limitation de moteur est fournie pour assurer un serveur logique n’utilisent trop de ressources sur un ordinateur ou dépassent les limites physiques de la machine.

###<a name="elasticity"></a>Élasticité

Si l’application requiert plus que la limite de base de données de 1 To, il doit mettre en œuvre une approche horizontale. Vous évoluer avec la base de données de SQL Azure en partitionnant manuellement, ont également connu sous le nom, les données sur plusieurs bases de données SQL. Cette approche horizontale offre l’opportunité de réaliser la croissance quasi linéaires coût avec échelle. Croissance élastique ou capacité à la demande peut grandir avec les surcoûts en fonction des besoins, car les bases de données sont facturés en fonction de moyenne taille réelle utilisée par jour, ne dépendent ne pas de la taille maximale possible.

##<a name="sql-server-on-virtual-machines"></a>SQL Server sur des Machines virtuelles

En installant SQL Server (version 2014 ou version ultérieure) sur Azure Virtual Machines, vous pouvez tirer parti des fonctionnalités traditionnelles de disponibilité de SQL Server. Ces fonctionnalités comprennent les groupes de disponibilité AlwaysOn et la mise en miroir de base de données. Notez que Azure VM, de stockage et de réseau ont des caractéristiques opérationnelles différentes à un local, non virtualisés des infrastructures informatiques. Une implémentation réussie d’une haute disponibilité et de reprise solution (HA/DR) SQL Server dans Azure nécessite que vous compreniez ces différences et concevez votre solution pour les prendre en charge.

###<a name="high-availability-nodes-in-an-availability-set"></a>Haute disponibilité des nœuds dans un jeu de disponibilité

Lorsque vous implémentez une solution de haute disponibilité dans Azure, vous pouvez utiliser la disponibilité définie dans Azure pour placer les nœuds haute disponibilité dans des domaines distincts des pannes et la mise à niveau des domaines. Pour être clair, l’ensemble de la disponibilité est un concept d’Azure. Il est recommandé que vous devez suivre pour vous assurer que vos bases de données sont en effet hautement disponibles, si vous utilisez des groupes de disponibilité AlwaysOn, la mise en miroir de base de données ou autre chose. Si vous ne suivez pas cette méthode conseillée, vous pouvez être en false supposant que votre système est hautement disponible. Mais en réalité, vos nœuds peuvent tous échouer simultanément car elles se produisent à placer dans le même domaine de panne dans la région d’Azure.

Cette recommandation n’est pas aussi applicable avec envoi de journaux. Une fonctionnalité de récupération après sinistre, vous devez vous assurer que les serveurs sont en cours d’exécution dans les régions Azure distinctes. Par définition, ces zones sont des domaines d’erreur distinct.

Pour Azure Cloud Services ordinateurs virtuels déployés via le portail classique être dans le même jeu de disponibilité, vous devez les déployer dans le même Service de Cloud. Ordinateurs virtuels déployés via le Gestionnaire de ressources Azure (le portail actuel) n’ont pas cette limitation. Pour portail classique déployé des ordinateurs virtuels dans Azure Cloud Service, seuls les nœuds dans le même Service de Cloud peuvent participer le même ensemble de disponibilité. En outre, les ordinateurs virtuels Services de nuage doit être dans le même réseau virtuel pour s’assurer qu’ils conservent leur adresses IP même après le rétablissement du service. Cela permet d’éviter les interruptions de service mise à jour DNS.

###<a name="azure-only-high-availability-solutions"></a>Azure uniquement : solutions de haute disponibilité

Vous avez une solution de haute disponibilité pour vos bases de données SQL Server dans Azure à l’aide de groupes de disponibilité AlwaysOn ou la mise en miroir de base de données.

Le diagramme suivant illustre l’architecture de groupes de disponibilité AlwaysOn Azure machines virtuelles en fonctionnement en cours d’exécution. Ce diagramme a été extraite de l’article approfondi sur ce sujet, [haute disponibilité et reprise après sinistre pour SQL Server Azure machines virtuelles en fonctionnement](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Groupes de disponibilité AlwaysOn dans Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png)

Vous pouvez également prévoir un groupes de disponibilité AlwaysOn déploiement de bout en bout sur Azure VM en utilisant le modèle AlwaysOn dans Azure portal. Pour plus d’informations, consultez [SQL Server AlwaysOn offre dans la galerie de Microsoft Azure Portal](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

Le diagramme suivant illustre l’utilisation de la mise en miroir de base de données Azure machines virtuelles en fonctionnement. Il a été tenu également à partir de la rubrique approfondie, [haute disponibilité et reprise après sinistre pour SQL Server Azure machines virtuelles en fonctionnement](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![La mise en miroir de base de données dans Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png)

>[AZURE.NOTE] Ces deux architectures nécessitent un contrôleur de domaine. Toutefois, la mise en miroir de base de données, il est possible d’utiliser des certificats de serveur pour éliminer le besoin d’un contrôleur de domaine.

##<a name="other-azure-platform-services"></a>Autres services de la plateforme Azure

Les applications qui reposent sur Azure bénéficient de capacités de la plate-forme pour récupérer de défaillances locales. Dans certains cas, vous pouvez effectuer des actions spécifiques pour accroître la disponibilité pour votre scénario spécifique.

###<a name="service-bus"></a>Bus des services

Pour atténuer une panne temporaire du Bus des services Azure, envisagez de créer une file d’attente durable du côté client. Ceci utilise temporairement un mécanisme de stockage de remplacement, local pour stocker les messages qui ne peuvent pas être ajoutés à la file d’attente de Bus de Service. L’application peut décider comment gérer les messages stockées provisoirement une fois que le service est restauré. Pour plus d’informations, consultez [méthodes conseillées pour améliorer les performances à l’aide du Bus de Service demandé de la messagerie](../service-bus-messaging/service-bus-performance-improvements.md) et [Le Bus de Service (reprise après sinistre)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

###<a name="hdinsight"></a>HDInsight

Les données qui sont associé à l’HDInsight d’Azure sont stockées par défaut dans le stockage des objets Blob Azure. Stockage Azure spécifie la haute disponibilité et la durabilité des propriétés pour le stockage des objets Blob. Le traitement de plusieurs nœuds qui est associée aux travaux d’Hadoop MapReduce se produit sur un passager Hadoop distribué fichier système très qui est mis en service lorsque HDInsight a besoin. Les résultats d’un travail MapReduce sont également stockés par défaut dans le stockage Blob d’Azure, afin que les données traitées sont durables et demeure hautement disponibles une fois que le cluster Hadoop est arrêté. Pour plus d’informations, consultez [HDInsight (reprise après sinistre)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

##<a name="checklists-for-local-failures"></a>Listes de vérification pour les défaillances locales

###<a name="cloud-services"></a>Services en nuage

  1. Consultez la section Services de nuage de ce document.
  2. Configurer au moins deux instances pour chaque rôle.
  3. Maintenir l’état dans le stockage durable, pas sur les instances de rôles.
  4. Gérer correctement l’événement StatusCheck.
  5. Retour à la ligne les modifications connexes dans des transactions lorsque cela est possible.
  6. Vérifiez que les tâches de rôle de travail sont idempotents et redémarrables.
  7. Continuer à appeler des opérations jusqu'à ce qu’ils réussissent.
  8. Prendre en compte les stratégies autoscaling.

###<a name="virtual-machines"></a>Ordinateurs virtuels

  1. Consultez la section ordinateurs virtuels de ce document.
  2. N’utilisez pas de lecteur D pour le stockage persistant.
  3. Regroupement d’ordinateurs dans un niveau de service en un ensemble de disponibilité.
  4. Configurer l’équilibrage de charge et de sondes facultatifs.

###<a name="storage"></a>Stockage

  1. Consultez la section de stockage de ce document.
  2. Utiliser plusieurs comptes de stockage lorsque la bande passante ou données dépasse les quotas.

###<a name="sql-database"></a>Base de données SQL

  1. Consultez la section de la base de données SQL de ce document.
  2. Implémenter une stratégie de nouvelle tentative pour gérer les erreurs transitoires.
  3. Utiliser le partitionnement/ont comme stratégie d’évolution.

###<a name="sql-server-on-virtual-machines"></a>SQL Server sur des Machines virtuelles

  1. Passez en revue le SQL Server sur des Machines virtuelles des section de ce document.
  2. Suivez les recommandations précédentes pour les Machines virtuelles.
  3. Utilisez les fonctions de haute disponibilité de SQL Server, comme AlwaysOn.

###<a name="service-bus"></a>Bus des services

  1. Consultez la section Service Bus de ce document.
  2. Envisagez de créer une file d’attente durable du côté client de sauvegarde.

###<a name="hdinsight"></a>HDInsight

  1. Consultez la section HDInsight de ce document.
  2. Aucune étape de disponibilité supplémentaires n’est nécessaires pour les défaillances locales.

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série de [conseils techniques de résilience Azure](./resiliency-technical-guidance.md). Le prochain article de cette série est la [récupération à partir d’une interruption de service à l’échelle de la région](./resiliency-technical-guidance-recovery-loss-azure-region.md).
