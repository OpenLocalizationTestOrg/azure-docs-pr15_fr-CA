<properties
    pageTitle="Envoyer des messages de nuage-dispositif avec IoT Hub | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à envoyer des messages de nuage vers le périphérique à l’aide d’Azure IoT Hub avec Java."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-java"></a>Didacticiel : Comment envoyer des messages de nuage-dispositif avec IoT Hub et Java

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduction

Concentrateur de IoT Azure est un service entièrement géré qui vous aide à activer fiables et sécurisées des communications bidirectionnelles entre millions d’appareils de IoT et mettre fin à une demande de retour. Le didacticiel [mise en route de concentrateur de IoT] montre comment créer un concentrateur IoT, configurer une identité de périphérique qu’il contient et un périphérique simulé qui envoie des messages de périphérique-nuage de code.

Ce didacticiel s’appuie sur la [mise en route de IoT concentrateur]. Il vous montre comment procéder pour :

- À partir de votre application cloud back-end, envoyer des messages de nuage-appareil à un seul périphérique par IoT concentrateur.
- Recevoir des messages du nuage vers le périphérique sur un périphérique.
- À partir de votre nuage application back-end, demander un accusé de réception (*commentaires*) pour les messages envoyés à un périphérique IoT concentrateur.

Vous trouverez plus d’informations sur les messages du nuage vers le périphérique dans le [Guide de développeur de concentrateur IoT][IoT Hub Developer Guide - C2D].

À la fin de ce didacticiel, vous exécutez deux applications de console Java :

* **périphérique simulé**, une version modifiée de l’application créée dans [mise en route de IoT concentrateur], qui se connecte à votre concentrateur IoT et reçoit les messages du nuage vers le périphérique.
* **envoi de messages de c2d**, qui envoie un message de nuage vers le périphérique à périphérique simulé par IoT concentrateur, puis reçoit son accusé de réception.

> [AZURE.NOTE] IoT concentrateur a la prise en charge du Kit de développement logiciel pour de nombreuses plates-formes de périphérique et les langues (y compris C, Java et Javascript) par le biais de kits SDK de périphériques Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre périphérique pour les code de ce didacticiel et généralement Azure IoT Hub, consultez le [Centre pour développeurs IoT Azure].

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Java SE 8. <br/> [Préparation de votre environnement de développement] [ lnk-dev-setup] décrit comment installer Java pour ce didacticiel sous Windows ou Linux.

+ Maven 3.  <br/> [Préparation de votre environnement de développement] [ lnk-dev-setup] décrit comment installer Maven pour ce didacticiel sous Windows ou Linux.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

## <a name="receive-messages-on-the-simulated-device"></a>Recevoir des messages sur le périphérique simulé

Dans cette section, vous modifiez l’application simulée périphérique créé à la [mise en route de IoT concentrateur] pour recevoir des messages de nuage vers le périphérique du concentrateur IoT.

1. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

2. Ajoutez la classe **MessageCallback** suivante sous la forme d’une classe imbriquée à l’intérieur de la classe **App** . La méthode **execute** est appelée lorsque le périphérique reçoit un message IoT concentrateur. Dans cet exemple, le périphérique signale toujours le concentrateur qu’il a terminé le message :

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modifiez la méthode **main** pour créer une instance de **MessageCallback** et appelez la méthode **setMessageCallback** avant d’ouvrir le client comme suit :

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Si vous utilisez HTTP au lieu de MQTT ou AMQP comme moyen de transport, l’instance **DeviceClient** vérifie les messages à partir de concentrateur IoT rarement (moins de 25 minutes). Pour plus d’informations sur les différences entre la prise en charge MQTT, AMQP et HTTP et IoT Hub la limitation, voir le [Guide de développeur de concentrateur IoT][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Envoyer un message de nuage-appareil

Dans cette section, vous créez une application console Java qui envoie des messages de nuage-dispositif pour l’application du périphérique simulé. Vous avez besoin de l’Id du périphérique que vous avez ajouté dans le didacticiel [mise en route de IoT Hub] du périphérique. Vous devez également la chaîne de connexion pour votre concentrateur IoT que vous trouverez dans le [portail Azure].

1. Créez un projet Maven appelé **c2d-envoyer des messages** à l’aide de la commande suivante à l’invite de commande. Remarque Il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Suivante à l’invite de commandes, accédez au nouveau dossier c2d-envoyer des messages.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier de l’envoi de messages de c2d et ajouter les dépendances suivantes pour le nœud **dépendances** . Ajout de la dépendance vous permet d’utiliser le package **iothub-java-service-client** dans votre application pour communiquer avec votre service de concentrateur IoT :

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions **d’importation** suivantes au fichier :

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Ajoutez les variables de niveau classe suivants à la classe **App** , remplaçant **{yourhubconnectionstring}** et **{yourdeviceid}** par les valeurs votre mentionnés plus haut :

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQP;
    ```
    
8. Remplacez la méthode **principale** par le code suivant qui se connecte à votre concentrateur IoT, envoie un message à votre appareil, puis attend un accusé de réception que le périphérique a reçu et traité le message :

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (comme intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes].

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À une invite de commande dans le dossier du périphérique simulé, exécutez la commande suivante pour commencer l’envoi de télémétrie pour votre concentrateur IoT et pour écouter les messages de nuage-DISPOSITIF envoyés votre concentrateur :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Exécutez l’application de périphérique simulé][img-simulated-device]

2. À une invite de commande dans le dossier de l’envoi de messages de c2d, exécutez la commande suivante pour envoyer un message de nuage-dispositif et attendre un accusé de réception des commentaires :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécutez la commande Envoyer le message de nuage-appareil][img-send-command]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment envoyer et recevoir des messages du nuage vers le périphérique. 

Pour voir des exemples de solutions de bout en bout complètes qui utilisent IoT Hub, consultez [Azure IoT Suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT concentrateur].


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Mise en route de concentrateur de IoT]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guide du développeur IoT concentrateur]: iot-hub-devguide.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure portal]: https://portal.azure.com
[IoT Azure Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/