<properties
    pageTitle="Gestion de l’inscription"
    description="Cette rubrique explique comment enregistrer des périphériques avec les concentrateurs de notification pour recevoir des notifications de type Pousser."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="registration-management"></a>Gestion de l’inscription

##<a name="overview"></a>Vue d’ensemble

Cette rubrique explique comment enregistrer des périphériques avec les concentrateurs de notification pour recevoir des notifications de type Pousser. La rubrique décrit les enregistrements à un niveau élevé, puis présente les deux schémas principaux pour l’enregistrement des périphériques : enregistrement à partir du périphérique directement au concentrateur de notification et l’inscription via un back-end de l’application. 


##<a name="what-is-device-registration"></a>Quel est l’enregistrement du périphérique

Enregistrement de dispositif avec un concentrateur de Notification s’effectue à l’aide d’une **Installation**ou un **enregistrement** .

#### <a name="registrations"></a>Enregistrements
Un enregistrement associe le handle du Service de Notification de plate-forme (solution) pour un périphérique avec les balises et, éventuellement, un modèle. Le handle de la solution peut être un ChannelURI, jeton de périphérique ou numéro d’inscription GCM. Les balises sont utilisées pour acheminer des notifications vers le jeu de poignées de périphérique approprié. Pour plus d’informations, consultez [routage et les Expressions de la balise](notification-hubs-tags-segment-push-message.md). Les modèles sont utilisés pour implémenter la transformation de l’enregistrement par. Pour plus d’informations, consultez [modèles](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Installations
Une Installation est une version améliorée d’inscription qui inclut un ensemble de transmission des propriétés associées. Il s’agit de la meilleure approche à l’enregistrement de vos périphériques. Toutefois, il n'est pas pris en charge par le côté client .NET SDK ([Kit de développement de logiciel de Notification Hub pour les opérations de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) à partir d’encore.  Cela signifie que si vous vous inscrivez à partir du périphérique client lui-même, vous devrez utiliser l’approche de [l’API REST de concentrateurs Notification](https://msdn.microsoft.com/library/mt621153.aspx) pour prendre en charge les installations. Si vous utilisez un service back-end, vous devez pouvoir utiliser [SDK de concentrateur de Notification pour les opérations de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Avantages à l’utilisation d’installations sont les suivantes :

* Création ou mise à jour d’une installation est entièrement équipotentes. Ainsi, vous pouvez la recommencer sans les préoccupations à propos des enregistrements en double.
* Le modèle d’installation facilite la faire individuels push - ciblant des périphériques spécifiques. Une balise de système **« $InstallationId : [ID] »** est ajouté automatiquement avec chaque enregistrement de l’installation de la base. Par conséquent, vous pouvez appeler un envoi à cette balise à un périphérique spécifique sans avoir à effectuer de codage supplémentaires.
* À l’aide d’installations vous permet également d’effectuer des mises à jour de l’enregistrement partiel. La mise à jour partielle d’une installation est demandée avec une méthode de correctifs à l’aide de la [norme JSON-Patch](https://tools.ietf.org/html/rfc6902). Ceci est particulièrement utile lorsque vous souhaitez mettre à jour des balises sur l’enregistrement. Vous n’êtes pas obligé de dérouler l’enregistrement entier et puis renvoyez à nouveau toutes les balises précédentes.

Une installation peut contenir les propriétés suivantes. Pour une liste complète de l’installation de propriétés voir [Propriétés d’Installation](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx) pour [créer ou remplacer une Installation avec l’API REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) ou la.

    // Example installation format to show some supported properties
    {
        installationId: "",
        expirationTime: "",
        tags: [],
        platform: "",
        pushChannel: "",
        ………
        templates: {
            "templateName1" : {
                body: "",
                tags: [] },
            "templateName2" : {
                body: "",
                // Headers are for Windows Store only
                headers: {
                    "X-WNS-Type": "wns/tile" }
                tags: [] }
        },
        secondaryTiles: {
            "tileId1": {
                pushChannel: "",
                tags: [],
                templates: {
                    "otherTemplate": {
                        bodyTemplate: "",
                        headers: {
                            ... }
                        tags: [] }
                }
            }
        }
    }

 

Il est important de noter que les inscriptions et les installations par défaut n’est plus à expiration.

Les enregistrements et les installations doivent contenir un handle valide de la solution pour chaque périphérique/canal. Étant donné que les poignées de la solution ne peuvent être obtenues dans une application cliente sur le périphérique, un modèle consiste à enregistrer directement sur ce périphérique avec l’application cliente. D’autre part, considérations sur la sécurité et la logique métier liée aux balises amené à gérer des enregistrement de dispositif dans l’application back-end. 

#### <a name="templates"></a>Modèles

Si vous souhaitez utiliser les [modèles](notification-hubs-templates-cross-platform-push-messages.md), l’installation du périphérique doit aussi contenir tous les modèles associés à ce périphérique dans un JSON format (voir l’exemple ci-dessus). Les noms de modèles aident ciblent des modèles différents pour le même périphérique.

Notez que le nom de chaque modèle correspond à un corps de modèle et un ensemble facultatif de balises. En outre, chaque plate-forme peut avoir des propriétés de modèle supplémentaires. Pour Windows Store (à l’aide de WNS) et 8 de Windows Phone (à l’aide de MPNS), un ensemble d’en-têtes supplémentaire peut être partie du modèle. Dans le cas de APNs, vous pouvez définir une propriété d’expiration à une constante ou à une expression de modèle. Pour une liste complète de l’installation de propriétés rubrique, [créer ou remplacer une Installation avec reste](https://msdn.microsoft.com/library/azure/mt621153.aspx) .

#### <a name="secondary-tiles-for-windows-store-apps"></a>Vignettes secondaires pour les applications Windows Store

Pour les applications clientes de Windows Store, envoyer des notifications aux vignettes secondaires est identique à leur envoi vers le principal. Cela est également pris en charge dans des installations. Notez que les vignettes secondaires ont un ChannelUri différent, ce qui le Kit de développement de votre application client gère en toute transparence.

Le dictionnaire SecondaryTiles utilise la même TileId qui est utilisé pour créer l’objet SecondaryTiles dans votre application du Windows Store.
Comme avec le principal ChannelUri, ChannelUris de vignettes secondaires peuvent changer à tout moment. Pour éviter que les installations dans le concentrateur de notification mis à jour, le périphérique doit actualiser avec le ChannelUris actuel des mosaïques secondaires.


##<a name="registration-management-from-the-device"></a>Gestion de l’inscription de l’appareil

Lorsque vous gérez l’inscription de périphérique à partir d’applications clientes, le serveur principal n’est plus responsable de l’envoi de notifications. Les applications client poignées de la solution mise à jour et enregistrement des balises. L’image suivante illustre ce modèle.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Tout d’abord le périphérique récupère le handle de la solution de la solution, puis inscrit directement avec le hub de notification. Une fois l’inscription réussie, le back-end de l’application peut envoyer une notification que l’inscription de ciblage. Pour plus d’informations sur l’envoi de notifications, consultez [routage et les Expressions de la balise](notification-hubs-tags-segment-push-message.md).
Notez que dans ce cas, vous utiliserez écoute uniquement les droits d’accès de vos concentrateurs de notification à partir du périphérique. Pour plus d’informations, reportez-vous à la section [sécurité](notification-hubs-push-notification-security.md).

Inscription de l’appareil est la méthode la plus simple, mais il présente certains inconvénients.
Le premier inconvénient est qu’une application cliente peut uniquement mettre à jour ses balises lorsque l’application est active. Par exemple, si un utilisateur possède deux périphériques qui inscrivent des balises associées aux équipes de sport, lorsque le premier périphérique inscrit pour une balise supplémentaire (par exemple, Seahawks), le deuxième périphérique ne recevra pas les notifications sur le Seahawks jusqu'à ce que l’application sur le deuxième périphérique est exécutée une seconde fois. En règle générale, lorsque les balises sont affectés par plusieurs périphériques, la gestion des balises à partir du serveur principal est une option souhaitable.
Le deuxième inconvénient de la gestion de l’inscription à partir de l’application client est que, dans la mesure où les applications peuvent être piratées, sécurisation de l’inscription à des balises spécifiques nécessite prudent, comme expliqué dans la section « sécurité au niveau de l’étiquette. »



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Code exemple pour enregistrer avec un concentrateur de notification à partir d’un périphérique à l’aide d’une installation 

À ce stade, il est uniquement pris en charge à l’aide de l' [API REST de concentrateurs Notification](https://msdn.microsoft.com/library/mt621153.aspx).

Vous pouvez également utiliser la méthode de correctifs à l’aide de la [norme JSON-correctif](https://tools.ietf.org/html/rfc6902) de mise à jour de l’installation.

    class DeviceInstallation
    {
        public string installationId { get; set; }
        public string platform { get; set; }
        public string pushChannel { get; set; }
        public string[] tags { get; set; }
    }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
         string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                        "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }

   

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Code exemple pour enregistrer avec un concentrateur de notification à partir d’un périphérique à l’aide d’un enregistrement


Ces méthodes, créer ou mettre à jour un enregistrement pour le périphérique sur lequel ils sont appelés. Cela signifie que, pour mettre à jour de la poignée ou les balises, vous devez remplacer l’enregistrement entier. N’oubliez pas que les enregistrements sont transitoires, vous devez toujours un magasin fiable avec les balises actives qui a besoin d’un périphérique spécifique.


    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
    // storage. Then when the app starts, you can check if a registration id already exists or not before
    // creating.
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a registration id in application data, store in application data.
    if (!settings.ContainsKey("__NHRegistrationId"))
    {
        // make sure there are no existing registrations for this push handle (used for iOS and Android)    
        string newRegistrationId = null;
        var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
        foreach (RegistrationDescription registration in registrations)
        {
            if (newRegistrationId == null)
            {
                newRegistrationId = registration.RegistrationId;
            }
            else
            {
                await hub.DeleteRegistrationAsync(registration);
            }
        }

        newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
    }
     
    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

    try
    {
        await hub.CreateOrUpdateRegistrationAsync(registration);
    }
    catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
    {
        settings.Remove("__NHRegistrationId");
    }


## <a name="registration-management-from-a-backend"></a>Gestion de l’inscription à partir d’un serveur principal

Gestion des enregistrements à partir du serveur principal nécessite l’écriture de code supplémentaire. L’application à partir du périphérique doit fournir à gérer de la solution mise à jour sur le serveur principal chaque démarrage de l’application (ainsi que des balises et des modèles) et le serveur principal doit mettre à jour ce handle sur le concentrateur de notification. L’image suivante illustre cette conception.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Les avantages de la gestion des enregistrements à partir du serveur principal incluent la possibilité de modifier les balises pour les enregistrements même lorsque l’application correspondante sur le périphérique est inactive et d’authentifier l’application client avant d’ajouter une balise à son inscription.


#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Code exemple pour enregistrer avec un concentrateur de notification à partir d’un serveur principal à l’aide d’une installation

Le périphérique client obtient sa poignée de solution et des propriétés pertinentes installation avant toujours et appelle une API personnalisée sur le serveur principal qui peut effectuer l’inscription et autoriser des balises etc.. Le back-end peut exploiter le [SDK de concentrateur de Notification pour les opérations de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Vous pouvez également utiliser la méthode de correctifs à l’aide de la [norme JSON-correctif](https://tools.ietf.org/html/rfc6902) de mise à jour de l’installation.
 

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Code exemple pour enregistrer avec un concentrateur de notification à partir d’un périphérique à l’aide d’un id d’enregistrement

À partir de votre back-end de l’application, vous pouvez effectuer des opérations de CRUDS base sur des enregistrements. Par exemple :

    var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");
            
    // create a registration description object of the correct type, e.g.
    var reg = new WindowsRegistrationDescription(channelUri, tags);

    // Create
    await hub.CreateRegistrationAsync(reg);

    // Get by id
    var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

    // update
    r.Tags.Add("myTag");

    // update on hub
    await hub.UpdateRegistrationAsync(r);

    // delete
    await hub.DeleteRegistrationAsync(r);


Le serveur principal doit gérer l’accès concurrentiel entre les mises à jour de l’enregistrement. Bus de service offre un contrôle d’accès concurrentiel optimiste pour la gestion de l’inscription. Au niveau HTTP, cela est implémenté à l’aide d’ETag sur les opérations de gestion de l’inscription. Cette fonctionnalité est utilisée en toute transparence par Microsoft SDKs, qui lève une exception si une mise à jour est rejetée pour des raisons de concurrence d’accès. Le back-end de l’application est responsable de la gestion de ces exceptions et une nouvelle tentative de la mise à jour si nécessaire.