<properties
 pageTitle="Surveillance à distance préconfigurées de procédure pas à pas de solution | Microsoft Azure"
 description="Une description de la IoT Azure préconfiguré de son architecture et solution de surveillance à distance."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="dobett"/>

# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Surveillance à distance préconfigurées de procédure pas à pas de solution

## <a name="introduction"></a>Introduction

La télécommande IoT Suite [solution préconfigurée] de surveillance[ lnk-preconfigured-solutions] est une implémentation d’un bout à bout surveillance solution pour plusieurs ordinateurs dans des emplacements distants. La solution combine des services Azure clés pour fournir une implémentation générique d’au scénario d’entreprise, et vous pouvez l’utiliser comme point de départ pour votre propre implémentation. Vous pouvez [Personnaliser] [ lnk-customize] la solution pour répondre à vos besoins professionnels spécifiques.

Cet article vous guide parmi les éléments clés de la solution de surveillance à distance pour vous permettre de comprendre comment il fonctionne. Ces connaissances vous aide à :

- Résoudre les problèmes dans la solution.
- Planifier la personnalisation à la solution pour répondre à vos besoins spécifiques. 
- Concevoir votre propre solution IoT qui utilise des services Azure.

## <a name="logical-architecture"></a>Architecture logique

Le schéma suivant présente les composants logiques de la solution préconfigurée :

