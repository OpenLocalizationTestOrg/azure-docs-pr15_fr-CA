<properties 
    pageTitle="Didacticiel de relais de Bus de service | Microsoft Azure"
    description="Création d’un client de Bus de Service application et service à l’aide du relais de Bus de Service."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-tutorial"></a>Didacticiel de relais de Bus de service

Ce didacticiel explique comment créer une simple application de client de Service Bus et le service en utilisant les fonctionnalités de « relais » de Bus des services. Pour obtenir un didacticiel correspondant qui utilise le Bus de Service [par courtier de messagerie](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging), consultez le [Service Bus : négociée dans un didacticiel de .NET Messaging](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Travail par le biais de ce didacticiel vous offre une présentation des étapes requises pour créer une application client et le service de Bus de Service. Comme leurs homologues WCF, un service est une construction qui expose un ou plusieurs points de terminaison, dont chacun dispose d’une ou plusieurs opérations de service. Le point de terminaison de service spécifie une adresse où le service peut être trouvé, une liaison qui contient les informations d’un client doit communiquer avec le service et le contrat qui définit les fonctionnalités fournies par le service à ses clients. La principale différence entre un service WCF et d’un service de Bus de Service est que le point de terminaison est exposé dans le nuage et non localement sur votre ordinateur.

Une fois que vous travaillez à travers la séquence des rubriques dans ce didacticiel, vous aurez un service en cours d’exécution et un client qui peut appeler les opérations du service. La première rubrique explique comment configurer un compte. Les étapes suivantes décrivent comment définir un service qui utilise un contrat implémenter le service et comment configurer le service dans le code. Elles décrivent également comment héberger et exécuter le service. Le service qui est créé est autonome et que le client et le service s’exécuter sur le même ordinateur. Vous pouvez configurer le service à l’aide de code ou un fichier de configuration.

Les trois dernière étapes décrivent comment créer une application cliente, configurer l’application cliente et créer et utilisent un client qui peut accéder à la fonctionnalité de l’hôte.

Toutes les rubriques de cette section supposent que vous utilisez Visual Studio comme environnement de développement.

## <a name="sign-up-for-an-account"></a>Inscrivez-vous à un compte

La première étape consiste à créer un espace de noms et obtenir une clé de Signature de l’accès partagé (SAS). Un espace de noms fournit une limite d’application pour chaque application exposé par le biais de Bus de Service. Une clé SAS est générée automatiquement par le système lors de la création d’un espace de noms du service. La combinaison d’espace de noms de service et de la clé SAS fournit les informations d’identification pour le Bus de Service authentifier l’accès à une application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract-to-use-with-service-bus"></a>Définir un contrat de service à utiliser avec le Bus de Service WCF

Le contrat de service spécifie les opérations (la terminologie du service Web pour les fonctions ou méthodes) prend en charge par le service. Les contrats sont créés en définissant une interface C++, C# ou Visual Basic. Chaque méthode de l’interface correspond à une opération de service spécifique. Chaque interface doit avoir l’attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) qui lui est appliqué, et chaque opération doit avoir l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) qui lui est appliqué. Si une méthode dans une interface qui possède l’attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) n’a pas l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) , cette méthode n’est pas exposée. Le code de ces tâches est fourni dans l’exemple suivant la procédure. Pour une discussion plus grande, des contrats et des services, consultez conception [et mise en œuvre de Services](https://msdn.microsoft.com/library/ms729746.aspx) dans la documentation WCF.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Pour créer un contrat de Service Bus grâce à une interface

1. Ouvrez Visual Studio en tant qu’administrateur en cliquant sur le programme dans le menu **Démarrer** et sélectionnez **Exécuter en tant qu’administrateur**.

2. Créer un nouveau projet d’application console. Cliquez sur le menu **fichier** et sélectionnez **Nouveau**, puis cliquez sur **projet**. Dans la boîte de dialogue **Nouveau projet** , cliquez sur **Visual C#** ( **Visual C#** n’apparaît pas, regardez sous **Autres langages**). Cliquez sur le modèle **Application Console** et nommez-la **EchoService**. Cliquez sur **OK** pour créer le projet.

    ![][2]

3. Installez le package NuGet de Bus de Service. Ce package ajoute automatiquement des références pour les bibliothèques de Bus des services, ainsi que le WCF **System.ServiceModel**. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) est l’espace de noms vous permet d’accéder par programme les fonctionnalités de base de WCF. Bus de service utilise la plupart des objets et des attributs de WCF pour définir des contrats de service.

    Dans l’Explorateur de solutions, avec le bouton droit de la solution, puis cliquez sur **Gérer les Packages NuGet pour la Solution**. Cliquez sur l’onglet **Parcourir** , puis recherchez les `Microsoft Azure Service Bus`. Assurez-vous que le nom du projet est sélectionné dans la zone **ou les versions** . Cliquez sur **installer**et accepter les conditions d’utilisation.

    ![][3]

