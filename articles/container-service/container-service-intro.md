<properties
   pageTitle="Introduction de Service conteneur Azure | Microsoft Azure"
   description="Service de conteneur Azure offre un moyen pour simplifier la création, la configuration et la gestion d’un cluster de machines virtuelles préconfigurées pour exécuter des applications."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Services-docker, conteneurs, Micro, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# <a name="azure-container-service-introduction"></a>Introduction de conteneur Service Azure

Azure conteneur Service rend plus simple vous permettant de créer, de configurer et de gérer un cluster de machines virtuelles préconfigurées pour exécuter des applications. Il utilise une configuration optimisée des outils de planification et d’orchestration open source populaires. Cela vous permet d’utiliser vos compétences existantes ou de dessiner sur un corps de grande taille ou croissant de compétence communautaire, à déployer et à gérer les applications conteneur sur Microsoft Azure.


![Service de conteneur Azure fournit un moyen pour gérer des applications sur plusieurs hôtes dans Azure.](./media/acs-intro/acs-cluster.png)


Service de conteneur Azure s’appuie sur le format de conteneur Docker pour vous assurer que vos conteneurs d’application sont entièrement portables. Il prend également en charge le choix de Marathon et DC/OS ou Docker par essaim afin que vous pouvez adapter ces applications à des milliers de conteneurs, ou même de dizaines de milliers.

En utilisant le Service de conteneur d’Azure, vous pouvez profiter des fonctionnalités à l’échelle de l’entreprise d’Azure, tout en conservant la portabilité des applications, y compris au niveau des couches d’orchestration, la portabilité de la.

<a name="using-azure-container-service"></a>À l’aide du Service de conteneur Azure
-----------------------------

Notre objectif avec Azure conteneur Service est de fournir un environnement d’hébergement à l’aide d’outils de l’open source et les technologies qui sont populaires parmi nos clients aujourd'hui de conteneur. À cette fin, nous exposer les points de terminaison API standard pour votre orchestrator choisi (contrôleur de domaine/OS ou Docker par essaim). À l’aide de ces points de terminaison, vous pouvez utiliser n’importe quel logiciel capable de communiquer avec ces points de terminaison. Par exemple, en ce qui concerne le point de terminaison Docker par essaims particulaires, vous pouvez choisir d’utiliser l’interface de ligne de commande (CLI) Docker. DC/système d’exploitation, vous pouvez choisir d’utiliser la CLI DCOS.

<a name="creating-a-docker-cluster-by-using-azure-container-service"></a>Création d’un cluster de Docker à l’aide du Service de conteneur Azure
-------------------------------------------------------

Pour commencer à utiliser le Service de conteneur d’Azure, vous déployez un cluster Service de conteneur Azure via le portail (recherche de « Service de conteneur Azure »), à l’aide d’un modèle de gestionnaire de ressources Azure ([Docker par essaim](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm) ou [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)) ou avec la [CLI](/documentation/articles/xplat-cli-install/). Les modèles de démarrage rapide fourni peuvent être modifiés pour inclure des supplémentaire ou advanced configuration Azure. Pour plus d’informations sur le déploiement d’un cluster Service de conteneur d’Azure, voir [déploiement d’un cluster Service de conteneur d’Azure](container-service-deployment.md).

<a name="deploying-an-application"></a>Déploiement d’une application
------------------------

Service de conteneur Azure propose la Docker par essaim ou DC/système d’exploitation pour l’orchestration. Façon dont vous déployez votre application dépend de votre choix d’orchestrator.

### <a name="using-dcos"></a>À l’aide du contrôleur de domaine/OS

