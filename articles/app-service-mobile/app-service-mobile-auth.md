<properties
    pageTitle="Authentification et autorisation dans les applications mobiles Azure | Microsoft Azure"
    description="Référence conceptuelle et vue d’ensemble de l’authentification / autorisation de fonctionnalités pour les applications mobiles Azure"
    services="app-service\mobile"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Authentification et autorisation dans les applications mobiles Azure

## <a name="what-is-app-service-authentication--authorization"></a>Quelle est l’application Service authentification / autorisation ?

> [AZURE.NOTE] Cette rubrique sera migrée vers une liste consolidée [application Service authentification / autorisation](../app-service/app-service-authentication-overview.md) rubrique, qui couvre les applications d’API, Web et Mobile.

Application Service authentification / autorisation est une fonctionnalité qui permet à votre application connecter des utilisateurs sans aucune modification de code requise sur le serveur principal chargé de l’application. Il fournit un moyen simple pour protéger votre application et utiliser les données par utilisateur.

Service d’application utilise l’identité fédérée, dans laquelle un **fournisseur d’identité** de la 3 rd-party (« IDP ») stocke des comptes et authentifie les utilisateurs, et que l’application utilise cette identité au lieu de son propre. Service d’application prend en charge les cinq fournisseurs d’identité prêts à l’emploi : _Azure Active Directory_, _Facebook_, _Google_, _Compte Microsoft_et _Twitter_. Vous pouvez également développer cette prise en charge de vos applications grâce à l’intégration d’un autre fournisseur d’identité ou de votre propre solution d’identité personnalisée.

Votre application peut tirer parti de n’importe quel nombre de ces fournisseurs d’identité, vous pouvez permettre à vos utilisateurs finaux avec les options de la façon dont ils se connecter.

Si vous souhaitez commencer immédiatement, consultez un des didacticiels suivants :

- [Ajouter l’authentification à votre application d’iOS]
- [Ajouter l’authentification à votre application Xamarin.iOS]
- [Ajouter l’authentification à votre application Xamarin.Android]
- [Ajouter l’authentification à votre application Windows]

## <a name="how-authentication-works"></a>Fonctionnement de l’authentification

Pour s’authentifier à l’aide d’un des fournisseurs d’identité, vous devez d’abord configurer le fournisseur d’identité sur votre application. Le fournisseur d’identité vous fournira avec des ID et des secrets que vous fournissez à l’application. Cela termine la relation d’approbation et permet au Service d’application valider les identités qui lui sont fournies.

Ces étapes sont décrites en détail dans les rubriques suivantes :

- [Comment faire pour configurer votre application pour utiliser la connexion d’Azure Active Directory]
- [Comment faire pour configurer votre application pour utiliser la connexion Facebook]
- [Comment faire pour configurer votre application pour utiliser la connexion de Google]
- [Comment faire pour configurer votre application pour utiliser la connexion de Microsoft Account]
- [Comment faire pour configurer votre application pour utiliser la connexion de Twitter]

Une fois que tout est configuré sur le serveur principal, vous pouvez modifier votre client à se connecter. Il existe deux approches :

- À l’aide d’une seule ligne de code, de laisser les applications Mobile client SDK connecter utilisateurs.
- Exploiter un kit de développement logiciel publié par un fournisseur d’identité donnée pour établir l’identité et d’accéder au Service de l’application.

>[AZURE.TIP] La plupart des applications doivent utiliser un fournisseur de kit de développement logiciel pour obtenir une expérience de connexion plus natif-sentiment et à tirer parti de la prise en charge de l’actualisation et autres avantages spécifiques au fournisseur.

### <a name="how-authentication-without-a-provider-sdk-works"></a>Fonctionnement de l’authentification sans fournisseur de kit de développement logiciel

Si vous ne souhaitez pas configurer un fournisseur de kit de développement logiciel, vous pouvez autoriser des applications mobiles effectuer l’ouverture de session pour vous. Le Kit de développement logiciel du client applications Mobile ouvre un affichage web pour le fournisseur de votre choix et terminer la connexion. Parfois sur des blogs et des forums vous verrez il agit comme le « flux de serveur » ou « flux dirigé de serveur » depuis le serveur gère la connexion, et le Kit de développement logiciel du client ne reçoit jamais le jeton du fournisseur.

Le code nécessaire pour démarrer ce flux est abordé dans le didacticiel d’authentification pour chaque plate-forme. À la fin du flux, le Kit de développement logiciel du client possède un jeton du Service de l’application, et le jeton est automatiquement joint à toutes les demandes vers le serveur principal.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Fonctionnement de l’authentification avec un fournisseur de kit de développement logiciel

Kit de développement logiciel travaillant auprès d’un fournisseur permet de l’expérience d’ouverture de session d’interagir plus étroitement avec la plate-forme du système d’exploitation, sur que l’application s’exécute. Cela vous donne également un jeton de fournisseur et des informations utilisateur sur le client, ce qui le rend beaucoup plus facile de consommer graphique d’API et de personnaliser l’expérience utilisateur. Il peut arriver que sur les blogs et les forums vous verrez ce appelé « flux de client » ou « client dirigé le flux » depuis le code sur le client gère la connexion, et le code client a accès à un fournisseur de jeton.

Une fois un jeton du fournisseur est obtenu, il doit être envoyé au Service de l’application pour la validation. À la fin du flux, le Kit de développement logiciel du client possède un jeton du Service de l’application, et le jeton est automatiquement joint à toutes les demandes vers le serveur principal. Le développeur peut également conserver une référence au jeton du fournisseur s’ils le souhaitent.

## <a name="how-authorization-works"></a>Fonctionnement des autorisations

Application Service authentification / autorisation expose plusieurs choix pour une **Action à effectuer lorsque la demande n’est pas authentifié**. Avant que votre code reçoit une demande donnée, vous pouvez avoir à cocher du Service de l’application pour voir si la requête est authentifiée et, si pas, rejeter et tenter l’utilisateur ouvrir une session avant d’essayer à nouveau.

Une option consiste à avoir non authentifié redirection de demandes vers un des fournisseurs d’identité. Dans un navigateur web, cela prendrait effectivement l’utilisateur vers une nouvelle page. Toutefois, votre client mobile ne peut pas être redirigé de cette manière et réponses non authentifiés reçoit une réponse HTTP _401 non autorisé_ . Compte tenu de cela, la première demande de que votre client effectue doit toujours être sur le point de terminaison de connexion et puis vous pouvez effectuer des appels à n’importe quel autre API. Si vous essayez d’appeler une autre API avant de se connecter, votre client reçoit une erreur.

Si vous souhaitez que plus granulaire de contrôle sur quels points de terminaison requièrent une authentification, vous pouvez également choisir « aucune action (autoriser les requêtes de) » pour les demandes non authentifiées. Dans ce cas, toutes les décisions d’authentification sont différées à votre code d’application. Cela vous permet également d’autoriser l’accès à des utilisateurs spécifiques en fonction des règles d’autorisation personnalisée.

## <a name="documentation"></a>Documentation

Les didacticiels suivants montrent comment ajouter une authentification pour les clients mobiles à l’aide du Service de l’application :

- [Ajouter l’authentification à votre application d’iOS]
- [Ajouter l’authentification à votre application Xamarin.iOS]
- [Ajouter l’authentification à votre application Xamarin.Android]
- [Ajouter l’authentification à votre application Windows]

Les didacticiels suivants montrent comment configurer le Service d’application pour tirer parti de différents fournisseurs d’authentification :

- [Comment faire pour configurer votre application pour utiliser la connexion d’Azure Active Directory]
- [Comment faire pour configurer votre application pour utiliser la connexion Facebook]
- [Comment faire pour configurer votre application pour utiliser la connexion de Google]
- [Comment faire pour configurer votre application pour utiliser la connexion de Microsoft Account]
- [Comment faire pour configurer votre application pour utiliser la connexion de Twitter]

Si vous souhaitez utiliser un système d’identité différent de ceux fournis ici, vous pouvez également exploiter d' [Afficher un aperçu de la prise en charge de l’authentification personnalisée dans le Kit de développement logiciel de .NET server](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Ajouter l’authentification à votre application d’iOS]: app-service-mobile-ios-get-started-users.md
[Ajouter l’authentification à votre application Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Ajouter l’authentification à votre application Xamarin.Android]: app-service-mobile-xamarin-android-get-started-users.md
[Ajouter l’authentification à votre application Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Comment faire pour configurer votre application pour utiliser la connexion d’Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Comment faire pour configurer votre application pour utiliser la connexion Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Comment faire pour configurer votre application pour utiliser la connexion de Google]: app-service-mobile-how-to-configure-google-authentication.md
[Comment faire pour configurer votre application pour utiliser la connexion de Microsoft Account]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Comment faire pour configurer votre application pour utiliser la connexion de Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
