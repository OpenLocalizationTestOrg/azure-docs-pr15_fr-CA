<properties 
    pageTitle="Bus de service demandé de messagerie didacticiel de .NET | Microsoft Azure"
    description="Didacticiel de .NET messagerie contemporains."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-brokered-messaging-net-tutorial"></a>Bus de service demandé de messagerie .NET didacticiel

Bus des services Azure fournit deux solutions de messagerie complètes – une, via un service centralisé de « relais » s’exécutant dans le nuage qui prend en charge une variété de différents protocoles de transport et de Web services les normes, y compris SOAP, WS-* et le reste. Le client ne doit pas une connexion directe au service sur site, ni qu’il a besoin de savoir où réside le service et le service sur site n’a pas besoin de ports entrants ouvrir sur le pare-feu.

La deuxième solution de messagerie permet de fonctionnalités de messagerie « courtage ». Ces peuvent être considérés comme asynchrones ou dissociés des fonctionnalités de messagerie prise en charge de publication-abonnement découplage temporelle et scénarios à l’aide de l’infrastructure de messagerie de Bus de Service d’équilibrage de la charge. Communication découplée a présente de nombreux avantages ; par exemple, clients et les serveurs peuvent se connecter en fonction des besoins et effectué leurs propres opérations de manière asynchrone.

Ce didacticiel est destiné à vous donner une vue d’ensemble et une expérience pratique des files d’attente, un des composants principaux du Bus de Service demandé de la messagerie. Une fois que vous travaillez à travers la séquence des rubriques dans ce didacticiel, vous aurez une application qui remplit une liste de messages, crée une file d’attente et envoie les messages à cette file d’attente. Enfin, l’application reçoit et affiche les messages de la file d’attente, puis nettoie ses ressources et les sorties. Pour obtenir un didacticiel correspondant qui décrit comment générer une application qui utilise le relais de Bus de Service, consultez le [Bus de Service relais de messagerie didacticiel](../service-bus-relay/service-bus-relay-tutorial.md).

## <a name="introduction-and-prerequisites"></a>Introduction et conditions préalables

Files d’attente offrent First In, remise des messages à un ou plusieurs consommateurs concurrents premier sorti (FIFO). FIFO signifie que les messages sont en général devrait être reçus et traités par les destinataires dans l’ordre temporel dans lequel ils ont été placés, et chaque message sera reçu et traité par un consommateur de message qu’un seul. Des principaux avantages de l’utilisation de files d’attente sont atteindre *temporelle de découplage* des composants d’application : en d’autres termes, les producteurs et les consommateurs n’est pas nécessaire à l’envoi et la réception de messages en même temps, dans la mesure où les messages sont stockés durablement dans la file d’attente. Un avantage connexe est *nivellement de charge*, qui permet d’envoyer et de recevoir des messages à des taux différents producteurs et les consommateurs.

Voici certaines étapes d’administration et des conditions requises, que vous devez suivre avant de commencer le didacticiel. Le premier est de créer un espace de noms du service et pour obtenir une clé de signature (SAS) d’un accès partagé. Un espace de noms fournit une limite d’application pour chaque application exposé par le biais de Bus de Service. Une clé SAS est générée automatiquement par le système lors de la création d’un espace de noms du service. La combinaison de l’espace de noms de service et de la clé des associations de sécurité fournit des informations d’identification avec lesquelles Bus de Service authentifie l’accès à une application.

### <a name="create-a-service-namespace-and-obtain-a-sas-key"></a>Créer un espace de noms du service et obtenir une clé de SAS

La première étape consiste à créer un espace de noms de service et d’obtenir une clé de [Signature de l’accès partagé](service-bus-sas-overview.md) (SAS). Un espace de noms fournit une limite d’application pour chaque application exposé par le biais de Bus de Service. Une clé SAS est générée automatiquement par le système lors de la création d’un espace de noms du service. La combinaison de l’espace de noms de service et de la clé des associations de sécurité fournit des informations d’identification pour le Bus de Service authentifier l’accès à une application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

L’étape suivante consiste à créer un projet Visual Studio et écrire deux fonctions d’assistance qui chargent une liste délimitée par des virgules des messages dans un objet fortement typé de .NET, [liste](https://msdn.microsoft.com/library/6sh2ey19.aspx) de [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .

### <a name="create-a-visual-studio-project"></a>Créez un projet Visual Studio

1. Ouvrez Visual Studio en tant qu’administrateur en cliquant le programme dans le menu Démarrer et en cliquant sur **Exécuter en tant qu’administrateur**.

1. Créer un nouveau projet d’application console. Cliquez sur le menu **fichier** et sélectionnez **Nouveau**, puis cliquez sur **projet**. Dans la boîte de dialogue **Nouveau projet** , cliquez sur **Visual C#** (si **Visual C#** n’apparaît pas, regardez sous **Autres langages**), cliquez sur le modèle **Application Console** et nommez-la **QueueSample**. Utilisez **l’emplacement**par défaut. Cliquez sur **OK** pour créer le projet.

1. Utilisez le Gestionnaire de package NuGet pour ajouter les bibliothèques de Bus de Service à votre projet :
    1. Dans l’Explorateur de solutions, cliquez sur le projet **QueueSample** et puis cliquez sur **Gérer les Packages NuGet**.
    2. Dans la boîte de dialogue **Manage Nuget Packages** , cliquez sur l’onglet **Parcourir** , recherchez le **Bus des services Azure**, puis cliquez sur **installer**.
<br />
1. Dans l’Explorateur de solutions, double-cliquez sur le fichier Program.cs pour l’ouvrir dans l’éditeur de Visual Studio. Modifier le nom d’espace de noms à partir de son nom par défaut de `QueueSample` à `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Modifier la `using` comme le montre le code suivant.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```

1. Créez un fichier texte nommé Data.csv et copier du texte délimité par des virgules suivantes.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Enregistrez et fermez le fichier Data.csv et n’oubliez pas de l’emplacement dans lequel vous l’avez enregistré.

1. Dans l’Explorateur de solutions, cliquez sur le nom de votre projet (dans cet exemple, **QueueSample**), cliquez sur **Ajouter**, puis cliquez sur **Élément existant**.

1. Recherchez le fichier Data.csv que vous avez créé à l’étape 6. Cliquez sur le fichier, puis cliquez sur **Ajouter**. Assurez-vous que * *tous les fichiers (*.*) ** est sélectionné dans la liste des types de fichiers.

### <a name="create-a-method-that-parses-a-list-of-messages"></a>Créez une méthode qui analyse une liste de messages

1. Dans le `Program` de classe avant de la `Main()` méthode, déclarer deux variables : un de type **DataTable**, pour contenir la liste des messages dans Data.csv. L’autre doit être de type objet de liste, fortement typée de [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Cette dernière est la liste des messages de courtage utilisées par les étapes suivantes du didacticiel.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```

1. Externe `Main()`, définir un `ParseCSV()` méthode qui analyse la liste des messages dans Data.csv et charge les messages dans une table de la [table de données](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx) , comme illustré ici. La méthode retourne un objet **DataTable** .

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;
    
                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }
    
                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }
    
        return tableIssues;
    }
    ```

1. Dans le `Main()` méthode, ajoutez une instruction qui appelle la `ParseCSVFile()` méthode :

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
    
    }
    ```

### <a name="create-a-method-that-loads-the-list-of-messages"></a>Créez une méthode qui charge la liste des messages

1. Externe `Main()`, définir un `GenerateMessages()` méthode prend l’objet **DataTable** retourné par `ParseCSVFile()` et charge le tableau dans une liste fortement typée de messages contemporains. Ensuite, la méthode retourne l’objet de **liste** , comme dans l’exemple suivant. 

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();
    
        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```

1. Dans `Main()`, directement après l’appel à `ParseCSVFile()`, ajoutez une instruction qui appelle la `GenerateMessages()` méthode avec la valeur de retour à partir de `ParseCSVFile()` en tant qu’argument :

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### <a name="obtain-user-credentials"></a>Obtenir des informations d’identification de l’utilisateur

1. Commencez par créer trois variables de chaîne globale pour stocker ces valeurs. Déclarez les variables directement après les déclarations de variable précédentes ; par exemple :

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. Ensuite, créez une fonction qui accepte et enregistre l’espace de noms de service et de la clé SAS. Ajoutez cette méthode à l’extérieur de `Main()`. Par exemple : 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. Dans `Main()`, directement après l’appel à `GenerateMessages()`, ajoutez une instruction qui appelle la `CollectUserInput()` méthode :

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
        
        // Collect user input
        CollectUserInput();
    }
    ```

### <a name="build-the-solution"></a>Générez la solution

Dans le menu **Générer** dans Visual Studio, vous pouvez cliquez sur **Générer la Solution** ou appuyez sur **Ctrl + Maj + B** pour confirmer l’exactitude de votre travail jusqu'à présent.

## <a name="create-management-credentials"></a>Créer des informations d’identification d’administration

Dans cette étape, vous définissez les opérations de gestion que vous allez utiliser pour créer des informations d’identification de la signature (SAS) avec laquelle votre application sera autorisée un accès partagé.

1. Pour plus de clarté, ce didacticiel place toutes les opérations de file d’attente dans une méthode distincte. Créer un async `Queue()` méthode dans le `Program` de classe, après la `Main()` méthode. Par exemple :
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. L’étape suivante consiste à créer une information d’identification SAS à l’aide d’un objet [TokenProvider a](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) . La méthode de création prend le nom de sa clé et la valeur obtenue dans le `CollectUserInput()` méthode. Ajoutez le code suivant à la `Queue()` méthode :

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. Créer un nouvel objet de gestion de l’espace de noms, avec un URI qui contient l’espace de noms et les informations d’identification de gestion obtenues à l’étape précédente, en tant qu’arguments. Ajoutez ce code directement après le code ajouté à l’étape précédente. Veillez à remplacer `<yourNamespace>` avec le nom de votre espace de noms du service :
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### <a name="example"></a>Exemple

À ce stade, votre code doit ressembler à ce qui suit :

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## <a name="send-messages-to-the-queue"></a>Envoyer des messages à la file d’attente

Dans cette étape, vous créez une file d’attente, puis envoyez les messages contenus dans la liste des messages contemporains pour cette file d’attente.

### <a name="create-queue-and-send-messages-to-the-queue"></a>Créer la file d’attente et d’envoyer des messages à la file d’attente

1. Commencez par créer la file d’attente. Par exemple, appelez-le `myQueue`et vous le déclarez directement après les opérations de gestion que vous avez ajouté dans la `Queue()` méthode dans la dernière étape :

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. Dans le `Queue()` méthode, créez un objet de fabrique de messagerie avec un URI de Bus de Service nouvellement créé en tant qu’argument. Ajoutez le code suivant directement après les opérations de gestion que vous avez ajoutée à la dernière étape. Veillez à remplacer `<yourNamespace>` avec le nom de votre espace de noms du service :

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

1. Ensuite, créez l’objet de file d’attente à l’aide de la classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Ajoutez le code suivant directement après le code ajouté à la dernière étape :

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```

