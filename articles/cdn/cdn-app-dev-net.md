<properties
    pageTitle="Mise en route avec la bibliothèque de CDN Azure pour .NET | Microsoft Azure"
    description="Apprenez à écrire des applications .NET pour gérer les Azure CDN à l’aide de Visual Studio."
    services="cdn"
    documentationCenter=".net"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="casoper"/>

# <a name="get-started-with-azure-cdn-development"></a>Mise en route avec le développement d’Azure CDN

> [AZURE.SELECTOR]
- [Node.js](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

Vous pouvez utiliser la [Bibliothèque du CDN Azure pour .NET](https://msdn.microsoft.com/library/mt657769.aspx) pour automatiser la création et la gestion des profils du Canada et des points de terminaison.  Ce didacticiel vous guide par le biais de la création d’une application console .NET simple qui illustre plusieurs les opérations disponibles.  Ce didacticiel n’est pas destiné pour décrire tous les aspects de la bibliothèque du CDN Azure pour .NET en détail.

Vous avez besoin de Visual Studio 2015 pour terminer ce didacticiel.  [2015 de communauté de Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) est disponible gratuitement au téléchargement.

> [AZURE.TIP] La [fin d’un projet à partir de ce didacticiel](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) est disponible en téléchargement sur le site MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Créez votre projet et ajouter des packages Nuget

Maintenant que nous avons créé un groupe de ressources pour les profils de nos CDN et compte tenu de notre annonce Azure application l’autorisation de gérer des profils CDN et points de terminaison dans ce groupe, nous pouvons commencer à créer notre application.

Dans Visual Studio 2015, cliquez sur **fichier**, **Nouveau**, **projet...** pour ouvrir la boîte de dialogue Nouveau projet.  Développez **Visual C#**, puis sélectionnez **Windows** dans le volet de gauche.  Dans le volet central, cliquez sur **Application Console** .  Nommez votre projet, puis cliquez sur **OK**.  

![Nouveau projet](./media/cdn-app-dev-net/cdn-new-project.png)

Notre projet va utiliser certaines bibliothèques Azure contenues dans des packages Nuget.  Nous allons ajouter celles au projet.

1. Cliquez sur le menu **Outils** , **Du Gestionnaire de package Nuget**, puis **Console du Gestionnaire de package**.

    ![Gérer les Packages Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. Dans la Console du Gestionnaire de package, exécutez la commande suivante pour installer **Bibliothèque de l’authentification Active Directory (ADAL)**:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. Exécutez la commande suivante pour installer la **Bibliothèque de gestion Azure CDN**:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Directives, des constantes, méthode principale et des méthodes d’assistance

Passons à la structure de base de notre programme écrit.

1. Dans l’onglet Program.cs, remplacer le `using` directives en haut avec les éléments suivants :

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

2. Nous devons définir certaines constantes que nos méthodes utilisera.  Dans le `Program` de classe, mais avant que le `Main` méthode, ajoutez le code suivant.  Veillez à remplacer les espaces réservés, y compris la ** &lt;crochets&gt;**, avec vos propres valeurs le cas échéant.

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```

3. Également au niveau de classe, vous devez définir ces deux variables.  Nous utiliserons ces ultérieurement pour déterminer si notre profil et le point de terminaison existent déjà.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```

4.  Remplacer le `Main` méthode comme suit :

    ```csharp
    static void Main(string[] args)
    {
        //Get a token
        AuthenticationResult authResult = GetAccessToken();

        // Create CDN client
        CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
            { SubscriptionId = subscriptionId };

        ListProfilesAndEndpoints(cdn);

        // Create CDN Profile
        CreateCdnProfile(cdn);

        // Create CDN Endpoint
        CreateCdnEndpoint(cdn);
        
        Console.WriteLine();

        // Purge CDN Endpoint
        PromptPurgeCdnEndpoint(cdn);

        // Delete CDN Endpoint
        PromptDeleteCdnEndpoint(cdn);

        // Delete CDN Profile
        PromptDeleteCdnProfile(cdn);

        Console.WriteLine("Press Enter to end program.");
        Console.ReadLine();
    }
    ```

5. Certaines de nos autres méthodes vont demander à l’utilisateur des questions de « Oui/non ».  Ajoutez la méthode suivante pour faire un peu plus facile :

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Maintenant que la structure de base de notre programme est écrit, nous devons créer les méthodes appelées par le `Main` méthode.

## <a name="authentication"></a>Authentification

Avant de pouvoir utiliser la bibliothèque de gestion Azure CDN, il nous faut authentifier notre principal du service et obtenir un jeton d’authentification.  Cette méthode utilise ADAL pour récupérer le jeton.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Si vous utilisez l’authentification de l’utilisateur individuel, le `GetAccessToken` méthode aura un aspect légèrement différent.

>[AZURE.IMPORTANT] Utiliser uniquement le code si vous préférez que l’authentification d’utilisateur individuel au lieu d’un service principal.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Veillez à remplacer `<redirect URI>` avec la redirection URI que vous avez entré lorsque vous avez enregistré l’application dans Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Liste des profils CDN et points de terminaison

Nous sommes maintenant prêts à effectuer des opérations de CDN.  La première chose que notre méthode est la liste de tous les profils et les points de terminaison dans notre groupe de ressources et s’il trouve une correspondance pour les noms de profil et de point de terminaison spécifié dans nos constantes, établit que pour plus tard pour nous n’essayez pas de créer des doublons.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Créer des points de terminaison et les profils CDN

Ensuite, nous allons créer un profil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Une fois le profil créé, nous allons créer un point de terminaison.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

>[AZURE.NOTE] L’exemple ci-dessus assigne le point de terminaison une origine nommée *Contoso* avec un nom d’hôte `www.contoso.com`.  Vous devez modifier pour pointer vers le nom d’hôte de votre propre initiale.

## <a name="purge-an-endpoint"></a>Purge d’un point de terminaison

En supposant que le point de terminaison a été créé, une tâche courante que nous pouvons effectuer dans notre programme de purge le contenu de notre point de terminaison.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

>[AZURE.NOTE] Dans l’exemple ci-dessus, la chaîne `/*` indique que je souhaite effacer tout le contenu de la racine du chemin d’accès du point de terminaison.  Cela équivaut à la vérification de **Purger tous** dans la boîte de dialogue « Purger » du portail Azure. Dans le `CreateCdnProfile` méthode, j’ai créé notre profil comme profil **Azure CDN de Verizon** en utilisant le code `Sku = new Sku(SkuName.StandardVerizon)`, de sorte que cela ne sera pas réussie.  Toutefois, profils **d’Azure CDN à partir d’Akamai** ne gèrent pas **Purger tous les**, donc si j’utilisais un profil Akamai pour ce didacticiel, je devrais inclure des chemins spécifiques à purger.

## <a name="delete-cdn-profiles-and-endpoints"></a>Supprimer les points de terminaison et les profils CDN

Les dernières méthodes supprimera notre point de terminaison et le profil.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Le programme en cours d’exécution

Nous pouvons maintenant compiler et exécuter le programme en cliquant sur le bouton **Démarrer** dans Visual Studio.

![Programme en cours d’exécution](./media/cdn-app-dev-net/cdn-program-running-1.png)

Lorsque le programme atteint la ligne ci-dessus, il se peut que vous devez être en mesure de revenir à votre groupe de ressources dans le portail Azure et que le profil a été créé.

![Succès !](./media/cdn-app-dev-net/cdn-success.png)

Nous pouvons ensuite confirmer les invites pour exécuter le reste du programme.

![Fin du programme](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Étapes suivantes

Pour voir le projet réalisé à partir de cette procédure pas à pas, [Téléchargez l’exemple](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Pour obtenir une documentation supplémentaire sur la bibliothèque de gestion Azure CDN pour .NET, permet d’afficher la [référence sur MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

Gérer vos ressources CDN avec [PowerShell](./cdn-manage-powershell.md).


