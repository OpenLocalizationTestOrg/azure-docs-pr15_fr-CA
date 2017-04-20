<properties
 pageTitle="Créer et déployer l’application blink | Microsoft Azure"
 description="Clone de l’exemple d’application Node.js à partir de Github et gulp pour déployer cette application sur votre carte de framboises Pi 3. Cet exemple d’application se met à clignoter le voyant connecté à la carte toutes les deux secondes."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="13-create-and-deploy-the-blink-application"></a>1.3 créer et déployer l’application de clignotement

## <a name="131-what-you-will-do"></a>1.3.1 ce que vous ferez

Cloner l’exemple d’application Node.js à partir de Github et utiliser l’outil de choses pour déployer l’exemple d’application à votre framboises Pi 3. L’exemple d’application se met à clignoter le voyant connecté à la carte toutes les deux secondes. Si vous répondez à tous les problèmes, rechercher des solutions dans la [page Dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="132-what-you-will-learn"></a>1.3.2 vous apprendrez

- L’utilisation de la `device-discover-cli` outil pour récupérer des informations de mise en réseau sur votre Pi.
- Comment faire pour déployer et exécuter l’exemple d’application sur votre Pi.
- Comment faire pour déployer et déboguer des applications exécutées à distance sur votre Pi.

## <a name="133-what-you-need"></a>1.3.3 ce dont vous avez besoin

Avec succès, vous devez avoir terminé les sections suivantes dans la leçon 1 :

- [Configurer votre périphérique](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)
- [Obtenez les outils](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

## <a name="134-obtain-the-ip-address-and-host-name-of-your-pi"></a>1.3.4 obtenir le nom d’hôte et adresse IP de votre Pi

Ouvrez une invite de commande de Windows ou d’une fenêtre de terminal dans macOS ou Ubuntu, puis exécutez la commande suivante :

```bash
devdisco list --eth
```

Vous devriez voir une sortie semblable à la suivante :

![découverte de périphérique](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Prenez note de la `IP address` et `hostname` de votre Pi. Vous avez besoin de cette information plus loin dans cette section.

> [AZURE.NOTE] Assurez-vous que votre Pi est connecté au même réseau que votre ordinateur. Par exemple, si votre ordinateur est connecté à un réseau sans fil pendant vos Pi est connecté à un réseau câblé, vous ne pouvez pas voir l’adresse IP dans la sortie de devdisco.

## <a name="135-clone-the-sample-application"></a>1.3.5 cloner l’exemple d’application

Pour ouvrir l’exemple de code, procédez comme suit :

1. Cloner le référentiel de l’exemple à partir de Github en exécutant la commande suivante :

    ```bash
    git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started.git
    ```

2. Ouvrir l’exemple d’application dans le Code de Visual Studio en exécutant les commandes suivantes :

    ```bash
    cd iot-hub-node-raspberrypi-getting-started
    cd Lesson1
    code .
    ```

![Structure de mis en pension](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-mac.png)

Le `app.js` de fichiers dans le `app` sous-dossier est le fichier source de clé qui contient le code pour contrôler le voyant.

### <a name="136-install-application-dependencies"></a>1.3.6 installer les dépendances de l’application

Installer les bibliothèques et autres modules que vous avez besoin pour l’exemple d’application en exécutant la commande suivante :

```bash
npm install
```

## <a name="137-configure-the-device-connection"></a>1.3.7 configurer la connexion du périphérique

Pour configurer la connexion du périphérique, procédez comme suit :

1. Générer le fichier de configuration du périphérique en exécutant la commande suivante :

    ```bash
    gulp init
    ```

    Le fichier de configuration `config-raspberrypi.json` contient les informations d’identification utilisateur que vous utilisez pour vous connecter votre Pi. Pour éviter la fuite d’informations d’identification de l’utilisateur, le fichier de configuration est généré dans le sous-dossier `.iot-hub-getting-started` du dossier personnel sur votre ordinateur.

2. Ouvrez le fichier de configuration de périphérique dans le Code de Visual Studio en exécutant la commande suivante :

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json

    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

3. Remplacez l’espace réservé `[device hostname or IP address]` avec l’adresse IP ou le nom d’hôte que vous obtenez dans section 1.3.4.

    ![Config.JSON](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

Félicitations ! Vous avez créé le premier exemple d’application pour votre Pi.

## <a name="138-deploy-and-run-the-sample-application"></a>1.3.8 déployer et exécuter l’exemple d’application

### <a name="1381-install-nodejs-and-npm-on-your-pi"></a>1.3.8.1 installer Node.js et NPM sur votre Pi

Installer Node.js et NPM sur votre Pi en exécutant la commande suivante :

```bash
gulp install-tools
```

Il peut prendre 10 minutes pour terminer lors de la première exécution de cette tâche.

### <a name="1382-deploy-and-run-the-sample-app"></a>1.3.8.2 déployer et exécuter l’exemple d’application

Déployer et exécuter l’exemple d’application sur en exécutant la commande suivante :

```bash
gulp deploy && gulp run
```

### <a name="1383-verify-the-app-works"></a>1.3.8.3 Vérifiez que l’application fonctionne

Vous devez maintenant voir le voyant sur votre Pi clignote toutes les deux secondes.  Si vous ne voyez pas le voyant clignotant, consultez le [guide de dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md) des solutions aux problèmes courants.
![VOYANT clignotant](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

> [AZURE.NOTE] Utilisez `Ctrl + C` pour mettre fin à l’application.

## <a name="139-summary"></a>1.3.9 synthèse de

Vous avez installé les outils nécessaires pour travailler avec votre Pi et déployer un exemple d’application à votre Pi faire clignoter le voyant. Vous pouvez désormais vous déplacer à la leçon suivante pour créer, déployer et exécuter un autre exemple d’application qui se connecte votre Pi au concentrateur de IoT Azure pour envoyer et recevoir des messages.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à commencer la leçon 2 qui commence avec [les outils Azure](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
