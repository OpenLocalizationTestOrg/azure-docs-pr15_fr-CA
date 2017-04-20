<properties
    pageTitle="Utilisez les propriétés double | Microsoft Azure"
    description="Ce didacticiel vous montre comment utiliser les propriétés double"
    services="iot-hub"
    documentationCenter=".net"
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

# <a name="tutorial-use-desired-properties-to-configure-devices-preview"></a>Didacticiel : Utiliser les propriétés de votre choix pour configurer les périphériques (aperçu)

[AZURE.INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

À la fin de ce didacticiel, vous avez deux applications de console Node.js :

* **SimulateDeviceConfiguration.js**, une application de périphérique simulé qui attend une mise à jour de la configuration souhaitée et signale l’état d’un processus de mise à jour de configuration simulée.
* **SetDesiredConfigurationAndQuery**, une application de console .NET destinée à être exécuté depuis le serveur principal, ce qui définit la configuration souhaitée sur un périphérique et les requêtes que le processus de mise à jour de configuration.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les kits de développement différents que vous pouvez utiliser pour créer des applications de périphérique et de back-end.

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Microsoft Visual Studio 2015.

+ Node.js version 0.10.x ou une version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

Si vous avez suivi la [mise en route de jumeaux de périphérique] [ lnk-twin-tutorial] (didacticiel), vous disposez déjà d’un concentrateur de gestion activée de périphérique et une identité de périphérique appelée **myDeviceId**; et vous pouvez passer à la [Création de l’application de périphérique simulé] [ lnk-how-to-configure-createapp] section.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>Créer l’application de périphérique simulé

Dans cette section, vous créez une application de console Node.js qui se connecte à votre concentrateur comme **myDeviceId**et attend une mise à jour de la configuration souhaitée et signale ensuite les mises à jour sur le processus de mise à jour de configuration simulée.

1. Créez un dossier vide appelé **simulatedeviceconfiguration**. Dans le dossier **simulatedeviceconfiguration** , créez un nouveau fichier package.json à l’aide de la commande suivante à l’invite de commande. Acceptez les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **simulatedeviceconfiguration** , exécutez la commande suivante pour installer le **périphérique-iot azure**et **azure-iot-périphérique-mqtt** package :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, de créer un nouveau fichier **SimulateDeviceConfiguration.js** dans le dossier **simulatedeviceconfiguration** .

4. Ajoutez le code suivant dans le fichier **SimulateDeviceConfiguration.js** et remplacez l’espace réservé de **{chaîne de connexion de périphérique}** avec la chaîne de connexion que vous avez copié lors de la création de l’identité du périphérique **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });

    L’objet **Client** expose toutes les méthodes requises pour interagir avec des jumeaux de périphérique à partir du périphérique. Le code précédent, après l’initialisation de l’objet **Client** , récupère le double pour **myDeviceId**et attache un gestionnaire pour la mise à jour sur les propriétés de votre choix. Le gestionnaire vérifie qu’il est une demande de modification de configuration à proprement parler en comparant le configIds, puis appelle la méthode une méthode qui démarre la modification de configuration.

    Notez que par souci de simplicité, le code précédent utilise une valeur par défaut codée de manière irréversible pour la configuration initiale. Une application réelle serait probablement de charger cette configuration à partir d’un stockage local.
    
    > [AZURE.IMPORTANT] Les événements de modification de propriété de votre choix sont toujours émises une fois lors de la connexion du périphérique, vérifiez qu’il y a une modification réelle dans les propriétés de votre choix avant d’exécuter toute action.

5. Ajoutez les méthodes suivantes avant de la `client.open()` invocation :

        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";

            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }

        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
            
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;

            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };

    La méthode **initConfigChange** met à jour les propriétés déclarées sur l’objet local double avec la demande de mise à jour de configuration définit le statut sur **en attente**, puis le double du périphérique sur le service de mises à jour. Après la mise à jour de la double, elle simule un processus long qui met fin à l’exécution de **completeConfigChange**. Cette méthode met à jour les propriétés de signalée de la double local définissant le statut sur la **Réussite** et la suppression de l’objet **pendingConfig** . Il met ensuite à jour le double sur le service.

    Notez que, pour économiser la bande passante, déclarés sont mises à jour en spécifiant uniquement les propriétés modifiables (nommé **correctif** dans le code ci-dessus), au lieu de remplacer l’ensemble du document.

    > [AZURE.NOTE] Ce didacticiel ne simule pas tout comportement des mises à jour de configuration simultanées. Certains processus de mise à jour de configuration peuvent être en mesure de prendre en compte les modifications de la configuration de la cible lors de la mise à jour est en cours d’exécution, d’autres peut-être en file d’attente les, et d’autres pourraient les refuser avec une condition d’erreur. Veillez à vérifier le comportement souhaité pour votre processus de configuration spécifique et ajouter la logique appropriée avant d’effectuer la modification de configuration.

