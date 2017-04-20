<properties
    pageTitle="Comment travailler avec le serveur principal de .NET SDK pour les applications mobiles | Service d’application Azure"
    description="Apprenez à travailler avec le serveur principal de .NET SDK pour les applications Azure Application Service Mobile."
    keywords="service d’application, service d’application azure, application mobile, service mobile, échelle, évolutive, application, azure application déploiement"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Travailler avec le serveur principal de .NET SDK pour les applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Cette rubrique vous indique comment utiliser le serveur principal de .NET SDK dans les scénarios d’Azure Application Service Mobile applications clés. Le Kit de développement logiciel applications Azure Mobile vous permet de travailler avec des clients mobiles à partir de votre application ASP.NET.

>[AZURE.TIP] Le [serveur de .NET SDK pour les applications mobiles Azure] [ 2] est open source sur GitHub. Le référentiel contient tout le code source, y compris la suite de tests unitaires SDK serveur entier et certains exemples de projets.

## <a name="reference-documentation"></a>Documentation de référence

La documentation de référence du Kit de développement logiciel du serveur se trouve ici : [Référence de .NET applications Azure Mobile][1].

## <a name="create-app"></a>Comment : créer un back-end de l’application Mobile de .NET

Si vous démarrez un nouveau projet, vous pouvez créer une application de Service de l’application via le [portail Azure] ou Visual Studio. Vous pouvez exécuter l’application de Service de l’application localement ou publier le projet à votre application mobile application Service nuage.  

