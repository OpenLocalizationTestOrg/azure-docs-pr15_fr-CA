## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Recevoir des messages avec EventProcessorHost dans Java

EventProcessorHost est une classe Java qui simplifie le récepteur d’événements de concentrateurs d’événement par gestion des points de contrôle permanents et parallèle reçoit de concentrateurs de ces événements. À l’aide de EventProcessorHost vous pouvez répartir des événements sur plusieurs récepteurs, même lorsqu’il est hébergé dans des nœuds différents. Cet exemple montre comment utiliser EventProcessorHost pour un récepteur unique.

###<a name="create-a-storage-account"></a>Créer un compte de stockage

Pour utiliser EventProcessorHost, vous devez disposer d’un [compte de stockage Azure][]:

1. Ouvrez une session sur [Azure portal classique][], puis cliquez sur **Nouveau** en bas de l’écran.

2. Cliquez sur **Services de données**, **stockage**, puis sur **Création rapide**et tapez un nom pour votre compte de stockage. Sélectionnez votre région souhaitée, puis cliquez sur **Créer un compte de stockage**.

    ![][11]

3. Cliquez sur le compte de stockage nouvellement créé, puis cliquez sur **Gérer les touches d’accès rapide**:

    ![][12]

    Copiez la clé d’accès principal pour l’utiliser plus tard dans ce didacticiel.

###<a name="create-a-java-project-using-the-eventprocessor-host"></a>Créez un projet de Java à l’aide de l’hôte EventProcessor

La bibliothèque cliente Java pour les concentrateurs d’événement n’est disponible pour une utilisation dans les projets Maven à partir du [Référentiel Central de Maven][Maven Package]et peuvent être référencées à l’aide de la déclaration suivante de la dépendance à l’intérieur de votre fichier de projet Maven :    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Pour les différents types d’environnements de génération, vous pouvez obtenir explicitement des derniers fichiers JAR lancés à partir du [Référentiel Central de Maven] [ Maven Package] ou à partir [du point de distribution release sur GitHub](https://github.com/Azure/azure-event-hubs/releases).  

1. Pour l’exemple suivant, créez tout d’abord un projet pour une application de console/shell Maven dans votre environnement de développement Java favori. La classe sera appelée ```ErrorNotificationHandler```.     

    ``` Java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```

2. Utilisez le code suivant pour créer une nouvelle classe appelée ```EventProcessor```.

    ```Java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;

    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
                
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```

3. Créer une classe finale appelée ```EventProcessorSample```, en utilisant le code suivant.

    ```Java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;

    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";

            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
            
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
            
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
            
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }

            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
                
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
            
            System.out.println("End of sample");
        }
    }
    ```

4. Remplacer les champs suivants avec les valeurs utilisées lorsque vous avez créé le concentrateur d’événements et le compte de stockage.

    ``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";

    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";

    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [AZURE.NOTE] Ce didacticiel utilise une seule instance de EventProcessorHost. Pour augmenter le débit, il est recommandé d’exécuter plusieurs instances de EventProcessorHost. Dans ces cas, les différentes instances automatiquement coordonnent entre eux afin d’équilibrer la charge les événements reçus. Si vous souhaitez que plusieurs récepteurs pour chaque processus *tous* les événements, vous devez utiliser le concept de **ConsumerGroup** . Lors de la réception d’événements à partir d’ordinateurs différents, il peut être utile de spécifier des noms pour les instances de EventProcessorHost basées sur les ordinateurs (ou rôles) dans lequel ils sont déployés.

<!-- Links -->
[Event Hubs overview]: ../articles/event-hubs/event-hubs-overview.md
[Compte de stockage Azure]: ../articles/storage/storage-create-storage-account.md
[Azure portal classique]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

