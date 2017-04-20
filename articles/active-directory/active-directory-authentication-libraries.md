<properties
   pageTitle="Bibliothèques d’authentification Azure Active Directory | Microsoft Azure"
   description="La bibliothèque d’authentification AD Azure (ADAL) permet aux clients les développeurs d’applications faciliter l’authentification des utilisateurs vers le cloud ou sur les sites Active Directory (AD) et ensuite obtenir les jetons d’accès pour la sécurisation des appels d’API."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-authentication-libraries"></a>Bibliothèques d’authentification Azure Active Directory

L’authentification AD Azure Library (ADAL) permet aux développeurs d’applications client faciliter l’authentification des utilisateurs vers le cloud ou sur les sites Active Directory (AD) et ensuite obtenir les jetons d’accès pour la sécurisation des appels d’API. ADAL comporte de nombreuses fonctionnalités que l’authentification de rendre plus facile pour les développeurs, tels que de la prise en charge asynchrone, un cache de jetons configurable qui stocke les jetons d’accès et jetons d’actualisation, l’actualisation automatique jeton lorsqu’un jeton d’accès arrive à expiration et n’est disponible, un jeton d’actualisation et bien plus encore. En gérant la majeure partie de la complexité, les ADAL peuvent aider un développeur à se concentrer sur la logique métier dans leur application et facilement sécuriser les ressources sans être un expert en sécurité.

ADAL est disponible sur un grand nombre de plates-formes.

|Plate-forme|Nom du package|Client/serveur|Télécharger|Code source|Documentation et exemples|
|---|---|---|---|---|---|
|Client .NET, Windows Store, série UWP, Xamarin iOS et Android|Bibliothèque de l’authentification d’Active Directory (ADAL) pour .NET v3 |Client|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[Documentation](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)|
|.NET client, magasin de Windows, Windows Phone 8.1 |Bibliothèque de l’authentification d’Active Directory (ADAL) pour .NET v2 |Client|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2)|[ADAL pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2)|[Documentation](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory)|
|JavaScript|Bibliothèque de l’authentification d’Active Directory (ADAL) pour JavaScript|Client|[ADAL pour JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL pour JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|Exemple : [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, e/s|Bibliothèque de l’authentification d’Active Directory (ADAL) pour Objective-C|Client|[ADAL pour Objective-C (CocoaPods)](http://cocoadocs.org/docsets/ADAL/)|[ADAL pour Objective-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|Exemple : [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Bibliothèque de l’authentification d’Active Directory (ADAL) pour Android|Client|[ADAL pour Android (le référentiel Central)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL pour Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|Exemple : [NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Bibliothèque de l’authentification d’Active Directory (ADAL) pour Node.js|Client|[ADAL pour Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL pour Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|Exemple : [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Middleware de l’authentification Passport de Microsoft Azure Active Directory pour le nœud|Client|[Azure Active Directory Passport Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Active Directory Azure Node.js (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Bibliothèque de l’authentification d’Active Directory (ADAL) pour Java|Client|[ADAL pour Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL pour Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Extensions de protocole d’identité pour le Microsoft.NET Framework 4.5|Serveur|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Extensions de modèle AD identité Azure pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Gestionnaire de jeton Web JSON pour Microsoft .net Framework 4.5|Serveur|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Extensions de modèle AD identité Azure pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Middleware OWIN qui permet à une application d’utiliser la technologie de Microsoft pour l’authentification.|Serveur|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|Middleware OWIN qui permet à une application d’utiliser OpenIDConnect pour l’authentification.|Serveur|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Exemple : [WebApp-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|Middleware OWIN qui permet à une application utiliser WS-Federation pour l’authentification.|Serveur|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Exemple : [WebApp-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## <a name="scenarios"></a>Scénarios

Voici trois scénarios courants dans lesquels les ADAL peut être utilisé pour l’authentification.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>L’authentification des utilisateurs d’une Application cliente à une ressource distante

Dans ce scénario, un développeur a un client, tel qu’une application WPF, qui a besoin d’accéder à une ressource distante sécurisée par AD Azure, par exemple un site web API. Il a un abonnement Azure sait comment appeler l’API web en aval et connaît le locataire AD Azure qui utilise de l’API web. Par conséquent, il peut utiliser ADAL pour faciliter l’authentification avec AD Azure, par délégation entièrement de l’expérience de l’authentification à ADAL ou en gérant explicitement les informations d’identification de l’utilisateur. ADAL rend facile à authentifier l’utilisateur, obtenir un jeton d’accès et le jeton de l’actualisation d’Active Directory Azure et ensuite d’utiliser le jeton d’accès pour rendre demande à l’API web.

Pour obtenir un exemple de code qui illustre ce scénario à l’aide de l’authentification dans AD Azure, consultez [Application de WPF Native Client de l’API Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>L’authentification d’une Application de serveur à une ressource distante

Dans ce scénario, un développeur possède une application qui s’exécute sur un serveur qui a besoin d’accéder à une ressource distante sécurisée par AD Azure, par exemple un site web API. Il a un abonnement Azure sait comment appeler le service en aval et connaît que le locataire AD Azure l’API web utilise. Par conséquent, il peut utiliser ADAL pour faciliter l’authentification avec Azure AD en gérant explicitement les informations d’identification de l’application. ADAL rend facile de récupérer un jeton d’Azure AD à l’aide d’informations d’identification de l’application client et ensuite utiliser ce jeton pour rendre demande à l’API web. ADAL gère également la gestion de la durée de vie du jeton d’accès par la mise en cache et renouveler si nécessaire. Pour obtenir un exemple de code qui illustre ce scénario, consultez [Application de Console pour l’API Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>L’authentification d’une Application serveur pour le compte d’un utilisateur à accéder à une ressource distante

Dans ce scénario, un développeur possède une application qui s’exécute sur un serveur qui a besoin d’accéder à une ressource distante sécurisée par AD Azure, par exemple un site web API. La demande doit également être effectuée pour le compte d’un utilisateur dans AD Azure. Il a un abonnement Azure sait comment appeler l’API de web en aval et connaît la publicité Azure client le service utilise. Une fois que l’utilisateur est authentifié à l’application web, l’application peut obtenir un code d’autorisation de l’utilisateur, à partir d’AD Azure. L’application web peut puis utiliser ADAL pour obtenir un jeton d’accès et d’actualiser le jeton pour le compte d’un utilisateur avec les références client et le code d’autorisation associés à l’application d’Active Directory Azure. Une fois que l’application web est en possession d’un jeton d’accès, il peut appeler l’API web jusqu'à ce que le jeton a expiré. Le jeton d’expiration, l’application web peut utiliser ADAL pour obtenir un nouveau jeton d’accès à l’aide de l’actualisation du jeton reçu précédemment.


## <a name="see-also"></a>Voir aussi

[Guide du développeur d’Azure Active Directory](active-directory-developers-guide.md)

[Scénarios d’authentification pour Azure Active directory](active-directory-authentication-scenarios.md)

[Exemples de code Active Directory Azure](active-directory-code-samples.md)
