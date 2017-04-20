<properties 
    pageTitle="L’utilisation des thèmes du Bus de Service avec les Python | Microsoft Azure" 
    description="Apprenez à utiliser les rubriques du Bus des services Azure et des abonnements à partir de Python." 
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
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Comment faire pour utiliser des abonnements et des rubriques du Bus des services

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article décrit comment utiliser les abonnements et les rubriques du Bus de Service. Les exemples sont écrits dans les Python et utilisent les [Python Azure package][]. Les scénarios présentés incluent la **Création de rubriques et les abonnements**, **Création de filtres d’abonnement**, **Envoyer des messages à une rubrique**, **recevoir des messages à partir d’un abonnement**et **suppression des rubriques et des abonnements**. Pour plus d’informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Remarque :** Si vous devez installer les Python ou le [package Python Azure][], consultez le [Guide d’Installation de Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Créer une rubrique

L’objet **ServiceBusService** vous permet d’utiliser des thèmes. Ajoutez le code suivant en haut de n’importe quel fichier Python dans lequel vous souhaitez accéder par programme aux Bus de Service :

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Le code suivant crée un objet **ServiceBusService** . Remplacer `mynamespace`, `sharedaccesskeyname`, et `sharedaccesskey` avec votre espace de noms réel, les nom et la clé de valeur de clé de partagés accès Signature (SAS).

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Vous pouvez obtenir les valeurs pour le nom de clé des associations de sécurité et la valeur à partir du [portail Azure][].

```
bus_service.create_topic('mytopic')
```

**Création\_rubrique** prend également en charge des options supplémentaires qui vous permettent de remplacer les paramètres de rubrique par défaut tels que la durée de vie du message ou de la taille du nombre de rubriques. L’exemple suivant définit la taille maximale de rubrique pour 5 Go et un temps de vie) la valeur TTL (de 1 minute :

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Créer des abonnements

Abonnements aux rubriques sont également créés avec l’objet **ServiceBusService** . Les abonnements sont nommés et peuvent avoir un filtre facultatif qui limite l’ensemble des messages remis à la file d’attente virtuelle de l’abonnement.

> [AZURE.NOTE] Les abonnements sont persistantes et continueront à exister jusqu'à ce qu’elles, soit la rubrique à laquelle ils sont abonnés, sont supprimés.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Le filtre de le **MatchAll** est le filtre par défaut qui est utilisé si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement. Lorsque le filtre de le **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « AllMessages » et utilise le filtre de **MatchAll** par défaut.

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec des filtres

Vous pouvez également définir des filtres qui vous permettent de spécifier les messages envoyés à une rubrique doivent s’afficher dans un abonnement à une rubrique spécifique.

Le type de filtre pris en charge par les abonnements plus souple est un **SqlFilter**, qui implémente un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur les expressions qui peuvent être utilisés avec un filtre SQL, consultez la syntaxe [SqlFilter.SqlExpression][] .

Vous pouvez ajouter des filtres à un abonnement à l’aide de la **créer\_règle** méthode de l’objet **ServiceBusService** . Cette méthode vous permet d’ajouter de nouveaux filtres à un abonnement existant.

> [AZURE.NOTE] Comme le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d’abord supprimer le filtre par défaut ou l' **MatchAll** remplace tous les autres filtres que vous pouvez spécifier. Vous pouvez supprimer la règle par défaut à l’aide de la **Supprimer\_règle** méthode de l’objet **ServiceBusService** .

L’exemple suivant crée un abonnement nommé `HighMessages` avec un **SqlFilter** qui sélectionne uniquement les messages qui comportent une propriété personnalisée **messagenumber** supérieure à 3 :

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

De la même façon, l’exemple suivant crée un abonnement nommé `LowMessages` avec un **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **messagenumber** inférieure ou égale à 3 :

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Désormais, lorsqu’un message est envoyé à `mytopic` qu’il est toujours livré aux récepteurs souscrit à l’abonnement de la rubrique **AllMessages** et sélectivement remis aux récepteurs d’abonné pour les abonnements de rubrique **HighMessages** et **LowMessages** (selon le contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoyer des messages à une rubrique

Pour envoyer un message à une rubrique du Bus des services, votre application doit utiliser le **envoyer\_rubrique\_message** méthode de l’objet **ServiceBusService** .

L’exemple suivant montre comment envoyer des cinq tests des messages `mytopic`. Notez que la valeur de la propriété **format** de chaque message varie en fonction de l’itération de la boucle (Cela détermine les abonnements recevoir) :

```
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Rubriques du Bus de service prend en charge une taille maximale de message de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche de la prime](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés de l’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’y a aucune limite sur le nombre de messages conservés dans une rubrique, mais il existe un embout de la taille totale des messages émanant d’une rubrique. Cette taille de rubrique est définie au moment de la création, avec un maximum de 5 Go. Pour plus d’informations sur les quotas, reportez-vous à la section [quotas de Bus de Service][].

## <a name="receive-messages-from-a-subscription"></a>Recevoir des messages à partir d’un abonnement

Les messages sont reçus à partir d’un abonnement à l’aide de la **de réception\_abonnement\_message** méthode sur l’objet **ServiceBusService** :

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Les messages sont supprimés de l’abonnement lorsqu’ils sont lus lorsque le paramètre **lecture\_verrou** est définie sur **False**. Vous pouvez lire (lecture) et verrouiller le message sans le supprimer de la file d’attente en définissant le paramètre **lecture\_verrouillage** à **True**.

Le comportement de lecture et de suppression du message dans le cadre de l’opération de réception est le modèle le plus simple et plus adapté à des scénarios dans lequel une application tolère ne pas traiter un message en cas de panne. Pour comprendre cela, imaginez un scénario dans lequel le consommateur envoie la demande de réception et puis se bloque avant de le traiter. Étant donné que le Bus de Service vous avez marqué le message comme étant consommé, puis lorsque l’application redémarre et commence à consommer des messages, il sera ont manqué le message qui a été utilisé avant l’incident.

Si le **lecture\_verrou** paramètre est défini sur **True**, la réception devient une opération deux étapes, ce qui permet aux applications de prise en charge qui ne peuvent tolérer des messages manquants. Lorsque le Bus de Service reçoit une demande, il recherche du message suivant devant être consommés, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois que l’application a terminé le traitement du message (ou stocke de façon fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant la méthode **delete** de l’objet **Message** . La méthode **delete** marque le message comme étant consommé et le supprime de l’abonnement.

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer l’application tombe en panne et messages illisibles

Bus de service fournit des fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou les difficultés de traitement d’un message. Si une application de récepteur ne parvient pas à traiter le message pour une raison quelconque, il peut appeler la méthode de **déverrouillage** de l’objet **Message** . Cela entraînera le Bus de Service déverrouiller le message dans l’abonnement et le rendre disponible à recevoir à nouveau, soit par la même application consommatrice, soit par une autre application prend beaucoup de.

Il existe également un délai d’expiration d’un message verrouillé au sein de l’abonnement, et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), puis déverrouille le message automatiquement de Bus de Service et le rend disponible à recevoir à nouveau.

Dans le cas où l’application se bloque après avoir traité le message, mais avant l’appel de la méthode **delete** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. On appelle souvent cela **Au moins une fois traitement**, autrement dit, chaque message sera traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer de traitement en double, puis les développeurs d’applications doivent ajouter une logique supplémentaire à leur application pour gérer la remise des messages en double. Cette opération est souvent réalisée à l’aide de la propriété **MessageId** du message, qui reste constante entre les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer les rubriques et les abonnements

Rubriques et les abonnements sont persistantes et doivent être explicitement supprimés par le biais du [portail Azure][] ou par programme. L’exemple suivant montre comment supprimer la rubrique nommée `mytopic`:

```
bus_service.delete_topic('mytopic')
```

Suppression d’une rubrique supprime également tous les abonnements qui sont enregistrés à l’aide de la rubrique. Les abonnements peuvent également être supprimés indépendamment. Le code suivant montre comment supprimer un abonnement nommé `HighMessages` de la `mytopic` sujet :

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base des rubriques du Bus des services, cliquez sur ces liens pour en savoir plus.

-   Reportez-vous à la section [files d’attente, des rubriques et des abonnements][].
-   Référence de [SqlFilter.SqlExpression][].

[Azure portal]: https://portal.azure.com
[Package de Python Azure]: https://pypi.python.org/pypi/azure  
[Files d’attente, des rubriques et des abonnements]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Quotas de Bus des services]: service-bus-quotas.md 
