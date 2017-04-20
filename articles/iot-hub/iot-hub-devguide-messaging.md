<properties
 pageTitle="Guide du développeur - messagerie | Microsoft Azure"
 description="Guide de développeur IoT concentrateur Azure - périphérique-nuage et messagerie du nuage vers le périphérique"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="send-and-receive-messages-with-iot-hub"></a>Envoyer et recevoir des messages avec IoT concentrateur

## <a name="overview"></a>Vue d’ensemble

IoT concentrateur fournit les primitives de messagerie suivants pour communiquer avec un périphérique :

- [Périphérique-nuage] [ lnk-d2c] à partir d’un périphérique à une application back-end.
- [Nuage-DISPOSITIF] [ lnk-c2d] à partir d’une application back-end (*service* ou *nuage*).

Propriétés principales de fonctionnalités de messagerie IoT Hub sont la fiabilité et la durabilité des messages. Ces propriétés permettent de résilience à connectivité intermittente sur le côté du périphérique et de charger des pics de traitement sur le côté du nuage de l’événement. Concentrateur de IoT implémente *au moins une fois* les garanties de remise pour périphérique-nuage et de nuage-dispositif de messagerie.

IoT Hub prend en charge plusieurs [protocoles de périphérique orienté] [ lnk-protocols] (par exemple, MQTT, AMQP et HTTP). Pour prendre en charge une interopérabilité transparente entre des protocoles, IoT concentrateur définit un [format de message commun] [ lnk-message-format] qui la prennent en charge tous les protocoles orientés vers le périphérique.

Concentrateur de IoT expose un [point de terminaison compatible avec concentrateur événement] [ lnk-compatible-endpoint] pour permettre aux applications back-end lire les messages de périphérique-nuage reçus par le concentrateur.

### <a name="when-to-use"></a>Quand utiliser

La messagerie est une fonctionnalité importante de IoT concentrateur. Utilisez-la lorsque vous devez envoyer des messages à partir de votre appareil à votre back-end, ou envoyer des messages depuis votre back-end pour un périphérique.

Pour une comparaison des services IoT Hub et concentrateurs d’événement, reportez-vous à la section [comparaison de IoT Hub et concentrateurs d’événement][lnk-compare].

## <a name="device-to-cloud-messages"></a>Messages de périphérique-nuage

Vous envoyez des messages de périphérique-nuage via un point de terminaison côté périphérique (**/devices/ {deviceId} / messages/événements**). Votre service principal reçoit des messages de périphérique-nuage via un point de terminaison d’orienté service (**/messages/events**) est compatible avec les [Concentrateurs d’événement][lnk-event-hubs]. Par conséquent, vous pouvez utiliser standard [intégration de concentrateurs d’événement et les kits de développement logiciel] [ lnk-compatible-endpoint] pour recevoir des messages de périphérique-nuage.

Concentrateur de IoT implémente le périphérique-nuage de messagerie d’une manière similaire à [l’Événement concentrateurs][lnk-event-hubs]. Messages de périphérique-nuage du concentrateur IoT sont plus comme les concentrateurs d’événement *événements* de [Bus de Service] [ lnk-servicebus] *messages*.

Cette implémentation a les conséquences suivantes :

* De la même façon aux événements de l’événement concentrateurs, des messages de périphérique-nuage sont durables et conservés dans un concentrateur IoT pendant sept jours (voir [les options de configuration de périphérique-nuage][lnk-d2c-configuration]).
* Messages de périphérique-nuage sont partitionnés entre un ensemble fixe de partitions qui est défini au moment de la création (voir [les options de configuration de périphérique-nuage][lnk-d2c-configuration]).
* Comme à des concentrateurs d’événement, clients de lire des messages de périphérique-nuage doivent gérer des partitions et point de contrôle. Consultez les [Concentrateurs d’événement - consommation d’événements][lnk-event-hubs-consuming-events].
* Comme les événements de l’événement concentrateurs, des messages de périphérique-nuage peuvent représenter un maximum de 256 Ko et peuvent être regroupés par lots afin d’optimiser les envoie. Les lots peuvent être au maximum 256 Ko et au plus de 500 messages.

