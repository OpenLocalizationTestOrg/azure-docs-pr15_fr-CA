<properties
    pageTitle="Mise en route de jumeaux | Microsoft Azure"
    description="Ce didacticiel vous montre comment utiliser des jumeaux"
    services="iot-hub"
    documentationCenter="node"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>

# <a name="tutorial-get-started-with-device-twins-preview"></a>Didacticiel : Mise en route de jumeaux de périphérique (aperçu)

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

À la fin de ce didacticiel, vous avez deux applications de console Node.js :

* **AddTagsAndQuery.js**, une application Node.js destinée à être exécuté depuis le back-end, qui ajoute des balises et interroge des jumeaux de périphérique.
* **TwinSimulatedDevice.js**, une application Node.js qui simule un périphérique qui se connecte à votre concentrateur IoT avec l’identité du périphérique créée précédemment et indique son état de connectivité.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les kits de développement différents que vous pouvez utiliser pour créer des applications de périphérique et de back-end.

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Node.js version 0.10.x ou une version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Créer l’application de service

Dans cette section, vous créez une application console Node.js qui ajoute des meta-données de localisation pour le double associé à **myDeviceId**. Il interroge ensuite le jumeaux stockées dans le concentrateur de sélectionner les périphériques situés dans aux États-Unis, puis ceux que la déclaration d’une connexion cellulaire.

1. Créez un dossier vide appelé **addtagsandqueryapp**. Dans le dossier **addtagsandqueryapp** , créez un nouveau fichier package.json à l’aide de la commande suivante à l’invite de commande. Acceptez les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **addtagsandqueryapp** , exécutez la commande suivante pour installer le package **azure-iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **AddTagsAndQuery.js** dans le dossier **addtagsandqueryapp** .

4. Ajoutez le code suivant dans le fichier **AddTagsAndQuery.js** et remplacez l’espace réservé de **{chaîne de connexion de service}** avec la chaîne de connexion que vous avez copié lorsque vous avez créé votre concentrateur :

        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
             
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });

    L’objet **Registry** expose toutes les méthodes requises pour interagir avec des jumeaux de périphérique à partir du service. Le code précédent s’initialise l’objet **Registry** , puis récupère le double de **myDeviceId**et enfin met à jour ses balises avec les informations d’emplacement de votre choix.

    Après les balises de mise à jour, il appelle la fonction **queryTwins** .

7. Ajoutez le code suivant à la fin **AddTagsAndQuery.js** pour implémenter la fonction **queryTwins** :

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
            
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };

    Le code précédent s’exécute deux requêtes : la première sélectionne uniquement les jumeaux de périphérique des périphériques situés dans l’usine de **Redmond43** , et le second affine la requête pour sélectionner uniquement les périphériques qui sont également connectés via un réseau cellulaire.

    Notez que le code précédent, lorsqu’il crée l’objet de **requête** , spécifie un nombre maximal de documents renvoyés. L’objet de la **requête** contient une propriété booléenne **hasMoreResults** que vous pouvez utiliser pour appeler les méthodes de **nextAsTwin** à plusieurs fois pour récupérer tous les résultats. Une méthode appelée **suivant** est disponible pour les résultats qui ne sont pas jumeaux de périphérique, par exemple, les résultats de requêtes d’agrégation.

8. Exécutez l’application avec :

        node AddTagsAndQuery.js

    Vous devriez voir un périphérique dans les résultats de la requête de demande de tous les périphériques situés dans **Redmond43** et aucun pour la requête qui restreint les résultats pour les périphériques qui utilisent un réseau cellulaire.

    ![][1]

Dans la section suivante, vous créez une application de périphérique qui signale les informations de connectivité et modifie le résultat de la requête dans la section précédente.

## <a name="create-the-device-app"></a>Créer l’application de périphérique

Dans cette section, vous créez une application console qui se connecte à votre concentrateur comme **myDeviceId**et met ensuite à jour de son double a signalé les propriétés contiennent les informations qu’il est connecté à l’aide d’un réseau cellulaire de Node.js.

> [AZURE.NOTE] À ce stade, jumeaux de périphérique est accessibles uniquement à partir de périphériques qui se connectent au concentrateur IoT utilisant le protocole MQTT. Veuillez vous reporter à la [prise en charge de la MQTT] [ lnk-devguide-mqtt] l’article pour obtenir des instructions sur la conversion d’applications de périphérique existant à utiliser MQTT.

1. Créez un dossier vide appelé **reportconnectivity**. Dans le dossier **reportconnectivity** , créez un nouveau fichier package.json à l’aide de la commande suivante à l’invite de commande. Acceptez les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **reportconnectivity** , exécutez la commande suivante pour installer le **périphérique-iot azure**et **azure-iot-périphérique-mqtt** package :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **ReportConnectivity.js** dans le dossier **reportconnectivity** .

4. Ajoutez le code suivant dans le fichier **ReportConnectivity.js** et remplacez l’espace réservé de **{chaîne de connexion de périphérique}** avec la chaîne de connexion que vous avez copié lors de la création de l’identité du périphérique **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    L’objet **Client** expose toutes les méthodes que vous avez besoin d’interagir avec des jumeaux de périphérique à partir du périphérique. Le code précédent, une fois qu’il initialise l’objet **Client** , récupère le double pour **myDeviceId** et met à jour sa propriété signalée avec les informations de connectivité.

5. Exécutez l’application de périphérique

        node ReportConnectivity.js

    Vous devriez voir le message `twin state reported`.

6. Maintenant que le périphérique a signalé ses informations de connexion, il doit apparaître dans les deux requêtes. Revenez dans le dossier **addtagsandqueryapp** et réexécutez les requêtes :

        node AddTagsAndQuery.js

    Ce temps **myDeviceId** doit apparaître dans les résultats de la requête.

    ![][3]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous configuré un nouveau concentrateur IoT dans Azure portal et ensuite créé une identité de périphérique dans le Registre d’identité du concentrateur. Vous ajouté des meta-données de périphérique en tant que balises à partir d’une application back-end et a écrit une application de périphérique simulé pour signaler des informations de connectivité de dispositif dans le double du périphérique. Vous avez également appris comment interroger ces informations à l’aide du concentrateur IoT langage de requête de type SQL.

Utilisez les ressources suivantes pour apprendre comment :

- envoyer de télémétrie à partir de périphériques avec la [mise en route de concentrateur de IoT] [ lnk-iothub-getstarted] (didacticiel),
- configurer des périphériques à l’aide des propriétés de double avec l' [utiliser propriétés pour configurer les périphériques] [ lnk-twin-how-to-configure] (didacticiel),
- contrôle de périphériques de manière interactive (activation sur un ventilateur à partir d’une application contrôlées par l’utilisateur), avec les [méthodes directes d’utilisation] [ lnk-methods-tutorial] didacticiel.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md