1. Ensuite, ajoutez le code qui effectue une boucle sur la liste des messages de courtage que précédemment créé, envoyez à la file d’attente. Ajoutez le code suivant directement après le `CreateQueueClient()` relevé à l’étape précédente :
    
    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## <a name="receive-messages-from-the-queue"></a>Recevoir des messages de la file d’attente

Dans cette étape, vous obtenez la liste des messages de la file d’attente que vous avez créé à l’étape précédente.

### <a name="create-a-receiver-and-receive-messages-from-the-queue"></a>Créer un récepteur et recevoir des messages de la file d’attente

Dans le `Queue()` méthode, effectuer une itération dans la file d’attente et les messages à l’aide de la méthode [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx) , l’impression de chaque message sur la console. Ajoutez le code suivant directement après le code ajouté à l’étape précédente :

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Notez que les `Thread.Sleep` est uniquement utilisé pour simuler le message et que vous probablement n’aurait pas besoin dans une application de messagerie réel.

### <a name="end-the-queue-method-and-clean-up-resources"></a>Fin de la méthode de la file d’attente et nettoyer des ressources

Directement après le code précédent, ajoutez le code suivant pour nettoyer les ressources d’usine et de la file d’attente de message :

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### <a name="call-the-queue-method"></a>Appelez la méthode de la file d’attente

La dernière étape consiste à ajouter une instruction qui appelle la `Queue()` méthode à partir de `Main()`. Ajoutez la ligne de code en surbrillance suivante à la fin de Main() :
    
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
    
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
    
    // Add this call
    Queue();
}
```

### <a name="example"></a>Exemple

Le code suivant contient l’application complète de **QueueSample** .

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## <a name="build-and-run-the-queuesample-application"></a>Générer et exécuter l’application QueueSample

Maintenant que vous avez effectué les étapes ci-dessus, vous pouvez générer et exécuter l’application **QueueSample** .

### <a name="build-the-queuesample-application"></a>Générez l’application QueueSample

Dans Visual Studio, dans le menu **Générer** , cliquez sur **Générer la Solution**ou appuyez sur **Ctrl + Maj + B**. Si vous rencontrez des erreurs, vérifiez que votre code est correct en fonction de l’exemple complet présenté à la fin de l’étape précédente.

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a montré comment créer un client de Bus de Service application et le service en utilisant les fonctionnalités de messagerie dans le Bus des services. Pour obtenir un didacticiel similaire qui utilise le Bus de Service [relais](service-bus-messaging-overview.md#Relayed-messaging), consultez le [Service Bus relayé didacticiel de messagerie](../service-bus-relay/service-bus-relay-tutorial.md).

Pour en savoir plus sur le [Bus de Service](https://azure.microsoft.com/services/service-bus/), consultez les rubriques suivantes.

- [Vue d’ensemble de la messagerie Bus des services](service-bus-messaging-overview.md)
- [Principes de base de Bus des services](service-bus-fundamentals-hybrid-solutions.md)
- [Architecture de Bus de service](service-bus-architecture.md)