Si vous ajoutez des fonctionnalités mobiles à un projet existant, reportez-vous à la section [téléchargement et initialiser le Kit de développement logiciel](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Créer un back-end .NET via le portail Azure

Pour créer un serveur principal de Service d’application mobile, soit suivre le [didacticiel de démarrage rapide] de la[ 3] ou procédez comme suit :

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Dans la lame de _mise en route_ , sous **créer une API de table**, choisissez **C#** comme **langage de back-end**. Cliquez sur **Télécharger**, extraire les fichiers de projet compressés sur votre ordinateur local et ouvrez la solution dans Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2013-and-visual-studio-2015"></a>Créer un serveur principal de .NET à l’aide de Visual Studio 2013 et 2015 de Visual Studio

Installer le [Kit de développement logiciel Azure pour .NET] [ 4] (version 2.9.0 ou version ultérieure) pour créer un projet d’applications Mobile de Azure dans Visual Studio. Une fois que vous avez installé le Kit de développement, créez une application ASP.NET à l’aide des étapes suivantes :

1. Ouvrez la boîte de dialogue **Nouveau projet** (à partir du *fichier* > **Nouveau** > **projet...**).
2. **Modèles de** > **Visual C#**, sélectionnez et **Web**.
3. Sélectionnez **l’Application Web d’ASP.NET**.
4. Renseignez le nom du projet. Puis cliquez sur **OK**.
5. Sous _ASP.NET 4.5.2 modèles_, sélectionnez **Azure Mobile App**. Vérifiez l' **hôte dans le nuage** pour créer un serveur principal mobile dans le nuage sur lequel vous pouvez publier ce projet.
6. Cliquez sur **OK**.

## <a name="install-sdk"></a>Comment : télécharger et initialiser le SDK

Le Kit de développement est disponible sur [NuGet.org]. Ce package inclut les fonctionnalités de base requises pour commencer à utiliser le Kit de développement. Pour initialiser le SDK, vous devez exécuter des actions sur l’objet **HttpConfiguration** .

### <a name="install-the-sdk"></a>Installer le Kit de développement

Pour installer le Kit de développement, avec le bouton droit sur le projet serveur dans Visual Studio, sélectionnez **Manage NuGet Packages**, rechercher le package [Microsoft.Azure.Mobile.Server] , puis cliquez sur **installer**.

###<a name="server-project-setup"></a>Initialiser le projet serveur

Un projet de serveur backend .NET est initialisé similaire à d’autres projets ASP.NET, en incluant une classe de démarrage OWIN. Assurez-vous que vous avez référencé le package NuGet `Microsoft.Owin.Host.SystemWeb`. Pour ajouter cette classe dans Visual Studio, avec le bouton droit sur votre projet de serveur et sélectionnez **Ajouter** > 
**Un nouvel élément**, puis sur **Web** > **générale** > **classe de démarrage de OWIN**.  Une classe est générée avec l’attribut suivant :

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Dans le `Configuration()` méthode de votre classe de démarrage OWIN, utilisez un objet **HttpConfiguration** pour configurer l’environnement d’applications de Mobile Azure.
L’exemple suivant initialise le projet serveur avec aucune fonctionnalités ajoutées :

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Pour activer des fonctions individuelles, vous devez appeler les méthodes d’extension sur l’objet **MobileAppConfiguration** avant **ApplyTo**. Par exemple, le code suivant ajoute les itinéraires par défaut sur tous les contrôleurs d’API qui ont l’attribut `[MobileAppController]` lors de l’initialisation :

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Le démarrage rapide du serveur à partir du portail Azure appelle **UseDefaultConfiguration()**. Cet équivalent à la configuration suivante :

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Les méthodes d’extension utilisées sont les suivantes :

* `AddMobileAppHomeController()`Fournit la page d’accueil par défaut les applications Azure Mobile.
* `MapApiControllers()`Fournit des fonctions API personnalisées pour des contrôleurs de WebAPI décorée avec le `[MobileAppController]` attribut.
* `AddTables()`fournit un mappage de le `/tables` contrôleurs de la table des points de terminaison.
* `AddTablesWithEntityFramework()`est un court disponible pour le mappage de la `/tables` contrôleurs basés sur des points de terminaison à l’aide d’Entity Framework.
* `AddPushNotifications()`Fournit une méthode simple d’inscription de périphériques pour les concentrateurs de la Notification.
* `MapLegacyCrossDomainController()`Fournit des en-têtes CORS standard de développement local.

### <a name="sdk-extensions"></a>Extensions du Kit de développement logiciel

Les modules d’extension NuGet suivants offrent diverses fonctionnalités mobiles qui peuvent être utilisées par votre application. Vous activez les extensions lors de l’initialisation à l’aide de l’objet **MobileAppConfiguration** .

- [Microsoft.Azure.Mobile.Server.Quickstart] prend en charge l’installation d’applications Mobile base. Ajouté à la configuration en appelant la méthode d’extension **UseDefaultConfiguration** pendant l’initialisation. Cette extension inclut suivant les extensions : Notifications, l’authentification, entité, Tables, entre les domaines et les packages d’accueil. Ce package est utilisé par les applications Mobile Quickstart disponibles sur le portail Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) 
   implémente la valeur par défaut *cette application mobile est en cours d’exécution page* pour la racine du site web. Ajouter à la configuration en appelant la méthode d’extension   **AddMobileAppHomeController** .

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) 
   inclut des classes pour la manipulation des données et le pipeline de données de configuration. Ajouter à la configuration en appelant la méthode d’extension **AddTables** .

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) 
   permet à Entity Framework pour accéder aux données dans la base de données SQL. Ajouter à la configuration en appelant la méthode d’extension **AddTablesWithEntityFramework** .

- [Microsoft.Azure.Mobile.Server.Authentication] Active l’authentification et le middleware OWIN utilisée pour valider des jetons de configuration. Ajouter à la configuration en appelant **AddAppServiceAuthentication**  
   et **IAppBuilder**. Méthodes d’extension **UseAppServiceAuthentication** .

- [Microsoft.Azure.Mobile.Server.Notifications] Active les notifications de transmission et définit un point de terminaison de l’inscription de push. Ajouter à la configuration en appelant la méthode d’extension **AddPushNotifications** .

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) 
   crée un contrôleur qui se sert des données dans les navigateurs web existants à partir de votre application Mobile. Ajouter à la configuration en appelant la méthode d’extension   **MapLegacyCrossDomainController** .

