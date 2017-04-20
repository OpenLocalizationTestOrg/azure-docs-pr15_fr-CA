<properties
 pageTitle="Exécutez l’exemple d’application de recevoir des messages de nuage-DISPOSITIF | Microsoft Azure"
 description="L’exemple d’application dans la leçon 4 s’exécute sur votre Pi et surveille les messages entrants à partir de votre concentrateur IoT. Une nouvelle tâche de choses envoie des messages à votre Pi votre concentrateur IoT faire clignoter le voyant."
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

# <a name="41-run-the-sample-application-to-receive-cloud-to-device-messages"></a>4.1 exécuter l’exemple d’application de recevoir des messages du nuage vers le périphérique

Dans cette section, vous déployez un exemple d’application sur votre framboises Pi 3. L’exemple d’application surveille les messages entrants à partir de votre concentrateur IoT. Vous pouvez également exécuter une tâche de choses sur votre ordinateur pour envoyer des messages à votre Pi votre concentrateur IoT. Lors de la réception des messages, l’exemple d’application se met à clignoter le voyant. Si vous répondez à tous les problèmes, rechercher des solutions dans la [page Dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="411-what-you-will-do"></a>4.1.1 ce que vous ferez

- Se connecter à votre concentrateur IoT l’exemple d’application.
- Déployer et exécuter l’exemple d’application.
- Envoyer des messages à partir de votre concentrateur IoT à votre Pi faire clignoter le voyant.

## <a name="412-what-you-will-learn"></a>4.1.2 vous apprendrez

- Comment faire pour analyser les messages entrants à partir de votre concentrateur IoT.
- Comment envoyer des messages de nuage-DISPOSITIF votre concentrateur IoT votre Pi. 

## <a name="413-what-do-you-need"></a>4.1.3 que vous faut-il

- Un Pi de framboises 3 qui est configuré pour utiliser. Pour savoir comment configurer votre Pi, consultez [leçon 1 : mise en route de votre périphérique de framboises Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md)
- Un concentrateur IoT est créé dans votre abonnement Azure. Pour savoir comment créer votre concentrateur de IoT Azure, consultez [leçon 2 : créer votre concentrateur de IoT Azure](iot-hub-raspberry-pi-kit-node-get-started.md)

## <a name="414-connect-the-sample-application-to-your-iot-hub"></a>4.1.4 se connecter à l’exemple d’application à votre concentrateur IoT

1. Assurez-vous que vous êtes dans le dossier mis en pension `iot-hub-node-raspberrypi-getting-started`. Ouvrir l’exemple d’application dans le Code de Visual Studio en exécutant les commandes suivantes :

    ```bash
    cd Lesson4
    code .
    ```

    Avis de la `app.js` de fichiers dans le `app` sous-dossier. Le `app.js` fichier est le fichier source de clé qui contient le code pour analyser les messages entrants IoT concentrateur. Le `blinkLED` fonction se met à clignoter le voyant.

    ![Structure de mis en pension](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)

2. Initialiser le fichier de configuration avec les commandes suivantes :

    ```bash
    npm install
    gulp init
    ```

    Si vous avez terminé la leçon 3 sur cet ordinateur, toutes les configurations sont héritées, vous pouvez donc passer à l’étape 4.1.5. Leçon 3 fois sur un autre ordinateur, vous devez remplacer les espaces réservés dans les `config-raspberrypi.json` fichier. Le `config-raspberrypi.json` fichier se trouve dans le sous-dossier de votre dossier de base.

    ![Config](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

- Remplacez **[nom de périphérique d’hôte ou adresse IP]** avec l’adresse IP ou le nom d’hôte que vous obtenez en exécutant la commande de votre Pi`devdisco list --eth`
- Remplacez **[chaîne de connexion de périphérique IoT]** avec la chaîne de connexion de périphérique que vous obtenez en exécutant la commande `az iot hub show-connection-string --name {my hub name} --resource-group {resource group name}`.
- Remplacez **[chaîne de connexion d’un concentrateur de IoT]** avec la chaîne de connexion de concentrateur IoT que vous obtenez en exécutant la commande `az iot device show-connection-string --hub {my hub name} --device-id {device id} --resource-group {resource group name}`.

## <a name="415-deploy-and-run-the-sample-application"></a>4.1.5 déployer et exécuter l’exemple d’application

Déployer et exécuter l’exemple d’application sur votre Pi en exécutant les commandes suivantes :
  
```
gulp
```

La commande de choses s’exécute la tâche d’installer les outils en premier. Puis il déploie l’exemple d’application à votre Pi. Enfin, il exécute l’application sur votre Pi et une tâche séparée sur votre ordinateur hôte pour envoyer des messages de clignotement 20 à votre Pi votre concentrateur IoT.

Une fois l’exemple d’application s’exécute, il commence à écouter les messages à partir de votre concentrateur IoT. Pendant ce temps, la choses tâche envoie plusieurs messages de « clignoter » votre concentrateur IoT à votre Pi. Pour chaque valeur du clignotement du message reçu, l’exemple d’application appelle la fonction blinkLED pour clignoter le voyant.

Vous devez voir le voyant clignote toutes les deux secondes que la tâche de choses envoie des messages de 20 votre concentrateur IoT à votre Pi. La dernière est un message « stop » qui indique à l’application à arrêter l’exécution.

![Choses](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="416-summary"></a>4.1.6 résumé

Vous avez correctement envoyé des messages votre concentrateur IoT à votre Pi faire clignoter le voyant. Section suivante est une section facultative qui vous montre comment modifier la dans et hors de comportement du voyant.

## <a name="next-steps"></a>Étapes suivantes

[Section facultative : modifier la dans et hors de comportement du voyant](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)