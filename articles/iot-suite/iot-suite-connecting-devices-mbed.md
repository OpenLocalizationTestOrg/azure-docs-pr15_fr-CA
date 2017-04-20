<properties
   pageTitle="Connectez un périphérique à l’aide de C sur mbed | Microsoft Azure"
   description="Décrit comment connecter un périphérique à la Suite de IoT Azure préconfiguré à distance solution d’analyse à l’aide d’une application écrite en C en cours d’exécution sur un périphérique mbed."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Connectez votre périphérique à la solution préconfigurée surveillance à distance (mbed)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Générer et exécuter l’exemple de solution C

Les instructions suivantes décrivent les étapes de connexion un [Activé mbed FRDM-K64F Freescale] [ lnk-mbed-home] device à la solution de surveillance à distance.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Connectez le dispositif de mbed à votre ordinateur du réseau et de bureau

1. Connectez le périphérique mbed à votre réseau à l’aide d’un câble Ethernet. Cette étape est nécessaire car l’exemple d’application requiert un accès à internet.

2. Reportez-vous à la section [Mise en route de la mbed] [ lnk-mbed-getstarted] connecter votre appareil mbed à votre ordinateur de bureau.

3. Si votre ordinateur de bureau exécutant Windows, reportez-vous à la section [Configuration de PC] [ lnk-mbed-pcconnect] pour configurer l’accès port série sur votre périphérique mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Importer l’exemple de code et de créer un projet mbed

1. Dans votre navigateur web, allez sur le [site des développeurs](https://developer.mbed.org/)de mbed.org. Si vous n’avez pas souscrit, vous voyez une option pour créer un compte (c’est gratuit). Dans le cas contraire, vous connecter avec vos informations d’identification du compte. Dans l’angle supérieur droit de la page, cliquez ensuite sur **compilateur** . Cette action vous amène à l’interface de *l’espace de travail* .

2. Assurez-vous que la plate-forme matérielle que vous utilisez apparaît dans l’angle supérieur droit de la fenêtre, ou cliquez sur l’icône dans l’angle droit pour sélectionner votre plate-forme matérielle.

3. Dans le menu principal, cliquez sur **Importer** . Cliquez sur **Cliquez ici** pour importer à partir de l’URL de ce lien à côté du logo de globe mbed.

    ![][6]

4. Dans la fenêtre contextuelle, entrez le lien pour le https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ code échantillon, puis cliquez sur **Importer**.

    ![][7]

5. Dans la fenêtre de compilateur mbed, vous pouvez voir que l’importation de ce projet importe également les différentes bibliothèques. Certaines sont fournies et gérées par l’équipe d’Azure IoT ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), tandis que d’autres sont des bibliothèques tierces disponibles dans le catalogue de bibliothèques mbed.

    ![][8]

