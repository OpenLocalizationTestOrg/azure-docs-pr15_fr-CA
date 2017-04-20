<properties
    pageTitle="Quelles sont les applications Mobile"
    description="Découvrez quels sont les avantages App Service apporte à votre entreprise les applications mobiles."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>Ce qui est des applications Mobile ?

Service d’application Azure est une entièrement géré [plate-forme en tant que Service](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) pour les développeurs professionnels qui offre un ensemble complet de fonctionnalités web, mobile et de scénarios d’intégration. *Applications Mobile* dans le *Service d’application Azure* offre une plate-forme de développement des applications mobiles évolutives, globalement disponibles pour les développeurs d’entreprise et aux intégrateurs système qui offre un ensemble rich de fonctionnalités pour les développeurs mobiles.

![Applications mobiles](./media/app-service-mobile-value-prop/overview.png)

##<a name="why-mobile-apps"></a>Pourquoi les applications mobiles ?
*Applications Mobile* dans le *Service d’application Azure* offre une plate-forme de développement des applications mobiles évolutives, globalement disponibles pour les développeurs d’entreprise et aux intégrateurs système qui offre un ensemble rich de fonctionnalités pour les développeurs mobiles. Avec les applications Mobile, vous pouvez :

- **Build native et croisée des applications de plate-forme** - si vous générez natif iOS, Android et Windows applications multiplates-formes Xamarin ou Cordova (Phonegap) applications, vous pouvez tirer parti du Service d’application à l’aide de kits de développement logiciel natifs.
- **Se connecter à vos systèmes d’entreprise** - avec les applications Mobile ajouter d’entreprise signe sur en quelques minutes et vous connecter à votre entreprise sur site ou ressources en nuage.
- **Créer des applications en mode hors connexion compatibles avec la synchronisation de données** - rendre votre personnel mobile productive par les applications de création qui fonctionnent en mode hors connexion et applications Mobile pour la synchronisation des données en arrière-plan lorsque la connectivité est présente avec n’importe lequel de vos sources de données d’entreprise ou de SaaS APIs.
- **Notifications de type Pousser vers des millions de secondes** - engager vos clients avec les notifications de transmission instantanée sur n’importe quel périphérique, personnalisés pour leurs besoins, envoyés lorsque le moment sera venu.

## <a name="mobile-app-features"></a>Fonctionnalités de l’application mobile
Les fonctionnalités suivantes sont importantes pour le développement mobile activé pour le cloud :

- **Authentification et autorisation** , sélectionnez à partir d’une liste de fournisseurs d’identité, y compris d’Azure Active Directory pour l’authentification de l’entreprise, ainsi que des fournisseurs sociaux tels que Facebook, Google, Twitter et Account Microsoft.  Les applications Mobile Azure propose un service de OAuth 2.0 pour chaque fournisseur.  Vous pouvez également intégrer le Kit de développement pour le fournisseur d’identité pour les fonctionnalités spécifiques du fournisseur.

  En savoir plus sur nos [fonctionnalités d’authentification].

- **Accès aux données** : applications de Mobile Azure fournit une source de données compatibles avec les mobiles OData v3 liée à une de SQL Server sur site ou à SQL Azure.  Ce service peut être basé sur l’Entity Framework, ce qui vous permet d’intégrer facilement avec les autre NoSQL, et les fournisseurs de données SQL, y compris les fournisseurs de [Stockage par Table Azure], MongoDB, [DocumentDB] et les API de SaaS tels que Office 365 et Salesforce.com.
- **Synchronisation hors connexion** - vérifiez nos kits de développement logiciel Client plus facile pour vous de créer des applications de mobiles robustes et réactives qui fonctionnent avec des données hors ligne la valeur qui peuvent être automatiquement synchronisées avec les données back-end, y compris la prise en charge de résolution de conflit.

  En savoir plus sur nos [fonctions données].

