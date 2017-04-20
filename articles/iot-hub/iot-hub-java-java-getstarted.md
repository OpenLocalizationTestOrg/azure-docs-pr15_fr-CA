<properties
    pageTitle="Concentrateur de IoT Azure pour Java mise en route | Microsoft Azure"
    description="Azure concentrateur IoT avec Java mise en route didacticiel. Utilisez Azure IoT Hub et Java avec les kits de développement logiciel de Microsoft Azure IoT d’implémenter une solution de l’Internet des objets."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-java"></a>Mise en route de concentrateur de IoT Azure pour Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous avez trois applications de console Java :

* **identité de périphérique de créer**, ce qui crée une identité de périphérique et d’une clé de sécurité associées pour connecter votre périphérique simulé.
* **messages lus-d2c**, qui affiche la télémétrie envoyé par votre périphérique simulé.
* **périphérique simulé**, qui se connecte à votre concentrateur IoT avec l’identité du périphérique créée précédemment et envoie un message de télémétrie chaque seconde à l’aide du protocole AMQP.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les kits de développement différents que vous pouvez utiliser pour générer les deux applications de s’exécuter sur les périphériques et le back-end votre solution.

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Java SE 8. <br/> [Préparation de votre environnement de développement] [ lnk-dev-setup] décrit comment installer Java pour ce didacticiel sous Windows ou Linux.

+ Maven 3.  <br/> [Préparation de votre environnement de développement] [ lnk-dev-setup] décrit comment installer Maven pour ce didacticiel sous Windows ou Linux.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

En tant que dernière étape, prenez note de la valeur de **clé primaire** , puis cliquez sur **messagerie**. Sur la lame de **messagerie** , prenez note du **nom d’événement compatible avec le Hub** et **point de terminaison compatible avec concentrateur événement**. Vous avez besoin de ces trois valeurs lorsque vous créez votre application de **lecture-d2c-messages** .

![Lame de concentrateur de messagerie IoT portail Azure][6]

Vous venez de créer votre concentrateur IoT, et vous l’IoT concentrateur hostname, chaîne de connexion IoT concentrateur, clé primaire du concentrateur IoT, nom du concentrateur d’événements compatibles et point de terminaison compatible avec concentrateur événement que vous devez suivre ce didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité de périphérique

Dans cette section, vous créez une application console Java qui crée une nouvelle identité de périphérique dans le Registre de l’identité de votre concentrateur IoT. Un périphérique ne peut pas se connecter IoT concentrateur, à moins qu’une entrée dans le Registre d’identité de périphérique. Reportez-vous à la section du **Registre d’identité de périphérique** du [Guide de développeur de concentrateur IoT] [ lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, il génère un ID de périphérique unique et la clé de votre périphérique peut utiliser pour s’identifier lorsqu’il envoie des messages de périphérique-nuage à IoT concentrateur.

1. Créez un dossier vide appelé iot java-Démarrer. Dans le dossier iot java-Démarrer, créez un nouveau projet Maven appelé **identité de périphérique de créer** à l’aide de la commande suivante à l’invite de commande. Remarque Il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, naviguez vers le nouveau dossier d’identité de périphérique de créer.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier d’identité de périphérique de créer et ajouter les dépendances suivantes pour le nœud **dépendances** . Cela vous permet d’utiliser le package iothub-service-sdk dans votre application :

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
    
4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions **d’importation** suivantes au fichier :

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Ajoutez les variables de niveau classe suivants à la classe **App** , remplaçant **{yourhubconnectionstring}** par la valeur votre mentionnés plus haut :

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. Modifiez la signature de la méthode **main** pour inclure les exceptions comme suit :

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Ajoutez le code suivant dans le corps de la méthode **main** . Ce code crée une unité appelée *javadevice* dans le Registre d’identité IoT concentrateur si n’existe pas déjà. Il affiche ensuite l’id de périphérique et de la clé dont vous avez besoin plus tard :

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Enregistrez et fermez le fichier App.java.

11. Pour générer l’application **d’identité de périphérique de créer** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier d’identité de périphérique de créer :

    ```
    mvn clean package -DskipTests
    ```

12. Pour exécuter l’application **d’identité de périphérique de créer** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier d’identité de périphérique de créer :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Prenez note de **l’id de périphérique** et de la **clé de périphérique**. Vous aurez besoin ultérieurement lorsque vous créez une application qui se connecte à un concentrateur IoT en tant que périphérique.

> [AZURE.NOTE] Le Registre d’identité IoT concentrateur ne stocke que les identités des appareils pour permettre un accès sécurisé au concentrateur. Il stocke les ID de périphérique et les clés à utiliser comme informations d’identification de sécurité et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un périphérique. Si votre application a besoin stocker d’autres métadonnées spécifiques au périphérique, il doit utiliser une banque spécifique à l’application. Reportez-vous au [Guide de développeur de concentrateur IoT] [ lnk-devguide-identity] pour plus d’informations.

## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages de périphérique-nuage

Dans cette section, vous créez une application console Java qui lit les messages de périphérique-nuage IoT concentrateur. Un concentrateur IoT expose un [Concentrateur d’événements][lnk-event-hubs-overview]-le point de terminaison compatible pour vous permettre de lire des messages de périphérique-nuage. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui n’est pas approprié pour un déploiement à un débit élevé. Le [traitement des messages de périphérique-nuage] [ lnk-process-d2c-tutorial] didacticiel vous montre comment traiter les messages de périphérique-nuage à grande échelle. La [Mise en route avec les concentrateurs d’événement] [ lnk-eventhubs-tutorial] didacticiel fournit des informations plus sur la manière de traiter les messages de concentrateurs d’événement et n’est applicable aux points de terminaison IoT concentrateur événement compatible avec le concentrateur.

> [AZURE.NOTE] Le point de terminaison compatible avec concentrateur événement pour lire des messages de périphérique-nuage toujours utilise le protocole AMQP.

1. Dans le dossier iot java-démarrer que vous avez créé dans la section *Création d’une identité de périphérique* , créer un nouveau projet Maven appelé **d2c-lire les messages** à l’aide de la commande suivante à l’invite de commande. Remarque Il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Suivante à l’invite de commandes, naviguez vers le nouveau dossier en lecture-d2c-messages.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier en lecture-d2c-messages et ajouter les dépendances suivantes pour le nœud **dépendances** . Cela vous permet d’utiliser le package eventhubs-client dans votre application pour lire à partir du point de terminaison du concentrateur d’événements compatibles :

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions **d’importation** suivantes au fichier :

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Ajoutez les variables de niveau classe suivants à la classe **App** . Remplacez **{youriothubkey}**, **{youreventhubcompatibleendpoint}**et **{youreventhubcompatiblename}** les valeurs que vous avez notée précédemment :

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Ajoutez la méthode **receiveMessages** suivante à la classe **App** . Cette méthode crée une instance de **EventHubClient** pour se connecter au point de terminaison compatible avec concentrateur événement et asynchrone crée ensuite une instance de **PartitionReceiver** pour lire à partir d’une partition de concentrateur de l’événement. Il en continu et imprime les détails du message jusqu'à ce que l’application se termine.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] Cette méthode utilise un filtre lorsqu’il crée le récepteur pour que le récepteur ne lit que les messages envoyés au Hub de IoT après le démarrage du récepteur. Cela est utile dans un environnement de test afin de voir l’ensemble actuel des messages. Dans un environnement de production Assurez-vous votre code qu’elle traite tous les messages, voir [comment traiter les messages de périphérique-nuage IoT concentrateur] [ lnk-process-d2c-tutorial] didacticiel pour plus d’informations.

9. Modifiez la signature de la méthode **main** pour inclure l’exception comme suit :

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Ajoutez le code suivant à la méthode **principale** dans la classe **App** . Ce code crée deux instances de **EventHubClient** et de **PartitionReceiver** et vous permet de fermer l’application lorsque vous avez terminé le traitement des messages :

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] Ce code suppose que vous avez créé votre concentrateur IoT dans la couche de F1 (gratuite). Un concentrateur IoT libre a deux partitions nommées « 0 » et « 1 ».

11. Enregistrez et fermez le fichier App.java.

12. Pour générer l’application **d2c-lire les messages** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier en lecture-d2c-messages :

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Java qui simule un périphérique qui envoie des messages de périphérique-nuage à un concentrateur IoT.

