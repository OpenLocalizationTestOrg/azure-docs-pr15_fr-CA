<properties 
    pageTitle="Comment utiliser les files d’attente de Bus de Service avec les Python | Microsoft Azure" 
    description="Découvrez comment utiliser les files d’attente de Bus des services Azure à partir de Python." 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="sethm;lmazuel"/>


# <a name="how-to-use-service-bus-queues"></a>L’utilisation de files d’attente de Bus de Service

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Cet article décrit comment utiliser les files d’attente de Bus de Service. Les exemples sont écrits dans les Python et utilisent le [package de Bus des services Azure Python][]. Les scénarios présentés incluent la **Création de files d’attente, l’envoi et réception de messages**et la **suppression de files d’attente**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] Pour installer les Python ou le [package de Bus des services Azure Python][], consultez le [Guide d’Installation de Python](../python-how-to-install.md).

## <a name="create-a-queue"></a>Créer une file d’attente

L’objet **ServiceBusService** vous permet d’utiliser des files d’attente. Ajoutez le code suivant en haut de n’importe quel fichier Python, dans laquelle vous souhaitez accéder par programme aux Bus de Service :

```
from azure.servicebus import ServiceBusService, Message, Queue
```

Le code suivant crée un objet **ServiceBusService** . Remplacer `mynamespace`, `sharedaccesskeyname`, et `sharedaccesskey` avec votre espace de noms, le nom de clé de signature (SAS) un accès partagé et la valeur.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Les valeurs pour le nom de clé des associations de sécurité et la valeur peuvent être trouvés dans les informations de connexion [Azure portal classique][] ou dans le volet des **Propriétés** Visual Studio lors de la sélection de l’espace de noms du Bus de Service dans l’Explorateur de serveurs (comme indiqué dans la section précédente).

```
bus_service.create_queue('taskqueue')
```

**create_queue** prend également en charge des options supplémentaires qui vous permettent de remplacer les paramètres de file d’attente par défaut, tels que la durée de vie (TTL) du message ou de la taille maximale de file d’attente. L’exemple suivant définit la taille de la file d’attente maximale de 5 Go et la valeur de durée de vie à une minute :

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Envoyer des messages à une file d’attente

Pour envoyer un message à une file d’attente de Bus de Service, votre application appelle la **envoyer\_file d’attente\_message** méthode sur l’objet **ServiceBusService** .

L’exemple suivant montre comment envoyer un message de test à la file d’attente nommée *à l’aide de la taskqueue* **envoyer\_file d’attente\_message**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Files d’attente de Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages dans une file d’attente, mais il existe une extrémité sur la taille totale des messages émanant d’une file d’attente. Cette taille de file d’attente est définie au moment de la création, avec un maximum de 5 Go. Pour plus d’informations sur les quotas, reportez-vous à la section [quotas de Bus de Service][].

## <a name="receive-messages-from-a-queue"></a>Recevoir des messages d’une file d’attente

Les messages sont reçus à partir d’une file d’attente à l’aide de la **de réception\_file d’attente\_message** méthode sur l’objet **ServiceBusService** :

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Les messages sont supprimés de la file d’attente lorsqu’ils sont lus lorsque le paramètre **lecture\_verrou** est définie sur **False**. Vous pouvez lire (lecture) et verrouiller le message sans le supprimer de la file d’attente en définissant le paramètre **lecture\_verrouillage** à **True**.

Le comportement de lecture et de suppression du message dans le cadre de l’opération de réception est le modèle le plus simple et plus adapté à des scénarios dans lequel une application tolère ne pas traiter un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service vous avez marqué le message comme étant consommé, puis lorsque l’application redémarre et commence à consommer des messages, il sera ont manqué le message qui a été utilisé avant l’incident.

Si le **lecture\_verrou** paramètre est défini sur **True**, la réception devient une opération deux étapes, ce qui permet aux applications de prise en charge qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception par l’appel de la méthode **delete** de l’objet **Message** . La méthode **delete** marque le message comme étant consommé et le supprimer de la file d’attente.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application de récepteur ne parvient pas à traiter le message pour une raison quelconque, il peut appeler la méthode de **déverrouillage** de l’objet **Message** . Cela entraînera le Bus de Service déverrouiller le message dans la file d’attente et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de.

Il existe également un délai d’expiration d’un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis le Bus de Service sera déverrouiller le message automatiquement et le rendre disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message, mais avant l’appel de la méthode **delete** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. On appelle souvent cela **Au moins une fois traitement**, autrement dit, chaque message sera traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis les développeurs d’applications doivent ajouter une logique supplémentaire à leur application pour gérer la remise des messages en double. Cette opération est souvent réalisée à l’aide de la propriété **MessageId** du message, qui reste constante entre les tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des files d’attente de Bus de Service, cliquez sur ces liens pour en savoir plus.

-   Reportez-vous à la section [files d’attente, des rubriques et des abonnements][].

[Azure portal classique]: https://manage.windowsazure.com
[Package de Bus des services Azure Python]: https://pypi.python.org/pypi/azure-servicebus  
[Files d’attente, des rubriques et des abonnements]: service-bus-queues-topics-subscriptions.md
[Quotas de Bus des services]: service-bus-quotas.md
 