- [Microsoft.Azure.Mobile.Server.Login] fournit la méthode AppServiceLoginHandler.CreateToken(), qui est une méthode statique est utilisée au cours de scénarios d’authentification personnalisée.   

## <a name="publish-server-project"></a>Comment : publier le projet serveur

Cette section vous indique comment publier votre projet principal de .NET à partir de Visual Studio. Vous pouvez également déployer votre projet de serveur principal à l’aide de Git ou une des autres méthodes décrites dans la [documentation de déploiement de Service d’application Azure](../app-service-web/web-sites-deploy.md).

1. Dans Visual Studio, régénérez le projet pour restaurer les packages NuGet.

2. Dans l’Explorateur de solutions, cliquez sur le projet, cliquez sur **Publier**. La première fois que vous publiez, vous devez définir un profil de publication. Lorsque vous possédez déjà un profil défini, vous pouvez le sélectionner et cliquez sur **Publier**.

2. Si vous êtes invité à sélectionner une cible de publication, cliquez sur **Service d’application Microsoft Azure** > **suivant**, puis (si nécessaire) signe à l’aide de vos informations d’identification Azure. 
   Visual Studio télécharge et les stocke en toute sécurité vos paramètres directement à partir d’Azure de publication.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Choisissez votre **abonnement**, sélectionnez le **Type de ressource** à partir de la **vue**, développez l' **Application Mobile**, cliquez sur principal de votre application Mobile, puis cliquez sur **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Vérifiez les informations de profil de publication, puis cliquez sur **Publier**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Lorsque la principale application Mobile a publié avec succès, vous voyez une page de renvoi indiquant le succès.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

##<a name="define-table-controller"></a>Comment : définir un contrôleur de tableau

Définissez un contrôleur de tableau pour exposer une table SQL pour les clients mobiles.  Configuration d’un contrôleur de tableau requiert trois étapes :

1. Créez une classe d’objet de transfert de données (DTO).
2. Configurez une référence de table dans la classe Mobile DbContext.
3. Créez un contrôleur de tableau.

Un objet de transfert de données (DTO) est un objet C# simple qui hérite de `EntityData`.  Par exemple :

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

