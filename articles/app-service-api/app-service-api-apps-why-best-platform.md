<properties 
    pageTitle="Présentation de l’API applications | Microsoft Azure" 
    description="Découvrez comment Azure Application Service vous aide à développer, hôte et d’utiliser des API RESTful." 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/23/2016" 
    ms.author="rachelap"/>

# <a name="api-apps-overview"></a>Vue d’ensemble des applications de l’API

Applications d’API dans le Service d’application Azure offrent des fonctionnalités qui le rendent plus facile à développer, hôte et d’utiliser des API dans le nuage et sur site. Avec des applications d’API, vous obtenez sécurité de niveau entreprise, contrôle d’accès simple, la connectivité de la hybride, la génération automatique de kit de développement logiciel et intégration transparente avec les [Applications de la logique](../app-service-logic/app-service-logic-what-are-logic-apps.md).

[Service d’application Azure](../app-service/app-service-value-prop-what-is.md) est une plateforme entièrement gérée pour le web, mobile et des scénarios d’intégration. Applications d’API est un des quatre types d’application proposées par le [Service d’application Azure](../app-service/app-service-value-prop-what-is.md).

![Types d’application dans le Service d’application Azure](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## <a name="why-use-api-apps"></a>Pourquoi utiliser des applications d’API ?

Voici les principales fonctionnalités des applications de l’API :

- **Mettre votre API existante en tant que-est** -vous n’êtes pas obligé de modifier le code dans votre API existantes pour tirer parti des applications d’API--simplement déployer votre code pour une application API. Votre API peut utiliser n’importe quel langage ou un framework pris en charge par le Service d’application, y compris ASP.NET et C#, Java, PHP, Node.js et Python.

- La **consommation simple** - prise en charge intégrée pour les [métadonnées de la Swagger des API](http://swagger.io/) rend votre API facilement utilisable par une variété de clients.  Générer automatiquement le code client pour votre API dans une variété de langages, notamment C#, Java et Javascript. Configurer facilement les [CORS](app-service-api-cors-consume-javascript.md) sans modifier votre code. Pour plus d’informations, reportez-vous à la section [métadonnées App Service API Apps pour la génération de code et de la découverte des API](app-service-api-metadata.md) et [consommer une application API à partir de JavaScript à l’aide de CORS](app-service-api-cors-consume-javascript.md). 

- **Contrôle d’accès simple** - une application API contre un accès non authentifié sans apporter de modifications à votre code. Service d’authentification intégré les API sécurisées pour l’accès par d’autres services ou par les clients représentant des utilisateurs. Les fournisseurs d’identité pris en charge incluent Azure Active Directory, Facebook, Twitter, Google et Microsoft Account. Clients peuvent utiliser la bibliothèque de l’authentification Active Directory (ADAL) ou le Kit de développement des applications mobiles. Pour plus d’informations, consultez [authentification et autorisation pour les applications d’API dans le Service d’application Azure](app-service-api-authentication.md).

- **Intégration de Visual Studio** - outils dédiés dans Visual Studio rationaliser le travail de création, le déploiement, consommation, débogage et gestion des applications de l’API. Pour plus d’informations, consultez [l’annonce le SDK Azure 2.8.1 pour .NET](/blog/announcing-azure-sdk-2-8-1-for-net/).

- **Intégration avec les applications de logique** - applications d’API que vous créez peuvent être consommées par [Les applications de logique de Service App](../app-service-logic/app-service-logic-what-are-logic-apps.md).  Pour plus d’informations, reportez-vous à la section [utilisation de votre API personnalisé hébergé sur le Service d’application avec des applications de la logique](../app-service-logic/app-service-logic-custom-hosted-api.md) et le [nouveau schéma version 2015-08-01-aperçu](../app-service-logic/app-service-logic-schema-2015-08-01.md).

En outre, une application API peut bénéficier des fonctionnalités offertes par les [Applications Web](../app-service-web/app-service-web-overview.md) et [d’Applications Mobile](../app-service-mobile/app-service-mobile-value-prop.md). L’inverse est également vrai : Si vous utilisez une application web ou une application mobile pour héberger une API, il peut profiter des fonctionnalités des applications d’API comme métadonnées Swagger pour le client CORS et génération de code pour l’accès inter-domaines. La seule différence entre les types de trois d’application (API, web, mobile) est le nom et l’icône utilisée pour eux dans le portail Azure.

## <a name="whats-the-difference-between-api-apps-and-azure-api-management"></a>Quelle est la différence entre les applications d’API et gestion des API Azure ?

Applications d’API et [Gestion des API Azure](../api-management/api-management-key-concepts.md) sont des services complémentaires :

* Gestion de l’API est sur la gestion des API. Vous placez un front-end de gestion de l’API sur une API pour le moniteur et limitation de l’utilisation, manipuler les entrées et les sorties, consolidez plusieurs API dans un point de terminaison et ainsi de suite. Les API gérées peuvent résider n’importe où.
* Applications d’API est sur l’API d’hébergement. Le service inclut des fonctionnalités qui facilitent l’API de développement et long, mais il ne les types de surveillance, la limitation, la manipulation ou consolidation de la gestion de cette API est. Si vous n’avez pas besoin des fonctionnalités de gestion de l’API, vous pouvez héberger API dans les API applications sans utiliser l’API de gestion.

Voici un diagramme qui illustre la gestion d’API pour les API hébergées dans les applications d’API et ailleurs.

![Gestion de l’API Azure et les applications de l’API](./media/app-service-api-apps-why-best-platform/apia-apim.png)

Certaines fonctionnalités de l’API de gestion et les applications de l’API ont des fonctions similaires.  Par exemple, les deux peuvent automatiser la prise en charge CORS. Lorsque vous utilisez les deux services conjointement, vous utiliseriez l’API de gestion pour les CORS dans la mesure où elle fonctionne comme la partie frontale à vos applications d’API. 

## <a name="getting-started"></a>Mise en route

Pour commencer avec des applications de l’API en déployant des exemples de code dans l’un, consultez le didacticiel pour n’importe quel cadre vous préférez :

* [ASP.NET](app-service-api-dotnet-get-started.md) 
* [Node.js](app-service-api-nodejs-api-app.md) 
* [Java](app-service-api-java-api-app.md) 

Pour poser des questions à propos des applications d’API, démarrer un thread sur le [forum des applications d’API](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps). 
