<properties
    pageTitle="Simuler un périphérique avec le SDK de passerelle IoT | Microsoft Azure"
    description="Azure SDK de passerelle IoT procédure pas à pas pour illustrer la télémétrie envoi à partir d’un périphérique simulé à l’aide du SDK de passerelle IoT Azure à l’aide de Windows."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-simulated-device-using-windows"></a>Azure IoT passerelle SDK (version bêta) – envoyer des messages de périphérique-nuage avec un périphérique simulé à l’aide de Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Générer et exécuter l’exemple.

Avant de commencer, vous devez :

- [Configurer votre environnement de développement] [ lnk-setupdevbox] pour travailler avec le SDK de Windows.
- [Créer un concentrateur IoT] [ lnk-create-hub] votre abonnement Azure, vous devez le nom de votre concentrateur pour effectuer cette procédure pas à pas. Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.
- Ajouter les deux périphériques à votre concentrateur IoT et prenez note de leur ID et les clés du périphérique. Vous pouvez utiliser l' [Explorateur de périphérique ou iothub-explorer] [ lnk-explorer-tools] outil pour ajouter vos périphériques au concentrateur IoT que vous avez créé à l’étape précédente et récupérer leurs clés.

Pour générer l’exemple :

1. Ouvrez une invite de commande **d’invite de commandes de développeur pour VS2015** .
2. Accédez au dossier racine de votre copie locale du référentiel **azure-iot-passerelle-Kit de développement logiciel** .
3. Exécuter le **outils\\build.cmd** script. Ce script crée un fichier de solution Visual Studio génère la solution et exécute les tests. Vous trouverez la solution Visual Studio dans le dossier de **génération** dans votre copie locale du référentiel **azure-iot-passerelle-Kit de développement logiciel** .

Pour exécuter l’exemple :

Dans un éditeur de texte, ouvrez le fichier **exemples de\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** dans votre copie locale du référentiel **azure-iot-passerelle-Kit de développement logiciel** . Ce fichier configure les modules dans l’exemple de passerelle :

- Le module **IoTHub** se connecte à votre concentrateur IoT. Vous devez le configurer pour envoyer des données à votre concentrateur IoT. En particulier, la valeur de **IoTHubName** sur le nom de votre concentrateur IoT et définissez la valeur de **IoTHubSuffix** sur **azure-devices.net**. La valeur de **Transport** sur un des : « HTTP », « AMQP » ou « MQTT ». Notez qu’actuellement, seul « HTTP » partagent une connexion TCP pour tous les messages de périphérique. Si vous définissez la valeur à « AMQP » ou « MQTT », la passerelle conserve une connexion TCP distincte à IoT concentrateur pour chaque périphérique.
- Le module de **mappage** mappe les adresses MAC des périphériques simulés pour les ID de périphérique du concentrateur de IoT. Assurez-vous que les valeurs **d’ID de périphérique** correspondent aux codes des deux périphériques que vous avez ajouté à votre concentrateur IoT, et que les valeurs de **deviceKey** contiennent les clés des deux périphériques.
- Les modules **BLE1** et **BLE2** sont les périphériques simulés. Notez comment leurs adresses MAC correspondent à ceux du module de **mappage** .
- Le module de **journalisation** enregistre l’activité de votre passerelle dans un fichier.
- Les valeurs de **chemin d’accès du module** illustrés ci-dessous supposent que vous cloné le référentiel IoT passerelle SDK à la racine de votre lecteur **C:** . Si vous l’avez téléchargé vers un autre emplacement, vous devez ajuster les valeurs de **chemin d’accès du module** en conséquence.
- Le tableau de **liens** au bas du fichier JSON connecte les modules **BLE1** et **BLE2** pour le module de **mappage** et le module de **mappage** pour le module **IoTHub** . Elle garantit également que tous les messages sont consignés par le module de **journalisation** .

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\iothub\\Debug\\iothub_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\identitymap\\Debug\\identity_map_hl.dll",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}
```

Enregistrer toutes les modifications apportées au fichier de configuration.

Pour exécuter l’exemple :

1. À une invite de commandes, accédez au dossier racine de votre copie locale du référentiel **azure-iot-passerelle-Kit de développement logiciel** .
2. Exécutez la commande suivante :
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. Vous pouvez utiliser l' [Explorateur de périphérique ou iothub-explorer] [ lnk-explorer-tools] outil pour surveiller les messages IoT concentrateur reçoit de la passerelle.


## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez acquérir une compréhension plus avancée du SDK passerelle IoT et faire des essais avec des exemples de code, consultez les didacticiels suivants de développeur et les ressources :

- [Envoyer des messages de périphérique-nuage à partir d’un périphérique réel avec le SDK de passerelle IoT][lnk-physical-device]
- [Passerelle de IoT Azure SDK][lnk-gateway-sdk]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 