Le DTO est utilisé pour définir la table dans la base de données SQL.  Pour créer l’entrée de la base de données, ajouter une `DbSet<>` propriété le DbContext que vous utilisez.  Dans le modèle de projet par défaut pour les applications mobiles Azure le DbContext est appelé `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Si vous avez installé le kit SDK Azure, vous pouvez maintenant créer un contrôleur de la table modèle comme suit :

1. Avec le bouton droit sur le dossier Controllers et sélectionnez **Ajouter** > **contrôleur...**.
2. Sélectionnez l’option **Contrôleur de Table applications Azure Mobile** , puis cliquez sur **Ajouter**.
3. Dans la boîte de dialogue **Ajouter un contrôleur** :
    * Dans la liste déroulante **classe de modèle** , sélectionnez votre nouvelle DTO.
    * Dans la liste déroulante **DbContext** , sélectionnez la classe DbContext du Service Mobile.
    * Le nom du contrôleur est créé pour vous.
4. Cliquez sur **Ajouter**.

Le projet de serveur de démarrage rapide contient un exemple d’une simple **TodoItemController**.

### <a name="how-to-adjust-the-table-paging-size"></a>Comment : ajuster la taille de pagination de table

Par défaut, les applications Azure Mobile retourne 50 enregistrements par demande.  Pagination garantit que le client n’occupe pas leur thread de l’interface utilisateur ni le serveur trop longtemps, garantissant ainsi une utilisation efficace. Pour modifier la taille de pagination table, augmenter le côté serveur « requête autorisé » et la côté client taille de page côté serveur « requête autorisée » est ajustée à l’aide de la `EnableQuery` attribut :

    [EnableQuery(PageSize = 500)]

Assurez-vous que la valeur PageSize est la même ou supérieure à la taille demandée par le client.  Reportez-vous à la documentation de faire pour plus d’informations sur la modification de la taille de la page client de client spécifique.

## <a name="how-to-define-a-custom-api-controller"></a>Comment : définir un contrôleur API personnalisé

Le contrôleur API personnalisé fournit les fonctionnalités de base à votre back-end de l’application Mobile en exposant un point de terminaison. Vous pouvez enregistrer un contrôleur d’API spécifiques à mobile à l’aide de l’attribut [MobileAppController]. Le `MobileAppController` attribut enregistre l’itinéraire, configure le sérialiseur JSON d’applications Mobile et Active la [vérification de version de client](app-service-mobile-client-and-server-versioning.md).

1. Dans Visual Studio, cliquez sur le dossier Controllers, puis cliquez sur **Ajouter** > **contrôleur**, sélectionnez **Web API 2 contrôleur&mdash;vide** et cliquez sur **Ajouter**.

2. Fournir un **nom de contrôleur**, tel que `CustomController`, puis cliquez sur **Ajouter**.

3. Dans le nouveau fichier de classe de contrôleur, ajoutez le code suivant à l’aide d’instruction :

        using Microsoft.Azure.Mobile.Server.Config;

4. Appliquer l’attribut **[MobileAppController]** pour la définition de classe de contrôleur API, comme dans l’exemple suivant :

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }

4. Dans le fichier de App_Start/Startup.MobileApp.cs, ajoutez un appel à la méthode d’extension **MapApiControllers** , comme dans l’exemple suivant :

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Vous pouvez également utiliser le `UseDefaultConfiguration()` méthode d’extension au lieu de `MapApiControllers()`. N’importe quel contrôleur qui n’est pas appliqué le **MobileAppControllerAttribute** sera toujours accessible par les clients, mais il ne peut pas être correctement utilisé par les clients à l’aide de n’importe quel client Mobile application SDK.

## <a name="how-to-work-with-authentication"></a>Comment : utiliser l’authentification

Les applications Mobile Azure utilise App, Service d’authentification / autorisation pour sécuriser votre serveur principal mobile.  Cette section vous montre comment effectuer les tâches suivantes liées à l’authentification dans votre projet de serveur backend .NET :

+ [Comment : ajouter une authentification à un projet serveur](#add-auth)
+ [Comment : utiliser l’authentification personnalisée pour votre application](#custom-auth)
+ [Comment : récupérer authentifié d’informations utilisateur](#user-info)
+ [Comment : restreindre l’accès aux données pour les utilisateurs autorisés](#authorize)

### <a name="add-auth"></a>Comment : ajouter une authentification à un projet serveur

Vous pouvez ajouter l’authentification à votre projet de serveur en extension de l’objet **MobileAppConfiguration** et en configurant des middleware OWIN. Lorsque vous installez le package [Microsoft.Azure.Mobile.Server.Quickstart] et appelez la méthode d’extension **UseDefaultConfiguration** , vous pouvez passer à l’étape 3.

1. Dans Visual Studio, installez le package [Microsoft.Azure.Mobile.Server.Authentication] .

2. Dans le fichier de projet Startup.cs, ajoutez la ligne de code suivante au début de la méthode de **Configuration** :

        app.UseAppServiceAuthentication(config);

    Ce composant de middleware OWIN valide les jetons émis par la passerelle de Service d’application associée.

3. Ajouter le `[Authorize]` d’attribut à n’importe quel contrôleur ou d’une méthode qui requiert une authentification. 

Pour en savoir plus sur la façon d’authentifier les clients à votre back-end d’applications Mobile, reportez-vous à la section [authentification ajouter à votre application](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Comment : utiliser l’authentification personnalisée pour votre application

Si vous ne souhaitez pas utiliser un des fournisseurs d’application Service d’authentification/autorisation, vous pouvez implémenter votre propre système d’ouverture de session. Installez le package [Microsoft.Azure.Mobile.Server.Login] pour vous aider à la génération de jeton d’authentification.  Fournir votre propre code pour valider les informations d’identification de l’utilisateur. Par exemple, vous pouvez vérifier par rapport aux mots de passe hachés et salés dans une base de données. Dans l’exemple ci-dessous, le `isValidAssertion()` méthode (définie ailleurs) est responsable de ces contrôles.

L’authentification personnalisée est exposée par la création d’un ApiController et l’exposition de `register` et `login` actions. Le client doit utiliser une interface utilisateur personnalisée pour collecter les informations de l’utilisateur.  Les informations sont ensuite soumises à l’API avec un appel HTTP POST standard. Une fois que le serveur valide l’assertion, un jeton est émis à l’aide de la `AppServiceLoginHandler.CreateToken()` méthode.  L’utilisation de **ne doivent pas** ApiController les `[MobileAppController]` attribut. 

Un exemple `login` action :

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

Dans l’exemple précédent, LoginResult et LoginResultUser sont des objets sérialisables qui exposent les propriétés requises. Le client attend les réponses à retourner en tant qu’objets JSON du formulaire :

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Le `AppServiceLoginHandler.CreateToken()` méthode inclut une _assistance_ et un paramètre de _l’émetteur_ . Ces deux paramètres sont définies à l’URL de la racine de votre application, à l’aide du schéma HTTPS. De même, vous devez définir _secretKey_ à que la valeur de votre application de signature de la clé. Ne distribuez pas de la clé de signature dans un client tel qu’il peut être utilisé pour pourra pas aller à touches et emprunter l’identité des utilisateurs. Vous pouvez obtenir la clé de signature lorsque hébergé dans le Service d’application en référençant le _site Web\_AUTH\_signature\_clé_ variable d’environnement. Le cas échéant dans un contexte de débogage local, suivez les instructions de la section [débogage Local avec l’authentification](#local-debug) pour récupérer la clé et le stocker sous la forme d’un paramètre d’application.

Le jeton émis peut également inclure des autres déclarations et une date d’expiration.  Au minimum, le jeton émis doit inclure une demande de remboursement d’objet (**sub**).

Vous pouvez prendre en charge le client standard `loginAsync()` méthode par la surcharge de l’itinéraire de l’authentification.  Si le client appelle `client.loginAsync('custom');` pour vous connecter, votre itinéraire doit être `/.auth/login/custom`.  Vous pouvez définir l’itinéraire pour le contrôleur de l’authentification personnalisée à l’aide de `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

