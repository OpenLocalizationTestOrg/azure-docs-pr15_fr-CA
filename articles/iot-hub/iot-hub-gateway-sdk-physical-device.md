<properties
    pageTitle="Utiliser un périphérique réel avec le SDK de passerelle IoT | Microsoft Azure"
    description="Azure SDK de passerelle IoT procédure pas à pas à l’aide d’un dispositif de Texas Instruments SensorTag pour envoyer des données via une passerelle s’exécutant sur un Module de calcul Intel Edison IoT concentrateur"
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


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-real-device-using-linux"></a>Azure IoT passerelle SDK (version bêta) – envoyer des messages de périphérique-nuage avec un périphérique réel à l’aide de Linux

Cette procédure pas à pas de l' [exemple de faible consommation d’énergie de Bluetooth] [ lnk-ble-samplecode] vous montre comment utiliser le [Kit de développement logiciel Azure IoT passerelle] [ lnk-sdk] de télémétrie de périphérique-nuage directe à IoT concentrateur à partir d’un périphérique physique et comment router les commandes IoT concentrateur vers un périphérique physique.

Cette procédure pas à pas couvre :

* **Architecture**: informations architecturales importantes sur l’échantillon de faible consommation d’énergie de Bluetooth.

* **Générer et exécuter**: les étapes nécessaires pour générer et exécuter l’exemple.

## <a name="architecture"></a>Architecture

La procédure pas à pas vous montre comment créer et exécuter une passerelle IoT sur un Module de calcul Intel Edison fonctionnant sous Linux. La passerelle est construite à l’aide du SDK de passerelle IoT. L’exemple utilise un périphérique Bluetooth faible énergie (BLE) SensorTag de Texas Instruments pour collecter des données de température.

Lorsque vous exécutez la passerelle qu’il :

- Se connecte à un périphérique SensorTag en utilisant le protocole Bluetooth faible énergie (BLE).
- Se connecte à un concentrateur IoT utilisant le protocole HTTP.
- Transfère la télémétrie à partir du périphérique SensorTag IoT concentrateur.
- Achemine les commandes IoT concentrateur vers le périphérique SensorTag.

La passerelle contient les modules suivants :

- Un *module BLE* qui sert d’interface avec un dispositif BLE pour recevoir des données de température le dispositif et envoyer des commandes vers le périphérique.
- Un *Nuage de BLE pour module de dispositif* qui convertit les messages JSON en provenance du nuage en instructions de BLE pour le *module BLE*.
- Un *module de journal* qui enregistre tous les messages de passerelle.
- Un *module de mappage d’identité* qui effectue la traduction entre les adresses MAC des périphériques BLE et identités des appareils Azure IoT concentrateur.
- Un *module IoT concentrateurs* qui transfère les données de télémétrie à un concentrateur IoT et reçoit des commandes du dispositif d’un concentrateur IoT.
- Un *module d’imprimante BLE* qui interprète des données télémétriques fournies par le périphérique BLE et imprime mise en forme des données sur la console pour activer la résolution des problèmes et de débogage.

### <a name="how-data-flows-through-the-gateway"></a>Le flux des données par l’intermédiaire de la passerelle

Le schéma suivant illustre le pipeline de flux de données télémétriques téléchargement :

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Les étapes qui prend d’un élément de télémétrie lors des déplacements à partir d’un périphérique BLE et IoT Hub sont :

1. Le périphérique BLE génère un échantillon de la température et l’envoie vers le module BLE de la passerelle via Bluetooth.
2. Le module BLE reçoit l’échantillon et le publie dans le broker ainsi que l’adresse MAC du périphérique.
3. Le module de mappage d’identité récupère ce message et utilise une table interne à l’adresse MAC du périphérique se traduire par une identité de périphérique IoT Hub (un ID de périphérique et la clé de périphérique). Il publie ensuite un message qui contient les données d’exemple de température, de l’adresse MAC de la clé de périphérique, l’ID de périphérique et le périphérique.
4. Le module IoT concentrateur reçoit ce nouveau message (généré par le module de mappage d’identité) et le publie à IoT concentrateur.
5. Le module de journalisation enregistre tous les messages à partir du courtier dans un fichier disque.

