<properties
    pageTitle="B2C d’Azure Active Directory : Utiliser l’API de graphique | Microsoft Azure"
    description="L’appel de l’API de graphique pour un locataire B2C pour automatiser le processus à l’aide d’une identité d’application."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-use-the-graph-api"></a>Azure AD B2C : Utiliser l’API de graphique

Locataires de Active Directory (AD Azure) B2C Azure ont tendance à être très volumineux. Cela signifie que de nombreuses tâches courantes de gestion des clients doivent être effectuées par programme. Un exemple principal est la gestion des utilisateurs. Vous devrez migrer un magasin utilisateur existant à un locataire B2C. Vous souhaiterez peut-être héberger sur votre page d’inscription des utilisateurs et créer des comptes d’utilisateur dans AD Azure en tâche de fond. Ces types de tâches nécessitent la possibilité de créer, lire, mettre à jour et supprimer des comptes d’utilisateur. Vous pouvez effectuer ces tâches à l’aide de l’API Azure AD graphique.

Pour les locataires B2C, il existe deux modes de communication avec l’API du graphique.

- Pour les tâches interactives, exécution unique, vous devez agir comme un compte d’administrateur dans le locataire B2C lorsque vous effectuez les tâches. Ce mode nécessite un administrateur pour vous connecter avec les informations d’identification avant qu’admin peut effectuer tous les appels à l’API de graphique.
- Pour les tâches automatisés en continu, vous devez utiliser un type de compte de service que vous fournissez avec les privilèges nécessaires pour effectuer les tâches de gestion. Dans AD Azure, cela par l’inscription d’une application et d’authentification à Active Directory Azure. Cela est fait à l’aide d’un **ID d’Application** qui utilise les [informations d’identification du client OAuth 2.0 accordent](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Dans ce cas, l’application agit comme lui-même, pas en tant qu’utilisateur, d’appeler l’API graphique.

Dans cet article, nous verrons comment effectuer le cas d’utilisation automatisée. Pour illustrer, nous créerons un .NET 4.5 `B2CGraphClient` qui effectue l’utilisateur créer, lire, mettre à jour et supprimer des opérations. Le client dispose d’une interface de ligne de commande (CLI) Windows qui vous permet d’appeler des méthodes différentes. Toutefois, le code est écrit de se comporter de manière non interactive et automatisée.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obtenir un locataire Azure AD B2C

Avant de pouvoir créer des applications ou des utilisateurs, ou interagir avec Active Directory Azure tout, vous devrez un locataire Azure AD B2C et un compte d’administrateur global dans le locataire. Si vous n’avez pas un locataire déjà, [mise en route avec Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-a-service-application-in-your-tenant"></a>Enregistrer une application de service de vos clients

Une fois que vous avez un locataire B2C, vous avez besoin créer votre application de service à l’aide des applets de commande PowerShell de publicité Azure.
Tout d’abord, téléchargez et installez [l’Assistant Microsoft Online Services Sign-In](http://go.microsoft.com/fwlink/?LinkID=286152). Puis téléchargez et installez le [module d’Azure Active Directory de 64 bits de Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.IMPORTANT]
Pour utiliser l’API graphique avec vos clients B2C, vous devrez inscrire une application dédiée à l’aide de PowerShell. Suivez les instructions de cet article à le faire. Vous ne pouvez pas réutiliser les applications B2C existante que vous avez enregistré dans le portail Azure.

Après avoir installé le module PowerShell, ouvrez PowerShell et connectez-vous à vos clients B2C. Après avoir exécuté `Get-Credential`, vous serez invité pour un nom d’utilisateur et le mot de passe, entrez le nom d’utilisateur et le mot de passe de votre compte d’administrateur de clients B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Avant de créer votre application, vous devez générer un nouveau **secret de client**.  Votre application utilisera le secret du client pour s’authentifier sur Active Directory Azure et d’acquérir des jetons d’accès. Vous pouvez générer un secret valide dans PowerShell :

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

La commande finale doit imprimer votre nouveau secret de client. Le copier dans un endroit sûr. Vous en aurez besoin ultérieurement. Vous pouvez désormais créer votre application en fournissant le nouveau secret de client comme une information d’identification pour l’application :

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Si vous créez avec succès l’application, elle doit imprimer les propriétés de l’application, comme celles représentées ci-dessus. Vous devrez fournir les `ObjectId` et `AppPrincipalId`, donc copier ces valeurs, trop.

Après avoir créé une application dans vos clients B2C, vous devez affecter les autorisations dont il a besoin pour effectuer des opérations de l’utilisateur. Affecter les rôles de trois applications : lecteurs de répertoire (pour les utilisateurs de lire), les auteurs de répertoire (pour créer et mettre à jour des utilisateurs) et un administrateur de compte utilisateur (pour supprimer des utilisateurs). Ces rôles ont des identificateurs connus, vous pouvez remplacer le `-RoleMemberObjectId` paramètre avec `ObjectId` à partir du haut et exécutez les commandes suivantes. Pour afficher la liste de tous les rôles de répertoire, exécutez `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Vous avez une application qui a l’autorisation de créer, lire, mettre à jour et supprimez des utilisateurs de vos clients B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Télécharger, configurer et générer l’exemple de code

Tout d’abord, téléchargez l’exemple de code et de pouvoir l’exécuter. Nous prendrons puis examinez de plus près il.  Vous pouvez [Télécharger l’exemple de code dans un fichier .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Vous pouvez également cloner dans un répertoire de votre choix :

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Ouvrir le `B2CGraphClient\B2CGraphClient.sln` solution Visual Studio dans Visual Studio. Dans la `B2CGraphClient` du projet, ouvrez le fichier `App.config`. Remplacer les paramètres d’application de trois par vos propres valeurs :

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ensuite, avec le bouton droit sur le `B2CGraphClient` solution et la reconstruction de l’échantillon. Si vous réussissez, vous devez maintenant avoir un `B2C.exe` fichier exécutable se trouve dans `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Créer des opérations de l’utilisateur à l’aide de l’API de graphique

Pour utiliser le B2CGraphClient, ouvrez une `cmd` de commande Windows invite et modifier votre répertoire à la `Debug` répertoire. Puis exécutez le `B2C Help` commande.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Cela affiche une brève description de chaque commande. Chaque fois que vous appelez une de ces commandes, `B2CGraphClient` fait une demande à l’API Azure AD graphique.

### <a name="get-an-access-token"></a>Obtenir un jeton d’accès

Toute demande à l’API Graph requiert un jeton d’accès pour l’authentification. `B2CGraphClient`utilise l’open source Active Directory d’authentification Library (ADAL) pour acquérir des jetons d’accès. ADAL facilite l’acquisition de jeton en fournissant une API simple et en prenant soin de certains détails importants, tels que la mise en cache des jetons d’accès. Vous n’êtes pas obligé d’utiliser ADAL pour obtenir les jetons, cependant. Vous pouvez également obtenir des jetons en créant des requêtes HTTP.

> [AZURE.NOTE]
    Cet exemple de code utilise des ADAL v2 afin de communiquer avec l’API du graphique.  Vous devez utiliser ADAL v2 ou v3 afin d’obtenir les jetons d’accès qui peuvent être utilisés avec l’API Azure AD graphique.

Lors de la `B2CGraphClient` s’exécute, il crée une instance de le `B2CGraphClient` classe. Le constructeur de cette classe définit une structure de ADAL de l’authentification :

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Nous allons utiliser le `B2C Get-User` commande par exemple. Lors de la `B2C Get-User` est appelé sans les entrées supplémentaires, les appels de l’infrastructure du langage commun le `B2CGraphClient.GetAllUsers(...)` méthode. Cette méthode appelle `B2CGraphClient.SendGraphGetRequest(...)`, qui envoie une requête HTTP GET à l’API de graphique. Avant `B2CGraphClient.SendGraphGetRequest(...)` envoie demande de GET, il obtient d’abord un accès à un jeton à l’aide de ADAL :

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Vous pouvez obtenir un accès à un jeton pour l’API graphique en appelant le ADAL `AuthenticationContext.AcquireToken(...)` méthode. ADAL renvoie un `access_token` qui représente l’identité de l’application.

### <a name="read-users"></a>Lecture les utilisateurs

Lorsque vous souhaitez obtenir la liste des utilisateurs de l’API de graphique ou un utilisateur particulier, vous pouvez envoyer HTTP `GET` demande à la `/users` point de terminaison. Une demande pour tous les utilisateurs dans un locataire ressemble à ceci :

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour voir cette demande, exécutez :

 ```
 > B2C Get-User
 ```

Il existe deux points importants à noter :

- Le jeton d’accès acquis via ADAL est ajouté à la `Authorization` en-tête à l’aide de la `Bearer` schéma.
- Pour les locataires B2C, vous devez utiliser le paramètre de requête `api-version=1.6`.

Deux de ces détails sont gérés dans le `B2CGraphClient.SendGraphGetRequest(...)` méthode :

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Créer des comptes d’utilisateur de consommateur

Lorsque vous créez des comptes d’utilisateurs dans votre client B2C, vous pouvez envoyer HTTP `POST` demande à la `/users` point de terminaison :

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",              // a value that can be used for displaying to the end user
    "mailNickname": "joec",                     // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

La plupart de ces propriétés dans cette demande est requis pour créer des utilisateurs de consommateur. Pour en savoir plus, cliquez [ici](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Notez que la `//` commentaires ont été inclus à titre d’illustration. N’incluez pas les dans une demande réelle.

Pour visualiser la demande, exécutez une des commandes suivantes :

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

Le `Create-User` commande prend un fichier de .json en tant que paramètre d’entrée. Cet onglet contient une représentation JSON d’un objet utilisateur. Il existe deux exemples de fichiers .json dans l’exemple de code : `usertemplate-email.json` et `usertemplate-username.json`. Vous pouvez modifier ces fichiers en fonction de vos besoins. Outre les champs obligatoires ci-dessus, plusieurs champs facultatifs que vous pouvez utiliser sont inclus dans ces fichiers. Vous trouverez plus d’informations sur les champs facultatifs dans la [référence d’entité API d’Azure AD graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Vous pouvez voir comment la demande POST est construite dans `B2CGraphClient.SendGraphPostRequest(...)`.

- Il s’attache à un jeton d’accès à la `Authorization` en-tête de la demande.
- Il définit `api-version=1.6`.
- Il inclut l’objet utilisateur JSON dans le corps de la demande.

> [AZURE.NOTE]
Si les comptes que vous voulez migrer à partir d’un magasin d’utilisateur existant a force de mot de passe moindre que la [résistance de mot de passe fort appliquée par Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), vous pouvez désactiver l’exigence de mot de passe fort à l’aide de la `DisableStrongPassword` valeur dans le `passwordPolicies` propriété. Par exemple, vous pouvez modifier la demande de l’utilisateur créer fournie ci-dessus comme suit : `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Mettre à jour les comptes d’utilisateurs de consommateur

Lorsque vous mettez à jour les objets utilisateur, le processus est similaire à celui que vous utilisez pour créer des objets utilisateur. Mais ce processus utilise le protocole HTTP `PATCH` méthode :

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",              // this request updates only the user's displayName
}
```

Essayez de mettre à jour un utilisateur en mettant à jour vos fichiers JSON avec de nouvelles données. Vous pouvez ensuite utiliser `B2CGraphClient` pour exécuter une des commandes suivantes :

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Vérifiez que le `B2CGraphClient.SendGraphPatchRequest(...)` méthode pour plus d’informations sur l’envoi de cette demande.

### <a name="search-users"></a>Rechercher des utilisateurs

Vous pouvez rechercher des utilisateurs dans vos clients B2C de deux façons. Un, à l’aide de l’utilisateur d’objet ID ou les deux, à l’aide d’identificateur de connexion de l’utilisateur (par exemple, le `signInNames` propriété).

Exécutez une des commandes suivantes pour rechercher un utilisateur spécifique :

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Voici quelques exemples :

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Supprimer des utilisateurs

Le processus de suppression d’un utilisateur est simple. Utilisez le protocole HTTP `DELETE` méthode et construction de l’URL avec le bon ID de l’objet :

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour voir un exemple, saisissez cette commande et d’afficher la demande de suppression est imprimée dans la console :

```
> B2C Delete-User <object-id-of-user>
```

Vérifiez que le `B2CGraphClient.SendGraphDeleteRequest(...)` méthode pour plus d’informations sur l’envoi de cette demande.

Vous pouvez effectuer de nombreuses autres actions avec l’API Azure AD graphique en plus de la gestion des utilisateurs. La [référence de l’API d’Azure AD graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fournit des détails sur chaque action, ainsi que les demandes d’exemples.

## <a name="use-custom-attributes"></a>Utiliser des attributs personnalisés

La plupart des applications consommateur est nécessaire stocker un type d’informations de profil utilisateur personnalisé. Une pour ce faire consiste à définir un attribut personnalisé dans vos clients B2C. Vous pouvez ensuite traiter cet attribut comme vous traitez n’importe quelle autre propriété sur un objet utilisateur. Vous pouvez mettre à jour l’attribut, supprimez l’attribut, par l’attribut de requête, envoyer l’attribut sous forme de revendication dans les jetons de connexion et bien plus encore.

Pour définir un attribut personnalisé dans vos clients B2C, consultez la [référence de l’attribut personnalisé B2C](active-directory-b2c-reference-custom-attr.md).

Vous pouvez afficher les attributs personnalisés définis dans vos clients B2C en utilisant `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

La sortie de ces fonctions révèle les détails de chaque attribut personnalisé, tel que :

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Vous pouvez utiliser le nom complet, tel que `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, en tant que propriété sur vos objets utilisateur.  Mettre à jour votre fichier .json avec la nouvelle propriété et une valeur pour la propriété, puis exécutez :

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

À l’aide de `B2CGraphClient`, vous disposez d’une application de service capable de gérer les utilisateurs de clients B2C par programme. `B2CGraphClient`utilise sa propre identité de l’application pour authentifier à l’API Azure AD graphique. Il acquiert également les jetons à l’aide d’un secret de client. Lors de l’incorporation de cette fonctionnalité dans votre application, gardez à l’esprit quelques points importants pour des applications B2C :

- Vous devez accorder les autorisations appropriées dans le locataire à l’application.
- Pour l’instant, vous devez utiliser ADAL v2 pour obtenir les jetons d’accès. (Vous pouvez également envoyer des messages de protocole directement, sans utilisation d’une bibliothèque.)
- Lorsque vous appelez l’API graphique, utilisez `api-version=1.6`.
- Lorsque vous créez et de mettre à jour les utilisateurs grand public, certaines propriétés sont nécessaires, comme décrit ci-dessus.

Si vous avez des questions ou des demandes pour des actions que vous souhaitez effectuer à l’aide de l’API de graphique sur vos clients B2C, laissez un commentaire sur cet article ou un problème de fichier dans le référentiel d’exemples de code GitHub.
