<properties
 pageTitle="Option section - Modifier la dans et hors de comportement de la DEL | Microsoft Azure"
 description="Personnaliser les messages pour modifier le voyant et désactive le comportement."
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

# <a name="42-optional-section-change-the-on-and-off-behavior-of-the-led"></a>4.2 section facultative : modifier la dans et hors de comportement du voyant

## <a name="421-what-you-will-do"></a>4.2.1 ce que vous ferez

Personnaliser les messages pour modifier le voyant et désactive le comportement. Si vous répondez à tous les problèmes, rechercher des solutions dans la [page Dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="422-what-you-will-learn"></a>4.2.2 vous apprendrez

Des fonctions supplémentaires Node.js permet de modifier le voyant et désactive le comportement.

## <a name="423-what-you-need"></a>4.2.3 ce dont vous avez besoin

Vous devez avoir correctement terminé [4.1 exécuter un exemple d’application sur votre Pi framboises pour recevoir le nuage à des messages de périphérique](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

## <a name="424-add-nodejs-functions"></a>4.2.4 ajouter des fonctions de Node.js

1. Ouvrir l’exemple d’application dans le code de Visual Studio en exécutant les commandes suivantes :

    ```bash
    cd Lesson4
    code .
    ```

2. Ouvrir le `app.js` de fichier et ajoutez les fonctions suivantes à la fin :

    ```javascript
    function turnOnLED() {
      wpi.digitalWrite(CONFIG_PIN, 1);
    }

    function turnOffLED() {
      wpi.digitalWrite(CONFIG_PIN, 0);
    }
    ```

    ![mise à jour app.js](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)

3. Ajouter les conditions suivantes avant par défaut dans le bloc switch case de la `receiveMessageCallback` fonction :

    ```javascript
    case 'on':
      turnOnLED();
      break;
    case 'off':
      turnOffLED();
      break;
    ```

    Maintenant, vous avez configuré l’exemple d’application pour répondre à plusieurs instructions par le biais de messages. L’instruction « on » Active le voyant et l’instruction « off » désactive le voyant.

4. Ouvrez le fichier gulpfile.js et ajoutez une nouvelle fonction avant la fonction `sendMessage`:

    ```javascript
    var buildCustomMessage = function (messageId) {
      if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
        return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
      } else if (messageId < MAX_MESSAGE_COUNT) {
        return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
      } else {
        return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
      }
    }
    ```

    ![mise à jour gulpfile](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)

5. Dans le `sendMessage` fonction, remplacez la ligne `var message = buildMessage(sentMessageCount);` avec la nouvelle ligne illustrée dans l’extrait suivant :

    ```javascript
    var message = buildCustomMessage(sentMessageCount);
    ```

6. Enregistrer toutes les modifications.

### <a name="425-deploy-and-run-the-sample-application"></a>4.2.5 déployer et exécuter l’exemple d’application

Déployer et exécuter l’exemple d’application sur votre Pi en exécutant la commande suivante :

```bash
gulp
```

Vous devez voir le voyant allumé pendant deux secondes et puis désactivé pour un autre deux secondes. Le dernier message « stop » cesse de l’exemple d’application de s’exécuter.

![sous et hors tension](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

Félicitations ! Avec succès, vous avez personnalisé les messages qui sont envoyés à la Pi votre concentrateur IoT.

### <a name="427-summary"></a>4.2.7 Résumé

Cette section explique comment personnaliser les messages qui l’exemple d’application permet de contrôler la dans et hors de comportement du voyant d’une manière différente.

