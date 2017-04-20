<properties
    pageTitle="Concentrateur de IoT Azure pour Node.js mise en route | Microsoft Azure"
    description="Azure concentrateur IoT Node.js route démarré le didacticiel. Utilisez Azure IoT Hub et Node.js avec les kits de développement logiciel de Microsoft Azure IoT d’implémenter une solution de l’Internet des objets."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-nodejs"></a>Mise en route avec Azure IoT Hub pour Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous avez trois applications de console Node.js :

* **CreateDeviceIdentity.js**, qui crée une identité de périphérique et d’une clé de sécurité associées pour connecter votre périphérique simulé.
* **ReadDeviceToCloudMessages.js**, qui affiche la télémétrie envoyé par votre périphérique simulé.
* **SimulatedDevice.js**, qui se connecte à votre concentrateur IoT avec l’identité du périphérique créée précédemment et envoie un message de télémétrie chaque seconde à l’aide du protocole AMQP.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les kits de développement différents que vous pouvez utiliser pour générer les deux applications de s’exécuter sur les périphériques et le back-end votre solution.

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Node.js version 0.10.x ou une version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Vous venez de créer votre concentrateur IoT. Vous avez le nom d’hôte IoT concentrateur et la chaîne de connexion IoT concentrateur dont vous avez besoin pour compléter le reste de ce didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité de périphérique

Dans cette section, vous créez une application console Node.js crée une identité de périphérique dans le Registre de l’identité de votre concentrateur IoT. Un périphérique ne peut pas se connecter IoT concentrateur, à moins qu’une entrée dans le Registre d’identité de périphérique. Reportez-vous à la section du **Registre d’identité de périphérique** du [Guide de développeur de concentrateur IoT] [ lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, il génère un ID de périphérique unique et la clé de votre périphérique peut utiliser pour s’identifier lorsqu’il envoie des messages de périphérique-nuage à IoT concentrateur.

1. Créez un dossier vide appelé **createdeviceidentity**. Dans le dossier **createdeviceidentity** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande. Acceptez les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **createdeviceidentity** , exécutez la commande suivante pour installer le package de Service SDK **azure-iothub** :

    ```
    npm install azure-iothub --save
    ```

3. À l’aide d’un éditeur de texte, de créer un fichier **CreateDeviceIdentity.js** dans le dossier **createdeviceidentity** .

4. Ajoutez le code suivant `require` instruction au début du fichier **CreateDeviceIdentity.js** :

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Ajoutez le code suivant dans le fichier **CreateDeviceIdentity.js** et remplacez la valeur de l’espace réservé par la chaîne de connexion pour le moyeu IoT que vous avez créé dans la section précédente : 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Ajoutez le code suivant pour créer une définition de périphérique dans le Registre d’identité de périphérique dans votre concentrateur IoT. Ce code crée un périphérique si l’id de périphérique n’existe pas dans le Registre, sinon il retourne la clé du dispositif existant :

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Enregistrez et fermez le fichier de **CreateDeviceIdentity.js** .

8. Pour exécuter l’application **createdeviceidentity** , exécutez la commande suivante à l’invite de commandes dans le dossier createdeviceidentity :

    ```
    node CreateDeviceIdentity.js 
    ```

9. Prenez note de **l’id de périphérique** et de la **clé de périphérique**. Vous avez besoin de ces valeurs lorsque vous créez une application qui se connecte à un concentrateur IoT en tant que périphérique.

> [AZURE.NOTE] Le Registre d’identité IoT concentrateur ne stocke que les identités des appareils pour permettre un accès sécurisé au concentrateur. Il stocke les ID de périphérique et les clés à utiliser comme informations d’identification de sécurité et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un périphérique. Si votre application a besoin stocker d’autres métadonnées spécifiques au périphérique, il doit utiliser une banque spécifique à l’application. Reportez-vous au [Guide de développeur de concentrateur IoT] [ lnk-devguide-identity] pour plus d’informations.

## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages de périphérique-nuage

Dans cette section, vous créez une application console Node.js qui lit les messages de périphérique-nuage IoT concentrateur. Un concentrateur IoT expose un [Événement concentrateurs][lnk-event-hubs-overview]-le point de terminaison compatible pour vous permettre de lire des messages de périphérique-nuage. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui n’est pas approprié pour un déploiement à un débit élevé. Le [traitement des messages de périphérique-nuage] [ lnk-process-d2c-tutorial] didacticiel vous montre comment traiter les messages de périphérique-nuage à grande échelle. La [Mise en route avec les concentrateurs d’événement] [ lnk-eventhubs-tutorial] didacticiel fournit des informations plus sur la manière de traiter les messages de concentrateurs d’événement et n’est applicable aux points de terminaison IoT concentrateur événement compatible avec le concentrateur.

> [AZURE.NOTE] Le point de terminaison compatible avec concentrateur événement pour lire des messages de périphérique-nuage toujours utilise le protocole AMQP.

1. Créez un dossier vide appelé **readdevicetocloudmessages**. Dans le dossier **readdevicetocloudmessages** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande. Acceptez les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **readdevicetocloudmessages** , exécutez la commande suivante pour installer le package **azure-événement-hubs** :

    ```
    npm install azure-event-hubs --save
    ```

3. À l’aide d’un éditeur de texte, de créer un fichier **ReadDeviceToCloudMessages.js** dans le dossier **readdevicetocloudmessages** .

4. Ajoutez le code suivant `require` instructions au début du fichier **ReadDeviceToCloudMessages.js** :

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Ajoutez la déclaration de variable suivante et remplacez la valeur de l’espace réservé par la chaîne de connexion pour votre concentrateur IoT :

    ```
    var connectionString = '{iothub connection string}';
    ```

6. Ajoutez les deux fonctions suivantes qui s’impriment sur la console :

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Ajoutez le code suivant pour créer **EventHubClient**, ouvrez la connexion à votre concentrateur IoT et créer un récepteur pour chaque partition. Cette application utilise un filtre lorsqu’il crée un récepteur afin que le récepteur ne lit que les messages envoyés au Hub de IoT après le démarrage du récepteur. Ce filtre est utile dans un environnement de test afin de voir l’ensemble des messages en cours. Dans un environnement de production, votre code doit Assurez-vous qu’elle traite tous les messages - voir [comment traiter les messages de périphérique-nuage IoT concentrateur] [ lnk-process-d2c-tutorial] didacticiel pour plus d’informations :

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Enregistrez et fermez le fichier **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui simule un périphérique qui envoie des messages de périphérique-nuage à un concentrateur IoT.

1. Créez un dossier vide appelé **simulateddevice**. Dans le dossier **simulateddevice** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande. Acceptez les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **simulateddevice** , exécutez la commande suivante pour installer le package du périphérique SDK **azure-iot-périphérique** et **azure-iot-périphérique-amqp** :

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **SimulatedDevice.js** dans le dossier **simulateddevice** .

4. Ajoutez le code suivant `require` instructions au début du fichier **SimulatedDevice.js** :

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Ajoutez une variable **connectionString** et l’utiliser pour créer un client de périphérique. Remplacez **{youriothostname}** avec le nom du concentrateur IoT vous avez créé à la section *créer un concentrateur IoT* . Remplacez **{yourdevicekey}** par la valeur de clé de périphérique générées dans la section *Création d’une identité de périphérique* :

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Ajoutez la fonction suivante pour afficher la sortie à partir de l’application :

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Créer un rappel et utiliser la fonction **setInterval** pour envoyer un message à votre concentrateur IoT toutes les secondes :

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

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

8. Ouverture de la connexion à votre concentrateur IoT et commencer l’envoi des messages :

    ```
    client.open(connectCallback);
    ```

9. Enregistrez et fermez le fichier **SimulatedDevice.js** .

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes][lnk-transient-faults].


## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À une invite de commande dans le dossier **readdevicetocloudmessages** , exécutez la commande suivante pour commencer l’analyse de votre concentrateur IoT :

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![Node.js IoT concentrateur client application de service de contrôler les messages de périphérique-nuage][7]

2. À une invite de commande dans le dossier **simulateddevice** , exécutez la commande suivante pour commencer l’envoi des données de télémétrie pour votre concentrateur IoT :

    ```
    node SimulatedDevice.js
    ```

    ![Application de client smart device Node.js IoT Hub pour envoyer des messages de périphérique-nuage][8]

3. La mosaïque de **l’utilisation** du [portail Azure] [ lnk-portal] indique le nombre de messages envoyés au hub :

    ![Azure portail utilisation mosaïque montrant le nombre de messages envoyés au Hub de IoT][43]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous configuré un nouveau concentrateur IoT dans Azure portal et ensuite créé une identité de périphérique dans le Registre d’identité du concentrateur. Cette identité de périphérique vous permet d’activer l’application de périphérique simulé envoyer des messages de périphérique-nuage au concentrateur. Vous avez également créé une application qui affiche les messages reçus par le concentrateur. 

Pour continuer la mise en route avec IoT Hub et Explorer d’autres scénarios IoT, voir :

- [Connectez votre périphérique][lnk-connect-device]
- [Mise en route de la gestion des périphériques][lnk-device-management]
- [Mise en route avec le Kit de développement de passerelle IoT][lnk-gateway-SDK]

Pour savoir comment étendre votre solution IoT et de traiter des messages de périphérique-nuage à grande échelle, consultez [traitement des messages de périphérique-nuage] [ lnk-process-d2c-tutorial] didacticiel.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