>[AZURE.TIP] À l’aide de la `loginAsync()` approche garantit que le jeton d’authentification est attaché à chaque appel ultérieur au service.

###<a name="user-info"></a>Comment : récupérer authentifié d’informations utilisateur

Lorsqu’un utilisateur est authentifié par le Service de l’application, vous pouvez accéder à l’ID utilisateur et autres informations dans votre code principal de .NET. Les informations utilisateur peuvent être utilisées pour prendre des décisions d’autorisation dans le système principal. Le code suivant obtient l’ID d’utilisateur associé à une demande :

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Le SID est dérivé de l’ID d’utilisateur spécifiques à un fournisseur et est statique pour un utilisateur donné et le fournisseur de connexions.  Le SID est null pour les jetons d’authentification non valide.

Service d’application vous permet également de demander des déclarations spécifiques à votre fournisseur de connexion. Chaque fournisseur d’identité peut fournir plus d’informations à l’aide du Kit de développement logiciel du fournisseur d’identité.  Par exemple, vous pouvez utiliser l’API de graphique Facebook pour les informations de vos amis.  Vous pouvez spécifier les revendications qui sont demandées dans la lame de fournisseur dans le portail Azure. Certaines demandes nécessitent une configuration supplémentaire avec le fournisseur d’identité.