Contrôleur de domaine/système d’exploitation est un système d’exploitation distribué, basé sur le noyau de systèmes distribués Apache Mesos. Apache Mesos est hébergé à l’Apache Software Foundation et répertorie certaines de la [plus grands noms en informatique](http://mesos.apache.org/documentation/latest/powered-by-mesos/) en tant qu’utilisateurs et contributeurs.

![Service de conteneur Azure configuré pour essaim montrant des agents et des formes de base.](media/acs-intro/dcos.png)

DC/OS et Apache Mesos incluent un ensemble de fonctionnalités impressionnantes :

-   Évolutivité éprouvée

-   Réplication à tolérance de pannes maître et les esclaves à l’aide de Apache soigneur

-   Prise en charge des conteneurs d’au format Docker

-   Isolation entre les tâches avec des conteneurs de Linux native

-   Multiresource planification (mémoire, processeur, disque et ports)

-   Java, Python et APIs C++ pour le développement de nouvelles applications parallèles

-   Une interface utilisateur web permettant d’afficher l’état du cluster

Par défaut, DC/système d’exploitation s’exécutant sur le Service de conteneur Azure inclut la plate-forme d’orchestration Marathon pour la planification des charges de travail. Toutefois, inclus dans le déploiement de contrôleurs de domaine/système d’exploitation de ACS est l’univers mésosphère de services qui peuvent être ajoutés à votre service, citons étincelle, Hadoop, Cassandra et bien plus encore.

![Univers DC/système d’exploitation dans le Service de conteneur Azure](media/dcos/universe.png)

#### <a name="using-marathon"></a>À l’aide de Marathon

Marathon est un système de contrôle pour les services cgroups--ou, dans le cas d’Azure conteneur Service, au format Docker des conteneurs et l’init de l’ensemble du cluster. Marathon fournit une interface web à partir de laquelle vous pouvez déployer vos applications. Vous pouvez l’accéder à une URL qui ressemble à `http://DNS_PREFIX.REGION.cloudapp.azure.com` où DNS\_préfixe et zone sont tous deux définis au moment du déploiement. Bien sûr, vous pouvez également fournir votre propre nom DNS. Pour plus d’informations sur l’exécution d’un conteneur à l’aide de l’interface utilisateur du web Marathon, consultez [gestion du conteneur par le biais de l’interface utilisateur web](container-service-mesos-marathon-ui.md).

![Liste des Applications de marathon](media/dcos/marathon-applications-list.png)

Vous pouvez également utiliser les API reste pour communiquer avec le Marathon. Il existe un certain nombre de bibliothèques clientes qui sont disponibles pour chaque outil. Elles couvrent une variété de langues--et, bien sûr, vous pouvez utiliser le protocole HTTP dans tout langage. En outre, de nombreux outils DevOps courants prennent en charge Marathon. Cela offre une souplesse maximale pour votre équipe des opérations lorsque vous travaillez avec un cluster Service de conteneur d’Azure. Pour plus d’informations sur l’exécution d’un conteneur à l’aide de l’API REST de Marathon, consultez [Gestion des conteneurs avec l’API REST](container-service-mesos-marathon-rest.md).

### <a name="using-docker-swarm"></a>À l’aide de Docker essaim

Docker essaim fournit native de clustering pour Docker. Car Docker par essaim remplit l’API Docker standard, n’importe quel outil déjà communique avec un démon Docker pouvez utiliser essaim à l’échelle de façon transparente à plusieurs hôtes sur Azure conteneur de Service.

![Service de conteneur Azure configuré pour utiliser le contrôleur de domaine/du système d’exploitation--montrant les jumpbox, les agents et les formes de base.](media/acs-intro/acs-swarm2.png)

Les outils pris en charge pour la gestion des conteneurs sur un cluster essaim incluent, mais ne sont pas limitées à, les éléments suivants :

-   Dokku

-   Composent de docker CLI et Docker

-   Krane

-   Jenkins

<a name="videos"></a>Vidéos
------

Mise en route avec le Service de conteneur Azure (101) :  

> [AZURE.VIDEO azure-container-service-101]

Développement d’Applications en utilisant le Service de conteneur Azure (Build 2016)

> [AZURE.VIDEO build-2016-building-applications-using-the-azure-container-service]
