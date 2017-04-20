<properties
 pageTitle="Guide du développeur - méthodes directes | Microsoft Azure"
 description="Guide du développeur Azure IoT Hub - utilisation de méthodes directes pour appeler du code sur vos périphériques"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>

# <a name="invoke-a-direct-method-on-a-device-preview"></a>Appeler une méthode directe sur un périphérique (aperçu)

## <a name="overview"></a>Vue d’ensemble

IoT Hub vous donne la possibilité d’appeler des méthodes sur des périphériques à partir du cloud. Les méthodes représentent une interaction de demande-réponse avec un dispositif similaire à un appel HTTP dans la mesure où ils réussissent ou échouent immédiatement (après un délai d’attente spécifié par l’utilisateur) permettre à l’utilisateur de connaître l’état de l’appel. Cela est utile pour les scénarios où, au cours d’une action immédiate est différent selon que le périphérique a été en mesure de répondre, telles que l’envoi d’une réactivation de SMS à un périphérique si le périphérique est en mode hors connexion (SMS est plus coûteuse qu’un appel de méthode).

Vous pouvez considérer d’une méthode comme un appel de procédure distante directement sur le périphérique. Seules les méthodes qui ont été implémentées sur un périphérique peuvent être appelées à partir du cloud. Si le nuage tente d’appeler une méthode sur un périphérique qui n’a pas cette méthode définie, l’appel de méthode échoue.

Chaque méthode de périphérique cible à un seul périphérique. [Tâches] [ lnk-devguide-jobs] fournissent un moyen pour appeler des méthodes sur plusieurs périphériques et file d’attente d’appel de la méthode pour les périphériques déconnectés.

Quiconque disposant d’autorisations de **service se connecter** IoT concentrateur peut appeler une méthode sur un périphérique.

### <a name="when-to-use"></a>Quand utiliser

Méthodes de périphérique sont semblables aux [messages du nuage-DISPOSITIF] [ lnk-devguide-messages] permettent à nuage back-end passer des informations à un périphérique, mais ils sont fondamentalement différents. Du point de vue conceptuel, les méthodes sont synchrones et pas durables, tandis que les messages du nuage-dispositif sont asynchrones avec jusqu'à 48 heures de durabilité.

Méthodes suivent un modèle de requête-réponse et ne sont pas durables. L’absence de durabilité fournit deux avantages immédiats lors de l’exécution de commandes périphériques :

- **Des commentaires immédiats sur l’exécution de la méthode** signifie qu’il n’y a pas besoin de vous gérer la corrélation entre la demande et de réponse.
- **Un débit plus élevé** signifie que les opérations peuvent être exécutées plus rapidement car IoT concentrateur ne fournit pas une durabilité. IoT Hub permet plus d’appels de méthode par unité que les messages du nuage vers le périphérique.

