<properties
 pageTitle="Surveillance des opérations IoT concentrateur"
 description="Une vue d’ensemble des opérations d’Azure IoT concentrateur de surveillance, ce qui vous permet de surveiller l’état des opérations sur votre concentrateur IoT en temps réel"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-operations-monitoring"></a>Introduction à la surveillance des opérations

Surveillance des opérations IoT Hub vous permet de surveiller l’état des opérations sur votre concentrateur IoT en temps réel. IoT Hub effectue le suivi des événements entre les différentes catégories d’opérations, et vous pouvez choisir dans l’envoi d’événements à partir d’une ou plusieurs catégories à un point de terminaison de votre concentrateur IoT pour traitement. Vous pouvez analyser les données des erreurs ou paramétrer un traitement plus complexe, basée sur des modèles de données.

Concentrateur de IoT surveille les cinq catégories d’événements :

- Opérations d’identité de périphérique
- Télémesure pour dispositif
- Commandes de nuage-appareil
- Connexions
- Téléchargements de fichiers

## <a name="how-to-enable-operations-monitoring"></a>Comment faire pour activer le contrôle des opérations

1. Créer un concentrateur IoT. Vous trouverez des instructions sur la création d’un concentrateur IoT dans la [Mise en route] [ lnk-get-started] guide.

2. Ouvrez la lame de votre concentrateur IoT. À partir de là, cliquez sur **opérations de surveillance**.

    ![][1]

3. Sélectionnez les catégories de contrôle que vous souhaitez vous surveillez, puis cliquez sur **Enregistrer**. Les événements sont disponibles pour la lecture du point de terminaison compatible avec concentrateur événement répertorié dans les **paramètres de surveillance**. Le point de terminaison IoT concentrateur est appelé `messages/operationsmonitoringevents`.

    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>Catégories d’événements et comment les utiliser

Chaque contrôle de pistes de la catégorie des opérations un autre type d’interaction avec IoT concentrateur et chaque catégorie de surveillance a un schéma qui définit la façon dont les événements de cette catégorie sont structurés.

### <a name="device-identity-operations"></a>Opérations d’identité de périphérique

La catégorie opérations identité effectue le suivi des erreurs qui se produisent lorsque vous tentez de créer, mettre à jour ou supprimer une entrée dans le Registre d’identité de votre concentrateur IoT. Le suivi de cette catégorie est utile pour les scénarios de mise en service.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Télémesure pour dispositif

La catégorie de télémétrie périphérique effectue le suivi des erreurs qui se produisent au niveau du concentrateur IoT et associés au pipeline de télémétrie. Cette catégorie comprend les erreurs qui se produisent lors de l’envoi des événements de télémesure (par exemple, la limitation) et de recevoir des événements de télémesure (par exemple, lecteur non autorisé). Notez que cette catégorie ne peut pas intercepter les erreurs provoquées par le code en cours d’exécution sur le périphérique lui-même.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Commandes de nuage-appareil

La catégorie de commandes de nuage-DISPOSITIF assure le suivi des erreurs qui se produisent au niveau du concentrateur IoT et concernent le pipeline de commande du dispositif. Cette catégorie comprend les erreurs qui se produisent lors de l’envoi de commandes (par exemple, les expéditeurs non autorisés), la réception des commandes (telles que le dépassement du nombre de livraison) et recevoir des commentaires de commande (telles que les commentaires expiré). Cette catégorie n’intercepte pas d’erreurs à partir d’un périphérique qui gère incorrectement une commande si la commande a été correctement remise.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Connexions

La catégorie connexions effectue le suivi des erreurs qui se produisent lorsque les périphériques se connectent ou déconnecter un concentrateur IoT. Le suivi de cette catégorie est utile pour identifier les tentatives de connexion non autorisée et de suivi lorsqu’une connexion est perdue pour les périphériques dans les zones de connectivité médiocre.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Téléchargements de fichiers

La catégorie de téléchargement de fichier assure le suivi des erreurs qui se produisent au niveau du concentrateur IoT et sont liés à la fonctionnalité de téléchargement de fichier. Cette catégorie comprend les erreurs qui se produisent avec le SAS URI (par exemple lorsqu’il arrive à expiration avant un périphérique notifie le cœur d’un téléchargement terminé), les téléchargements ayant échouées signalés par le périphérique et qu’un fichier ne se trouve pas dans le stockage lors de la création de message de notification IoT concentrateur. Notez que cette catégorie ne peut pas intercepter les erreurs survenant directement tandis que le périphérique est téléchargement d’un fichier de stockage.

    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## <a name="next-steps"></a>Étapes suivantes

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md