Le schéma suivant illustre le pipeline de flux de données de commande dispositif :

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. Le module IoT Hub interroge périodiquement le concentrateur IoT pour les nouveaux messages de la commande.
2. Lorsque le module IoT concentrateur reçoit un nouveau message de commande, il le publie dans le service broker.
3. Le module de mappage d’identité récupère le message de commande et utilise une table interne pour traduire l’ID de périphérique IoT Hub à un adresse MAC du périphérique. Il publie ensuite un message qui inclut l’adresse MAC de l’équipement cible dans la table des propriétés du message.
4. Le nuage BLE au module de périphérique sélectionne ce message et il se traduit par l’instruction BLE correcte pour le module BLE. Il publie ensuite un nouveau message.
5. Le module BLE récupère ce message et exécute l’instruction d’e/s en communication avec le périphérique BLE.
6. Le module de journalisation enregistre tous les messages à partir du courtier dans un fichier disque.

## <a name="prepare-your-hardware"></a>Préparation du matériel

Ce didacticiel suppose que vous utilisez un périphérique de [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) connecté à une carte Intel Edison.

### <a name="set-up-the-edison-board"></a>Configurer le forum Edison

Avant de commencer, assurez-vous que votre périphérique Edison peuvent se connecter à votre réseau sans fil. Pour configurer votre périphérique Edison, vous avez besoin pour le connecter à un ordinateur hôte. Intel fournit la mise en route des guides pour les systèmes d’exploitation suivants :

- [Mise en route avec la carte de développement Edison Intel sous Windows 64 bits][lnk-setup-win64].
- [Mise en route avec la carte de développement Edison Intel sous Windows 32-bit][lnk-setup-win32].
- [Mise en route avec la carte de développement Intel Edison sous Mac OS X][lnk-setup-osx].
- [Mise en route avec la carte Edison Intel® sous Linux][lnk-setup-linux].

Pour configurer votre périphérique Edison et vous familiariser avec elle, vous devez effectuer toutes les étapes de ces « mise en route » les articles, à l’exception de la dernière étape, « Choisissez IDE », qui n’est pas nécessaire pour le didacticiel en cours. À la fin du processus d’installation Edison que vous avez :

- Flashez votre Edison avec le microprogramme le plus récent.
- Permet d’établir une connexion série de votre hôte vers l’Edison.
- Exécutez le script **configure_edison** pour définir un mot de passe et activer le WiFi sur votre Edison.

### <a name="enable-connectivity-to-the-sensortag-device-from-your-edison-board"></a>Activer la connectivité sur le périphérique SensorTag de votre carte Edison

Avant d’exécuter l’exemple, vous devez vérifier que votre carte Edison peut se connecter au périphérique SensorTag.

Vous devez d’abord vérifier que votre Edison peut se connecter au périphérique SensorTag.

1. Débloquer le bluetooth sur le Edison et vérifiez que le numéro de version est **5.37**.
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. Exécutez la commande **bluetoothctl** . Vous êtes maintenant dans un shell interactif bluetooth. 

3. Entrez la commande d' **alimentation sur** le contrôleur bluetooth sous tension. Vous devriez voir une sortie similaire à :
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

4. Dans le shell interactif bluetooth, entrez la commande **analyse** pour rechercher des périphériques bluetooth. Vous devriez voir une sortie similaire à :
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

5. Rendre SensorTag détectable en appuyant sur le petit bouton (le voyant vert doit clignoter). Le Edison doit détecter le périphérique SensorTag :
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    Dans cet exemple, vous pouvez voir que l’adresse MAC du périphérique SensorTag est **A0:E6:F8:B5:F6:00**.

6. Désactiver l’analyse en entrant la commande **analyse hors tension** .
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

