<properties
   pageTitle="Vue d’ensemble du Service Fabric | Microsoft Azure"
   description="Vue d’ensemble du Service de Fabric, où les applications sont composées de nombreux microservices pour fournir évolutivité et résilience. TISSU de service est une plate-forme de systèmes distribués permet de générer des évolutives, fiables et gérées facilement des applications pour le nuage"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="mfussell"/>

# <a name="overview-of-service-fabric"></a>Vue d’ensemble de tissu de Service
TISSU de service est une plate-forme de systèmes distribués qui le rend facile à empaqueter, de déployer et de gérer des microservices fiables et évolutives. Service de Fabric résout également les problèmes importants dans le développement et la gestion des applications en nuage. Les développeurs et les administrateurs peuvent éviter à la place la résolution du focus et des problèmes d’infrastructure complexes sur l’implémentation des charges de travail critiques et les plus exigeants sachant qu’elles sont évolutives, fiables et faciles à gérer. TISSU de service représente la plate-forme middleware de nouvelle génération pour la création et la gestion de ces entreprises, les applications de cloud à l’échelle de niveau 1.

Cette [courte vidéo](https://aka.ms/servicefabricvideo) fournit une introduction à Service Fabric et microservices.


## <a name="applications-composed-of-microservices"></a>Les applications composées de microservices
Service de Fabric permet vous permet de créer et de gérer des applications évolutives et fiables composées de microservices en cours d’exécution à très haute densité sur un pool partagé d’ordinateurs (appelé un cluster). Il fournit un runtime sophistiqué pour la création d’un microservices distribué et évolutif avec et sans état. Il fournit également des fonctionnalités de gestion d’application complète pour la mise en service, de déploiement, de surveillance, de mise à jour/correctifs et la suppression des applications déployées.

Pourquoi est-il une approche microservices important ? Les deux raisons principales sont les suivantes :

1. Ils vous permettent de vous mettre à l’échelle des différentes parties de votre application en fonction de ses besoins.

2. Les équipes de développement sont capables d’être plus souples dans le déploiement de modifications et fournissent ainsi des fonctionnalités à vos clients plus rapidement et plus fréquemment.

Service de tissu alimente plusieurs services Microsoft aujourd'hui, y compris la base de données de SQL Azure, Azure DocumentDB, Cortana, alimentation BI, Microsoft Intune, Azure événement concentrateurs, Azure IoT, Skype pour les entreprises et la plupart des services Azure de base.

TISSU de service est conçue pour la création de cloud services natifs qui peuvent commencer, en fonction des besoins et atteindre à grande échelle avec des centaines ou des milliers d’ordinateurs.

Les services à l’échelle de l’Internet d’aujourd'hui sont composés de microservices. Exemples de microservices des passerelles de protocole, profils d’utilisateur, vos achats paniers, des stocks, traitement des files d’attente et met en cache. TISSU de service est une plate-forme de microservices qui donne à chaque microservice un nom unique qui peut être sans état ou avec état.

TISSU de service propose des fonctionnalités complètes gestion de runtime et du cycle de vie d’applications composées de ces microservices. Il héberge microservices à l’intérieur de conteneurs déployé et activé sur le cluster Service Fabric. Déplacement à partir d’ordinateurs virtuels à possible de fait de conteneurs une augmentation de l’ordre de grandeur densité. De même, un autre ordre de grandeur de densité devient possible en déplaçant des conteneurs vers microservices. Par exemple, un seul cluster de base de données de SQL Azure comprend des centaines de machines, des dizaines de milliers de conteneurs hébergeant un total de centaines de milliers de bases de données en cours d’exécution. Chaque base de données est un microservice avec état Service Fabric. Cela vaut des autres services mentionnés précédemment, c’est pourquoi le terme « hyperscale » est utilisé pour décrire les fonctionnalités du Service Fabric. Si les conteneurs vous donnent à haute densité, puis microservices donne hyperscale.

Pour plus d’informations sur l’approche microservices, lisez [Pourquoi une approche microservices de création d’applications ?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>L’orchestration et le déploiement de conteneur
Service de Fabric est un [orchestrator](service-fabric-cluster-resource-manager-introduction.md) de microservices au sein d’un cluster d’ordinateurs. Microservices peuvent être développés de nombreuses façons d’utiliser les [modèles de programmation de Service Fabric](service-fabric-choose-framework.md) pour le déploiement des [fichiers exécutables d’invité](service-fabric-deploy-existing-app.md). Service de Fabric peut déployer des services dans les images de conteneur et important, vous pouvez combiner les services dans les processus et services dans les conteneurs ensemble dans la même application. Si vous souhaitez que juste à [déployer et à gérer les images d’un conteneur](service-fabric-containers-overview.md) au sein d’un cluster d’ordinateurs, Service Fabric est le choix idéal pour cela.


## <a name="create-service-fabric-clusters-anywhere"></a>Créer des clusters de Service Fabric n’importe où
Vous pouvez créer des clusters de Service Fabric dans de nombreux environnements, notamment d’Azure, sur Windows Server ou dans les locaux ou sur Linux. En outre, l’environnement de développement dans le Kit de développement est identique à l’environnement de production avec aucun émulateurs impliqués. En d’autres termes, si elle s’exécute sur votre cluster de développement local qu’il déploie sur le même cluster dans d’autres environnements.

Pour plus d’informations sur la création de clusters sur site, consultez [Création d’un cluster de serveur Windows ou Linux](service-fabric-deploy-anywhere.md) , ou pour Azure création d’un cluster [via le portail Azure](service-fabric-cluster-creation-via-portal.md).

![Plate-forme de service Fabric][Image1]

## <a name="stateless-and-stateful-service-fabric-microservices"></a>Microservices de Service Fabric avec et sans état

Fabric de service vous permet de créer des applications qui se compose de microservices. Microservices sans état (passerelles de protocole, les proxys web, etc.) ne conservent pas un état mutable en dehors de toute demande donnée et de sa réponse à partir du service. Les rôles de travail de Services en nuage Azure sont un exemple de service sans état. Microservices avec état (comptes d’utilisateurs, bases de données, périphériques, shopping paniers, files d’attente, etc.) conservent un état mutable, faisant autorité au-delà de la demande et la réponse. Échelle d’Internet, les applications actuelles sont constitués d’une combinaison de microservices avec et sans état.

Pourquoi avoir microservices avec état ainsi que de ceux qui sont sans état ? Les deux raisons principales sont les suivantes :

1. La capacité à haut débit et à faible latence, à tolérance de panne de traitement transactionnel en ligne les services (OLTP) en maintenant le code et les données de fermer sur le même ordinateur de build. Quelques exemples sont des présentations interactives, recherche, Internet des objets (IoT) systèmes, systèmes de négociation, systèmes de détection des fraudes et de traitement de carte de crédit et gestion des enregistrements de personnel.

2. Simplification de conception d’application. Microservices avec état suppriment la nécessité des files d’attente supplémentaires et met en mémoire cache, généralement requises pour répondre aux besoins de disponibilité et de la latence d’une application purement sans état. Avec état services sont naturellement une disponibilité élevée et à faible latence, réduisant le nombre d’éléments mobiles à gérer dans votre application comme un tout.

Pour plus d’informations sur les modèles d’application avec un Service de Fabric, lire des [scénarios d’Application](service-fabric-application-scenarios.md) et le [choix d’une infrastructure de modèle de programmation](service-fabric-choose-framework.md) de votre service

## <a name="application-lifecycle-management"></a>Gestion du cycle de vie des applications
Service Fabric fournit une excellente prise en charge pour la complète application lifecycle management (ALM) des applications du nuage--de développement jusqu’au déploiement, la gestion quotidienne et maintenance au déclassement éventuelle.

Les fonctionnalités ALM de tissu de Service permettent aux administrateurs d’applications / opérateurs informatiques pour utiliser des flux de travail simple et peu provisionner, déployer, correctif et surveiller des applications. Ces flux de travail intégrés de réduire considérablement la charge pesant sur les opérateurs informatiques vos applications disponibles en permanence.

La plupart des applications sont constitués d’une combinaison de microservices avec et sans état et d’autres fichiers exécutables/runtimes déployés ensemble. En ayant des types forts sur les applications et les paquets microservices, Service Fabric permet le déploiement de plusieurs instances de l’application. Chaque instance est géré et mis à jour indépendamment. Surtout, Service est en mesure de déployer *des* exécutables ou runtime et de les rendre plus fiables. Par exemple, Service Fabric déploie ASP.NET Core 1, Node.js, Java VM, scripts ou tout autre élément qui compose votre application.

Pour plus d’informations sur la gestion du cycle de vie des applications, lisez le [cycle de vie des applications](service-fabric-application-lifecycle.md) et sur le déploiement de n’importe quel code voir [déployer un invité exécutable](service-fabric-deploy-existing-app.md)

## <a name="key-capabilities"></a>Fonctionnalités clés
À l’aide de tissu de Service, vous pouvez :

- Développer des applications hautement évolutives qui sont des fonctions de réparation automatique.

- Développement d’applications composées de microservices en utilisant le modèle de programmation de Service Fabric. Ou, simplement hôte invité exécutables et autres infrastructures d’application de votre choix, par exemple ASP.NET Core 1 ou Node.js.

- Développer des microservices sans et avec état très fiables.

- Déployer et orchestrer les conteneurs incluent des conteneurs de Windows et Docker au sein d’un cluster. Ces conteneurs peuvent conteneur invité exécutables ou des microservices sans et avec état fiables.  Dans les deux cas, vous obtenez port de conteneur pour le mappage de port hôte, découverte de conteneur et basculement automatique.

- Simplifier la conception de votre application à l’aide de microservices dynamique à la place des caches et des files d’attente.

- Déployer vers Azure ou à nuages local exécutant le serveur Windows ou Linux avec zéro modifications du code. Écrire qu’une seule fois et les déployer ensuite n’importe où sur un cluster Service Fabric.

- Développer une approche de « centre de données sur votre ordinateur ». L’environnement de développement local est le même code que celui qui s’exécute dans les centres de données Azure.

- Déployer des applications en quelques secondes.

- Déployer des applications à la densité supérieure à celle des ordinateurs virtuels, déployer des centaines ou des milliers d’applications par ordinateur.

- Déployer des versions différentes de la même application côte à côte, chaque indépendamment mise à jour.

- Gérer le cycle de vie de vos applications avec état sans interruption de service, y compris les mises à niveau de rupture et insécables.

- Gérer des applications à l’aide des API .NET, Java (Linux), PowerShell, Azure CLI (Linux) ou autres interfaces.

- Mise à niveau et correctifs indépendamment de microservices au sein des applications.

- Surveiller et diagnostiquer l’état de santé de vos applications et définir des stratégies pour effectuer des réparations automatiques.

- Possibilité d’évolution cloisonnée ou échelle dans le nombre de nœuds dans un cluster, ainsi que l’extrapolation ou échelle en bas de la taille de chaque nœud, en sachant que vos applications mise à l’échelle automatiquement et sont distribuées en fonction des ressources disponibles.

- Regarder l’autoréparation équilibreur de ressource orchestrer la redistribution d’applications au sein du cluster. TISSU de service récupère des échecs et optimise la distribution de charge en fonction des ressources disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations :
    * [Pourquoi un microservices approche à la génération d’applications ?](service-fabric-overview-microservices.md)
    * [Vue d’ensemble de la terminologie](service-fabric-technical-overview.md)
* Configuration de votre [environnement de développement](service-fabric-get-started.md) tissu de Service  
* Le [choix d’une infrastructure de modèle de programmation](service-fabric-choose-framework.md) de votre service

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
