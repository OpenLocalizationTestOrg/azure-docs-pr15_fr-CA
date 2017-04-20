<properties
    pageTitle="Surveillez votre API avec une API Azure Management, événement et les concentrateurs Runscope"
    description="Exemple d’application illustrant la stratégie journal-à-eventhub par connexion Azure API gestion, Azure événement concentrateurs et Runscope pour HTTP de journalisation et la surveillance"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Surveillez votre API avec une API Azure Management, événement et les concentrateurs Runscope

Le [service de gestion de l’API](api-management-key-concepts.md) fournit de nombreuses fonctionnalités pour améliorer le traitement des demandes HTTP envoyées à votre API HTTP. Toutefois, l’existence des demandes et des réponses sont transitoires. La demande est effectuée et il transite par le service de gestion de l’API à la principale API. Votre API traite la demande et une réponse transmise par le biais de pour le consommateur de l’API. Le service de gestion de l’API conserve certaines statistiques importantes sur les API pour l’affichage dans le tableau de bord de portail de Publisher, mais après que les informations ont disparu.

À l’aide de la [stratégie](api-management-howto-policies.md) de [journal-à-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) dans le service de gestion de l’API, vous pouvez envoyer les détails de la demande et la réponse à un [Concentrateur d’événements Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Il existe de nombreuses raisons, raison pour laquelle vous souhaitez générer des événements à partir de messages HTTP envoyés à votre API. Certains exemples incluent la piste de vérification des mises à jour, analytique de l’utilisation, des alertes de l’exception et les intégrations tierces 3e.   

Cet article montre comment capturer le message de demande et de réponse HTTP entière, envoyez-le à un concentrateur d’événements et ensuite relayer ce message à un service qui fournit des HTTP de journalisation et la surveillance des services tiers.

## <a name="why-send-from-api-management-service"></a>Pourquoi envoyer à partir du Service de gestion d’API ?
Il est possible d’écrire des logiciels intermédiaires HTTP qui peut s’intégrer dans les infrastructures de l’API HTTP pour capturer les requêtes et réponses HTTP et les flux dans la journalisation et la surveillance des systèmes. L’inconvénient de cette approche est le middleware HTTP doit être intégrées dans l’API de serveur principal et doit correspondre à la plate-forme de l’API. S’il existe plusieurs API chacun d’eux doit déployer les logiciels intermédiaires. Il y a souvent des raisons pourquoi back-end API ne peut pas être mis à jour.

À l’aide du service de gestion d’API Azure pour s’intégrer à l’infrastructure de journalisation fournit une solution centralisée et indépendante de la plate-forme. Il est également extensible, en partie en raison des fonctions de [réplication géographique](api-management-howto-deploy-multi-region.md) Azure API de gestion des.

## <a name="why-send-to-an-azure-event-hub"></a>Pourquoi envoyer à un concentrateur d’événements Azure ?
Il est une judicieux de demander, pourquoi créer une stratégie qui est spécifique à des concentrateurs d’événement Azure ? Il existe plusieurs endroits où je souhaite avoir mes demandes. Pourquoi ne pas simplement envoyer les demandes directement à la destination finale ?  C’est une option. Toutefois, lors de la création des demandes d’enregistrement à partir d’un service de gestion d’API, il est nécessaire à prendre en compte l’impact de journalisation des messages sur les performances de l’API. Augmentation progressive de charge peut être traitées en augmentant les instances disponibles de composants système ou en tirant parti de la réplication de la zone géographique. Toutefois, courts pointes de trafic peuvent entraîner des demandes être retardait considérablement si les demandes à l’infrastructure de consignation démarrent ralentir sous charge.

Les concentrateurs d’événement Azure vise à pénétration énormes volumes de données, avec la capacité de gérer un nombre beaucoup plu d’événements que le nombre de requêtes HTTP des processus de l’API. Le concentrateur d’événements agit comme une sorte de tampon sophistiqué entre votre service de gestion d’API et de l’infrastructure qui doit stocker et traiter les messages. Cela garantit que les performances de votre API ne seront pas détérioré en raison de l’infrastructure de journalisation.  

Une fois que les données a été passées à un concentrateur d’événements qu’il est persistant et attendra pour les consommateurs de concentrateur d’événements à traiter. Le concentrateur d’événements ne se soucie pas du mode de traitement, il respecte simplement vous assurer que le message sera remis avec succès.     

Événement concentrateurs ont la possibilité pour les événements de flux de données à plusieurs groupes de consommateurs. Ainsi, les événements à traiter par les systèmes totalement différents. Cela permet la prise en charge de nombreux scénarios d’intégration sans la placer des retards de plus sur le traitement de la demande de l’API du service de gestion de l’API comme un seul événement doit être générée.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Une stratégie pour envoyer des messages de l’application/http
Un concentrateur d’événements accepte les données d’événement comme une chaîne simple. Le contenu de cette chaîne est complètement à vous. Pour être en mesure de créer un package d’une requête HTTP avant de l’envoyer à des concentrateurs d’événement, nous devons mettre en forme la chaîne avec les informations de la demande ou la réponse. Dans des situations comme celle-ci, s’il existe un format existant que nous pouvons réutiliser, puis nous pouvons avoir à écrire notre propre de l’analyse du code. Initialement j’envisagé à l’aide de la [QAR](http://www.softwareishard.com/blog/har-12-spec/) pour envoyer des demandes et réponses HTTP. Cependant, ce format est optimisé pour le stockage d’une séquence de requêtes HTTP dans un format JSON en fonction. Il contenue un certain nombre d’éléments obligatoires qu’ajouter une complexité inutile pour le scénario de transmettre le message HTTP sur le réseau.  

Une autre option est d’utiliser la `application/http` type de média comme décrit dans la spécification HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Ce type de média utilise le même format que celui qui est utilisé pour envoyer des messages HTTP sur le réseau, mais l’ensemble du message peut être placé dans le corps d’une autre demande HTTP. Dans notre cas, nous allons simplement à utiliser le corps comme notre message à envoyer à des concentrateurs de l’événement. Heureusement, est un analyseur qui existe dans les bibliothèques de [Client 2.2 des API Web Microsoft ASP.NET](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) qui peuvent analyser ce format et le convertir en natif `HttpRequestMessage` et `HttpResponseMessage` objets.

Pour être en mesure de créer ce message que nous devons tirer parti de C# en fonction des [expressions de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx) dans la gestion des API Azure. Voici la stratégie qui envoie un message de requête HTTP à des concentrateurs d’événement Azure.

       <log-to-eventhub logger-id="conferencelogger" partition-id="0">
       @{
           var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                       context.Request.Method,
                                                       context.Request.Url.Path + context.Request.Url.QueryString);

           var body = context.Request.Body?.As<string>(true);
           if (body != null && body.Length > 1024)
           {
               body = body.Substring(0, 1024);
           }

           var headers = context.Request.Headers
                                  .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                  .ToArray<string>();

           var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

           return "request:"   + context.Variables["message-id"] + "\n"
                               + requestLine + headerString + "\r\n" + body;
       }
       </log-to-eventhub>

### <a name="policy-declaration"></a>Déclaration de stratégie
Il quelques éléments notamment important de mentionner sur cette expression de stratégie. La stratégie de journal-à-eventhub possède un attribut nommé id journal qui fait référence au nom de journal qui a été créé dans le service de gestion de l’API. Vous trouverez les détails de la façon de configurer un journal de concentrateur d’événements dans le service de gestion de l’API dans le document [comment enregistrer des événements dans les concentrateurs d’événement Azure dans Azure API de gestion](api-management-howto-log-event-hubs.md). Le deuxième attribut est un paramètre facultatif qui indique les concentrateurs d’événement qui partitionnent pour stocker le message dans. Événement concentrateurs utilisent des partitions pour activer l’expansion et nécessitent un minimum de deux. La livraison chronologique des messages est garantie uniquement au sein d’une partition. Si nous ne pas demander à concentrateur d’événements dans la partition dans laquelle placer le message, il utilise un algorithme de répétition alternée pour répartir la charge. Toutefois, qui peut provoquer certains de nos messages pour être traités dans l’ordre.  

### <a name="partitions"></a>Partitions
Pour garantir à nos messages sont remis aux consommateurs dans l’ordre et tirent parti de la fonctionnalité de distribution de charge de partitions, j’ai choisi envoyer des messages de demande HTTP à une partition et les messages de réponse HTTP sur une deuxième partition. Cela permet de garantir une distribution de charge même et que nous pouvons garantir que toutes les demandes seront consommées dans l’ordre et toutes les réponses seront consommées dans l’ordre. Il est possible pour une réponse à consommer avant la demande correspondante, mais qui n’est pas un problème que nous avons un autre mécanisme de corrélation demande pour les réponses et nous savons que les demandes sont toujours placés avant les réponses.

### <a name="http-payloads"></a>Charges HTTP
Après la génération du `requestLine` nous vérifions si le corps de la demande doit être tronqué. Le corps de la demande est tronqué à 1024 uniquement. Cela pourrait être augmenté, mais des messages d’événement Hub sont limitées à 256 Ko, il est probable que certains messages HTTP organismes ne tient ne pas dans un seul message. Lors de la journalisation et analytique une quantité importante d’informations peut être dérivée uniquement les fichiers et les en-têtes de ligne de requête HTTP. Également, de nombreuses demandes d’API ne renvoient que les petits organismes, et la perte de valeur des informations en tronquant les grands organismes est minime par rapport à la réduction du transfert, de traitement et les coûts de stockage pour conserver tous les contenus de corps. Une dernière remarque sur le traitement de l’organisme est que nous devons transmettre `true` à la<string>méthode () nous lisons le contenu du corps, mais n’a pas été aussi souhaitez la principale API pour être en mesure de lire le corps. En passant la valeur True pour cette méthode, nous entraîner le corps en mémoire tampon afin qu’il puisse être lu une deuxième fois. Il est important de savoir si vous avez une API qui ne le téléchargement de fichiers volumineux ou d’utilise l’interrogation longue. Dans ces cas, il serait préférable d’éviter la lecture du corps du tout.   

### <a name="http-headers"></a>En-têtes HTTP
En-têtes HTTP peuvent être simplement transférées via dans le format de message dans un format de paire clé/valeur simples. Nous avons choisi de retirer certains champs sensibles de sécurité, pour éviter toute fuite inutilement des informations d’identification. Il est peu probable que clés de l’API et d’autres informations d’identification peut servir à des fins d’analytique. Si nous souhaitons effectuer une analyse sur l’utilisateur et le produit qu’ils utilisent, puis nous pouvons obtenir que de la `context` objet et ajoutez au message.     
### <a name="message-metadata"></a>Métadonnées du message
Lorsque vous créez le message complet à envoyer au hub d’événement, la première ligne n’est pas réellement partie de la `application/http` message. La première ligne est consistant si le message est une demande ou de message de réponse et d’un identificateur qui est utilisé pour faire correspondre les demandes aux réponses des métadonnées supplémentaires. L’id de message est créé à l’aide d’une autre stratégie qui se présente comme suit :

    <set-variable name="message-id" value="@(Guid.NewGuid())" />

Nous pourrions avoir créé le message de demande, stocké dans une variable jusqu'à ce que la réponse a été retournée et puis simplement envoyée la demande et la réponse sous la forme d’un seul message. Toutefois, en envoyant la demande et la réponse d’indépendamment et à l’aide d’un id de message pour faire correspondre les deux, nous obtenons un peu plus de souplesse dans la taille du message, la possibilité de bénéficier de plusieurs partitions tandis que l’ordre des messages et la demande de mise à jour s’affichera plus tôt dans notre tableau de bord de journalisation. Il peut aussi exister des scénarios où une réponse valide n’est jamais envoyée sur le concentrateur d’événements, probablement en raison d’une erreur irrécupérable de demande dans le service de gestion de l’API, mais nous aura encore un enregistrement de la demande.

La stratégie pour envoyer le message de réponse HTTP est très similaire à la demande et par conséquent, la configuration de la stratégie complète se présente comme suit :

      <policies>
        <inbound>
            <set-variable name="message-id" value="@(Guid.NewGuid())" />
            <log-to-eventhub logger-id="conferencelogger" partition-id="0">
              @{
                  var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                              context.Request.Method,
                                                              context.Request.Url.Path + context.Request.Url.QueryString);

                  var body = context.Request.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Request.Headers
                                       .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                       .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                       .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "request:"   + context.Variables["message-id"] + "\n"
                                      + requestLine + headerString + "\r\n" + body;
              }
          </log-to-eventhub>
        </inbound>
        <backend>
            <forward-request follow-redirects="true" />
        </backend>
        <outbound>
            <log-to-eventhub logger-id="conferencelogger" partition-id="1">
              @{
                  var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                                      context.Response.StatusCode,
                                                      context.Response.StatusReason);

                  var body = context.Response.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Response.Headers
                                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                                  .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "response:"  + context.Variables["message-id"] + "\n"
                                      + statusLine + headerString + "\r\n" + body;
             }
          </log-to-eventhub>
        </outbound>
      </policies>

Le `set-variable` stratégie crée une valeur qui est accessible par les deux le `log-to-eventhub` stratégie de la `<inbound>` section et la `<outbound>` section.  

## <a name="receiving-events-from-event-hubs"></a>Recevoir des événements de concentrateurs d’événement
Réception d’événements à partir du concentrateur d’événements Azure en utilisant le [protocole AMQP](http://www.amqp.org/). L’équipe du Bus des services Microsoft ont apportées client pour faciliter les événements beaucoup de bibliothèques. Il existe deux approches différentes prises en charge, un est un *Consommateur directe* et l’autre est à l’aide de la `EventProcessorHost` classe. Vous trouverez des exemples de ces deux approches dans le [Guide de programmation des concentrateurs d’événement](../event-hubs/event-hubs-programming-guide.md). La version courte des différences est, `Direct Consumer` vous permet de contrôler et de la `EventProcessorHost` d’intervient la plomberie pour vous mais en faisant certaines suppositions à propos de la façon dont vous allez traiter ces événements.  

### <a name="eventprocessorhost"></a>EventProcessorHost
Dans cet exemple, nous allons utiliser le `EventProcessorHost` pour plus de simplicité, toutefois il ne peut-être pas le meilleur choix pour ce scénario particulier. `EventProcessorHost`effectue le travail de vous assurer que vous n’avez pas à vous soucier de problèmes au sein d’une classe événement particulier du processeur de thread. Toutefois, dans notre scénario, nous allons simplement convertir le message dans un autre format et en lui passant le long d’un autre service à l’aide d’une méthode asynchrone. N’est pas nécessaire de mettre à jour un état partagé et donc aucun risque de problèmes liés aux threads. La plupart des scénarios, `EventProcessorHost` est probablement le meilleur choix et il est certainement l’option plus facile.     

### <a name="ieventprocessor"></a>IEventProcessor
Le concept central lors de l’utilisation `EventProcessorHost` consiste à créer une implémentation de la `IEventProcessor` interface qui contient la méthode `ProcessEventAsync`. L’essence de cette méthode est indiqué ici :

  Async {de la tâche IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)

           foreach (EventData eventData in messages)
           {
               _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

               try
               {
                   var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
                   await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
               }
               catch (Exception ex)
               {
                   _Logger.LogError(ex.Message);
               }
           }
            ... checkpointing code snipped ...
        }

Une liste d’objets de EventData sont passés à la méthode et nous itérer sur cette liste. Les octets de chaque méthode sont analysés dans un objet HttpMessage et cet objet est passé à une instance de IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
Le `HttpMessage` instance contient trois éléments de données :

      public class HttpMessage
       {
           public Guid MessageId { get; set; }
           public bool IsRequest { get; set; }
           public HttpRequestMessage HttpRequestMessage { get; set; }
           public HttpResponseMessage HttpResponseMessage { get; set; }

        ... parsing code snipped ...

      }

Le `HttpMessage` instance contient un `MessageId` GUID qui nous permet de vous connecter la demande HTTP pour la réponse HTTP correspondante et une valeur de type boolean qui indique si l’objet contient une instance de HttpRequestMessage et HttpResponseMessage. À l’aide de la génération dans des classes HTTP de `System.Net.Http`, j’étais en mesure de tirer parti de la `application/http` l’analyse du code qui est inclus dans `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
Le `HttpMessage` instance est ensuite transmis à la mise en œuvre de `IHttpMessageProcessor` qui est une interface que j’ai créé pour découpler la réception et l’interprétation de l’événement du concentrateur d’événements Azure et le traitement effectif de celui-ci.


## <a name="forwarding-the-http-message"></a>Transférer le message HTTP
Pour cet exemple, j’ai décidé qu’il serait intéressant de la poussée sur la demande HTTP à [Runscope](http://www.runscope.com). Runscope est un service en nuage basé spécialisée dans HTTP de débogage, la journalisation et la surveillance. Ils ont une couche libre, il est facile à essayer et il nous permet de voir les requêtes HTTP dans une transmission en temps réel par le biais de notre service de gestion de l’API.

Le `IHttpMessageProcessor` implémentation se présente comme suit,

      public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
       {
           private HttpClient _HttpClient;
           private ILogger _Logger;
           private string _BucketKey;
           public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
           {
               _HttpClient = httpClient;
               var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
               _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
               _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
               _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
               _Logger = logger;
           }

           public async Task ProcessHttpMessage(HttpMessage message)
           {
               var runscopeMessage = new RunscopeMessage()
               {
                   UniqueIdentifier = message.MessageId
               };

               if (message.IsRequest)
               {
                   _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
                   runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
               }
               else
               {
                   _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
                   runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
               }

               var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
               messagesLink.BucketKey = _BucketKey;
               messagesLink.RunscopeMessage = runscopeMessage;
               var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
               _Logger.LogDebug("Request sent to Runscope");
           }
       }

J’ai réussi à tirer parti d’une [bibliothèque de client existant pour Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) qui facilite la transmission `HttpRequestMessage` et `HttpResponseMessage` instances haut dans leur service. Pour accéder à l’API Runscope vous devez un compte et une clé d’API. Vous trouverez des instructions pour obtenir une clé API dans l’enregistrement de la [Création d’Applications à l’API d’accès aux Runscope](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) .

## <a name="complete-sample"></a>Exemple complet
Le [code source](https://github.com/darrelmiller/ApimEventProcessor) et les tests de l’exemple sont sur Github. Vous aurez besoin d’une [API de gestion de Service](api-management-get-started.md), [Un concentrateur d’événements connectés](api-management-howto-log-event-hubs.md)et un [Compte de stockage](../storage/storage-create-storage-account.md) pour exécuter l’exemple vous-même.   

L’exemple est juste une simple application de Console qui est à l’écoute d’événements en provenance de concentrateur d’événements, les convertit en un `HttpRequestMessage` et `HttpResponseMessage` d’objets et les transmet ensuite à l’API Runscope.

Dans l’image animée suivante, vous pouvez voir une demande à une API dans le portail destiné aux développeurs, l’application Console affiche le message reçu, traitée et transmise et puis la demande et la réponse s’affiche dans l’inspecteur de trafic de Runscope.

![Démonstration de la demande transmis à Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Résumé
Service de gestion de l’API d’Azure fournit un endroit idéal pour capturer le trafic HTTP de voyage vers et à partir de votre API. Concentrateurs d’événement Azure est une solution hautement évolutive et économique pour capturer le trafic et d’intégrer les systèmes de traitement secondaire pour la journalisation, surveillance et autres sophistiquées analytique. Connexion à la 3ème partie de surveillance du trafic systèmes tels que Runscope est un simple en quelques dizaines de lignes de code.

## <a name="next-steps"></a>Étapes suivantes
-   En savoir plus sur les concentrateurs d’événement Azure
    -   [Mise en route avec les concentrateurs d’événement Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Recevoir des messages avec EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Concentrateurs d’événement guide de programmation](../event-hubs/event-hubs-programming-guide.md)
-   Pour en savoir plus sur l’intégration de la gestion de l’API et les concentrateurs d’événement
    -   [Comment enregistrer des événements à des concentrateurs d’événement Azure dans la gestion des API Azure](api-management-howto-log-event-hubs.md)
    -   [Référence d’entité de journal](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [référence de la stratégie de journal-à-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    