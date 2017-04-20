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

À la fin de ce didacticiel, vous aurez un .NET et une application de console Node.js :

* **AddTagsAndQuery.sln**, une application .NET destinée à être exécuté depuis le back-end, qui ajoute des balises et interroge des jumeaux de périphérique.
* **TwinSimulatedDevice.js**, une application Node.js qui simule un périphérique qui se connecte à votre concentrateur IoT avec l’identité du périphérique créée précédemment et indique son état de connectivité.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les kits de développement différents que vous pouvez utiliser pour créer des applications de périphérique et de back-end.

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Microsoft Visual Studio 2015.

+ Node.js version 0.10.x ou une version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Créer l’application de service

Dans cette section, vous créez une application console Node.js qui ajoute des meta-données de localisation pour le double associé à **myDeviceId**. Il interroge ensuite le jumeaux stockées dans le concentrateur de sélectionner les périphériques situés dans aux États-Unis, puis ceux que la déclaration d’une connexion cellulaire.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique de Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **AddTagsAndQuery**.

    ![Nouveau projet Visual C# Bureau classique de Windows][img-createapp]

2. Dans l’Explorateur de solutions, cliquez sur le projet **AddTagsAndQuery** , puis cliquez sur **Manage Nuget Packages**.

3. Dans la fenêtre **Du Gestionnaire de package Nuget** , assurez-vous que l’option **inclure la version préliminaire** est cochée, recherchez **microsoft.azure.devices**, sélectionnez **installer** pour installer le la version *préliminaire* de la **Microsoft.Azure.Devices** du package et accepter les conditions d’utilisation. Cette procédure télécharge, installe et ajoute une référence dans le [Kit de développement logiciel de Microsoft Azure IoT Service] [ lnk-nuget-service-sdk] Nuget package et ses dépendances.

    ![Fenêtre de du Gestionnaire de package NuGet][img-servicenuget]

4. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier **Program.cs** :

        using Microsoft.Azure.Devices;

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé par la chaîne de connexion pour le moyeu IoT que vous avez créé dans la section précédente.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }

    La classe **RegistryManager** expose toutes les méthodes requises pour interagir avec des jumeaux de périphérique à partir du service. Le code précédent s’initialise l’objet **registryManager** , puis récupère le double de **myDeviceId**et enfin met à jour ses balises avec les informations d’emplacement de votre choix.

    Après la mise à jour, il exécute deux requêtes : la première sélectionne uniquement les jumeaux de périphérique des périphériques situés dans l’usine de **Redmond43** , et le second affine la requête pour sélectionner uniquement les périphériques qui sont également connectés via un réseau cellulaire.

    Notez que le code précédent, lorsqu’il crée l’objet de **requête** , spécifie un nombre maximal de documents renvoyés. L’objet de la **requête** contient une propriété booléenne **HasMoreResults** que vous pouvez utiliser pour appeler les méthodes de **GetNextAsTwinAsync** à plusieurs fois pour récupérer tous les résultats. Une méthode appelée **GetNextAsJson** est disponible pour les résultats qui ne sont pas jumeaux de périphérique, par exemple, les résultats de requêtes d’agrégation.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

8. Exécuter cette application, et vous devriez voir un périphérique dans les résultats de la requête de demande de tous les périphériques situés dans **Redmond43** et aucun pour la requête qui restreint les résultats pour les périphériques qui utilisent un réseau cellulaire.

    ![Dans la fenêtre Résultats de la requête][img-addtagapp]

Dans la section suivante, vous créez une application de périphérique qui signale les informations de connectivité et modifie le résultat de la requête dans la section précédente.

## <a name="create-the-device-app"></a>Créer l’application de périphérique

Dans cette section, vous créez une application console qui se connecte à votre concentrateur comme **myDeviceId**et met ensuite à jour de son double a signalé les propriétés contiennent les informations qu’il est connecté à l’aide d’un réseau cellulaire de Node.js.

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

6. Maintenant que le périphérique a signalé ses informations de connexion, il doit apparaître dans les deux requêtes. Exécutez l’application .NET **AddTagsAndQuery** pour réexécuter les requêtes. Ce temps **myDeviceId** doit apparaître dans les résultats de la requête.

    ![][img-addtagapp2]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous configuré un nouveau concentrateur IoT dans Azure portal et ensuite créé une identité de périphérique dans le Registre d’identité du concentrateur. Vous ajouté des meta-données de périphérique en tant que balises à partir d’une application back-end et a écrit une application de périphérique simulé pour signaler des informations de connectivité de dispositif dans le double du périphérique. Vous avez également appris comment interroger ces informations à l’aide du concentrateur IoT langage de requête de type SQL.

Utilisez les ressources suivantes pour apprendre comment :

- envoyer de télémétrie à partir de périphériques avec la [mise en route de concentrateur de IoT] [ lnk-iothub-getstarted] (didacticiel),
- configurer des périphériques à l’aide des propriétés de double avec l' [utiliser propriétés pour configurer les périphériques] [ lnk-twin-how-to-configure] (didacticiel),
- contrôle de périphériques de manière interactive (activation sur un ventilateur à partir d’une application contrôlées par l’utilisateur) avec les [méthodes directes d’utilisation] [ lnk-methods-tutorial] didacticiel.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

