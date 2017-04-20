<properties
   pageTitle="Vue d’ensemble de communication Services fiable | Microsoft Azure"
   description="Vue d’ensemble du modèle de communication des Services fiables, y compris les écouteurs d’ouverture sur les services, résolution des points de terminaison et de la communication entre les services."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="how-to-use-the-reliable-services-communication-apis"></a>Comment faire pour utiliser l’API de communication des Services fiables

Azure Fabric de Service en tant que plate-forme est complètement indépendante sur la communication entre les services. Tous les protocoles et les piles sont acceptables, UDP à HTTP. C’est au développeur de service pour choisir la façon dont les services doivent communiquer. L’infrastructure d’application des Services fiables fournit des piles de communication intégrées, ainsi que des API que vous pouvez utiliser pour créer vos composants de communication personnalisé. 

## <a name="set-up-service-communication"></a>Configurer la communication du service

L’API de Services fiables utilise une interface simple pour la communication du service. Pour ouvrir un point de terminaison de votre service, simplement implémenter cette interface :

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Vous pouvez ensuite ajouter votre implémentation d’écouteur de communication en la renvoyant dans une substitution de la méthode basée sur un service de classe.

Pour les services sans état :

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```

Pour les services de l’état :

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Dans les deux cas, vous retournez une collection d’écouteurs. Cela permet à votre service écouter sur plusieurs points de terminaison, éventuellement à l’aide de différents protocoles, à l’aide de plusieurs écouteurs. Par exemple, peut avoir un écouteur HTTP et un écouteur de WebSocket distinct. Chaque écouteur Obtient un nom et la collection résultante de *nom : adresse* paires est représenté sous la forme d’un objet JSON lorsqu’un client demande les adresses d’écoute pour une instance de service ou d’une partition.

Dans un service sans état, la substitution retourne une collection de ServiceInstanceListeners. Un ServiceInstanceListener contient une fonction pour créer un ICommunicationListener et lui donne un nom. Pour les services de l’état, la substitution retourne une collection de ServiceReplicaListeners. Cela est légèrement différente de son homologue sans état, car un ServiceReplicaListener a la possibilité d’ouvrir une ICommunicationListener sur les doubles secondaires. Non seulement vous pouvez utiliser plusieurs écouteurs de communication dans un service, mais vous pouvez également spécifier quels écouteurs acceptent les demandes sur les doubles secondaires et ceux qui écoute uniquement réplicas principales.

Par exemple, vous pouvez avoir un ServiceRemotingListener qui accepte les appels RPC uniquement sur les doubles primaires, et un écouteur de la deuxième, personnalisé qui accepte la lecture demande sur les doubles secondaires sur HTTP :

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [AZURE.NOTE] Lorsque vous créez plusieurs écouteurs d’un service, chaque port d’écoute **doit** être donné à un nom unique.

Enfin, décrivez les points de terminaison qui sont requis pour le service dans le [manifeste de service](service-fabric-application-model.md) sous la section sur les points de terminaison.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Le récepteur de communication peut accéder aux ressources de point de terminaison que lui sont allouées à partir de la `CodePackageActivationContext` dans les `ServiceContext`. Le port d’écoute peut alors commencer à écouter les demandes lorsqu’il est ouvert.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] Ressources du point de terminaison sont communes au package service tout entier, et ils sont alloués par le Service Fabric lorsque le package de service est activé. Plusieurs réplicas de service hébergés dans un ServiceHost même peuvent partager le même port. Cela signifie que le récepteur de communication doit prendre en charge le partage de port. Il est recommandé d’effectuer cette opération pour le récepteur de communication à utiliser la partition ID et l’ID de réplica/instance lorsqu’il génère l’adresse d’écoute.

### <a name="service-address-registration"></a>Enregistrement d’adresse de service

Un service système appelé *Naming Service* s’exécute sur des clusters de Service Fabric. Le Service d’attribution de noms est un Registre pour les services et leurs adresses de chaque instance ou un réplica du service est à l’écoute. Lors de la `OpenAsync` méthode d’un `ICommunicationListener` se termine, son retour valeur enregistré dans le Service d’attribution de noms. Cette valeur de retour qui est publiée dans le Service d’attribution de noms est une chaîne dont la valeur peut être n’importe quoi du tout. Valeur de chaîne est ce que verront les clients lorsqu’ils demanderont une adresse pour le service à partir du Service d’attribution de noms.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);
                        
    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
            
    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));
    
    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```

