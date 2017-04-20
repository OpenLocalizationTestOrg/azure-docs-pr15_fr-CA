## <a name="send-messages-to-event-hubs"></a>Envoyer des messages à des concentrateurs d’événement

La bibliothèque cliente Java pour les concentrateurs d’événement n’est disponible pour une utilisation dans les projets Maven à partir du [Référentiel Central de Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)et peut être référencée à l’aide de la déclaration suivante de la dépendance à l’intérieur de votre fichier de projet Maven :    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Pour différents types d’environnements de génération, vous pouvez explicitement obtenir les derniers fichiers JAR lancés à partir du [Référentiel Central de Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) ou à partir [du point de distribution release sur GitHub](https://github.com/Azure/azure-event-hubs/releases).  

Pour un éditeur d’événements simples, importer les packages *com.microsoft.azure.eventhubs* pour les classes clientes de concentrateurs d’événement et *com.microsoft.azure.servicebus* pour les classes d’utilitaire telles que les exceptions qui sont partagées avec le client de messagerie de Bus des services Azure. 

Pour l’exemple suivant, créez tout d’abord un projet pour une application de console/shell Maven dans votre environnement de développement Java favori. La classe sera appelée ```Send```.     

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Remplacez l’espace de noms et les noms Hub d’événement avec les valeurs utilisées lorsque vous avez créé le concentrateur d’événements.

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Ensuite, créez un événement singulier en activant une chaîne dans le codage d’octets UTF-8. Nous avons ensuite créer une nouvelle instance de client de concentrateurs d’événement à partir de la chaîne de connexion et envoyer le message.   

``` Java 
                
    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);
    
    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 
