<properties
    pageTitle="Authentification et autorisation dans les services d’application Azure | Microsoft Azure"
    description="Référence conceptuelle et vue d’ensemble de l’authentification / autorisation fonctionnalité de Service d’application Azure"
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-app-service"></a>Authentification et autorisation dans les services d’application Azure

## <a name="what-is-app-service-authentication--authorization"></a>Quelle est l’application Service authentification / autorisation ?

Application Service authentification / autorisation est une fonctionnalité qui permet à votre application pour vous connecter aux utilisateurs afin que vous n’êtes pas obligé de modifier le code sur le serveur d’application principal. Il fournit un moyen simple pour protéger votre application et utiliser les données par utilisateur.

Service d’application utilise l’identité fédérée, dans laquelle un fournisseur d’identité du tiers stocke les comptes et authentifie les utilisateurs. L’application s’appuie sur les informations d’identité du fournisseur afin que l’application ne doit pas stocker ces informations dans lui-même. Service d’application prend en charge les cinq fournisseurs d’identité prêts à l’emploi : Azure Active Directory, Facebook, Google, Microsoft Account et Twitter. Votre application peut utiliser n’importe quel nombre de ces fournisseurs d’identité pour fournir à vos utilisateurs avec les options de la façon dont ils se connectent. Pour développer la prise en charge intégrée, vous pouvez intégrer un autre fournisseur d’identité ou de [votre propre solution personnalisée d’identité][custom-auth].

Si vous souhaitez commencer immédiatement, consultez parmi les didacticiels suivants :

- [Ajouter l’authentification à votre application d’e/s] [ iOS] (ou [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]ou [Cordova])
- [Authentification de l’utilisateur pour les applications d’API dans le Service d’application Azure][apia-user]
- [Mise en route de Service d’application Azure - partie 2][web-getstarted]

## <a name="how-authentication-works-in-app-service"></a>Fonctionnement de l’authentification dans le Service d’application

Pour s’authentifier à l’aide d’un des fournisseurs d’identité, vous devez d’abord configurer le fournisseur d’identité sur votre application. Le fournisseur d’identité fournit ensuite des ID et des secrets que vous fournissez au Service de l’application. La relation d’approbation est terminée pour que le Service d’application peut valider les assertions de l’utilisateur, telles que les jetons d’authentification, à partir du fournisseur d’identité.

Pour vous connecter un utilisateur à l’aide d’un de ces fournisseurs, l’utilisateur doit être redirigé vers un point de terminaison qui se connecte aux utilisateurs pour ce fournisseur. Si les clients utilisent un navigateur web, vous pouvez avoir Service d’application de diriger automatiquement tous les utilisateurs non authentifiés au point de terminaison qui se connecte aux utilisateurs. Dans le cas contraire, vous devez diriger vos clients vers `{your App Service base URL}/.auth/login/<provider>`, où `<provider>` est une des valeurs suivantes : DAS, facebook, google, microsoft ou twitter. Les scénarios mobiles et API sont expliqués dans les sections plus loin dans cet article.

