<properties
    pageTitle="Envoyer des messages de nuage-dispositif avec IoT Hub | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à envoyer des messages de nuage vers le périphérique à l’aide d’Azure IoT Hub avec Java."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-nodejs"></a>Didacticiel : Comment envoyer des messages de nuage-dispositif avec IoT Hub et Node.js

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduction

Concentrateur de IoT Azure est un service entièrement géré qui vous aide à activer fiables et sécurisées des communications bidirectionnelles entre millions d’appareils de IoT et mettre fin à une demande de retour. Le didacticiel [mise en route de concentrateur de IoT] montre comment créer un concentrateur IoT, configurer une identité de périphérique qu’il contient et un périphérique simulé qui envoie des messages de périphérique-nuage de code.

Ce didacticiel s’appuie sur la [mise en route de IoT concentrateur]. Il vous montre comment procéder pour :

- À partir de votre application cloud back-end, envoyer des messages de nuage-appareil à un seul périphérique par IoT concentrateur.
- Recevoir des messages du nuage vers le périphérique sur un périphérique.
- À partir de votre nuage application back-end, demander un accusé de réception (*commentaires*) pour les messages envoyés à un périphérique IoT concentrateur.

Vous trouverez plus d’informations sur les messages du nuage vers le périphérique dans le [Guide de développeur de concentrateur IoT][IoT Hub Developer Guide - C2D].

À la fin de ce didacticiel, vous exécutez deux applications de console Node.js :

* **SimulatedDevice**, une version modifiée de l’application créée dans [mise en route de IoT concentrateur], qui se connecte à votre concentrateur IoT et reçoit les messages du nuage vers le périphérique.
* **SendCloudToDeviceMessage**, qui envoie un message de nuage vers le périphérique à périphérique simulé par IoT concentrateur, puis reçoit son accusé de réception.

> [AZURE.NOTE] IoT concentrateur a la prise en charge du Kit de développement logiciel pour de nombreuses plates-formes de périphérique et les langues (y compris C, Java et Javascript) par le biais de kits SDK de périphériques Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre périphérique pour les code de ce didacticiel et généralement Azure IoT Hub, consultez le [Centre pour développeurs IoT Azure].

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Node.js version 0.10.x ou une version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

## <a name="receive-messages-on-the-simulated-device"></a>Recevoir des messages sur le périphérique simulé

Dans cette section, vous modifiez l’application simulée périphérique créé à la [mise en route de IoT concentrateur] pour recevoir des messages de nuage vers le périphérique du concentrateur IoT.

1. À l’aide d’un éditeur de texte, ouvrez le fichier SimulatedDevice.js.

2. Modifiez la fonction **connectCallback** pour gérer les messages envoyés à partir de concentrateur de IoT. Dans cet exemple, le périphérique appelle toujours la fonction **complète** pour avertir IoT Hub qu’il a traité le message. La nouvelle version de la fonction **connectCallback** ressemble à ceci :

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] Si vous utilisez HTTP au lieu de MQTT ou AMQP comme moyen de transport, l’instance **DeviceClient** vérifie les messages à partir de concentrateur IoT rarement (moins de 25 minutes). Pour plus d’informations sur les différences entre la prise en charge MQTT, AMQP et HTTP et IoT Hub la limitation, voir le [Guide de développeur de concentrateur IoT][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Envoyer un message de nuage-appareil

Dans cette section, vous créez une application console Node.js qui envoie des messages de nuage-dispositif pour l’application du périphérique simulé. Vous avez besoin de l’Id du périphérique que vous avez ajouté dans le didacticiel [mise en route de IoT Hub] du périphérique. Vous devez également la chaîne de connexion pour votre concentrateur IoT que vous trouverez dans le [portail Azure].

1. Créez un dossier vide appelé **sendcloudtodevicemessage**. Dans le dossier **sendcloudtodevicemessage** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande. Acceptez les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **sendcloudtodevicemessage** , exécutez la commande suivante pour installer le package **azure-iothub** :

    ```
    npm install azure-iothub --save
    ```

3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **SendCloudToDeviceMessage.js** dans le dossier **sendcloudtodevicemessage** .

4. Ajoutez le code suivant `require` instructions au début du fichier **SendCloudToDeviceMessage.js** :

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Ajoutez le code suivant au fichier de **SendCloudToDeviceMessage.js** . Remplacez la valeur espaces réservés de chaîne de connexion avec la chaîne de connexion pour le moyeu IoT que vous avez créé dans le didacticiel [mise en route de IoT concentrateur] . Remplacez l’espace réservé du périphérique cible avec l’id de périphérique du périphérique vous ajouté dans le didacticiel [mise en route de IoT Hub] :

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Ajoutez la fonction suivante pour imprimer les résultats de l’opération sur la console :

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Ajoutez la fonction suivante pour imprimer les messages de feedback de livraison à la console :

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Ajoutez le code suivant pour envoyer un message à votre appareil et de traiter le message de feedback lorsque le périphérique reconnaît le message du nuage vers le périphérique :

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. Enregistrez et fermez le fichier de **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commande dans le dossier **simulateddevice** , exécutez la commande suivante pour envoyer télémétrie à IoT Hub et pour écouter les messages du nuage vers le périphérique :

    ```
    node SimulatedDevice.js 
    ```

    ![Exécutez l’application de périphérique simulé][img-simulated-device]

2. À une invite de commande dans le dossier **sendcloudtodevicemessage** , exécutez la commande suivante pour envoyer un message de nuage-dispositif et attendez les commentaires de l’accusé de réception :

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![Exécutez l’application pour envoyer la commande c2d][img-send-command]

    > [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (comme intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes].

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment envoyer et recevoir des messages du nuage vers le périphérique. 

Pour voir des exemples de solutions de bout en bout complètes qui utilisent IoT Hub, consultez [Azure IoT Suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT concentrateur].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Mise en route de concentrateur de IoT]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guide du développeur IoT concentrateur]: iot-hub-devguide.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure portal]: https://portal.azure.com
[IoT Azure Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/