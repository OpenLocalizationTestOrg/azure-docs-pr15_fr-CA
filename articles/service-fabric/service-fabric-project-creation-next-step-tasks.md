<properties
   pageTitle="Étapes suivantes de création de projet service Fabric | Microsoft Azure"
   description="Cet article contient des liens vers un ensemble de tâches de développement de base pour le Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="your-service-fabric-application-and-next-steps"></a>Votre application de Service Fabric et les étapes suivantes
Votre application de Service de Azure Fabric a été créée. Cet article décrit la création de votre projet et certaines étapes potentiels.

## <a name="your-application"></a>Votre application
Chaque nouvelle application inclut un projet d’application. Il peut y avoir un ou deux autres projets, en fonction du type de service choisi.

### <a name="the-application-project"></a>Le projet d’application
Le projet d’application se compose de :

- Un ensemble de références aux services qui composent votre application.

- Deux publier les profils (locaux et Cloud) que vous pouvez utiliser pour mettre à jour les préférences de travail avec différents environnements, tels que les préférences liées à un point de terminaison du cluster et d’effectuer des déploiements de mise à niveau par défaut.

- Deux paramètre fichiers d’application (locaux et Cloud) qui vous permet de maintenir des configurations d’application spécifique à un environnement, telles que le nombre de partitions à créer pour un service.

- Un script de déploiement que vous pouvez utiliser pour déployer votre application à partir de la ligne de commande ou dans le cadre d’un pipeline de déploiement et d’intégration continu automatique.

- Le manifeste d’application qui décrit l’application. Vous pouvez trouver le manifeste dans le dossier ApplicationPackageRoot.

### <a name="stateless-service"></a>Service sans état
Lorsque vous ajoutez un nouveau service sans état, Visual Studio ajoute un projet de service de votre solution qui inclut un type affilié à `StatelessService`. Le service incrémente une variable locale dans un compteur.

### <a name="stateful-service"></a>Service avec état
Lorsque vous ajoutez un nouveau service avec état, Visual Studio ajoute un projet de service de votre solution qui inclut un type affilié à `StatefulService`. Le service incrémente un compteur dans son `RunAsync` méthode et stocke le résultat dans un `ReliableDictionary`.

### <a name="actor-service"></a>Service de l’acteur
Lorsque vous ajoutez un nouveau acteur fiable, Visual Studio ajoute deux projets à votre solution : un projet de l’acteur et un projet d’interface.

Le projet de l’acteur fournit des méthodes pour la définition et l’obtention de la valeur d’un compteur qui est rendu persistant fiable au sein de l’état de l’acteur. Le projet d’interface fournit une interface que les autres services peuvent utiliser pour invoquer l’acteur.

### <a name="stateless-web-api"></a>API de Web sans état
Le projet Web API sans état fournit un service web de base que vous pouvez utiliser pour ouvrir votre application à des clients externes. Pour plus d’informations sur la façon du projet structuré, consultez [services API de Web Service Fabric avec OWIN d’auto-hébergement](service-fabric-reliable-services-communication-webapi.md).

### <a name="aspnet-core"></a>Noyau d’ASP.NET

Le Kit de développement de tissu de Service fournit le même ensemble de base d’ASP.NET modèles qui sont disponibles pour l’autonomie des projets ASP.NET Core : vide, [API de Web][aspnet-webapi]et [l’Application Web][aspnet-webapp].

## <a name="next-steps"></a>Étapes suivantes
### <a name="create-an-azure-cluster"></a>Créez un cluster d’Azure
Le Kit de développement de tissu de Service fournit un cluster local de développement et de test. Pour créer un cluster dans Azure, consultez [configuration d’un cluster à structure de Service à partir du portail Azure][create-cluster-in-portal].

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>Essayez de déployer vers Azure libre avec les clusters de partie

Si vous souhaitez essayer de déploiement et la gestion des applications dans Azure sans configuration de vos clusters, vous pouvez utiliser le [service de cluster tiers](http://aka.ms/tryservicefabric)de libre.

### <a name="publish-your-application-to-azure"></a>Publiez votre application vers Azure
Vous pouvez publier votre application directement à partir de Visual Studio à un cluster Azure. Pour savoir comment procéder, consultez la [publication de votre application vers Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Explorateur de tissu de Service permet de visualiser votre cluster
Service Fabric Explorer offre un moyen simple de visualiser votre cluster, y compris les applications déployées et disposition physique. Pour plus d’informations, consultez [visualiser votre cluster à l’aide du Service de Fabric Explorer][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Version et mise à niveau de vos services
Service de Fabric permet de versioning indépendant et mise à niveau des services indépendants dans une application. Pour plus d’informations, consultez [Gestion des versions et la mise à niveau de vos services de][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Configurer l’intégration continue avec Visual Studio Team Services
Pour savoir comment vous pouvez configurer un processus d’intégration continue pour votre application de Service Fabric, voir [configurer l’intégration continue avec Visual Studio Team Services][ci-with-vso].


<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
