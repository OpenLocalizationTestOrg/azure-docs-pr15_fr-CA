<properties
   pageTitle="Les bibliothèques d’authentification v2.0 Active Directory Azure | Microsoft Azure"
   description="Les bibliothèques client compatible et bibliothèques serveur middleware et bibliothèque connexe, source et liens exemples, pour le point de terminaison v2.0 Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="skwan;bryanla"/>


# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure bibliothèques de l’authentification Active Directory v2.0
Le point de terminaison Azure Active Directory (AD Azure) version 2.0 prend en charge les protocoles standard OAuth 2.0 et 1.0 de connexion OpenID. Vous pouvez utiliser différentes bibliothèques à partir de Microsoft et d’autres organisations avec le point de terminaison v2.0.

Lorsque vous générez une application qui utilise le point de terminaison v2.0, nous vous conseillons d’utiliser des bibliothèques qui sont écrits par des experts en domaines protocole qui suivent une méthodologie de cycle de vie de développement de sécurité (SDL), similaire à [celui suivi par Microsoft][Microsoft-SDL]. Si vous décidez de la prise en charge pour les protocoles, de coder manuellement, nous vous recommandons de suivre la méthodologie SDL et soyez extrêmement attentif à la sécurité dans les spécifications de normes pour chaque protocole.

## <a name="types-of-libraries"></a>Types de bibliothèques

Azure AD 2.0 fonctionne avec deux types de bibliothèques :

- **Les bibliothèques clientes**. Serveurs et clients natifs utilisent bibliothèques client pour obtenir des jetons d’accès pour l’appel d’une ressource, par exemple de Microsoft Graph.
- **Les bibliothèques serveur middleware**. Applications Web utilisent des bibliothèques de middleware de serveur pour l’authentification de l’utilisateur. API Web utilisent les bibliothèques serveur middleware valide des jetons qui sont envoyées par des clients natifs ou par d’autres serveurs.

## <a name="library-support"></a>Prise en charge de la bibliothèque
Dans la mesure où vous pouvez choisir n’importe quelle bibliothèque conformes aux normes lorsque vous utilisez le point de terminaison v2.0, il est important de savoir où aller pour la prise en charge. Pour les problèmes et demandes de fonctionnalités dans le code de la bibliothèque, contactez le propriétaire de la bibliothèque. Pour les problèmes et demandes de fonctionnalités dans l’implémentation du protocole du côté service, contactez Microsoft.

Les bibliothèques sont fournis dans les deux catégories de prise en charge :

- **Prise en charge de Microsoft**. Microsoft fournit des correctifs pour ces bibliothèques et a fait SDL préalable de ces bibliothèques.
- **Compatible**. Microsoft a testé ces bibliothèques dans les scénarios de base et confirmé qu’ils fonctionnent avec le point de terminaison v2.0. Microsoft ne fournit pas de correctifs pour ces bibliothèques et n’a pas effectué une révision de ces bibliothèques. Problèmes et demandes doivent être adressées au projet open source de la bibliothèque.

Pour obtenir la liste des bibliothèques qui fonctionnent avec le point de terminaison v2.0, consultez les sections suivantes de cet article.

## <a name="microsoft-supported-client-libraries"></a>Bibliothèques de prise en charge Microsoft des clients
| Plate-forme| Nom de la bibliothèque| Télécharger | Code source | Exemple |
| :-: | :-: | :-: | :-: | :-: |
| .NET, Windows Store, Xamarin | Bibliothèque de l’authentification de Microsoft (MSAL) pour .NET | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [MSAL pour .NET (GitHub)][ClientLib-NET-Repo] | [Exemple de client natif du bureau de Windows][ClientLib-NET-Sample] |
| Node.js | Plug-in Microsoft Azure-Active Directory-Passport.js | [Passport-Azure-AD (npm)][ClientLib-Node-Lib] | [Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] | À venir |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliothèques de prise en charge Microsoft de serveur middleware
| Plate-forme| Nom de la bibliothèque| Télécharger | Code source | Exemple |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4.x | OWIN OpenID connexion Middleware pour ASP.NET | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Projet d’interconnexions (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Exemple d’application Web][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x | Middleware de porteur de OAuth OWIN pour ASP.NET | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Projet d’interconnexions (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [Web API, exemple][ServerLib-Net4-Owin-Oauth-Sample] |
| Cœur de .NET | OWIN OpenID connexion Middleware de base de .NET | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [Sécurité ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Exemple d’application Web][ServerLib-NetCore-Owin-Oidc-Sample] |
| Cœur de .NET | Middleware de OAuth PORTEUR OWIN de base de .NET | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [Sécurité ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | À venir |
| Node.js | Microsoft Azure Active Directory Passport.js plug-in | [Passport-Azure-AD (npm)][ServerLib-Node-Lib] | [Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] | [Exemple d’application Web][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Bibliothèques client compatible
| Plate-forme| Nom de la bibliothèque | Version testée | Code source | Exemple |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [Exemple d’application native](active-directory-v2-devquickstarts-android.md) |
| e/s | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [Exemple d’application native](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello.js](https://adodson.com/hello.js/) | question 1.13.5 | [Hello.js](https://github.com/MrSwitch/hello.js) | À venir |
| Python-ballon | [Ballon-OAuthlib](https://github.com/lepture/flask-oauthlib) | 0.9.3 | [Ballon-OAuthlib](https://github.com/lepture/flask-oauthlib) | À venir |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[OAuth2 de OmniAuth](https://github.com/intridea/omniauth-oauth2) | À venir |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Bibliothèques de middleware de serveur compatible
À venir

## <a name="related-content"></a>Contenu associé
Pour plus d’informations sur le point de terminaison v2.0 AD Azure, consultez la [vue d’ensemble du v2.0 modèle application Azure AD][AAD-App-Model-V2-Overview].

Pour nous aider à affiner et à notre contenu de forme, utilisez la fonctionnalité de commentaires Disqus à la fin de cet article pour fournir des commentaires.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