Les utilisateurs qui interagissent avec votre application via un navigateur web aura un cookie la valeur afin qu’ils restent authentifiés lorsqu’ils parcourent votre application. Pour les autres types de client, telles que mobile, un jeton de web JSON (JWT), qui doivent être présentés dans les `X-ZUMO-AUTH` en-tête, sera émis pour le client. Le client applications Mobile SDK gère cela pour vous. Sinon, un jeton d’identité Azure Active Directory ou un jeton d’accès peut être directement inclus dans le `Authorization` en-tête sous la forme d’un [jeton de support](https://tools.ietf.org/html/rfc6750).

Service d’application valide n’importe quel cookie ou un jeton que votre application émet pour authentifier les utilisateurs. Pour restreindre l’accès à votre application, consultez la section [Authorization](#authorization) , plus loin dans cet article.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Authentification auprès d’un fournisseur de kit de développement mobile

Une fois que tout est configuré sur le serveur principal, vous pouvez modifier les clients mobiles à vous connecter avec le Service d’application. Il existe deux approches :

- Utilisez un kit de développement logiciel qui publie par un fournisseur d’identité donnée pour établir l’identité et d’accéder au Service de l’application.
- Utiliser une seule ligne de code afin que les utilisateurs peut se connecter le client applications Mobile SDK.

>[AZURE.TIP] La plupart des applications doivent utiliser un kit de développement logiciel du fournisseur afin d’obtenir une expérience plus cohérente lorsque les utilisateurs se connecter, d’utiliser la prise en charge de l’actualisation et pour obtenir d’autres avantages que le fournisseur spécifie.

Lorsque vous utilisez un fournisseur de kit de développement logiciel, les utilisateurs peuvent signer une expérience qui intègre plus étroitement avec le système d’exploitation que l’application est en cours d’exécution. Cela vous donne également un jeton de fournisseur et des informations utilisateur sur le client, ce qui le rend beaucoup plus facile de consommer graphique d’API et de personnaliser l’expérience utilisateur. Il peut arriver que sur les blogs et les forums, vous verrez ce appelé « flux de client » ou « flux dirigées par le client » car le code sur le client se connecte aux utilisateurs et le code client a accès à un fournisseur de jeton.

Après obtention d’un jeton de fournisseur, il doit être envoyé au Service de l’application pour la validation. Une fois que le Service de l’application valide le jeton, Service de l’application crée un nouveau jeton de Service d’application qui est renvoyé au client. Le Kit de développement logiciel du client applications Mobile a des méthodes d’assistance pour gérer cet échange et lier automatiquement le jeton à toutes les demandes vers le serveur principal chargé de l’application. Les développeurs maintiennent également une référence au jeton du fournisseur s’ils le souhaitent.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Authentification mobile sans fournisseur de kit de développement logiciel

Si vous ne souhaitez pas configurer un fournisseur de kit de développement logiciel, vous pouvez autoriser la fonctionnalité d’applications Mobile de Service d’application Azure pour vous connecter à votre place. Le Kit de développement logiciel du client applications Mobile ouvre un affichage web pour le fournisseur de votre choix et vous connecter l’utilisateur. Il peut arriver que sur les blogs et les forums, vous verrez cette dénommée « flux de serveur » ou « flux dirigé de serveur », car le serveur gère le processus qui se connecte aux utilisateurs, et le Kit de développement logiciel du client ne reçoit jamais le jeton du fournisseur.

Pour démarrer ce flux de code est inclus dans le didacticiel d’authentification pour chaque plate-forme. À la fin du flux, le Kit de développement logiciel du client possède un jeton du Service de l’application, et le jeton est automatiquement joint à toutes les demandes vers le serveur principal chargé de l’application.

### <a name="service-to-service-authentication"></a>Authentification de service-service

Bien que vous pouvez donner aux utilisateurs l’accès à votre application, vous pouvez également approuver une autre application pour appeler votre propre API. Par exemple, vous pouvez avoir une application web appeler une API dans une autre application web. Dans ce scénario, vous utilisez informations d’identification pour un compte de service au lieu des informations d’identification de l’utilisateur afin d’obtenir un jeton. Un compte de service est également appelé un *service principal* dans le jargon d’Azure Active Directory et l’authentification qui utilise ce compte est également connu comme un scénario de service à service.

>[AZURE.IMPORTANT] Comme les applications mobiles s’exécutent sur les périphériques de client, les applications mobiles effectuent nombre _pas_ d’applications sécurisées et ne doit pas utiliser un flux de principal du service. Au lieu de cela, ils doivent utiliser un flux de l’utilisateur qui a été décrit précédemment.

Pour les scénarios de service-service, Service d’application peut protéger votre application à l’aide d’Azure Active Directory. L’application appelante doit simplement fournir un jeton d’autorisation principal service Azure Active Directory qui est obtenu en fournissant au client ID et secret d’Azure Active Directory du client. Un exemple de ce scénario qui utilise les API ASP.NET apps est expliqué dans le didacticiel, [l’authentification principale du Service pour les applications d’API][apia-service].

Si vous souhaitez utiliser l’authentification du Service d’application pour traiter un scénario de service-service, vous pouvez utiliser l’authentification de base ou de certificats clients. Pour plus d’informations sur les certificats de client dans Azure, consultez [La procédure pour configurer TLS mutuelle l’authentification pour les applications Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Pour plus d’informations sur l’authentification de base dans ASP.NET, reportez-vous à la section [Filtres de l’authentification dans ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Authentification des comptes de service à partir d’une application de logique de Service d’application pour une application API est un cas particulier qui est détaillé dans [l’utilisation de votre API personnalisé hébergé sur le Service d’application avec des applications de logique](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Fonctionnement des autorisations dans le Service d’application

Vous avez un contrôle total sur les requêtes qui accèdent à votre application. Application Service authentification / autorisation peut être configurée avec un des problèmes suivants :

- Autoriser uniquement les demandes authentifiées accéder à votre application.

    Si un navigateur reçoit une demande anonyme, Service de l’application redirige vers une page pour le fournisseur d’identité que vous choisissez afin que les utilisateurs peuvent se connecter. Si la demande provient d’un périphérique mobile, la réponse renvoyée est une réponse HTTP _401 non autorisé_ .

    Avec cette option, vous n’avez pas besoin d’écrire du code d’authentification du tout dans votre application. Si vous avez besoin d’autorisations plus précises, des informations sur l’utilisateur sont disponibles pour votre code.

- Autoriser toutes les demandes atteindre votre application, mais valider les demandes authentifiées et transmettre les informations d’authentification dans les en-têtes HTTP.

    Cette option diffère des décisions d’autorisation à votre code d’application. Il offre davantage de flexibilité dans la gestion des demandes anonymes, mais vous devez écrire du code.

- Autoriser les demandes de tous les accès à votre application et effectuer aucune action sur les informations d’authentification dans les requêtes.

    Dans ce cas, l’authentification / autorisation fonctionnalité est désactivée. Les tâches d’authentification et d’autorisation sont entièrement à votre code d’application.

Les comportements précédents sont contrôlées par l’option de **l’Action à effectuer lorsque la demande n’est pas authentifié** dans le portail Azure. Si vous choisissez * *d’Ouvrir une session avec *un nom de fournisseur* **, toutes les demandes doivent être authentifiés.** Autoriser les requêtes (aucune action) ** diffère de la décision d’autorisation à votre code, mais il fournit toujours les informations d’authentification. Si vous souhaitez que votre code gère tous les éléments, vous pouvez désactiver l’authentification / fonctionnalité d’autorisation.

## <a name="working-with-user-identities-in-your-application"></a>Utilisation de l’identité des utilisateurs dans votre application

Service d’application transmet des informations utilisateur à votre application à l’aide d’en-têtes spéciaux. Demandes externes interdisent ces en-têtes et est uniquement présent if définie par l’application, Service d’authentification / autorisation. Certains en-têtes de l’exemple sont les suivants :

* X-MS-PRINCIPAL-NOM DU CLIENT
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Code qui est écrit dans une langue ou d’un framework peut obtenir les informations dont il a besoin de ces en-têtes. Pour les applications ASP.NET 4.6, le **ClaimsPrincipal** est automatiquement définie avec les valeurs appropriées.

Votre application peut également obtenir des informations utilisateur supplémentaires via un verbe HTTP GET sur le `/.auth/me` point de terminaison de votre application. Un jeton valide qui est inclus avec la requête retournera une charge JSON avec plus de détails sur le fournisseur qui est utilisé, le jeton du fournisseur sous-jacent et d’autres informations de l’utilisateur. Le serveur d’applications Mobile SDK fournissent des méthodes d’assistance pour travailler avec ces données. Pour plus d’informations, voir [comment utiliser le SDK de Node.js applications Azure Mobile](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)et [travailler sur le serveur principal de .NET SDK pour les applications mobiles Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Documentation et ressources supplémentaires

### <a name="identity-providers"></a>Fournisseurs d’identité
Les didacticiels suivants montrent comment configurer le Service de l’application pour utiliser différents fournisseurs d’authentification :

- [Comment faire pour configurer votre application pour utiliser la connexion d’Azure Active Directory][AAD]
- [Comment faire pour configurer votre application pour utiliser la connexion Facebook][Facebook]
- [Comment faire pour configurer votre application pour utiliser la connexion de Google][Google]
- [Comment faire pour configurer votre application pour utiliser la connexion de Microsoft Account][MSA]
- [Comment faire pour configurer votre application pour utiliser la connexion de Twitter][Twitter]

Si vous souhaitez utiliser un système d’identité différent de ceux fournis ici, vous pouvez également utiliser l' [Afficher un aperçu de la prise en charge de l’authentification personnalisée dans le Kit de développement logiciel du serveur Mobile applications .NET][custom-auth], qui peut être utilisée dans les applications web, les applications mobiles ou applications d’API.

### <a name="web-applications"></a>Applications Web
Les didacticiels suivants montrent comment ajouter une authentification pour une application web :

- [Mise en route de Service d’application Azure - partie 2][web-getstarted]

### <a name="mobile-applications"></a>Applications mobiles
Les didacticiels suivants montrent comment ajouter une authentification pour les clients mobiles à l’aide du flux dirigées par le serveur :

- [Ajouter l’authentification à votre application d’iOS][iOS]
- [Ajouter une authentification à votre application d’Android] [Android]
- [Ajouter une authentification à votre application Windows] [Windows]
- [Authentification d’ajouter à votre application Xamarin.iOS] [Xamarin.iOS]
- [Authentification d’ajouter à votre application Xamarin.Android] [Xamarin.Android]
- [Authentification d’ajouter à votre application Xamarin.Forms] [Xamarin.Forms]
- [Ajouter une authentification à votre application Cordova] [Cordova]

Utilisez les ressources suivantes si vous souhaitez utiliser le flux client dirigé pour Azure Active Directory :

- [Utilisez la bibliothèque d’authentification Active Directory pour iOS][ADAL-iOS]
- [Utilisez la bibliothèque d’authentification Active Directory pour Android][ADAL-Android]
- [Utilisez la bibliothèque d’authentification Active Directory pour Windows et Xamarin][ADAL-dotnet]

Utilisez les ressources suivantes si vous souhaitez utiliser le flux client dirigé pour Facebook :

- [Utilisez le SDK Facebook pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Utilisez les ressources suivantes si vous souhaitez utiliser le flux client dirigé pour Twitter :

- [Utilisez Twitter de tissu pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Utilisez les ressources suivantes si vous souhaitez utiliser le flux client dirigé pour Google :

- [Utiliser Google connexion SDK pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

### <a name="api-applications"></a>Applications d’API
Les didacticiels suivants montrent comment protéger vos applications API :

- [Authentification de l’utilisateur pour les applications d’API dans le Service d’application Azure][apia-user]
- [Authentification principale du service pour les applications d’API dans le Service d’application Azure][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[web-getstarted]: ../app-service-web/app-service-web-get-started-2.md#authenticate-your-users

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