![Architecture logique](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## <a name="simulated-devices"></a>Simulations de périphériques

Dans la solution préconfigurée, périphérique simulé représente un dispositif de refroidissement (par exemple, un conditionneur d’air de construction ou une unité de traitement d’air facility). Lorsque vous déployez la solution préconfigurée, vous configurez également automatiquement quatre périphériques simulés qui s’exécutent dans un [Azure WebJob][lnk-webjobs]. Les simulations de périphériques facilement vous permet d’Explorer le comportement de la solution sans avoir besoin de déployer des périphériques physiques. Pour déployer un appareil physique réel, consultez la [solution préconfigurée de surveillance de connecter votre appareil au serveur distant] [ lnk-connect-rm] didacticiel.

Chaque périphérique simulé peut envoyer les types de message suivants à IoT concentrateur :

| Message  | Description |
|----------|-------------|
| Démarrage  | Lorsque le périphérique démarre, il envoie un message **d’informations du périphérique** contenant des informations sur lui-même pour le back-end. Ces données incluent l’id de périphérique, les métadonnées de périphérique, la liste des commandes le périphérique prend en charge et la configuration actuelle de l’appareil. |
| Présence | Un périphérique envoie régulièrement un message de **présence** pour signaler si le périphérique peut détecter la présence d’un capteur. |
| Télémétrie | Un périphérique envoie régulièrement un message de **télémétrie** qui affiche les valeurs de la température et l’humidité collectées à partir des capteurs de simulation du périphérique simulés. |


Les simulations de périphériques envoie les propriétés du périphérique suivant dans un message de **périphérique-info** :

| Propriété               |  Objectif |
|------------------------|--------- |
| ID de périphérique              | ID qui est fourni ou affecté lors de la création d’une unité dans la solution. |
| Fabricant           | Fabricant du périphérique |
| Numéro de modèle           | Numéro de modèle du périphérique |
| Numéro de série          | Numéro de série du périphérique |
| Firmware               | Version actuelle du micrologiciel sur le périphérique |
| Plate-forme               | Architecture de la plate-forme du périphérique |
| Processeur              | Processeur du périphérique en cours d’exécution |
| Mémoire RAM installée          | Quantité de RAM installée sur le périphérique |
| État activé de concentrateur      | Propriété d’état IoT Hub du périphérique |
| Heure de création           | Le périphérique a été créé dans la solution de temps |
| Heure mise à jour           | Heure de la dernière propriétés ont été mises à jour pour le périphérique |
| Latitude               | Emplacement de latitude du périphérique |
| Longitude              | Emplacement de longitude du périphérique |

Le simulateur de graines de ces propriétés dans les simulations de périphériques avec des exemples de valeurs.  Chaque fois que le simulateur Initialise un périphérique simulé, le périphérique enregistre les métadonnées prédéfinies à IoT concentrateur. Notez comment cette opération remplace les métadonnées mises à jour dans le portail du périphérique.


Les simulations de périphériques peuvent gérer les commandes suivantes, envoyées à partir du tableau de bord de solution via le concentrateur IoT :

| Commande                | Description                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Envoie une _commande ping_ sur le périphérique pour vérifier qu’il est actif   |
| StartTelemetry         | Démarre le périphérique d’envoi de télémétrie                 |
| StopTelemetry          | Arrête le périphérique à partir de l’envoi de télémétrie             |
| ChangeSetPointTemp     | Modifie la valeur de définir le point autour duquel les données aléatoires sont générées. |
| DiagnosticTelemetry    | Déclenche le simulateur de l’appareil pour envoyer une valeur de télémétrie supplémentaires (externalTemp) |
| ChangeDeviceState      | Modifie une propriété de l’état étendu pour le périphérique et envoie le message d’informations sur le périphérique à partir du périphérique |

L’accusé de réception de commande de dispositif sur le back-end de solution est fournie par le concentrateur IoT.

## <a name="iot-hub"></a>Concentrateur de IoT

Le [concentrateur de IoT] [ lnk-iothub] ingests les données envoyées à partir des périphériques dans le nuage et les rend disponibles pour les tâches de flux de Azure Analytique (ASA). IoT concentrateur envoie également des commandes pour vos périphériques au nom du portail de périphérique. Chaque tâche ASA de flux utilise un groupe de consommateurs de IoT concentrateur distinct pour lire le flux des messages à partir de vos périphériques.

## <a name="azure-stream-analytics"></a>Analytique de flux Azure

Dans la solution de surveillance à distance, [Azure flux Analytique] [ lnk-asa] (ASA) distribue les messages de périphérique concentrateur IoT à d’autres composants back-end de traitement ou de stockage. Différentes tâches ASA exécutent des fonctions spécifiques en fonction du contenu des messages.

**Travail 1 : informations sur le périphérique** filtre les messages d’informations de périphérique à partir du flux de messages entrants et les envoie à un point de terminaison du concentrateur de l’événement. Un périphérique envoie des messages d’informations de périphérique au démarrage et en réponse à une commande de **SendDeviceInfo** . Cette tâche utilise la définition de requête suivant pour identifier les messages **d’informations du périphérique** :

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Cette tâche envoie sa sortie à un concentrateur d’événements pour un traitement ultérieur.

**Travail 2 : règles** évalue les valeurs entrantes télémétrie de température et d’humidité par rapport aux seuils de chaque périphérique. Les valeurs de seuil sont définies dans l’éditeur de règles disponible dans le tableau de bord de solution. Chaque paire de périphérique/valeur est stockée par horodatage dans un blob qui Analytique de flux lit les données de **Référence**. La tâche compare une valeur non vide contre le seuil défini pour le périphérique. S’il dépasse le ' >' condition, la tâche génère un événement **d’alarme** qui indique que le seuil est dépassé et fournit le dispositif, valeur et valeurs d’horodatage. Cette tâche utilise la définition de requête suivant pour identifier les messages de télémétrie qui doivent déclencher une alarme :

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

La tâche envoie sa sortie à un concentrateur d’événements pour un traitement ultérieur et enregistre les détails de chaque alerte pour le stockage des objets blob d’où le tableau de bord de solution peut lire les informations d’alerte.

**Travail 3 : télémétrie** fonctionne sur le flux entrant de télémétrie périphérique de deux manières. Le premier envoie tous les messages de télémétrie à partir des périphériques de stockage persistant blob pour le stockage à long terme. La deuxième calcule les valeurs de l’humidité moyenne, minimale et maximale sur une fenêtre glissante de cinq minutes et envoie ces données pour le stockage des objets blob. Le tableau de bord de solution lit les données de télémétrie depuis le stockage blob pour remplir les graphiques. Cette tâche utilise la définition de requête suivante :

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Concentrateurs d’événement

Les travaux ASA **Infos sur l’appareil** et les **règles** de sortie leurs données à des concentrateurs d’événement fiable transférer sur le **Processeur d’événements** est en cours d’exécution dans le WebJob.

## <a name="azure-storage"></a>Stockage Azure

La solution utilise le stockage blob Azure pour conserver toutes les données de télémétrie brutes et résumées à partir des périphériques de la solution. Le tableau de bord lit les données de télémétrie depuis le stockage blob pour remplir les graphiques. Pour afficher des messages, le tableau de bord lit les données depuis le stockage blob qui enregistre lorsque les valeurs de TELEMETRIE a dépassé les valeurs seuil configuré. La solution utilise également le stockage blob pour enregistrer les valeurs de seuil que vous définissez dans le tableau de bord.

## <a name="webjobs"></a>WebJobs

Outre l’hébergement les simulateurs de périphérique, les WebJobs dans la solution a également héberger le **Processeur d’événements** est en cours d’exécution dans un WebJob Azure qui gère les messages de d’informations de périphérique et les réponses aux commandes. Il utilise :

- Messages d’informations de périphérique à mettre à jour le Registre de périphérique (stocké dans la base de données DocumentDB) avec les informations de périphérique en cours.
- Messages de réponse de commande Mettre à jour l’historique des commandes de périphérique (stockée dans la base de données DocumentDB).

## <a name="documentdb"></a>DocumentDB

La solution utilise une base de données DocumentDB pour stocker des informations sur les périphériques connectés à la solution. Ces informations incluent des métadonnées de périphérique et de l’historique des commandes envoyées vers des périphériques à partir du tableau de bord.

## <a name="web-apps"></a>Applications Web

### <a name="remote-monitoring-dashboard"></a>Tableau de bord de surveillance à distance
Cette page de l’application web utilise des contrôles de javascript PowerBI (voir [mis en pension de PowerBI-visuels](https://www.github.com/Microsoft/PowerBI-visuals)) pour visualiser les données de télémétrie à partir des périphériques. La solution utilise le travail de télémétrie ASA pour écrire les données de télémétrie pour le stockage blob.


### <a name="device-administration-portal"></a>Portail d’administration de périphérique

Cette application web vous permet de :

- Mettre en service un nouveau périphérique. Cette action définit l’id de périphérique unique et génère la clé d’authentification. Il écrit des informations sur le périphérique dans le Registre d’identité IoT concentrateur et de la base de données DocumentDB de solution spécifique.
- Gérer les propriétés du périphérique. Cette action comprend l’affichage des propriétés existantes et la mise à jour avec de nouvelles propriétés.
- Envoyer des commandes à un périphérique.
- Afficher l’historique de commande pour un périphérique.
- Activer et désactiver des périphériques.

## <a name="next-steps"></a>Étapes suivantes

Les billets de blog de TechNet suivants fournissent plus de détails sur la solution préconfigurée surveillance à distance :

- [La surveillance à distance IoT Suite - dans les coulisses :](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [Suite de IoT - surveillance à distance - Ajout de périphériques simulés et en temps réel](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Vous pouvez continuer la mise en route avec IoT Suite, lisez les articles suivants :

- [Connectez votre périphérique à la solution de surveillance préconfigurée à distance][lnk-connect-rm]
- [Autorisations sur le site azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md