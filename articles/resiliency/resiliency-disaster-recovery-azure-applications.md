<properties
   pageTitle="Reprise après sinistre pour les applications Azure | Microsoft Azure"
   description="Vue d’ensemble technique et des informations détaillées sur la conception d’applications de reprise après sinistre sur Microsoft Azure."
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

#<a name="disaster-recovery-for-applications-built-on-microsoft-azure"></a>Reprise après sinistre pour les applications basées sur Microsoft Azure

Considérant que la haute disponibilité est sur la gestion d’une défaillance temporaire, de reprise après sinistre (DR) est la perte catastrophique de fonctionnalités de l’application. Par exemple, considérez le scénario où une région tombe en panne. Dans ce cas, vous devez disposer d’un plan pour l’exécution de votre application ou accéder à vos données en dehors de la région d’Azure. L’exécution de ce plan implique des personnes, des processus et des applications de support qui permettent au système de la fonction. Les propriétaires de métier et technologiques qui définissent le mode de fonctionnement du système pour un reprise après sinistre déterminent également le niveau de fonctionnalité pour le service au cours d’un reprise après sinistre. Le niveau de fonctionnalité peut prendre plusieurs formes : totalement indisponible, partiellement disponible (dégradation des fonctionnalités ou retard de traitement), ou complètement disponible.

##<a name="azure-disaster-recovery-features"></a>Fonctions de reprise après sinistre Azure

Comme avec les considérations relatives à la disponibilité, Azure a [des conseils techniques résilience](./resiliency-technical-guidance.md) qui a conçu pour prendre en charge la reprise après sinistre. Il existe également une relation entre certaines des fonctionnalités de disponibilité d’Azure et reprise après sinistre. Par exemple, la gestion des rôles entre les domaines d’erreur augmente la disponibilité d’une application. Sans cette gestion, une défaillance du matériel non gérée devient un scénario « incident ». Pour la bonne application des stratégies et des fonctionnalités de disponibilité est une partie importante de la vérification de reprise après sinistre votre application. Toutefois, cet article va au-delà des problèmes de disponibilité générale aux événements de reprise après sinistre grave (et plus rares).

##<a name="multiple-datacenter-regions"></a>Plusieurs zones de centre de données

Azure gère les centres de données dans de nombreuses régions du monde. Cette infrastructure prend en charge plusieurs scénarios de reprise après sinistre, par exemple la fourni par le système géo-réplication de stockage Azure à des zones secondaires. Cela signifie également que vous pouvez facilement et à moindres frais déployer un service cloud à plusieurs emplacements dans le monde entier. Faites la comparaison avec le coût et la difficulté de l’exécution de vos propres centres de données dans plusieurs régions. Déploiement de services et données dans plusieurs régions, vous aide à protéger votre application contre les interruptions majeures dans une seule région.

##<a name="azure-traffic-manager"></a>Gestionnaire de trafic Azure

Lorsqu’une erreur spécifique se produit, vous devez rediriger le trafic vers les déploiements dans une autre région ou de services. Vous pouvez effectuer cette gamme manuellement, mais il est plus efficace d’utiliser un processus automatisé. Azure Traffic Manager est conçu pour cette tâche. Vous pouvez l’utiliser pour gérer automatiquement le basculement du trafic d’utilisateur dans une autre région en cas d’échec de la zone primaire. Étant donné que la gestion du trafic est une partie importante de la stratégie globale, il est important de comprendre les notions de base du Gestionnaire de trafic.

Dans le diagramme suivant, les utilisateurs se connectent à une URL qui a spécifié de Traffic Manager (__http://myATMURL.trafficmanager.net__) et qui extrait l’URL de site proprement dit (__http://app1URL.cloudapp.net__ et __http://app2URL.cloudapp.net__). En fonction de la configuration de critères de lorsque pour les utilisateurs de la gamme, les utilisateurs recevront le site correct lorsque la stratégie dicte. Les options de stratégie sont alternée, de performances ou de basculement. Pour cet article, nous seront concernés par l’option de basculement uniquement.

![Routage via le Gestionnaire de trafic Azure](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

Lorsque vous configurez le Traffic Manager, vous fournissez un nouveau préfixe de gestionnaire de trafic DNS. Il s’agit du préfixe de l’URL que vous allez fournir à vos utilisateurs d’accéder à votre service. Traffic Manager rend à présent abstraites et non à l’échelle de la région d’un niveau d’équilibrage de charge. Le Gestionnaire de trafic DNS mappe vers un enregistrement CNAME pour tous les déploiements qu’il gère.

Dans le Gestionnaire de trafic, vous spécifiez la priorité des déploiements qui seront acheminées vers les utilisateurs en cas de défaillance. Traffic Manager surveille les points de terminaison des notes et des déploiements lorsque le déploiement primaire échoue. En cas d’échec, Traffic Manager analyse la liste classée des déploiements et achemine les utilisateurs à la suivante dans la liste.

Bien que le Traffic Manager décide où aller dans un basculement sur incident, vous pouvez décider si votre domaine de basculement est actif ou dormant alors que vous n’êtes pas en mode de basculement. Cette fonctionnalité n’a rien à voir avec le Gestionnaire de trafic Azure. Traffic Manager détecte une panne du site principal et bascule vers le site de basculement. Traffic Manager survole que si ce site sert actuellement les utilisateurs ou non.

Pour plus d’informations sur le fonctionnement du Gestionnaire de trafic d’Azure, reportez-vous à :

 * [Vue d’ensemble du Gestionnaire de trafic](../traffic-manager/traffic-manager-overview.md)
 * [Configurer la méthode d’acheminement de basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##<a name="azure-disaster-scenarios"></a>Scénarios de reprise après sinistre Azure

Les sections suivantes décrivent les différents types de scénarios de reprise après sinistre. Les interruptions de service à l’échelle de la région ne sont pas la seule cause des défaillances de l’application. Des erreurs de conception ou d’administration médiocres peuvent également entraîner des pannes. Il est important de prendre en compte les causes possibles d’une défaillance lors de la conception et le test des phases de votre plan de récupération. Un bon plan tire parti des fonctionnalités d’Azure et augmente les avec les stratégies d’application spécifiques. La réponse choisie dépend de l’importance de l’application, l’objectif de point de récupération (RPO) et l’objectif de temps de récupération (RTO).

###<a name="application-failure"></a>Échec de l’application

Azure Traffic Manager gère automatiquement les défaillances qui résultent du logiciel de matériel ou de système d’exploitation sous-jacent dans la machine virtuelle. Azure crée une nouvelle instance du rôle sur un serveur qui fonctionne et l’ajoute à la rotation de l’équilibrage de la charge. Si le nombre d’instances de rôle est supérieur à un, Azure décale le traitement pour les autres instances de rôle en cours d’exécution lors du remplacement du nœud défaillant.

Il existe des erreurs graves se produisent indépendamment les défaillances de matériel ou de système d’exploitation. L’application peut échouer en raison d’exceptions grave causées par une logique incorrecte ou des problèmes d’intégrité des données. Vous devez intégrer suffisamment télémétrie dans le code afin que le système de surveillance peut détecter les conditions d’échec et avertir un administrateur de l’application. Un administrateur qui connaît parfaitement les processus de récupération après sinistre peut décider d’appeler un processus de basculement. Un administrateur pouvez également simplement accepter une interruption de la disponibilité pour résoudre les erreurs critiques.

###<a name="data-corruption"></a>Corruption des données

Azure stocke automatiquement les données de votre base de données de SQL Azure et d’Azure Storage trois fois redondante dans les domaines de pannes différentes dans la même région. Si vous utilisez la réplication géographique, les données sont stockées à trois fois plus dans une région différente. Toutefois, si vos utilisateurs ou votre application endommage les données dans la copie principale, les données de réplique rapidement vers les autres copies. Malheureusement, cela entraîne trois copies de données endommagées.

Pour gérer le risque d’altération de vos données, vous avez deux options. Tout d’abord, vous pouvez gérer une stratégie de sauvegarde personnalisée. Vous pouvez stocker vos sauvegardes dans Azure ou sur site, selon vos besoins ou les règles de gouvernance. Une autre option consiste à utiliser la nouvelle option de restauration de point-à-temps pour la récupération d’une base de données SQL. Pour plus d’informations, consultez la section sur les [stratégies de données pour la reprise après sinistre](#data-strategies-for-disaster-recovery).

###<a name="network-outage"></a>Panne de réseau

Lorsque les parties du réseau Azure sont inaccessibles, vous n’est peut-être pas en mesure d’accéder à votre application ou vos données. Si une ou plusieurs instances de rôle ne sont pas disponibles en raison de problèmes de réseau, Azure utilise les autres instances disponibles de votre application. Si votre application ne peut pas accéder à ses données en raison d’une panne de réseau Azure, vous pouvez exécuter en mode dégradé localement à l’aide des données mises en cache. Vous devez concevoir la stratégie de récupération d’urgence pour l’exécution en mode dégradé dans votre application. Pour certaines applications, cela peut ne pas convenir.

Une autre option est de stocker des données dans un autre emplacement jusqu'à ce que la connectivité est restaurée. Si le mode dégradé n’est pas une option, les autres options sont les temps d’arrêt de l’application ou le basculement vers une autre zone. La conception d’une application fonctionne en mode dégradé est autant une décision commerciale comme une technique. Cette opération est abordée plus en détail dans la section sur la [dégradation des fonctionnalités de l’application](#degraded-application-functionality).

###<a name="failure-of-a-dependent-service"></a>Échec d’un service dépendant

Azure fournit de nombreux services qui peuvent subir des temps d’inactivité périodiques. Prenons l’exemple [Du Cache Redis d’Azure](https://azure.microsoft.com/services/cache/) . Ce service mutualisée fournit à votre application des fonctionnalités de mise en cache. Il est important de tenir compte de ce qui se passe dans votre application si le service dépendant n’est pas disponible. De nombreuses manières, ce scénario est semblable au scénario de panne réseau. Toutefois, en tenant compte de chaque service génère indépendamment des améliorations potentielles à votre plan global.

Cache de Redis Azure fournit la mise en cache sur votre application dans votre déploiement de service cloud, qui offre des avantages de la récupération après sinistre. Tout d’abord, le service s’exécute désormais sur les rôles qui sont locaux à votre déploiement. Par conséquent, vous êtes mieux en mesure de surveiller et de gérer l’état de la mémoire cache dans le cadre de votre processus de gestion globale pour le service en nuage. Ce type de mise en cache expose également les nouvelles fonctionnalités. Une des nouvelles fonctionnalités est la haute disponibilité des données en mémoire cache. Cela permet de conserver les données mises en cache en cas de défaillance d’un seul nœud en conservant des copies sur d’autres nœuds.

Notez que la haute disponibilité diminue le débit et augmente le temps de latence en raison de la mise à jour de la copie secondaire sur les écritures. Il fait également fonction de la quantité de mémoire qui est utilisée pour chaque élément, qui donc planifier. Cet exemple montre que chaque service dépendant peut avoir des capacités qui améliorent votre disponibilité globale et la résistance aux pannes catastrophiques.

Avec chaque service dépendant, vous devez comprendre les conséquences d’une interruption de service. Dans l’exemple de mise en cache, il est possible d’accéder aux données directement à partir d’une base de données jusqu'à ce que vous restauriez votre cache. Ce serait un mode dégradé en termes de performances, mais fournit des fonctionnalités complètes en matière de données.

###<a name="region-wide-service-disruption"></a>Interruption de service à l’échelle de la région

Les échecs précédents ont été principalement les défaillances qui peuvent être gérés au sein de la même région d’Azure. Toutefois, vous devez également préparer la possibilité qu’il existe une interruption de service de l’ensemble de la région. En cas d’une interruption de service à l’échelle de la région, les copies redondantes localement des données ne sont pas disponibles. Si vous avez activé la réplication géographique, il existe trois copies supplémentaires de votre BLOB et les tables dans une autre région. Si Microsoft déclare la région perdue, Azure remappe toutes les entrées DNS pour la zone géo-répliquées.

>[AZURE.NOTE] Sachez que vous n’avez aucun contrôle sur ce processus, et il se produit uniquement pour les interruptions de service à l’échelle de la région. Pour cette raison, vous devez compter sur les autres stratégies de sauvegarde spécifiques à l’application d’atteindre le plus haut niveau de disponibilité. Pour plus d’informations, consultez la section sur les [stratégies de données pour la reprise après sinistre](#data-strategies-for-disaster-recovery).

###<a name="azure-wide-service-disruption"></a>Interruption de service à l’échelle Azure

Dans la planification de la reprise après sinistre, vous devez prendre en compte l’ensemble des catastrophes. Parmi les plus graves interruptions de service implique toutes les régions Azure simultanément. Comme avec les autres interruptions de service, vous pouvez décider que vous allez prendre les risques d’interruption temporaire dans ce cas. Les interruptions de service de grande envergure qui couvrent des régions doivent être beaucoup plus rares que les interruptions de service isolé qui impliquent des services dépendants ou régions unique.

Toutefois, pour certaines applications critiques pour l’entreprise, vous pouvez décider qu’il doit y avoir un plan de sauvegarde pour ce scénario. Le plan de cet événement peut inclure basculement vers les services dans un [nuage de remplacement](#alternative-cloud) ou un [hybride locaux et solution de cloud](#hybrid-on-premises-and-cloud-solution).

###<a name="degraded-application-functionality"></a>Fonctionnalités de l’application de dégradé

Une application bien conçue utilise généralement une collection de modules qui communiquent entre eux via l’implémentation de modèles d’échange d’information-faiblement couplé. Une application conviviale pour la reprise après sinistre exige la séparation des tâches au niveau du module. C’est pour éviter l’interruption d’un service dépendant d’interrompre l’application entière. Par exemple, prenons une application de commerce web pour Y de la société. Les modules suivants pourraient constituer l’application :

 * __Catalogue de produits__ permet aux utilisateurs de parcourir les produits.
 * __Panier d’achat__ permet aux utilisateurs d’ajouter ou de supprimer des produits dans son panier d’achat.
 * __Statut de la commande__ affiche l’état de la livraison de commandes de l’utilisateur.
 * __Envoi de la commande__ finalise la session d’achat en soumettant la commande avec le paiement.
 * __Traitement des__ valide la commande pour l’intégrité des données et effectue une vérification de disponibilité de quantité.

Lorsqu’une personne à charge d’un module de cette application tombe en panne, comment le module fonctionne jusqu'à ce que la partie récupère ? Un système bien conçu implémente des limites d’isolation par le biais de la séparation des tâches à la fois au moment du design et au moment de l’exécution. Vous pouvez classer chaque panne récupérable et non récupérable. Erreurs non récupérables prendra le module vers le bas, mais vous pouvez atténuer une erreur récupérable par le biais de solutions de remplacement. Comme expliqué dans la section haute disponibilité, vous pouvez masquer certains problèmes des utilisateurs par la gestion des erreurs et de prendre d’autres actions. Au cours d’une interruption de service plus grave, l’application peut être totalement indisponible. Toutefois, une troisième option consiste à continuer à traiter les utilisateurs en mode dégradé.

Par exemple, si la base de données pour l’hébergement de commandes tombe en panne, le module de traitement des commandes perd sa capacité à traiter les transactions de vente. En fonction de l’architecture, il peut être difficile, voire impossible pour les parties d’envoi de la commande et de traitement des commandes de l’application à continuer. Si l’application n’est pas conçue pour gérer ce scénario, l’application peut se déconnecter.

Toutefois, dans ce scénario, il est possible que les données de produit sont stockées dans un emplacement différent. Dans ce cas, le module catalogue produit toujours utilisable pour l’affichage des produits. En mode dégradé, l’application continue à être disponible pour les utilisateurs de fonctionnalités disponibles, telles que l’affichage du catalogue de produits. Toutefois, les autres parties de l’application, sont indisponibles, telles que les requêtes de classement ou de stock.

Une autre variante de mode dégradé centre de performances plutôt que des fonctionnalités. Par exemple, envisagez un scénario où le catalogue des produits est mis en cache par le Cache de Redis d’Azure. Si la mise en cache n’est pas disponible, l’application peut atteindre directement le serveur de stockage pour récupérer des informations de catalogue de produits. Mais cet accès peut être plus lent que la version mise en cache. De ce fait, les performances des applications diminuent jusqu'à ce que le service de mise en cache est entièrement restauré.

Décider combien d’une application continue de la fonction en mode dégradé est une décision de l’entreprise et une décision technique. L’application doit également décider comment informer les utilisateurs des problèmes temporaires. Dans cet exemple, l’application peut permettre d’affichage de produits et même de les ajouter dans un panier d’achat. Toutefois, lorsque l’utilisateur tente d’effectuer un achat, l’application avertit l’utilisateur que le module de ventes est temporairement inaccessible. Il n’est pas idéale pour le client, mais il n’empêche pas une interruption de service à l’échelle de l’application.

##<a name="data-strategies-for-disaster-recovery"></a>Stratégies de données pour la reprise après sinistre

Gestion des données correctement est la zone plus difficile à réaliser correctement dans n’importe quel plan de récupération d’urgence. Restauration de données fait également partie du processus de restauration qui prend généralement le plus de temps. Différents choix de modes de dégradation entraîner des défis difficiles pour la récupération de défaillance et de la cohérence des données après une panne.

Un des facteurs est obligé de restaurer ou de conserver une copie des données de l’application. Vous utiliserez ces données pour référence et transactionnelle sur un site secondaire. Un local configuration nécessite un processus de planification long et coûteux à implémenter une stratégie de récupération après sinistre de plusieurs régions. Heureusement, la plupart des fournisseurs de nuage, y compris d’Azure, permettent facilement le déploiement des applications dans plusieurs régions. Ces régions sont distribuées de manière à ce que les interruptions de service de plusieurs régions doivent être extrêmement rares. La stratégie de gestion des données entre les régions est parmi les éléments déterminants pour la réussite de tout plan de récupération d’urgence.

Les sections suivantes décrivent les techniques de récupération d’urgence liés aux sauvegardes de données, les données de référence et des données transactionnelles.

###<a name="backup-and-restore"></a>Sauvegarde et restauration

Des sauvegardes régulières des données d’application peuvent prendre en charge des scénarios de reprise après sinistre. Les ressources de stockage différents nécessitent différentes techniques.

Pour les niveaux basique, Standard et de base de données SQL de prime, vous pouvez tirer parti de la restauration de point-à-temps pour récupérer votre base de données. Pour plus d’informations, consultez [Overview : Cloud business continuité d’activité et de la base de données de récupération après sinistre de la base de données SQL](../sql-database/sql-database-business-continuity.md). Une autre option consiste à utiliser Active géo-réplication de base de données de SQL. Cette réplique automatiquement les modifications de la base de données pour les bases de données secondaires dans la même région d’Azure, ou même dans un autre région Azure. Cela fournit une alternative de potentielle à certaines des techniques de synchronisation de données plus manuels présentés dans cet article. Pour plus d’informations, consultez [Overview : SQL de base de données géo-réplication Active](../sql-database/sql-database-geo-replication-overview.md).

Vous pouvez également utiliser une approche plus manuelle pour la sauvegarde et de restauration. Utilisez la commande de copie de la base de données pour créer une copie de la base de données. Vous devez utiliser cette commande pour obtenir une sauvegarde avec cohérence transactionnelle. Vous pouvez également utiliser le service d’importation/exportation de base de données de SQL Azure. Cela prend en charge les bases de données exportation des fichiers de type sac à DOS qui sont stockés dans le stockage des objets Blob Azure.

La redondance intégrée de stockage Azure crée deux réplicas du fichier de sauvegarde dans la même région. Toutefois, la fréquence d’exécution du processus de sauvegarde détermine votre RPO, qui est la quantité de données, que vous risquez de perdre dans les scénarios de reprise après sinistre. Par exemple, imaginez que vous effectuez une sauvegarde au début de l’heure, et deux minutes avant le début de l’heure en situation d’urgence. Vous perdez 58 minutes de données qui s’est passé après que la dernière sauvegarde a été effectuée. En outre, pour protéger contre une interruption de service à l’échelle de la région, vous devez copier les fichiers de type sac à DOS à une autre région. Vous avez ensuite la possibilité de restaurer ces sauvegardes, dans la zone autre. Pour plus d’informations, consultez [Overview : Cloud business continuité d’activité et de la base de données de récupération après sinistre de la base de données SQL](../sql-database/sql-database-business-continuity.md).

Pour le stockage Azure, vous pouvez développer votre propre processus de sauvegarde personnalisé ou utiliser un des nombreux outils de sauvegarde tiers. Notez que la plupart des conceptions d’application plus complexes où les ressources de stockage référencent mutuellement. Par exemple, considérez une base de données SQL ayant une colonne liée à un objet blob dans le stockage Azure. Si les sauvegardes ne sont pas effectuées simultanément, la base de données peut avoir le pointeur vers un objet blob qui n’a pas sauvegardé avant la panne. L’application ou un plan de récupération d’urgence doit implémenter des processus pour gérer cette incohérence après une restauration.

###<a name="reference-data-pattern-for-disaster-recovery"></a>Modèle de données de référence pour la reprise après sinistre

Données de référence sont des données en lecture seule qui prend en charge les fonctionnalités de l’application. Il ne change généralement pas fréquemment. Bien que la sauvegarde et restauration est une méthode pour gérer les interruptions de service à l’échelle de la région, le RTO est relativement long. Lorsque vous déployez l’application à une zone secondaire, certaines stratégies peuvent améliorer le RTO de données de référence.

Étant donné que les modifications de données de référence de rares occasions, vous pouvez améliorer le RTO en conservant une copie permanente des données de référence dans la zone secondaire. Cela permet d’éliminer le temps nécessaire à la restauration des sauvegardes en cas de sinistre. Pour satisfaire les besoins de récupération après sinistre de plusieurs régions, vous devez déployer l’application et les données de référence simultanément dans plusieurs régions. Comme indiqué dans le [modèle de données de référence pour une haute disponibilité](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), vous pouvez déployer des données de référence pour le rôle lui-même, à un stockage externe, ou vers une combinaison des deux.

Implicitement, le modèle de déploiement des données de référence dans les nœuds de calcul satisfait les exigences de la récupération après sinistre. Déploiement de données de référence pour la base de données de SQL requiert que vous déployez une copie des données de référence pour chaque région. La même stratégie s’applique au stockage Azure. Vous devez déployer une copie de toutes les données de référence qui sont stockées dans le stockage Azure pour les zones principales et secondaires.

![Publication des données de référence pour les zones principales et secondaires](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

Vous devez implémenter vos propres routines de sauvegarde spécifiques à l’application de toutes les données, y compris les données de référence. Copies de géo-répliquées entre les régions sont utilisées uniquement dans une interruption de service à l’échelle de la région. Pour éviter les temps d’arrêt prolongés, déployer les parties critiques de données de l’application à la zone secondaire. Pour obtenir un exemple de cette topologie, consultez le [modèle actif-passif](#active-passive).

###<a name="transactional-data-pattern-for-disaster-recovery"></a>Modèle de données transactionnelles de reprise après sinistre

Implémentation d’une stratégie de mode de reprise après sinistre entièrement fonctionnel nécessite la réplication asynchrone des données transactionnelles à la zone secondaire. Les fenêtres de temps pratique dans lequel la réplication peut se produire détermine les caractéristiques RPO de l’application. Vous pouvez toujours récupérer les données perdues à partir de la zone primaire pendant la période de réplication. Vous pourrez également fusionner ultérieurement avec la zone secondaire.

Les exemples d’architecture suivantes fournissent quelques idées sur les différentes façons de traiter les données transactionnelles dans un scénario de basculement sur incident. Il est important de noter que ces exemples ne sont pas exhaustives. Par exemple, les emplacements de stockage intermédiaire tels que les files d’attente peuvent être remplacées par la base de données de SQL Azure. Les files d’attente se peuvent être des files d’attente stockage Azure ou de Bus des services Azure (voir [Azure files d’attente et des Bus de Service--comparé et comparaison](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)). Destinations de stockage serveur peuvent également varier, tels que les tables Azure au lieu de la base de données SQL. En outre, les rôles de travail peuvent être insérées en tant qu’intermédiaires dans les différentes étapes. L’important est de ne pas émuler ces architectures exactement, mais à prendre en compte les diverses solutions dans la récupération des données transactionnelles et les modules associés.

####<a name="replication-of-transactional-data-in-preparation-for-disaster-recovery"></a>Réplication des données transactionnelles en vue de la reprise après sinistre

Considérez une application qui utilise des files d’attente de stockage Azure pour contenir les données transactionnelles. Ainsi, les rôles de travail traiter les données transactionnelles de la base de données dans une architecture découplée. Cette opération nécessite les transactions à utiliser une certaine forme de mise en cache temporaire si les rôles frontaux nécessitent la requête immédiate de ces données. Selon le niveau de tolérance des pertes de données, vous pouvez choisir de répliquer la base de données, les files d’attente ou toutes les ressources de stockage. Avec la réplication de base de données uniquement, si la région principale tombe en panne, vous pouvez toujours récupérer les données dans les files d’attente lorsque la région principale revient.

Le diagramme suivant montre une architecture où la base de données du serveur est synchronisée entre les régions.

![Réplication des données transactionnelles en vue de la reprise après sinistre](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

Le plus grand défi à l’implémentation de cette architecture est la stratégie de réplication entre différentes régions. Le service de synchronisation de données SQL Azure permet à ce type de réplication. Toutefois, le service est toujours en aperçu et n’est pas recommandé pour les environnements de production. Pour plus d’informations, consultez [Overview : Cloud business continuité d’activité et de la base de données de récupération après sinistre de la base de données SQL](../sql-database/sql-database-business-continuity.md). Pour les applications de production, vous devez investir dans une solution tierce ou créer votre propre logique de réplication dans le code. En fonction de l’architecture, la réplication peut être bidirectionnel, qui est également plus complexe.

Une implémentation potentielle susceptibles de rendre l’utilisation de la file d’attente intermédiaire dans l’exemple précédent. Le rôle de travail qui traite les données vers la destination finale de stockage peut effectuer la modification dans la région principale et dans la zone secondaire. Ils ne sont pas des tâches simples et Guide pas à pas pour le code de réplication est au-delà de la portée de cet article. Le point important est que beaucoup de temps et de tests doit se concentrer sur la façon dont vous répliquez vos données sur la zone secondaire. Traitement et les tests supplémentaires peuvent aider à garantir que les processus de basculement et de restauration de gérer correctement des incohérences de données ou des transactions en double.

>[AZURE.NOTE] La majeure partie de ce document se concentre sur la plate-forme en tant que service (PaaS). Toutefois, des options supplémentaires de disponibilité et de réplication pour des applications hybrides utilisent des ordinateurs virtuels Azure. Ces applications hybrides utilisent l’infrastructure en tant que service (IaaS) pour héberger SQL Server sur des machines virtuelles dans Azure. Ainsi, les approches traditionnelles de disponibilité dans SQL Server, tels que les groupes de disponibilité AlwaysOn ou l’envoi de journaux. Certaines techniques, comme AlwaysOn, fonctionnent uniquement entre instances de SQL Server sur site et les ordinateurs virtuels Azure. Pour plus d’informations, reportez-vous à la section [haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

####<a name="degraded-application-mode-for-transaction-capture"></a>Mode d’application de dégradé pour l’acquisition de la transaction

Envisagez une architecture deuxième qui fonctionne en mode dégradé. L’application sur la zone secondaire désactive toutes les fonctionnalités, telles que rapports, d’analyse décisionnelle (BI), ou drainage des files d’attente. Il accepte uniquement des plus importants types de flux de travail transactionnel, tel que défini par les besoins de l’entreprise. Le système capture les transactions et les écrit dans les files d’attente. Le système peut différer le traitement des données au cours de la phase initiale de l’interruption de service. Si le système sur la région principale est réactivé dans le délai prévu, les rôles de travail dans la région principale peuvent se vider les files d’attente. Ce processus élimine la nécessité de fusion de la base de données. Si l’interruption de service principale région va au-delà de la fenêtre maximale tolérée, l’application peut démarrer le traitement des files d’attente.

Dans ce scénario, la base de données sur le serveur secondaire contient des données transactionnelles incrémentielles qui doivent être fusionnées en une fois le serveur principal est réactivé. Le diagramme suivant illustre cette stratégie pour stocker temporairement des données transactionnelles jusqu'à ce que la zone primaire est restaurée.

![Mode d’application de dégradé pour l’acquisition de la transaction](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

Pour plus d’informations sur des techniques de gestion de données pour les applications Azure robustes, consultez [Failsafe : conseils pour des Architectures de Cloud souple](https://channel9.msdn.com/Series/FailSafe).

##<a name="deployment-topologies-for-disaster-recovery"></a>Topologies de déploiement pour la reprise après sinistre

Vous devez préparer les applications critiques pour l’éventualité d’une interruption de service à l’échelle de la région. Pour cela, grâce à l’intégration d’une stratégie de déploiement de plusieurs pays dans la planification opérationnelle.

Les déploiements multiples-région peuvent impliquer des processus professionnels de l’informatique pour publier les données de référence et application à la zone secondaire après un sinistre. Si l’application requiert une reprise instantanée, le processus de déploiement peut impliquer une installation actif/passif ou un actif/actif. Ce type de déploiement a des instances existantes de l’application en cours d’exécution dans l’autre zone. Un outil de routage comme gestionnaire de trafic Azure fournit des services de l’équilibrage de la charge au niveau du DNS. Il peut détecter des interruptions de service et acheminer les utilisateurs à différentes régions, lorsque cela est nécessaire.

Partie d’une récupération d’urgence d’Azure réussie est de conception que la récupération dans la solution à partir du début. Le cloud fournit des options supplémentaires pour la récupération des défaillances qui ne sont pas disponibles dans un fournisseur d’hébergement traditionnel au cours d’un reprise après sinistre. En particulier, vous pouvez rapidement et de manière dynamique affecter les ressources dans une autre région. Par conséquent, vous ne payez beaucoup ressources inactives pendant que vous attendez d’une cas de défaillance.

Les sections suivantes décrivent les différentes topologies de déploiement pour la reprise après sinistre. En règle générale, il existe un compromis dans l’augmentation du coût ou de complexité pour davantage de disponibilité.

###<a name="single-region-deployment"></a>Déploiement de la seule région

Un seule région déploiement n’est pas réellement une topologie de reprise après sinistre, mais il est destiné à contraste avec les autres architectures. Les déploiements seule région sont courants pour les applications dans Azure. Toutefois, ce type de déploiement n’est pas un concurrent sérieux pour un plan de récupération d’urgence.

Le diagramme suivant illustre une application s’exécutant dans une seule région d’Azure. Azure Traffic Manager et l’utilisation des pannes et mise à niveau des domaines augmentent la disponibilité de l’application au sein de la région.

![Déploiement de la seule région](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

Ici, il est évident que la base de données est un point de défaillance unique. Bien que Azure réplique les données dans les domaines des pannes différentes duplications internes, tout ceci se produit dans la même région. L’application ne peut pas résister à une panne grave. Si la région tombe en panne, tous les domaines de la panne Descendre, y compris toutes les instances de service et des ressources de stockage.

Pour tous les champs, sauf les applications moins critiques, vous devez concevoir un plan pour déployer vos applications dans plusieurs régions. Vous devez également prendre en compte les RTO et coût des contraintes en ce qui concerne la topologie de déploiement à utiliser.

Examinons maintenant à des modèles spécifiques pour prendre en charge le basculement entre les différentes régions. Ces exemples permet de décrire le processus de deux régions.

###<a name="redeployment-to-a-secondary-azure-region"></a>Redéploiement d’une région d’Azure secondaire

Dans le modèle de redéploiement pour une zone secondaire, seulement la zone primaire a des applications et des bases de données en cours d’exécution. La zone secondaire n’est pas configurée pour un basculement automatique. Ainsi lorsqu’un incident se produit, vous devez tourner toutes les parties du service dans la nouvelle zone. Cela inclut le téléchargement d’un service en nuage d’Azure, le déploiement du service de cloud, restauration des données et la modification de DNS pour rediriger le trafic.

Bien qu’il s’agit des options multiples-la région la plus abordable, elle a les caractéristiques RTO pires. Dans ce modèle, les sauvegardes de package et de la base de données de service sont stockés sur site ou dans l’instance de stockage Azure Blob de la zone secondaire. Toutefois, vous devez déployer un nouveau service et restaurer les données avant de reprendre son fonctionnement. Même si vous automatisez complètement le transfert de données à partir de la sauvegarde, faire le tour du nouvel environnement de base de données consomme beaucoup de temps. Déplacement de données du stockage de disque de sauvegarde de la base de données vide sur la zone secondaire est la partie la plus coûteuse du processus de restauration. Vous devez le faire, toutefois, pour mettre la nouvelle base de données à un état opérationnel, car il n’est pas répliquée.

La meilleure approche consiste à stocker les packages de service dans le stockage Blob dans la zone secondaire. Cela évite d’avoir à télécharger le package vers Azure, qui est ce qui se passe lors du déploiement d’un ordinateur de développement local. Vous pouvez rapidement déployer les packages de service pour un service en nuage depuis le stockage Blob à l’aide de scripts PowerShell.

Cette option est utile uniquement pour les applications non critiques qui peuvent tolérer une augmentation du RTO. Par exemple, cela peut fonctionner pour une application qui peut être enfoncée pendant plusieurs heures, mais doit être exécuté à nouveau dans les 24 heures.

![Redéploiement d’une région d’Azure secondaire](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###<a name="active-passive"></a>Actif-passif

Le modèle actif-passif est le choix qui favorisent de nombreuses sociétés. Ce modèle apporte des améliorations au RTO avec une petite augmentation des coûts sur le modèle de redéploiement.
Dans ce scénario, il est de nouveau un principal et une secondaire région Azure. Tout le trafic atteint le déploiement actif sur la zone primaire. La zone secondaire est mieux préparée pour la reprise après sinistre, car la base de données est en cours d’exécution sur les deux régions. En outre, un mécanisme de synchronisation est en place entre les deux. Cette approche de secours peut impliquer deux variantes : une approche de la base de données uniquement ou un déploiement complet de la zone secondaire.

####<a name="database-only"></a>Base de données uniquement

Dans la première version du modèle actif-passif, seulement la zone primaire a une application de service de cloud déployé. Toutefois, à la différence du modèle de redéploiement, les deux régions sont synchronisées avec le contenu de la base de données. (Pour plus d’informations, voir la section sur le [modèle de données transactionnelles de reprise après sinistre](#transactional-data-pattern-for-disaster-recovery)). Lorsqu’un incident se produit, il vous faut impérativement moins d’activation. Vous démarrez l’application dans la zone secondaire, modifiez les chaînes de connexion à la base de données et modifiez les entrées DNS pour rediriger le trafic.

Comme le modèle de redéploiement, vous devez ont déjà stockés les packages de service dans le stockage Blob d’Azure dans la région secondaire pour un déploiement plus rapide. À la différence du modèle de redéploiement, vous n’engagez pas la majorité de la surcharge qui requiert de l’opération de restauration de base de données. La base de données est prêt et en cours d’exécution. Cela fait gagner beaucoup de temps, ce qui en fait un modèle de reprise après sinistre abordable. Il est également le modèle de reprise après sinistre les plus populaires.

![Base de données actif-passif, uniquement](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####<a name="full-replica"></a>Réplica complet

Dans la seconde version du modèle actif-passif, la région principale et la zone secondaire ont un déploiement complet. Ce déploiement comprend les services en nuage et une base de données synchronisée. Toutefois, seulement la zone primaire gère activement les requêtes réseau des utilisateurs. La zone secondaire devient active uniquement lorsque la région principale subit une interruption de service. Dans ce cas, toutes les nouvelles demandes de réseau achemine vers la zone secondaire. Ce basculement peut gérer automatiquement les Azure Traffic Manager.

Basculement plus rapide que la variation de la base de données uniquement dans la mesure où les services sont déjà déployés. Ce modèle fournit un RTO très faible. La zone secondaire de basculement sur incident doit être prête à fonctionner immédiatement après l’échec de la zone primaire.

Avec un temps de réponse plus rapide, ce modèle a l’avantage de la préallocation et le déploiement des services de sauvegarde. Vous n’avez pas à vous soucier d’une zone n’ayant ne pas l’espace à allouer des instances en cas de sinistre. Ceci est important si votre région Azure secondaire proche de sa capacité. Il n’y a aucune garantie (contrat de niveau de service), que vous serez immédiatement en mesure de déployer un certain nombre de nouveaux services de cloud dans n’importe quelle région.

Pour le temps de réponse plus rapide avec ce modèle, vous devez disposer d’échelle similaire (nombre d’instances de rôle) dans les régions principales et secondaires. Malgré les avantages, pour les instances de calcul inutilisé est coûteuse et n’est pas toujours le choix financier plus prudent. Pour cette raison, il est plus courant d’utiliser une version légèrement réduite des services en nuage sur la zone secondaire. Puis, vous pouvez rapidement basculer et faire évoluer le déploiement secondaire si nécessaire. Vous devez automatiser le processus de basculement afin qu’une fois que la zone primaire est inaccessible, vous activez des instances supplémentaires, en fonction de la charge. Cela peut impliquer l’utilisation d’un mécanisme autoscaling comme [définit de l’échelle de l’ordinateur virtuel](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Le diagramme suivant illustre le modèle dans lequel les zones primaires et secondaires contiennent un service cloud entièrement déployée dans un modèle actif-passif.

![Réplica actif-passif, total](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###<a name="active-active"></a>Actif-actif

À présent, vous êtes sans doute à découvrir l’évolution des modèles : réduire le RTO augmente les coûts et la complexité. La solution de l’actif est effectivement coupé cette tendance, en ce qui concerne le coût.

Dans un modèle actif-actif, les services en nuage et la base de données sont complètement déployés dans les deux régions. Contrairement au modèle actif-passif, les deux zones de recevoir le trafic des utilisateurs. Cette option donne le temps de récupération plus rapide. Les services sont déjà mises à l’échelle pour gérer une partie de la charge au niveau de chaque région. DNS est déjà activé pour utiliser la zone secondaire. Il existe une complexité supplémentaire pour déterminer comment router les utilisateurs vers la région appropriée. Planification de la répétition alternée est possible. Il est plus probable que certains utilisateurs utiliserait une région spécifique où réside la copie principale de leurs données.

En cas de basculement, vous devez simplement désactiver DNS à la zone principale. Ceci achemine tout le trafic vers la zone secondaire.

Même dans ce modèle, il existe quelques différences. Par exemple, le diagramme suivant montre un modèle dans lequel la région principale possède la copie principale de la base de données. Les services en nuage dans les deux régions d’écrire dans cette base de données principale. Le déploiement secondaire peut lire à partir de la base de données primaire ou répliqué. Dans cet exemple la réplication se produit une façon.

![Actif-actif](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

Il existe un inconvénient à l’architecture de l’actif dans le diagramme précédent. La seconde région doit accéder à la base de données de la première région parce que la copie principale réside. Performances nettement disparaît lorsque vous accédez aux données à partir de l’extérieur d’une région. Dans les appels inter-région de base de données, vous devez envisager un type de traitement par lots de la stratégie pour améliorer les performances de ces appels. Pour plus d’informations, voir [comment utiliser le traitement par lots pour améliorer les performances de l’application de base de données SQL](../sql-database/sql-database-use-batching-to-improve-performance.md).

Une autre architecture peut impliquer de chaque région d’accéder directement à sa propre base de données. Dans ce modèle, un type de réplication bidirectionnelle est requis pour synchroniser les bases de données dans chaque région.

Dans le modèle actif, vous n’ayez pas autant d’instances de la région principale comme vous le feriez dans le modèle actif-passif. Si vous avez 10 instances sur la zone primaire dans une architecture d’actif-passif, vous devrez peut-être seulement 5 dans chaque région dans une architecture d’actif. Les deux régions à présent partagent la charge. Il peut s’agir d’une réduction des coûts sur le modèle actif-passif si vous conservez un à chaud en attente sur la région du passive avec 10 instances et en attente pour le basculement.

Réalisez jusqu'à ce que vous restauriez la région principale, la zone secondaire peut recevoir une hausse brutale de nouveaux utilisateurs. Si il y a 10 000 utilisateurs sur chaque serveur lorsque la région principale subit une interruption de service, la zone secondaire est soudainement à gérer 20 000 utilisateurs. Surveillance des règles sur la zone secondaire doit détecter cette augmentation et double les instances dans la zone secondaire. Pour plus d’informations, consultez la section sur [la détection de défaillance](#failure-detection).

##<a name="hybrid-on-premises-and-cloud-solution"></a>Sur les sites et solution de cloud hybride

Une stratégie supplémentaire pour la reprise après sinistre consiste à concevoir une application hybride qui s’exécute en local et dans le cloud. Selon l’application, la région principale peut être à deux emplacements. Considérez les architectures précédents et supposons que la zone primaire ou secondaire comme un emplacement local.

Il existe certains problèmes dans ces architectures hybride. Tout d’abord, la plupart de cet article a traité des modèles d’architecture PaaS. Parmi les applications PaaS dans Azure s’appuient sur les constructions Azure spécifiques telles que les rôles et les services en nuage Traffic Manager. Pour créer une solution sur site pour ce type d’application de plate-forme PaaS nécessiterait une architecture différente. Il peut être impossible à partir d’une direction ou un point de vue coût.

Toutefois, une solution hybride de reprise après sinistre a moins de difficultés pour des architectures traditionnelles qui ont été simplement déplacés vers le nuage. Cela est vrai d’architecture qui utilise IaaS. Les applications IaaS utilisent des ordinateurs virtuels dans le nuage qui peut avoir des équivalents de direct sur site. Vous pouvez également utiliser des réseaux virtuels pour connecter des ordinateurs dans le nuage avec les ressources du réseau local. Plusieurs possibilités qui ne sont pas possibles avec les applications PaaS seule s’ouvre. Par exemple, SQL Server peut bénéficier des solutions de reprise après sinistre des groupes de disponibilité AlwaysOn la mise en miroir de base de données. Pour plus d’informations, reportez-vous à la section [haute disponibilité et reprise après sinistre pour SQL Server sur des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

IaaS proposent également un chemin d’accès plus facile pour les applications sur site d’utiliser Azure comme l’option de basculement. Vous pouvez avoir une application totalement fonctionnelle dans une zone locale existante. Toutefois, si vous n’avez pas les ressources pour maintenir une région géographiquement de basculement sur incident ? Vous pouvez décider d’utiliser des machines virtuelles et des réseaux virtuels pour que votre application fonctionne dans Azure. Dans ce cas, définir des processus qui synchronisent les données dans le cloud. Le déploiement d’Azure puis devient la zone secondaire à utiliser pour le basculement. La région principale reste l’application locale. Pour plus d’informations sur les capacités et les architectures de IaaS, consultez la [documentation de Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/).

##<a name="alternative-cloud"></a>Nuage de remplacement

Il existe des situations où même la robustesse du Cloud Microsoft pas forcément conformes aux règles de conformité internes ou stratégies requis par votre organisation. Même la meilleure préparation et design pour mettre en œuvre des systèmes de sauvegarde pendant un incident majeur se trouvent courts, s’il y a une interruption de service global d’un fournisseur de service de cloud.

Vous voudrez à comparer avec le coût et la complexité de l’augmentation de la disponibilité, les exigences de disponibilité. Effectuer une analyse des risques et de définir les RTO et RPO pour votre solution. Si votre application ne peut pas tolérer aucune interruption de service, il peut être utile pour vous d’envisager d’utiliser une autre solution de cloud. À moins que la totalité de l’Internet tombe en panne, une autre solution de cloud peut-être toujours disponible si Azure devient inaccessible de façon globale.

Comme dans le scénario hybride, les déploiements de basculement dans les architectures de récupération après sinistre précédente peuvent également exister dans une autre solution de cloud. Sites de nuage autre DR doivent être utilisées uniquement pour les solutions dont RTO permet très peu, le cas échéant, des interruptions de service. Notez qu’une solution qui utilise un site de reprise après sinistre en dehors d’Azure nécessitera plus de travail pour configurer, développer, déployer et mettre à jour. Il est également plus difficile à mettre en œuvre des meilleures pratiques dans une architecture de cross-nuage. Bien que les plateformes en nuage de concepts de haut niveau similaires, les API et les architectures sont différentes.

Si vous décidez de fractionner votre DR entre différentes plates-formes, il serait judicieux de concevoir des couches d’abstraction dans la conception de la solution. Si vous procédez ainsi, vous n’aurez pas besoin de développer et de mettre à jour des deux versions différentes de la même application pour les plates-formes de nuage différent en cas de sinistre. Comme dans le scénario hybride, l’utilisation d’ordinateurs virtuels Azure ou Service de conteneur Azure peut être plus facile dans ces cas que l’utilisation de dessins ou modèles de cloud spécifiques PaaS.

##<a name="automation"></a>Automation

Certains des modèles que nous venons de nécessitent l’activation rapide des déploiements en mode hors connexion, ainsi que la restauration de parties spécifiques d’un système. Automation ou script, prend en charge la possibilité d’activer des ressources à la demande et de déployer rapidement des solutions. Dans cet article, liés à la reprise après sinistre d’automation est mis en équation avec [PowerShell d’Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx), mais l' [API REST de gestion de Service](https://msdn.microsoft.com/library/azure/ee460799.aspx) est également une option.

Développement de scripts permet de gérer les parties de la reprise après sinistre Azure ne gère pas en toute transparence. Ceci a pour avantage de produire des résultats cohérents à chaque fois, ce qui réduit le risque d’erreur humaine. Ayant prédéfinis DR scripts permet également de réduire le temps de reconstruire un système et ses composantes au cours d’un reprise après sinistre. Vous ne souhaitez pas essayer manuellement comment restaurer votre site pendant qu’il est de l’argent vers le bas et perdre toutes les minutes.

Après avoir créé les scripts, les tester à plusieurs reprises à partir du début à la fin. Après avoir vérifié les fonctionnalités de base, assurez-vous que les tests de [simulation de reprise après sinistre](#disaster-simulation). Cela vous permet de découvrir des défauts dans les scripts ou les processus.

Avec l’automation, une meilleure solution consiste à créer un référentiel de scripts de PowerShell ou l’interface de ligne de commande (CLI) pour la reprise après sinistre Azure. Clairement marque et les classer pour la recherche simple. Désigner une personne pour gérer le référentiel et le versioning des scripts. Les documenter avec explications des paramètres et des exemples d’utilisation de script. Également vous assurer que cette documentation synchronisés avec vos déploiements Azure. Cela souligne l’intérêt d’avoir un seul responsable de toutes les parties du référentiel.

##<a name="failure-detection"></a>Détection de défaillance

Pour gérer correctement les problèmes de disponibilité et de reprise après sinistre, vous devez être en mesure de détecter et diagnostiquer les échecs. Vous devez faire avancée des serveurs et surveillance du déploiement afin de savoir rapidement si un système ou ses parties sont soudainement vers le bas. Outils qui ressemblent à la santé globale du service cloud et ses dépendances de contrôle peut effectuer la partie de ce travail. Un outil de Microsoft est le [système Centre de 2016](https://www.microsoft.com/en-us/server-cloud/products/system-center-2016/). Des outils tiers peuvent également fournir des fonctions de contrôle. La plupart des solutions de surveillance suivre les principaux compteurs de performance et de disponibilité du service.

Bien que ces outils sont essentielles, elles ne remplacent pas la nécessité de prévoir de détection de panne et de reporting au sein d’un service en nuage. Vous devez planifier d’utiliser correctement les Diagnostics d’Azure. Compteurs de performance personnalisés ou des entrées de journal des événements peuvent être également partie de la stratégie globale. Cela fournit plus de données lors de défaillances pour diagnostiquer le problème et restaurer toutes les fonctionnalités rapidement. Il fournit également les outils de surveillance permet de déterminer la santé de l’application des mesures supplémentaires. Pour plus d’informations, consultez [Activation des Diagnostics de Azure dans Azure Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md). Pour une présentation de la planification globale « modèle de santé », reportez-vous à la section [Failsafe : conseils pour des Architectures de Cloud souple](https://channel9.msdn.com/Series/FailSafe).

##<a name="disaster-simulation"></a>Simulation de reprise après sinistre

Test de simulation implique la création des situations réelles small sur le sol de travail pour observer comment réagissent les membres de l’équipe. Simulations également indiquent l’efficacité les solutions dans le plan de récupération. Effectuer des simulations de telle façon que les scénarios créés ne perturbent pas commercial réel pendant encore se considèrent comme des situations réelles.

Envisagez la conception d’un type de « menu général » de l’application pour simuler des problèmes de disponibilité manuellement. Par exemple, via une commutation logicielle, déclenchent des exceptions d’accès de base de données pour un module de commande en l’obligeant à un dysfonctionnement. Vous pouvez prendre des approches légers similaires pour d’autres modules au niveau de l’interface réseau.

La simulation met en évidence les problèmes qui ont été abordés mal. Les scénarios de simulations doivent être entièrement contrôlables. Cela signifie que, même si le plan de récupération a échoué, vous pouvez restaurer la situation à la normale sans provoquer des dommages importants. Il est également important que vous informez de gestion de haut niveau sur quand et comment les exercices de simulation seront exécutées. Ce plan doit inclure des informations sur le temps ou les ressources qui peuvent devenir non productives pendant l’exécution du test de simulation. Lorsque vous êtes soumettre votre plan de récupération après sinistre à un test, il est également important de définir la réussite est mesuré.

Il existe plusieurs autres techniques que vous pouvez utiliser pour tester des plans de récupération d’urgence. Toutefois, la plupart d'entre eux est des versions modifiées de simplement de ces techniques de base. La principale motivation de ce test est d’évaluer la faisabilité et réalisable comment le plan de récupération. Test de reprise après sinistre met l’accent sur les détails pour découvrir des trous dans le plan de restauration de base.

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série d’articles consacré à la [reprise après sinistre et une haute disponibilité des applications basées sur Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). Le précédent article de cette série est la [haute disponibilité des applications basées sur Microsoft Azure](./resiliency-high-availability-azure-applications.md).