6. Ouvrez le fichier remote_monitoring\remote_monitoring.c et recherchez le code suivant dans le fichier :

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Remplacez [Id de périphérique] et [clé de périphérique], avec les données de votre périphérique pour activer le programme d’exemple pour vous connecter à votre concentrateur IoT. Utilisez le nom d’hôte du concentrateur IoT remplacer [nom IoTHub] et les espaces réservés [IoTHub suffixe, c'est-à-dire azure-devices.net]. Par exemple, si votre nom d’hôte du concentrateur IoT est **contoso.azure-devices.net**, **contoso** est le **hubName** et tous les éléments après l' **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### <a name="walk-through-the-code"></a>Passez en revue le code

Si vous êtes intéressé par le fonctionnement du programme, cette section décrit certains éléments clés de l’exemple de code. Si vous souhaitez uniquement exécuter le code, passez directement à [Générer et exécuter le programme](#buildandrun).

#### <a name="defining-the-model"></a>Définition du modèle

Cet exemple utilise le [sérialiseur] [ lnk-serializer] bibliothèque pour définir un modèle qui spécifie les messages, le périphérique peut envoyer à IoT Hub et recevoir IoT concentrateur. Dans cet exemple, l’espace de noms **Contoso** définit un modèle de **Thermostat** qui spécifie les données de télémétrie **ExternalTemperature**, **température**et **l’humidité** ainsi que des métadonnées telles que l’id de périphérique et les commandes que le dispositif répond aux propriétés d’un périphérique :

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

Liées à la définition de modèle sont les définitions pour les commandes **SetTemperature** et **SetHumidity** que le dispositif répond à :

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### <a name="connecting-the-model-to-the-library"></a>Connectez le modèle dans la bibliothèque

Les fonctions **sendMessage** et **IoTHubMessage** sont du code réutilisable pour l’envoi de télémétrie à partir du périphérique et de connexion des messages IoT concentrateur aux gestionnaires de commandes.

#### <a name="the-remotemonitoringrun-function"></a>La fonction remote_monitoring_run

La fonction du programme **principal** appelle la fonction **remote_monitoring_run** lorsque l’application commence à exécuter le comportement du périphérique comme un client d’appareil IoT concentrateur. Cette fonction **remote_monitoring_run** se compose principalement de paires imbriqués de fonctions :

- **plate-forme\_init** et **plate-forme\_deinit** des opérations spécifiques à la plateforme d’initialisation et d’arrêt.
- **sérialiseur\_init** et **sérialiseur\_deinit** d’initialisation et de dé-initialiser la bibliothèque du sérialiseur.
- **IoTHubClient\_créer** et **IoTHubClient\_détruire** créer un handle client, **iotHubClientHandle**, en utilisant les informations d’identification de périphérique pour la connexion à votre concentrateur IoT.

Dans la section principale de la fonction **remote_monitoring_run** , le programme effectue les opérations suivantes à l’aide de la poignée de **iotHubClientHandle** :

- Crée une instance du modèle Contoso thermostat et définit les rappels de message pour les deux commandes.
- Envoie des informations sur le périphérique lui-même, y compris les commandes qu’il prend en charge, votre concentrateur IoT à l’aide de la bibliothèque de sérialiseur. Lorsque le concentrateur reçoit ce message, il modifie l’état du périphérique dans le panneau de commandes **en attente** en cours **d’exécution**.
- Démarre une boucle **while** qui envoie des valeurs d’humidité, température et température externe au concentrateur de IoT chaque seconde.

Pour référence, Voici un exemple de message **DeviceInfo** envoyé à IoT concentrateur au démarrage :

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Pour référence, Voici un exemple de message de **télémétrie** envoyé à IoT concentrateur :

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Pour référence, Voici un exemple de **que commande** reçue IoT concentrateur :

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### <a name="build-and-run-the-program"></a>Générer et exécuter le programme

1. Cliquez sur **compiler** pour générer le programme. Vous pouvez en toute sécurité ignorer tous les avertissements, mais si la génération génère des erreurs, corrigez-les avant de continuer.

2. Si la génération est réussie, le site Web de mbed du compilateur génère un fichier .bin avec le nom de votre projet et le télécharge sur votre ordinateur local. Copiez le fichier .bin sur le périphérique. L’enregistrement du fichier .bin pour le périphérique provoque le périphérique redémarrer et exécuter le programme contenu dans le fichier .bin. Vous pouvez redémarrer manuellement le programme à tout moment en appuyant sur le bouton de réinitialisation sur le périphérique mbed.

3. Connecter le périphérique à l’aide d’une application client SSH tel que PuTTY. Vous pouvez déterminer le port série que votre périphérique utilise en vérifiant le Gestionnaire de périphériques Windows.

    ![][11]

4. Dans PuTTY, cliquez sur le type de connexion **série** . Le périphérique se connecte généralement à 9600 bauds, entrez dans la zone **vitesse** 9600. Puis cliquez sur **Ouvrir**.

5. Le programme démarre l’exécution. Vous devrez peut-être réinitialiser la carte (appuyez sur CTRL + Pause ou appuyez sur le bouton de réinitialisation de la carte) si le programme ne démarre pas automatiquement lorsque vous vous connectez.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer
