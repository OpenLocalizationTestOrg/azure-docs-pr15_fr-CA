<properties
   pageTitle="Service de communication avec l’API Web ASP.NET | Microsoft Azure"
   description="Apprenez à implémenter la communication de service détaillée à l’aide de l’API Web ASP.NET avec l’auto-hébergement OWIN dans l’API de Services fiables."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="get-started-service-fabric-web-api-services-with-owin-self-hosting"></a>Mise en route : services API de Web Service Fabric avec l’auto-hébergement OWIN

Azure Fabric de Service met la puissance de vos mains lorsque vous décidez comment vous souhaitez que vos services pour communiquer avec les utilisateurs et les uns avec les autres. Ce didacticiel se concentre sur l’implémentation de la communication de service à l’aide d’API de Web ASP.NET avec Open Interface Web pour l’auto-hébergement .NET (OWIN) dans l’API des Services de Fabric de Service fiable. Nous allons approfondir dans l’API de communication enfichable Services fiables. Nous utiliserons également des API Web dans un exemple étape par étape pour vous montrer comment configurer un écouteur de communication personnalisé.


## <a name="introduction-to-web-api-in-service-fabric"></a>Introduction à l’API de Web Service fabric

API de Web ASP.NET est une infrastructure de puissante et populaire pour la création de APIs HTTP sur le.NET Framework. Si vous n’êtes pas familiarisé avec l’infrastructure, consultez [mise en route avec ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) pour en savoir plus.

API de Web Service fabric est la même API Web ASP.NET vous connaissez et appréciez. La différence réside dans la manière dont vous *hôte* une API Web application. Vous n’utiliserez pas Microsoft Internet Information Services (IIS). Pour mieux comprendre la différence, nous allons de la diviser en deux parties :

 1. L’application de l’API Web (y compris les contrôleurs et les modèles)
 2. L’hôte (le serveur web, généralement IIS)

Une application Web API lui-même ne change pas. Il ne diffère pas des applications API Web que vous avez écrit dans le passé, et vous devez être en mesure de déplacer simplement sur la plupart du code de votre application. Mais si vous avez été hébergement sur IIS, où vous hébergez l’application peut être un peu différente de ce que vous avez l’habitude de. Avant de nous atteindrons l’étape hébergement, commençons par quelque chose de plus familier : l’application de l’API Web.


## <a name="create-the-application"></a>Création de l’application

Commencez par créer une nouvelle application de Service Fabric avec un service unique sans état dans Visual Studio 2015 :

![Créez une nouvelle application de Service Fabric](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Un modèle Visual Studio pour un service sans état à l’aide des API de Web est disponible pour vous. Dans ce didacticiel, nous créerons un projet Web API qui aboutit à ce que vous obtiendriez si vous avez sélectionné ce modèle.

Sélectionnez un projet de Service sans état vierge pour apprendre à créer un projet Web API à partir de rien ou vous pouvez commencer avec le modèle API Web de service sans état et suivez simplement le long.  

![Créer un service sans état](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

La première étape consiste à extraire dans certains packages NuGet pour API Web. Le package que nous souhaitons utiliser est Microsoft.AspNet.WebApi.OwinSelfHost. Ce package comprend tous les packages Web API et les packages de *l’hôte* . Il sera important ultérieurement.

![Créer des API Web à l’aide du Gestionnaire de package NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Après avoir installé les packages, vous pouvez commencer à créer la structure de projet Web API base. Si vous avez utilisé les API Web, la structure de projet devrait sembler très familière. Commencez par ajouter un `Controllers` répertoire et un contrôleur des valeurs simples :

**Fichier ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

Ensuite, ajoutez une classe de démarrage à la racine du projet pour enregistrer le routage, des formateurs et des autres paramètres de configuration. C’est également où les API Web se connecte à l' *hôte*, qui sera revue ultérieurement. 

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

C’est tout pour la partie de l’application. À ce stade, nous avons défini uniquement la disposition du projet Web API base. Jusqu’ici, il ne doit pas différer beaucoup de projets d’API Web que vous avez écrit dans le passé ou à partir du modèle API Web de base. Votre logique métier passe comme d’habitude dans les contrôleurs et les modèles.

Maintenant que nous faire sur l’hébergement de sorte que nous pouvons exécuter réellement ?

## <a name="service-hosting"></a>Hébergement de services

Service de fabric, votre service s’exécute dans un *processus hôte de service*, un fichier exécutable qui exécute le code de votre service. Lorsque vous écrivez un service à l’aide de l’API des Services fiables, votre projet de service compile uniquement à un fichier exécutable qui enregistre votre type de service et exécute votre code. Cela est vrai dans la plupart des cas lorsque vous écrivez un service de Fabric de Service dans .NET. Lorsque vous ouvrez Program.cs dans le projet de service sans état, vous devez voir :

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Si qui ressemble étrangement au point d’entrée à une application de console, c’est parce qu’il est.

Plus de détails sur le processus hôte de service et de l’inscription du service sortent de la portée de cet article. Mais il est important de savoir, pour le moment, *que votre code de service s’exécute dans son propre processus*.

## <a name="self-host-web-api-with-an-owin-host"></a>Auto-hébergée API Web avec un hôte OWIN

Étant donné que le code de votre application d’API de Web est hébergé dans son propre processus, comment vous connecter à un serveur web ? Entrez [OWIN](http://owin.org/). OWIN est simplement un contrat entre les serveurs web et les applications web .NET. En règle générale lorsque ASP.NET (jusqu'à 5 de MVC) est utilisé, l’application web est étroitement couplée à IIS par l’intermédiaire de System.Web. Toutefois, les API Web implémente OWIN, afin de pouvoir écrire à partir du serveur web qui héberge une application web qui est dissociée. De ce fait, vous pouvez utiliser un serveur *auto-hébergé* de web OWIN que vous pouvez démarrer dans votre propre processus. Il s’intègre parfaitement avec le modèle d’hébergement Service Fabric que nous venons de décrire.

Dans cet article, nous utiliserons des interconnexions comme l’hôte OWIN pour l’application de l’API Web. Interconnexions sont une implémentation d’hôte OWIN open source reposant sur les [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) et les [API de serveur HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)Windows.

> [AZURE.NOTE] Pour en savoir plus sur les interconnexions, accédez au [site des interconnexions](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Pour obtenir un aperçu rapide de l’utilisation des interconnexions auto-hébergement des API Web, reportez-vous à la section [Utiliser OWIN Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## <a name="set-up-the-web-server"></a>Configurer le serveur web

L’API de Services fiables fournit un point d’entrée de communication dans lequel vous pouvez brancher dans les piles de communication qui permettent aux utilisateurs et aux clients de se connecter au service :

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Le serveur web (et une autre pile de communication que vous utilisez dans le futur, comme le protocole WebSocket) doivent utiliser l’interface ICommunicationListener à la parfaite intégration avec le système. Les raisons pour cela deviendra plus apparents dans les étapes suivantes.

Tout d’abord, créez une classe appelée OwinCommunicationListener qui implémente ICommunicationListener :

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

L’interface ICommunicationListener fournit trois méthodes pour gérer un écouteur de communication pour votre service :

 - *OpenAsync*. Démarrer l’écoute des demandes.
 - *CloseAsync*. Arrêter l’écoute des demandes, terminer toutes les requêtes en cours d’exécution et s’arrête.
 - *Abandonner*. Tout annuler et arrêter immédiatement.

Pour commencer, ajoutez des membres de classes privées pour le port d’écoute devra fonctionner de choses. Ces seront initialisées par le constructeur et utilisés ultérieurement lorsque vous configurez l’URL à l’écoute.

```csharp
internal class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## <a name="implement-openasync"></a>Mettre en œuvre des OpenAsync

Pour configurer le serveur web, vous avez besoin de deux informations :

 - *Préfixe de chemin d’accès d’une URL*. Bien que cette étape est facultative, qu'il est bon que vous paramétrez cette information afin que vous puissiez en toute sécurité héberger plusieurs services web dans votre application.
 - *Un port*.

Avant d’obtenir un port pour le serveur web, il est important de comprendre que le Service Fabric fournit une couche d’application qui agit comme un tampon entre votre application et le système d’exploitation sous-jacent, il s’exécute sur. En tant que tel, Service Fabric permet de configurer des *points de terminaison* de vos services. TISSU de service garantit que les points de terminaison sont disponibles pour votre service à utiliser. De cette façon, vous n’êtes pas obligé de les configurer vous-même dans l’environnement du système d’exploitation sous-jacent. Vous pouvez facilement héberger votre application de Service Fabric dans différents environnements sans avoir à apporter des modifications à votre application. (Par exemple, vous pouvez héberger la même application dans Azure ou dans votre propre centre de données).

Configurer un point de terminaison HTTP dans PackageRoot\ServiceManifest.xml :

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Cette étape est importante car le processus hôte de service s’exécute sous les informations d’identification sensibles (Service réseau sur Windows). Cela signifie que vous n’avez pas accès pour configurer un point de terminaison HTTP sur son propre pour votre service. À l’aide de la configuration de point de terminaison, Fabric de Service sait qu’il doit pour configurer la liste de contrôle d’accès (ACL) pour que le service écoute sur l’URL. TISSU de service fournit également un emplacement standard pour configurer des points de terminaison.


Dans OwinCommunicationListener.cs, vous pouvez démarrer la mise en œuvre de OpenAsync. Il s’agit d’où vous démarrez le serveur web. Tout d’abord, obtenir les informations de point de terminaison et créer l’URL à laquelle le service écoutera. L’URL sera différent selon que l’écouteur est utilisé dans un service sans état ou un état. Pour un service dynamique, l’écouteur doit créer une adresse unique pour chaque réplica avec état service qu'il écoute. Pour les services sans état, l’adresse peut être beaucoup plus simple. 

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

Notez que « http://+ » est utilisée ici. Cela consiste à s’assurer que le serveur web est à l’écoute sur toutes les adresses disponibles, y compris de localhost, nom de domaine complet et l’adresse IP d’ordinateur.

La mise en oeuvre OpenAsync est une des raisons plus importantes pourquoi le serveur web (ou une pile de communications) est implémentée comme un ICommunicationListener, plutôt que de son ouvre directement à partir de `RunAsync()` dans le service. La valeur de retour de OpenAsync est l’adresse qui est à l’écoute sur le serveur web. Lorsque cette adresse est renvoyée au système, il enregistre l’adresse auprès du service. TISSU de service fournit une API qui permet aux clients et aux autres services puis demander cette adresse par nom de service. Ceci est important car l’adresse de service n’est pas statique. Les services sont déplacés dans le cluster à des fins de disponibilité et de l’équilibrage de la ressource. C’est le mécanisme qui permet aux clients de résoudre l’adresse d’écoute pour un service.

Dans cette optique, OpenAsync démarre le serveur web et retourne l’adresse qu’il écoute. Notez qu’il est à l’écoute sur « http://+ », mais avant de OpenAsync renvoie l’adresse, le « + » est remplacée par l’adresse IP ou le nom de domaine complet du nœud qu’il est actuellement. L’adresse qui est retourné par cette méthode est ce qui est enregistré dans le système. Il est également ce que les clients et les autres services voient lorsqu’ils demanderont les adresses d’un service. Pour les clients de se connecter correctement au, ils doivent un IP ou un nom de domaine complet réel dans l’adresse.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Notez que cela fait référence à la classe de démarrage qui a été passée à la OwinCommunicationListener dans le constructeur. Cette instance de démarrage est utilisée par le serveur web à l’application Web API des données d’amorçage.

Le `ServiceEventSource.Current.Message()` ligne apparaîtra dans la fenêtre des événements de Diagnostic par la suite, lorsque vous exécutez l’application pour vérifier que le serveur web a démarré avec succès.

## <a name="implement-closeasync-and-abort"></a>CloseAsync de mise en œuvre et d’abandon

Enfin, implémentez CloseAsync et abandonner pour arrêter le serveur web. Le serveur web peut être arrêté en supprimant le handle de serveur qui a été créé au cours de la OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

Dans cet exemple d’implémentation, les CloseAsync et les abandonner tout simplement s’arrêter le serveur web. Vous pouvez choisir d’effectuer un arrêt plus en douceur coordonné du serveur web dans CloseAsync. Par exemple, l’arrêt peut attendre pour les demandes en cours d’exécution soient terminées avant le retour.

## <a name="start-the-web-server"></a>Démarrer le serveur web

Vous êtes maintenant prêt à créer et retourner une instance de OwinCommunicationListener pour démarrer le serveur web. Dans la classe de Service (WebService.cs), vous devez substituer les `CreateServiceInstanceListeners()` méthode :

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

Il s’agit de l' API Web *application* et *l’hôte* de OWIN enfin intersection. L’hôte (OwinCommunicationListener) est une instance donnée de l' *application* (API Web) via la classe de démarrage. Service de Fabric gère ensuite son cycle de vie. Ce même modèle peut généralement être suivi avec une pile de communication.

## <a name="put-it-all-together"></a>Obtenir un ensemble cohérent

Dans cet exemple, vous n’avez pas besoin de faire quoi que ce soit dans les `RunAsync()` méthode, afin que cette substitution peut simplement être supprimée.

L’implémentation du service final doit être très simple. Il n’a pas besoin de créer l’écouteur de communication :

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

Le `OwinCommunicationListener` classe :

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}
```

Maintenant que vous avez placé tous les éléments en place, votre projet doit se présenter comme une application API Web standard avec des points d’entrée de l’API des Services fiables et un hôte OWIN :


![API de Web avec des points d’entrée de l’API des Services fiables et hôte de l’OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## <a name="run-and-connect-through-a-web-browser"></a>Exécuter et se connecter via un navigateur web

Si vous n’avez pas fait, [Configurez votre environnement de développement](service-fabric-get-started.md).


Vous pouvez maintenant générer et déployer votre service. Appuyez sur **F5** dans Visual Studio pour générer et déployer l’application. Dans la fenêtre des événements de Diagnostic, vous devez voir un message qui indique que le serveur web ouvert sur http://localhost:8281 /.


![Fenêtre d’événements de Diagnostic de Visual Studio](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Si le port a déjà été ouvert par un autre processus sur votre ordinateur, vous pouvez voir une erreur ici. Cela indique que le récepteur n’a pas pu être ouvert. Si tel est le cas, essayez en utilisant un port différent pour la configuration de point de terminaison dans ServiceManifest.xml.


Une fois que le service est en cours d’exécution, ouvrez un navigateur et accédez à [http://localhost:8281/api/values](http://localhost:8281/api/values) pour le tester.

## <a name="scale-it-out"></a>Mettre à l’échelle des

Mise à l’échelle des applications web sans état généralement signifie ajoutant plus de machines et de révolution sur les applications web. Moteur d’orchestration de service Fabric peut le faire pour vous à chaque fois que de nouveaux nœuds sont ajoutés à un cluster. Lorsque vous créez des instances de service sans état, vous pouvez spécifier le nombre d’instances que vous souhaitez créer. Service de Fabric place ce nombre d’instances sur les nœuds du cluster. Et garde à ne pas créer plus d’une instance sur tout un nœud. Vous pouvez également commander à Fabric du Service pour créer une instance sur chaque nœud en spécifiant **-1** pour le nombre d’instances. Cela garantit que chaque fois que vous ajoutez des nœuds à faire évoluer votre cluster, une instance de votre service sans état sera créée sur les nouveaux nœuds. Cette valeur est une propriété de l’instance de service, elle est définie lorsque vous créez une instance de service. Vous pouvez pour cela via PowerShell :

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Vous pouvez également le faire lorsque vous définissez un service par défaut dans un projet de service sans état de Visual Studio :

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Pour plus d’informations sur la création d’application et les instances de service, voir [déploiement d’une application](service-fabric-deploy-remove-applications.md).

## <a name="next-steps"></a>Étapes suivantes

[Déboguer votre application de Service Fabric à l’aide de Visual Studio](service-fabric-debugging-your-application.md)
