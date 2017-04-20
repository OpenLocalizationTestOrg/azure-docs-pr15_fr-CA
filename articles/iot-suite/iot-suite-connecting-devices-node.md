<properties
   pageTitle="Connectez un périphérique à l’aide de Node.js | Microsoft Azure"
   description="Décrit comment connecter un périphérique à la Suite de IoT Azure préconfiguré à distance solution d’analyse à l’aide d’une application écrite en Node.js."
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


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Connectez votre périphérique à la solution préconfigurée surveillance à distance (Node.js)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Générer et exécuter l’exemple de solution node.js

1. Pour cloner le référentiel de GitHub de *Kits de développement logiciel Microsoft Azure IoT* et installer le *dispositif de Microsoft Azure IoT SDK pour Node.js* dans votre environnement de bureau, suivez la [Préparation de votre environnement de développement] [ lnk-github-prepare] instructions.

2. À partir de votre copie locale de la [azure-iot-SDK] [ lnk-github-repo] du référentiel, copie les deux fichiers à partir du dossier du nœud / / exemples de périphériques dans un dossier sur votre périphérique :

  - packages.JSON
  - remote_monitoring.js

3. Ouvrez le fichier remote_monitoring.js et recherchez la variable suivante :

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Remplacez **[chaîne de connexion d’un périphérique de concentrateur de IoT]** par votre chaîne de connexion de périphérique. Vous pouvez rechercher les valeurs pour votre concentrateur IoT hostname, id de périphérique et clé de périphérique du tableau de bord de solution de surveillance à distance. Une chaîne de connexion du périphérique a le format suivant :

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Si votre nom d’hôte IoT concentrateur est **contoso** et que votre id de périphérique est **mydevice**, votre chaîne de connexion ressemble à :

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Enregistrez le fichier. Exécutez les commandes suivantes à une invite de commande dans le dossier qui contient ces fichiers pour installer les packages nécessaires et exécutez l’exemple d’application :

    ```
    npm install --save
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md