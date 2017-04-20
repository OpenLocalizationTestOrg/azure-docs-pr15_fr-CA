<properties
 pageTitle="Comment faire pour planifier des tâches | Microsoft Azure"
 description="Ce didacticiel vous montre comment faire pour planifier des tâches"
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

# <a name="tutorial-schedule-and-broadcast-jobs-preview"></a>Didacticiel : Planification et diffusion des travaux (aperçu)

## <a name="introduction"></a>Introduction
Concentrateur de IoT Azure est un service entièrement géré qui permet à une application principale créer et suivre des tâches de planifier et de mettre à jour des millions de périphériques.  Travaux peuvent être utilisés pour les actions suivantes :

- Mettre à jour les propriétés du périphérique double souhaité
- Mettre à jour des balises de double DISPOSITIF
- Appeler des méthodes de nuage-appareil

Du point de vue conceptuel, un travail encapsule l’un de ces actions et effectue le suivi de la progression de l’exécution par rapport à un ensemble de périphériques, qui est défini par une requête de double.  Par exemple, à l’aide d’un travail d’une application principale peut appeler une méthode de redémarrage sur 10 000 périphériques, spécifié par une requête double et planifiée à une date ultérieure.  Cette application peut ensuite suivre la progression que chacun de ces périphériques de réception et exécuter la méthode de redémarrage.

Plus d’informations sur chacune de ces fonctionnalités dans ces articles :

- Double du périphérique et propriétés : [mise en route avec double] [ lnk-get-started-twin] et [didacticiel : comment utiliser les propriétés double][lnk-twin-props]
- Méthodes de nuage-dispositif : [guide de développeur - méthodes directes] [ lnk-dev-methods] et [didacticiel : méthodes de C2D][lnk-c2d-methods]

Ce didacticiel vous montre comment procéder pour :

- Créer un périphérique simulé qui possède une méthode directe permettant de lockDoor qui peut être appelée par l’application back end.
- Créer une application console qui appelle la méthode directe de lockDoor sur le périphérique simulé à l’aide d’une tâche et met à jour les propriétés double souhaité à l’aide d’un projet de périphérique.

À la fin de ce didacticiel, vous avez deux applications de console Node.js :

**simDevice.js**, qui se connecte à votre concentrateur IoT avec l’identité du périphérique et reçoit une méthode directe de lockDoor.

**scheduleJobService.js**, qui appelle une méthode directement sur le périphérique simulé et mettre à jour les propriétés de disired de la double à l’aide d’une tâche.

Pour terminer ce didacticiel, vous devez les éléments suivants :

Node.js version 0.12.x ou version ultérieure, <br/>  [Préparation de votre environnement de développement] [ lnk-dev-setup] décrit comment installer Node.js pour ce didacticiel sous Windows ou Linux.

Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui répond à une méthode directe, appelée par le cloud, ce qui déclenche un redémarrage du périphérique simulé et utilise la double périphérique signalé propriétés pour activer les requêtes de double DISPOSITIF identifier les périphériques et lorsque leur dernier redémarrage.

1. Créez un dossier vide appelé **simDevice**.  Dans le dossier **simDevice** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande.  Acceptez les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **simDevice** , exécutez la commande suivante pour installer le package du périphérique SDK **azure-iot-périphérique** et **azure-iot-périphérique-mqtt** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **simDevice.js** dans le dossier **simDevice** .

4. Ajouter comme 'nécessite' instructions au début du fichier **simDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Ajoutez une variable **connectionString** et l’utiliser pour créer un client de périphérique.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Ajoutez la fonction suivante pour gérer la méthode lockDoor.

    ```
    var onLockDoor = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        console.log('Locking Door!');
    };
    ```

7. Ajoutez le code suivant pour inscrire le gestionnaire pour la méthode lockDoor.

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for reboot direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
    
8. Enregistrez et fermez le fichier **simDevice.js** .

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes][lnk-transient-faults].

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-twins-properties"></a>Planifier des tâches pour l’appel d’une méthode directe et mettre à jour les propriétés d’un double

Dans cette section, vous créez une application console Node.js qui initie une lockDoor à distance sur un périphérique à l’aide d’une méthode directe et mettre à jour les propriétés de la double.

1. Créez un dossier vide appelé **scheduleJobService**.  Dans le dossier **scheduleJobService** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande.  Acceptez les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **scheduleJobService** , exécutez la commande suivante pour installer le package du Kit de développement de périphérique **azure-iothub** et **azure-iot-périphérique-mqtt** :

    ```
    npm install azure-iothub@dtpreview uuid --save
    ```
    
3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **scheduleJobService.js** dans le dossier **scheduleJobService** .

4. Ajouter comme 'nécessite' instructions au début du fichier **dmpatterns_gscheduleJobServiceetstarted_service.js** :

    ```
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Ajoutez les déclarations de variable suivantes et remplacer les valeurs d’espace réservé :

    ```
    var connectionString = '{iothubconnectionstring}';
    var deviceArray = ['myDeviceId'];
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  3600;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
    
6. Ajoutez la fonction suivante qui sera utilisée pour contrôler l’exécution de la tâche :

    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Ajoutez le code suivant pour planifier la tâche qui appelle la méthode de périphérique :

    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        timeoutInSeconds: 45
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                deviceArray,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
        
8. Ajoutez le code suivant pour planifier la tâche de mise à jour de la double :

    ```
    var twinPatch = {
        etag: '*',
        desired: {
            building: '43',
            floor: 3
        }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                deviceArray,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
    
9. Enregistrez et fermez le fichier **scheduleJobService.js** .

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commande dans le dossier **simDevice** , exécutez la commande suivante pour commencer à écouter pour la méthode directe de redémarrage.

    ```
    node simDevice.js
    ```

2. À l’invite de commande dans le dossier **scheduleJobService** , exécutez la commande suivante pour déclencher le redémarrage à distance et la requête pour le double de périphérique rechercher le dernier temps de redémarrage.

    ```
    node scheduleJobService.js
    ```

3. Vous verrez la sortie de périphérique et les applications back-end.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé un travail planifier une méthode directe pour un périphérique et de la mise à jour des propriétés de la double périphérique.

Pour continuer la mise en route de IoT Hub et modèles de gestion de périphérique comme à distance via la mise à jour du firmware air, voir :

[Didacticiel : Comment faire une mise à jour du firmware][lnk-fwupdate]

Pour continuer la mise en route avec IoT concentrateur, consultez [mise en route avec le Kit de développement de passerelle IoT][lnk-gateway-SDK].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx