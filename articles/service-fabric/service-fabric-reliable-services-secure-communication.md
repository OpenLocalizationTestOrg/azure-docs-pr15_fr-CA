<properties
   pageTitle="Aider les communications sécurisées concernant les services de Fabric de Service | Microsoft Azure"
   description="Vue d’ensemble de la sécurisation des communications pour les services fiables qui s’exécutent dans un cluster d’Azure Fabric de Service."
   services="service-fabric"
   documentationCenter=".net"
   authors="suchiagicha"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/06/2016"
   ms.author="suchiagicha"/>

# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Aide de communication sécurisée pour les services dans Azure Fabric de Service

La sécurité est un des aspects plus importants de la communication. L’infrastructure d’application des Services fiables fournit quelques piles de communication intégrés et des outils que vous pouvez utiliser pour améliorer la sécurité. Cet article sera plus tard comment améliorer la sécurité lorsque vous utilisez l’accès distant du service et la pile de communication de Windows Communication Foundation (WCF).

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Aider à sécuriser un service lorsque vous utilisez l’accès distant du service

Nous utiliserons un [exemple](service-fabric-reliable-services-communication-remoting.md) existant qui explique comment configurer la communication à distance pour les services fiables. Pour aider à sécuriser un service lorsque vous utilisez l’accès distant du service, procédez comme suit :

1. Créez une interface, `IHelloWorldStateful`, qui définit les méthodes qui seront disponibles pour un appel de procédure distante sur votre service. Votre service utilisera `FabricTransportServiceRemotingListener`, qui est déclarée dans le `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` espace de noms. Il s’agit d’un `ICommunicationListener` mise en œuvre qui fournit des fonctionnalités de communication à distance.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. Ajouter des paramètres de l’écouteur et des informations d’identification de sécurité.

    Assurez-vous que le certificat que vous souhaitez utiliser pour aider à sécuriser vos communications service est installé sur tous les nœuds du cluster. Il existe deux méthodes que vous pouvez fournir des paramètres de l’écouteur et des informations d’identification de sécurité :

    1. Leur donner directement dans le code de service :

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listenerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. Les fournir à l’aide d’un [package de configuration](service-fabric-application-model.md):

        Ajouter un `TransportSettings` section dans le fichier settings.xml.

        ```xml
        <!--Section name should always end with "TransportSettings".-->
        <!--Here we are using a prefix "HelloWorldStateful".-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        Dans ce cas, le `CreateServiceReplicaListeners` méthode se présente comme suit :

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Si vous ajoutez un `TransportSettings` section dans le fichier settings.xml sans préfixe, `FabricTransportListenerSettings` charge tous les paramètres à partir de cette section par défaut.

         ```xml
         <!--"TransportSettings" section without any prefix.-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         Dans ce cas, le `CreateServiceReplicaListeners` méthode se présente comme suit :

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. Lorsque vous appelez des méthodes sur un service sécurisé à l’aide de la pile de communication à distance, au lieu d’utiliser la `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` classe pour créer un proxy de service, utilisez `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Passez dans `FabricTransportSettings`, qui contient `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si le code client s’exécute dans le cadre d’un service, vous pouvez charger `FabricTransportSettings` dans le fichier settings.xml. Créez une section TransportSettings qui est semblable au code de service, comme indiqué plus haut. Apportez les modifications suivantes au code client :

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si le client ne s’exécute pas dans le cadre d’un service, vous pouvez créer un fichier client_name.settings.xml dans le même emplacement que celui où le client_name.exe est. Créez une section TransportSettings dans ce fichier.

    Similaire au service, si vous ajoutez un `TransportSettings` section sans préfixe dans client, settings.xml/client_name.settings.xml, `FabricTransportSettings` charge tous les paramètres à partir de cette section par défaut.

    Dans ce cas, le code précédent est même encore simplifié :  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Aider à sécuriser un service lorsque vous utilisez une pile de communication basée sur WCF

Nous utiliserons un [exemple](service-fabric-reliable-services-communication-wcf.md) existant qui explique comment configurer une pile de communication basée sur WCF pour les services fiables. Pour aider à sécuriser un service lorsque vous utilisez une pile de communication basée sur WCF, procédez comme suit :

1. Pour le service, vous avez besoin pour aider à sécuriser l’écouteur de communication WCF (`WcfCommunicationListener`) que vous créez. Pour ce faire, modifiez le `CreateServiceReplicaListeners` méthode.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```

2. Dans le client, le `WcfCommunicationClient` classe qui a été créé dans précédent [exemple](service-fabric-reliable-services-communication-wcf.md) reste inchangé. Mais vous devez remplacer le `CreateClientAsync` méthode de `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Utilisez `SecureWcfCommunicationClientFactory` pour créer un client de communication WCF (`WcfCommunicationClient`). Utilisez le client pour appeler des méthodes de service.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Web API avec OWIN de Services fiables](service-fabric-reliable-services-communication-webapi.md)
