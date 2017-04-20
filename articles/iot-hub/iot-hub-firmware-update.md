<properties
 pageTitle="Comment effectuer une mise à jour du firmware | Microsoft Azure"
 description="Ce didacticiel vous montre comment effectuer une mise à jour du firmware"
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

# <a name="tutorial-how-to-do-a-firmware-update-preview"></a>Didacticiel : Comment un firmware update (aperçu)

## <a name="introduction"></a>Introduction
Dans la [mise en route de la gestion des périphériques] [ lnk-dm-getstarted] didacticiel, vous avez appris à utiliser le [double de périphérique] [ lnk-devtwin] et [leurs procédés de nuage-dispositif (C2D)] [ lnk-c2dmethod] primitives de redémarrer à distance un périphérique. Ce didacticiel utilise les mêmes primitives IoT Hub et fournit des conseils et vous montre comment effectuer une mise à jour du microprogramme simulé de bout en bout.  Ce modèle est utilisé dans l’implémentation de la mise à jour du firmware pour l’échantillon de périphérique Intel Edison.

Ce didacticiel vous montre comment procéder pour :

- Créez une application console qui appelle la méthode directe de firmwareUpdate sur le périphérique simulé via votre concentrateur IoT.
- Créer un périphérique simulé qui implémente une méthode directe de firmwareUpdate qui passe par un processus en plusieurs étapes qui attend avant de télécharger l’image du microprogramme, téléchargement de l’image du firmware et applique enfin image du firmware th.  Tout au long de l’exécution de chaque étape les utilisations du périphérique le double a signalé des propriétés à mettre à jour la progression.

À la fin de ce didacticiel, vous avez deux applications de console Node.js :

**dmpatterns_fwupdate_service.js**, qui appelle une méthode directe sur le périphérique simulé, affiche la réponse et périodiquement (chaque 500 ms) affiche le double de périphérique mis à jour fait état de propriétés.

**dmpatterns_fwupdate_device.js**, qui se connecte à votre concentrateur IoT avec l’identité du périphérique créée précédemment, reçoit une méthode directe de firmwareUpdate, qu’il s’exécute dans un processus de plusieurs état pour simuler une mise à jour du firmware, y compris : en attente pour le téléchargement de l’image, le téléchargement de la nouvelle image et enfin appliquer l’image.


Pour terminer ce didacticiel, vous devez les éléments suivants :

Node.js version 0.12.x ou version ultérieure, <br/>  [Préparation de votre environnement de développement] [ lnk-dev-setup] décrit comment installer Node.js pour ce didacticiel sous Windows ou Linux.

Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

Suivez l’article [mise en route de la gestion des périphériques](iot-hub-device-management-get-started.md) pour créer votre concentrateur IoT et obtenir votre chaîne de connexion.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui répond à une méthode directe, appelée par le cloud, ce qui déclenche une mise à jour du firmware du périphérique simulé et utilise la double périphérique signalé propriétés pour activer les requêtes de double DISPOSITIF identifier les périphériques et lorsque leur dernier redémarrage.

1. Créez un dossier vide appelé **manageddevice**.  Dans le dossier **manageddevice** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande.  Acceptez les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **manageddevice** , exécutez la commande suivante pour installer le **azure-iot-device@dtpreview** package SDK de périphérique et **azure-iot-device-mqtt@dtpreview** package :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **dmpatterns_fwupdate_device.js** dans le dossier **manageddevice** .

4. Ajouter comme 'nécessite' instructions au début du fichier **dmpatterns_fwupdate_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Ajoutez une variable **connectionString** et l’utiliser pour créer un client de périphérique.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Ajoutez la fonction qui sera utilisée pour mettre à jour le double a signalé des propriétés

    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };

      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
    