6. Exécutez l’application de périphérique :

        node SimulateDeviceConfiguration.js

    Vous devriez voir le message `retrieved device twin`. Conserver l’application en cours d’exécution.

## <a name="create-the-service-app"></a>Créer l’application de service

Dans cette section, vous allez créer une application console .NET qui met à jour les *Propriétés de votre choix* sur le double associé à **myDeviceId** avec un nouvel objet de configuration de télémétrie. Il interroge le jumeaux de périphérique stocké dans le concentrateur, puis affiche la différence entre les configurations de votre choix et signalées du périphérique.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique de Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **SetDesiredConfigurationAndQuery**.

    ![Nouveau projet Visual C# Bureau classique de Windows][img-createapp]

2. Dans l’Explorateur de solutions, cliquez sur le projet **SetDesiredConfigurationAndQuery** , puis cliquez sur **Manage Nuget Packages**.

3. Dans la fenêtre **Du Gestionnaire de package Nuget** , assurez-vous que l’option **inclure la version préliminaire** est cochée, recherchez **microsoft.azure.devices**, sélectionnez **installer** pour installer le la version *préliminaire* de la **Microsoft.Azure.Devices** du package et accepter les conditions d’utilisation. Cette procédure télécharge, installe et ajoute une référence dans le [Kit de développement logiciel de Microsoft Azure IoT Service] [ lnk-nuget-service-sdk] Nuget package et ses dépendances.

    ![Fenêtre de du Gestionnaire de package NuGet][img-servicenuget]

4. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier **Program.cs** :

        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé par la chaîne de connexion pour le moyeu IoT que vous avez créé dans la section précédente.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
            
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired state");

            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }

    L’objet **Registry** expose toutes les méthodes requises pour interagir avec des jumeaux de périphérique à partir du service. Le code précédent, une fois qu’il initialise l’objet **Registry** , récupère le double pour **myDeviceId**et met à jour ses propriétés souhaitées avec un nouvel objet de configuration de télémétrie.
    Ensuite, toutes les 10 secondes, il interroge le jumeaux stockées dans le concentrateur et imprime les configurations de télémétrie signalée et de votre choix. Faire référence à la [langue de requête IoT concentrateur] [ lnk-query] pour savoir comment générer des rapports riches sur tous vos périphériques.

    > [AZURE.IMPORTANT] Cette application interroge IoT concentrateur toutes les 10 secondes à titre indicatif. Utiliser des requêtes pour générer des rapports d’utilisateur faisant face à de nombreux appareils et ne pas pour détecter les modifications. Si votre solution requiert des notifications en temps réel des événements de périphérique utilisent des [messages de périphérique-nuage][lnk-d2c].

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();

8. Avec **SimulateDeviceConfiguration.js** en cours d’exécution, exécuter l’application .NET à partir de Visual Studio à l’aide de **F5** et vous doit s’afficher la configuration signalée changer de **succès** **en attente** à la **Réussite** à nouveau avec la nouvelle activation fréquence d’envoi des cinq minutes au lieu de 24 heures.

    > [AZURE.IMPORTANT] Il existe un délai d’une minute entre l’opération d’état de périphérique et les résultats de la requête. Il s’agit d’activer l’infrastructure de requête fonctionner à très grande échelle. Pour récupérer des vues cohérentes d’un seul double utilisent la méthode **getDeviceTwin** dans la classe de **Registre** .

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous définissez une configuration souhaitée en tant que de *besoin des propriétés* de la fin et a écrit une application de périphérique pour détecter cette modification et de simuler un processus de mise à jour de plusieurs étapes reporting son état en tant que *propriétés déclarées* à la double.

Utilisez les ressources suivantes pour apprendre comment :

- envoyer de télémétrie à partir de périphériques avec la [mise en route de concentrateur de IoT] [ lnk-iothub-getstarted] (didacticiel),
- planifier ou exécuter des opérations sur de grands ensembles de périphériques, consultez les [tâches d’utilisation permet de planifier et de diffusion des opérations de périphérique] [ lnk-schedule-jobs] didacticiel.
- contrôle de périphériques de manière interactive (activation sur un ventilateur à partir d’une application contrôlées par l’utilisateur), avec les [méthodes directes d’utilisation] [ lnk-methods-tutorial] didacticiel.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
