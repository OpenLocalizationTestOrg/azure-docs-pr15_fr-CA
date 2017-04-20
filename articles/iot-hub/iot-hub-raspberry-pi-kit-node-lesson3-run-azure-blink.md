<properties
 pageTitle="Exécutez l’exemple d’application pour envoyer des messages de périphérique-nuage | Microsoft Azure"
 description="Déployer et exécuter un exemple d’application à votre framboises Pi 3 qui envoie des messages au hub de IoT et se met à clignoter le voyant."
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

# <a name="32-run-sample-application-to-send-device-to-cloud-messages"></a>3.2 exécuter l’exemple d’application pour envoyer des messages de périphérique-nuage

## <a name="321-what-you-will-do"></a>3.2.1 ce que vous ferez

Déployer et exécuter un exemple d’application sur votre framboises Pi 3 qui envoie des messages à votre concentrateur IoT. Si vous répondez à tous les problèmes, rechercher des solutions dans la [page Dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="322-what-you-will-learn"></a>3.2.2 vous apprendrez

- Comment utiliser l’outil de choses pour déployer et exécuter l’exemple d’application Node.js sur votre Pi.

## <a name="323-what-you-need"></a>3.2.3 ce dont vous avez besoin

- Vous devez avoir correctement terminé la section précédente dans cette leçon : [créer une application de fonction Azure et un compte de stockage Azure pour traiter et stocker les messages de concentrateur IoT](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).

## <a name="324-get-your-iot-hub-and-device-connection-strings"></a>3.2.4 obtenir vos chaînes de connexion de concentrateur et dispositif IoT

La chaîne de connexion de périphérique permet de connecter la Pi à votre concentrateur IoT. Chaîne de connexion pour le concentrateur IoT permet de connecter votre concentrateur IoT à l’identité du périphérique qui représente votre Pi dans le concentrateur IoT.

- Obtenir la chaîne de connexion du concentrateur IoT en exécutant la commande CLI d’Azure suivante :

```bash
az iot hub show-connection-string --name {my hub name} --resource-group iot-sample
```

`{my hub name}`est le nom que vous avez spécifié dans la leçon 2. Utilisez `iot-sample` comme valeur de `{resource group name}` si vous n’avez pas modifier la valeur dans la leçon 2.

- Obtenir la chaîne de connexion du périphérique en exécutant la commande suivante :

```bash
az iot device show-connection-string --hub {my hub name} --device-id myraspberrypi --resource-group iot-sample
```

`{my hub name}`prend la même valeur que celle utilisée avec la commande précédente. Utilisez `iot-sample` comme valeur de `{resource group name}` et `myraspberrypi` comme valeur de `{device id}` si vous n’avez pas modifier la valeur dans la leçon 2.

## <a name="325-configure-the-device-connection"></a>3.2.5 configurer la connexion du périphérique

1. Initialiser le fichier de configuration en exécutant les commandes suivantes :

    ```bash
    npm install
    gulp init
    ```

2. Ouvrez le fichier de configuration de périphérique `config-raspberrypi.json` dans le Code de Visual Studio en exécutant la commande suivante :

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
  
    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

    ![config.JSON](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)

3. Vérifiez les remplacements suivants dans le `config-raspberrypi.json` fichier :

  - Remplacez **[nom de périphérique d’hôte ou adresse IP]** avec l’adresse IP du périphérique ou le nom d’hôte que vous avez obtenu à partir de `device-discovery-cli` ou la valeur héritée de ce que vous avez configuré dans la leçon 1.
  - Remplacez **[chaîne de connexion de périphérique IoT]** avec les `device connection string` vous obtenu.
  - Remplacez **[chaîne de connexion d’un concentrateur de IoT]** avec les `iot hub connection string` vous obtenu.

Vous mettez à jour la `config-raspberrypi.json` fichier de sorte que vous pouvez déployer l’exemple d’application à partir de votre ordinateur.

## <a name="326-deploy-and-run-the-sample-application"></a>3.2.6 déployer et exécuter l’exemple d’application

Déployer et exécuter l’exemple d’application sur votre Pi en exécutant la commande suivante :

```bash
gulp
```

> [AZURE.NOTE] Exécution de la tâche de choses par défaut `install-tools`, `deploy`, et `run` tâches séquentiellement. Dans la [leçon 1](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md), vous avez exécuté ces tâches séparément un après l’autre.

## <a name="327-verify-the-sample-application-works"></a>3.2.7 Vérifiez que l’application d’exemple fonctionne

Vous devez voir le voyant qui est connecté à votre Pi clignote toutes les deux secondes. Chaque fois que le voyant clignote, l’exemple d’application envoie un message à votre concentrateur IoT et vérifie que le message a été envoyé à votre concentrateur IoT. En outre, pour chaque message reçu par le concentrateur IoT, le message est imprimé dans la fenêtre de console. L’exemple d’application se termine automatiquement après l’envoi de messages de 20.

![](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run.png)

## <a name="328-summary"></a>3.2.8 résumé

Vous avez déployé et exécuter l’exemple d’application clignotement du nouveau sur votre Pi pour envoyer des messages de périphérique-nuage à votre concentrateur IoT. Vous pouvez atteindre la section suivante pour contrôler les messages qu’ils sont écrits dans le compte de stockage Azure.

## <a name="next-steps"></a>Étapes suivantes

[3.3 messages lus rendues persistantes dans le stockage Azure](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)