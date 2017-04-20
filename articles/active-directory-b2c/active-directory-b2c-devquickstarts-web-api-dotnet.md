<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Comment créer une application web qui appelle une API web à l’aide d’Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>Azure AD B2C : Appeler un API web à partir d’une application web de .NET

À l’aide de B2C d’Azure Active Directory (AD Azure), vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissant à vos applications web et les web API dans quelques étapes. Cet article traite de la création d’une application web .NET Model-View-Controller (MVC) « liste de tâches » qui appelle une API web en utilisant des jetons de porteur

Cet article ne couvre pas la mise en œuvre d’ouverture de session, d’inscription et gestion des profils avec Azure AD B2C. Il se concentre sur web appel API une fois que l’utilisateur est déjà authentifié. Si vous ne l’avez pas déjà, vous devez démarrer avec le [.NET web app en route](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les bases d’Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire, ou client.  Un répertoire est un conteneur pour tous les utilisateurs, applications, groupes et bien plus encore.  Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de poursuivre dans ce guide.

## <a name="create-an-application"></a>Création d’une application

Ensuite, vous devez créer une application dans votre répertoire B2C. Ainsi, Azure informations dont il a besoin pour communiquer en toute sécurité avec votre application. Dans ce cas, l’application web et le web API seront représenté par un seul **ID de l’Application**, parce qu’elles comprennent une application logique. Pour créer une application, suivez [les instructions](active-directory-b2c-app-registration.md). Veillez à :

- Inclure un **site web app API** dans l’application.
- Entrez `https://localhost:44316/` sous la forme d’une **URL de la réponse**. Il s’agit de l’URL par défaut pour cet exemple de code.
- Copiez l' **ID de l’Application** qui est assigné à votre application. Vous devez également cela plus tard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure B2C d’Active Directory, chaque expérience de l’utilisateur est défini par une [stratégie](active-directory-b2c-reference-policies.md). Cette application web contient trois expériences d’identité : vous inscrire, vous connecter et modifier le profil. Vous devez créer une stratégie de chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lorsque vous créez les trois stratégies, veillez à :

- Cliquez sur le **nom d’affichage** et autres attributs d’abonnement dans votre stratégie d’inscription.
- Choisissez les demandes d’application de **nom complet** et **l’ID d’objet** dans chaque stratégie. Vous pouvez choisir les autres déclarations.
- Copier le **nom** de chaque stratégie après que l’avoir créé. Il doit avoir le préfixe `b2c_1_`. Vous devez ensuite ces noms de la stratégie.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Après avoir créé vos stratégies de trois, vous êtes prêt à créer votre application.

Notez que cet article ne couvre pas l’utilisation de stratégies que vous venez de créer. Pour en savoir plus sur le fonctionnement des stratégies dans Azure AD B2C, démarrer avec le [.NET web app en route](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Télécharger le code

Le code de ce didacticiel [est conservée sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Pour générer l’exemple à mesure que vous progressez, vous pouvez [Télécharger le squelette de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). Vous pouvez également cloner le squelette :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

L’application terminée est également [disponible sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) ou sur le `complete` branche du même référentiel.

Après avoir téléchargé l’exemple de code, ouvrez le fichier .sln Visual Studio pour commencer.

## <a name="configure-the-task-web-app"></a>Configurer l’application web de tâche

Pour obtenir des `TaskWebApp` pour communiquer avec Azure AD B2C, vous devez fournir quelques paramètres communs. Dans le `TaskWebApp` ouvert le projet, le `web.config` de fichiers dans la racine du projet et de remplacer les valeurs dans le `<appSettings>` section. Vous pouvez laisser le `AadInstance`, `RedirectUri`, et `TaskServiceUrl` valeurs comme ils le sont.

```
  <appSettings>
    
    ...
    
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>Obtenir des jetons d’accès et d’appeler le API de la tâche

Cette section traite de l’utilisation du jeton reçu lors de l’authentification avec Azure AD B2C afin d’accéder à un site web API est également sécurisé avec Azure AD B2C.

Cet article ne couvre pas les détails de la sécurisation de l’API. Pour savoir comment une API web authentifie en toute sécurité les demandes à l’aide d’Azure AD B2C, découvrez l' [API web mise en route l’article](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="save-the-sign-in-token"></a>Enregistrer le signe dans le jeton

Tout d’abord, authentification de l’utilisateur (à l’aide de l’une de vos stratégies) et recevez un jeton de connexion Azure AD B2C.  Si vous ne savez pas comment exécuter les politiques, revenez en arrière et essayez du [.NET web app en route](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les bases d’Azure AD B2C.

Ouvrez le fichier `App_Start\Startup.Auth.cs`.  Il existe une modification importante, vous devez apporter à la `OpenIdConnectAuthenticationOptions` -vous devez définir `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

return new OpenIdConnectAuthenticationOptions
{
    // For each policy, give OWIN the policy-specific metadata address, and
    // set the authentication type to the id of the policy
    MetadataAddress = String.Format(aadInstance, tenant, policy),
    AuthenticationType = policy,

    // These are standard OpenID Connect parameters, with values pulled from web.config
    ClientId = clientId,
    RedirectUri = redirectUri,
    PostLogoutRedirectUri = redirectUri,
    Notifications = new OpenIdConnectAuthenticationNotifications
    {
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",
        
        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>Obtenir un jeton dans les contrôleurs

Le `TasksController` est responsable de la communication avec le site web API, en envoyant des requêtes HTTP à l’API pour lire, créer et supprimer des tâches.  Étant que l’API est sécurisé par Azure AD B2C, vous devez d’abord récupérer le jeton que vous avez enregistré à l’étape ci-dessus.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;
        
    ...
}
```

Le `BootstrapContext` contient le signe dans le jeton que vous avez acheté par l’exécution d’une de vos stratégies B2C.

### <a name="read-tasks-from-the-web-api"></a>Lire des tâches à partir de l’API web

Lorsque vous disposez d’un jeton, vous pouvez les joindre à HTTP `GET` la demande dans le `Authorization` en-tête d’appeler en toute sécurité `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Créer et supprimer des tâches sur le web API

Suivent le même modèle lorsque vous envoyez des `POST` et `DELETE` requêtes sur le web, API, à l’aide de la `BootstrapContext` pour récupérer le jeton de connexion. Nous avons implémenté l’action Créer pour vous. Vous pouvez essayer de terminer l’action Supprimer dans `TasksController.cs`.

## <a name="run-the-sample-app"></a>Exécutez l’exemple d’application

Enfin, générez et exécutez l’application. Vous inscrire et vous connecter et créer des tâches pour l’utilisateur connecté. Déconnectez-vous, puis reconnectez-vous en tant qu’autre utilisateur. Créer des tâches pour cet utilisateur. Notez que les tâches sont stockées par utilisateur sur le API, car l’API extrait l’identité de l’utilisateur à partir du jeton qu’il reçoit.

Pour référence, l’exemple complet [est fourni sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Vous pouvez également cloner GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
