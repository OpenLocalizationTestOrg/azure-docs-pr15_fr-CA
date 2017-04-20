<properties
 pageTitle="Utilisez les méthodes directes | Microsoft Azure"
 description="Ce didacticiel vous montre comment utiliser les méthodes directes"
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
 ms.date="10/05/2016"
 ms.author="nberdy"/>

# <a name="tutorial-use-direct-methods"></a>Didacticiel : Utiliser les méthodes directes

## <a name="introduction"></a>Introduction

Concentrateur de IoT Azure est un service entièrement géré qui permet fiable et sécurisée des communications bidirectionnelles entre millions d’appareils de IoT et une application back-end. Des didacticiels précédents ([mise en route de IoT Hub] et [Envoyer des messages de nuage-dispositif avec concentrateur de IoT]) illustrent la fonctionnalité de messagerie périphérique-nuage et nuage-DISPOSITIF base de IoT concentrateur. IoT Hub vous donne également la possibilité d’appeler des méthodes non durables sur les périphériques à partir du cloud. Les méthodes représentent une interaction de demande-réponse avec un dispositif similaire à un appel HTTP dans la mesure où ils réussissent ou échouent immédiatement (après un délai d’attente spécifié par l’utilisateur) permettre à l’utilisateur de connaître l’état de l’appel. [Appeler une méthode directe sur un périphérique] [ lnk-devguide-methods] décrit les méthodes de façon plus détaillée et fournit des indications concernant l’utilisation de méthodes par rapport aux messages du nuage vers le périphérique.

Ce didacticiel vous montre comment procéder pour :

- Le portail Azure permet de créer un concentrateur IoT et créer une identité de périphérique dans votre concentrateur IoT.
- Créer un périphérique simulé qui possède une méthode directe qui peut être appelée par le nuage.
- Créer une application console qui appelle une méthode directement sur le périphérique simulé via votre concentrateur IoT.

> [AZURE.NOTE] À ce stade, les méthodes directes sont accessibles uniquement à partir de périphériques qui se connectent au concentrateur IoT utilisant le protocole MQTT. Veuillez vous reporter à la [prise en charge de la MQTT] [ lnk-devguide-mqtt] l’article pour obtenir des instructions sur la conversion d’applications de périphérique existant à utiliser MQTT.

À la fin de ce didacticiel, vous avez deux applications de console Node.js :

* **CallMethodOnDevice.js**, qui appelle une méthode sur le périphérique simulé et affiche la réponse.
* **SimulatedDevice.js**, qui se connecte à votre concentrateur IoT avec l’identité du périphérique créée précédemment et répond à la méthode appelée par le nuage.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les kits de développement différents que vous pouvez utiliser pour générer les deux applications de s’exécuter sur les périphériques et le back-end votre solution.

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Node.js version 0.10.x ou une version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui répond à une méthode appelée par le nuage.

1. Créez un dossier vide appelé **simulateddevice**. Dans le dossier **simulateddevice** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande. Acceptez les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **simulateddevice** , exécutez la commande suivante pour installer le package du périphérique SDK **azure-iot-périphérique** et **azure-iot-périphérique-mqtt** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **SimulatedDevice.js** dans le dossier **simulateddevice** .

4. Ajoutez le code suivant `require` instructions au début du fichier **SimulatedDevice.js** :

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. Ajoutez une variable **connectionString** et l’utiliser pour créer un client de périphérique. Remplacez **{chaîne de connexion de périphérique}** par la chaîne de connexion que vous avez généré dans la section *Création d’une identité de périphérique* :

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. Ajoutez la fonction suivante pour implémenter la méthode sur le périphérique :

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. Ouvrir la connexion à votre IoT hub et le démarrer initialiser l’écouteur de méthode :

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. Enregistrez et fermez le fichier **SimulatedDevice.js** .

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, la tentative de connexion), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes][lnk-transient-faults].

## <a name="call-a-method-on-a-device"></a>Appeler une méthode sur un périphérique

Dans cette section, vous créez une application de console Node.js qui appelle une méthode sur le périphérique simulé et affiche la réponse.

1. Créez un dossier vide appelé **callmethodondevice**. Dans le dossier **callmethodondevice** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande. Acceptez les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **callmethodondevice** , exécutez la commande suivante pour installer le package **azure-iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, de créer un fichier **CallMethodOnDevice.js** dans le dossier **callmethodondevice** .

4. Ajoutez le code suivant `require` instructions au début du fichier **CallMethodOnDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Ajoutez la déclaration de variable suivante et remplacez la valeur de l’espace réservé par la chaîne de connexion pour votre concentrateur IoT :

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. Créer le client pour ouvrir la connexion à votre concentrateur IoT.

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. Ajoutez la fonction suivante pour appeler la méthode de périphérique et d’imprimer la réponse du périphérique de la console :

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. Enregistrez et fermez le fichier **CallMethodOnDevice.js** .

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À une invite de commande dans le dossier **simulateddevice** , exécutez la commande suivante pour démarrer l’écoute des appels de méthode à partir de votre concentrateur IoT :

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. À une invite de commande dans le dossier **callmethodondevice** , exécutez la commande suivante pour commencer l’analyse de votre concentrateur IoT :

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. Vous verrez le périphérique réagir à la méthode en imprimant le message et l’application appelée l’affichage méthode la réponse du périphérique :

    ![][9]
    
## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous configuré un nouveau concentrateur IoT dans Azure portal et ensuite créé une identité de périphérique dans le Registre d’identité du concentrateur. Cette identité de périphérique vous permet d’activer l’application de périphérique simulé réagir aux méthodes appelées par le nuage. Vous avez également créé une application qui appelle des méthodes sur le périphérique et affiche la réponse du périphérique. 

Pour continuer la mise en route avec IoT Hub et Explorer d’autres scénarios IoT, voir :

- [Mise en route de concentrateur de IoT]
- [Planifier des tâches sur plusieurs périphériques][lnk-devguide-jobs]

Pour savoir comment étendre votre IoT appelle de méthode de planification et de la solution sur plusieurs périphériques, consultez [calendrier et les tâches de diffusion] [ lnk-tutorial-jobs] didacticiel.

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Envoyer des messages de nuage-dispositif avec IoT concentrateur]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Mise en route de concentrateur de IoT]: iot-hub-node-node-getstarted.md