1. Dans le dossier iot java-démarrer que vous avez créé dans la section *Création d’une identité de périphérique* , créer un nouveau projet Maven appelé **périphérique simulé** à l’aide de la commande suivante à l’invite de commande. Remarque Il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Suivante à l’invite de commandes, naviguez vers le nouveau dossier du périphérique simulé.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier du périphérique simulé et ajouter les dépendances suivantes pour le nœud **dépendances** . Cela vous permet d’utiliser le package iothub-java-client dans votre application pour communiquer avec votre concentrateur IoT et pour sérialiser des objets Java à JSON :

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions **d’importation** suivantes au fichier :

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Ajoutez les variables de niveau classe suivants à la classe **App** , remplaçant **{youriothubname}** par votre nom de concentrateur IoT, **{yourdevicekey}** , avec la valeur de clé de périphérique générées dans la section *Création d’une identité de périphérique* :

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQP;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    Cet exemple d’application utilise la **protocole** variable lorsqu’il instancie un objet **DeviceClient** . Vous pouvez utiliser le protocole HTTP ou AMQP pour communiquer avec IoT concentrateur.

8. Ajouter comme imbriqué classe **TelemetryDataPoint** à l’intérieur de la classe de **l’application** pour spécifier les données de télémétrie que votre périphérique envoie à votre concentrateur IoT :

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Ajoutez la classe suivante de **EventCallback** imbriquée à l’intérieur de la classe de **l’application** pour afficher l’état de l’accusé de réception que le concentrateur IoT renvoie lorsqu’il traite un message à partir du périphérique simulé. Cette méthode avertit également le thread principal de l’application lorsque le message a été traité :

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Ajoutez la classe suivante de **MessageSender** imbriquée à l’intérieur de la classe **App** . La méthode à **exécuter** dans cette classe génère des exemples de données de télémétrie pour envoyer à votre concentrateur IoT et attend un accusé de réception avant d’envoyer le message suivant :

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Cette méthode envoie un nouveau message de périphérique-nuage une seconde après que le concentrateur IoT accuse réception du message précédent. Le message contient un objet JSON sérialisé avec l’ID de périphérique et un nombre généré de manière aléatoire pour simuler un capteur de vitesse du vent.

11. Remplacez la méthode **principale** par le code suivant, qui crée un thread pour envoyer des messages de périphérique-nuage à votre concentrateur IoT :

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. Enregistrez et fermez le fichier App.java.

13. Pour générer l’application de **périphérique simulé** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier du périphérique simulé :

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoire de gestion des pannes][lnk-transient-faults].

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À une invite de commande dans le dossier en lecture-d2c, exécutez la commande suivante pour commencer l’analyse de la première partition dans votre concentrateur IoT :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Application cliente de service Java IoT Hub pour surveiller des messages de périphérique-nuage][7]

2. À une invite de commande dans le dossier du périphérique simulé, exécutez la commande suivante pour commencer l’envoi des données de télémétrie pour votre concentrateur IoT :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Application de client de périphérique Java IoT Hub pour envoyer des messages de périphérique-nuage][8]

3. La mosaïque de **l’utilisation** du [portail Azure] [ lnk-portal] indique le nombre de messages envoyés au hub :

    ![Azure portail utilisation mosaïque montrant le nombre de messages envoyés au Hub de IoT][43]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous configuré un nouveau concentrateur IoT dans Azure portal et ensuite créé une identité de périphérique dans le Registre d’identité du concentrateur. Cette identité de périphérique vous permet d’activer l’application de périphérique simulé envoyer des messages de périphérique-nuage au concentrateur. Vous avez également créé une application qui affiche les messages reçus par le concentrateur. 

Pour continuer la mise en route avec IoT Hub et d’étudier les autres scénarios IoT voir :

- [Connectez votre périphérique][lnk-connect-device]
- [Mise en route de la gestion des périphériques][lnk-device-management]
- [Mise en route avec le Kit de développement de passerelle IoT][lnk-gateway-SDK]

Pour savoir comment étendre votre solution IoT et de traiter des messages de périphérique-nuage à grande échelle, consultez [traitement des messages de périphérique-nuage] [ lnk-process-d2c-tutorial] didacticiel.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/