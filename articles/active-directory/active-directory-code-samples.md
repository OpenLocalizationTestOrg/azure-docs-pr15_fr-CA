<properties
   pageTitle="Les exemples de Code Azure Active Directory | Microsoft Azure"
   description="Un index Azure Active Directory d’exemples de code, organisés par le scénario."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="priyamohanram"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="azure-active-directory-code-samples"></a>Exemples de Code Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Vous pouvez utiliser Microsoft Azure Active Directory (AD Azure) pour ajouter l’authentification et l’autorisation à vos applications web et les web API. Cette section vous renvoie à des exemples de code qui vous montrent comment procéder et d’extraits de code que vous pouvez utiliser dans vos applications. Sur la page d’exemple de code, vous trouverez en lecture détaillée-me rubriques qui aident à la configuration requise, installation et de configuration. Et le code est commenté pour vous aider à comprendre les sections critiques.

Pour comprendre le scénario de base pour chaque type d’échantillon, consultez scénarios d’authentification pour Azure AD.

Contribuer à nos exemples de GitHub : [Microsoft Azure Active Directory exemples et la Documentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Navigateur Web pour l’Application Web

Ces exemples montrent comment écrire une application web qui indique au navigateur de l’utilisateur pour ouvrir une session les annonces Azure.



| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Utilisez OpenID connecter (middleware de OWIN de connexion ASP.Net OpenID) pour authentifier les utilisateurs à partir d’un fermier AD Azure.
| C# / .NET | [WebApp-pouvant être partagée-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Une application web MVC de .NET qui utilise le OpenID se connecter (middleware de OWIN de connexion ASP.Net OpenID) pour authentifier les utilisateurs à partir de différents utilisateurs AD Azure avec plusieurs utilisateurs.
| C# / .NET | [WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | WS-Federation (middleware ASP.Net WS-Federation OWIN) permet d’authentifier les utilisateurs à partir d’un fermier AD Azure.






## <a name="single-page-application-spa"></a>Application d’une Page unique (SPA)

Cet exemple montre comment écrire une application de page unique sécurisée avec AD Azure.  

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| JavaScript, C# / .NET | [SinglePageApp-DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | Utilisez ADAL pour JavaScript et AD Azure pour sécuriser une application basée sur les AngularJS une seule page mis en œuvre avec un principal de l’API web ASP.NET.


## <a name="native-application-to-web-api"></a>Application native pour une API Web

Ces exemples de code montrent comment créer des applications client natif qui appellent web API qui est sécurisés par AD Azure. Ils utilisent la [Bibliothèque d’authentification Azure AD (ADAL)](active-directory-authentication-libraries.md) et [2.0 OAuth dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | Le plug-in ADAL pour Apache Cordova permet de générer une application Apache Cordova qui appelle une API web et utilise Azure AD pour l’authentification.
| C# / .NET | [NativeClient-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | Une application WPF de .NET qui appelle une API web qui est sécurisée à l’aide d’Active Directory Azure.
| C# / .NET | [NativeClient-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Une application Windows Store qui appelle une API web qui est sécurisée avec AD Azure.
| C# / .NET | [NativeClient-WebAPI-pouvant être partagée-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | Appel d’un API qui est sécurisé avec Azure AD du web mutualisée une application du Windows Store.
| C# / .NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | Une application de client natif qui appelle une API, qui obtient un jeton pour agir pour le compte de l’utilisateur d’origine et utilise ensuite le jeton pour appeler un autre site web API de web.
| C# / .NET | [NativeClient-WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | Une application Windows Store pour Windows Phone 8.1 qui appelle une API web qui est sécurisée par AD Azure.
| ObjC | [NativeClient-iOS](https://github.com/Azure-Samples/active-directory-ios) | Une application iOS qui appelle une API web qui requiert Azure AD pour l’authentification.
| C# / .NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | Une application native client inclut une logique pour traiter un jeton JWT dans un site web API, au lieu d’utiliser OWIN middleware.
| C# / Xamarin | [NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | Une liaison de Xamarin pour le natif Azure AD authentification Library (ADAL) pour la bibliothèque Android.
| C# / Xamarin | [NativeClient-Xamarin-e/s](https://github.com/Azure-Samples/active-directory-xamarin-ios) | Une liaison de Xamarin pour le natif Azure AD authentification Library (ADAL) pour les e/s.
| C# / Xamarin | [NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | Un projet de Xamarin qui cible les cinq plates-formes et appelle une API web qui est sécurisé par AD Azure.
| C# / .NET | [NativeClient Headless DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | Une application native qui effectue l’authentification non interactive et appelle une API web qui est sécurisée par AD Azure.



## <a name="web-application-to-web-api"></a>Application Web sur le Web API

Afficher comment utiliser [2.0 OAuth dans AD Azure](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour créer des applications web qui appellent ces exemples de code web API qui est sécurisés par AD Azure.

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | Appeler une API web avec les autorisations de la signature de l’utilisateur.
|  C# / .NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | Appeler une API web avec les autorisations de l’application.
| C# / .NET | [WebApp-WebAPI-OAuth2-identité de l’utilisateur-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | Ajouter une autorisation 2.0 [OAuth dans Azure annonce](https://msdn.microsoft.com/library/azure/dn645545.aspx) à une application web existante afin qu’elle appelle une API web.
| JavaScript | [WebAPI-Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | Configurer un service API REST est intégré à AD Azure pour la protection de l’API. Comprend un serveur Node.js avec une API Web.
| C# / .NET | [WebApp-WebAPI-pouvant être partagée-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |  Une mutualisée MVC web application qui utilise le OpenID se connecter (middleware de OWIN de connexion ASP.Net OpenID) pour authentifier les utilisateurs à partir d’un fermier AD Azure. Utilise un code d’autorisation pour appeler l’API graphique.

## <a name="server-or-daemon-application-to-web-api"></a>Serveur ou une Application de démon sur Web API

Ces exemples de code montrent comment générer une processus ou une application serveur qui Récupère les ressources à partir d’un site web API à l’aide de la [Bibliothèque de l’authentification d’Active Directory Azure (ADAL)](active-directory-authentication-libraries.md) et [2.0 OAuth dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| C# / .NET | [Démon-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | Une application de console appelle une API web. Les informations d’identification du client sont un mot de passe.
| C# / .NET | [Démon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | Une application de console qui appelle une API web. Les informations d’identification du client sont un certificat.


## <a name="calling-azure-ad-graph-api"></a>L’appel d’API d’Azure AD graphique

Ces exemples de code montrent comment créer des applications qui appellent l’API Azure AD graphique pour lire et écrire des données de l’annuaire.

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | Une application web qui utilise l’API de graphe pour accéder aux données de l’annuaire Active Directory Azure.
| PHP | [WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | Une application web qui utilise l’API de graphe pour accéder aux données de l’annuaire Active Directory Azure.
| C# / .NET | [WebApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Une application web qui utilise l’API de graphe pour accéder aux données de l’annuaire Active Directory Azure.
| C# / .NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | Cette application console montre des appels communs en lecture et en écriture à l’API de graphique et indique comment exécuter l’attribution de licences utilisateur et mettre à jour les liens et la photo miniature d’un utilisateur.
| C# / .NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Une application console qui utilise la requête différentielle dans l’API de graphique pour obtenir les modifications périodiques pour les objets utilisateur dans un locataire AD Azure.
| C# / .NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | Une application MVC utilise des requêtes de l’API de graphique pour générer un organigramme de société simple.
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | Une application PHP qui appelle l’API graphique pour inscrire une extension puis lire, mettre à jour et supprimer des valeurs dans l’attribut d’extension.


## <a name="authorization"></a>Autorisation

Ces exemples de code montrent comment utiliser Azure AD pour l’autorisation.

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Effectuer un contrôle d’accès basé sur les rôles (RBAC) à l’aide de revendications de groupe Azure Active Directory dans une application qui est intégrée à AD Azure.
| C# / .NET | [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Effectuer un contrôle d’accès basé sur les rôles (RBAC) à l’aide de rôles d’application Azure Active Directory dans une application qui est intégrée à AD Azure.


## <a name="legacy-walkthroughs"></a>Procédures pas à pas hérité

Ces procédures pas à pas utilisent la technologie légèrement plus ancienne, mais peut présenter un intérêt.

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| C# / .NET | [Autorisation basée sur les ACL et rôle dans une Application d’annonces Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=331694) | Dans une application qui est intégrée à AD Azure, effectuer l’autorisation basée sur les rôles (RBAC) et l’autorisation basée sur les ACL.
| C# / .NET |  [AAL - application du Windows Store pour service REST - authentification](http://go.microsoft.com/fwlink/?LinkId=330605) |  [Bibliothèque de l’authentification d’Active Directory Azure (ADAL)](active-directory-authentication-libraries.md) (anciennement AAL) pour la version bêta de Windows Store permet d’ajouter des fonctionnalités d’authentification utilisateur pour une application du Windows Store.
| C# / .NET | [Authentification d’ADAL - application Native pour le service REST - avec DAS via la boîte de dialogue](http://go.microsoft.com/fwlink/?LinkId=259814) |  [Bibliothèque de l’authentification d’Active Directory Azure (ADAL)](active-directory-authentication-libraries.md) permet d’ajouter des fonctionnalités d’authentification utilisateur pour un client WPF.
| C# / .NET | [Authentification d’ADAL - application Native pour le service REST - ACS via la boîte de dialogue](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |  Utiliser la [Bibliothèque d’authentification Azure AD (ADAL)](active-directory-authentication-libraries.md) et [Access Control Service 2.0 (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) pour ajouter des fonctionnalités d’authentification utilisateur pour un client WPF.
| C# / .NET | [ADAL - authentification de serveur à serveur](http://go.microsoft.com/fwlink/?LinkId=259816) | [Bibliothèque de l’authentification d’Active Directory Azure (ADAL)](active-directory-authentication-libraries.md) permet de sécuriser des appels de service à partir d’un processus de côté serveur à un service de MVC4 Web API reste.
| C# / .NET | [Ajout de session pour votre Application Web à l’aide d’Active Directory Azure](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Configurer une application .NET pour exécuter web SSO contre votre annuaire d’entreprise Active Directory Azure.
| C# / .NET | [Développement d’Applications Web de mutualisée avec Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Annonce Azure permet d’ajouter une ouverture de session unique les capacités d’accès de répertoire d’une application .NET pour travailler sur plusieurs organisations.
JAVA | [Exemple d’application Java pour Azure AD graphique API](http://go.microsoft.com/fwlink/?LinkId=263969) | Utiliser l’API de graphique pour l’accès aux données de l’annuaire à partir d’AD Azure.
PHP | [Exemple d’application PHP pour Azure AD graphique API](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Utiliser l’API de graphique pour l’accès aux données de l’annuaire à partir d’AD Azure.
| C# / .NET | [Exemple d’application Azure AD graphique API](http://go.microsoft.com/fwlink/?LinkID=262648) | Utiliser l’API de graphique pour l’accès aux données de l’annuaire à partir d’AD Azure.
| C# / .NET | [Exemple d’application de requête différentielle d’Azure AD graphique](http://go.microsoft.com/fwlink/?LinkId=275398) | Utilisez la requête différentielle dans l’API de graphique pour obtenir les modifications périodiques pour les objets utilisateur dans un locataire Azure AD.
| C# / .NET | [Exemple d’application pour l’intégration d’Application nuage partagé pour Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Intégrer une application mutualisée dans Azure AD.
| C# / .NET | [Sécurisation d’une Application du Windows Store et le Service de Web reste à l’aide d’Active Directory Azure](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Créer une ressource web simple API et une application client de Windows Store à l’aide d’Active Directory Azure et [Bibliothèque de l’authentification d’Active Directory Azure (ADAL)](active-directory-authentication-libraries.md).
| C# / .NET| [À l’aide de l’API de graphique pour interroger Active Directory Azure](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Configurer une application .NET de Microsoft pour utiliser l’API Azure AD graphique pour accéder aux données à partir d’un annuaire de clients AD Azure.

## <a name="see-also"></a>Voir aussi

##### <a name="other-resources"></a>Autres ressources

[Guide du développeur d’Azure Active Directory](active-directory-developers-guide.md)

[Graphique de AD Azure API conceptuel et de référence](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Bibliothèque d’API d’application d’assistance Azure AD graphique](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