Le code suivant appelle la méthode d’extension **GetAppServiceIdentityAsync** pour obtenir les informations d’identification de connexion, qui incluent le jeton d’accès nécessaire pour effectuer des requêtes par rapport à l’API de graphique Facebook :

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Ajouter à l’aide d’une instruction de `System.Security.Principal` pour fournir de la méthode d’extension **GetAppServiceIdentityAsync** .

### <a name="authorize"></a>Comment : restreindre l’accès aux données pour les utilisateurs autorisés

Dans la section précédente, nous vous avons montré comment récupérer l’ID d’utilisateur d’un utilisateur authentifié. Vous pouvez limiter l’accès aux données et autres ressources en fonction de cette valeur. Par exemple, l’ajout d’une colonne de nom d’utilisateur à des tables et le filtrage des résultats de la requête par l’ID utilisateur sont un moyen simple pour limiter les données renvoyées uniquement aux utilisateurs autorisés. Le code suivant renvoie des lignes de données uniquement lorsque le SID correspond à la valeur dans la colonne nom d’utilisateur dans la table TodoItem :

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Le `Query()` méthode renvoie une `IQueryable` qui peut être manipulé par LINQ pour gérer le filtrage.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Comment : ajouter des émetteurs notifications à un projet serveur

Ajouter des notifications de type pousser à votre projet de serveur par l’extension de l’objet **MobileAppConfiguration** et la création d’un client de concentrateurs de Notification.

1. Dans Visual Studio, cliquez sur le projet serveur et cliquez sur **Gérer les Packages NuGet**, recherchez `Microsoft.Azure.Mobile.Server.Notifications`, puis cliquez sur **installer**. 

2. Répétez cette étape pour installer le `Microsoft.Azure.NotificationHubs` package, qui inclut la bibliothèque client de concentrateurs de Notification.

3. Dans App_Start/Startup.MobileApp.cs, ajoutez un appel à la méthode d’extension **AddPushNotifications()** lors de l’initialisation :

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);

4. Ajoutez le code suivant crée un client de concentrateurs de Notification :

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Vous pouvez maintenant utiliser le client de concentrateurs de Notification à envoyer des notifications de type Pousser aux périphériques inscrits. Pour plus d’informations, consultez [Ajouter des notifications de type pousser à votre application](app-service-mobile-ios-get-started-push.md). Pour plus d’informations sur les concentrateurs de Notification, consultez [Vue d’ensemble des concentrateurs de Notification](../notification-hubs/notification-hubs-push-notification-overview.md).

##<a name="tags"></a>Comment : activer ciblé par émission à l’aide de balises

Concentrateurs de notification vous permet d’envoyer des notifications ciblées vers les enregistrements spécifiques à l’aide de balises. Plusieurs balises sont créés automatiquement :

* L’ID d’Installation identifie un périphérique spécifique.
* L’Id d’utilisateur basées sur le SID authentifié identifie un utilisateur spécifique.

L’ID d’installation est accessible à partir de la propriété **ID** sur l' **MobileServiceClient**.  L’exemple suivant montre comment utiliser l’ID d’installation pour ajouter une balise à une installation spécifique de concentrateurs de Notification :

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Toutes les balises fournis par le client lors de l’inscription de notifications push sont ignorées par le serveur principal lors de la création de l’installation. Pour activer un client ajouter des balises à l’installation, vous devez créer une API personnalisée qui ajoute des balises qui utilisent le modèle précédent. 

Reportez-vous à la section [balises de notification Client ajouté un push] [ 5] dans l’exemple de démarrage rapide terminée App Service Mobile Apps pour obtenir un exemple.

##<a name="push-user"></a>Comment : envoyer des notifications de type Pousser aux utilisateurs authentifiés