TISSU de service fournit des API qui permet aux clients et aux autres services puis demander cette adresse par nom de service. Ceci est important car l’adresse de service n’est pas statique. Les services sont déplacés dans le cluster à des fins de disponibilité et de l’équilibrage de la ressource. C’est le mécanisme qui permet aux clients de résoudre l’adresse d’écoute pour un service.

> [AZURE.NOTE] Pour une vue d’ensemble complète comment écrire un `ICommunicationListener`, consultez [services API de Web Service Fabric avec l’auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi.md)

## <a name="communicating-with-a-service"></a>Communication avec un service
L’API de Services fiables fournit les bibliothèques suivantes pour écrire des clients qui communiquent avec les services.

### <a name="service-endpoint-resolution"></a>Résolution de point de terminaison de service
La première étape de la communication avec un service est pour résoudre une adresse de point de terminaison de la partition ou l’instance du service que vous souhaitez parler. Le `ServicePartitionResolver` classe utilitaire est une primitive de base qui permet aux clients de déterminer le point de terminaison d’un service en cours d’exécution. Dans la terminologie du Service Fabric, le processus de détermination du point de terminaison d’un service est appelé pour la *résolution de point de terminaison de service*.

Pour vous connecter à des services au sein d’un cluster, `ServicePartitionResolver` peuvent être créés à l’aide des paramètres par défaut. Il s’agit de l’utilisation recommandée pour la plupart des cas :

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```

Pour vous connecter à des services dans un cluster différent, un `ServicePartitionResolver` peuvent être créés avec un ensemble de points de terminaison de passerelle de cluster. Notez que les points de terminaison de passerelle sont simplement différents points de terminaison pour se connecter à un même cluster. Par exemple :

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Vous pouvez également `ServicePartitionResolver` peut être attribuée à une fonction de création d’un `FabricClient` à utiliser en interne : 
 
```csharp
public delegate FabricClient CreateFabricClientDelegate();
```

`FabricClient`est l’objet qui est utilisé pour communiquer avec le cluster Service Fabric pour diverses opérations de gestion sur le cluster. Cela est utile lorsque vous souhaitez davantage de contrôle sur la façon de `ServicePartitionResolver` interagit avec votre cluster. `FabricClient`effectue la mise en cache en interne et est généralement coûteux à créer, il est donc important de réutiliser `FabricClient` instances autant que possibles. 

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```

