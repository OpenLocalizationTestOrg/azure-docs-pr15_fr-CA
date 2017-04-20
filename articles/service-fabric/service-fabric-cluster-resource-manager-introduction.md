<properties
   pageTitle="Présentation du Gestionnaire de ressources de Cluster Service Fabric | Microsoft Azure"
   description="Introduction au Gestionnaire de ressources Cluster Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Présentation du Gestionnaire de ressources de cluster Service Fabric
Traditionnellement gestion des systèmes informatiques ou un ensemble de services signifiait quelques ordinateurs physiques ou virtuels dédiés à ces systèmes ou de services spécifiques. De nombreux services principaux ont été décomposés en une couche de « web » et une couche de « données » ou de « stockage », peut-être avec quelques autres composants spécialisés comme un cache. Autres types d’applications aurait à une couche de messagerie où les demandes passées en entrée et de sortie, connecté à un niveau de travail pour toute analyse ou la transformation nécessaire dans le cadre de la messagerie. Chaque type de charge de travail a reçu des machines spécifiques dédiées : la base de données a reçu deux ordinateurs dédiés, les serveurs web un peu. Si un type particulier de la charge de travail a provoqué les ordinateurs qu’il était sur Exécuter trop chaud, puis vous avez ajouté plus de machines avec ce type de charge de travail configuré pour s’exécuter dessus ou remplacé certains des ordinateurs avec des machines plus grandes. Facile. Si un ordinateur a échoué, cette partie de l’application globale a exécuté au moins de capacité jusqu'à ce que l’ordinateur peut être restauré. Toujours relativement facile (si pas nécessairement amusants).

Toutefois, nous allons dire que vous avez trouvé nécessaire de faire évoluer et ont pris les conteneurs et/ou microservice plongée. Soudain vous vous retrouvez avec des dizaines ou des centaines voire des milliers de machines, des dizaines de différents types de services, peut-être des centaines de différentes instances de ces services, chacun avec un ou plusieurs des instances ou des réplicas pour la haute disponibilité (HA).

Gestion soudain de votre environnement n’est pas aussi simple que de gérer quelques ordinateurs dédiés à des types de charges de travail unique. Et vos serveurs virtuels n’ont plus les noms (vous *avez* basculé mentalités à partir [d’animaux de compagnie pour le bétail](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) après tout). La configuration est inférieure sur les ordinateurs et plus d’informations sur les services eux-mêmes. Matériel dédié est un partie du passé et services sont devenus des petits systèmes distribués, couvrant plusieurs parties plus petites de matériel standard.

Fait de diviser votre application anciennement monolithique, hiérarchisée en services distincts en cours d’exécution sur le matériel, vous disposez désormais de nombreuses combinaisons de plus pour y faire face. Qui détermine les types de charges de travail peuvent s’exécuter sur le matériel, ou combien ? Quelles charges de travail fonctionnent correctement sur le même matériel, et qui sont en conflit ? Lorsqu’un ordinateur tombe en panne... ce qui était encore en cours d’exécution il ? Qui est chargé de s’assurer que la charge de travail recommence à s’exécuter ? Attendre l’ordinateur (virtuel) ? revenir ou vos charges de travail automatiquement basculer vers d’autres ordinateurs et les conserver en cours d’exécution ? Est une intervention humaine requise ? Qu’en est-il des mises à niveau dans ce type d’environnement ?

Comme les développeurs et les opérateurs vivant dans ce tri du monde, nous allons avoir besoin d’aide pour gérer cette complexité, et vous obtenez le sens qu’un recrutement binge et essayez de papier sur la complexité avec des personnes n’est pas la bonne réponse.

Que faire ?

## <a name="introducing-orchestrators"></a>Présentation d’orchestrators
Un « Orchestrator » est le terme général pour un élément logiciel qui permet aux administrateurs de gérer ces types d’environnements. Orchestrators sont les composants qui prennent dans les requêtes, comme « J’aimerais 5 copies de ce service en cours d’exécution dans mon environnement », rendent la valeur true, puis (Réessayer) maintenir ce niveau.

Orchestrators (non humains) sont ce qui étudient lorsqu’un ordinateur tombe en panne ou à une charge de travail se termine pour une raison quelconque. Orchestrators la plupart des plus que simplement gérer les défaillances, comme aider les nouveaux déploiements, gestion des mises à niveau et traitement de la consommation des ressources, mais toutes sont fondamentalement sur le maintien de que certaines souhaité de l’état de la configuration de l’environnement. Vous souhaitez pouvoir dire un Orchestrator que vous souhaitez et faire le gros du travail. Chronos ou Marathon sur Mesos, flotte, essaim, Kubernetes et tissu de Service sont des exemples de Orchestrators (ou ayant intégré). Plus sont créées tout le temps que la complexité de la gestion des déploiements réels dans différents types d’environnement et de conditions se développent et évoluent.

## <a name="orchestration-as-a-service"></a>Orchestration en tant que service
Le travail de la Orchestrator au sein d’un cluster de Service Fabric est géré principalement par le Gestionnaire de ressources de Cluster. La ressource de Cluster Service Fabric Manager est un des Services système dans le Service Fabric et est démarré automatiquement dans chaque cluster.  En règle générale, la tâche du Gestionnaire de ressources de Cluster est divisée en trois parties :

1. Application des règles
2. Optimisation de votre environnement.
3. Assistance dans d’autres processus

### <a name="what-it-isnt"></a>Qu’il n’est pas
Traditionnel N niveau-applications web il y avait toujours des notions de « Équilibreur de charge », généralement appelé un équilibreur de charge réseau (NLB) ou un équilibreur de charge d’Application (ALB) selon où il assis dans la pile réseau. Certains équilibreurs de charge sont matériel comme l’offre de F5 BigIP et d’autres logiciels, tel que Microsoft NLB. Dans d’autres environnements vous pouvez voir quelque chose comme HAProxy dans ce rôle. Dans ces architectures le travail d’équilibrage de charge est de s’assurer que tous les ordinateurs de différents sans état front-end ou les différents ordinateurs dans le cluster (à peu près) la même quantité de travail. Stratégies pour ce varié, d’envoyer chaque appel différent à un autre serveur, pour une session épinglage/caractère collant, à la répartition de l’estimation et l’appel réelle en fonction de son coût prévu et le chargement de machine en cours.

Notez que c’était au mieux le mécanisme de veiller à ce que la couche web est restée à peu près équilibrée. Stratégies pour l’équilibrage de la couche de données ont été complètement différent et des dépendants sur le mécanisme de stockage de données, généralement centrant sur ont des données, la mise en cache, les vues de base de données gérée et les procédures stockées, etc..

Alors que certains de ces stratégies sont intéressantes, le Gestionnaire de ressources de Cluster Service Fabric n’est rien comme un équilibreur de charge réseau ou d’un cache. Tandis qu’un équilibrage de charge réseau garantit que les serveurs frontaux est équilibrés en déplaçant le trafic pour lequel les services s’exécutent, la ressource de Cluster Service Fabric Manager prend une stratégie complètement différente, fondamentalement, Service Fabric déplace des *services* vers où leur impact est le plus approprié (et attend le trafic ou la charge à suivre). Ce peut être, par exemple, les nœuds qui sont actuellement à froid, car les services qui sont les font pas beaucoup de travail maintenant ou qui ont été supprimés ou déplacés ailleurs. Un autre exemple le Gestionnaire de ressources de Cluster peut également déplacer un service à partir d’un ordinateur qui est sur le point d’être mis à niveau ou qui est surchargé en raison d’un pic de la consommation par les services qui s’exécutaient sur celui-ci. Étant donné que le Gestionnaire de ressources du Cluster est responsable du déplacement des services autour de (garantissent ne pas le trafic réseau à l’endroit où les services sont déjà), il contient un ensemble de fonctionnalité considérablement différente par rapport à ce que vous trouveriez dans un équilibreur de charge réseau et emploie fondamentalement différentes stratégies pour s’assurer que les ressources matérielles dans le cluster sont également utilisées.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur le flux d’architecture et d’informations dans le Gestionnaire de ressources de Cluster, consultez [cet article](service-fabric-cluster-resource-manager-architecture.md)
- Le Gestionnaire de ressources de Cluster comporte un grand nombre d’options pour la description du cluster. Pour en savoir plus sur les consultez cet article sur la [Description d’un tissu de Service de cluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Pour plus d’informations sur les autres options disponibles pour la configuration des services d’extraction la rubrique sur les autres configurations de gestionnaire de ressources de Cluster disponibles [en savoir plus sur la configuration des Services](service-fabric-cluster-resource-manager-configure-services.md)
- Métriques sont la façon dont le Gestionnaire de ressources de Cluster Service de Fabric gère la consommation et la capacité du cluster. Pour en savoir plus sur comment les configurer et de les extraire [cet article](service-fabric-cluster-resource-manager-metrics.md)
- Le Gestionnaire de ressources de Cluster fonctionne avec les fonctionnalités de gestion de Service Fabric. Pour plus d’informations sur cette intégration, lisez [cet article](service-fabric-cluster-resource-manager-management-integration.md)
- Pour savoir plus sur comment le Gestionnaire de ressources de Cluster gère et équilibre la charge du cluster, consultez l’article sur [l’équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