- **Notifications de type Pousser** - nos kits de développement logiciel Client s’intègrent en toute transparence avec les fonctionnalités d’enregistrement de concentrateurs de Notification d’Azure, ce qui vous permet d’envoyer des notifications de type Pousser vers des millions d’utilisateurs simultanément.

  En savoir plus sur nos [pousser les fonctionnalités de notification].

- **Kits de développement logiciel client** - nous fournir un ensemble complet de kits de développement logiciel Client qui couvrent le développement natif ([iOS], [Android] et [Windows]), développement multiplateforme ([Xamarin pour iOS et Android], [Xamarin Forms]) et le développement d’applications hybrides ([Apache Cordova]).  Chaque client SDK est disponible avec une licence MIT et open source.

## <a name="azure-app-service-features"></a>Fonctionnalités de Service d’application Azure.
Les fonctionnalités suivantes de la plate-forme sont généralement utiles pour les sites de production mobile.

- **Mise à l’échelle** - Service de l’application vous permet rapidement de l’extrapolation ou out pour gérer la charge client entrantes. Choisir le nombre et la taille des ordinateurs virtuels ou configurer la mise à l’échelle à l’échelle de votre back-end de l’application mobile en fonction de la charge ou de la planification manuellement.

  En savoir plus sur la [mise à l’échelle].

- **Environnements de reclassement** - Service d’application peut s’exécuter plusieurs versions de votre site, ce qui vous permet d’effectuer un / B test, test de production dans le cadre d’un plan de DevOps supérieur et de place la mise en œuvre d’un nouveau backend.

  En savoir plus sur les [environnements de transfert].

- **DEPLOIEMENT continu** - Service de l’application peuvent s’intégrer aux systèmes SCM courantes, vous permettant de déployer automatiquement une nouvelle version de votre back-end en poussant vers une branche de votre système SCM.

  En savoir plus sur les [options de déploiement].

- **Réseau virtuel** - Service d’application peut se connecter aux ressources sur site à l’aide des connexions réseau, ExpressRoute ou hybride virtuelles.

  Découvrez plus sur [les connexions hybride], [réseaux virtuels]et [ExpressRoute].

- **Isolé / dédié environnements** -Service d’application peut être exécuté dans un environnement totalement isolé et dédié pour exécuter en toute sécurité des applications de Service d’application Azure à grande échelle.  Cela est idéal pour des charges de travail nécessitant une très grande échelle, l’isolement ou un accès réseau sécurisé.

  En savoir plus sur les [Environnements de Service d’application].

## <a name="getting-started"></a>Mise en route ##
Pour vous familiariser avec les applications Mobile, suivez le didacticiel [Mise en route] .  Il couvre les principes fondamentaux de la production d’un back-end mobile et le client de votre choix, puis d’intégration de l’authentification, de synchronisation hors connexion et de notifications de type Pousser.  Vous pouvez suivre le didacticiel [Mise en route] plusieurs fois : une fois pour chaque application cliente.

Pour plus d’informations sur les applications Azure Mobile, veuillez consulter notre [plan de formation].
Pour plus d’informations sur la plate-forme de services d’application Azure, voir [Service d’application Azure].

>[AZURE.NOTE]Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](https://tryappservice.azure.com/?appServiceName=mobile), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Service d’application Azure]: ../app-service/app-service-value-prop-what-is.md
[Mise en route]: app-service-mobile-ios-get-started.md
[Stockage par Table Azure]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[fonctionnalités d’authentification]: ./app-service-mobile-auth.md
[fonctionnalités de données]: ./app-service-mobile-offline-data-sync.md
[fonctionnalités de notification de transmission]: ../notification-hubs/notification-hubs-push-notification-overview.md
[e/s]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin pour iOS et Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Formulaires de Xamarin]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[mise à l’échelle]: ../app-service-web/web-sites-scale.md
[environnements de transfert]: ../app-service-web/web-sites-staged-publishing.md
[options de déploiement]: ../app-service-web/web-sites-deploy.md
[connexions hybride]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[réseaux virtuels]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[Environnements de Service d’application]: ../app-service-web/app-service-app-service-environment-intro.md
[plan de formation]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/
