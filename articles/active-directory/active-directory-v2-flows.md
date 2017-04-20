<properties
    pageTitle="Types de point de terminaison v2.0 | Microsoft Azure"
    description="Les types d’applications et de scénarios pris en charge par le point de terminaison AD Azure v2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="types-of-apps-for-the-v20-endpoint"></a>Types d’applications pour le point de terminaison v2.0
Le point de terminaison v2.0 prend en charge l’authentification pour une variété d’architectures d’application moderne, qui sont basés sur les protocoles standard de l’industrie [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) ou [OpenID de se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Ce document décrit brièvement les types d’applications que vous pouvez créer, indépendant de la plate-forme ou le langage souhaité.  Il vous permettra de comprendre les scénarios de haut niveau avant de vous [plonger dans le code](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Les notions de base
Chaque application qui utilise le point de terminaison v2.0 devra être inscrit à [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Le processus d’inscription de app collecter & affecter quelques valeurs à votre application :

- Un **Id d’Application** qui identifie de manière unique votre application
- **Rediriger un URI** qui peut être utilisé pour diriger les réponses à votre application
- Quelques autres valeurs spécifiques au scénario.  Pour plus de détails, découvrez comment [Enregistrer une application](active-directory-v2-app-registration.md).

Une fois inscrit, l’application communique avec Active Directory Azure en envoyant des requêtes au point de terminaison v2.0 Azure Active Directory.  Nous fournissons les infrastructures open source et les bibliothèques qui prennent en charge les détails de ces demandes, ou vous pouvez implémenter vous-même la logique d’authentification en créant des requêtes à ces points de terminaison :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Applications Web
Pour les applications web (.NET, PHP, Java, Ruby, Python, nœud, etc.) qui sont accessibles via un navigateur, vous pouvez effectuer des utilisateur signe à l’aide de [OpenID de se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  OpenID connecter l’application web reçoit une `id_token`, un jeton de sécurité qui vérifie l’identité de l’utilisateur et fournit des informations sur l’utilisateur dans le formulaire de déclarations :

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Vous pouvez obtenir des informations sur tous les types de jetons et les revendications qui sont disponibles pour une application dans la [référence de jeton de version 2.0](active-directory-v2-tokens.md).

Dans les applications de serveur web, le flux d’authentification de connexion prend ces étapes de haut niveau :

![Image de couloirs d’application Web](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

La validation de l’id_token à l’aide d’une clé de signature publique reçue à partir de la version 2.0 est suffisante pour garantir l’identité de l’utilisateur et la valeur d’un cookie de session qui peut être utilisé pour identifier l’utilisateur sur les demandes de page suivantes.

Pour voir cela en action, essayez un des exemples de code de connexion web app dans notre section de [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) .

Outre la simple sign-in, une application de serveur web deviez également accéder à un autre service web comme une API REST.  Dans ce cas l’application de serveur web peut participer à un flux OpenID se connecter & OAuth 2.0 combiné, utilisant le [flux de Code d’autorisation OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Ce scénario est décrit ci-dessous dans notre [rubrique de WebApp-WebAPI mise en route](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>API Web
Vous pouvez utiliser le point de terminaison v2.0 pour sécuriser des services web, telles que les API Web RESTful de votre application.  Au lieu de cookies de session et d’id_tokens, API de Web utiliser OAuth 2.0 access_tokens à sécuriser leurs données et authentifier les demandes entrantes.  L’appelant d’une API Web ajoute un access_token dans l’en-tête d’autorisation d’une demande HTTP :

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L’API Web pouvez ensuite utiliser l’access_token pour vérifier l’identité de l’appelant de l’API et d’extraire des informations relatives à l’appelant de revendications qui sont codées dans l’access_token.  Vous pouvez obtenir des informations sur tous les types de jetons et les revendications qui sont disponibles pour une application dans la [référence de jeton de version 2.0](active-directory-v2-tokens.md).

Une API Web permettent aux utilisateurs le pouvoir d’opt-in/opt-out de certaines fonctionnalités ou de données en exposant les autorisations, également appelées [étendues](active-directory-v2-scopes.md).  Pour une application appelante à acquérir l’autorisation d’une étendue, l’utilisateur doit consent à la portée au cours d’un flux.  Le point de terminaison v2.0 s’occupera de demander l’autorisation et d’enregistrement de ces autorisations dans tous les access_tokens qui reçoit de l’API Web.  Tout ce que l’API Web doit à vous soucier valide les access_tokens qu’il reçoit sur chaque appel et effectuer les vérifications d’autorisation appropriée.

Une API Web peut recevoir des access_tokens de tous les types d’applications, y compris les applications de serveur web, bureau et les applications mobiles, seule page applications, processus du côté serveur et même d’autres API de Web.  Le flux de haut niveau pour l’authentification des api web est la suivante :

![Image de couloirs d’API Web](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Pour plus d’informations sur les étapes détaillées de mise en route d’access_tokens, refresh_tokens et authorization_codes, en savoir plus sur le [protocole OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Pour savoir comment sécuriser un site web api avec access_tokens de OAuth2, consultez les exemples de code d’api web cité dans la [section mise en route](active-directory-appmodel-v2-overview.md#getting-started).


## <a name="mobile-and-native-apps"></a>Applications mobiles et natives
Les applications qui sont installées sur un périphérique, comme les applications mobiles et de bureau, souvent besoin d’accéder aux services principaux ou les API Web qui stockent les données et exécuter diverses fonctions au nom d’un utilisateur.  Ces applications peuvent ajouter dans l’authentification et l’autorisation aux services back-end en utilisant le [flux de Code d’autorisation OAuth 2.0](active-directory-v2-protocols-oauth-code.md).  

Dans ce type de flux, un l’application reçoit un authorization_code à partir de la version 2.0 lors de la connexion de l’utilisateur, qui représente l’autorisation de l’application d’appeler les services back-end pour le compte de l’utilisateur actuellement connecté.  L’application peut alors échanger les authoriztion_code dans l’arrière-plan d’un access_token OAuth 2.0 et un refresh_token.  L’application permet l’access_token pour d ' authentifier aux API de Web dans les demandes HTTP et permet du refresh_token pour obtenir les nouvelles access_tokens lors de l’expirent des plus anciens.

![Image de couloirs d’application native](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Applications de page unique (javascript)
Plusieurs applications modernes ont un App de Page unique (SPA) frontal principalement écrite dans javascript et souvent à l’aide des infrastructures (AngularJS, Ember.js, Durandal, etc.).  Le point de terminaison AD Azure version 2.0 prend en charge ces applications en utilisant le [Flux implicite de OAuth 2.0](active-directory-v2-protocols-implicit.md).

Dans ce type de flux, l’application reçoit des jetons à partir de la version 2.0 autorisent le point de terminaison directement, sans effectuer de tout échange de serveur à serveur back-end.  Cela permet de placent tous les logique d’authentification et gestion de session pour prendre entièrement dans le client javascript, sans effectuer des redirections de pages supplémentaires.

![Image de couloirs de flux implicite](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Pour voir cela en action, essayez un des exemples de code de l’application de page unique dans notre section de [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) .

### <a name="daemonsserver-side-apps"></a>Applications côté-serveur du processus
Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans la présence d’un utilisateur également besoin d’un moyen d’accéder aux ressources sécurisées, telles que les API Web.  Ces applications peuvent s’authentifier et obtenir des flux d’informations d’identification de jetons en utilisant l’identité de l’application (plutôt que délégué identité d’un utilisateur) en utilisant le client OAuth 2.0.

Dans ce type de flux, l’application obtienne les jetons en interagissant directement avec le `/token` point de terminaison :

![Image de démon App couloirs](../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Pour créer une application de démon, reportez-vous à la documeenation d’informations d’identification client dans notre section de [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) , ou reportez-vous à [Cet exemple d’application .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="current-limitations"></a>Limitations actuelles
Ces types d’applications ne sont pas actuellement pris en charge par le point de terminaison v2.0, mais sur la feuille de route.  Autres limitations et restrictions pour le point de terminaison v2.0 sont décrites dans l' [article de limitations v2.0](active-directory-v2-limitations.md).

### <a name="chained-web-apis-on-behalf-of"></a>Chained web API (de la part de)
De nombreuses architectures incluent une API Web qui doit appeler une autre API Web en aval, à la fois sécurisé par le point de terminaison v2.0.  Ce scénario est courant dans les clients natifs qui disposent d’une API Web principale, qui à son tour appelle un service Microsoft Online comme Office 365 ou l’API de graphique.

Ce scénario d’API Web chaîné peut prendre en charge à l’aide de l’octroi d’informations d’identification de OAuth 2.0 Jwt porteur, également appelé le [Flux de On-Behalf-Of](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow).  Toutefois, le flux de la part de n’est pas actuellement implémenté dans le point de terminaison v2.0.  Pour voir comment ce flux fonctionne dans la publicité Azure disponible de service, consultez l' [exemple de la part de code sur GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).
