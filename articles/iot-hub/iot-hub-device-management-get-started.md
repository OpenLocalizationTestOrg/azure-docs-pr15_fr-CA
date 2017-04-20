<properties
 pageTitle="Mise en route de la gestion des périphériques | Microsoft Azure"
 description="Ce didacticiel vous montre comment se familiariser avec la gestion des périphériques sur Azure IoT concentrateur"
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

# <a name="tutorial-get-started-with-device-management-preview"></a>Didacticiel : Mise en route de la gestion des périphériques (aperçu)

## <a name="introduction"></a>Introduction
Applications en nuage IoT permet primitives dans Azure IoT, concentrateur, à savoir le double du périphérique et les méthodes directes, démarrer à distance et de surveiller des actions de gestion de périphérique sur les périphériques.  Cet article fournit les directives et le code de fonctionnement d’une application en nuage IoT et dispositif pour lancer et surveiller un redémarrage du périphérique distant à l’aide de IoT concentrateur.

Pour démarrer et surveiller les actions de gestion de dispositif sur vos périphériques à partir d’une application en nuage, back-end à distance, utilisez des primitives de Azure IoT concentrateur comme le [double du périphérique] [ lnk-devtwin] et [leurs procédés de nuage-dispositif (C2D)][lnk-c2dmethod]. Ce didacticiel vous montre comment une application back-end et un périphérique peuvent travailler ensemble pour vous permettre de lancez et surveillez le redémarrage du périphérique distant IoT concentrateur.

Une méthode C2D vous permet de lancer des actions de gestion de périphérique (par exemple, redémarrage, usine par défaut et mise à jour du microprogramme) à partir d’une application back-end dans le nuage. Le périphérique est responsable de :

- Gestion de la requête de méthode envoyée IoT concentrateur.
- Lancement de l’action spécifique de périphérique correspondant sur le périphérique.
- Mise à jour d’état via la double périphérique signalé propriétés IoT concentrateur.

Vous pouvez utiliser une application back-end dans le nuage pour exécuter des requêtes de double de périphérique pour le rapport sur l’avancement de vos actions de gestion de périphérique.

Ce didacticiel vous montre comment procéder pour :

- Le portail Azure permet de créer un concentrateur IoT et créer une identité de périphérique dans votre concentrateur IoT.
- Créer un périphérique simulé qui possède une méthode directe qui permet le redémarrage qui peut être appelée par le nuage.
- Créez une application console qui appelle la méthode directe de redémarrage sur le périphérique simulé via votre concentrateur IoT.

À la fin de ce didacticiel, vous avez deux applications de console Node.js :

**dmpatterns_getstarted_device.js**, qui se connecte à votre concentrateur IoT avec l’identité du périphérique créée précédemment, reçoit une méthode directe de redémarrage, simule un redémarrage physique et indique l’heure pour le dernier redémarrage.

**dmpatterns_getstarted_service.js**, qui appelle une méthode directement sur le périphérique simulé et affiche la réponse affiche la double mise à jour de périphérique a signalé des propriétés.

Pour terminer ce didacticiel, vous devez les éléments suivants :

Node.js version 0.12.x ou version ultérieure, <br/>  [Préparation de votre environnement de développement] [ lnk-dev-setup] décrit comment installer Node.js pour ce didacticiel sous Windows ou Linux.

Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui répond à une méthode directe, appelée par le cloud, ce qui déclenche un redémarrage du périphérique simulé et utilise la double périphérique signalé propriétés pour activer les requêtes de double DISPOSITIF identifier les périphériques et lorsque leur dernier redémarrage.

1. Créez un dossier vide appelé **manageddevice**.  Dans le dossier **manageddevice** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande.  Acceptez les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **manageddevice** , exécutez la commande suivante pour installer le **azure-iot-device@dtpreview** package SDK de périphérique et **azure-iot-device-mqtt@dtpreview** package :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **dmpatterns_getstarted_device.js** dans le dossier **manageddevice** .