Lorsqu’un utilisateur authentifié enregistre pour les notifications de type Pousser, un identificateur d’utilisateur est automatiquement ajouté à l’enregistrement. À l’aide de cette balise, vous pouvez envoyer des notifications de type pousser à tous les périphériques sont enregistrés par cette personne. Le code suivant obtient le SID de l’utilisateur effectuant la demande et envoie une notification de transmission modèle à chaque enregistrement de dispositif par cette personne :

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Lors de l’enregistrement pour les notifications de transmission à partir d’un client authentifié, assurez-vous que l’authentification est terminée avant d’essayer de l’enregistrement. Pour plus d’informations, voir [Activer pour les utilisateurs] [ 6] dans l’exemple de démarrage rapide terminée App Service Mobile Apps pour le back-end de .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Comment : déboguer et résoudre les problèmes de serveur .NET SDK

Service d’application Azure fournit plusieurs de débogage et de résolution de problèmes pour les applications ASP.NET :

- [Surveillance d’un Service d’application Azure](../app-service-web/web-sites-monitor.md)
- [Activer l’enregistrement des diagnostics dans le Service d’application Azure](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Résoudre les problèmes liés à un Service d’application Azure dans Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Enregistrement dans le journal

Vous pouvez écrire dans les journaux de diagnostic de Service de l’application à l’aide de l’écriture de trace ASP.NET standard. Avant de pouvoir écrire dans les journaux, vous devez activer les tests de diagnostic dans votre back-end de l’application Mobile.

Pour activer les tests de diagnostic et écrire dans les journaux :

1. Suivez les étapes de [l’activation des diagnostics](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Ajoutez le code suivant à l’aide d’instruction dans votre fichier de code :

        using System.Web.Http.Tracing;

3. Créer un writer de trace à écrire dans le système principal de .NET pour les journaux de diagnostic, comme suit :

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");

4. Republiez votre projet server et accéder le back-end de l’application Mobile pour exécuter le chemin d’accès de code avec la journalisation.

5. Téléchargez et évaluez les journaux, comme décrit dans [Comment : télécharger des journaux](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Débogage local avec authentification

Vous pouvez exécuter votre application localement pour tester les modifications avant de les publier sur le nuage. Pour les serveurs principaux de la plupart des applications de Mobile d’Azure, appuyez sur la touche *F5* dans Visual Studio. Toutefois, il existe des considérations supplémentaires lors de l’utilisation de l’authentification.

Vous devez disposer d’une application mobile basée sur le nuage avec application d’authentification/autorisation du Service configurée, et que votre client doit avoir le point de terminaison du nuage spécifié comme hôte de remplacement. Consultez la documentation de votre plate-forme cliente pour les étapes spécifiques nécessaires.

Assurez-vous que votre serveur principal mobile a [Microsoft.Azure.Mobile.Server.Authentication] installé. Puis, dans la classe de démarrage de votre application OWIN, ajoutez ce qui suit, après `MobileAppConfiguration` a été appliqué à votre `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

Dans l’exemple précédent, vous devez configurer les paramètres de l’application _authAudience_ et _authIssuer_ dans votre fichier Web.config à chacun l’URL de la racine de votre application, à l’aide du schéma HTTPS. De même, vous devez définir _authSigningKey_ comme clé de signature de la valeur de votre application. Pour obtenir la clé de signature :

1. Accédez à votre application dans [Azure portal] 
2. Cliquez sur **Outils**, **Kudu**, **Aller**.
3. Dans le site de gestion de Kudu, cliquez sur **l’environnement**.
4. Rechercher la valeur de _site Web\_AUTH\_signature\_clé_. 

Utilisez la clé de signature pour le paramètre _authSigningKey_ dans votre configuration de l’application locale.  La principale mobile est maintenant équipée pour valider les jetons s’exécute en local, le client obtient le jeton du point de terminaison sur le nuage.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure portal]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx

