<properties
    pageTitle="Les notifications de transmission de géo-extraits avec Bing des données spatiales et les concentrateurs de Notification d’Azure | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre livrer des notifications basées sur l’emplacement de push avec Bing des données spatiales et les concentrateurs de Notification d’Azure."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="notification de transmission, de notification d’émission"
    authors="dend"
    manager="yuaxu"
    editor="dend"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="dendeli"/>
    
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Notifications de transmission de géo-extraits avec Bing des données spatiales et les concentrateurs de Notification d’Azure
 
 > [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).

Dans ce didacticiel, vous allez apprendre livrer des notifications push de fondée sur l’emplacement avec les données spatiales Bing, exploité à partir d’une application de plate-forme universel de Windows et les concentrateurs de Notification Azure.

##<a name="prerequisites"></a>Conditions préalables
Tout d’abord, vous devez vous assurer que vous disposez de tous les logiciels et services composants requis :

* [Visual Studio 2015 mise à jour 1](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) ou une version ultérieure ([Édition Communauté](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) fera également). 
* Version la plus récente du [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/). 
* [Compte du centre de développement de Bing Maps](https://www.bingmapsportal.com/) (vous pouvez créer une gratuitement et l’associer à votre compte Microsoft). 

##<a name="getting-started"></a>Mise en route

Commençons par créer le projet. Dans Visual Studio, démarrez un nouveau projet de type **Application vide (universel de Windows)**.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

Une fois la création du projet terminée, vous devez avoir le faisceau de l’application elle-même. Maintenant nous allons définir tous les éléments de l’infrastructure de gardiennage. Étant donné que nous allons utiliser les services de Bing pour cela, il existe un point de terminaison API REST public qui nous permet d’interroger des cadres de l’emplacement spécifique :

    http://spatial.virtualearth.net/REST/v1/data/
    
Vous devez spécifier les paramètres suivants pour qu’il fonctionne :

* **ID de Source de données** et le **Nom de Source de données** : dans l’API Bing Maps, sources de données contiennent des métadonnées de bucketed diverses, telles que les emplacements et les heures d’opération. Vous pouvez en savoir plus sur ces ici. 
* **Nom de l’entité** , l’entité que vous souhaitez utiliser comme point de référence pour la notification. 
* **Clé de l’API Bing Maps** – il s’agit de la clé que vous avez obtenu précédemment lorsque vous avez créé le compte Centre de développement de Bing.
 
Nous allons le faire un approfondi sur le programme d’installation pour chacun des éléments ci-dessus.

##<a name="setting-up-the-data-source"></a>Configuration de la source de données

Vous pouvez le faire dans le centre de développement de Bing Maps. Cliquez simplement dans la barre de navigation supérieure, cliquez sur **sources de données** et sélectionnez **Gérer les Sources de données**.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Vous n’avez pas travaillé avec l’API Bing Maps avant, probablement il n’est pas les sources de données présentes, afin que vous pouvez simplement créer un nouveau en cliquant sur Télécharger les données à une source de données. Vérifiez que vous remplissez tous les champs obligatoires :

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Vous vous demandez peut-être – quel est le fichier de données et ce qui devez vous être téléchargement ? Pour les besoins de ce test, nous pouvons simplement utiliser l’échantillon de canaux de communication qui encadre la zone des gens de San Francisco :

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
La liste ci-dessus représente cette entité :

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Simplement copier coller la chaîne ci-dessus dans un nouveau fichier et enregistrez-le en tant que **NotificationHubsGeofence.pipe**et téléchargez-le dans le centre de développement de Bing.

>[AZURE.NOTE]Vous pouvez être invité à spécifier une nouvelle clé pour la **clé principale** qui est différente de la **Clé de la requête**. Simplement créer une nouvelle clé via le tableau de bord et actualiser la page de téléchargement de source de données.

Une fois que vous téléchargez le fichier de données, vous devez vous assurer que vous publiez la source de données. 

Aller à **Gérer les Sources de données**, comme nous l’avons fait précédemment, trouver votre source de données dans la liste et cliquez sur **Publier** dans la colonne **Actions** . Dans, vous devez voir votre source de données dans l’onglet **Sources de données de publication** :

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Si vous cliquez sur **Modifier**, vous ne pourrez pas voir en un coup de œil quels emplacements que nous avons introduit dans celui-ci :

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

À ce stade, le portail ne montre pas les limites de le geofence que nous avons créé, il nous suffit est une confirmation que l’emplacement spécifié se trouve à proximité droite.

Vous disposez maintenant de toutes les conditions pour la source de données. Pour obtenir les détails sur l’URL de la demande pour l’appel de l’API, dans le centre de développement de Bing Maps, cliquez sur **sources de données** et sélectionnez la **Source de données**.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

L' **URL de la requête** est ce dont nous parlons ici. C’est le point de terminaison par rapport à laquelle nous pouvons exécuter des requêtes pour vérifier si le périphérique est actuellement dans les limites d’un emplacement ou non. Pour effectuer cette vérification, il suffit d’exécuter un appel GET à l’URL de la requête, avec les paramètres suivants ajoutés :

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

De cette manière, vous spécifiez une cible point que nous procurer à partir du périphérique et Bing Maps effectue automatiquement les calculs pour voir si elle se trouve dans le geofence. Une fois que vous exécutez la requête via un navigateur (ou courbée), vous obtiendrez standard une réponse JSON :

![](./media/notification-hubs-geofence/bing-maps-json.png)

Cette réponse ne se produit que lorsque le point se trouve en fait dans les limites désignés. S’il n’est pas le cas, vous obtiendrez un compartiment vide de **résultats** :

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##<a name="setting-up-the-uwp-application"></a>Configuration de l’application de la série UWP

Maintenant que nous disposons de la source de données prête, nous pouvons commencer à travailler sur l’application UWP qui nous amorcés précédemment.

Tout d’abord, nous devons activer les services de localisation de notre application. Pour ce faire, double-cliquez sur `Package.appxmanifest` le fichier dans **L’Explorateur de solutions**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

Dans l’onglet Propriétés de package que vous venez d’ouvrir, cliquez sur **fonctions** et assurez-vous que vous sélectionnez **emplacement**:

![](./media/notification-hubs-geofence/vs-package-location.png)

Que la capacité de l’emplacement est déclarée, créez un nouveau dossier dans votre solution nommée `Core`et ajoutez un nouveau fichier au sein de ce dernier, appelé `LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

Le `LocationHelper` classe elle-même est assez simple à ce stade, il ne fait nous permettent d’obtenir l’emplacement de l’utilisateur par le biais de l’API système :

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

Vous pouvez en savoir plus sur l’emplacement de l’utilisateur dans les applications UWP du officiel [document MSDN](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx).

Pour vérifier que l’acquisition d’emplacement fonctionne réellement, ouvrez la partie du code de votre page d’accueil (`MainPage.xaml.cs`). Créer un nouveau gestionnaire d’événements pour le `Loaded` événement dans le `MainPage` constructeur :

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

L’implémentation du Gestionnaire d’événements est la suivante :

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Notez que nous avons déclaré le gestionnaire asynchrones, car `GetCurrentLocation` est await et requiert par conséquent à exécuter dans un contexte asynchrone. Également, car dans certaines circonstances nous pouvons vous retrouver avec un emplacement nul (par exemple, l’emplacement de services sont désactivés ou l’application a été refusée les autorisations d’accès à l’emplacement), nous avons besoin pour vous assurer qu’il est correctement géré avec un contrôle de valeur null.

Exécutez l’application. Assurez-vous que vous autorisez l’accès de l’emplacement :

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

Une fois le lancement de l’application, vous serez en mesure de voir les coordonnées dans la fenêtre **sortie** :

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Vous savez maintenant que works d’acquisition emplacement – vous pouvez supprimer le Gestionnaire d’événements de test pour chargé car nous ne l’utilise plus.

L’étape suivante consiste à capturer les modifications de l’emplacement. Pour ce faire, revenons à la `LocationHelper` de classe et ajouter le Gestionnaire d’événements `PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

La mise en oeuvre affichera les coordonnées de l’emplacement dans la fenêtre **sortie** :

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##<a name="setting-up-the-backend"></a>Configurer le serveur principal

Téléchargez l' [Exemple de back-end .NET à partir de GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). Une fois le téléchargement terminé, ouvrez le `NotifyUsers` dossier et par la suite, le `NotifyUsers.sln` fichier.

Définir le `AppBackend` de projet comme **Projet de démarrage** et la lancer.

![](./media/notification-hubs-geofence/vs-startup-project.png)

Le projet est déjà configuré pour envoyer des notifications de type Pousser aux périphériques cibles, nous aurons donc besoin de faire deux choses : permuter la bonne connexion de chaîne pour le concentrateur de notification et ajouter l’identification de limite pour envoyer la notification uniquement lorsque l’utilisateur se trouve dans le geofence.

Pour configurer la chaîne de connexion dans le `Models` dossier ouvert `Notifications.cs`. Le `NotificationHubClient.CreateClientFromConnectionString` fonction doit contenir les informations sur votre concentrateur de notification que vous pouvez obtenir dans le [Portail Azure](https://portal.azure.com) (regarder à l’intérieur de la lame de **Stratégies d’accès** dans les **paramètres**). Enregistrez le fichier de configuration mis à jour.

Nous devons maintenant créer un modèle pour le résultat de l’API Bing Maps. La façon la plus simple de le faire est avec le bouton droit sur le `Models` dossier, **Ajouter** > **classe**. Le nom `GeofenceBoundary.cs`. Une fois fait, copiez le JSON de la réponse de l’API dont nous avons parlé dans la première section et en cours d’utilisation de Visual Studio **Modifier** > **Collage spécial** > **JSON Coller en tant que Classes**. 

De cette façon, nous assurer que l’objet à désérialiser exactement telle qu’elle a été conçue. Votre classe de jeu résultant doit ressembler à ceci :

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Ensuite, ouvrez `Controllers`  >  `NotificationsController.cs`. Nous devons modifier l’appel de Post au compte de latitude et de longitude de la cible. Pour cela, ajoutez simplement deux chaînes à la signature de la fonction – `latitude` et `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Créer une nouvelle classe dans le projet appelé `ApiHelper.cs` – nous allons les utiliser pour se connecter à Bing à point d’intersection de frontières. Mettre en œuvre un `IsPointWithinBounds` fonction, comme suit :

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] Veillez à remplacer le point de terminaison API avec l’URL de la requête que vous avez obtenu précédemment à partir du centre de développement de Bing (même s’applique à la clé d’API). 

S’il existe des résultats de la requête, cela signifie que le point spécifié se trouve dans les limites de la geofence, donc nous revenons `true`. S’il n’y a aucun résultat, Bing est nous indiquant que le point est en dehors du cadre de la recherche, donc nous revenons `false`.

Dans `NotificationsController.cs`, création d’un chèque juste avant l’instruction switch :

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

De cette façon, la notification est envoyée uniquement lorsque le point est dans les limites.

##<a name="testing-push-notifications-in-the-uwp-app"></a>Test des notifications de type Pousser dans l’application de la série UWP

Pour revenir à l’application de la série UWP, nous devrions maintenant pouvoir tester les notifications. Dans le `LocationHelper` de classe, créez une nouvelle fonction – `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] Remplacez le `POST_URL` à l’emplacement de votre application web déployée que nous avons créée dans la section précédente. Pour le moment, il est OK afin de l’exécuter localement, mais lorsque vous travaillez sur le déploiement d’une version publique, vous devez héberger auprès d’un fournisseur externe.

Nous allons maintenant Assurez-vous que nous allons enregistrer l’application UWP pour les notifications de transmission. Dans Visual Studio, cliquez sur le **projet** > **stocker** > **associer l’application à la banque**.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Si vous vous connectez à votre compte de développeur, assurez-vous que vous sélectionnez une application existante ou créez une nouvelle et associez le package. 

Atteindre le centre de développement et ouvrez l’application que vous venez de créer. Cliquez sur **Services** > **Pousser des Notifications** > **services Microsoft Live de site**.

![](./media/notification-hubs-geofence/ms-live-services.png)

Sur le site, notez le **Secret de l’Application** et le **SID du Package**. Vous devez à la fois dans le portail Azure – ouvrez votre concentrateur de notification, cliquez sur **paramètres** > **De Notification Services** > **Windows (WNS)** et entrez les informations dans les champs obligatoires.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Cliquez sur **Enregistrer**.

Cliquez avec le bouton droit sur **références** dans **L’Explorateur de solutions** et sélectionnez **Manage NuGet Packages**. Nous devons ajouter une référence à la **bibliothèque managée de Bus des services Microsoft Azure** – recherchez simplement `WindowsAzure.Messaging.Managed` et l’ajouter à votre projet.

![](./media/notification-hubs-geofence/vs-nuget.png)

À des fins de test, nous pouvons créer la `MainPage_Loaded` une fois encore, gestionnaire d’événements et lui ajouter cet extrait de code :

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

Précède enregistre l’application avec le concentrateur de notification. Vous êtes prêt à l’emploi ! 

Dans `LocationHelper`, à l’intérieur du `Geolocator_PositionChanged` gestionnaire, vous pouvez ajouter un morceau de code de test qui placer force l’emplacement à l’intérieur de la geofence :

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Parce que nous ne réussissent pas les coordonnées réelles (qui peuvent être à l’intérieur des limites pour le moment) et que vous utilisez des valeurs de test prédéfinies, nous verrons une notification s’affiche pas sur la mise à jour :

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##<a name="whats-next"></a>Quel est l’avenir ?

Il existe quelques étapes que vous devrez peut-être suivre en plus des éléments ci-dessus pour vous assurer que la solution est prêt pour la production.

Tout d’abord, vous devrez vous assurer que les geofences sont dynamiques. Cette opération nécessite un surcroît de travail avec les API Bing pour être en mesure de télécharger les nouvelles frontières au sein de la source de données existante. Pour plus d’informations sur ce sujet, consultez la [documentation de l’API Bing spatiale Data Services](https://msdn.microsoft.com/library/ff701734.aspx) .

Deuxièmement, que vous utilisez pour vous assurer que la livraison est effectuée pour les participants appropriés, vous pouvez souhaiter les cibler via [le balisage](notification-hubs-tags-segment-push-message.md).

La solution ci-dessus décrit un scénario dans lequel vous pouvez avoir une grande variété de plates-formes cible, afin que nous ne pas limiter le gardiennage virtuel pour des fonctionnalités spécifiques du système. Ceci dit, la plate-forme Windows universel offre des fonctions pour [détecter les geofences droit - de-l’emploi](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).

Pour plus de détails concernant les fonctionnalités de Notification concentrateurs, consultez notre [portail de documentation](https://azure.microsoft.com/documentation/services/notification-hubs/).
