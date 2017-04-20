<properties
   pageTitle="Pile de communication fiable Services WCF | Microsoft Azure"
   description="La pile de communication WCF intégrée dans Fabric de Service fournit une communication WCF-service client pour des Services fiables."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="bharatn"/>

# <a name="wcf-based-communication-stack-for-reliable-services"></a>Pile de communication basée sur WCF pour les Services fiables
L’infrastructure des Services fiables permet aux auteurs de service permet de choisir la pile de communication qu’ils souhaitent utiliser pour leur service. Il peuvent s’intégrer dans la pile de communication de leur choix via **ICommunicationListener** renvoyée par les méthodes [CreateServiceReplicaListeners ou CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) . Le framework fournit une implémentation de la pile de communication basée sur Windows Communication Foundation (WCF) pour les auteurs de service qui souhaitent utiliser la communication basée sur WCF.

## <a name="wcf-communication-listener"></a>Récepteur de Communication WCF
L’implémentation spécifique à WCF de **ICommunicationListener** est fournie par la classe **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** .

Moins de dire que nous avons un contrat de service de type`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Nous pouvons créer un écouteur de communication WCF dans le service de la manière suivante.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Écriture de clients pour la pile de communication WCF
Pour écrire des clients de communiquer avec les services à l’aide de WCF, le framework propose **WcfClientCommunicationFactory**, qui est l’implémentation spécifique à WCF de [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Le canal de communication WCF est accessible à partir de la **WcfCommunicationClient** créée par le **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Code client peut utiliser le **WcfCommunicationClientFactory** et le **WcfCommunicationClient** qui implémente **ServicePartitionClient** pour déterminer le point de terminaison de service et de communiquer avec le service.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
>[AZURE.NOTE] La valeur par défaut ServicePartitionResolver suppose que le client est en cours d’exécution dans le même cluster que le service. Si c’est pas le cas, créez un objet ServicePartitionResolver et passez les points de terminaison de connexion de cluster.

## <a name="next-steps"></a>Étapes suivantes
* [Appel de procédure distante avec l’accès distant des Services fiables](service-fabric-reliable-services-communication-remoting.md)

* [Web API avec OWIN de Services fiables](service-fabric-reliable-services-communication-webapi.md)

* [Sécurisation des communications pour les Services fiables](service-fabric-reliable-services-secure-communication.md)
