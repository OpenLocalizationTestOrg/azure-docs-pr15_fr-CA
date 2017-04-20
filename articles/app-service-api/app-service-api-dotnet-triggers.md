<properties 
    pageTitle="Service d’application API application déclencheurs | Microsoft Azure" 
    description="Comment implémenter des déclencheurs dans une application API dans le Service d’application Azure" 
    services="logic-apps" 
    documentationCenter=".net" 
    authors="guangyang"
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="na" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="rachelap"/>

# <a name="azure-app-service-api-app-triggers"></a>Déclencheurs d’application API du Service application Azure

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma API applications 2014-12-01-aperçu.


## <a name="overview"></a>Vue d’ensemble

Cet article explique comment implémenter des déclencheurs d’application API et les consommer à partir d’une application de logique.

Tous les extraits de code dans cette rubrique sont copiés à partir de l' [exemple de code FileWatcher API App](http://go.microsoft.com/fwlink/?LinkId=534802). 

Notez que vous devrez télécharger le package nuget suivant pour le code dans cet article pour générer et exécuter : [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/).

## <a name="what-are-api-app-triggers"></a>Quels sont les déclencheurs d’application API ?

Il s’agit d’un scénario courant pour une application API déclencher un événement, afin que les clients de l’application API prennent l’action appropriée en réponse à l’événement. Le mécanisme de l’API REST en fonction qui prend en charge ce scénario est appelé un déclencheur d’application API. 

Par exemple, supposons que votre code client à l’aide de l' [API de connecteur Twitter app](../app-service-logic/app-service-logic-connector-twitter.md) et votre code doit exécuter une action basée sur Nouveau tweet qui contiennent des mots spécifiques. Dans ce cas, vous pouvez définir un déclencheur de sondage ou push à répondre à ce besoin.

## <a name="poll-trigger-versus-push-trigger"></a>Déclencheur d’interrogation et le déclencheur d’émission

Actuellement, les deux types de déclencheurs sont prises en charge :

- Déclencheur d’interrogation - Client interroge l’application API pour la notification d’un événement a été déclenché. 
- Déclencheur d’émission - Client est averti par l’application API lorsqu’un événement est déclenché. 

### <a name="poll-trigger"></a>Déclencheur de sondage

Un déclencheur de sondage est implémenté comme une API REST régulière et attend de ses clients (par exemple, une application logique) pour les interrogent pour recevoir notification. Alors que le client peut conserver l’état, le déclencheur de l’interrogation elle-même est sans état. 

Les informations suivantes concernant les paquets de demande et de réponse illustrent certains aspects essentiels du contrat déclencheur sondage :

- Demande
    - Méthode HTTP : GET
    - Paramètres
        - triggerState - ce paramètre facultatif permet aux clients d’indiquer leur état pour que le déclencheur de sondage peut correctement décider s’il faut renvoyer la notification ou non en fonction de l’état spécifié.
        - Paramètres spécifiques de l’API
- Réponse
    - De code d’état **200** - la demande est valide, et il existe une notification à partir du déclencheur. Le contenu de la notification sera le corps de réponse. Un en-tête « Retry-After » dans la réponse indique que les données de notification supplémentaire doivent être récupérées avec un appel de la demande suivante.
    - Code de statut **202** - la demande est valide, mais qu’il n’y a aucune nouvelle notification à partir du déclencheur.
    - Code de statut **4xx** - la demande n’est pas valide. Le client ne doit pas réessayer la demande.
    - Code de statut **5xx** - demande a entraîné une erreur de serveur interne ou d’un problème temporaire. Le client doit réessayer la demande.

L’extrait de code suivant est un exemple d’implémentation d’un déclencheur de sondage.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

Pour tester ce déclencheur de sondage, procédez comme suit :

1. Déploiement de l’application API avec un paramètre d’authentification **anonymes**du public.
2. Appelez l’opération **tactile** pour toucher un fichier. L’image suivante montre un exemple de demande via Postman.
   ![Appeler une opération tactile via Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. Appelez le déclencheur d’interrogation avec le paramètre **triggerState** défini sur la date et l’heure avant l’étape n ° 2. L’image suivante affiche l’exemple de requête via Postman.
   ![Déclencheur de sondage appel via Postman](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### <a name="push-trigger"></a>Déclencheur d’émission

Un déclencheur d’émission est implémenté comme une API REST régulière qui pousse les notifications aux clients qui ont demandé à être informé lorsque des événements spécifiques surviennent.

Les informations suivantes concernant les paquets de demande et de réponse illustrent certains aspects essentiels du contrat déclencheur par émission.

- Demande
    - Méthode HTTP : placez
    - Paramètres
        - triggerId : obligatoire - chaîne Opaque (par exemple, un GUID) qui représente l’enregistrement d’un déclencheur d’émission.
        - callbackUrl : obligatoire - URL de rappel à appeler lorsque l’événement se déclenche. L’appel est un simple appel HTTP POST.
        - Paramètres spécifiques de l’API
- Réponse
    - État code **200** - demande d’enregistrement client réussie.
    - Code de statut **4xx** - la demande n’est pas valide. Le client ne doit pas réessayer la demande.
    - Code de statut **5xx** - demande a entraîné une erreur de serveur interne ou d’un problème temporaire. Le client doit réessayer la demande.
- Rappel
    - Méthode HTTP : POST
    - Corps de requête : contenu de la Notification.

L’extrait de code suivant est un exemple d’implémentation d’un déclencheur d’émission :

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

Pour tester ce déclencheur de sondage, procédez comme suit :

1. Déploiement de l’application API avec un paramètre d’authentification **anonymes**du public.
2. Accédez à [http://requestb.in/](http://requestb.in/) pour créer un RequestBin qui servira de votre URL de rappel.
3. Appelez le déclencheur d’émission avec un GUID comme **triggerId** et le RequestBin de l’URL **callbackUrl**.
   ![Déclencheur d’émission appel via Postman](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. Appelez l’opération **tactile** pour toucher un fichier. L’image suivante montre un exemple de demande via Postman.
   ![Appeler une opération tactile via Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. Vérifiez le RequestBin pour confirmer que le rappel de déclencheur d’émission est appelé avec la sortie de la propriété.
   ![Déclencheur de sondage appel via Postman](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### <a name="describe-triggers-in-api-definition"></a>Décrire les déclencheurs dans la définition de l’API

Après les déclencheurs de mise en œuvre et le déploiement de votre application d’API vers Azure, accédez à la lame de **Définition des API** dans le portail Azure aperçu et vous verrez que les déclencheurs sont automatiquement reconnus dans l’interface utilisateur, qui est contrôlée par la définition de Swagger des API 2.0 de l’application API.

![Lame de définition d’API](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

Si vous cliquez sur le bouton de **Téléchargement Swagger** et que vous ouvrez le fichier JSON, vous verrez des résultats similaires à ce qui suit :

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

Propriété extension **x-ms-programmation-déclencheur** est comment les déclencheurs sont décrits dans la définition de l’API et est automatiquement ajouté par la passerelle d’application API lorsque vous demandez la définition de l’API via la passerelle, si la demande à l’un des critères suivants. (Vous pouvez également ajouter cette propriété manuellement.)

- Déclencheur de sondage
    - Si la méthode HTTP **GET**.
    - Si la propriété **operationId** contient la chaîne **déclencheur**.
    - Si la propriété de **paramètres** comprend un paramètre avec une propriété **name** la valeur **triggerState**.
- Déclencheur d’émission
    - Si la méthode HTTP est **PUT**.
    - Si la propriété **operationId** contient la chaîne **déclencheur**.
    - Si la propriété de **paramètres** comprend un paramètre avec une propriété **name** la valeur **triggerId**.

## <a name="use-api-app-triggers-in-logic-apps"></a>Utiliser des déclencheurs d’application API dans les applications de logique

### <a name="list-and-configure-api-app-triggers-in-the-logic-apps-designer"></a>Lister et configurer des déclencheurs d’application API dans le Concepteur d’applications logique

Si vous créez une application logique dans le même groupe de ressources que l’application de l’API, vous ne pourrez pas les ajouter à la zone du concepteur simplement en cliquant dessus. Les images suivantes illustrent ce principe :

![Déclencheurs dans le Concepteur d’application logique](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![Configurer le déclenchement de la sonde dans le Concepteur d’application logique](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![Configurer le déclencheur d’émission dans le Concepteur d’application logique](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## <a name="optimize-api-app-triggers-for-logic-apps"></a>Optimiser les déclencheurs d’application API pour les applications de logique

Une fois que vous ajoutez des déclencheurs à une application de l’API, il y a quelques choses à que faire pour améliorer l’expérience lors de l’utilisation de l’application API dans une logique d’application.

Par exemple, le paramètre **triggerState** pour les déclencheurs de sondage doit indiquer l’expression suivante dans la logique d’application. Cette expression doit évaluer le dernier appel du déclencheur à partir de l’application logique et renvoyer cette valeur.  

    @coalesce(triggers()?.outputs?.body?['triggerState'], '')

Remarque : Pour obtenir une explication des fonctions utilisées dans l’expression ci-dessus, reportez-vous à la documentation sur le [Langage de définition de flux de travail logique d’application](https://msdn.microsoft.com/library/azure/dn948512.aspx).

Les utilisateurs de logique app doivent fournir l’expression ci-dessus pour le paramètre **triggerState** lors de l’utilisation du déclencheur. Il est possible d’avoir cette valeur prédéfinie par le Concepteur d’application logique par le biais de la propriété d’extension **x-ms-planificateur-recommandation**.  La propriété d’extension **x-ms-visibilité** peut être définie à la valeur *interne* afin que le paramètre lui-même n’est pas affiché dans le concepteur.  L’extrait de code suivant illustre que.

    "/api/Messages/poll": {
      "get": {
        "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

Pour les triggers de la transmission, le paramètre **triggerId** doit identifier de manière unique l’application logique. Il est recommandé de paramétrer cette propriété sur le nom du flux de travail à l’aide de l’expression suivante :

    @workflow().name

En utilisant les propriétés d’extension **x-ms-planificateur-recommandation** et **x-ms-visibilité** dans sa définition d’API, l’application API peut transmettre au concepteur Application logique pour définir automatiquement cette expression pour l’utilisateur.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### <a name="add-extension-properties-in-api-defintion"></a>Ajouter des propriétés de l’extension dans la définition de l’API

-Par exemple l’extension propriétés **x-ms-planificateur-recommandation** et **x-ms-visibilité** - les informations de métadonnées supplémentaires peuvent être ajoutées à la définition de l’API dans une des deux façons : statique ou dynamique.

Pour les métadonnées statiques, vous pouvez directement modifier le fichier */metadata/apiDefinition.swagger.json* dans votre projet et ajoutez les propriétés manuellement.

Pour les applications d’API à l’aide de métadonnées dynamiques, vous pouvez modifier le fichier SwaggerConfig.cs pour ajouter un filtre d’opération qui peut ajouter ces extensions.

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


Voici un exemple de la façon dont cette classe peut être implémentée pour faciliter le scénario de métadonnées dynamique.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }
 