3. Dans l’Explorateur de solutions, double-cliquez sur le fichier Program.cs pour l’ouvrir dans l’éditeur, s’il n’est pas déjà ouvert.

4. Ajoutez le code suivant à l’aide d’instructions en haut du fichier :

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Remplacez le nom de l’espace de noms à partir de son nom par défaut de **EchoService** **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] Ce didacticiel utilise le C# espace de noms **Microsoft.ServiceBus.Samples**, qui est l’espace de noms du type contrat géré qui est utilisé dans le fichier de configuration de l’étape de [configuration du client WCF](#configure-the-wcf-client) . Vous pouvez spécifier n’importe quel espace de noms lorsque vous générez cet exemple ; Toutefois, le didacticiel ne fonctionnera pas à moins que vous puis modifiez les espaces de noms du contrat de service en conséquence, dans le fichier de configuration d’application. L’espace de noms spécifié dans le fichier App.config doit être le même que l’espace de noms spécifié dans vos fichiers C#.

1. Directement après le `Microsoft.ServiceBus.Samples` déclaration de l’espace de noms, mais dans l’espace de noms, définissez une nouvelle interface nommée `IEchoContract` et appliquer le `ServiceContractAttribute` d’attribut à l’interface avec une valeur d’espace de noms de **http://samples.microsoft.com/ServiceModel/Relay/**. La valeur de l’espace de noms est différent de l’espace de noms que vous utilisez dans la portée de votre code. Au lieu de cela, la valeur de l’espace de noms est utilisée comme identificateur unique pour ce contrat. Spécification explicite de l’espace de noms empêche l’ajout d’un nom de contrat la valeur d’espace de noms par défaut.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] En règle générale, l’espace de noms de contrat de service contient un schéma d’affectation de noms qui contient des informations de version. Y compris les informations de version dans l’espace de noms de contrat de service permet de services à isoler des modifications majeures en définissant un nouveau contrat de service avec un espace de noms et en l’exposant sur un nouveau point de terminaison. De cette manière, les clients peuvent continuer à utiliser l’ancien contrat de service sans avoir à être mis à jour. Informations de version peuvent être constitué d’une date ou un numéro de build. Pour plus d’informations, consultez le [Service de contrôle de version](http://go.microsoft.com/fwlink/?LinkID=180498). Pour les besoins de ce didacticiel, le schéma d’affectation de noms de l’espace de noms de contrat de service ne contient-elle pas d’informations de version.

1. Dans le `IEchoContract` d’interface, de déclarer une méthode pour la seule opération la `IEchoContract` expose dans l’interface de contrat et s’appliquent le `OperationContractAttribute` d’attribut à la méthode que vous souhaitez exposer comme faisant partie du contrat public des Bus de Service.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Directement après le `IEchoContract` définition d’interface, de déclarer un canal qui hérite à la fois `IEchoContract` et également à la `IClientChannel` de l’interface, comme indiqué ici :

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Un canal est l’objet WCF par le biais duquel l’hôte et le client passent des informations entre eux. Par la suite, vous écrivez du code contre le canal à répercuter les informations entre les deux applications.

1. Dans le menu **Générer** , cliquez sur **Générer la Solution** ou appuyez sur **Ctrl + Maj + B** pour confirmer l’exactitude de votre travail jusqu'à présent.

### <a name="example"></a>Exemple

Le code suivant montre une interface de base qui définit un contrat de Service Bus.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Maintenant que l’interface est créée, vous pouvez implémenter l’interface.

## <a name="implement-the-wcf-contract-to-use-service-bus"></a>Implémenter le contrat WCF à utiliser le Bus de Service

Création d’un relais de Bus de Service exige que vous créiez d’abord le contrat, qui est défini à l’aide d’une interface. Pour plus d’informations sur la création de l’interface, reportez-vous à l’étape précédente. L’étape suivante consiste à implémenter l’interface. Ceci implique la création d’une classe nommée `EchoService` qui implémente défini par l’utilisateur `IEchoContract` interface. Après avoir implémenté l’interface, vous configurer l’interface à l’aide d’un fichier de configuration App.config. Le fichier de configuration contient les informations nécessaires pour l’application, telles que le nom du service, le nom de contrat et le type de protocole utilisé pour communiquer avec le Bus de Service. Le code utilisé pour effectuer ces tâches est fourni dans l’exemple suivant la procédure. Pour plus d’informations sur comment implémenter un contrat de service, consultez [Implémentation de contrats de Service](https://msdn.microsoft.com/library/ms733764.aspx) dans la documentation WCF.

1. Créer une nouvelle classe nommée `EchoService` directement après la définition de la `IEchoContract` interface. Le `EchoService` de classe implémente la `IEchoContract` interface. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Similaire à d’autres implémentations de l’interface, vous pouvez implémenter la définition dans un autre fichier. Toutefois, la mise en oeuvre dans le cadre de ce didacticiel, se trouve dans le même fichier que la définition d’interface et de la `Main` méthode.

1. Appliquer l’attribut [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à la `IEchoContract` interface. L’attribut spécifie le nom du service et l’espace de noms. Après cela, le `EchoService` classe apparaît comme suit :

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Mettre en œuvre la `Echo` méthode définie dans le `IEchoContract` d’interface dans le `EchoService` classe. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Cliquez sur **Générer**, puis cliquez sur **Générer la Solution** pour vérifier l’exactitude de votre travail.

### <a name="to-define-the-configuration-for-the-service-host"></a>Pour définir la configuration de l’hôte de service

1. Le fichier de configuration est très similaire à un fichier de configuration WCF. Il inclut le nom du service, le point de terminaison (autrement dit, l’emplacement expose des Bus de Service pour les clients et hôtes de communiquer entre eux) et la liaison (le type de protocole utilisé pour communiquer). La principale différence est que ce point de terminaison de service configuré fait référence à une liaison de [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) , ce qui n’est pas partie du.NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) est une des liaisons définies par le Bus de Service.

1. Dans l' **Explorateur de solutions**, double-cliquez sur le fichier App.config pour l’ouvrir dans l’éditeur de Visual Studio.

2. Dans le `<appSettings>` élément, remplacer les espaces réservés par le nom de votre espace de noms de service et les associations de sécurité de clé que vous avez copié dans une étape antérieure. 

1. Dans le `<system.serviceModel>` balises, ajouter un `<services>` élément. Vous pouvez définir plusieurs applications de Bus de Service dans un seul fichier de configuration. Toutefois, ce didacticiel ne définit qu’une seule.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Dans le `<services>` élément, ajoutez un `<service>` élément pour définir le nom du service.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Dans le `<service>` élément, définissez l’emplacement du contrat de point de terminaison, ainsi que le type de liaison pour le point de terminaison.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Le point de terminaison définit où le client recherche l’application hôte. Par la suite, le didacticiel utilise cette étape pour créer un URI qui expose entièrement de l’hôte par le biais de Bus de Service. La liaison déclare que nous utilisons TCP comme protocole pour communiquer avec le Bus de Service.

1. Dans le menu **Générer** , cliquez sur **Générer la Solution** pour vérifier l’exactitude de votre travail.

### <a name="example"></a>Exemple

Le code suivant illustre l’implémentation du contrat de service.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Le code suivant montre le format du fichier App.config associé à l’hôte de service de base.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-service-bus"></a>Héberger et exécuter un service Web de base pour enregistrer avec le Bus des services

Cette étape décrit l’exécution d’un service de Bus des services de base.

### <a name="to-create-the-service-bus-credentials"></a>Pour créer les informations d’identification du Bus des services

1. Dans `Main()`, créez deux variables de l’espace de noms de stockage et les associations de sécurité clés qui sont lus à partir de la fenêtre de console.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    La clé SAS servira ultérieurement pour accéder à votre projet de Service Bus. L’espace de noms est passé en tant que paramètre à `CreateServiceUri` pour créer un URI de service.

4. À l’aide d’un objet [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) , déclarer que vous utilisez une clé SAS en tant que le type d’informations d’identification. Ajoutez le code suivant directement après le code ajouté à la dernière étape.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="to-create-a-base-address-for-the-service"></a>Pour créer une adresse de base pour le service

1. Après le code ajouté à la dernière étape, créez un `Uri` instance pour l’adresse de base du service. Cet URI Spécifie le jeu de Bus des services, l’espace de noms et le chemin d’accès de l’interface de service.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    « sb » est une abréviation pour le schéma du Bus de Service et indique que nous utilisons TCP comme protocole. Il a été également précédemment indiqué dans le fichier de configuration lorsque [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) a été spécifié en tant que la liaison.
    
    Pour ce didacticiel, l’URI est `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="to-create-and-configure-the-service-host"></a>Pour créer et configurer l’hôte de service

1. Définir le mode de connexion à `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Le mode de connectivité décrit le protocole que le service utilise pour communiquer avec le Bus de Service ; HTTP ou TCP. À l’aide de la valeur par défaut `AutoDetect`, le service tente de se connecter au Service Bus sur TCP s’il est disponible et HTTP si TCP n’est pas disponible. Notez que ceci diffère du protocole du service spécifie pour la communication client. Ce protocole est déterminé par la liaison utilisée. Par exemple, un service peut utiliser la liaison de [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) , ce qui indique que son point de terminaison (exposée sur le Bus de Service) communique avec les clients via HTTP. Ce service peut spécifier **ConnectivityMode.AutoDetect** afin que le service communique avec le Service Bus sur TCP.

1. Créer l’hôte de service, à l’aide de l’URI créé plus haut dans cette section.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    L’hôte de service est l’objet WCF instancie le service. Ici, vous lui passez le type de service que vous souhaitez créer (un `EchoService` type) et également à l’adresse à laquelle vous voulez exposer le service.

1. En haut du fichier Program.cs, ajoutez des références à [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) et [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Dans `Main()`, configurer le point de terminaison pour activer l’accès public.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Cette étape indique le Bus de Service de votre application sont accessibles au public en examinant l’atome de Bus de Service d’alimentation pour votre projet. Si vous définissez **DiscoveryType** sur **privé**, un client est toujours en mesure d’accéder au service. Toutefois, le service n’apparaîtrait pas lorsqu’il recherche l’espace de noms du Bus de Service. Au lieu de cela, le client devra connaître le chemin d’accès du point de terminaison au préalable.

1. Appliquer les informations d’identification du service pour les points de terminaison de service définis dans le fichier App.config :

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Comme indiqué à l’étape précédente, vous avez pourriez déclaré plusieurs services et points de terminaison dans le fichier de configuration. Si vous aviez, ce code peut parcourir le fichier de configuration et de la recherche pour chaque point de terminaison auquel il s’applique à vos informations d’identification. Toutefois, dans le cadre de ce didacticiel, le fichier de configuration a un seul point de terminaison.

### <a name="to-open-the-service-host"></a>Pour ouvrir l’hôte de service

1. Ouvrez le service.

    ```
    host.Open();
    ```

1. Informer l’utilisateur que le service est en cours d’exécution et expliquez comment arrêter le service.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Lorsque vous avez terminé, fermez l’hôte de service.

    ```
    host.Close();
    ```

1. Appuyez sur **Ctrl + Maj + B** pour générer le projet.

### <a name="example"></a>Exemple

L’exemple suivant inclut le contrat de service et de la mise en oeuvre des étapes précédentes du didacticiel et héberge le service dans une application console. Compiler les éléments suivants dans un fichier exécutable nommé EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Créer un client WCF pour le contrat de service

L’étape suivante consiste à créer une application client de base Bus de Service et définir le contrat de service que vous allez implémenter dans les étapes ultérieures. Notez que la plupart de ces étapes ressemblent à la procédure utilisée pour créer un service : définition d’un contrat, la modification d’un fichier App.config de fichiers, à l’aide des informations d’identification pour vous connecter à un Bus de Service et ainsi de suite. Le code utilisé pour effectuer ces tâches est fourni dans l’exemple suivant la procédure.

1. Créer un nouveau projet dans la solution Visual Studio en cours pour le client en procédant comme suit :
    1. Dans l’Explorateur de solutions, dans la même solution qui contient le type de service, avec le bouton droit de la solution en cours (et non sur le projet), puis cliquez sur **Ajouter**. Puis cliquez sur **Nouveau projet**.
    2. Dans la boîte de dialogue **Ajouter un nouveau projet** , cliquez sur **Visual C#** (si **Visual C#** n’apparaît pas, regardez sous **Autres langages**), sélectionnez le modèle **Application Console** et nommez-la **EchoClient**.
    3. Cliquez sur **OK**.
<br />

1. Dans l’Explorateur de solutions, double-cliquez sur le fichier Program.cs dans le projet **EchoClient** pour l’ouvrir dans l’éditeur, s’il n’est pas déjà ouvert.

1. Modifier le nom d’espace de noms à partir de son nom par défaut de `EchoClient` à `Microsoft.ServiceBus.Samples`.

1. Installez le [package NuGet de Bus de Service](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Dans l’Explorateur de solutions, cliquez sur le projet **EchoClient** , puis cliquez sur **Manage NuGet Packages**. Cliquez sur l’onglet **Parcourir** , puis recherchez les `Microsoft Azure Service Bus`. Cliquez sur **installer**et accepter les conditions d’utilisation.

    ![][3]

1. Ajouter un `using` instruction pour l’espace de noms [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) dans le fichier Program.cs. 

    ```
    using System.ServiceModel;
    ```

1. Ajoutez la définition de contrat de service à l’espace de noms, comme illustré dans l’exemple suivant. Notez que cette définition est identique à la définition utilisée dans le projet de **Service** . Vous devez ajouter ce code en haut de la `Microsoft.ServiceBus.Samples` espace de noms.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Appuyez sur **Ctrl + Maj + B** pour générer le client.

### <a name="example"></a>Exemple

Le code suivant affiche l’état actuel du fichier Program.cs dans le projet EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurer le client WCF

Dans cette étape, vous créez un fichier App.config pour une application de client de base qui accède au service créé précédemment dans ce didacticiel. Ce fichier App.config définit le contrat, la liaison et le nom du point de terminaison. Le code utilisé pour effectuer ces tâches est fourni dans l’exemple suivant la procédure.

1. Dans l’Explorateur de solutions, dans le projet **EchoClient** , double-cliquez sur **App.config** pour ouvrir le fichier dans l’éditeur de Visual Studio.

2. Dans le `<appSettings>` élément, remplacer les espaces réservés par le nom de votre espace de noms de service et les associations de sécurité de clé que vous avez copié dans une étape antérieure.

1. Dans l’élément system.serviceModel, ajoutez un `<client>` élément.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Cette étape déclare que vous définissez une application de client WCF-style.

1. Dans le `client` élément, définissez le nom, le contrat et le type de liaison pour le point de terminaison.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Cette étape définit le nom du point de terminaison, le contrat défini dans le service et le fait que l’application cliente utilise TCP pour communiquer avec le Bus de Service. Le nom de point de terminaison est utilisé à l’étape suivante pour lier cette configuration de point de terminaison avec l’URI de service.

1. Cliquez sur **fichier**, puis sur **Enregistrer tout**.

## <a name="example"></a>Exemple

Le code suivant montre le fichier App.config pour le client de l’écho.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client-to-call-service-bus"></a>Implémenter le client WCF pour appeler le Service de Bus

Dans cette étape, vous implémentez une application de client de base qui accède au service que vous avez créé précédemment dans ce didacticiel. Comme pour le service, le client effectue la plupart les mêmes opérations pour accéder aux Bus de Service :

1. Définit le mode de connexion.
1. Crée l’URI qui localise le service hôte.
1. Définit les informations d’identification de sécurité.
1. Applique les informations d’identification pour la connexion.
1. Ouvre la connexion.
1. Effectue les tâches spécifiques à l’application.
1. Ferme la connexion.

Toutefois, une des principales différences est que l’application cliente utilise un canal pour se connecter à un Bus de Service, alors que le service utilise un appel à **ServiceHost**. Le code utilisé pour effectuer ces tâches est fourni dans l’exemple suivant la procédure.

### <a name="to-implement-a-client-application"></a>Pour implémenter une application de client

1. Définir le mode de connectivité pour **détecter automatiquement**. Ajoutez le code suivant à l’intérieur de la `Main()` méthode de l’application **EchoClient** .

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Définir des variables pour contenir les valeurs de l’espace de noms de service et la clé SAS qui sont lus à partir de la console.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Créez l’URI qui définit l’emplacement de l’hôte dans votre projet de Service Bus.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Créer l’objet d’informations d’identification pour votre point de terminaison de service espace de noms.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Création de la fabrique de canaux qui se charge de la configuration décrite dans le fichier App.config.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Une fabrique de canaux est un objet WCF qui crée un canal par lequel communiquent les applications clientes et de service.

1. Appliquer les informations d’identification de Service Bus.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Créer et ouvrir le canal vers le service.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Écrire l’interface utilisateur de base et les fonctionnalités de l’écho.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Notez que le code utilise l’instance de l’objet de canal en tant que proxy pour le service.

1. Fermez le canal et l’usine.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## <a name="to-run-the-applications"></a>Pour exécuter les applications

1. Appuyez sur **Ctrl + Maj + B** pour générer la solution. Cela génère le projet client et le projet de service que vous avez créé dans les étapes précédentes.

2. Avant d’exécuter l’application cliente, vous devez vous assurer que l’application de service s’exécute. Dans l’Explorateur de solutions dans Visual Studio, avec le bouton droit de la solution de **EchoService** , puis cliquez sur **Propriétés**.

3. Dans la boîte de dialogue de propriétés de solution, cliquez sur le **Projet de démarrage**, puis cliquez sur le bouton de **plusieurs projets de démarrage** . Assurez-vous que **EchoService** apparaît en premier dans la liste. 

4. Définir la zone **d’Action** pour les projets à la fois le **EchoService** et **EchoClient** pour **Démarrer**.

    ![][5]

5. Cliquez sur **dépendances du projet**. Dans la zone **projets** , sélectionnez **EchoClient**. Dans la zone **dépend** , assurez-vous que **EchoService** est activée.

    ![][6]

6. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés** .

7. Appuyez sur **F5** pour exécuter les deux projets.

8. Les deux fenêtres de la console, ouvrez et vous demande le nom d’espace de noms. Le service doit s’exécuter en premier, dans la fenêtre de console **EchoService** , entrez l’espace de noms et puis appuyez sur **entrée**.

9. Ensuite, vous êtes invité de votre clé SAS. Entrez la clé SAS et appuyez sur ENTRÉE.

    Voici un exemple de sortie à partir de la fenêtre de console. Notez que les valeurs fournies ici sont par exemple uniquement.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    L’application de service s’imprime sur la fenêtre de console l’adresse à laquelle il est à l’écoute, comme indiqué dans l’exemple suivant.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. Dans la fenêtre de console **EchoClient** , entrez les mêmes informations que vous avez précédemment entrées pour l’application de service. Suivez les étapes précédentes pour entrer les valeurs de clé de SAS et le même espace de noms de service pour l’application cliente.

11. Après la saisie de ces valeurs, le client ouvre un canal au service et vous invite à entrer du texte, comme dans l’exemple de sortie de console suivante.

    `Enter text to echo (or [Enter] to exit):` 

    Entrez du texte pour l’envoyer à l’application de service et appuyez sur ENTRÉE. Ce texte est envoyé au service par le biais de l’opération de service Echo et s’affiche dans la fenêtre de console de service comme dans l’exemple de sortie suivant.

    `Echoing: My sample text`

    L’application cliente reçoit la valeur de retour de la `Echo` opération, qui est le texte d’origine et l’imprime dans sa fenêtre de console. Voici un exemple de sortie de la fenêtre de console du client.

    `Server echoed: My sample text`

12. Vous pouvez continuer à envoyer des messages texte à partir du client au service de cette manière. Lorsque vous avez terminé, appuyez sur entrée dans les fenêtres de console client et le service à la fin des deux applications.

## <a name="example"></a>Exemple

L’exemple suivant montre comment créer une application cliente, comment appeler les opérations du service et fermer le client après que l’appel de l’opération est terminée.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a montré comment créer un client de Bus de Service application et le service en utilisant les fonctionnalités de « relais » de Bus de Service. Pour obtenir un didacticiel similaire qui utilise le Bus de Service [messagerie](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging), consultez le [Service Bus : négociée dans un didacticiel de .NET Messaging](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Pour en savoir plus sur le Bus de Service, consultez les rubriques suivantes.

- [Vue d’ensemble de la messagerie Bus des services](../service-bus-messaging/service-bus-messaging-overview.md)
- [Principes de base de Bus des services](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Architecture de Bus de service](../service-bus-messaging/service-bus-architecture.md)

[Azure classic portal]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
