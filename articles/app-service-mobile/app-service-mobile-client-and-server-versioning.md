<properties
  pageTitle="Version de kit de développement logiciel client et serveur dans les applications mobiles et de Services mobiles | Service d’application Azure"
  description="Liste des kits de développement logiciel de client et compatibilité avec les versions serveur Kit de développement logiciel pour les Services mobiles et des applications de Mobile Azure"
  services="app-service\mobile"
  documentationCenter=""
  authors="adrianhall"
  manager="erikre"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="10/01/2016"
  ms.author="adrianha"/>

# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Versions client et serveur dans les applications mobiles et de Services mobiles

La version la plus récente de Services mobiles de Azure est la fonctionnalité **d’Applications Mobile** de Service d’application Azure.

Les kits de développement client et le serveur par les applications Mobile sont à l’origine après ceux de Services mobiles, mais ils sont *pas* compatibles entre eux.
Autrement dit, vous devez utiliser un client *Applications Mobile* SDK avec un serveur *d’Applications Mobile* SDK et de même pour *Les Services mobiles*. Ce contrat est appliqué au moyen d’une valeur d’en-tête spécial utilisée par le client et le serveur SDK, `ZUMO-API-VERSION`.

Remarque : chaque fois que ce document fait référence à un serveur principal de *Services mobiles* , il ne pas nécessaire être hébergées sur les Services mobiles. Il est désormais possible de migrer un service mobile s’exécute sur le Service de l’application sans aucune modification de code, mais le service serait continue à utiliser les versions du Kit de développement logiciel *Services mobiles* .

Pour plus d’informations sur la migration vers le Service de l’application sans aucune modification de code, consultez l’article [migrer un Service Mobile au Service d’application Azure].

## <a name="header-specification"></a>Spécification de l’en-tête

La touche `ZUMO-API-VERSION` peut être spécifié dans la chaîne de requête ou de l’en-tête HTTP. La valeur est une chaîne de version dans le formulaire **x.y.z**.

Par exemple :

OBTENIR https://service.azurewebsites.net/tables/TodoItem

EN-TÊTES : ZUMO-API-VERSION : 2.0.0

PUBLICATION https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Choix de la vérification de la version

Vous pouvez la désactiver en affectant la valeur **true** pour l’application de configuration de **MS_SkipVersionCheck**la vérification de version. Spécifier dans votre fichier web.config ou dans la section Paramètres de l’Application du portail Azure.

> [AZURE.NOTE] Il existe un certain nombre de modifications de comportement entre les Services mobiles et des applications mobiles, en particulier dans les domaines de la synchronisation en mode hors connexion, l’authentification et les notifications de transmission. Vous devez uniquement désinscrire version vérification après tous les tests pour vous assurer que ces modifications comportementales n’arrêtent pas les fonctionnalités de votre application.

## <a name="summary-of-compatibility-for-all-versions"></a>Synthèse de compatibilité pour toutes les versions

Le tableau ci-dessous indique la compatibilité entre tous les types de client et le serveur. Un serveur principal est classé soit Mobile **Services** ou les **applications** mobiles basées sur le serveur du Kit de développement logiciel qu’il utilise.

|                           | **Services mobiles** Node.js ou .NET | **Applications mobiles** Node.js ou .NET |
| ----------                | -----------------------             |   ----------------              |
| [Clients de Services mobiles] | Bien                                  | Erreur\*                         |
| [Clients d’applications Mobile]     | Erreur\*                             | Bien                              |

\*Cela peut être contrôlé en spécifiant **MS_SkipVersionCheck**.


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Serveur et client de Services mobiles

Le kits de développement logiciel client dans le tableau ci-dessous sont compatibles avec les **Services mobiles**.

Remarque : le client Mobile Services SDK *pas* envoyer une valeur d’en-tête pour `ZUMO-API-VERSION`. Si le service reçoit cet en-tête ou une valeur de chaîne de requête, une erreur est renvoyée, sauf si vous avez choisi explicitement comme décrit ci-dessus.

### <a name="MobileServicesClients"></a>Client de *Services* Mobile SDK

| Plate-forme de client                   | Version                                                                   | Valeur d’en-tête de version |
| -------------------               | ------------------------                                                  | -------------------  |
| Client managé (Windows, Xamarin) | [1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | n/a                  |
| e/s                               | [2.2.2](http://aka.ms/gc6fex)                                             | n/a                  |
| Android                           | [2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126)                   | n/a                  |
| HTML                              | [1.2.7).](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | n/a     |

### <a name="mobile-services-server-sdks"></a>Serveur de *Services* Mobile SDK

| Plate-forme de serveur  | Version                                                                                                        | En-tête de version acceptés |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **Aucun en-tête de version** |
| Node.js          | (disponible prochainement)                        | **Aucun en-tête de version** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Comportement des serveurs principaux de Services mobiles

| VERSION DE L’API ZUMO | Valeur de MS_SkipVersionCheck | Réponse |
| ---------------- | ---------------------------- | -------- |
| Non spécifié    | Tout                          | 200 - OK |
| N’importe quelle valeur        | True                         | 200 - OK |
| N’importe quelle valeur        | Faux, non spécifié          | 400 - Requête incorrecte |

## <a name="2.0.0"></a>Azure client d’applications Mobile et le serveur

### <a name="MobileAppsClients"></a>Client *applications* Mobile SDK

Vérification de la version a été introduite le démarrage avec les versions suivantes du Kit de développement logiciel client pour **Les applications mobiles Azure**:

| Plate-forme de client                   | Version                   | Valeur d’en-tête de version |
| -------------------               | ------------------------  | -----------------    |
| Client managé (Windows, Xamarin) | [2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2.0.0 |
| e/s                               | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2.0.0  |
| Android                           | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Serveur *d’applications* Mobile SDK

Vérification de la version est incluse dans les versions du Kit de développement logiciel serveur suivantes :

| Plate-forme de serveur  | KIT DE DÉVELOPPEMENT LOGICIEL                                                                                                        | En-tête de version acceptés |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2.0.0 |
| Node.js          | [Azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps)                         | 2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportement des serveurs principaux d’applications Mobile

| VERSION DE L’API ZUMO | Valeur de MS_SkipVersionCheck | Réponse |
| ---------------- | ---------------------------- | -------- |
| x.y.z ou Null    | True                         | 200 - OK |
| Valeur null             | Faux, non spécifié          | 400 - Requête incorrecte |
| 1.x.y            | Faux, non spécifié          | 400 - Requête incorrecte |
| 2.0.0-2.x.y      | Faux, non spécifié          | 200 - OK |
| 3.0.0-3.x.y      | Faux, non spécifié          | 400 - Requête incorrecte |


## <a name="next-steps"></a>Étapes suivantes

- [Migrer un Service Mobile au Service d’application Azure]


[Clients de Services mobiles]: #MobileServicesClients
[Clients d’applications Mobile]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrer un Service Mobile au Service d’application Azure]: app-service-mobile-migrating-from-mobile-services.md

