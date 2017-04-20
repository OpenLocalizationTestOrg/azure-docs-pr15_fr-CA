<properties 
   pageTitle="Authentification Active Directory et le Gestionnaire de ressources | Microsoft Azure"
   description="Guide du développeur d’authentification avec l’API du Gestionnaire de ressources Azure et Active Directory pour l’intégration d’une application avec d’autres abonnements Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="dugill;tomfitz" />

# <a name="how-to-use-azure-active-directory-and-resource-manager-to-manage-a-customers-resources"></a>Comment utiliser Azure Active Directory et le Gestionnaire de ressources pour gérer les ressources d’un client

## <a name="introduction"></a>Introduction

Si vous êtes un développeur de logiciels qui ont besoin de créer une application qui gère les ressources d’Azure du client, cette rubrique vous indique comment authentifier avec les API du Gestionnaire de ressources Azure et accéder aux ressources dans les autres abonnements. 

Votre application peut accéder à l’API Gestionnaire de ressources de deux façons :

1. **Utilisateur + l’accès de l’application**: pour les applications qui accèdent aux ressources pour le compte d’un utilisateur connecté. Cette approche fonctionne pour les applications, telles que les applications web et des outils de ligne de commande, qui traitent uniquement « interactive gestion » des ressources Azure.
1. **Accès App uniquement**: pour les applications qui s’exécutent les services de processus et de tâches planifiées. Identité de l’application bénéficie d’un accès direct aux ressources. Cette approche fonctionne pour les applications qui en ont besoin à long terme « hors connexion » sur Azure.

Cette rubrique fournit des instructions pas à pas pour créer une application qui utilise ces deux méthodes d’autorisation. Il montre comment effectuer chaque étape avec l’API REST ou C#. L’application ASP.NET MVC complète est disponible à l’adresse [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Tout le code de cette rubrique est exécuté comme une application web que vous pouvez essayer à [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense). 

## <a name="what-the-web-app-does"></a>Ce que fait l’application web

L’application web :

1. Signes dans un utilisateur Azure.
2. Demande à l’utilisateur d’accorder l’accès d’application web dans le Gestionnaire de ressources.
3. Obtient l’utilisateur + le jeton d’accès app pour accéder au Gestionnaire de ressources.
4. Utilise le jeton (de l’étape 3) pour appeler le Gestionnaire de ressources et d’affecter des principaux du service de l’application à un rôle dans l’abonnement, ce qui donne l’accès à long terme app à l’abonnement.
5. Obtient le jeton d’accès uniquement par l’application.
6. Utilise le jeton (de l’étape 5) pour gérer les ressources de l’abonnement par le biais du Gestionnaire de ressources.

Voici le flux de bout en bout de l’application web.