Une méthode de résolution est ensuite utilisée pour récupérer l’adresse d’un service ou d’une partition de service pour les services partitionnées.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```

Une adresse de service peut être résolue facilement en utilisant une `ServicePartitionResolver`, mais plus de travail est nécessaire pour garantir l’adresse résolue peut être utilisé correctement. Votre client devra détecter si la tentative de connexion a échoué en raison d’une erreur transitoire et peut être tentée (par exemple, service déplacé ou est temporairement indisponible), ou une erreur permanente (par exemple, le service a été supprimé ou la ressource demandée n’existe plus). Réplicas ou les instances de service peuvent déplacer de nœud à nœud à tout moment pour plusieurs raisons. L’adresse de service résolue par le biais de `ServicePartitionResolver` peut être périmée au moment où votre code client tente de se connecter. Dans ce cas à nouveau le client devez résoudre l’adresse. Fournissant la précédente `ResolvedServicePartition` indique que le programme de résolution a besoin pour essayer à nouveau au lieu de simplement récupérer une adresse en mémoire cache.

En règle générale, le code client ne doit pas travailler avec le `ServicePartitionResolver` directement. Il est créé et transmis à des fabriques de client de communication de l’API de Services fiables. Les fabriques utilisent le programme de résolution en interne pour générer un objet client qui peut être utilisé pour communiquer avec les services.

### <a name="communication-clients-and-factories"></a>Fabriques et clients de communication

La bibliothèque de fabrique de communication implémente un modèle de réessayer de gestion des erreurs typique qui facilite la nouvelle tentative de connexions aux points de terminaison de service résolu. La bibliothèque en usine fournit le mécanisme de reprise lorsque vous donnez les gestionnaires d’erreurs.

`ICommunicationClientFactory`définit l’interface de base mis en oeuvre par une fabrique de client de communication qui produit des clients qui peuvent communiquer avec un service de Fabric du Service. La mise en oeuvre de la CommunicationClientFactory dépend de la pile de communication utilisée par le service de Fabric du Service où le client souhaite communiquer. L’API de Services fiables fournit un `CommunicationClientFactoryBase<TCommunicationClient>`. Cela fournit une implémentation de base de la `ICommunicationClientFactory` de l’interface et effectue des tâches qui sont communes à toutes les piles de communication. (Ces tâches comprennent l’à l’aide d’une `ServicePartitionResolver` pour déterminer le point de terminaison du service). Généralement les clients implémenter la classe abstraite CommunicationClientFactoryBase pour gérer la logique spécifique à la pile de communication.

Le client de communication reçoit une adresse simplement et l’utilise pour se connecter à un service. Le client peut utiliser le protocole qu’il souhaite.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```

Le client factory est principalement responsable de la création de clients de communication. Pour les clients qui ne pas maintenir une connexion permanente, comme un client HTTP, la fabrique doit uniquement créer et retourner le client. Autres protocoles de maintient une connexion permanente, telles que certains protocoles binaires, doivent également être validés par la fabrique afin de déterminer si la connexion doit être recréé.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```

Enfin, un gestionnaire d’exceptions est reponsible pour déterminer l’action à entreprendre lorsqu’une exception se produit. Les exceptions sont placé dans la catégorie **reproductible** et **non reproductible**. 

 - Les exceptions **non reproductible** simplement obtenir levées à nouveau à l’appelant. 
 - Les exceptions **Retriable** sont encore classées dans **transitoire** et **non transitoire**.
  - Les exceptions **temporaires** sont ceux qui peut simplement être tentée sans résoudre de nouveau l’adresse de point de terminaison de service. Citons les problèmes réseau passagers ou des réponses d’erreur de service autres que ceux qui indiquent que l’adresse de point de terminaison de service n’existe pas. 
  - Les exceptions **non transitoires** sont ceux qui nécessitent de l’adresse de point de terminaison de service pour être à nouveau résolues. Ces bibliothèques incluent des exceptions qui indiquent que le point de terminaison de service n’a pas pu être atteint, indiquant que le service a déplacé vers un autre nœud. 

Le `TryHandleException` prend une décision à propos d’une exception donnée. Si elle **ne sait pas** que les décisions à prendre concernant une exception, il doit retourner **false**. Si elle **sait** faire la décision, il doit définir le résultat en conséquence et retourner la **valeur true**.
 
```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
### <a name="putting-it-all-together"></a>Vue d’ensemble
Avec une `ICommunicationClient`, `ICommunicationClientFactory`, et `IExceptionHandler` basé sur un protocole de communication, un `ServicePartitionClient` est encapsule tous les éléments et fournit la boucle de résolution d’adresse partition de gestion des erreurs et de services autour de ces composants.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```

## <a name="next-steps"></a>Étapes suivantes
 - Consultez un exemple de communication HTTP entre les services dans un [exemple de projet sur GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Appels de procédure distante avec l’accès distant des Services fiables](service-fabric-reliable-services-communication-remoting.md)

 - [Web API qui utilise OWIN dans des Services fiables](service-fabric-reliable-services-communication-webapi.md)

 - [Communication WCF à l’aide des Services fiables](service-fabric-reliable-services-communication-wcf.md)