7. Se connecter à votre périphérique de SensorTag à l’aide de son adresse MAC en entrant **connecter \<adresse MAC >**. Notez que l’exemple de sortie ci-dessous est abrégé :
    
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
    
    Remarque : Vous pouvez répertorier les caractéristiques du GATT du périphérique à l’aide de la commande de la **liste d’attributs** .

8. Vous pouvez maintenant déconnecter le périphérique à l’aide de la commande **Déconnecter** et quitter puis à partir du shell de bluetooth à l’aide de la commande **quit** :
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Vous êtes maintenant prêt à exécuter l’exemple de passerelle de BLE sur votre périphérique Edison.

## <a name="run-the-ble-gateway-sample"></a>Exécuter l’exemple de passerelle de BLE

Pour exécuter l’exemple BLE sur votre Edison, vous devez effectuer trois tâches :

- Configurer deux dispositifs dans votre concentrateur IoT.
- Créer le Kit de développement de passerelle IoT sur votre périphérique Edison.
- Configurer et exécuter l’exemple BLE sur votre périphérique Edison.

Au moment de la rédaction, le Kit de développement de passerelle IoT prend uniquement en charge les passerelles qui utilisent des modules BLE sous Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurer les deux dispositifs dans votre concentrateur IoT

- [Créer un concentrateur IoT] [ lnk-create-hub] votre abonnement Azure, vous devez le nom de votre concentrateur pour effectuer cette procédure pas à pas. Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.
- Ajouter un périphérique appelé **SensorTag_01** à votre concentrateur IoT et prenez note de sa clé d’id et de périphérique. Vous pouvez utiliser l' [Explorateur de périphérique ou iothub-explorer] [ lnk-explorer-tools] outils pour ajouter ce périphérique sur le concentrateur IoT que vous avez créé à l’étape précédente et récupérer sa clé. Lorsque vous configurez la passerelle, vous allez mapper ce périphérique sur le périphérique SensorTag.

### <a name="build-the-iot-gateway-sdk-on-your-edison-device"></a>Créer le Kit de développement de passerelle IoT sur votre périphérique Edison

La version de **git** sur la Edsion ne gère pas submodules. Pour télécharger la source complète pour le SDK de passerelle IoT à la Edison, vous avez deux options :

- Option #1 : Cloner le [SDK de passerelle Azure IoT] [ lnk-sdk] référentiel sur votre Edison et puis cloner manuellement le référentiel pour chaque submodule.
- Option #2 : Cloner le [SDK de passerelle Azure IoT] [ lnk-sdk] espace de stockage sur un périphérique de bureau où **git** prend en charge les submodules et copiez le référentiel complet avec submodules sur votre Edison.

Si vous choisissez l’option #2, utilisez les commandes de **git** suivantes pour cloner le Kit de développement de passerelle IoT et toutes ses submodules :

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

Vous devez ensuite compresser l’intégralité du référentiel local dans un fichier archive unique avant de le copier à la Edison. Vous pouvez utiliser un utilitaire tel que **pscp** , qui est inclus avec **Putty** pour copier le fichier d’archive à l’Edison. Par exemple :

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Lorsque vous avez une copie complète du référentiel IoT passerelle SDK sur votre Edison, vous pouvez créer à l’aide de la commande suivante à partir du dossier qui contient le Kit de développement logiciel :

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-edison-device"></a>Configurer et exécuter l’exemple BLE sur votre périphérique Edison

Pour démarrer et exécuter l’exemple, vous devez configurer chaque module qui fait partie de la passerelle. Cette configuration est fournie dans un fichier JSON et vous devez configurer tous les cinq modules de participants. Voici un exemple de fichier JSON fourni dans le référentiel appelé **gateway_sample.json** , que vous pouvez utiliser comme point de départ pour la création de votre propre fichier de configuration. Ce fichier est dans le dossier **samples/ble_gateway_hl/src** dans la copie locale du référentiel IoT passerelle SDK.

