<properties
    pageTitle="Utilisez les rubriques du Bus de Service avec .NET | Microsoft Azure"
    description="Découvrez comment utiliser les thèmes du Bus de Service et des abonnements avec .NET dans Azure. Les exemples de code sont écrits pour les applications .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Comment faire pour utiliser des abonnements et des rubriques du Bus des services

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article décrit comment utiliser les abonnements et les rubriques du Bus de Service. Les exemples sont écrits en C# et utilisent les API .NET. Les scénarios présentés incluent la création de rubriques et les abonnements, la création de filtres d’abonnement, envoi de messages à une rubrique, recevoir des messages à partir d’un abonnement et supprimer des abonnements et des rubriques. Pour plus d’informations sur les rubriques et les abonnements, consultez la section [étapes suivantes](#next-steps) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configurer l’application pour utiliser le Bus de Service

Lorsque vous créez une application qui utilise le Bus des services, vous devez ajouter une référence à l’assembly de Service Bus et inclure des espaces de noms correspondant. Pour ce faire, le plus simple consiste à télécharger le package [NuGet](https://www.nuget.org) approprié.

## <a name="get-the-service-bus-nuget-package"></a>Obtenir le package NuGet de Bus de Service

Le [package NuGet de Bus de Service](https://www.nuget.org/packages/WindowsAzure.ServiceBus) est le moyen le plus simple pour obtenir l’API du Bus de Service et configurer votre application avec toutes les dépendances de Bus des services nécessaires. Pour installer le package NuGet de Bus de Service dans votre projet, effectuez les opérations suivantes :

1.  Dans l’Explorateur de solutions, cliquez sur **références**, puis cliquez sur **Gérer les Packages NuGet**.
2.  Recherchez « Bus de Service » et sélectionnez l’élément de **Bus des services Microsoft Azure** . Cliquez sur **installer** pour terminer l’installation, puis fermez la boîte de dialogue suivante :

    ![][7]

Vous êtes maintenant prêt à écrire le code pour le Bus de Service.

## <a name="create-a-service-bus-connection-string"></a>Créer une chaîne de connexion du Bus des services

Bus de service utilise une chaîne de connexion pour stocker les informations d’identification et de points de terminaison. Vous pouvez placer votre chaîne de connexion dans un fichier de configuration, plutôt que des coder en dur il :

- Lors de l’utilisation des services Azure, il est recommandé de stocker la chaîne de connexion en utilisant le système de configuration de service Azure (fichiers .csdef et .cscfg).
- Lors de l’utilisation des sites Web Azure ou ordinateurs virtuels Azure, il est recommandé de stocker la chaîne de connexion en utilisant le système de configuration .NET (par exemple, le fichier Web.config).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion à l’aide de la `CloudConfigurationManager.GetSetting` méthode, comme indiqué plus loin dans cet article.

### <a name="configure-your-connection-string"></a>Configurer votre chaîne de connexion

Le mécanisme de configuration de service vous permet de modifier dynamiquement les paramètres de configuration à partir du [portail Azure][] sans redéployer votre application. Par exemple, ajouter un `Setting` étiquette à votre fichier de définition (**.csdef**) service, comme illustré dans l’exemple suivant.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Puis, vous spécifiez des valeurs dans le fichier de configuration (.cscfg) du service.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Utilisez le nom de clé de Signature de l’accès partagé (SAS) et les valeurs de clé récupérées à partir du portail comme décrit précédemment.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configurer votre chaîne de connexion lors de l’utilisation des sites Web Azure ou ordinateurs virtuels Azure

Lors de l’utilisation des sites Web ou ordinateurs virtuels, il est recommandé d’utiliser le système de configuration .NET (par exemple, Web.config). Vous stockez la chaîne de connexion à l’aide de la `<appSettings>` élément.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Utilisez le nom d’associations de sécurité et les valeurs de clé que vous avez récupérée à partir du [portail Azure][], comme décrit précédemment.

## <a name="create-a-topic"></a>Créer une rubrique

Vous pouvez effectuer des opérations de gestion pour les rubriques du Bus de Service et des abonnements à l’aide de la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Cette classe fournit des méthodes pour créer, énumérer et supprimer des rubriques.

L’exemple suivant construit un `NamespaceManager` objet à l’aide de l’Azure `CloudConfigurationManager` d’informations d’identification de classe avec une chaîne de connexion comprenant l’adresse de base d’un espace de noms du Bus de Service et les associations de sécurité appropriées avec les autorisations pour le gérer. Cette chaîne de connexion est sous la forme suivante :

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Utilisez l’exemple suivant, selon les paramètres de configuration dans la section précédente.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Il existe des surcharges de la méthode [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) qui vous permettent de définir les propriétés de la rubrique ; par exemple, pour définir la valeur par défaut de valeur time-to-live (TTL) à appliquer aux messages envoyés à la rubrique. Ces paramètres sont appliqués à l’aide de la classe [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . L’exemple suivant montre comment créer une rubrique nommée **TestTopic** avec une taille maximale de 5 Go et d’un durée de vie de 1 minute du message par défaut.

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] Vous pouvez utiliser la méthode [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) sur les objets [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) pour vérifier si une rubrique avec un nom spécifié existe déjà dans un espace de noms.

## <a name="create-a-subscription"></a>Créer un abonnement

Vous pouvez également créer des abonnements de rubrique à l’aide de la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Les abonnements sont nommés et peuvent disposer d’un filtre facultatif qui restreint l’ensemble des messages transmis à la file d’attente virtuelle de l’abonnement.

> [AZURE.IMPORTANT] Afin que les messages soient reçus par un abonnement, vous devez créer cet abonnement avant d’envoyer des messages à la rubrique. S’il n’y a aucun abonnement à une rubrique, la rubrique ignore ces messages.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement, le filtre de le **MatchAll** est le filtre par défaut qui est utilisé. Lorsque vous utilisez le filtre de le **MatchAll** , tous les messages publiés dans la rubrique sont placés dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « AllMessages » et utilise le filtre de **MatchAll** par défaut.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec des filtres

Vous pouvez également définir des filtres qui vous permettent de spécifier lesquelles messages envoyés à une rubrique doivent apparaître dans un abonnement à une rubrique spécifique.

Le type de filtre pris en charge par les abonnements plus souple est la classe [SqlFilter][] , qui implémente un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur les expressions qui peuvent être utilisés avec un filtre SQL, consultez la syntaxe [SqlFilter.SqlExpression][] .

L’exemple suivant crée un abonnement nommé **HighMessages** avec un objet [SqlFilter][] qui sélectionne uniquement les messages qui ont une propriété personnalisée de **format** supérieure à 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

De la même façon, l’exemple suivant crée un abonnement nommé **LowMessages** avec un [SqlFilter][] qui sélectionne uniquement les messages qui ont une propriété **MessageNumber** inférieur ou égal à 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Maintenant lorsqu’un message est envoyé à `TestTopic`, il est toujours livré aux récepteurs souscrit à l’abonnement de la rubrique **AllMessages** et sélectivement remis aux récepteurs d’abonné pour les abonnements de rubrique **HighMessages** et **LowMessages** (selon le contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoyer des messages à une rubrique

Pour envoyer un message à une rubrique du Bus des services, l’application crée un objet [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) à l’aide de la chaîne de connexion.

Le code suivant montre comment créer un objet [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) correspondant à la rubrique **TestTopic** créé précédemment à l’aide de la [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) appel d’API.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Les messages envoyés aux rubriques du Bus de Service sont des instances de la classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) objets ont un ensemble de propriétés standard (par exemple, [l’étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et de [la propriété TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire qui est utilisée pour inclure des propriétés personnalisées spécifiques à l’application et un corps de données d’application arbitraire. Une application peut définir le corps du message en passant tout objet sérialisable au constructeur de l’objet [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) , et le approprié **DataContractSerializer** est ensuite utilisé pour sérialiser l’objet. Vous pouvez également **System.IO.Stream** peut être fourni.

L’exemple suivant montre comment envoyer des messages de test cinq à l’objet de [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) de **TestTopic** obtenu dans l’exemple de code précédent. Notez que la valeur de la propriété [format](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) de chaque message varie en fonction de l’itération de la boucle (Cela détermine les abonnements recevoir).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Rubriques du Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages conservés dans une rubrique, mais il existe un embout de la taille totale des messages émanant d’une rubrique. Cette taille de rubrique est définie au moment de la création, avec un maximum de 5 Go. Si le partitionnement est activé, la limite supérieure est plus élevée. Pour plus d’informations, voir [entités de messagerie partitionnées](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Comment recevoir des messages à partir d’un abonnement

La méthode recommandée pour recevoir des messages à partir d’un abonnement doit utiliser un objet [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) . Objets de [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) peuvent fonctionner dans deux modes différents : [ *ReceiveAndDelete* et *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Lors de l’utilisation du mode **ReceiveAndDelete** , recevoir est une opération de prise de vue unique ; Autrement dit, lorsque le Bus de Service reçoit une demande de lecture d’un message dans un abonnement, il marque le message comme étant consommé et retourne à l’application. **ReceiveAndDelete** est le modèle le plus simple et plus adapté à des scénarios dans lequel une application tolère ne pas traiter un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service a marqué le message comme étant consommé, lorsque l’application redémarre et commence à consommer des messages à nouveau, il est ont manqué au message qui a été utilisé avant l’incident.

En mode **PeekLock** (qui est le mode par défaut), le processus de réception devient une opération deux étapes, ce qui permet aux applications de prise en charge qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant [complète](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sur le message reçu. Lorsque le Bus des services voit la **complète** appeler, il marque le message comme étant consommé et le supprime de l’abonnement.

L’exemple suivant montre comment les messages peuvent être reçus et traitement à l’aide du mode **PeekLock** par défaut. Pour spécifier une valeur différente de [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , vous pouvez utiliser une autre surcharge pour [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Cet exemple utilise le rappel [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) pour traiter les messages dès leur arrivée dans l’abonnement **HighMessages** .

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Cet exemple configure le rappel [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) à l’aide d’un objet [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) . [La saisie semi-automatique](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) est définie à **false** pour activer les contrôles manuels de moment appeler [complète](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sur le message reçu. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) est défini à 1 minute, ce qui oblige le client à attendre jusqu'à une minute avant de mettre fin à la fonction de renouvellement automatique et le client effectue un nouvel appel à vérifier pour les messages. Valeur de cette propriété permet de réduire le nombre de fois que le client effectue des appels facturables qui ne récupèrent pas les messages.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, il peut appeler la méthode [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) sur le message reçu (au lieu de la méthode [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ). Dans ce cas déverrouiller le message dans l’abonnement et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de Bus des services.

Il existe également un délai d’expiration d’un message verrouillé au sein de l’abonnement, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis déverrouille le message automatiquement de Bus de Service et le rend disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message, mais avant que la demande [complète](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) est émise, le message sera remis à nouveau à l’application lorsqu’il redémarre. On parle souvent *de traitement au moins une fois*; en d’autres termes, chaque message est traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis les développeurs d’applications doivent ajouter une logique supplémentaire à leur application pour gérer la remise des messages en double. Cette opération est souvent réalisée à l’aide de la propriété [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) du message, qui reste constante entre les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer les rubriques et les abonnements

L’exemple suivant montre comment supprimer la rubrique **TestTopic** à partir de l’espace de noms du service **HowToSample** .

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Suppression d’une rubrique supprime également tous les abonnements qui sont enregistrés à l’aide de la rubrique. Les abonnements peuvent également être supprimés indépendamment. Le code suivant montre comment supprimer un abonnement nommé **HighMessages** à partir de la rubrique **TestTopic** .

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des rubriques du Bus de Service et des abonnements, cliquez sur ces liens pour en savoir plus.

-   [Files d’attente, des rubriques et des abonnements][].
-   [Exemples de filtres rubrique][]
-   Référence des API pour [SqlFilter][].
-   Créer une application qui envoie et reçoit des messages vers et à partir d’une file d’attente de Bus de Service : [Service Bus demandé de messagerie didacticiel de .NET][].
-   Exemples de Bus de service : télécharger à partir [d’échantillons d’Azure][] , ou consultez la [vue d’ensemble](service-bus-samples.md).

  [Azure portal]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Files d’attente, des rubriques et des abonnements]: service-bus-queues-topics-subscriptions.md
  [Exemples de filtres rubrique]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Bus de service demandé de messagerie .NET didacticiel]: service-bus-brokered-tutorial-dotnet.md
  [Exemples d’Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