Messages du nuage vers le périphérique ne sont pas nécessairement des commandes sur le périphérique, mais représentent plutôt un service en nuage passant certains bits d’informations sur le périphérique pour pouvoir prélever à son gré et à laquelle le périphérique peut ou peut ne pas répond. Messages du nuage vers le périphérique ont un délai d’attente plus long (jusqu'à 48 heures) alors que les méthodes expirent beaucoup plus rapidement.

Utilisez des méthodes de périphérique pour un appel de commande immédiate sur un périphérique et les tâches pour l’appel planifié d’une commande sur un périphérique.

## <a name="method-lifecycle"></a>Méthode de cycle de vie

Les méthodes sont implémentées sur le périphérique et peuvent nécessiter de zéro, une ou plusieurs entrées dans la charge utile de méthode pour instancier correctement. Vous appelez une méthode directe via un URI de service orienté (`{iot hub}/twins/{device id}/methods/`). Un périphérique reçoit des méthodes directes à une rubrique spécifique à l’appareil de MQTT (`$iothub/methods/POST/{method name}/`). Nous pouvons prend en charge les méthodes sur les protocoles de réseau côté périphérique supplémentaires à l’avenir.

> [AZURE.NOTE] Lorsque vous appelez une méthode directe sur un périphérique, les valeurs et les noms de propriété peuvent contenir uniquement US-ASCII imprimable alphanumériques, à l’exception dans le jeu suivant : ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

Méthodes sont synchrones et soit réussir ou échouer après le délai d’expiration (par défaut : 30 secondes, définissables haut à 3 600 secondes). Les méthodes sont utiles dans les scénarios interactifs où vous souhaitez qu’un périphérique à agir si et seulement si le périphérique est en ligne et reçoit commandes, par exemple si une lumière à partir d’un téléphone. Dans ces scénarios, vous souhaitez voir une réussite immédiate ou une défaillance afin que le service en nuage peut agir sur le résultat dès que possible. Le périphérique peut renvoyer des corps de message à la suite de la méthode, mais il n’est pas obligatoire pour la méthode pour le faire. Aucune garantie sur la commande ou la sémantique quelconque d’accès concurrentiel pour les appels de méthode existe.

Les appels de méthode de périphérique sont HTTP uniquement du côté du nuage et MQTT uniquement à partir du côté du périphérique.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes vous fournissent plus d’informations sur l’utilisation de méthodes directes.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Appeler une méthode directe à partir d’une application back-end

### <a name="method-invocation"></a>Appel de la méthode

Appels de méthode directs sur un périphérique sont les appels HTTP qui comprennent :

- L' *URI* spécifique au périphérique (`{iot hub}/twins/{device id}/methods/`)
- La *méthode* de publication
- *Les en-têtes* contiennent l’autorisation, la demande ID, type de contenu et le codage du contenu
- Un JSON transparent *corps* dans le format suivant :

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  Délai d’attente est exprimée en secondes. Si le délai d’attente n’est pas définie, la valeur par défaut est 30 secondes.
  
### <a name="response"></a>Réponse

Back-end reçoit une réponse qui comprend :

- *Code d’état HTTP*, qui est utilisée pour les erreurs provenant du concentrateur IoT, y compris une erreur 404 pour les périphériques non connecté actuellement
- *En-têtes* contiennent l’etag, demande l’ID, le type de contenu et le codage du contenu
- Un JSON *corps* dans le format suivant :

```
{
    "status" : 201,
    "payload" : {...}
}
```
  
   Les deux `status` et `body` sont fournies par le périphérique et permet de répondre avec le code d’état du périphérique et/ou la description.

## <a name="handle-a-direct-method-on-a-devcie"></a>Gérer une méthode directe sur un devcie

### <a name="method-invocation"></a>Appel de la méthode

Périphériques de recevoir des requêtes de méthode directe sur la rubrique MQTT :`$iothub/methods/POST/{method name}/?$rid={request id}`

L’organisme qui reçoit par le périphérique est dans le format suivant :

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Les demandes de méthode sont QoS 0.

### <a name="response"></a>Réponse

Le périphérique envoie les réponses au `$iothub/methods/res/{status}/?$rid={request id}`, où :

 - Le `status` propriété est l’état fourni par dispositif d’exécution de la méthode.
 - Le `$rid` propriété est l’ID de la demande à partir de l’appel de méthode provenant d’IoT concentrateur.

Le corps est défini par le périphérique et peut être n’importe quel état.

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

D’autres rubriques de référence dans le Guide du développeur incluent :

- [Les points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison qui expose de chaque concentrateur IoT pour les opérations de gestion et d’exécution.
- [Les quotas et la régulation] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT Hub et le comportement d’accélération s’attendre lorsque vous utilisez le service.
- [SDK de périphérique et service IoT concentrateur] [ lnk-sdks] répertorie la langue différents kits de développement logiciel vous une utilisation lorsque vous développez des services et des périphériques à la fois les applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, procédés et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations du concentrateur de IoT sur vos jumeaux de périphérique, les méthodes et les tâches.
- [Prise en charge de IoT concentrateur MQTT] [ lnk-devguide-mqtt] fournit plus d’informations sur la prise en charge de IoT Hub pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous avez appris comment utiliser les méthodes directes, peuvent vous intéresser dans la rubrique Guide du développeur :

- [Planifier des tâches sur plusieurs périphériques][lnk-devguide-jobs]

Si vous souhaitez essayer certaines des concepts décrits dans cet article, vous intéresser dans ce didacticiel IoT Hub :

- [Utilisez les méthodes de nuage-appareil][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
