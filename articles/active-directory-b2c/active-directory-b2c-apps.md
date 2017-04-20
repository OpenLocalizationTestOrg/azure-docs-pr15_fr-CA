<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="Les types d’applications que vous pouvez créer dans l’Active Directory B2C du Azure."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure B2C de répertoire actif : Les Types d’applications

Azure B2C d’Active Directory (AD Azure) prend en charge l’authentification pour une variété d’architectures d’applications modernes. Toutes les sont basés sur les protocoles standard de l’industrie [OAuth 2.0](active-directory-b2c-reference-protocols.md) ou [OpenID de se connecter](active-directory-b2c-reference-protocols.md). Ce document décrit brièvement les types d’applications que vous pouvez générer, indépendant de la plate-forme ou le langage souhaité. Il vous permet également de comprendre les scénarios avant de [commencer à créer des applications](active-directory-b2c-overview.md#getting-started)de haut niveau.

## <a name="the-basics"></a>Les notions de base
Chaque application qui utilise Azure AD B2C doit être enregistrée dans votre [répertoire de B2C](active-directory-b2c-get-started.md) via le [Portail Azure](https://portal.azure.com/). Le processus d’inscription application collecte et assigne de quelques valeurs à votre application :

- Un **ID d’Application** qui identifie de manière unique votre application.
- **Rediriger un URI** qui peut être utilisé pour diriger les réponses à votre application.
- Toutes les autres valeurs spécifiques au scénario. Pour plus d’informations, découvrez comment [Enregistrer une application](active-directory-b2c-app-registration.md).

Une fois que l’application est enregistrée, elle communique avec Azure AD en envoyant des requêtes au point de terminaison AD Azure v2.0 :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Chaque demande est envoyée à Azure AD B2C spécifie une **stratégie**. Une stratégie de contrôle le comportement de publicité Azure. Vous pouvez également utiliser ces points de terminaison pour créer un ensemble très personnalisable d’expériences utilisateur. Politiques communes comprennent l’inscription, inscription et profil-modifier les stratégies. Si vous n’êtes pas familiarisé avec les stratégies, vous devez lire sur Azure AD B2C [cadre politique extensible](active-directory-b2c-reference-policies.md) avant de continuer.

L’interaction de chaque application avec un point de terminaison v2.0 suit un modèle similaire de haut niveau :

1. L’application dirige l’utilisateur vers le point de terminaison v2.0 pour exécuter une [stratégie](active-directory-b2c-reference-policies.md).
2. L’utilisateur termine la stratégie en fonction de la définition de la stratégie.
4. L’application reçoit un type de jeton de sécurité à partir de la version 2.0.
5. L’application utilise le jeton de sécurité pour accéder aux informations protégées ou à une ressource protégée.
6. Le serveur de ressource valide le jeton de sécurité pour vérifier que l’accès peut être accordé.
7. L’application actualise régulièrement le jeton de sécurité.

<!-- TODO: Need a page for libraries to link to -->
Ces étapes peuvent différer légèrement en fonction du type d’application que vous créez. Les bibliothèques Open source peuvent traiter les détails pour vous.

## <a name="web-apps"></a>Applications Web
Pour les applications web (y compris .NET, PHP, Java, Ruby, Python et Node.js) qui sont hébergées sur un serveur et accessible via un navigateur, Azure AD B2C prend en charge [OpenID connecter](active-directory-b2c-reference-protocols.md) toutes les expériences utilisateur. Cela inclut la connexion, inscription et gestion des profils. Dans l’implémentation Azure AD B2C de OpenID Connect, votre application web lance ces expériences utilisateur en émettant des demandes d’authentification à Active Directory Azure. Le résultat de la requête est un `id_token`. Ce jeton de sécurité représente l’identité de l’utilisateur. Il fournit également des informations sur l’utilisateur dans le formulaire de déclarations :

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

Pour en savoir plus sur les types de jetons et les revendications qui sont disponibles pour une application dans la [référence de jeton de B2C](active-directory-b2c-reference-tokens.md).

Dans une application web, chaque exécution d’une [stratégie](active-directory-b2c-reference-policies.md) prend ces étapes principales :

![Image de couloirs d’application Web](./media/active-directory-b2c-apps/webapp.png)

Validation de le `id_token` à l’aide d’une clé de signature publique qui est reçue d’Azure AD est suffisante pour vérifier l’identité de l’utilisateur. Il définit également un cookie de session qui peut être utilisé pour identifier l’utilisateur sur les demandes de pages ultérieures.

Pour voir cela en action, essayez un des exemples de code de connexion web app dans notre [que section de mise en route](active-directory-b2c-overview.md#getting-started).

Au delà de l’authentification simple, une application de serveur web peut également doivent accéder à un service web de back-end. Dans ce cas, l’application web peut effectuer un légèrement différents [flux de OpenID se connecter](active-directory-b2c-reference-oidc.md) et acquérir des jetons à l’aide des codes d’autorisation et actualiser les jetons. Ce scénario est représenté dans la [section des API Web](#web-apis)de suivant.

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>API Web
Vous pouvez utiliser Azure AD B2C pour sécuriser des services web tels que des API de web RESTful de votre application. Web API permet de sécuriser leurs données, en les requêtes HTTP entrantes à l’aide des jetons d’authentification OAuth 2.0. L’appelant de l’API web ajoute un jeton dans l’en-tête d’autorisation d’une demande HTTP :

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L’API web peut ensuite utiliser le jeton pour vérifier l’identité de l’appelant de l’API et pour extraire des informations relatives à l’appelant de revendications qui sont codées dans le jeton. Pour en savoir plus sur les types de jetons et revendications disponibles pour une application dans la [référence de jeton d’Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
    Azure AD B2C prend actuellement en charge uniquement le web API qui est accessibles par leurs propres clients connus. Par exemple, votre application complète peut inclure une application iOS, une application d’Android et une API de web back-end. Cette architecture est entièrement pris en charge. Permettre à un client de partenaire, tel qu’une autre application iOS accéder au web même QU'API n’est pas pris en charge. Tous les composants de votre application complète doivent partager un ID unique.

Une API web peut recevoir des jetons à de nombreux types de clients, y compris les applications web, bureau et les applications mobiles, seule page applications, côté serveur, processus et autre API de web. Voici un exemple du déroulement complet d’une application web qui appelle une API web :

![Image de couloirs d’application Web API Web](./media/active-directory-b2c-apps/webapi.png)

Pour plus d’informations sur les codes d’autorisation, actualiser les jetons et les étapes de la mise en route de jetons, en savoir plus sur le [protocole OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Pour savoir comment sécuriser un site web API à l’aide d’Azure AD B2C, consultez le site web didacticiels API dans notre [section de mise en route](active-directory-b2c-overview.md#getting-started).

## <a name="mobile-and-native-apps"></a>Applications mobiles et natives
Les applications qui sont installées sur les périphériques, tels que les applications mobiles et de bureau, souvent besoin d’accéder aux services back-end ou web API au nom des utilisateurs. Vous pouvez ajouter des expériences de gestion d’identité personnalisée à vos applications natives et en toute sécurité les services principaux d’appel à l’aide d’Azure AD B2C et le [flux de code d’autorisation OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

Dans ce type de flux, l’application exécute des [stratégies](active-directory-b2c-reference-policies.md) et reçoit un `authorization_code` d’Azure annonce la fin de la stratégie. Le `authorization_code` représente l’autorisation de l’application à appeler les services back-end pour le compte de l’utilisateur qui est actuellement connecté. L’application peut ensuite échanger les `authorization_code` en arrière-plan pour une `id_token` et un `refresh_token`.  L’application peut utiliser les `id_token` pour s’authentifier sur un site web principal API dans les demandes HTTP. Il peut également utiliser le `refresh_token` pour obtenir un nouveau `id_token` lorsqu’un ancien arrive à expiration.

> [AZURE.NOTE]
    Azure AD B2C prend actuellement en charge uniquement les jetons qui sont utilisés pour accéder à un service web de back-end de l’application. Par exemple, votre application complète peut inclure une application iOS, une application d’Android et une API de web back-end. Cette architecture est entièrement pris en charge. Permettant à votre application d’e/s accéder à un site web partenaire API à l’aide des jetons d’accès OAuth 2.0 n’est pas actuellement pris en charge. Tous les composants de votre application complète doivent partager un ID unique.

![Image de couloirs d’application native](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Limitations actuelles
Azure B2C d’Active Directory ne prend pas en charge les types d’applications suivants, mais ils sont sur le roadmapy. Autres limitations et restrictions liées à Azure AD B2C sont décrites dans les [Limitations et restrictions](active-directory-b2c-limitations.md).

### <a name="single-page-apps-javascript"></a>Applications de page unique (JavaScript)
Plusieurs applications modernes ont une page application frontale écrite principalement en JavaScript. Ils utilisent souvent un framework comme AngularJS, Ember.js ou Durandal. Généralement disponibles service AD Azure prend en charge ces applications à l’aide du flux implicite OAuth 2.0. Toutefois, ce flux n’est pas encore disponible dans Azure AD B2C.

### <a name="daemonsserver-side-apps"></a>Applications côté serveur/processus
Les applications qui contiennent des processus longs ou qui fonctionnent sans la présence d’un utilisateur également besoin d’un moyen d’accéder aux ressources sécurisées telles que web API. Ces applications peuvent s’authentifier et obtenir des jetons en utilisant l’identité de l’application (plutôt que l’identité d’un utilisateur déléguée) et en utilisant le client OAuth 2.0 flux d’informations d’identification.

Ce flux n’est pas pris en charge par Azure AD B2C. Ces applications peuvent obtenir des jetons qu’après qu’un flux d’utilisateur interactif s’est produite.

### <a name="web-api-chains-on-behalf-of-flow"></a>API Web chaînes (de la part de flux)
De nombreuses architectures incluent un site web API qui doit appeler un autre site web en aval API, où les deux sont sécurisés par Azure AD B2C. Ce scénario est courant dans les clients natifs qui ont un API Web back-end. Il appelle ensuite un service en ligne de Microsoft comme l’API Azure AD graphique.

Ce scénario web chaînée API peut être pris en charge à l’aide de l’octroi d’informations d’identification au porteur OAuth 2.0 JWT, également connu sous le nom du flux de la part de.  Toutefois, le flux de la part de n’est pas actuellement implémenté dans le AD B2C Azure.