![Flux d’authentification du Gestionnaire de ressources](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

En tant qu’utilisateur, vous fournissez l’id d’abonnement pour l’abonnement que vous souhaitez utiliser :

![fournir des id d’abonnement](./media/resource-manager-api-authentication/sample-ux-1.png)

Sélectionnez le compte à utiliser pour la connexion.

![Sélectionnez le compte](./media/resource-manager-api-authentication/sample-ux-2.png)

Fournir vos informations d’identification.

![fournir des informations d’identification](./media/resource-manager-api-authentication/sample-ux-3.png)

Accorder l’accès de l’application à vos abonnements Azure :
 
![Autoriser l’accès](./media/resource-manager-api-authentication/sample-ux-4.png)
 
Gérer vos abonnements connectés :

![Connecter l’abonnement](./media/resource-manager-api-authentication/sample-ux-7.png)


## <a name="register-application"></a>Enregistrement de l’application

Avant de commencer le codage, vous pouvez enregistrer votre application web avec Azure Active Directory (AD). L’inscription de l’application crée une identité centrale pour votre application dans Azure AD. Il conserve des informations de base sur votre application comme ID de Client OAuth, des URL de réponse et les informations d’identification que votre application utilise pour s’authentifier et accéder aux API du Gestionnaire de ressources Azure. L’inscription de l’application enregistre également les différentes autorisations déléguées nécessaires à votre application lorsque vous accédez à Microsoft APIs au nom de l’utilisateur. 

Dans la mesure où votre application accède à d’autre d’abonnement, vous devez le configurer comme une application partagée. Pour effectuer le contrôle, fournir un domaine associé à Active Directory. Pour visualiser les domaines associés à Active Directory, connectez-vous au [portail classique](https://manage.windowsazure.com). Sélectionnez votre Active Directory, puis **domaines**.

L’exemple suivant montre comment inscrire l’application à l’aide de PowerShell d’Azure. Vous devez disposer de la version la plus récente (août 2016) de PowerShell Azure pour cette commande fonctionne. 

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
    
Pour vous connecter en tant que l’application d’Active Directory, vous avez besoin de l’id d’application et d’un mot de passe. Pour afficher l’id de l’application qui est retourné à partir de la commande précédente, utilisez :

    $app.ApplicationId

L’exemple suivant montre comment enregistrer l’application en utilisant l’infrastructure du langage commun Azure. 

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Les résultats incluent l’AppId, dont vous aurez besoin lors de l’authentification en tant que l’application.

### <a name="optional-configuration---certificate-credential"></a>Configuration facultative - informations d’identification du certificat

Annonce Azure prend également en charge les informations d’identification de certificat pour les applications : vous créez un certificat auto-signé, conserver la clé privée et ajoutez la clé publique à votre enregistrement d’application Azure AD. Pour l’authentification, votre application envoie une petite surcharge pour Azure AD signé à l’aide de votre clé privée, et AD Azure valide la signature à l’aide de la clé publique que vous avez enregistré.

Pour plus d’informations sur la création d’une application Active Directory avec un certificat, consultez [PowerShell d’Azure utilisation pour créer un service principal pour accéder aux ressources](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) ou de la [CLI d’Azure utilisation pour créer un service principal pour accéder aux ressources](resource-group-authenticate-service-principal-cli.md#create-service-principal-with-certificate).

## <a name="get-tenant-id-from-subscription-id"></a>Obtenir l’id de client à partir de l’id d’abonnement

Pour demander un jeton qui peut être utilisé pour appeler le Gestionnaire de ressources, votre application doit connaître l’ID client du locataire AD Azure qui héberge l’abonnement Azure. Très probablement, vos utilisateurs connaissent leur ID d’abonnement, mais ils ne savent ne peut-être pas leurs clients ID pour Active Directory. Pour obtenir l’id de l’utilisateur client, demandez à l’utilisateur pour l’id d’abonnement. Fournir cet id d’abonnement lors de l’envoi d’une demande sur l’abonnement :

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Échec de la demande, car l’utilisateur n’a pas encore connecté, mais vous pouvez extraire l’id de client à partir de la réponse. Cette exception, extraire l’id de client à partir de la valeur de l’en-tête de réponse pour **WWW-Authenticate**. Vous consultez cette implémentation de la méthode [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Obtenir l’utilisateur + le jeton d’accès app

Votre application redirige l’utilisateur vers Azure AD avec un OAuth 2.0 autorisent demande - pour authentifier les informations d’identification de l’utilisateur et obtenir un code d’autorisation. Votre application utilise le code d’autorisation pour obtenir un accès à un jeton pour le Gestionnaire de ressources. La méthode [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) crée la demande d’autorisation.

Cette rubrique indique les demandes d’autres API pour authentifier l’utilisateur. Vous pouvez également utiliser des bibliothèques d’assistance pour effectuer une authentification dans votre code. Pour plus d’informations sur ces bibliothèques, consultez [Azure des bibliothèques de l’authentification Active Directory](./active-directory/active-directory-authentication-libraries.md). Pour obtenir des conseils sur l’intégration de la gestion d’identité dans une application, consultez le [guide du développeur d’Azure Active Directory](./active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Demandes d’authentification (OAuth 2.0)

Émettre un Open ID connexion/OAuth2.0 autoriser demande au point de terminaison Azure AD autoriser :

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Les paramètres de chaîne de requête qui sont disponibles pour cette demande sont décrites dans la rubrique [demande un code d’autorisation](./active-directory/active-directory-protocols-oauth-code.md#request-an-authorization-code) .

L’exemple suivant montre comment demander l’autorisation de OAuth2.0 :

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Annonce Azure authentifie l’utilisateur et, si nécessaire, demande à l’utilisateur d’accorder l’autorisation à l’application. Il renvoie le code d’autorisation pour l’URL de la réponse de votre application. Selon la response_mode demandée, AD Azure soit renvoie les données dans la chaîne de requête ou en tant que données de publication.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Demandes d’authentification (Open ID de connexion)

Si vous vous souhaitez non seulement accéder Azure le Gestionnaire de ressources pour le compte de l’utilisateur, mais également autoriser l’utilisateur à se connecter à votre application à l’aide de leur compte Azure AD, émettre un Open ID autoriser demande de connexion. Avec Open ID de connexion, votre application reçoit également une id_token d’Azure Active Directory que votre application peut utiliser pour signer l’utilisateur.

Les paramètres de chaîne de requête qui sont disponibles pour cette demande sont décrites dans la rubrique [envoi de la demande d’ouverture de session](./active-directory/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) .

Un exemple de demande de connexion de code ouvert est la suivante :

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Annonce Azure authentifie l’utilisateur et, si nécessaire, demande à l’utilisateur d’accorder l’autorisation à l’application. Il renvoie le code d’autorisation pour l’URL de la réponse de votre application. Selon la response_mode demandée, AD Azure soit renvoie les données dans la chaîne de requête ou en tant que données de publication.

Est un exemple de réponse de connexion de code ouvert :

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Demande de jeton (licence de Code OAuth2.0 flux)

Maintenant que votre application a reçu le code d’autorisation dans Active Directory Azure, il est temps d’obtenir l’accès au jeton pour le Gestionnaire de ressources Azure.  Valider un OAuth2.0 Code Grant jeton demande au point de terminaison Azure AD jeton : 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Les paramètres de chaîne de requête qui sont disponibles pour cette demande sont décrites dans la rubrique [utiliser le code d’autorisation](./active-directory/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

L’exemple suivant illustre une demande pour un jeton de licence de code avec les informations d’identification de mot de passe :

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Lorsque vous travaillez avec les informations d’identification de certificat, créez un jeton JSON Web (JWT) et un signe (RSA SHA256) à l’aide de la clé privée des informations d’identification de certificat de votre application. Les types de revendications du jeton sont affichés dans [les revendications de jeton JWT](./active-directory/active-directory-protocols-oauth-code.md#jwt-token-claims). Pour référence, consultez le [code de la bibliothèque d’Active Directory faisant autorité (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) pour signer les jetons de Client Assertion JWT.

Consultez les [spécifications de connexion de code ouvert](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) pour plus d’informations sur l’authentification du client. 

L’exemple suivant illustre une demande de jeton de subvention de code avec les informations d’identification du certificat :

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
    
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012
    
    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Une réponse de l’exemple de code accorde un jeton : 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Gérer la réponse jeton du code licence

Une réponse correcte de jeton contient (utilisateur + app) jeton d’accès pour le Gestionnaire de ressources Azure. Votre application utilise ce jeton d’accès pour accéder aux Gestionnaire de ressources pour le compte de l’utilisateur. La durée de vie des jetons d’accès émises par AD Azure est d’une heure. Il est peu probable que votre application web doit le renouveler (utilisateur + app) jeton d’accès. Si elle doit le renouveler le jeton d’accès, utilisez le jeton d’actualisation que reçoit votre application dans la réponse de jeton. Valider une demande jeton OAuth2.0 au point de terminaison Azure AD jeton : 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Les paramètres à utiliser avec la demande d’actualisation sont décrits dans [l’actualisation du jeton d’accès](./active-directory/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

L’exemple suivant montre comment utiliser l’actualisation de jeton :

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Bien que les jetons de rafraîchissement peuvent être utilisés pour obtenir de nouveaux jetons d’accès pour le Gestionnaire de ressources Azure, ils ne conviennent pas pour l’accès en mode hors connexion par votre application. La durée de vie des jetons actualisation est limitée, et jetons d’actualisation sont liés à l’utilisateur. Si l’utilisateur quitte l’organisation, l’application en utilisant le jeton d’actualisation perd l’accès. Cette approche n’est pas adaptée aux applications qui sont utilisées par des équipes pour gérer leurs ressources Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Vérifiez si utilisateur peut affecter l’accès à l’abonnement

Votre application dispose maintenant d’un jeton d’accès Azure le Gestionnaire de ressources pour le compte de l’utilisateur. L’étape suivante consiste à connecter votre application à l’abonnement. Une fois connecté, votre application peut gérer les abonnements, même lorsque l’utilisateur n’est pas présent (accès hors connexion à long terme). 

Pour chaque abonnement pour vous connecter, appelez les [autorisations de liste de gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn906889.aspx) API pour déterminer si l’utilisateur dispose des droits de gestion d’accès à l’abonnement.

La méthode [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) de l’exemple d’application ASP.NET MVC implémente cet appel.

L’exemple suivant montre comment demander des autorisations d’un utilisateur sur un abonnement. 83cfe939-2402-4581-b761-4f59b0a041e4 est l’id de l’abonnement.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Est un exemple de la réponse pour obtenir les autorisations de l’utilisateur lors de l’abonnement :

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Les autorisations API renvoie plusieurs autorisations. Chaque autorisation se compose d’actions autorisées (actions) et interdit les actions (notactions). Si une action est présent dans la liste d’actions autorisées de toute autorisation et ne figurent pas dans la liste notactions de cette autorisation, l’utilisateur est autorisé à exécuter cette action. **Microsoft.Authorization/RoleAssignments/Write** est l’action que qui autorise l’accès des droits de gestion. Votre application doit analyser le résultat des autorisations pour rechercher une correspondance d’expression régulière dans cette chaîne d’action dans les actions et les notactions de chaque autorisation.

## <a name="get-app-only-access-token"></a>Obtenir le jeton accès uniquement par l’application

Maintenant, vous savez si l’utilisateur peut attribuer l’accès à l’abonnement Azure. Les étapes sont les suivantes :

1. Affecter le rôle RBAC approprié à l’identité de votre application sur l’abonnement.
2. Valider l’affectation de l’accès à la demande d’autorisation de l’Application sur l’abonnement, ou en accédant au Gestionnaire de ressources à l’aide du jeton d’application uniquement.
1. Enregistrer la connexion dans votre structure de données d’applications « abonnements connecté » - conserver l’id de l’abonnement.

Examinons de plus près la première étape. Pour attribuer le rôle RBAC approprié à l’identité de l’application, vous devez déterminer :

- Id d’objet de l’identité de votre application dans Azure de Active Directory l’utilisateur
- L’identificateur du rôle RBAC requis par votre application sur l’abonnement

Lorsque votre application authentifie un utilisateur à partir d’une annonce d’Azure, il crée un objet principal de service pour votre application dans cette annonce Azure. Azure permet les rôles RBAC à affecter aux entités de service à accorder un accès direct aux applications correspondantes sur les ressources d’Azure. Cette action est exactement ce que nous souhaitons. L’API Azure AD graphique de requête afin de déterminer l’identificateur de l’entité de service de votre application dans l’utilisateur connecté 's AD Azure.

Vous avez uniquement un jeton d’accès pour le Gestionnaire de ressources Azure, vous avez besoin d’un nouveau jeton d’accès d’appeler l’API Azure AD graphique. Chaque application dans Azure AD a l’autorisation d’interroger son propre objet de principal du service, un jeton d’accès d’application seule est suffisant.

<a id="app-azure-ad-graph">
### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obtenir le jeton accès application seule pour Azure AD graphique API

Pour authentifier votre application et d’obtenir un jeton à Azure AD graphique API, émettre une demande de jeton de flux OAuth2.0 de subvention d’informations d’identification de Client au point de terminaison AD Azure token (**jeton de https://login.microsoftonline.com/ {directory_domain_name} / OAuth2 /**).

La méthode [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) de l’exemple d’application ASP.net MVC Obtient un accès application seule jeton pour API de graphique à l’aide de la bibliothèque de l’authentification Active Directory pour .NET.

Les paramètres de chaîne de requête qui sont disponibles pour cette demande sont décrites dans la rubrique [demande un jeton d’accès](./active-directory/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) .

Une exemple de demande de jeton de licence d’informations d’identification du client : 

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Une réponse d’exemple pour un jeton de licence d’informations d’identification du client : 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obtenir des ObjectId d’entité de service d’application d’utilisateur AD Azure

Maintenant, utiliser le jeton d’accès d’application uniquement pour interroger les [entités de Service d’Azure AD graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API pour déterminer l’Id d’objet de service de l’application principal dans le répertoire.

La méthode [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) de l’exemple d’application ASP.net MVC implémente cet appel.

L’exemple suivant montre comment demander l’entité de sécurité de service d’une application. a0448380-c346-4f9f-b897-c18733de9394 est l’id de client de l’application.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

L’exemple suivant illustre une réponse à la demande de service de l’application principale 

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obtenir l’identificateur de rôle Azure RBAC

Pour affecter le rôle RBAC approprié à votre service principal, vous devez déterminer l’identificateur du rôle RBAC d’Azure.

Le rôle RBAC droit pour votre application :

- Si votre application analyse uniquement l’abonnement, sans apporter de modifications, il requiert uniquement des autorisations de lecture sur l’abonnement. Affecter le rôle de **lecteur** .
- Si votre application gère l’Azure l’abonnement, la création/modification/suppression d’entités, il nécessite une des autorisations de collaborateur.
  - Pour gérer un type particulier de ressource, attribuer les rôles spécifiques aux ressources collaborateur (contributeur de la Machine virtuelle, les collaborateurs de réseau virtuel, collaborateur de compte de stockage, etc.)
  - Pour gérer tout type de ressource, attribuer le rôle de **contributeur** .

L’affectation de rôle de votre application est visible aux utilisateurs, sélectionnez le privilège minimum requis.

Appelez la [définition de rôle Gestionnaire de ressources API](https://msdn.microsoft.com/library/azure/dn906879.aspx) pour répertorier tous les rôles RBAC d’Azure et recherche puis itérer sur le résultat pour rechercher la définition de rôle souhaité par nom.

La méthode [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) de l’exemple d’application ASP.net MVC implémente cet appel.

L’exemple de requête suivant montre comment obtenir l’identificateur du rôle RBAC d’Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb est l’id de l’abonnement.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

La réponse est dans le format suivant : 

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Vous n’avez pas besoin d’appeler cette API de façon continue. Une fois que vous avez déterminé le GUID bien connu de la définition de rôle, vous pouvez construire le code de définition de rôle en tant que :

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Voici les GUID bien connus des rôles intégrés couramment utilisées :

| Rôle | GUID |
| ----- | ------ |
| Lecteur | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Collaborateur | b24988ac-6180-42a0-ab88-20f7382dd24c
| Collaborateurs de Machine virtuelle | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Collaborateurs de réseau virtuel | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Collaborateurs de compte de stockage | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Collaborateur du site Web | de139f84-1756-47ae-9be6-808fbbe84772
| Contributeur du Plan Web | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| Collaborateurs de SQL Server | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| Contributor de base de données SQL | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### <a name="assign-rbac-role-to-application"></a>Attribuer un rôle RBAC à application

Vous avez tout ce que vous devez attribuer le rôle RBAC approprié à votre service principal à l’aide du [Gestionnaire de ressources créer rôle](https://msdn.microsoft.com/library/azure/dn906887.aspx) API.

La méthode [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) de l’exemple d’application ASP.net MVC implémente cet appel.

Un exemple de demande pour assigner un rôle RBAC à application : 

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Dans la demande, les valeurs suivantes sont utilisées :

| GUID | Description |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | l’id de l’abonnement
| c3097b31-7309-4c59-b4e3-770f8406bad2 | id d’objet de l’entité du service de l’application
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | l’id du rôle Lecteur
| 4f87261d-2816-465d-8311-70a27558df4c | un nouveau guid que celui créé pour la nouvelle attribution de rôle

La réponse est dans le format suivant : 

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obtenir accès app uniquement le jeton pour le Gestionnaire de ressources Azure

Pour valider cette application a souhaité accéder sur l’abonnement, d’effectuer une tâche de test sur l’abonnement à l’aide d’un jeton d’application uniquement.

Pour obtenir un accès application seule jeton, suivez les instructions de la section [obtenir le jeton pour Azure AD graphique API accès uniquement par l’application](#app-azure-ad-graph), avec une valeur différente pour le paramètre de ressource : 

    https://management.core.windows.net/

La méthode [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) de l’exemple d’application ASP.NET MVC Obtient un accès application seule jeton pour le Gestionnaire de ressources Azure pour .net à l’aide de la bibliothèque de l’authentification Active Directory.

#### <a name="get-applications-permissions-on-subscription"></a>Obtenir les autorisations de l’Application lors de l’abonnement

Pour vérifier que votre application dispose de l’accès de votre choix sur un abonnement Azure, vous pouvez également appeler les [Autorisations de gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn906889.aspx) API. Cette approche est similaire à la détermination si l’utilisateur dispose des droits de gestion de l’accès de l’abonnement. Cependant, cette fois, appelez les API d’autorisations avec le jeton d’accès uniquement par l’application que vous avez reçue à l’étape précédente.

La méthode [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) de l’exemple d’application ASP.NET MVC implémente cet appel.

## <a name="manage-connected-subscriptions"></a>Gérer les abonnements connectés

Lorsque le rôle RBAC approprié est affecté au service de votre application principale sur l’abonnement, votre application peut conserver contrôler/gérer à l’aide des jetons d’accès d’application uniquement pour le Gestionnaire de ressources Azure.

Si le propriétaire d’un abonnement Supprime l’affectation de rôle de votre application à l’aide d’un portail classique du ou des outils de ligne de commande, votre application n’est plus en mesure d’accéder à cet abonnement. Dans ce cas, vous devez informer l’utilisateur que la connexion avec l’abonnement a été rompue à partir de l’extérieur de l’application et leur donner une option « Réparer ». « Réparation » serait recréer simplement l’attribution de rôle qui a été supprimée d’en mode hors connexion.

Comme vous avez activé l’utilisateur à connecter les abonnements à votre application, vous devez autoriser l’utilisateur à déconnecter les abonnements trop. À partir d’un point de gestion des accès, de vue déconnecter signifie supprimer l’affectation de rôle principal du service de l’application a sur l’abonnement. Le cas échéant, un état dans l’application de l’abonnement peut également supprimé. Seuls les utilisateurs disposant de l’autorisation de gestion d’accès sur l’abonnement sont en mesure de se déconnecter de l’abonnement.

La [méthode de RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) de l’exemple d’application ASP.net MVC implémente cet appel.

C’est tout : les utilisateurs peuvent désormais se connecter et de gérer facilement leurs abonnements Azure avec votre application.

