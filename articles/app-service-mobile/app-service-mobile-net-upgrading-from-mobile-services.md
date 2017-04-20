<properties
    pageTitle="Mise à niveau à partir de Services mobiles au Service d’application Azure"
    description="Découvrez comment facilement mettre à niveau votre application de Services mobiles à une application de Service Mobile App"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Mise à niveau de votre Service Mobile de Azure .NET existant au Service de l’application

Service d’application Mobile est une nouvelle façon de générer des applications mobiles à l’aide de Microsoft Azure. Pour plus d’informations, consultez [Quelles sont les applications Mobile ?].

Cette rubrique décrit comment mettre à niveau une application back-end .NET Azure Mobile Services vers une nouvelle App Service Mobile Apps. Lorsque vous effectuez cette mise à niveau, votre application Mobile Services existante peut continuer à fonctionner.   Si vous devez mettre à niveau une application back-end de Node.js, reportez-vous à [mise à niveau de vos Services de Mobile Node.js](./app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Lorsqu’un back-end mobile est mis à niveau vers le Service d’application Azure, il a accès à toutes les fonctionnalités de Service de l’application et sont facturés en fonction de la [tarification Service de l’application], pas les Services mobiles de tarification.

##<a name="migrate-vs-upgrade"></a>Migration et mise à niveau

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Il est recommandé d' [effectuer une migration](app-service-mobile-migrating-from-mobile-services.md) avant de passer par une mise à niveau. De cette façon, vous pouvez placer les deux versions de votre application sur le même Plan de Service application et entraînent des frais supplémentaires.

###<a name="improvements-in-mobile-apps-net-server-sdk"></a>Améliorations dans Mobile applications .NET server SDK

Mise à niveau vers le nouveau [SDK de Apps Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) offre les avantages suivants :

- Plus grande souplesse sur les dépendances de NuGet. L’environnement d’hébergement ne fournisse pas ses propres versions des packages NuGet, vous pouvez utiliser les autres versions compatibles. Toutefois, s’il y a des nouveaux bugfixes critiques ou des mises à jour de sécurité pour le Kit de développement de serveur Mobile ou les dépendances, vous devez mettre à jour manuellement votre service.

- Plus grande souplesse dans le Kit de développement mobile. Vous pouvez contrôler de manière explicite les fonctionnalités et les itinéraires sont définis, comme l’authentification, API de table et le point de terminaison de l’inscription de push. Pour plus d’informations, voir [comment utiliser le Kit de développement .NET server SDK pour les applications mobiles Azure](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Prise en charge pour les autres types de projets ASP.NET et les itinéraires. Vous pouvez désormais héberger les contrôleurs MVC et API de Web dans le même projet que votre projet principal mobile.

- Prise en charge de nouvelles fonctionnalités de l’authentification de Service d’application, qui vous permettent d’utiliser une configuration commune de l’authentification sur le web et les applications mobiles.

##<a name="overview"></a>Présentation de mise à niveau de base

Dans de nombreux cas, la mise à niveau sera aussi simple que de basculer vers le nouveau serveur d’applications Mobile SDK et republiez votre code sur une nouvelle instance de l’application Mobile. Il existe toutefois des scénarios qui nécessitent une configuration supplémentaire, tels que les scénarios d’authentification avancée et travailler avec des tâches planifiées. Chacune d’elles est couverts dans les sections suivantes.

>[AZURE.TIP] Il est recommandé de lire et comprendre le reste de cette rubrique de complètement avant de commencer une mise à niveau. Prenez note de toutes les fonctionnalités, vous utilisez indiquées ci-dessous.

Le client des Services de Mobile SDK sont **pas** compatible avec le nouveau serveur d’applications Mobile SDK. Afin d’assurer la continuité de service pour votre application, vous ne devez pas publier des modifications sur un site actuellement desservant des clients publiés. Au lieu de cela, vous devez créer une nouvelle application mobile qui sert un doublon. Vous pouvez placer cette application sur le même plan de Service de l’application pour éviter de supporter des charges financières.

Vous aurez alors deux versions de l’application : reste la même et sert publié des applications dans la nature et l’autre qui vous pouvez ensuite mettre à niveau et la cible avec une nouvelle version du client. Vous pouvez déplacer et à votre rythme à tester votre code, mais vous devez vous assurer que les correctifs de bogues que vous rendre appliquées à la fois. Lorsque vous vous sentez le nombre désiré de client applications dans la nature ont mis à jour vers la dernière version, supprimer l’application migrée d’origine si vous le souhaitez.

Le plan complet pour le processus de mise à niveau est la suivante :

1. Créer une nouvelle application Mobile
2. Mettre à jour le projet pour utiliser le nouveau SDK de serveur
3. Publiez une nouvelle version de votre application client
4. (Facultatif) Supprimer votre instance migré d’origine

##<a name="mobile-app-version"></a>Création d’une seconde instance d’application
La première étape de la mise à niveau est de créer la ressource d’application Mobile qui doit héberger la nouvelle version de votre application. Si vous avez déjà migré un service mobile existant, vous devez créer cette version sur le même plan d’hébergement. Ouvrir le [portail Azure] et accédez à votre application migrée. Notez le Plan de Service application il s’exécute sur.

Créez ensuite la deuxième instance de l’application en suivant les [instructions de création de back-end de .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Lorsque vous êtes invité à vous sélectionnez Plan de Service d’application ou des « hébergement de plan » choisissez le plan de votre application migrée.

Vous souhaiterez probablement utiliser la même base de données et d’un concentrateur de Notification comme vous le faisiez dans les Services mobiles. Vous pouvez copier ces valeurs en ouvrant [Azure portal] et en accédant à l’application d’origine, puis cliquez sur **paramètres** > **paramètres de l’Application**. Sous **Chaînes de connexion**, vous devez copier `MS_NotificationHubConnectionString` et `MS_TableConnectionString`. Accédez à votre nouveau site de mise à niveau et les coller en remplaçant les valeurs existantes. Répétez cette procédure pour les autres paramètres de l’application les besoins de votre application. Si vous N'utilisez pas un service de migration, vous pouvez lire les chaînes de connexion et les paramètres de l’application à partir de l’onglet **configuration** de la section Services mobiles d' [Azure portal classique].

Faites une copie du projet de votre application ASP.NET et le publier sur votre nouveau site. À l’aide d’une copie de votre application client mis à jour avec la nouvelle adresse URL, valider que tout fonctionne comme prévu.

## <a name="updating-the-server-project"></a>Mise à jour du projet serveur

Applications Mobile fournit un nouveau [Kit de développement Mobile Application Server] , qui fournit la majeure partie de la même fonctionnalité que l’exécution de Services mobiles. Tout d’abord, vous devez supprimer toutes les références aux packages de Services mobiles. Dans le Gestionnaire de package NuGet, recherchez `WindowsAzure.MobileServices.Backend`. La plupart des applications s’affiche plusieurs packages ici, y compris les `WindowsAzure.MobileServices.Backend.Tables` et `WindowsAzure.MobileServices.Backend.Entity`. Dans ce cas, démarrez avec le package plus bas dans l’arborescence des dépendances, telles que `Entity`et le supprimer. Lorsque vous y êtes invité, ne supprimez pas tous les packages dépendants. Répétez ce processus jusqu'à ce que vous avez supprimé `WindowsAzure.MobileServices.Backend` lui-même.

À ce stade, vous aurez un projet qui n’a plus de référence Mobile Services SDK.

Vous allez ensuite ajouter des références du Kit de développement des applications mobiles. Pour cette mise à jour, la plupart des développeurs devez télécharger et installer le `Microsoft.Azure.Mobile.Server.Quickstart` du package, comme cela ira chercher dans tout le jeu requis.

Il y a quelques erreurs du compilateur résultant des divergences entre les kits de développement, mais ces sont faciles à adresse et sont décrites dans le reste de cette section.

### <a name="base-configuration"></a>Configuration de base

Ensuite, dans WebApiConfig.cs, vous pouvez remplacer :

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

avec

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

>[AZURE.NOTE] Si vous souhaitez en savoir plus sur le nouveau serveur .NET SDK et comment faire pour ajouter ou supprimer des fonctionnalités à partir de votre application, reportez-vous à la rubrique [comment utiliser le Kit de développement logiciel de .NET server] .

Si votre application permet d’utiliser les fonctionnalités d’authentification, vous devrez également inscrire un middleware OWIN. Dans ce cas, vous devez déplacer le code de configuration ci-dessus dans une nouvelle classe de démarrage de OWIN.

1. Ajoutez le package NuGet `Microsoft.Owin.Host.SystemWeb` si elle n’est pas déjà incluse dans votre projet.
2. Dans Visual Studio, cliquez avec le bouton droit sur votre projet et sélectionnez **Ajouter** -> **Un nouvel élément**. Sélectionnez **Web** -> **générale** -> **classe de démarrage de OWIN**.
3. Déplacez le code ci-dessus pour MobileAppConfiguration de `WebApiConfig.Register()` à la `Configuration()` méthode de votre nouvelle classe de démarrage.

Assurez-vous que le `Configuration()` méthode se termine par :

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Il existe des modifications supplémentaires associées à l’authentification, qui sont traitées dans la section authentification complète ci-dessous.

### <a name="working-with-data"></a>Utilisation des données

Dans les Services mobiles, le nom de l’application mobile pris en charge en tant que le nom de schéma par défaut dans la configuration d’Entity Framework.

Pour vous assurer d’avoir le même schéma référencé comme avant, utilisez la commande suivante pour définir le schéma dans le DbContext pour votre application :

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Vérifiez que vous disposez des MS_MobileServiceName si vous effectuez les étapes ci-dessus. Vous pouvez également fournir un autre nom de schéma si votre application le personnalisé précédemment.

### <a name="system-properties"></a>Propriétés système

#### <a name="naming"></a>D’attribution de noms

Dans le Kit de développement logiciel du serveur Azure Mobile Services, propriétés du système contiennent toujours un trait de soulignement double (`__`) préfixe pour les propriétés :

- __createdAt
- __updatedAt
- __deleted
- __version

Kits de développement logiciel client Services mobiles ont une logique spéciale pour l’analyse des propriétés système dans ce format.

Dans les applications mobiles Azure, ne sont plus des propriétés système ont un format spécial et portent les noms suivants :

- créédans
- updatedAt
- supprimé
- Version

Le kits de développement logiciel du client applications Mobile utilisent les nouveaux noms de propriétés système, aucune modification n’est requise pour le code client. Toutefois, si vous apportez directement des appels reste à votre service puis vous devez modifier vos requêtes en conséquence.

#### <a name="local-store"></a>Magasin local

Les modifications apportées aux noms de propriétés système signifient qu’une base de données locale de synchronisation hors connexion pour les Services mobiles n’est pas compatible avec les applications mobiles. Si possible, vous devez éviter la mise à niveau du client, les applications de Services mobiles pour les applications mobiles jusqu’après modifications en attente ont été envoyées au serveur. Ensuite, l’application mise à niveau doit utiliser un nouveau nom de fichier de base de données.

Si une application client est mis à niveau à partir de Services mobiles pour applications Mobile alors qu’il existe des modifications en mode hors connexion en attente dans la file d’attente de l’opération, puis la base de données du système doit être mis à jour pour utiliser les nouveaux noms de colonne. Dans iOS, ceci peut être réalisé à l’aide de migrations légers pour modifier les noms de colonne. Sur Android et le client géré .NET, vous devez écrire SQL personnalisé pour renommer les colonnes de vos tables d’objet de données.

Dans iOS, vous devez modifier votre schéma de données de base pour vos entités de données correspondre à la suivante. Notez que les propriétés `createdAt`, `updatedAt` et `version` n’est plus nécessaire une `ms_` préfixe :

| Attribut |  Type de   | Remarque                                                 |
|---------- |  ------ | -----------------------------------------------------|
| ID        | Chaîne, requises  | clé primaire dans un magasin distant         |
| créédans | Date    | (facultatif) correspond à la propriété du système créédans         |
| updatedAt | Date    | (facultatif) correspond à la propriété du système updatedAt         |
| Version   | Chaîne  | (facultatif) permet de détecter les conflits, correspond à la version |

#### <a name="querying-system-properties"></a>Interrogation de propriétés système

Dans les Services de Mobile d’Azure, les propriétés système ne sont pas envoyées par défaut, mais uniquement lorsqu’elles sont demandées à l’aide de la chaîne de requête `__systemProperties`. En revanche, dans les applications Azure Mobile système propriétés sont **toujours sélectionné** dans la mesure où ils font partie du modèle d’objet serveur Kit de développement logiciel.

Cette modification affecte principalement les implémentations personnalisées de gestionnaires de domaine, telles que les extensions de `MappedEntityDomainManager`. Dans les Services mobiles, si un client demande jamais des propriétés du système, il est possible d’utiliser un `MappedEntityDomainManager` qui n’est pas réellement mappé toutes les propriétés. Toutefois, dans des applications Azure Mobile, ces propriétés non mappées entraîne une erreur dans les requêtes GET.

La façon la plus simple pour résoudre le problème consiste à modifier de votre DTO afin qu’ils héritent de `ITableData` à la place de `EntityData`. Ensuite, ajoutez le `[NotMapped]` d’attribut pour les champs qui doivent être omis.

Par exemple, les définitions suivantes `TodoItem` sans propriétés système :

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Remarque : Si vous obtenez des erreurs `NotMapped`, ajoutez une référence à l’assembly `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS

Services mobiles inclus une prise en charge pour les CORS en encapsulant la solution ASP.NET CORS. Cette couche de renvoi à la ligne a été supprimée pour permettre à plus de contrôle, le développeur pour valoriser les [CORS ASP.NET prennent en charge](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)directement.

Les principaux domaines de préoccupation si CORS sont que la `eTag` et `Location` en-têtes doivent être autorisés que pour le kits de développement logiciel du client fonctionne correctement.

### <a name="push-notifications"></a>Notifications de type Pousser
Pour la distribution, l’élément principal que vous trouvez manquants à partir du Kit de développement logiciel serveur est la classe PushRegistrationHandler. Les enregistrements sont traitées différemment dans les applications mobiles et les enregistrements il sont activées par défaut. Gestion des balises peuvent être effectuée à l’aide des API personnalisés. Veuillez consulter les instructions [d’enregistrement des balises](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) pour plus d’informations.

### <a name="scheduled-jobs"></a>Tâches planifiées
Les tâches planifiées ne sont pas intégrées d’applications Mobile, afin que tous les travaux existants que vous avez dans votre back-end .NET devra être mis à jour individuellement. Une option consiste à créer un planifié, [Travail Web] sur le site de code d’application Mobile. Vous pouvez également configurer un contrôleur contenant votre code de projet et configurer le [Planificateur d’Azure] pour atteindre ce point de terminaison sur la planification.

### <a name="miscellaneous-changes"></a>Modifications diverses
Tous les ApiControllers qui sera consommées par un client mobile doit désormais avoir le `[MobileAppController]` attribut. Ce n’est plus inclus par défaut afin que d’autres ApiControllers pour aller pas affectés par les formateurs mobiles.

Le `ApiServices` objet ne fait plus partie du SDK. Pour accéder aux paramètres de l’application Mobile, vous pouvez utiliser les éléments suivants :

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

De même, l’enregistrement est désormais possible à l’aide de l’écriture de trace ASP.NET standard :

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Considérations de l’authentification

Les composants d’authentification des Services mobiles ont maintenant été déplacées dans la fonctionnalité de l’application Service d’authentification/autorisation. Vous pouvez apprendre à activer cette pour votre site, en lisant la rubrique [authentification ajouter à votre application mobile](app-service-mobile-ios-get-started-users.md) .

Pour certains fournisseurs, tels que DAS, Facebook et Google, vous devez être en mesure de tirer parti de l’enregistrement existant à partir de votre application de copie. Vous devez simplement accéder au portail du fournisseur identité et d’ajouter une nouvelle URL de redirection pour l’enregistrement. Puis configurer Application Service d’authentification/autorisation avec l’ID de client et le secret.

### <a name="controller-action-authorization"></a>Autorisation d’action de contrôleur
Toutes les instances de la `[AuthorizeLevel(AuthorizationLevel.User)]` attribut doit maintenant être modifiée pour utiliser l’ASP.NET standard `[Authorize]` attribut. En outre, contrôleurs sont désormais anonyme par défaut, comme dans les autres applications ASP.NET.
Si vous utilisez une des autres options de AuthorizeLevel, tels que Admin ou Application, veuillez noter que ces ont disparu. Vous pouvez plutôt configurer AuthorizationFilters de secrets partagés ou configurer une identité de Service DAS pour activer les appels de service au service en toute sécurité.

### <a name="getting-additional-user-information"></a>Lors de l’obtention des informations utilisateur supplémentaires

Vous pouvez obtenir des informations utilisateur supplémentaires, y compris les jetons d’accès par le biais de la `GetAppServiceIdentityAsync()` méthode :

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

En outre, si votre application prend des dépendances sur les ID utilisateur, telles que de les stocker dans une base de données, il est important de noter que les ID utilisateur entre les Services mobiles et des applications de Mobile Application Service sont différents. Vous pouvez toujours obtenir l’ID utilisateur de Services Mobile, cependant. Toutes les sous-classes de ProviderCredentials ont une propriété de nom d’utilisateur. Pour continuer à partir de l’exemple antérieur :

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Si votre application ne prend pas toutes les dépendances sur les ID utilisateur, il est important que vous exploitez le même type d’enregistrement avec un fournisseur d’identité si possible. ID utilisateur sont généralement portée limitée à l’inscription de l’application qui a été utilisée, afin d’introduire une nouvelle inscription pourrait créer des problèmes avec la correspondance des utilisateurs à leurs données.

### <a name="custom-authentication"></a>Authentification personnalisée

Si votre application utilise une solution d’authentification personnalisée, vous devez vous assurer que le site de mise à niveau a accès au système. Suivez les instructions de nouveau pour l’authentification personnalisée dans la [vue d’ensemble du Kit de développement .NET server] pour intégrer votre solution. Veuillez noter que les composants d’authentification personnalisé dans l’aperçu.

##<a name="updating-clients"></a>Mise à jour des clients
Une fois que vous avez un back-end Mobile application opérationnelle, vous pouvez travailler sur une nouvelle version de votre application client qui la consomme. Applications Mobile inclut également une nouvelle version du SDK de client, et comme pour la mise à niveau de serveur ci-dessus, vous devez supprimer toutes les références aux kits de développement logiciel de Mobile Services avant d’installer les versions d’applications Mobile.

Un des principaux changements entre les versions est que les constructeurs ne nécessitent plus une clé d’application. Vous maintenant simplement passer dans l’URL de votre application Mobile. Par exemple, sur les clients .NET, les `MobileServiceClient` constructeur est maintenant :

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Vous pouvez lire sur l’installation de nouveaux kits de développement logiciel et à l’aide de la nouvelle structure via les liens ci-dessous :

- [iOS version 3.0.0 ou version ultérieure](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) version 2.0.0 ou ultérieur](app-service-mobile-dotnet-how-to-use-client-library.md)

Si votre application effectue l’utilisation de notifications de type Pousser, notez les instructions d’enregistrement spécifique pour chaque plate-forme, comme l’ont été certaines modifications ainsi.

Une fois que la nouvelle version de client prête, essayez-le par rapport à votre projet de serveur mis à niveau. Après avoir validé que cela fonctionne, vous pouvez lancer une nouvelle version de votre application à des clients. Finalement, une fois que vos clients ont eu la possibilité de recevoir ces mises à jour, vous pouvez supprimer la version de Services mobiles de votre application. À ce stade, vous avez entièrement mis à niveau vers une application Mobile de Service application utilisant le tout dernier serveur d’applications Mobile SDK.

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure portal classique]: https://manage.windowsazure.com/
[Quelles sont les applications Mobile ?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Kit de développement de serveur d’applications mobiles]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Planificateur d’Azure]: /en-us/documentation/services/scheduler/
[Travail Web]: ../app-service-web/websites-webjobs-resources.md
[Comment utiliser le Kit de développement logiciel de .NET server]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Service d’application de tarification]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Vue d’ensemble du Kit de développement .NET server]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
