<properties
    pageTitle="L’utilisation des thèmes du Bus de Service avec Java | Microsoft Azure"
    description="Apprenez à utiliser des thèmes du Bus de Service et des abonnements dans Azure. Les exemples de code sont écrits pour les applications Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Comment faire pour utiliser des abonnements et des rubriques du Bus des services

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ce guide explique comment utiliser les abonnements et les rubriques du Bus de Service. Les exemples sont écrits en Java et utilisent le [Azure SDK pour Java][]. Les scénarios présentés incluent la **Création de rubriques et les abonnements**, **Création de filtres d’abonnement**, **Envoyer des messages à une rubrique**, **recevoir des messages à partir d’un abonnement**et **suppression des rubriques et des abonnements**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Quels sont les abonnements et les rubriques du Bus de Service ?

Les abonnements et les rubriques du Bus de Service prend en charge d’une *publication et d’abonnement* modèle de communication de messagerie. Lorsque vous utilisez des rubriques et des abonnements, les composants d’une application distribuée ne communiquent pas directement avec eux ; au lieu de cela ils échangent des messages via une rubrique, qui sert d’intermédiaire.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Contrairement aux files d’attente de Bus de Service, dans lequel chaque message est traité par un seul consommateur, les abonnements et les rubriques fournissent un formulaire « un-à-plusieurs » de la communication, à l’aide d’un modèle de publication et d’abonnement. Il est possible d’enregistrer plusieurs abonnements à une rubrique. Lorsqu’un message est envoyé à une rubrique, puis le rend disponible à chaque abonnement à poignée/processus indépendamment.

Un abonnement à une rubrique ressemble à une file d’attente virtuelle qui reçoit une copie des messages qui ont été envoyés à la rubrique. Vous pouvez enregistrer les règles de filtrage pour une rubrique sur une base par abonnement, ce qui vous permet de restreindre/filtrer une rubrique de messages qui sont reçus par les abonnements de la rubrique.

Les abonnements et les rubriques du Bus de Service permettent de mettre à l’échelle pour traiter un très grand nombre de messages sur un très grand nombre d’utilisateurs et d’applications.

## <a name="create-a-service-namespace"></a>Créer un espace de noms du service

Pour commencer à utiliser des thèmes du Bus de Service et des abonnements dans Azure, vous devez d’abord créer un espace de noms du service. Un espace de noms fournit un conteneur de portée pour l’adressage des ressources de Bus de Service au sein de votre application.

Pour créer un espace de noms :

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser le Bus de Service

Assurez-vous que vous avez installé le [Azure SDK pour Java][] avant la génération de cet exemple. Si vous utilisez Eclipse, vous pouvez installer le [Shared Computer Toolkit Azure pour Eclipse][] qui inclut l’Azure SDK pour Java. Vous pouvez ensuite ajouter les **Bibliothèques d’Azure Microsoft pour Java** à votre projet :

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Ajoutez les instructions d’importation suivantes en haut du fichier Java :

```
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Ajouter les bibliothèques Azure pour Java à votre chemin d’accès de build et l’inclure dans votre assembly de déploiement du projet.

## <a name="create-a-topic"></a>Créer une rubrique

Les opérations de gestion des rubriques du Bus de Service peuvent être effectuées via la classe **ServiceBusContract** . Construit un objet **ServiceBusContract** avec une configuration appropriée qui encapsule le jeton d’associations de sécurité avec les autorisations pour gérer, et la classe **ServiceBusContract** est le seul point de communication avec Azure.

La classe **ServiceBusService** fournit des méthodes pour créer, énumérer et supprimer des rubriques. L’exemple suivant montre comment un objet **ServiceBusService** peut être utilisé pour créer une rubrique nommée `TestTopic`, avec un espace de noms appelé `HowToSample`:

    Configuration config =
        ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

    ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
    try  
    {
        CreateTopicResult result = service.createTopic(topicInfo);
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Sur **TopicInfo** , les méthodes qui permettent de la rubrique pour définir les propriétés (par exemple : la valeur par défaut time-to-live (TTL) à appliquer aux messages envoyés à la rubrique). L’exemple suivant montre comment créer une rubrique nommée `TestTopic` avec une taille maximale de 5 Go :

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

Notez que vous pouvez utiliser la méthode **listTopics** sur les objets de **ServiceBusContract** pour vérifier si une rubrique avec un nom spécifié existe déjà dans un espace de noms du service.

## <a name="create-subscriptions"></a>Créer des abonnements

Abonnements aux rubriques sont également créés avec la classe **ServiceBusService** . Les abonnements sont nommés et peuvent disposer d’un filtre facultatif qui restreint l’ensemble des messages transmis à la file d’attente virtuelle de l’abonnement.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Le filtre de le **MatchAll** est le filtre par défaut qui est utilisé si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement. Lorsque le filtre de le **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « AllMessages » et utilise le filtre de **MatchAll** par défaut.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec des filtres

Vous pouvez également créer des filtres qui vous permettent de portée de messages envoyés à une rubrique doivent s’afficher dans un abonnement à une rubrique spécifique.

Le type de filtre pris en charge par les abonnements plus souple est le [SqlFilter][], qui implémente un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur les expressions qui peuvent être utilisées avec un filtre SQL, passez en revue la syntaxe [SqlFilter.SqlExpression][] .

L’exemple suivant crée un abonnement nommé `HighMessages` avec un objet [SqlFilter][] qui sélectionne uniquement les messages qui ont une propriété personnalisée de **format** supérieure à 3 :

```
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

De la même façon, l’exemple suivant crée un abonnement nommé `LowMessages` avec un objet [SqlFilter][] qui sélectionne uniquement les messages qui ont une propriété **MessageNumber** inférieure ou égale à 3 :

```
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Lorsqu’un message est maintenant envoyé à `TestTopic`, elle est envoyée à l’abonné à des récepteurs la `AllMessages` abonnement et sélectivement livrée aux récepteurs d’abonné à la `HighMessages` et `LowMessages` abonnements (en fonction du contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoyer des messages à une rubrique

Pour envoyer un message à une rubrique du Bus des services, votre application obtient un objet **ServiceBusContract** . Le code suivant montre comment envoyer un message pour le `TestTopic` rubrique créé précédemment dans le `HowToSample` espace de noms :

```
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Les messages envoyés aux rubriques du Bus de Service sont des instances de la classe [BrokeredMessage][] . [BrokeredMessage][] *objets comportent un ensemble de méthodes standard (tel que * *setLabel* * et * *la propriété TimeToLive**), d’un dictionnaire qui est utilisée pour inclure des propriétés personnalisées spécifiques à l’application et un corps de données d’application arbitraire. Une application peut définir le corps du message en passant tout objet sérialisable dans le constructeur de la [BrokeredMessage][]et le * *DataContractSerializer* * sera ensuite utilisé pour sérialiser l’objet. Vous pouvez également un * *java.io.InputStream** peut être fourni.

L’exemple suivant montre comment envoyer cinq tester des messages à la `TestTopic` **MessageSender** , nous avons obtenus dans l’extrait de code ci-dessus.
Notez comment la valeur de la propriété **format** de chaque message varie en fonction de l’itération de la boucle (Cela permet de déterminer les abonnements recevoir) :

```
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Rubriques du Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages conservés dans une rubrique, mais il existe un embout de la taille totale des messages émanant d’une rubrique. Cette taille de rubrique est définie au moment de la création, avec un maximum de 5 Go.

## <a name="how-to-receive-messages-from-a-subscription"></a>Comment recevoir des messages à partir d’un abonnement

Pour recevoir des messages à partir d’un abonnement, utilisez un objet **ServiceBusContract** . Messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**.

Lors de l’utilisation du mode **ReceiveAndDelete** , recevoir est une opération de prise de vue unique - autrement dit, lorsque le Bus de Service reçoit une demande de lecture d’un message, il marque le message comme étant consommé et retourne à l’application. **ReceiveAndDelete** est le modèle le plus simple et plus adapté à des scénarios dans lequel une application tolère ne pas traiter un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service vous avez marqué le message comme étant consommé, puis lorsque l’application redémarre et commence à consommer des messages, il sera ont manqué le message qui a été utilisé avant l’incident.

En mode de **PeekLock** , recevoir devient une opération deux étapes, ce qui permet aux applications de prise en charge qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant **Supprimer** sur le message reçu. Lorsque le Service Bus voit l’appel **Supprimer** , il marque le message comme étant consommé et le supprimer à partir de la rubrique.

L’exemple ci-dessous montre comment les messages peuvent être reçus et traitement à l’aide du mode de **PeekLock** (pas le mode par défaut). L’exemple suivant effectue une boucle et traite les messages dans l’abonnement « HighMessages » et puis s’arrête lorsqu’il n’y a pas plus de messages (il peut également être définir à attendre pour les nouveaux messages).

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application de récepteur ne parvient pas à traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur le message reçu (au lieu de la méthode **deleteMessage** ). Cela entraînera le Bus de Service déverrouiller le message dans la rubrique et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de.

Il existe également un délai d’expiration d’un message verrouillé dans la rubrique, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis le Bus de Service sera déverrouiller le message automatiquement et le rendre disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message, mais avant l’émission de la demande de **deleteMessage** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. On appelle souvent cela **Au moins une fois traitement**, autrement dit, chaque message sera traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis les développeurs d’applications doivent ajouter une logique supplémentaire à leur application pour gérer la remise des messages en double. Cette opération est souvent réalisée à l’aide de la méthode **getMessageId** du message, qui reste constante entre les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer les rubriques et les abonnements

Pour supprimer des abonnements et des rubriques, le principal consiste à utiliser un objet **ServiceBusContract** . Suppression d’une rubrique supprimera également tous les abonnements qui sont enregistrés à l’aide de la rubrique. Les abonnements peuvent également être supprimés indépendamment.

```
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des files d’attente de Bus de Service, voir [abonnements, des rubriques et des files d’attente de Bus de Service][] pour plus d’informations.

  [Azure SDK pour Java]: http://azure.microsoft.com/develop/java/
  [Azure Shared Computer Toolkit pour Éclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [Abonnements, des rubriques et des files d’attente de Bus de Service]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx 
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  
  [0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