4. Ajouter comme 'nécessite' instructions au début du fichier **dmpatterns_getstarted_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Ajoutez une variable **connectionString** et l’utiliser pour créer un client de périphérique.  Remplacez la chaîne de connexion de votre chaîne de connexion de périphérique.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Ajoutez la fonction suivante pour implémenter la méthode directe sur le périphérique

    ```
    var onReboot = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
        
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Ouverture de la connexion à votre concentrateur IoT et démarrez le récepteur de la méthode directe :

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
    
8. Enregistrez et fermez le fichier **dmpatterns_getstarted_device.js** .

 [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes][lnk-transient-faults].

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Déclencher un redémarrage à distance sur le périphérique à l’aide d’une méthode directe 

Dans cette section, vous créez une application de console Node.js qui lance un redémarrage à distance sur un périphérique à l’aide d’une méthode directe et utilise des requêtes de double de périphérique pour rechercher la dernière heure de redémarrage pour ce périphérique.

1. Créez un dossier vide appelé **triggerrebootondevice**.  Dans le dossier **triggerrebootondevice** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande.  Acceptez les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **triggerrebootondevice** , exécutez la commande suivante pour installer le **azure-iothub@dtpreview** package SDK de périphérique et **azure-iot-device-mqtt@dtpreview** package :

    ```
    npm install azure-iothub@dtpreview --save
    ```
    
3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **dmpatterns_getstarted_service.js** dans le dossier **triggerrebootondevice** .

4. Ajouter comme 'nécessite' instructions au début du fichier **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Ajoutez les déclarations de variable suivantes et remplacer les valeurs d’espace réservé :

    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
    
6. Ajoutez la fonction suivante pour appeler la méthode de périphérique pour le périphérique cible de redémarrer :

    ```
    var startRebootDevice = function(twin) {

        var methodName = "reboot";
        
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
        
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Ajoutez la fonction suivante pour demander le périphérique et obtenir l’heure du dernier redémarrage :

    ```
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
    
8. Ajoutez le code suivant pour appeler les fonctions qui déclencheront la méthode directe de redémarrage et de requête pour la dernière fois de redémarrage :

    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
    
9. Enregistrez et fermez le fichier **dmpatterns_getstarted_service.js** .

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commande dans le dossier **manageddevice** , exécutez la commande suivante pour commencer à écouter pour la méthode directe de redémarrage.

    ```
    node dmpatterns_getstarted_device.js
    ```

2. À l’invite de commande dans le dossier **triggerrebootondevice** , exécutez la commande suivante pour déclencher le redémarrage à distance et la requête pour le double de périphérique rechercher le dernier temps de redémarrage.

    ```
    node dmpatterns_getstarted_service.js
    ```

3. Vous verrez la réaction à la méthode directe en imprimant le message

## <a name="customize-and-extend-the-device-management-actions"></a>Personnaliser et étendre des actions de gestion du périphérique

Vos solutions IoT peuvent développer le jeu défini des modèles de gestion de périphérique ou activer des modèles personnalisés à l’aide de la double de périphérique et les primitives de méthode C2D. Autres actions de gestion de périphérique exemples factory reset, mise à jour du firmware, mise à jour logicielle, gestion de l’alimentation, gestion de réseau et de connectivité et cryptage des données.

## <a name="device-maintenance-windows"></a>Fenêtres de gestion de périphérique

En règle générale, vous configurez des périphériques pour effectuer des actions à la fois qui minimise les interruptions et les périodes d’inactivité.  Fenêtres de gestion de périphérique sont un modèle fréquemment utilisé pour définir l’heure quand un périphérique doit mettre à jour sa configuration. Les solutions de votre back-end peuvent utiliser les propriétés désirées de la double dispositif pour définir et activer une stratégie sur votre périphérique qui permet à une fenêtre de maintenance. Lorsqu’un périphérique reçoit la stratégie de fenêtre de maintenance, il pouvez utiliser la propriété signalée de la double dispositif pour présenter l’état de la stratégie. L’application back-end pouvez ensuite utiliser des requêtes de double dispositif d’attester la conformité des dispositifs et de chaque stratégie.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher un arrêt à distance sur un périphérique, utilisé le double du périphérique a signalé des propriétés pour indiquer l’heure de la dernière réinitialisation du périphérique et interrogé pour la double DISPOSITIF découvrir la dernière réinitialisation du périphérique à partir du cloud.

Pour continuer la mise en route de IoT Hub et modèles de gestion de périphérique comme à distance via la mise à jour du firmware air, voir :

[Didacticiel : Comment faire une mise à jour du firmware][lnk-fwupdate]

Pour savoir comment étendre votre IoT appelle de méthode de planification et de la solution sur plusieurs périphériques, consultez [calendrier et les tâches de diffusion] [ lnk-tutorial-jobs] didacticiel.

Pour continuer la mise en route avec IoT concentrateur, consultez [mise en route avec le Kit de développement de passerelle IoT][lnk-gateway-SDK].


<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx