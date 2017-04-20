<properties
    pageTitle="Traiter les messages de périphérique-nuage IoT Hub (Java) | Microsoft Azure"
    description="Suivez ce didacticiel Java pour en savoir plus de modèles utiles pour traiter les messages de périphérique-nuage IoT concentrateur."
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
     ms.date="09/01/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-java"></a>Didacticiel : Comment traiter les messages de périphérique-nuage IoT concentrateur à l’aide de Java

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introduction

Concentrateur de IoT Azure est un service entièrement géré qui permet fiable et sécurisée des communications bidirectionnelles entre millions d’appareils de IoT et une application back-end. Autres didacticiels ([mise en route de IoT Hub] et [d’Envoyer des messages de nuage-dispositif avec concentrateur de IoT][lnk-c2d]) montrent comment utiliser la fonctionnalité de messagerie périphérique-nuage et nuage-DISPOSITIF base de IoT concentrateur.

Ce didacticiel s’appuie sur le code indiqué dans le didacticiel [mise en route de IoT concentrateur] , et il montre deux modèles évolutifs que vous pouvez utiliser pour traiter les messages de périphérique-nuage :

- Le stockage fiable des messages de périphérique-nuage dans le [stockage blob Azure]. Un scénario courant est analytique de *chemin à froid* , dans lequel vous stockez les données de télémétrie de BLOB à utiliser comme entrée dans les processus analytique. Ces processus peuvent être pilotés par des outils tels que [l’Usine de données Azure] ou la pile [HDInsight (Hadoop)] .

- Le traitement des messages de périphérique-nuage *interactive* fiable. Messages de périphérique-nuage sont interactifs lorsqu’elles sont des déclencheurs immédiate pour un ensemble d’actions dans l’application principale. Par exemple, un périphérique peut envoyer un message d’alerte qui déclenche l’insertion d’un ticket dans un système CRM. En revanche, les messages de *point de données* alimentent simplement dans un moteur analytique. Par exemple, la télémétrie de température à partir d’un périphérique qui doit être stockées pour une analyse ultérieure est un message de point de données.

Étant donné que IoT concentrateur expose un [Concentrateur d’événements][lnk-event-hubs]-le point de terminaison compatible pour recevoir des messages de périphérique-nuage, ce didacticiel utilise une instance de [EventProcessorHost] . Cette instance :

* Stocke les messages de *point de données* fiable dans le stockage blob Azure.
* *Interactive* transfère des messages de périphérique-nuage à une [file d’attente de Bus de Service] d' Azure pour un traitement immédiat.

Bus de service permet d’assurer un traitement fiable des messages interactifs, car il fournit des points de contrôle par message et de la déduplication basée sur la fenêtre de temps.

> [AZURE.NOTE] Une instance de **EventProcessorHost** n'est qu’un seul moyen de traiter les messages interactifs. D’autres options incluent [Azure Fabric de Service] [ lnk-service-fabric] et [Azure flux Analytique][lnk-stream-analytics].

À la fin de ce didacticiel, vous exécutez trois applications de console Java :

* **périphérique simulé**, une version modifiée de l’application créée dans le didacticiel [mise en route de IoT concentrateur] , envoie des messages de périphérique-nuage de point de données chaque seconde, interactive périphérique-nuage messages et toutes les 10 secondes. Cette application utilise le protocole de l’AMQP de communiquer avec IoT concentrateur.
* **processus-d2c-messages** utilise la classe [EventProcessorHost] pour récupérer des messages depuis le point de terminaison du concentrateur d’événements compatibles. Il fiable stocke les messages de point de données dans le stockage blob Azure, puis transfère les messages interactifs à une file d’attente de Bus de Service.
* **processus interactif messages** des files d’attente de messages interactifs à partir de la file d’attente de Bus de Service.

> [AZURE.NOTE] IoT concentrateur a la prise en charge du Kit de développement logiciel pour de nombreuses plates-formes de périphérique et les langages, notamment C, Java et JavaScript. Pour obtenir des instructions sur la façon de remplacer le périphérique simulé dans ce didacticiel avec un périphérique physique et comment connecter des périphériques à un concentrateur IoT, consultez le [Centre pour développeurs IoT Azure].

Ce didacticiel est directement applicable aux autres modes de consommation de messages concentrateur d’événements compatibles, tels que les projets de [HDInsight (Hadoop)] . Pour plus d’informations, voir le [guide du développeur d’Azure IoT Hub - périphérique vers le cloud].

Pour terminer ce didacticiel, vous devez les éléments suivants :

+ Une version de travail complète du didacticiel [mise en route de IoT concentrateur] .

+ Java SE 8. <br/> [Préparation de votre environnement de développement] [ lnk-dev-setup] décrit comment installer Java pour ce didacticiel sous Windows ou Linux.

+ Maven 3.  <br/> [Préparation de votre environnement de développement] [ lnk-dev-setup] décrit comment installer Maven pour ce didacticiel sous Windows ou Linux.

+ Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

Vous devez disposer d’une connaissance de base du [Stockage Azure] et [d’Azure Service Bus].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Envoyer des messages interactives à partir d’un périphérique simulé

Dans cette section, vous modifiez l’application de périphérique simulé que vous avez créé dans le didacticiel [mise en route de IoT Hub] pour envoyer des messages de périphérique-nuage interactifs au concentrateur IoT.

1. Utilisez un éditeur de texte pour ouvrir le fichier simulated-device\src\main\java\com\mycompany\app\App.java. Ce fichier contient le code de l’application de **périphérique simulé** que vous avez créé dans le didacticiel [mise en route de concentrateur de IoT] .

2. Ajoutez la classe imbriquée suivante à la classe **App** :

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    Cette classe est semblable à la classe **MessageSender** dans le projet de **périphérique simulé** . Les seules différences sont que vous maintenant de définir la propriété **MessageId** du système, et une propriété personnalisée appelée **messageType**.
    Le code assigne un identificateur unique universel (UUID) à la propriété **MessageId** . Le Bus de Service permet de dédupliquer les messages qu’il reçoit cet identificateur. L’exemple utilise la propriété **messageType** pour distinguer interactive à partir de messages de point de données. L’application transmet ces informations dans les propriétés du message, et non dans le corps du message, afin que le processeur d’événements n’a pas besoin désérialiser le message pour effectuer le routage des messages.

    > [AZURE.NOTE] Il est important de créer le **MessageId** permet de dédupliquer les messages interactifs dans le code de périphérique. Communications réseau intermittente ou autres défaillances, peuvent entraîner des retransmissions plusieurs du même message à partir de ce périphérique. Vous pouvez également utiliser un ID de message de sémantique, par exemple un hachage des champs de données pertinents de message, à la place d’un UUID.

3. Modifiez la méthode **main** pour envoyer des messages à la fois interactifs et données de points de messages comme indiqué dans l’extrait de code suivant :

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. Enregistrez et fermez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

    > [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente pas n’importe quelle stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter une stratégie de nouvelles tentatives notamment backoff exponentielle, comme indiqué dans l’article MSDN [Transitoire de gestion des pannes].

5. Pour générer l’application de **périphérique simulé** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier du périphérique simulé :

    ```
    mvn clean package -DskipTests
    ```

## <a name="process-device-to-cloud-messages"></a>Traitez les messages périphérique-nuage

Dans cette section, vous créez une application console Java qui traite les messages de périphérique-nuage IoT concentrateur. Concentrateur de IOT expose un [concentrateur d’événements]-point de terminaison compatible pour activer une application lire des messages de périphérique-nuage. Ce didacticiel utilise la classe [EventProcessorHost] pour traiter ces messages dans une application console. Pour plus d’informations sur la façon de traiter les messages de concentrateurs d’événement, consultez le didacticiel [Mise en route de concentrateurs de l’événement] .

Le défi principal lorsque vous implémentez le stockage fiable de messages de point de données ou le transfert de messages interactifs, êtes que le traitement de l’événement s’appuie sur le consommateur de messages pour fournir des points de contrôle pour sa progression. En outre, pour atteindre un débit élevé, lorsque vous lisez à partir de l’événement concentrateurs vous devez fournir des points de contrôle par lots importants. Cette approche crée la possibilité d’un traitement en double pour un grand nombre de messages, si une panne se produit et vous revenez à un point de contrôle précédent. Dans ce didacticiel, vous allez apprendre à synchroniser les écritures sur le stockage Azure et windows de suppression des doublons de Bus de Service avec les points de contrôle **EventProcessorHost** .

Pour écrire des messages dans le stockage Azure, fiable, l’exemple utilise la fonctionnalité de validation de chaque bloc de [BLOB de bloc][Azure Block Blobs]. Le processeur d’événements accumule les messages en mémoire jusqu'à ce qu’il est temps de fournir un point de contrôle. Par exemple, après que le tampon cumulé de messages atteint la taille de bloc maximale de 4 Mo, ou le Bus des services de déduplication laps de temps s’écoule. Ensuite, avant le point de contrôle, le code valide un nouveau bloc au blob.

Le processeur d’événements utilise les concentrateurs d’événement offsets de message comme ID de bloc. Ce mécanisme permet d’effectuer une vérification de la déduplication avant elle valide le nouveau bloc de stockage, en prenant soin de blocage possible entre la validation d’un bloc et le point de contrôle, le processeur d’événements.

> [AZURE.NOTE] Ce didacticiel utilise un seul compte de stockage Azure pour écrire tous les messages récupérés IoT concentrateur. Pour décider si vous devez utiliser plusieurs comptes de stockage Azure dans votre solution, consultez [instructions une évolutivité du stockage Azure].

L’application utilise la fonction de déduplication de Bus des services afin d’éviter les doublons lorsqu’il traite les messages interactifs. Le périphérique simulé l’estampille interactive avec un unique **MessageId**. Cet id de Bus des services afin de garantir que, dans la fenêtre de temps spécifiée de déduplication, aucun deux messages avec le même **MessageId** ne sont remis aux récepteurs. Cette déduplication, ainsi que la sémantique d’achèvement par message fournie par les files d’attente de Bus de Service, rend facile à implémenter le traitement fiable des messages interactifs.

Pour vous assurer qu’aucun message n’est renvoyé à l’extérieur de la fenêtre de la déduplication, le code synchronise le mécanisme de point de contrôle de **EventProcessorHost** avec la fenêtre de la déduplication de file d’attente de Bus de Service. Cette synchronisation est effectuée en forçant un point de contrôle au moins une fois, chaque fois que la fenêtre de la déduplication s’écoule (dans ce didacticiel, la fenêtre est d’une heure).

> [AZURE.NOTE] Ce didacticiel utilise une file d’attente de Bus de Service partitionnée unique pour traiter tous les messages interactifs extraite IoT concentrateur. Pour plus d’informations sur l’utilisation des files d’attente de Bus de Service pour satisfaire les exigences d’évolutivité de votre solution, consultez la documentation de [Bus des services Azure] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Configurer un compte de stockage Azure et une file d’attente de Bus de Service

Pour utiliser la classe [EventProcessorHost] , vous devez disposer d’un compte de stockage Azure pour activer **EventProcessorHost** enregistrer les informations de point de contrôle. Vous pouvez utiliser un compte de stockage Azure existant, ou suivez les instructions [Sur le stockage Azure] pour créer un nouveau. Prenez note de la chaîne de connexion de compte de stockage Azure.

> [AZURE.NOTE] Lorsque vous copiez et collez la chaîne de connexion de compte de stockage Azure, assurez-vous qu’il n’y pas d’espaces inclus.

Vous devez également une file d’attente de Bus de Service pour permettre un traitement fiable des messages interactifs. Vous pouvez créer une file d’attente par programme, avec une fenêtre de déduplication d’une heure, comme expliqué dans [l’utilisation de files d’attente de Bus de Service de][file d’attente de Bus de Service]. Vous pouvez également utiliser le [Azure portal classique][lnk-classic-portal], procédez comme suit :

1. Cliquez sur **Nouveau** dans le coin inférieur gauche. Puis cliquez sur **Services d’App** > **Service Bus** > **file d’attente** > **Personnalisé créer**. Entrez le nom **d2ctutorial**, sélectionnez une zone et utilisez un espace de noms ou créer un nouveau. Prenez note de l’espace de noms, vous en avez besoin plus loin dans ce didacticiel. Sur la page suivante, sélectionnez **Activer la détection des doublons**et définir la **fenêtre de temps de l’historique de détection de doublons** pour une heure. Cliquez sur la case à cocher dans le coin inférieur droit pour enregistrer la configuration de votre file d’attente.

    ![Créer une file d’attente dans Azure portal][30]

2. Dans la liste des files d’attente de Bus de Service, cliquez sur **d2ctutorial**, puis cliquez sur **configurer**. Créer deux stratégies d’accès partagé, un appelé **Envoyer** avec autorisations **Envoyer** et un appelé **écouter** avec des autorisations **d’écouter** . Prenez note des valeurs de **clé primaire** pour les deux stratégies, vous avez besoin plus loin dans ce didacticiel. Lorsque vous avez terminé, cliquez sur **Enregistrer** dans la partie inférieure.

    ![Configurer une file d’attente dans Azure portal][31]

### <a name="create-the-event-processor"></a>Créer le processeur d’événements

Dans cette section, vous créez une application Java pour traiter les messages à partir de l’extrémité du concentrateur d’événements compatibles.

La première tâche consiste à ajouter un projet Maven appelé **d2c-traitement des messages** qui reçoit des messages de périphérique-nuage à partir du point de terminaison compatible avec concentrateur IoT concentrateur événement et achemine les messages vers d’autres services back-end.

1. Dans le dossier iot java-démarrer que vous avez créé dans le didacticiel [mise en route de IoT concentrateur] , créez un projet de Maven appelé **d2c-traitement des messages** à l’aide de la commande suivante à l’invite de commande. Remarque Il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Suivante à l’invite de commandes, accédez au nouveau dossier d2c-traitement des messages.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier d2c-traitement des messages et ajouter les dépendances suivantes pour le nœud **dépendances** . Ces dépendances permettent d’utiliser les azure-eventhubs, azure-eventhubs-eph et packages d’azure-servicebus dans votre application d’interagir avec votre IoT concentrateur et la file d’attente de Bus de Service :

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

La tâche suivante consiste à ajouter une classe **ErrorNotificationHandler** pour le projet.

1. Utilisez un éditeur de texte pour créer un fichier process-d2c-messages\src\main\java\com\mycompany\app\ErrorNotificationHandler.java. Ajoutez le code suivant dans le fichier pour afficher des messages d’erreur à partir de l’instance de **EventProcesssorHost** :

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. Enregistrez et fermez le fichier ErrorNotificationHandler.java.

Vous pouvez maintenant ajouter une classe qui implémente l’interface **IEventProcessor** . La classe **EventProcessorHost** appelle cette classe pour traiter les messages de périphérique-nuage reçus IoT concentrateur. Le code dans cette classe implémente la logique pour stocker des messages de manière fiable dans un conteneur de blob et transférer les messages à la file d’attente de Bus de Service interactifs.

La méthode **onEvents** définit la variable **latestEventData** qui suit le nombre de décalage et de séquence du dernier message lu par ce processeur d’événements. N’oubliez pas que chaque processeur est responsable d’une partition unique. La méthode **onEvents** puis IoT concentrateur reçoit un lot de messages et les traite comme suit : il envoie des messages interactifs à la file d’attente de Bus de Service et ajoute les messages de point de données dans la mémoire tampon de **toAppend** . Si la mémoire tampon atteint la limite de bloc de 4 Mo, ou les périodes de déduplication s’écoule (une heure après le dernier point de contrôle dans ce didacticiel), la méthode déclenche un point de contrôle.

La méthode **AppendAndCheckPoint** génère d’abord une **l’ID de bloc** pour le bloc à ajouter à l’objet blob. Le stockage Azure requiert que tous les bloquent ID pour avoir la même longueur, afin que la méthode remplit le décalage avec des zéros non significatifs. Ensuite, si un bloc avec cet ID existe déjà dans l’objet blob, la méthode remplace par le contenu actuel de la mémoire tampon.

> [AZURE.NOTE] Pour simplifier le code, ce didacticiel utilise un seul blob par partition pour stocker les messages. Une véritable solution implémenterait propagées en créant des fichiers supplémentaires après un certain temps ou lorsqu’ils atteignent une certaine taille de fichier. N’oubliez pas qu’un blob Azure bloc peut contenir au maximum 195 Go de données.

La tâche suivante consiste à implémenter l’interface **IEventProcessor** :

1. Utilisez un éditeur de texte pour créer un fichier process-d2c-messages\src\main\java\com\mycompany\app\EventProcessor.java.

2. Ajoutez les importations suivantes et la définition de classe dans le fichier EventProcessor.java. La classe **EventProcessor** implémente l’interface **IEventProcessor** qui définit le comportement du client concentrateurs d’événement :

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. Ajoutez les méthodes suivantes à la classe **EventProcessor** pour implémenter l’interface **IEventProcessor** :

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. Ajoutez les variables de niveau classe suivants à la classe **EventProcessor** :

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. Ajoutez une méthode **AppendAndCheckPoint** avec la signature suivante à la classe **EventProcessor** :

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. Ajoutez le code suivant à la méthode **AppendAndCheckPoint** pour récupérer le nombre de décalage et la séquence de message en cours dans la partition :

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. Dans la méthode **AppendAndCheckPoint** , utilisez la valeur de décalage en cours pour créer une instance de **BlockEntry** pour le bloc suivant enregistrer dans le blob :

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. Dans la méthode **AppendAndCheckPoint** , téléchargez le dernier jeu de messages pour le blob de bloc et récupérer la liste actuelle des blocs :

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. Dans la méthode **AppendAndCheckPoint** , créez le bloc initial dans un blob nouveau ou ajouter le bloc pour l’objets blob existants :

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. Enfin, dans la méthode **AppendAndCheckPoint** , créer un point de contrôle sur la partition et préparez-vous à enregistrer le bloc de messages suivant :

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. Dans la méthode **onEvents** , ajoutez le code suivant pour recevoir des messages à la file d’attente de Bus de Service à partir du point de terminaison IoT Hub et transférer les messages interactifs. Appelez ensuite la méthode **AppendAndCheckPoint** lorsque le bloc est plein ou de l’expiration du délai :

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. Enfin, dans la méthode **onEvents** , ajoutez une clause « else if » pour appeler l' **AppendAndCheckPoint** si le délai expire alors qu’aucun message provenant de IoT Hub :

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. Enregistrer les modifications dans le fichier EventProcessor.java.

La dernière tâche dans le projet de **processus-d2c-messages** consiste à ajouter du code à la méthode **main** qui instancie une instance de **EventProcessorHost** .

1. Utilisez un éditeur de texte pour ouvrir le fichier process-d2c-messages\src\main\java\com\mycompany\app\App.java.

2. Ajoutez les instructions **d’importation** suivantes au fichier :

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. Ajoutez la variable de niveau classe suivante à la classe **App** . Remplacez **{yourstorageaccountconnectionstring}** par la chaîne de connexion de compte de stockage Azure que vous notés précédemment dans la section [configurer un compte de stockage Azure et une file d’attente de Bus de Service](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. Ajouter les variables de niveau classe suivants à la classe **App** et remplacez **{yourservicebusnamespace}** votre espace de noms de Service Bus et le **{yourservicebussendkey}** avec **Envoyer** la clé de votre file d’attente. Précédemment notés un espace de noms et **d’écouter** des valeurs de clé dans la section [configurer un compte de stockage Azure et une file d’attente de Bus de Service](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. Ajoutez les variables de niveau classe suivants à la classe **App** . Remplacez **{youreventhubcompatibleendpoint}** par la valeur de point de terminaison du concentrateur d’événements compatibles. L’aspect de la valeur de point de terminaison **..., son espace de noms** vous ne devez supprimer la **sb : / /** préfixe et le suffixe **.servicebus.windows.net/** . Remplacez **{youreventhubcompatiblename}** par le nom de l’événement compatible avec le concentrateur. Remplacez **{youriothubkey}** avec la clé **iothubowner** . Vous avez de ces valeurs dans la [créer un concentrateur IoT] [ lnk-create-an-iot-hub] section du didacticiel *mise en route de concentrateur de IoT Azure pour Java* :

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. Modifiez la signature de la méthode **main** comme suit :

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. Ajoutez le code suivant à la méthode **principale** pour obtenir une référence au conteneur blob qui stocke les messages :

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. Ajoutez le code suivant à la méthode **principale** pour obtenir une référence au service de Bus de Service :

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. Dans la méthode **main** , configurer et créer une instance de **EventProcessorHost** . L’option **setInvokeProcessorAfterReceiveTimeout** permet de s’assurer que l’instance de **EventProcessorHost** appelle la méthode **onEvents** de l’interface **IEventProcessor** , même lorsqu’il n’y a aucun message à traiter. Puis la méthode **onEvents** appelle toujours la méthode **AppendAndCheckPoint** lors de l’expiration du délai.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. Dans la méthode **main** , enregistrer la mise en oeuvre de **IEventProcessor** avec l’instance de **EventProcessorHost** :

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. Enfin, ajouter la logique à la méthode **principale** d’arrêter l’instance de **EventProcessorHost** :

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. Enregistrez et fermez le fichier process-d2c-messages\src\main\java\com\mycompany\app\App.java.

13. Pour générer l’application **d2c-traitement des messages** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier messages de d2c de processus :

    ```
    mvn clean package -DskipTests
    ```

## <a name="receive-interactive-messages"></a>Recevoir des messages interactifs

Dans cette section, vous écrivez une application console Java qui reçoit les messages interactifs à partir de la file d’attente de Bus de Service.

La première tâche est d’ajouter un projet Maven appelé **processus interactif messages** qui reçoit les messages envoyés dans la file d’attente de Bus de Service à partir des instances **EventProcessor** .

1. Dans le dossier iot java-démarrer que vous avez créé dans le didacticiel [mise en route de IoT concentrateur] , créez un projet de Maven appelé **processus interactif messages** à l’aide de la commande suivante à l’invite de commande. Remarque Il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Suivante à l’invite de commandes, naviguez vers le nouveau dossier de processus interactif messages.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier messages interactifs processus et ajouter les dépendances suivantes pour le nœud **dépendances** . Cette dépendance vous permet d’utiliser le package azure-servicebus dans votre application d’interagir avec votre file d’attente de Bus de Service :

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

La tâche suivante consiste à ajouter du code pour extraire les messages de la file d’attente de Bus de Service.

1. Utilisez un éditeur de texte pour ouvrir le fichier process-interactive-messages\src\main\java\com\mycompany\app\App.java.

2. Ajoutez le code suivant `import` instructions dans le fichier :

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. Ajouter les variables de niveau classe suivants à la classe **App** et remplacez **{yourservicebusnamespace}** votre espace de noms de Service Bus et le **{yourservicebuslistenkey}** avec clé **d’écouter** votre file d’attente. Précédemment notés un espace de noms et **d’écouter** des valeurs de clé dans la section [configurer un compte de stockage Azure et une file d’attente de Bus de Service](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. Ajoutez la classe imbriquée suivante à la classe **d’application** de recevoir des messages de la file d’attente :

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. Modifiez la signature de la méthode **main** comme suit :

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. Dans la méthode **main** , ajoutez le code suivant pour démarrer l’écoute pour les nouveaux messages :

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. Enregistrez et fermez le fichier process-interactive-messages\src\main\java\com\mycompany\app\App.java.

8. Pour générer l’application de **processus interactif messages** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier messages interactifs processus :

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les trois applications.

1.  Pour exécuter l’application de **processus interactif messages** , naviguer dans le dossier messages interactifs processus dans une invite de commande ou du shell et exécutez la commande suivante :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécuter le processus interactif messages][processinteractive]

2.  Pour exécuter l’application **d2c-traitement des messages** , dans une invite de commande ou du shell, accédez au dossier d2c-traitement des messages et exécutez la commande suivante :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécuter d2c-traitement des messages][processd2c]

3.  Pour exécuter l’application de **périphérique simulé** , dans une invite de commande ou du shell, naviguez vers le dossier du périphérique simulé et exécutez la commande suivante :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécuter périphérique simulé][simulateddevice]

> [AZURE.NOTE] Pour voir les mises à jour dans le blob, vous devrez peut-être réduire la constante **MAX_BLOCK_SIZE** dans la classe **StoreEventProcessor** à une valeur inférieure, par exemple **1024**. Cette modification est utile dans la mesure où elle prend un certain temps pour atteindre la limite de taille de bloc avec les données envoyées par le périphérique simulé. Avec une plus petite taille de bloc, vous n’avez pas besoin d’attendre si longtemps pour voir l’objet blob est créé et mis à jour. Toutefois, à l’aide d’une taille de bloc supérieure rend l’application plus évolutive.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment traiter fiable du point de données et des messages de périphérique-nuage interactives à l’aide de la classe [EventProcessorHost] .

[Comment envoyer des messages de nuage-dispositif avec concentrateur de IoT] [ lnk-c2d] vous montre comment envoyer des messages à vos équipements depuis votre back-end.

Pour voir des exemples de solutions de bout en bout complètes qui utilisent IoT Hub, consultez [Azure IoT Suite][lnk-suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT concentrateur].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Stockage des objets blob Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Usine de données Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[File d’attente de Bus de service]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guide du développeur Azure IoT Hub - périphérique vers le cloud]: iot-hub-devguide-messaging.md

[Stockage Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus des services Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guide du développeur IoT concentrateur]: iot-hub-devguide.md
[Mise en route de concentrateur de IoT]: iot-hub-java-java-getstarted.md
[Centre de développement Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[À propos du stockage Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Mise en route avec les concentrateurs d’événement]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Évolutivité de stockage Azure instructions]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub