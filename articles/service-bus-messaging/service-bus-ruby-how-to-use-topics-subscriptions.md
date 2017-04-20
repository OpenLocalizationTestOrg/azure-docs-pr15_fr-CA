<properties
    pageTitle="L’utilisation des thèmes du Bus de Service (Ruby) | Microsoft Azure"
    description="Apprenez à utiliser des thèmes du Bus de Service et des abonnements dans Azure. Exemples de code sont écrits pour les applications Ruby."
    services="service-bus"
    documentationCenter="ruby"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topicssubscriptions"></a>L’utilisation de Bus de Service rubriques/abonnements

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article décrit comment utiliser des thèmes du Bus de Service et des abonnements à partir d’applications Ruby. Les scénarios présentés incluent la **Création de rubriques et les abonnements, création de filtres d’abonnement, envoyer des messages** à une rubrique, **recevoir des messages à partir d’un abonnement**et **Supprimer des abonnements et des rubriques**. Pour plus d’informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes](#next-steps) .

## <a name="service-bus-topics-and-subscriptions"></a>Les abonnements et les rubriques du Bus des services

Les abonnements et les rubriques du Bus de Service prend en charge d’une *publication et d’abonnement* modèle de communication de messagerie. Lorsque vous utilisez des rubriques et des abonnements, les composants d’une application distribuée ne communiquent pas directement avec eux ; ils échangent des messages via une rubrique, qui sert d’intermédiaire pour l’à la place.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

Contrairement aux files d’attente de Bus de Service, où chaque message est traité par un seul consommateur, les abonnements et les rubriques fournissent un formulaire **un-à-plusieurs** de la communication, à l’aide d’un modèle de publication et d’abonnement. Il est possible d’enregistrer plusieurs abonnements à une rubrique. Lorsqu’un message est envoyé à une rubrique, puis le rend disponible à chaque abonnement pour traiter de manière indépendante.

Un abonnement de rubrique ressemble à une file d’attente virtuelle qui reçoit une copie des messages qui ont été envoyés à la rubrique. Vous pouvez enregistrer les règles de filtrage pour une rubrique sur une base par abonnement, ce qui vous permet de restreindre/filtrer les messages à une rubrique sont reçus par les abonnements de la rubrique.

Les abonnements et les rubriques du Bus de Service permettent de mettre à l’échelle pour traiter un grand nombre de messages sur un grand nombre d’utilisateurs et d’applications.

## <a name="create-a-namespace"></a>Créer un espace de noms

Pour commencer à utiliser des files d’attente de Bus de Service dans Azure, vous devez d’abord créer un espace de noms. Un espace de noms fournit un conteneur de portée pour l’adressage des ressources de Bus de Service au sein de votre application. Vous devez créer l’espace de noms par le biais de l’interface de ligne de commande car le [portail Azure][] ne crée pas de l’espace de noms avec une connexion ACS.

Pour créer un espace de noms :

1. Ouvrez une fenêtre de console Powershell d’Azure.

2. Tapez la commande suivante pour créer un espace de noms. Fournir votre propre valeur d’espace de noms et spécifier la même région que votre application.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
    ```

    ![Créer le Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a name="obtain-default-management-credentials-for-the-namespace"></a>Obtenir des informations d’identification de la gestion de l’espace de noms par défaut

Pour effectuer des opérations de gestion, telles que la création d’une file d’attente dans l’espace de noms, vous devez obtenir les informations d’identification de la gestion de l’espace de noms.

L’applet de commande PowerShell que vous avez exécuté pour créer de l’espace de noms du Bus de Service affiche la clé que vous pouvez utiliser pour gérer l’espace de noms. Copiez la valeur de **DefaultKey** . Vous utiliserez cette valeur dans votre code, plus loin dans ce didacticiel.

![Copiez la clé](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]
> Vous pouvez également trouver cette clé si vous ouvrez une session sur le [portail Azure][] et accédez aux informations de connexion pour votre espace de noms.

## <a name="create-a-ruby-application"></a>Créer une application Ruby

Pour obtenir des instructions, voir [Création d’une Application de Ruby sur Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser le Bus des services

Pour utiliser le Service Bus, télécharger et à utiliser le package de Ruby Azure, qui inclut un ensemble de bibliothèques de commodité qui communiquent avec les services de reste de stockage.

### <a name="use-rubygems-to-obtain-the-package"></a>Utiliser RubyGems pour obtenir le package

1. Utilisez une interface de ligne de commande **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix).

2. Tapez « azure d’installation gem » dans la fenêtre de commande pour installer la marque et les dépendances.

### <a name="import-the-package"></a>Importer le package

À l’aide de votre éditeur de texte, ajoutez le code suivant en haut du fichier dans lequel vous avez l’intention d’utiliser le stockage Ruby :

```
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurer une connexion de Bus des services

Le module Azure lit les variables d’environnement **AZURE\_SERVICEBUS\_espace de noms** et **AZURE\_SERVICEBUS\_accès\_clé** pour les informations requises pour se connecter à votre espace de noms. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations d’espace de noms avant d’utiliser **Azure::ServiceBusService** avec le code suivant :

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Définissez la valeur de l’espace de noms à la valeur que vous avez créé au lieu de l’URL entière. Par exemple, utilisez **« yourexamplenamespace »**, pas « yourexamplenamespace.servicebus.windows.net ».

## <a name="create-a-topic"></a>Créer une rubrique

L’objet **Azure::ServiceBusService** vous permet d’utiliser des thèmes. Le code suivant crée un objet **Azure::ServiceBusService** . Pour créer une rubrique, utilisez les **créer\_topic()** méthode. L’exemple suivant crée une rubrique ou imprime les erreurs éventuelles.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Vous pouvez également passer un objet **Azure::ServiceBus::Topic** avec des options supplémentaires, qui permettent de modifier la rubrique Paramètres par défaut des temps de message en direct ou une taille maximale de file d’attente. L’exemple suivant montre la définition de la taille maximale de file d’attente de 5 Go et l’heure en direct à une minute :

```
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Créer des abonnements

Abonnement à une rubrique est également créés avec l’objet **Azure::ServiceBusService** . Les abonnements sont nommés et peuvent avoir un filtre facultatif qui limite l’ensemble des messages remis à la file d’attente virtuelle de l’abonnement.

Les abonnements sont persistantes et continueront à exister jusqu'à ce qu’une d’elles, ou la rubrique, ils sont associés, sont supprimés. Si votre application contient la logique pour créer un abonnement, il doit tout d’abord vérifier si l’abonnement existe déjà à l’aide de la méthode getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Le filtre de le **MatchAll** est le filtre par défaut qui est utilisé si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement. Lorsque le filtre de le **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « tous les messages » et utilise le filtre de **MatchAll** par défaut.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec des filtres

Vous pouvez également définir des filtres qui vous permettent de spécifier les messages envoyés à une rubrique doivent s’afficher dans un abonnement spécifique.

Le type de filtre pris en charge par les abonnements plus souple est **Azure::ServiceBus::SqlFilter**, qui implémente un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur les expressions qui peuvent être utilisées avec un filtre SQL, passez en revue la syntaxe [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) .

Vous pouvez ajouter des filtres à un abonnement à l’aide de la **créer\_rule()** méthode de l’objet **Azure::ServiceBusService** . Cette méthode vous permet d’ajouter de nouveaux filtres à un abonnement existant.

Dans la mesure où le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d’abord supprimer le filtre par défaut ou l' **MatchAll** remplace tous les autres filtres que vous pouvez spécifier. Vous pouvez supprimer la règle par défaut à l’aide de la **Supprimer\_rule()** méthode sur l’objet **Azure::ServiceBusService** .

L’exemple suivant crée un abonnement nommé « haute-messages » avec un **Azure::ServiceBus::SqlFilter** qui sélectionne uniquement les messages qui ont personnalisé **message\_numéro** propriété supérieure à 3 :

```
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

De la même façon, l’exemple suivant crée un abonnement nommé « faible-les messages » avec un **Azure::ServiceBus::SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **numéro_de_message** inférieure ou égale à 3 :

```
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Lorsqu’un message est maintenant envoyé au sujet « test », il est toujours être livré aux récepteurs souscrit à l’abonnement de la rubrique « all-messages » et sélectivement remis aux récepteurs d’abonné pour les abonnements de rubrique « haute-messages » et « faible-les messages » (en fonction du contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoyer des messages à une rubrique

Pour envoyer un message à une rubrique du Bus des services, votre application doit utiliser le **envoyer\_rubrique\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Les messages envoyés aux rubriques du Bus de Service sont des instances des objets **Azure::ServiceBus::BrokeredMessage** . **Azure::ServiceBus::BrokeredMessage** objets possèdent un ensemble de propriétés standard (telles que les **étiquettes** et **temps\_à\_live**), d’un dictionnaire qui est utilisée pour inclure des propriétés spécifiques d’une application personnalisée et un corps de données de type chaîne. Une application peut définir le corps du message en passant une valeur de chaîne à la **envoyer\_rubrique\_à message()** méthode et tout nécessaire de propriétés standard seront remplies par les valeurs par défaut.

L’exemple suivant montre comment envoyer cinq messages pour sujet « test » de test. Notez que la valeur de la propriété personnalisée **numéro_de_message** de chaque message varie en fonction de l’itération de la boucle (Cela détermine quel abonnement reçoit) :

```
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Rubriques du Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages conservés dans une rubrique, mais il existe un embout de la taille totale des messages émanant d’une rubrique. Cette taille de rubrique est définie au moment de la création, avec un maximum de 5 Go.

## <a name="receive-messages-from-a-subscription"></a>Recevoir des messages à partir d’un abonnement

Les messages sont reçus à partir d’un abonnement à l’aide de la **recevoir\_abonnement\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Par défaut, les messages sont read(peak) et verrouillé sans le supprimer de l’abonnement. Vous pouvez lire et supprimer le message de l’abonnement en définissant le **lecture\_verrou** option **false**.

Le comportement par défaut permet la lecture et la suppression d’une opération de deux étapes, ce qui permet également de prendre en charge les applications qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant **Supprimer\_abonnement\_à message()** méthode et en fournissant le message à supprimer en tant que paramètre. Le **Supprimer\_abonnement\_à message()** méthode va marquer le message comme étant consommé et le supprimer de l’abonnement.

Si le **: lecture\_verrou** paramètre est défini sur **false**, lecture et suppression du message devient le modèle le plus simple et est idéale pour les scénarios dans lequel une application tolère ne pas traiter un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service vous avez marqué le message comme étant consommé, puis lorsque l’application redémarre et commence à consommer des messages, il sera ont manqué le message qui a été utilisé avant l’incident.

L’exemple suivant montre comment les messages peuvent être reçus et traités à l’aide de **de réception\_abonnement\_à message()**. Tout d’abord, l’exemple reçoit et supprime un message de l’abonnement à « faible-les messages » à l’aide de **: lecture\_verrouillage** a la valeur **false**, il reçoit un autre message à partir des « haute-messages » et supprime ensuite le message à l’aide de **Supprimer\_abonnement\_à message()**:

```
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application de récepteur est incapable de traiter le message pour une raison quelconque, puis il peut appeler la **déverrouiller\_abonnement\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Dans ce cas déverrouiller le message dans l’abonnement et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de Bus des services.

Il existe également un délai d’expiration d’un message verrouillé au sein de l’abonnement, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis le Bus de Service sera déverrouiller le message automatiquement et le rendre disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message mais avant que le **Supprimer\_abonnement\_à message()** méthode est appelée, puis le message est redistribué à l’application lorsqu’il redémarre. On appelle souvent cela **Au moins une fois la transformation**; Autrement dit, chaque message sera traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis les développeurs d’applications doivent ajouter une logique supplémentaire à leur application pour gérer la remise des messages en double. Cette logique est souvent réalisée à l’aide de la **message\_id** propriété du message, qui reste constante entre les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer les rubriques et les abonnements

Rubriques et les abonnements sont persistantes et doivent être explicitement supprimés par le biais du [portail Azure][] ou par programme. L’exemple ci-dessous montre comment supprimer la rubrique nommée « test-rubrique ».

```
azure_service_bus_service.delete_topic("test-topic")
```

Suppression d’une rubrique supprime également tous les abonnements qui sont enregistrés à l’aide de la rubrique. Les abonnements peuvent également être supprimés indépendamment. Le code suivant montre comment supprimer l’abonnement nommé « haute-messages » de la rubrique « test-rubrique » :

```
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des rubriques du Bus des services, cliquez sur ces liens pour en savoir plus.

- Reportez-vous à la section [files d’attente, des rubriques et des abonnements](service-bus-queues-topics-subscriptions.md).
- Référence des API pour [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx).
- Visitez le référentiel [Azure SDK pour Ruby](https://github.com/Azure/azure-sdk-for-ruby) sur GitHub.
 
[Azure portal]: https://portal.azure.com
