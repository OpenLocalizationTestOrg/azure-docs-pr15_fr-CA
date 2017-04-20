<properties
 pageTitle="Lire les messages rendues persistantes dans le stockage Azure | Microsoft Azure"
 description="Surveiller les messages de périphérique-nuage qu’ils sont écrits dans le stockage par table Azure."
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

# <a name="33-read-messages-persisted-in-azure-storage"></a>3.3 messages lus rendues persistantes dans le stockage Azure

## <a name="331-what-will-you-do"></a>3.3.1. que souhaitez-vous faire

Surveiller les messages de périphérique-nuage qui sont envoyés à partir de votre framboises Pi 3 à votre concentrateur IoT que les messages sont écrits dans le stockage par table Azure. Si vous répondez à tous les problèmes, rechercher des solutions dans la [page Dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="332-what-will-you-learn"></a>3.3.2 vous apprendrez

- Comment faire pour utiliser la tâche de lecture du message de choses pour lire les messages rendues persistantes dans le stockage par table Azure.

## <a name="333-what-do-you-need"></a>3.3.3 que vous faut-il

- Vous devez avoir terminé la section précédente, [exécutez l’exemple d’application Azure clignotant sur votre framboises Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md).

## <a name="334-read-new-messages-from-your-storage-account"></a>3.3.4 lire les nouveaux messages de votre compte de stockage

Dans la section précédente, vous avez exécuté un exemple d’application sur votre Pi. Les exemples de messages application envoyée à votre concentrateur IoT d’Azure. Les messages envoyés à votre concentrateur IoT sont stockés dans le stockage par table Azure via l’application de la fonction Azure. Vous avez besoin de la chaîne de connexion de stockage Azure pour lire les messages à partir de votre stockage par table Azure.

Pour lire les messages stockés dans le stockage par table Azure, procédez comme suit :

1. Obtenir la chaîne de connexion en exécutant les commandes suivantes :

    ```bash
    az storage account list -g iot-sample --query [].name
    az storage account show-connection-string -g iot-sample -n {storage name}
    ```

    La première commande récupère la `storage name` qui est utilisé dans la deuxième commande pour obtenir la chaîne de connexion. `iot-sample`est la valeur par défaut de `{resource group name}` si vous n’avez pas modifier la valeur dans la leçon 2.

2. Ouvrez le fichier de configuration `config-raspberrypi.json` fichier de Code de Visual Studio en exécutant la commande suivante :

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json

    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

3. Remplacer `[Azure Storage connection string]` avec la chaîne de connexion que vous avez obtenu à l’étape 1.
4. Enregistrer le `config-raspberrypi.json` fichier.
5. Envoyer des messages à nouveau et les lire à partir de votre stockage par table Azure en exécutant la commande suivante :

    ```bash
    gulp run --read-storage
    ```

    La logique de lecture à partir du stockage par table Azure se trouve dans le `azure-table.js` fichier.

    ![choses exécution--stockage de lecture](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message.png)

## <a name="335-summary"></a>3.3.5 résumé de

Vous avez correctement connecté votre Pi à votre concentrateur IoT dans le nuage et l’exemple d’application de clignotement permet d’envoyer des messages de périphérique-nuage. L’application de la fonction Azure permet également de stocker les messages entrants de concentrateur IoT à votre stockage par table Azure. Vous pouvez atteindre la prochaine leçon comment envoyer des messages de nuage-DISPOSITIF votre concentrateur IoT votre Pi.

## <a name="next-steps"></a>Étapes suivantes

[Leçon 4 : Envoyer des messages de nuage-appareil](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)
