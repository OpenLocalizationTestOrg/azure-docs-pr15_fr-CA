<properties
    pageTitle="L’utilisation de files d’attente de Bus de Service Ruby | Microsoft Azure"
    description="Découvrez comment utiliser les files d’attente de Bus de Service dans Azure. Exemples de code écrites en Ruby."
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

# <a name="how-to-use-service-bus-queues"></a>L’utilisation de files d’attente de Bus de Service

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ce guide décrit comment utiliser les files d’attente de Bus de Service. Les exemples sont écrits en Ruby et utilisent la marque Azure. Les scénarios présentés incluent la **Création de files d’attente, l’envoi et réception de messages**et la **suppression de files d’attente**. Pour plus d’informations sur les files d’attente de Bus de Service, consultez la section [Étapes suivantes](#next-steps) .

## <a name="what-are-service-bus-queues"></a>Que sont les files d’attente de Bus de Service ?

Files d’attente de Bus de service prend en charge un modèle de communication *par courtier de messagerie* . Avec les files d’attente, les composants d’une application distribuée ne communiquent pas directement avec eux ; au lieu de cela ils échangent des messages via une file d’attente, qui sert d’intermédiaire. Un producteur de message (expéditeur) transmet un message à la file d’attente, puis poursuit son traitement.
En mode asynchrone, un consommateur de message (récepteur) extrait le message de la file d’attente et la traite. Le producteur n’a pas à attendre une réponse du consommateur pour pouvoir continuer à traiter et envoyer des messages. Files d’attente offrent **en premier, premier sorti (FIFO)** remise des messages à un ou plusieurs consommateurs concurrentes. Autrement dit, les messages sont généralement reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente, et chaque message est reçue et traitée par le consommateur d’un seul message.

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Files d’attente de Bus de service sont une technologie à usage général qui peut être utilisée pour un large éventail de scénarios :

-   Communication entre les rôles web et worker dans une [n-tier applications Azure](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md).
-   La communication entre les applications sur site et Azure hébergé des applications dans une [solution hybride](service-bus-dotnet-hybrid-app-using-service-bus-relay.md).
-   Communication entre les composants d’une application distribuée en cours d’exécution sur site dans les différentes organisations ou départements d’une organisation.

À l’aide de files d’attente peut vous permettre de faire évoluer vos applications mieux et activer plus de souplesse à votre architecture.

## <a name="create-a-namespace"></a>Créer un espace de noms

Pour commencer à utiliser des files d’attente de Bus de Service dans Azure, vous devez d’abord créer un espace de noms. Un espace de noms fournit un conteneur de portée pour l’adressage des ressources de Bus de Service au sein de votre application. Vous devez créer l’espace de noms par le biais de l’interface de ligne de commande car le portail Azure ne crée pas de l’espace de noms avec une connexion ACS.

Pour créer un espace de noms :

1. Ouvrez une console Powershell d’Azure.

2. Tapez la commande suivante pour créer un espace de noms du Bus de Service. Fournir votre propre valeur d’espace de noms et spécifier la même région que votre application.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
    ```

## <a name="obtain-management-credentials-for-the-namespace"></a>Obtenir des informations d’identification de la gestion de l’espace de noms

Pour effectuer des opérations de gestion, telles que la création d’une file d’attente dans l’espace de noms, vous devez obtenir les informations d’identification de la gestion de l’espace de noms.

L’applet de commande PowerShell que vous avez exécuté pour créer de l’espace de noms de bus de service Azure affiche la clé que vous pouvez utiliser pour gérer l’espace de noms. Copiez la valeur de **DefaultKey** . Vous utiliserez cette valeur dans votre code, plus loin dans ce didacticiel.

![Copiez la clé](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE] Vous trouverez cette clé si vous ouvrez une session sur le [portail Azure](https://portal.azure.com/) et accédez aux informations de connexion pour votre espace de noms du Bus de Service.

## <a name="create-a-ruby-application"></a>Créer une application Ruby

Créez une application Ruby. Pour obtenir des instructions, voir [Création d’une Application de Ruby sur Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser le Bus de Service

Pour utiliser le Bus des services Azure, téléchargez et utilisez le package Ruby Azure, qui inclut un ensemble de bibliothèques de commodité qui communiquent avec les services de reste de stockage.

### <a name="use-rubygems-to-obtain-the-package"></a>Utiliser RubyGems pour obtenir le package

1. Utilisez une interface de ligne de commande **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix).

2. Tapez « azure d’installation gem » dans la fenêtre de commande pour installer la marque et les dépendances.

### <a name="import-the-package"></a>Importer le package

À l’aide de votre éditeur de texte, ajoutez le code suivant en haut du fichier Ruby où vous avez l’intention d’utiliser le stockage :

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>Configurer une connexion de Bus des services Azure

Le module Azure lit les variables d’environnement **AZURE\_SERVICEBUS\_espace de noms** et **AZURE\_SERVICEBUS\_ACCESS_KEY** pour les informations requises pour se connecter à votre espace de noms du Bus de Service. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations d’espace de noms avant d’utiliser **Azure::ServiceBusService** avec le code suivant :

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Définissez la valeur de l’espace de noms à la valeur que vous avez créé, et non l’URL complète. Par exemple, utilisez **« yourexamplenamespace »**, pas « yourexamplenamespace.servicebus.windows.net ».

## <a name="how-to-create-a-queue"></a>Comment faire pour créer une file d’attente

L’objet **Azure::ServiceBusService** vous permet d’utiliser des files d’attente. Pour créer une file d’attente, utilisez la méthode **create_queue()** . L’exemple suivant crée une file d’attente ou imprime toutes les erreurs.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Vous pouvez également passer un objet **Azure::ServiceBus::Queue** avec des options supplémentaires, ce qui vous permet de remplacer les paramètres de file d’attente par défaut, tels que la durée de vie du message ou de la taille maximale de file d’attente. L’exemple suivant montre comment définir la taille de la file d’attente maximale de 5 Go et l’heure en direct à une minute :

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Comment faire pour envoyer des messages à une file d’attente

Pour envoyer un message à une file d’attente de Bus de Service, vous application appelle le **envoyer\_file d’attente de\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Les messages envoyés à (et reçus à partir de) files d’attente de Bus de Service sont les objets **Azure::ServiceBus::BrokeredMessage** et disposent d’un ensemble de propriétés standard (telles que les **étiquettes** et **temps\_à\_live**), d’un dictionnaire qui est utilisée pour inclure des propriétés spécifiques d’une application personnalisée et un corps de données d’application arbitraire. Une application peut définir le corps du message en passant une valeur de chaîne en tant que le message et toutes les propriétés standard requises seront remplies avec les valeurs par défaut.

L’exemple suivant montre comment envoyer un message de test à la file d’attente nommée à l’aide de « test-file d’attente » **envoyer\_file d’attente de\_à message()**:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Files d’attente de Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages dans une file d’attente, mais il existe une extrémité sur la taille totale des messages émanant d’une file d’attente. Cette taille de file d’attente est définie au moment de la création, avec un maximum de 5 Go.

## <a name="how-to-receive-messages-from-a-queue"></a>Comment recevoir des messages d’une file d’attente

Les messages sont reçus à partir d’une file d’attente à l’aide de la **recevoir\_file d’attente de\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Par défaut, les messages sont lus et verrouillés sans les supprimer de la file d’attente. Toutefois, vous pouvez supprimer des messages de la file d’attente lorsqu’ils sont lus en définissant le **: peek_lock** option **false**.

Le comportement par défaut permet la lecture et la suppression d’une opération de deux étapes, ce qui permet également de prendre en charge les applications qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant **Supprimer\_file d’attente de\_à message()** méthode et en fournissant le message à supprimer en tant que paramètre. Le **Supprimer\_file d’attente de\_à message()** méthode va marquer le message comme étant consommé et le supprimer de la file d’attente.

Si le **: lecture\_verrou** paramètre est défini sur **false**, lecture et suppression du message devient le modèle le plus simple et est idéale pour les scénarios dans lequel une application tolère ne pas traiter un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service va avoir marqué le message comme étant consommé, lorsque l’application redémarre et commence à consommer des messages à nouveau, il sera ont manqué au message qui a été utilisé avant l’incident.

L’exemple suivant montre comment recevoir et traiter des messages à l’aide de **de réception\_file d’attente de\_à message()**. Tout d’abord, l’exemple reçoit et supprime un message à l’aide de **: lecture\_verrouillage** a la valeur **false**, il reçoit un autre message et supprime ensuite le message à l’aide **Supprimer\_file d’attente de\_à message()**:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application de récepteur est incapable de traiter le message pour une raison quelconque, puis il peut appeler la **déverrouiller\_file d’attente de\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Dans ce cas déverrouiller le message dans la file d’attente et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de Bus des services.

Il existe également un délai d’expiration d’un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis déverrouille le message automatiquement de Bus de Service et le rend disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message mais avant que le **Supprimer\_file d’attente de\_à message()** méthode est appelée, puis le message sera remis à nouveau à l’application lorsqu’il redémarre. On appelle souvent cela **Au moins une fois la transformation**; en d’autres termes, chaque message est traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis les développeurs d’applications doivent ajouter une logique supplémentaire à leur application pour gérer la remise des messages en double. Cette opération est souvent réalisée à l’aide de la **message\_id** propriété du message, qui reste constante entre les tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des files d’attente de Bus de Service, cliquez sur ces liens pour en savoir plus.

-   Vue d’ensemble des [files d’attente, des rubriques et des abonnements](service-bus-queues-topics-subscriptions.md).
-   Visitez le référentiel [Azure SDK pour Ruby](https://github.com/Azure/azure-sdk-for-ruby) sur GitHub.

Pour une comparaison entre les files d’attente du Bus des services Azure abordées dans cet article et les files d’attente Azure décrites dans l’article [comment utiliser le stockage de file d’attente à partir de Ruby](../storage/storage-ruby-how-to-use-queue-storage.md) , voir [files d’attente Azure et Azure Service Bus files d’attente - comparaison et Contrasted](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