Toutefois, il existe quelques distinctions importantes entre les échanges de périphérique-nuage IoT Hub et concentrateurs de l’événement :

* Comme expliqué dans [contrôle de l’accès au concentrateur de IoT] [ lnk-devguide-security] section, IoT Hub permet par périphérique d’authentification et contrôle d’accès.
* IoT Hub permet à des millions de périphériques connectés simultanément (reportez-vous à la section [Quotas et des limitations][lnk-quotas]), alors que les concentrateurs d’événement est limitée à 5000 connexions AMQP par l’espace de noms.
* IoT Hub n’autorise pas arbitraire de partitionnement à l’aide d’un **PartitionKey**. Messages de périphérique-nuage sont partitionnées en fonction de leur **ID de périphérique**d' origine.
* Mise à l’échelle de IoT concentrateur est légèrement différente de celle de concentrateurs d’événement de mise à l’échelle. Pour plus d’informations, reportez-vous à la section [Mise à l’échelle un concentrateur IoT][lnk-guidance-scale].

> [AZURE.NOTE] Vous ne pouvez substituer concentrateur IoT pour les concentrateurs d’événements dans tous les scénarios. Par exemple, dans certains calculs de traitement d’événement, il peut être nécessaire repartitionner les événements par rapport à une autre propriété ou un champ avant d’analyser les flux de données. Dans ce scénario, vous pouvez utiliser un concentrateur d’événements de dissocier les deux parties du pipeline de traitement de flux. Pour plus d’informations, consultez des *Partitions* dans [Vue d’ensemble des concentrateurs Azure événement][lnk-eventhub-partitions].

Pour plus d’informations sur la façon d’utiliser la messagerie de périphérique-nuage, consultez [IoT concentrateur API et les kits de développement logiciel][lnk-sdks].

> [AZURE.NOTE] Lorsque vous utilisez HTTP pour envoyer des messages de périphérique-nuage, les valeurs et les noms de propriété peuvent contenir uniquement des caractères alphanumériques ASCII, plus ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="non-telemetry-traffic"></a>Trafic de non-télémétrie

Souvent, en plus de points de données de télémétrie, périphériques également envoient des messages et des demandes qui nécessitent l’exécution et la gestion de la couche de logique métier application. Par exemple, les alertes critiques qui doivent déclencher une action spécifique dans le back-end ou des réponses de dispositif de commandes envoyées depuis le serveur principal.

Pour plus d’informations sur la meilleure façon de traiter ce type de message, consultez le [didacticiel : comment traiter les messages de périphérique-nuage IoT concentrateur] [ lnk-d2c-tutorial] didacticiel.

### <a name="device-to-cloud-configuration-options"></a>Options de configuration de périphérique-nuage

Un concentrateur IoT expose les propriétés suivantes pour vous permettent de contrôler le périphérique-nuage de messagerie.

* **Nombre de partitions**. Définir cette propriété lors de la création pour définir le nombre de partitions pour la réception des événements de périphérique-nuage.
* **Durée de rétention**. Cette propriété spécifie la durée de rétention des messages de périphérique-nuage. La valeur par défaut est d’un jour, mais il peut être augmenté de sept jours.

Comme à des concentrateurs d’événement, IoT Hub vous permet également de gérer des groupes de consommateurs sur le périphérique-nuage recevoir le point de terminaison.

