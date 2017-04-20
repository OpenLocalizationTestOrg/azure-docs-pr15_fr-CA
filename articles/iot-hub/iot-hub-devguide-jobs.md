<properties
 pageTitle="Guide du développeur - travaux | Microsoft Azure"
 description="Guide du développeur IoT concentrateur Azure - planification des tâches à exécuter sur plusieurs périphériques connectés à votre concentrateur"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="schedule-jobs-on-multiple-devices-preview"></a>Planifier des tâches sur plusieurs périphériques (aperçu)

## <a name="overview"></a>Vue d’ensemble

Comme indiqué par les articles précédents, Azure IoT Hub permet à un nombre de blocs de construction ([balises et propriétés double] [ lnk-twin-devguide] et [procédés de nuage-DISPOSITIF][lnk-dev-methods]).  En règle générale, les applications back-end IoT activer les administrateurs de périphérique et les opérateurs de mettre à jour et d’interagir avec les périphériques IoT en vrac et à une heure planifiée.  Travaux d’encapsulent l’exécution de mises à jour de périphérique double et méthodes de C2D par rapport à un ensemble de périphériques à la fois de la planification.  Par exemple, un opérateur utiliserait une application back-end qui serait de lancer et de suivre un travail pour redémarrer un ensemble de périphériques dans la création de 43 et étage 3 à un moment qui ne serait pas perturber les opérations du bâtiment.

### <a name="when-to-use"></a>Quand utiliser

Pensez à l’aide de tâches lorsque : en solution principale doit planifier et suivre la progression des activités suivantes sur un ensemble de périphériques :

- Mettre à jour les propriétés du périphérique double souhaité
- Mettre à jour des balises de double DISPOSITIF
- Appeler des méthodes de C2D

## <a name="job-lifecycle"></a>Cycle de vie du projet

Les tâches sont initiées par le back-end de solution et gérées par IoT concentrateur.  Vous pouvez lancer une tâche via un URI de service orienté (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-09-30-preview`) et la requête d’avancement sur une tâche en cours d’exécution via un URI de service orienté (`{iot hub}/jobs/v2/<jobId>?api-version=2016-09-30-preview`).  Une fois qu’une tâche est lancée, interrogation de travaux permettra l’application back-end actualiser le statut de l’exécution des travaux.

> [AZURE.NOTE] Lorsque vous lancez un travail, les valeurs et les noms de propriété peuvent contenir uniquement US-ASCII imprimable alphanumériques, à l’exception dans le jeu suivant : ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes vous fournissent plus d’informations sur l’utilisation des tâches.

## <a name="jobs-to-execute-direct-methods"></a>Travaux à exécuter les méthodes directes

Voici les détails de la demande HTTP 1.1 pour l’exécution d’une [méthode directe] de[ lnk-dev-methods] sur un ensemble de périphériques à l’aide d’une tâche :

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            timeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
    
## <a name="jobs-to-update-device-twin-properties"></a>Tâches à mettre à jour les propriétés du périphérique double

Voici les détails de la demande HTTP 1.1 de propriétés double du périphérique à l’aide d’une tâche de mise à jour :

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Interrogation de progression sur les tâches

Voici les détails de la demande HTTP 1.1 pour [l’interrogation de tâches][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-09-30-preview[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```
    
Le continuationToken est fournie à partir de la réponse.  

## <a name="jobs-properties"></a>Propriétés des tâches

Voici une liste de propriétés et les descriptions correspondantes, ce qui peuvent être utilisées lors de l’interrogation pour les tâches ou les résultats des travaux.

| Propriété | Description |
| -------------- | -----------------|
| **ID de travail** | Demande prévue ID de la tâche. |
| **heure de début** | Application fournie à l’heure de début (ISO-8601) pour le travail. |
| **heure de fin** | Concentrateur de IoT fourni date (ISO-8601) de fin de la tâche. Cette option est valide uniquement après que le travail atteint l’état « terminé ». | 
| **type de** | Types de tâches : |
| | **scheduledUpdateTwin**: une tâche de mise à jour d’un ensemble de balises ou des propriétés de double que vous le souhaitez. |
| | **scheduledDeviceMethod**: un travail utilisé pour appeler une méthode de périphérique sur un ensemble de double. |
| **état** | État actuel de la tâche. Valeurs possibles pour l’état : |
| | **en attente** : planifié et en attente d’être récupérées par le service de travail. |
| | **planifiée** : prévue pour un moment dans le futur. |
| | **en cours d’exécution** : travail actuellement actif. |
| | **annulé** : tâche a été annulée. |
| | **Échec** : échouée de la tâche. |
| | **terminé** : fin de la tâche. |
| **deviceJobStatistics** | Statistiques sur l’exécution de la tâche. |

Lors de la visualisation, l’objet deviceJobStatistics est disponible uniquement après que la tâche est terminée.

| Propriété | Description |
| -------------- | -----------------|
| **deviceJobStatistics.deviceCount** | Nombre de périphériques dans le travail. |
| **deviceJobStatistics.failedCount** | Nombre de périphériques où la tâche a échoué. |
| **deviceJobStatistics.succeededCount** | Nombre d’unités dans lequel le travail a réussi. |
| **deviceJobStatistics.runningCount** | Nombre de périphériques qui sont en cours d’exécution du travail. |
| **deviceJobStatistics.pendingCount** | Nombre de périphériques qui sont en attente pour exécuter la tâche. |


### <a name="additional-reference-material"></a>Documents de référence supplémentaires

D’autres rubriques de référence dans le Guide du développeur incluent :

- [Les points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison qui expose de chaque concentrateur IoT pour les opérations de gestion et d’exécution.
- [Les quotas et la régulation] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT Hub et le comportement d’accélération s’attendre lorsque vous utilisez le service.
- [SDK de périphérique et service IoT concentrateur] [ lnk-sdks] répertorie la langue différents kits de développement logiciel vous une utilisation lorsque vous développez des services et des périphériques à la fois les applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, procédés et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations du concentrateur de IoT sur vos jumeaux de périphérique, les méthodes et les tâches.
- [Prise en charge de IoT concentrateur MQTT] [ lnk-devguide-mqtt] fournit plus d’informations sur la prise en charge de IoT Hub pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez essayer certaines des concepts décrits dans cet article, vous intéresser dans ce didacticiel IoT Hub :

- [Tâches de planification et de la diffusion][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