Les sections suivantes décrivent comment modifier ce fichier de configuration pour l’exemple BLE et supposent que le référentiel IoT passerelle SDK se trouve dans le **/home/root/azure-iot-gateway-sdk /** dossier sur votre périphérique Edison. Si le référentiel se trouve ailleurs, vous devez ajuster les chemins d’accès en conséquence :

#### <a name="logger-configuration"></a>Configuration de la journalisation

En supposant que le référentiel de la passerelle se trouve dans le dossier **/home/root/azure-iot-gateway-sdk /**, configurez le module journal comme suit :

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### <a name="ble-module-configuration"></a>Configuration du module BLE

L’exemple de configuration pour le périphérique BLE suppose un périphérique Texas Instruments SensorTag. N’importe quel périphérique BLE standard qui peut fonctionner comme un périphérique du GATT doit fonctionner, mais vous devez mettre à jour les codes caractéristiques du GATT et les données (pour les instructions d’écriture). Ajoutez l’adresse MAC de votre périphérique SensorTag : 

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>Module de concentrateur de IoT

Ajoutez le nom de votre concentrateur IoT. La valeur de suffixe est généralement **azure-devices.net**:

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuration de module de mappage identité

Ajoutez l’adresse MAC de votre périphérique SensorTag et l’Id de périphérique et de la clé du périphérique **SensorTag_01** que vous avez ajouté à votre concentrateur IoT :

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuration du module BLE imprimante

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### <a name="routing-configuration"></a>Configuration du routage

La configuration suivante vérifie les éléments suivants :
- Le module de **journalisation** reçoit et consigner tous les messages.
- Le module **SensorTag** envoie des messages pour le **mappage** et les modules de **BLE imprimante** .
- Le module de **mappage** envoie des messages au module **IoTHub** pour être envoyées à votre concentrateur IoT.
- Le module **IoTHub** envoie des messages vers le module de **mappage** .
- Le module de **mappage** envoie des messages vers le module **SensorTag** .

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

Pour exécuter l’exemple, vous exécutez le **ble_gateway_hl** binaire en passant le chemin d’accès au fichier de configuration JSON. Si vous avez utilisé le fichier **gateway_sample.json** , la commande à exécuter ressemble à ceci :

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Vous devrez peut-être appuyer sur le petit bouton situé sur le périphérique SensorTag pour la découverte avant d’exécuter l’exemple.

Lorsque vous exécutez l’exemple, vous pouvez utiliser l' [Explorateur de périphérique ou explorer-iothub] [ lnk-explorer-tools] outil pour surveiller les messages de la passerelle transfère à partir du périphérique SensorTag.

## <a name="send-cloud-to-device-messages"></a>Envoyer des messages de nuage-appareil

Le module BLE prend également en charge l’envoi d’instructions à partir du concentrateur de IoT Azure sur le périphérique. Vous pouvez utiliser l' [Explorateur de périphérique du concentrateur Azure IoT](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) ou l' [Explorateur du concentrateur IoT](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) pour envoyer des messages JSON qui le module de passerelle BLE transmet sur le périphérique BLE. Par exemple, si vous utilisez le périphérique de Texas Instruments SensorTag vous pouvez envoyer les messages JSON suivants au périphérique IoT concentrateur.

- Réinitialiser tous les voyants et l’alarme (désactiver)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- Configurer des e/s en tant que 'distant'

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Activer le voyant rouge

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Allumer le voyant vert

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- Activer l’alarme

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

Le comportement par défaut pour un périphérique utilisant le protocole HTTP pour se connecter à un concentrateur de IoT doit vérifier toutes les 25 minutes pour une nouvelle commande. Par conséquent, si vous envoyez plusieurs commandes séparées vous devez attendre 25 minutes pour ce périphérique à recevoir de chaque commande.

> [AZURE.NOTE] La passerelle vérifie également pour les nouvelles commandes chaque fois qu’il démarre et vous pouvez le forcer pour traiter une commande par l’arrêt et le démarrage de la passerelle.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez acquérir une compréhension plus avancée du SDK passerelle IoT et faire des essais avec des exemples de code, consultez les didacticiels suivants de développeur et les ressources :

- [Passerelle de IoT Azure SDK][lnk-sdk]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