Vous pouvez modifier toutes ces propriétés, soit par programme via le [fournisseur de ressources IoT concentrateur reste API][lnk-resource-provider-apis], ou en utilisant le [portail Azure][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Propriétés de la fonction anti-usurpation

Pour éviter l’usurpation d’identité dans les messages de périphérique-nuage, IoT concentrateur de périphérique tampons tous les messages avec les propriétés suivantes :

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Les deux premiers contiennent **l’ID de périphérique** et **generationId** de l’équipement d’origine, en fonction des [Propriétés d’identité de périphérique][lnk-device-properties].

La propriété **ConnectionAuthMethod** contient un objet JSON sérialisé, avec les propriétés suivantes :

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Messages du nuage vers le périphérique

Vous envoyez des messages de nuage-dispositif via un point de terminaison de service orienté (**/messages/devicebound**). Un périphérique reçoit les via un point de terminaison spécifique à l’appareil (**/devices/ {deviceId} / messages/devicebound**).

Chaque message de nuage-dispositif est destiné à un seul périphérique en affectant à la propriété **to** **/devices/ {deviceId} / messages/devicebound**.

>[AZURE.IMPORTANT] Chaque file d’attente conserve au maximum 50 messages de nuage vers le périphérique. Essayez d’envoyer des messages plus les mêmes résultats de dispositif dans une erreur.

> [AZURE.NOTE] Lorsque vous envoyez des messages du nuage vers le périphérique, les valeurs et les noms de propriété peuvent contenir uniquement des caractères alphanumériques ASCII, plus ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="message-lifecycle"></a>Cycle de vie du message

Pour garantir la remise du message au moins une fois, IoT concentrateur persiste nuage-DISPOSITIF des messages dans les files d’attente du périphérique. Périphériques doivent confirmer explicitement d' *achèvement* pour concentrateur IoT pour les supprimer de la file d’attente. Cela garantit la résilience contre les pannes des périphériques et de connectivité.

Le diagramme suivant montre le graphique d’état du cycle de vie d’un message du nuage vers le périphérique.

![Message de nuage-dispositif de cycle de vie][img-lifecycle]

Lorsque le service envoie un message, elle est considérée comme *en attente*. Lorsqu’un périphérique souhaite *recevoir* un message, IoT concentrateur *verrous* le message (affecte à l’état **Invisible**) permettre à d’autres threads sur le même périphérique pour recevoir d’autres messages. Lorsqu’un thread du périphérique a terminé le traitement d’un message, il avertit IoT Hub à *la fin de* votre message.

Un périphérique peut également :

- *Rejeter* le message, ce qui entraîne le concentrateur IoT lui affecter l’état de **Deadlettered** . Remarque : périphériques qui se connectent avec MQTT ne peuvent pas rejeter les messages de C2D.
- *Abandonner* le message, ce qui entraîne le concentrateur IoT replacer le message dans la file d’attente, l’état valeur **en file d’attente**.

Un thread peut échouer traiter un message sans avertir IoT concentrateur. Dans ce cas, les messages automatiquement la transition de l’état **Invisible** à l’état de la **file d’attente** après un *délai d’attente de visibilité (ou verrouillage)*. La valeur par défaut de ce délai est d’une minute.

Un message de faire la transition entre la **file d’attente** et **Invisible** États pour, au maximum, le nombre de fois spécifié dans la propriété **count de remise max** IoT concentrateur. Après ce nombre de transitions, IoT Hub définit l’état du message à **Deadlettered**. De même, IoT Hub définit l’état d’un message à **Deadlettered** après son délai d’expiration (voir [durée de vie][lnk-ttl]).

Pour un didacticiel sur les messages du nuage vers le périphérique, consultez [didacticiel : comment envoyer des messages de nuage-dispositif avec concentrateur de IoT][lnk-c2d-tutorial]. Pour des rubriques de référence sur la façon dont les différentes API et kits de développement logiciel exposent les fonctionnalités de nuage vers le périphérique, consultez [IoT concentrateur API et les kits de développement logiciel][lnk-sdks].

> [AZURE.NOTE] En règle générale, les messages de nuage-DISPOSITIF terminer chaque fois que la perte du message n’affecte pas la logique d’application. Par exemple, le contenu du message a été correctement rendu persistant dans le stockage local, ou une opération a été exécutée avec succès. Le message peut aussi réaliser des informations temporaires, dont la perte n’aurait aucun impact sur les fonctionnalités de l’application. Parfois, pour les tâches de durée d’exécution longue, vous pouvez effectuer le message du nuage vers le périphérique après la persistance de la description de la tâche dans le stockage local. Puis, vous pouvez avertir l’application back-end avec un ou plusieurs messages de périphérique-nuage à différents stades de la progression de la tâche.

### <a name="message-expiration-time-to-live"></a>Expiration du message (durée de vie)

Chaque message du nuage vers le périphérique a un délai d’expiration. Cette heure est définie par le service (dans la propriété **ExpiryTimeUtc** ), soit par IoT concentrateur à l’aide de la par défaut *durée de vie* spécifiée comme une propriété IoT concentrateur. Voir [les options de configuration Cloud-DISPOSITIF][lnk-c2d-configuration].

> [AZURE.NOTE] Une méthode courante pour tirer parti de l’expiration d’un message et d’éviter l’envoi de messages pour les périphériques déconnectés, consiste à définir les peu de temps aux valeurs de live. Cette approche permet d’obtenir le même résultat que la maintenance de l’état de connexion de périphérique, tout en étant plus efficace. Lorsque vous demandez des accusés de réception de message, IoT Hub vous avertit les périphériques qui sont en mesure de recevoir des messages, et les périphériques qui ne sont pas en ligne ou en panne.

### <a name="message-feedback"></a>Message de feedback

Lorsque vous envoyez un message de nuage vers le périphérique, le service peut demander la fourniture de commentaires par message en ce qui concerne l’état final de ce message.

- Si vous affectez à la propriété **d’accusé de réception** **positif**, IoT concentrateur génère un message d’évaluation si, et seulement si, le message de nuage-appareil a atteint l’état **terminé** .
- Si vous affectez à la propriété **d’accusé de réception** **négatif**, IoT concentrateur génère un message de retour, si et seulement si, le message de nuage-DISPOSITIF atteint l’état **Deadlettered** .
- Si vous définissez la propriété **d’accusé de réception** **complet**, IoT concentrateur génère un message de retour dans les deux cas.

> [AZURE.NOTE] Si **l’accusé de réception** est **plein**, et vous ne recevez pas de message de votre commentaire, cela signifie que le message d’évaluation a expiré. Le service ne peut pas savoir que s’est-il passé dans le message d’origine. Dans la pratique, un service doit s’assurer qu’il peut traiter les commentaires avant son expiration. Le délai d’expiration maximum deux jours, ce qui permet de suffisamment de temps pour obtenir le service exécute à nouveau si une défaillance se produit.

Comme expliqué dans les [points de terminaison][lnk-endpoints], IoT concentrateur fournit des commentaires via un point de terminaison de service orienté (**/messages/servicebound/feedback**) en tant que messages. La sémantique pour recevoir vos commentaires sont les mêmes que pour les messages du nuage vers le périphérique et le même [cycle de vie du Message][lnk-lifecycle]. Chaque fois que possible, des commentaires de message sont regroupés dans un seul message, au format suivant :

| Propriété | Description |
| -------- | ----------- |
| EnqueuedTime | Horodatage qui indique quand le message a été créé. |
| ID utilisateur | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

Le corps est un tableau JSON sérialisées d’enregistrements, chacun avec les propriétés suivantes :

| Propriété | Description |
| -------- | ----------- |
| EnqueuedTimeUtc | Horodatage qui indique quand le résultat du message est arrivé. Par exemple, le périphérique terminée ou le message a expiré. |
| OriginalMessageId | **MessageId** du message nuage vers le périphérique auquel se rapporte cette information de retour. |
| StatusCode | Argument de type integer obligatoire. Utilisé dans les messages de feedback générés par IoT concentrateur. <br/> 0 = succès <br/> 1 = message expiré <br/> 2 = dépassé du nombre maximal de remise <br/> 3 = message rejeté |
| Description | Les valeurs de chaîne pour **StatusCode**. |
| ID de périphérique | **ID de périphérique** de l’équipement cible du message nuage vers le périphérique auquel se rapporte ce morceau de commentaires. |
| DeviceGenerationId | **DeviceGenerationId** de l’équipement cible du message nuage vers le périphérique auquel se rapporte ce morceau de commentaires. |


>[AZURE.IMPORTANT] Le service doit spécifier un **MessageId** pour le message de nuage-DISPOSITIF pouvoir faire correspondre ses commentaires avec le message d’origine.

L’exemple suivant montre le corps d’un message de retour.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>Options de configuration du nuage vers le périphérique

Chaque concentrateur IoT expose des options de configuration suivantes pour la messagerie du nuage vers le périphérique.

| Propriété | Description | Plage et par défaut |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | Durée de vie par défaut des messages de nuage-dispositif. | Intervalle de ISO_8601 jusqu'à 2D (1 minute minimum). Par défaut : 1 heure. |
| maxDeliveryCount | Nombre maximal de remise pour les files d’attente du nuage vers le périphérique par périphérique. | 1 à 100. Par défaut : 10. |
| feedback.ttlAsIso8601 | Conservation des messages de service liés aux commentaires. | Intervalle de ISO_8601 jusqu'à 2D (1 minute minimum). Par défaut : 1 heure. |
| feedback.maxDeliveryCount | Nombre maximal de remise pour la file d’attente de commentaires. | 1 à 100. Par défaut : 100. |

Pour plus d’informations, consultez [créer un IoT concentrateurs][lnk-portal].

## <a name="read-device-to-cloud-messages"></a>Lire des messages de périphérique-nuage

Concentrateur de IoT expose un point de terminaison de vos services back-end lire les messages de périphérique-nuage reçus par votre concentrateur. Le point de terminaison est événement prend en charge le concentrateur compatible, ce qui vous permet d’utiliser un des mécanismes du service événements concentrateurs pour lire des messages.

Lorsque vous utilisez le [SDK du Bus de Service Azure pour .NET] [ lnk-servicebus-sdk] ou les [Concentrateurs d’événements - événements processeur hôte][lnk-eventprocessorhost], vous pouvez utiliser les chaînes de connexion IoT Hub avec les autorisations appropriées. Puis utilisez **messages/événements** comme nom de concentrateur de l’événement.

Lorsque vous utilisez des kits de développement logiciel (ou l’intégration de produit) qui ne sont pas conscients du concentrateur de IoT, vous devez récupérer un point de terminaison compatible avec concentrateur événement et d’un nom d’événement compatible avec le concentrateur à partir des paramètres IoT concentrateur dans [Azure portal][lnk-management-portal]:

1. De la lame de concentrateur IoT, cliquez sur **messagerie**.
2. Dans la section **paramètres de périphérique-nuage** , vous recherchez les valeurs suivantes : **événement concentrateur compatible avec le point de terminaison**, le **nom de l’événement compatible avec le concentrateur**et **Partitions**.

    ![Paramètres de périphérique-nuage][img-eventhubcompatible]

> [AZURE.NOTE] Si le Kit de développement logiciel requiert une valeur de **nom d’hôte** ou **Namespace** , supprimer le modèle de l' **événement concentrateur compatible avec le point de terminaison**. Par exemple, si votre point de terminaison compatible avec concentrateur événement est **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, le **nom d’hôte** **iothub-ns-myiothub-1234.servicebus.windows.net**, alors que le **Namespace** serait **iothub-ns-myiothub-1234**.

Vous pouvez ensuite utiliser une stratégie de sécurité accès partagé qui dispose des autorisations pour se connecter au concentrateur d’événements spécifié **ServiceConnect** .

Si vous avez besoin construire une chaîne de connexion du concentrateur d’événements en utilisant les informations précédentes, utilisez le modèle suivant :

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Voici une liste de kits de développement logiciel et les intégrations que vous pouvez utiliser avec les points de terminaison concentrateur d’événements compatibles qui expose les IoT Hub :

* [Client de concentrateurs d’événement Java](https://github.com/hdinsight/eventhubs-client)
* [Bec de tempête d’Apache](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Vous pouvez afficher le [BEC VERSEUR source](https://github.com/apache/storm/tree/master/external/storm-eventhubs) sur GitHub.
* [Intégration de l’allumage d’Apache](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes vous fournissent plus d’informations sur l’échange de messages avec IoT concentrateur.

## <a name="message-format"></a>Format de message

Messages de concentrateur de IoT comprennent :

* Un ensemble de *Propriétés système*. Propriétés IoT concentrateur interprète ou définit. Ce jeu est prédéterminé.
* Un ensemble de *Propriétés de l’application*. Dictionnaire de propriétés de chaîne que l’application peut définir et des accès, sans avoir besoin de désérialiser le corps du message. IoT concentrateur ne modifie jamais ces propriétés.
* Un corps binaire opaque.

Pour plus d’informations sur la façon dont le message est codé dans différents protocoles, consultez [IoT concentrateur API et les kits de développement logiciel][lnk-sdks].

Le tableau suivant répertorie l’ensemble des propriétés système dans les messages de IoT concentrateur.

| Propriété | Description |
| -------- | ----------- |
| MessageId | Un identificateur définissable par l’utilisateur pour le message, utilisé pour les modèles de demande-réponse. Format : Une chaîne respectant la casse (jusqu'à 128 caractères maximum) des caractères alphanumériques ASCII 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numéro de séquence | Numéro (spécifique à chaque périphérique de la file d’attente) affecté par IoT concentrateur à chaque message de nuage vers le périphérique. |
| À | Une destination spécifiée dans le [Nuage-DISPOSITIF] [ lnk-c2d] messages. |
| ExpiryTimeUtc | Date et heure d’expiration du message. |
| EnqueuedTime | Date et heure que le message a été reçu par IoT concentrateur. |
| ID de corrélation | Une propriété de type chaîne dans un message de réponse qui en général contient l’ID du message de la demande, dans les modèles de demande-réponse. |
| ID utilisateur | ID utilisé pour spécifier l’origine des messages. Lorsque des messages sont générés par IoT concentrateur, elle est définie sur `{iot hub name}`. |
| Accusé de réception | Un générateur de message de retour. Cette propriété est utilisée dans les messages du nuage-dispositif pour demander le concentrateur IoT pour générer des messages de retour à la suite de la consommation du message par le périphérique. Valeurs possibles : **Aucun** (par défaut) : aucun message de retour est généré, **positif**: recevoir un message d’évaluation si le message a été effectué, **négatif**: recevoir un message d’évaluation si le message a expiré (ou nombre de livraison maximum a été atteint) sans réalisée par le périphérique ou **complet**: positif et négatif. Pour plus d’informations, consultez le [Message de feedback dans][lnk-feedback]. |
| ConnectionDeviceId | Un ID défini par IoT Hub sur les messages de périphérique-nuage. Il contient l' **ID de périphérique** du périphérique qui a envoyé le message. |
| ConnectionDeviceGenerationId | Un ID défini par IoT Hub sur les messages de périphérique-nuage. Il contient le **generationId** (en fonction des [Propriétés d’identité de périphérique][lnk-device-properties]) du périphérique qui a envoyé le message. |
| ConnectionAuthMethod | Une méthode d’authentification définie par IoT concentrateur de messages de périphérique-nuage. Cette propriété contient des informations sur la méthode d’authentification utilisée pour authentifier le périphérique d’envoi du message. Pour plus d’informations, reportez-vous à la section [périphérique vers le cloud à l’usurpation d’identité anti][lnk-antispoofing].|

## <a name="communication-protocols"></a>Protocoles de communication

IoT Hub permet de périphériques à utiliser [MQTT][lnk-mqtt], MQTT via le protocole WebSocket, [AMQP][lnk-amqp], AMQP via le protocole WebSocket et HTTP des protocoles pour les communications de l’appareil. Le tableau suivant fournit des recommandations de haut niveau pour votre choix de protocole :

| Protocole | Lorsque vous optez pour ce protocole |
| -------- | ------------------------------------ |
| MQTT <br> MQTT sur WebSocket     | Utiliser sur tous les périphériques qui n’ont pas besoin de connecter plusieurs périphériques (chacun avec ses propres informations d’identification de périphérique) sur la même connexion TLS. |
| AMQP <br> AMQP sur WebSocket    | Utiliser sur les passerelles de champ et de cloud pour tirer parti de multiplexage sur les périphériques de connexion. |
| HTTP    | Utilisation de périphériques qui ne prennent pas en charge les autres protocoles. |

Lorsque vous choisissez votre protocole pour les communications côté périphérique, tenez compte des points suivants :

* **Motif de nuage vers le périphérique**. HTTP n’a pas un moyen efficace de mettre en œuvre des émetteurs du serveur. En tant que tel, lorsque vous utilisez le protocole HTTP, périphériques interrogent IoT Hub pour les messages du nuage vers le périphérique. Cette approche est inefficace pour le périphérique et le concentrateur de IoT. Sous les directives actuelles concernant HTTP, chaque périphérique doit vérifier les messages toutes les 25 minutes ou plus. D’autre part, MQTT et AMQP prennent en charge les émetteurs du serveur lors de la réception des messages du nuage vers le périphérique. Ils permettent la pousse immédiate des messages IoT concentrateur vers le périphérique. Si la latence de remise pose un problème, MQTT ou AMQP sont les meilleures protocoles à utiliser. Pour les périphériques rarement connectés, HTTP s’applique également.
* **Les passerelles de champ**. Lors de l’utilisation de HTTP et MQTT, vous ne pouvez pas connecter plusieurs périphériques (chacun avec ses propres informations d’identification par périphérique) utilisant la même connexion TLS. Ainsi, pour les [scénarios de passerelle de champ][lnk-azure-gateway-guidance], ces protocoles sont non optimaux, car ils exigent une connexion TLS entre la passerelle de champ et d’un concentrateur de IoT chaque périphérique connectée à la passerelle de champ.
* **Périphériques de ressources insuffisantes**. Les bibliothèques MQTT et HTTP ont un encombrement moindre les bibliothèques AMQP. Par conséquent, si le périphérique a limité des ressources (par exemple, moins de 1 Mo de mémoire vive), ces protocoles peuvent être la seule implémentation de protocole disponible.
* **Parcours du réseau**. Le protocole AMQP standard utilise le port 5671, alors que MQTT est à l’écoute sur le port 8883, ce qui peut provoquer des problèmes dans les réseaux qui sont fermés à des protocoles non-HTTP. MQTT via le protocole WebSocket, AMQP via le protocole WebSocket et HTTP sont disponibles pour être utilisés dans ce scénario.
* **Taille de charge utile**. MQTT et AMQP sont des protocoles binaires, qui entraînent des charges plus compacts que HTTP.

> [AZURE.NOTE] Lorsque vous utilisez HTTP, chaque périphérique doit interroger pour les messages du nuage-DISPOSITIF toutes les 25 minutes ou plus. Toutefois, au cours du développement, il est acceptable d’interrogation plus fréquemment que toutes les 25 minutes.

## <a name="port-numbers"></a>Numéros de port

Les périphériques peuvent communiquer avec concentrateur IoT dans Azure en utilisant divers protocoles. En général, le choix du protocole est piloté par les exigences spécifiques de la solution. Le tableau suivant répertorie les ports sortants qui doivent être ouverts pour un périphérique d’être en mesure d’utiliser un protocole spécifique :

| Protocole | Port (s) |
| -------- | ------- |
| MQTT     | 8883    |
| MQTT via le protocole WebSocket | 443    |
| AMQP     | 5671    |
| AMQP via le protocole WebSocket | 443    |
| HTTP     | 443     |
| LWM2M (gestion des périphériques) | 5684 |

Une fois que vous avez créé un concentrateur IoT dans une région d’Azure, le concentrateur conserve la même adresse IP pour la durée de vie de ce concentrateur. Toutefois, pour maintenir la qualité de service, si Microsoft dirige le concentrateur IoT à une unité d’échelle différente puis il reçoit une nouvelle adresse IP.

## <a name="notes-on-mqtt-support"></a>Remarques sur la prise en charge MQTT

Concentrateur de IoT implémente le protocole de v3.1.1 MQTT avec le comportement spécifique et des limitations suivantes :

  * **QoS 2 n’est pas pris en charge**. Lorsqu’un client de périphérique publie un message avec **QoS 2**, IoT concentrateur ferme la connexion réseau. Lorsqu’un client d’appareil s’abonne à une rubrique avec **QoS 2**, IoT concentrateur accorde maximale QoS de niveau 1 dans le paquet **SUBACK** .
  * **Conserver les messages ne sont pas conservées**. Ajoute de concentrateur de IoT si un client de périphérique publie un message avec l’indicateur de conserver la valeur 1, le **x-opt-conserver les** propriétés d’application pour le message. Dans ce cas, IoT concentrateur ne persiste pas le message de conservation, mais à la place de la passe à l’application back-end.

Pour plus d’informations, reportez-vous à la section [MQTT de concentrateur IoT prend en charge les][lnk-devguide-mqtt].

En termes de finale, vous devez examiner la [passerelle de protocole Azure IoT] [ lnk-azure-protocol-gateway] qui vous permet de déployer une passerelle de protocole personnalisé de hautes performances qui communique directement avec IoT concentrateur. La passerelle de protocole Azure IoT vous permet de personnaliser le protocole de périphérique pour prendre en charge des déploiements de MQTT brownfield ou autres protocoles personnalisés. Cette approche nécessite, toutefois, que vous exécutez et fonctionner une passerelle de protocole personnalisé.

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

D’autres rubriques de référence dans le Guide du développeur incluent :

- [Les points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison qui expose de chaque concentrateur IoT pour les opérations de gestion et d’exécution.
- [Les quotas et la régulation] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT Hub et le comportement d’accélération s’attendre lorsque vous utilisez le service.
- [SDK de périphérique et service IoT concentrateur] [ lnk-sdks] répertorie la langue différents kits de développement logiciel vous une utilisation lorsque vous développez des services et des périphériques à la fois les applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, procédés et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations du concentrateur de IoT sur vos jumeaux de périphérique, les méthodes et les tâches.
- [Prise en charge de IoT concentrateur MQTT] [ lnk-devguide-mqtt] fournit plus d’informations sur la prise en charge de IoT Hub pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous avez appris à envoyer et recevoir des messages avec IoT concentrateur, peut vous intéresser dans les rubriques du Guide de développement suivantes :

- [Télécharger des fichiers à partir d’un périphérique][lnk-devguide-upload]
- [Gérer les identités de périphérique IoT moyeu][lnk-devguide-identities]
- [Contrôle de l’accès à IoT concentrateur][lnk-devguide-security]
- [Des jumeaux de périphérique permet de synchroniser l’état et les configurations][lnk-devguide-device-twins]
- [Appeler une méthode directe sur un périphérique][lnk-devguide-directmethods]
- [Planifier des tâches sur plusieurs périphériques][lnk-devguide-jobs]

Si vous souhaitez essayer certaines des concepts décrits dans cet article, vous intéresser dans les didacticiels IoT Hub suivants :

- [Mise en route avec Azure IoT concentrateur][lnk-getstarted-tutorial]
- [Comment envoyer des messages de nuage-dispositif avec IoT concentrateur][lnk-c2d-tutorial]
- [Comment traiter les messages de périphérique-nuage IoT concentrateur][lnk-d2c-tutorial]


[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