7. Ajoutez les fonctions suivantes qui s’appliquent de l’image du firmware et simuler le téléchargement.

    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
      
      console.log("Downloading image from " + imageUrl);
      
      callback(error, image);
    }

    var simulateApplyImage = function(imageData, callback) {
      var error = null;
      
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
      
      callback(error);
    }
    ```

8. Ajoutez la fonction qui met à jour l’état de la mise à jour du firmware via la double signalé propriétés en attente de téléchargement.  En général, périphériques soient informés d’une mise à jour de produit et un administrateur nommé stratégie provoque du périphérique pour démarrer le téléchargement et l’application de la mise à jour.  Voici où la logique pour activer cette stratégie s’exécute.  Pour plus de simplicité, nous allons reporter pendant 4 secondes et procéder au téléchargement de l’image du microprogramme. 

    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
      
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
    
9. Ajoutez la fonction qui met à jour l’état de la mise à jour du firmware via la double a signalé des propriétés pour le téléchargement de l’image du microprogramme.  Il suit en simulant un téléchargement de microprogramme et enfin mises à jour de l’état de la mise à jour du firmware pour informer d’un téléchargement réussite ou d’échec.

    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
      
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
          
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
            
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
        
      }, 4000);
    }
    ```
    
10. Ajoutez la fonction qui met à jour l’état de la mise à jour du firmware via la double a signalé des propriétés à appliquer l’image du microprogramme.  Il suit en simulant une application de l’image du firmware et enfin mises à jour de l’état de la mise à jour du firmware pour informer appliquer réussite ou échec.

    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
      
      setTimeout(function() {
        
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
            
          }
        });
        
        setTimeout(callback, 4000);
        
      }, 4000);
    }
    ```

11. Ajouter le functoin suivant qui gère la méthode firmwareUpdate et lancer le processus de mise à jour du firmware de plusieurs étages.

    ```
    var onFirmwareUpdate = function(request, response) {
            
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });

      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;

      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
          
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });

        }
      });
    }
    ```
    
12. Enfin, ajoutez le code suivant qui se connecte à un concentrateur IoT comme un périphérique, 

    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
      
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes][lnk-transient-faults].

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Déclencher une mise à jour de microprogramme à distance sur le périphérique à l’aide d’une méthode directe 

Dans cette section, vous créez une application de console Node.js qui lance une mise à jour de microprogramme à distance sur un périphérique à l’aide d’une méthode directe et utilise des requêtes de double dispositif pour obtenir régulièrement de l’état de la mise à jour du firmware active sur ce périphérique.


1. Créez un dossier vide appelé **triggerfwupdateondevice**.  Dans le dossier **triggerfwupdateondevice** , créez un fichier package.json à l’aide de la commande suivante à l’invite de commande.  Acceptez les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **triggerfwupdateondevice** , exécutez la commande suivante pour installer le package du Kit de développement de périphérique **azure-iothub** et **azure-iot-périphérique-mqtt** :

    ```
    npm install azure-iot-hub@dtpreview --save
    ```
    
3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **dmpatterns_getstarted_service.js** dans le dossier **triggerfwupdateondevice** .

4. Ajouter comme 'nécessite' instructions au début du fichier **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Ajoutez les déclarations de variable suivantes et remplacer les valeurs d’espace réservé :

    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
    
6. Ajoutez la fonction suivante pour rechercher et afficher la valeur de la firmwareUpdate a signalé la propriété.

    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```

7. Ajoutez la fonction suivante pour appeler la méthode firmwareUpdate pour redémarrer l’équipement cible :

    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
      
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
      
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
      
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```

8. Enfin, ajouter la fonction suivante au code afin de lancer le firmware mise à jour de la séquence et apparition périodiquement le double a signalé des propriétés :

    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
    
9. Enregistrez et fermez le fichier **dmpatterns_fwupdate_service.js** .

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commande dans le dossier **manageddevice** , exécutez la commande suivante pour commencer à écouter pour la méthode directe de redémarrage.

    ```
    node dmpatterns_fwupdate_device.js
    ```

2. À l’invite de commande dans le dossier **triggerfwupdateondevice** , exécutez la commande suivante pour déclencher le redémarrage à distance et la requête pour le double de périphérique rechercher le dernier temps de redémarrage.

    ```
    node dmpatterns_fwupdate_service.js
    ```

3. Vous verrez la réaction à la méthode directe en imprimant le message

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher une mise à jour de microprogramme à distance sur un périphérique et signalés de la double utilisé régulièrement mise à jour de propriétés pour comprendre la progression du microprogramme du processus.  

Pour savoir comment étendre votre IoT appelle de méthode de planification et de la solution sur plusieurs périphériques, consultez [calendrier et les tâches de diffusion] [ lnk-tutorial-jobs] didacticiel.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
