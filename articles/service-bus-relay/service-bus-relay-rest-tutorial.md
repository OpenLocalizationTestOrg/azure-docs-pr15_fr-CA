<properties
    pageTitle="Utilisation du didacticiel Service Bus reste de relais de messagerie | Microsoft Azure"
    description="Créer une simple application hôte relais Bus de Service qui expose une interface basée sur REST."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-rest-tutorial"></a>Didacticiel du reste de relais de Bus de service

Ce didacticiel explique comment créer une simple application d’hôte de Bus de Service qui expose une interface basée sur REST. RESTE permet à un client web, comme un navigateur web, d’accéder à l’API de Bus de Service via les demandes HTTP.

Ce didacticiel utilise le reste de Windows Communication Foundation (WCF) modèle de programmation pour construire un service reste sur le Bus de Service. Pour plus d’informations, consultez [Modèle de programmation WCF reste](https://msdn.microsoft.com/library/bb412169.aspx) et la [conception et la mise en œuvre de Services](https://msdn.microsoft.com/library/ms729746.aspx) dans la documentation WCF.

## <a name="step-1-create-a-service-namespace"></a>Étape 1 : Créer un espace de noms du service

La première étape consiste à créer un espace de noms et obtenir une clé de Signature de l’accès partagé (SAS). Un espace de noms fournit une limite d’application pour chaque application exposé par le biais de Bus de Service. Une clé SAS est générée automatiquement par le système lors de la création d’un espace de noms du service. La combinaison d’espace de noms de service et de la clé SAS fournit les informations d’identification pour le Bus de Service authentifier l’accès à une application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-service-bus"></a>Étape 2 : Définition d’un contrat de service WCF de base reste à utiliser avec le Bus des services

Comme avec d’autres services de Bus de Service, lorsque vous créez un service de type REST, vous devez définir le contrat. Le contrat spécifie les opérations que l’hôte prend en charge. Une opération de service peut être considérée comme une méthode de service web. Les contrats sont créés en définissant une interface C++, C# ou Visual Basic. Chaque méthode de l’interface correspond à une opération de service spécifique. L’attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) doit être appliqué à chaque interface, et l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) doit être appliqué à chaque opération. Si une méthode dans une interface qui possède le [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) n’a pas l' [attribut OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), cette méthode n’est pas exposée. Le code utilisé pour effectuer ces tâches est indiqué dans l’exemple suivant la procédure.

La principale différence entre un contrat de Bus des services de base et d’un contrat de type REST est l’ajout d’une propriété à l' [attribut OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Cette propriété vous permet de mapper une méthode dans l’interface à une méthode de l’autre côté de l’interface. Dans ce cas, nous allons utiliser [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) à lier une méthode HTTP GET. Ainsi, le Service Bus avec précision comment récupérer et interpréter les commandes envoyées à l’interface.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Pour créer un contrat de Service Bus grâce à une interface

1. Ouvrez Visual Studio en tant qu’administrateur : cliquez sur le programme dans le menu **Démarrer** , puis cliquez sur **Exécuter en tant qu’administrateur**.

2. Créer un nouveau projet d’application console. Cliquez sur le menu **fichier** et sélectionnez **Nouveau**, puis sélectionnez le **projet**. Dans la boîte de dialogue **Nouveau projet** , cliquez sur **Visual C#**, sélectionnez le modèle **Application Console** et nommez-le **ImageListener**. Utilisez **l’emplacement**par défaut. Cliquez sur **OK** pour créer le projet.

3. Pour un projet C#, Visual Studio crée un `Program.cs` fichier. Cette classe contient un vide `Main()` méthode, requis pour un projet d’application console générer correctement.

4. Ajoutez des références aux Bus de Service et **System.ServiceModel.dll** au projet en installant le package NuGet de Bus de Service. Ce package ajoute automatiquement des références pour les bibliothèques de Bus des services, ainsi que le WCF **System.ServiceModel**. Dans l’Explorateur de solutions, cliquez sur le projet **ImageListener** , puis cliquez sur **Manage NuGet Packages**. Cliquez sur l’onglet **Parcourir** , puis recherchez les `Microsoft Azure Service Bus`. Cliquez sur **installer**et accepter les conditions d’utilisation.

5. Vous devez explicitement ajouter une référence à **System.ServiceModel.Web.dll** au projet :

    une barre d’outils. Dans l’Explorateur de solutions, cliquez sur le dossier **références** sous le dossier du projet et puis cliquez sur **Ajouter une référence**.

    b. Dans la boîte de dialogue **Ajouter une référence** , cliquez sur l’onglet de **Framework** sur le côté gauche et dans la zone **Rechercher** , tapez **System.ServiceModel.Web**. Activez la case à cocher **System.ServiceModel.Web** , puis cliquez sur **OK**.

6. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier Program.cs.

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) est l’espace de noms qui permet l’accès par programme aux fonctionnalités de base de WCF. Bus de service utilise la plupart des objets et des attributs de WCF pour définir des contrats de service. Vous utiliserez cet espace de noms dans la plupart de vos applications de relais de Bus de Service. De même, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) permet de définir le canal, qui est l’objet par le biais duquel vous communiquez avec le Service Bus et le navigateur web client. Enfin, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contient les types qui vous permettent de créer des applications basées sur le web.

7. Renommer le `ImageListener` **Microsoft.ServiceBus.Samples**de l’espace de noms.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Directement après l’accolade ouvrante de la déclaration d’espace de noms défini une nouvelle interface nommée **IImageContract** et appliquer l’attribut **ServiceContractAttribute** à l’interface avec une valeur de `http://samples.microsoft.com/ServiceModel/Relay/`. La valeur de l’espace de noms est différent de l’espace de noms que vous utilisez dans la portée de votre code. La valeur de l’espace de noms est utilisée comme un identificateur unique pour ce contrat et doit avoir des informations de version. Pour plus d’informations, consultez le [Service de contrôle de version](http://go.microsoft.com/fwlink/?LinkID=180498). Spécification explicite de l’espace de noms empêche l’ajout d’un nom de contrat la valeur d’espace de noms par défaut.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. Dans le `IImageContract` d’interface, de déclarer une méthode pour la seule opération la `IImageContract` expose dans l’interface de contrat et s’appliquent le `OperationContractAttribute` d’attribut à la méthode que vous souhaitez exposer comme faisant partie du contrat public des Bus de Service.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. Dans l’attribut **OperationContract** , ajoutez la valeur de **WebGet** .

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    Cela permet de Bus de Service pour router les demandes HTTP GET pour `GetImage`et de traduire des valeurs de retour de `GetImage` dans une réponse HTTP GETRESPONSE. Plus loin dans le didacticiel, vous utiliserez un navigateur web pour accéder à cette méthode et d’afficher l’image dans le navigateur.

11. Directement après le `IImageContract` définition, déclarez un canal qui hérite à la fois la `IImageContract` et `IClientChannel` interfaces.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    Un canal est l’objet WCF par le biais duquel le service et le client passent des informations entre eux. Par la suite, vous allez créer le canal dans votre application hôte. Bus de service utilise ensuite cette chaîne pour transmettre les requêtes HTTP GET à partir du navigateur à votre implémentation **GetImage** . Bus de service utilise également le canal à prendre la valeur de retour **GetImage** et la traduire en un GETRESPONSE HTTP du navigateur client.

12. Dans le menu **Générer** , cliquez sur **Générer la Solution** pour vérifier l’exactitude de votre travail jusqu'à présent.

### <a name="example"></a>Exemple

Le code suivant montre une interface de base qui définit un contrat de Service Bus.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Étape 3 : Mettre en œuvre un contrat de service WCF de base reste à utiliser le Bus de Service

Création d’un service de Bus de Service reste style nécessite que vous créiez d’abord le contrat, qui est défini à l’aide d’une interface. L’étape suivante consiste à implémenter l’interface. Cela implique la création d’une classe nommée **ImageService** qui implémente l’interface **IImageContract** définie par l’utilisateur. Après avoir implémenté le contrat, vous configurer l’interface à l’aide d’un fichier App.config. Le fichier de configuration contient les informations nécessaires pour l’application, telles que le nom du service, le nom de contrat et le type de protocole utilisé pour communiquer avec le Bus de Service. Le code utilisé pour effectuer ces tâches est fourni dans l’exemple suivant la procédure.

Comme précédemment, il existe très peu de différence entre la mise en œuvre d’un contrat de type REST et d’un contrat de Bus des services de base.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Pour implémenter un contrat de Bus des services de type REST

1. Créer une nouvelle classe nommée **ImageService** directement après la définition de l’interface **IImageContract** . La classe **ImageService** implémente l’interface **IImageContract** .

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Similaire à d’autres implémentations de l’interface, vous pouvez implémenter la définition dans un autre fichier. Toutefois, dans le cadre de ce didacticiel, la mise en oeuvre s’affiche dans le même fichier que la définition d’interface et de `Main()` méthode.

2. Appliquer l’attribut [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à la classe **IImageService** pour indiquer que la classe est une implémentation d’un contrat WCF.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Comme mentionné précédemment, cet espace de noms n’est pas un espace de noms traditionnel. Au lieu de cela, elle est la partie de l’architecture WCF qui identifie le contrat. Pour plus d’informations, consultez la rubrique [Noms de contrat de données](https://msdn.microsoft.com/library/ms731045.aspx) dans la documentation WCF.

3. Ajouter une image .jpg à votre projet.  

    Il s’agit d’une image que le service s’affiche dans le navigateur de réception. Droit sur votre projet, puis cliquez sur **Ajouter**. Cliquez sur **Un élément existant**. Utilisez la boîte de dialogue **Ajouter un élément existant** pour naviguer jusqu'à un .jpg approprié, puis cliquez sur **Ajouter**.

    Lorsque vous ajoutez le fichier, assurez-vous que **Tous les fichiers** est sélectionnée dans la liste déroulante à côté du **nom de fichier :** champ. Le reste de ce didacticiel suppose que le nom de l’image est « image.jpg ». Si vous avez un fichier différent, vous devrez renommer l’image ou modifiez votre code pour compenser.

4. Pour vous assurer que le service en cours d’exécution peut trouver le fichier de l’image, dans **L’Explorateur de solutions** , cliquez droit sur le fichier image, puis cliquez sur **Propriétés**. Dans le volet de **Propriétés** , la valeur **Copy to Output Directory** **Copier si plus récent**.

5. Ajoutez une référence à l’assembly **System.Drawing.dll** au projet et également ajouter les éléments suivants associés `using` instructions.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. Dans la classe **ImageService** , ajoutez le constructeur suivant qui charge la bitmap et se prépare à envoyer au navigateur client.

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. Directement après le code précédent, ajoutez la méthode **GetImage** suivante dans la classe **ImageService** pour renvoyer un message HTTP qui contient l’image.

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    Cette implémentation utilise un **MemoryStream** pour extraire l’image et le préparer pour la lecture en continu vers le navigateur. Démarre la position du flux à zéro, déclare le contenu du flux au format jpeg et diffuse les informations.

8. Dans le menu **Générer** , cliquez sur **Générer la Solution**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Pour définir la configuration pour l’exécution du service web sur le Bus de Service

1. Dans l' **Explorateur de solutions**, double-cliquez sur **App.config** pour l’ouvrir dans l’éditeur de Visual Studio.

    Le fichier **App.config** ressemble à un fichier de configuration WCF et inclut le nom du service, le point de terminaison (autrement dit, l’emplacement expose des Bus de Service pour les clients et hôtes de communiquer entre eux) et la liaison (le type de protocole utilisé pour communiquer). La principale différence est que le point de terminaison de service configuré fait référence à une liaison de [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) , ce qui n’est pas partie du.NET Framework.

2. Le `<system.serviceModel>` XML est un élément WCF qui définit un ou plusieurs services. Ici, il est utilisé pour définir le nom du service et le point de terminaison. Au bas de la `<system.serviceModel>` élément (mais toujours dans `<system.serviceModel>`), ajoutez un `<bindings>` élément possédant le contenu suivant. Cette opération définit les liaisons utilisées dans l’application. Vous pouvez définir plusieurs liaisons, mais pour ce didacticiel vous définissez qu’une seule.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Cette étape définit une liaison de Service Bus [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) avec **relayClientAuthenticationType** défini sur **Aucun**. Ce paramètre indique qu’un point de terminaison à l’aide de cette liaison ne requiert pas une information d’identification client.

3. Après le `<bindings>` élément, ajoutez un `<services>` élément. Comme pour les liaisons, vous pouvez définir plusieurs services dans un seul fichier de configuration. Toutefois, dans le cadre de ce didacticiel, vous ne définissez qu’un seul.

    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Cette étape configure un service qui utilise le précédemment définie par défaut **webHttpRelayBinding**. Il utilise également la valeur par défaut **sbTokenProvider**, qui est définie dans l’étape suivante.

4. Après le `<services>` élément, créer une `<behaviors>` élément avec le contenu suivant, en remplaçant « SAS_KEY » avec la clé de *Signature de l’accès partagé* (SAS) obtenu à partir du [portail Azure][].

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

5. Toujours dans App.config, dans le `<appSettings>` élément, remplacer la connexion de toute valeur de chaîne avec la chaîne de connexion que vous avez précédemment obtenu à partir du portail. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. Dans le menu **Générer** , cliquez sur **Générer la Solution** pour générer la solution entière.

### <a name="example"></a>Exemple

Le code suivant illustre l’implémentation de service et de contrat d’un service qui est en cours d’exécution sur le Bus de Service à l’aide de la liaison de **WebHttpRelayBinding** basée sur REST.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

L’exemple suivant montre le fichier App.config associé au service.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-service-bus"></a>Étape 4 : Héberger le service WCF de basée sur REST pour utiliser le Service de Bus

Cette étape décrit comment exécuter un service web à l’aide d’une application de console sur le Bus de Service. Une liste complète du code écrit dans cette étape est fournie dans l’exemple suivant la procédure.

### <a name="to-create-a-base-address-for-the-service"></a>Pour créer une adresse de base pour le service

1. Dans le `Main()` déclaration de fonction, créez une variable pour stocker l’espace de noms de votre projet de Service Bus. Veillez à remplacer `yourNamespace` avec le nom de l’espace de noms service que vous avez créé précédemment.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    Bus de service utilise le nom de votre espace de noms pour créer un URI unique.

2. Créer un `Uri` instance pour l’adresse de base du service qui est basé sur l’espace de noms.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Pour créer et configurer l’hôte de service web

- Créer l’hôte de service web à l’aide de l’adresse URI créée plus haut dans cette section.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    L’hôte de service est l’objet WCF qui instancie l’application hôte. Cet exemple passe le type d’hôte que vous souhaitez créer (un **ImageService**), ainsi que l’adresse à laquelle vous souhaitez exposer de l’application hôte.

### <a name="to-run-the-web-service-host"></a>Exécution de l’hôte de service web

1. Ouvrez le service.

    ```
    host.Open();
    ```
    Le service est en cours d’exécution.

2. Afficher un message indiquant que le service est en cours d’exécution et l’arrêt du service.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Lorsque vous avez terminé, fermez l’hôte de service.

    ```
    host.Close();
    ```

## <a name="example"></a>Exemple

L’exemple suivant inclut le contrat de service et de la mise en oeuvre des étapes précédentes du didacticiel et héberge le service dans une application console. Compiler le code suivant dans un fichier exécutable nommé ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>La compilation du code

Après avoir créé la solution, procédez comme suit pour exécuter l’application :

1. Appuyez sur **F5**, ou recherchez l’emplacement du fichier exécutable (ImageListener\bin\Debug\ImageListener.exe), pour exécuter le service. Conserver l’application en cours d’exécution, est nécessaire pour effectuer l’étape suivante.

2. Copiez et collez l’adresse à partir de l’invite de commande dans un navigateur pour voir l’image.

3. Lorsque vous avez terminé, appuyez sur **entrée** dans la fenêtre d’invite de commandes pour fermer l’application.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé une application qui utilise le service de relais de Bus des services, consultez les articles suivants pour en savoir plus sur la messagerie relayé :

- [Azure présentation architecturale de Bus des services](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)

- [Comment faire pour utiliser le Service de relais de Bus de Service](service-bus-dotnet-how-to-use-relay.md)

[Azure portal]: https://portal.azure.com