<properties
    pageTitle="Authentification et autorisation pour les applications d’API dans le Service d’application Azure | Microsoft Azure"
    description="Obtenir des informations sur les services d’authentification et d’autorisation qui fournit des services d’application Azure pour les applications d’API."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="rachelap"/>

# <a name="authentication-and-authorization-for-api-apps-in-azure-app-service"></a>Authentification et autorisation pour les applications d’API dans le Service d’application Azure

## <a name="overview"></a>Vue d’ensemble 

> [AZURE.NOTE] Cette rubrique sera migrée vers une liste consolidée [application Service authentification / autorisation](../app-service/app-service-authentication-overview.md) rubrique, qui couvre les applications d’API, Web et Mobile.

Service d’application Azure offre des services d’authentification et d’autorisation intégrés qui implémentent [OAuth 2.0](#oauth) et [OpenID se connecter](#oauth). Cet article décrit les services et les options qui sont disponibles pour les applications d’API dans le Service d’application Azure.

Le diagramme suivant illustre certaines caractéristiques clés d’authentification de Service de l’application :

* Il prétraite les demandes entrantes API, qui signifie qu’il fonctionne avec n’importe quel langage ou d’un framework pris en charge par le Service de l’application.
* Il vous offre plusieurs options pour combien l’authentification fonctionne que vous souhaitent faire dans votre propre code.
* Il fonctionne pour l’authentification de compte de service et aux utilisateurs finaux. 
* Il prend en charge les cinq fournisseurs d’identité : Azure Active Directory, Facebook, Google, Twitter et Account de Microsoft.
* Il fonctionne de la même pour les applications d’API, applications Web et applications Mobile.

![](./media/app-service-api-authentication/api-apps-overview.png)

## <a name="language-agnostic"></a>Indépendant de la langue

Traitement de l’authentification de Service d’application se produit avant que les demandes n’atteignent votre application API, ce qui signifie que les fonctionnalités d’authentification fonctionnent pour les applications d’API écrites dans une langue ou un cadre.  Votre API peut être basé sur ASP.NET, Java, Node.js ou n’importe quelle infrastructure qui prend en charge le Service de l’application.

Application Service passe le jeton à web JSON (JWT) dans l’en-tête d’autorisation d’une demande HTTP, et le code écrit dans une langue ou d’un framework peut obtenir les informations dont il a besoin à partir du jeton. En outre, Service d’application vous donne accès plus facile des sinistres les plus couramment utilisés en définissant certains en-têtes spéciales, telles que les suivantes :

* X-MS-PRINCIPAL-NOM DU CLIENT
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
Dans une API .NET, vous pouvez utiliser la `Authorize` d’attribut et pour une autorisation, vous pouvez écrire facilement du code sur la base des revendications dans la mesure où les informations sont renseignées dans les classes .NET.

## <a name="multiple-protection-options"></a>Plusieurs options de protection

Service d’application peut empêcher les requêtes HTTP anonymes d’atteindre votre application API, il peut passer toutes les demandes et valider des jetons pour les requêtes qui incluent les, ou il peut laisser passer toutes les demandes sans effectuer aucune action sur les :

1. Autoriser uniquement les demandes authentifiées accéder à votre application d’API.

    Si une demande anonyme est reçue à partir d’un navigateur, Service de l’application redirige vers une page de connexion pour le fournisseur d’authentification (Azure AD, Google, Twitter, etc.) que vous choisissez. 

    Avec cette option, vous n’avez pas besoin d’écrire du code d’authentification du tout dans votre application, et code d’autorisation est simplifiée car les déclarations plus importantes sont fournies dans les en-têtes HTTP.

2. Autoriser toutes les demandes pour accéder à votre application d’API, mais de valider les demandes authentifiées et transmettre les informations d’authentification dans les en-têtes HTTP.

    Cette option offre plus de souplesse dans le traitement des demandes anonymes, mais vous devez écrire du code si vous souhaitez empêcher les utilisateurs anonymes à partir de l’utilisation de votre API. Dans la mesure où les demandes les plus populaires sont passées dans les en-têtes de demandes HTTP, le code d’autorisation est relativement simple.
    
3. Autoriser toutes les demandes atteindre votre API, effectuer aucune action sur les informations d’authentification dans les requêtes.

    Cette option laisse les tâches d’authentification et d’autorisation dépend entièrement de votre code d’application.

Dans le [portail Azure](https://portal.azure.com/), vous sélectionnez l’option de votre choix sur le **l’authentification / autorisation** blade.

![](./media/app-service-api-authentication/authblade.png)

Activer **L’authentification de Service App**pour les options 1 et 2 et dans la liste déroulante **Action à entreprendre lors de la demande n’est pas authentifié** , choisissez **se connecter** ou **Autoriser les requêtes (aucune action)**.  Si vous choisissez de **vous connecter**, vous devez choisir un fournisseur d’authentification et configurer ce fournisseur.

![](./media/app-service-api-authentication/actiontotake.png)

Pour obtenir des informations détaillées sur la façon de configurer l’authentification, consultez [comment configurer votre application de Service de l’application pour utiliser des connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). L’article s’applique aux applications d’API ainsi que les applications mobiles, et des liens vers d’autres articles pour les autres fournisseurs d’authentification.
 
## <a id="internal"></a>Authentification de compte de service

L’authentification du Service d’application fonctionne pour les scénarios internes tel que pour l’appel à partir d’une application API à une autre application API. Dans ce scénario, vous obtenez un jeton à l’aide des informations d’identification pour un compte de service au lieu des informations d’identification de l’utilisateur final. Un compte de service est également appelé un *service principal* dans Azure Active Directory et l’authentification à l’aide de ce compte est également connu comme un scénario de service à service. 

Pour les scénarios de service-service, protéger l’application API appelée à l’aide d’Azure Active Directory et de fournir un jeton d’autorisation principal service DAS lorsque vous appelez l’application API. Vous obtenez un jeton en fournissant au client ID et client secrète à partir de l’application DAS. Aucun code de Azure seule spécial est requis, telles que True pour gérer le jeton Zumo de Services mobiles. Un exemple de ce scénario à l’aide des API d’ASP.NET apps est couvertes par le didacticiel [authentification principale du Service pour les applications d’API](app-service-api-dotnet-service-principal-auth.md).

Si vous souhaitez gérer un scénario de service-service sans l’aide de l’authentification du Service de l’application, vous pouvez utiliser l’authentification de base ou de certificats clients. Pour plus d’informations sur les certificats de client dans Azure, consultez [La procédure pour configurer TLS mutuelle l’authentification pour les applications Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Pour plus d’informations sur l’authentification de base dans ASP.NET, reportez-vous à la section [Filtres de l’authentification dans ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Authentification des comptes de service à partir d’une application de logique de Service d’application pour une application API est un cas particulier qui est expliqué dans [utilisation de votre API personnalisé hébergé sur le Service d’application avec des applications de logique](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="mobile-client-authentication"></a>Authentification du client mobile

Pour plus d’informations sur la façon de gérer l’authentification des clients mobiles, consultez la [documentation sur l’authentification pour les applications mobiles](../app-service-mobile/app-service-mobile-ios-get-started-users.md). L’authentification du Service d’application fonctionne de la même manière pour les applications mobiles et des applications d’API.
  
## <a name="more-information"></a>Plus d’informations

Pour plus d’informations sur l’authentification et autorisation dans les services d’application Azure, consultez les ressources suivantes :

* [Authentification de Service de l’application en pleine expansion / autorisation](/blog/announcing-app-service-authentication-authorization/)
* [Comment faire pour configurer votre application de Service de l’application pour utiliser des connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (Inclut des liens pour d’autres fournisseurs d’authentification, en haut de la page.) 

Pour plus d’informations sur OAuth 2.0, OpenID se connecter et JSON Web jetons (JWT), consultez les ressources suivantes.

* [Mise en route avec OAuth 2.0] (http://shop.oreilly.com/product/0636920021810.do "Mise en route avec OAuth 2.0") 
* [Introduction à OAuth2, OpenID de se connecter et JSON Web jetons (JWT) - les cours PluralSight](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Création et sécurisation d’une API RESTful pour plusieurs Clients dans ASP.NET - les cours PluralSight](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Pour plus d’informations sur Azure Active Directory, consultez les ressources suivantes.

* [Scénarios AD Azure](http://aka.ms/aadscenarios)
* [Guide d’Azure annonces destinées aux développeurs](http://aka.ms/aaddev)
* [Exemples d’annonces Azure](http://aka.ms/aadsamples)

## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit les fonctionnalités d’authentification et d’autorisation du Service d’application que vous pouvez utiliser pour les applications d’API. Le didacticiel suivant dans la série démarrée lors de l’obtention montre comment implémenter [l’authentification de l’utilisateur dans l’API de Service App Apps](app-service-api-dotnet-user-principal-auth.md).
