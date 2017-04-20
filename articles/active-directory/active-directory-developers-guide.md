<properties
   pageTitle="Guide du programmeur Active Directory Azure | Microsoft Azure"
   description="Cet article fournit un guide complet sur les ressources destinées aux développeurs pour Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/24/2016"
   ms.author="mbaldwin"/>


# <a name="azure-active-directory-developers-guide"></a>Guide du programmeur Active Directory Azure

## <a name="overview"></a>Vue d’ensemble
Sous la forme d’une gestion des identités comme une plate-forme de services (IDMaaS), Azure Active Directory (AD) offre aux développeurs un moyen efficace pour intégrer la gestion des identités dans leurs applications. Les articles suivants fournissent une vue d’ensemble sur l’implémentation et les fonctionnalités clés de l’annonce Azure. Nous vous conseillons les lire dans l’ordre, ou passer à la [mise en route](#getting-started) , si vous êtes prêt à approfondir.


1. [Les avantages de l’intégration de publicités Azure](./develop/active-directory-how-to-integrate.md): Découvrez pourquoi l’intégration avec Active Directory Azure offre la meilleure solution de connexion sécurisée et d’autorisation.

1. [Scénarios d’authentification AD Azure](active-directory-authentication-scenarios.md): tirer parti de l’authentification simplifiée dans Azure AD pour fournir à l’ouverture de session pour votre application.

1. [Intégration des applications avec AD Azure](active-directory-integrating-applications.md): Apprenez à ajouter, mettre à jour et supprimer des applications à partir d’AD Azure et sur les règles de personnalisation d’applications intégrées.

1. [API d’Azure AD graphique](active-directory-graph-api.md): l’API Azure AD graphique permet d’accéder par programme à AD Azure via les points de terminaison API REST. L’API Azure AD graphique est également accessible par le biais de [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph fournit une API unifiée qui permet l’accès à Microsoft cloud service API, via un seul point de terminaison API REST et avec un jeton d’accès unique.

1. [Bibliothèques d’authentification AD Azure](active-directory-authentication-libraries.md): faciliter l’authentification des utilisateurs afin d’obtenir les jetons d’accès à l’aide de bibliothèques d’authentification AD Azure pour .NET, JavaScript, Objective-C, Android et bien plus encore.


## <a name="getting-started"></a>Mise en route

Ces didacticiels sont conçues pour plusieurs plates-formes et peuvent vous aider à rapidement commencer à développer avec Azure Active Directory. Comme condition préalable, vous devez [obtenir un locataire Azure Active Directory](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guides de démarrage rapide d’application mobile et le PC

|[![e/s](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Universel de Windows](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[e/s](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Universel de Windows](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[S’intègre directement avec OAuth 2.0](active-directory-protocols-oauth-code.md)|

### <a name="web-application-quick-start-guides"></a>Guides de démarrage rapide d’application Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID connexion](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)|[S’intègre directement avec OpenID se connecter](active-directory-protocols-openid-connect-code.md)|

### <a name="web-api-quick-start-guides"></a>Guides de démarrage rapide d’API Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### <a name="querying-the-directory-quickstart-guide"></a>Interrogation du guide de démarrage rapide du répertoire

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API de graphique](active-directory-graph-api-quickstart.md)|

## <a name="how-tos"></a>Articles Comment faire

Ces articles décrivent comment effectuer des tâches spécifiques à l’aide d’Azure Active Directory :

- [Obtenir un locataire Azure AD](active-directory-howto-tenant.md)
- [Connectez-vous à n’importe quel utilisateur AD Azure en utilisant le modèle d’application de plusieurs utilisateurs](active-directory-devhowto-multi-tenant-overview.md)
- Activer SSO entre-app à l’aide de ADAL, [Android](active-directory-sso-android.md) et sur les périphériques [d’e/s](active-directory-sso-ios.md)
- [Rendre votre application AppSource certifié pour Azure AD](active-directory-devhowto-appsource-certified.md)
- [Enregistrez votre application dans la galerie des applications Azure AD](active-directory-app-gallery-listing.md)
- [Soumettre des applications web pour Office 365 pour le tableau de bord](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Comprendre le manifeste d’application Azure Active Directory](active-directory-application-manifest.md)
- [Comprendre les règles de personnalisation pour les boutons d’acquisition d’ouverture de session et d’application dans votre application cliente](active-directory-branding-guidelines.md)
- [Aperçu : Comment créer des applications qui signent les utilisateurs avec des comptes à la fois personnelles & travail ou de l’école](active-directory-appmodel-v2-overview.md)
- [Aperçu : Comment créer des applications qui vous inscrire et vous connecter aux consommateurs](../active-directory-b2c/active-directory-b2c-overview.md)
- [Aperçu : configuration de durée de vie des jetons dans Azure AD](active-directory-configurable-token-lifetimes.md) à l’aide de PowerShell. Consultez [les opérations de stratégie](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) et de l' [entité de stratégie](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) pour plus d’informations sur la configuration via l’API Azure AD graphique.

## <a name="reference"></a>Référence

Ces articles fournissent une référence de base pour reste et authentification bibliothèque API, protocoles, erreurs, exemples de code et points de terminaison.  

###  <a name="support"></a>Prise en charge
- [Les questions balisée](http://stackoverflow.com/questions/tagged/azure-active-directory): solutions de trouver Azure Active Directory de débordement de pile en recherchant les balises [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) et [adal](http://stackoverflow.com/questions/tagged/adal).
- Consultez le [Glossaire de développeur AD Azure](active-directory-dev-glossary.md) pour les définitions de certains des termes fréquemment utilisés relatifs à l’intégration et de développement d’applications.

### <a name="code"></a>Code

- [Les bibliothèques open source Azure Active Directory](http://github.com/AzureAD): le moyen le plus simple pour trouver la source d’une bibliothèque est à l’aide de notre [liste de la bibliothèque](active-directory-authentication-libraries.md).

- [Exemples d’Azure Active Directory](https://github.com/azure-samples?query=active-directory): le moyen le plus simple pour naviguer dans la liste des exemples est en utilisant l' [index d’exemples de code](active-directory-code-samples.md).

- [Active Directory d’authentification Library (ADAL) pour .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - documentation de référence n’est disponible pour [la dernière version majeure](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) et la [version principale précédente](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>API de graphique

- [Référence des API de graphe](https://msdn.microsoft.com/library/azure/hh974476.aspx): référence du reste de l’API de graphique Azure Active Directory. [Afficher l’expérience de référence API de graphique interactif](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Étendues d’autorisation graphique API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): étendues d’autorisation OAuth 2.0 qui sont utilisées pour contrôler l’accès aux données d’annuaire dans un client une application.

### <a name="authentication-and-authorization-protocols"></a>Protocoles d’authentification et d’autorisation

- [La signature de clé survol dans Azure annonce](active-directory-signing-key-rollover.md): en savoir plus sur l’annonce Azure signature cadence de substitution de la clé et de la mise à jour de la clé pour les scénarios d’application les plus courants.

- [Protocole OAuth 2.0 : à l’aide du code d’octroi de le](active-directory-protocols-oauth-code.md): vous pouvez utiliser code d’octroi du protocole OAuth 2.0, pour autoriser l’accès aux applications Web et client de l’API Web dans Azure Active Directory.

- [Protocole OAuth 2.0 : comprendre l’octroi implicite](active-directory-dev-understanding-oauth2-implicit-grant.md): en savoir plus sur l’octroi de l’autorisation implicite, et s’il est adapté à votre application.

- [Protocole OAuth 2.0 : Service Service appels à l’aide d’informations d’identification Client](active-directory-protocols-oauth-service-to-service.md): les informations d’authentification OAuth 2.0 Client grant permet à un service web (un client confidentiel) à utiliser ses propres informations d’identification pour l’authentification lors de l’appel d’un autre service web, au lieu de l’emprunt d’identité d’un utilisateur. Dans ce scénario, le client est généralement un service web de couche intermédiaire, un service démon ou site Web.

- [Protocole 1.0 de connexion OpenID : ouverture de session et l’authentification](active-directory-protocols-openid-connect-code.md): 1.0 de connexion OpenID du protocole étend OAuth 2.0 pour une utilisation en tant que protocole d’authentification. Une application cliente peut recevoir une id_token pour gérer le processus de connexion, ou augmenter le flux de code d’autorisation pour recevoir le code d’un id_token et d’autorisation.

- [Référence au protocole SAML 2.0](active-directory-saml-protocol-reference.md): protocole le SAML 2.0 permet aux applications de fournir une expérience d’ouverture de session unique pour leurs utilisateurs.

- [Protocole de WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory prend en charge WS-Federation des 1.2 conformément à la spécification 1.2 Version de fédération de Services Web. Pour plus d’informations sur le document de métadonnées de fédération, consultez [Métadonnées de fédération](active-directory-federation-metadata.md).

- [Prise en charge des types de jeton et de la demande](active-directory-token-and-claims.md): vous pouvez utiliser ce guide pour comprendre et évaluer les déclarations dans les jetons SAML 2.0 et JSON Web jetons (JWT).

## <a name="videos"></a>Vidéos

### <a name="build"></a>Build

Ces présentations de la vue d’ensemble sur le développement d’applications à l’aide des haut-parleurs de fonctionnalité Azure Active Directory qui travaillent directement dans l’équipe d’ingénierie. Les présentations couvrent des sujets fondamentaux, y compris les IDMaaS, l’authentification, la fédération d’identité et de l’authentification unique.

- [Microsoft Identity : État de l’Union et de la Direction Future](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory : Gestion des identités en tant que service pour les applications modernes](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Développer des applications web modernes avec Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Développer des applications natives modernes avec Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure vendredi
[Azure vendredi](https://azure.microsoft.com/documentation/videos/azure-friday/) est une série de vidéos de 1:1 qui s’engage à vous court (10 à 15 minutes) de vendredi entretiens avec des experts sur différents sujets Azure périodique.  Utilisez la fonctionnalité de filtrage des Services sur la page pour voir toutes les vidéos d’Azure Active Directory.

- [Identité Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identité Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identité Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Sociale

- [Blog de l’équipe Active Directory](http://blogs.technet.com/b/ad/): les derniers développements dans le monde d’Azure Active Directory.

- [Blog de l’équipe de graphique Azure Active Directory](http://blogs.msdn.com/b/aadgraphteam): informations Azure Active Directory qui sont spécifiques à l’API de graphique.

- [Identité du nuage](http://www.cloudidentity.net): réflexions sur la gestion des identités en tant que service, à partir d’un principal Azure Active Directory PM.  

- [Azure Active Directory sur Twitter](https://twitter.com/azuread): annonces Azure Active Directory dans 140 caractères ou moins.

## <a name="windows-server-on-premises-development"></a>Développement sur place de Windows Server
Pour obtenir des conseils sur l’utilisation de développement Windows Server et Active Directory Federation Services (ADFS), voir :

- [AD FS des scénarios pour les développeurs](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): fournit une vue d’ensemble des composants AD FS et son fonctionnement, avec des détails sur les scénarios pris en charge de l’authentification/autorisation.
- [Procédures pas à pas AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): une liste d’articles de procédure, qui fournissent des instructions détaillées sur l’implémentation les flux associés d’authentification/autorisation.
