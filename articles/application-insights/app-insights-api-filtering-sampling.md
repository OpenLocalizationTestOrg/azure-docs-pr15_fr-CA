<properties 
    pageTitle="Le filtrage et le prétraitement dans le SDK de perspectives d’Application | Microsoft Azure" 
    description="Écrire des processeurs de télémétrie et les initialiseurs de télémétrie pour le Kit de développement pour filtrer ou ajouter des propriétés aux données avant la télémétrie est envoyé vers le portail de perspectives de l’Application." 
    services="application-insights"
    documentationCenter="" 
    authors="beckylino" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="borooji"/>

# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Le filtrage et le prétraitement de télémétrie dans le SDK de perspectives d’Application

*Idées d’application est en mode Aperçu.*

Vous pouvez écrire et configurer les plug-ins pour le SDK de perspectives d’Application Personnaliser la télémétrie capturée et traitée avant d’être envoyé vers le service informations d’Application. 

Actuellement, ces fonctionnalités sont disponibles pour le Kit de développement ASP.NET.

* [L’échantillonnage](app-insights-sampling.md) permet de réduire le volume de TELEMETRIE sans affecter vos statistiques. Il permet de conserver ensemble concernant les points de données de sorte que vous pouvez naviguer entre eux pour diagnostiquer un problème. Dans le portail, les totaux est multipliés pour compenser l’échantillonnage.
* [Filtrage avec des processeurs de télémétrie](#filtering) vous permet de sélectionner ou de modifier la télémétrie dans le Kit de développement avant d’être envoyé au serveur. Par exemple, vous pourriez réduire le volume de télémétrie en excluant les demandes de robots. Le filtrage est une approche plus simple pour réduire le trafic que l’échantillonnage Il vous permet de mieux contrôler ce qui est transmis, mais vous devez être conscient qu’il a une incidence sur vos statistiques - par exemple, si vous filtrez toutes les demandes ayant abouti.
* [Les initialiseurs de télémétrie ajouter des propriétés](#add-properties) à n’importe quel télémétrie envoyé à partir de votre application, y compris des données télémétriques fournies par les modules standard. Par exemple, vous pouvez ajouter des valeurs calculées ; ou des numéros de version permettant de filtrer les données dans le portail.
* [Le Kit de développement logiciel API](app-insights-api-custom-events-metrics.md) est utilisée pour envoyer des métriques et des événements personnalisés.


Avant de commencer :

* Installer les [aperçus d’Application SDK pour ASP.NET v2](app-insights-asp-net.md) dans votre application. 


<a name="filtering"></a>
## <a name="filtering-itelemetryprocessor"></a>Filtrage : ITelemetryProcessor

Cette technique vous donne un contrôle plus direct sur ce qui est inclus ou exclu à partir du flux de télémétrie. Vous pouvez l’utiliser en association avec l’échantillonnage, ou séparément.

Pour filtrer la télémétrie, vous écrivez un processeur de télémétrie et inscrivez avec le Kit de développement logiciel. Télémétrie tous parcourt de votre processeur, et vous pouvez choisir de déplacer à partir du flux, ou ajouter des propriétés. Cela inclut des données télémétriques fournies par les modules standard, telles que le collecteur de demande HTTP et le collecteur de dépendance, ainsi que vous avez écrit vous-même de télémétrie. Vous pouvez, par exemple, filtrer les télémétriques sur les requêtes émanant des robots ou des appels de dépendance réussie. 

> [AZURE.WARNING] La télémétrie envoyé à partir du Kit de développement logiciel de filtrage à l’aide de processeurs peut incliner les statistiques que vous voyez dans le portail et rendre difficile de suivre les éléments associés.
> 
> Pensez plutôt à l’aide de [l’échantillonnage](app-insights-sampling.md).

### <a name="create-a-telemetry-processor"></a>Créer un processeur de télémétrie

1. Vérifiez que le SDK de perspectives d’Application dans votre projet est version 2.0.0 ou ultérieur. Droit sur votre projet dans l’Explorateur de solutions Visual Studio et sélectionnez Manage NuGet Packages. Dans le Gestionnaire de package NuGet, consultez Microsoft.ApplicationInsights.Web.

1. Pour créer un filtre, implémenter ITelemetryProcessor. Il s’agit d’un autre point d’extensibilité comme module de télémétrie, initialiseur de télémétrie et canal de télémétrie. 

    Notez que les processeurs de télémétrie construire une chaîne de traitement. Lorsque vous instanciez un processeur de télémétrie, vous passez un lien vers le processeur suivant dans la chaîne. Lorsqu’un point de données de télémétrie est passé à la méthode de processus, il fait son travail et appelle ensuite le processeur de télémétrie suivant dans la chaîne.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Insérez ceci dans ApplicationInsights.config : 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Il s’agit de la même section où vous initialisez un filtre d’échantillonnage).

Vous pouvez passer des valeurs de chaîne à partir du fichier .config en fournissant des propriétés nommées publiques dans votre classe. 

> [AZURE.WARNING] Prendre soin de faire correspondre le nom de type et des noms de propriété dans le fichier .config pour les noms de classe et de la propriété dans le code. Si le fichier .config fait référence à une propriété ou un type inexistant, le Kit de développement peut échouer en mode silencieux envoyer tout télémétrie.

 
**Sinon,** vous pouvez initialiser le filtre dans le code. Dans une classe d’initialisation approprié - par exemple AppStart dans Global.asax.cs - insérez la chaîne de votre processeur :

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients créés après ce point utilisera vos processeurs.

### <a name="example-filters"></a>Exemple de filtre

#### <a name="synthetic-requests"></a>Demandes synthétiques

Filtrer les tests web et des robots. Bien que Explorer de mesures vous donne la possibilité de filtrer les sources synthétiques, cette option réduit le trafic en les filtrant au Kit de développement logiciel.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Authentification a échoué

Filtrer les requêtes avec une réponse « 401 ». 

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrer les appels de dépendance rapide à distance

Si vous souhaitez uniquement diagnostiquer les appels qui sont lentes, filtrer celles rapide. 

> [AZURE.NOTE] Cela s’incliner les statistiques que vous voyez sur le portail. Le graphique de dépendance apparaîtra comme si les appels de dépendance sont tous les échecs.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnostiquer les problèmes de dépendance

[Ce blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) décrit un projet pour diagnostiquer des problèmes de dépendance en envoyant automatiquement des pings régulières aux dépendances.


<a name="add-properties"></a>
## <a name="add-properties-itelemetryinitializer"></a>Ajouter des propriétés : ITelemetryInitializer

Utilisez des initialiseurs de télémétrie pour définir des propriétés globales qui sont envoyées avec toutes les télémétrie ; et substituer le comportement sélectionné des modules standard de télémétrie. 

Par exemple, les perspectives d’Application pour le package Web collecte télémétrie sur les requêtes HTTP. Par défaut, elle le signale comme l’échec d’une demande avec un code de réponse > = 400. Mais si vous souhaitez traiter les 400 comme un succès, vous pouvez fournir un initialiseur de télémétrie qui définit la propriété de la réussite.

Si vous fournissez un initialiseur de télémétrie, elle est appelée chaque fois qu’une des méthodes Track*() est appelée. Cela inclut les méthodes appelées par les modules standard de télémétrie. Par convention, ces modules ne définissez pas de toute propriété qui a déjà été définie par un initialiseur. 

**Définir votre initialiseur**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Charger votre initialiseur**

Dans ApplicationInsights.config :

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Sinon,* vous pouvez instancier l’initialiseur dans le code, par exemple dans Global.aspx.cs :


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Voir plus de cet exemple.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### <a name="javascript-telemetry-initializers"></a>Initialiseurs de télémétrie de JavaScript

*JavaScript*

Insérer un initialiseur de télémétrie immédiatement après le code d’initialisation que vous avez obtenu à partir du portail : 

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Pour obtenir un résumé des propriétés non personnalisé disponibles sur le telemetryItem, consultez le [modèle de données](app-insights-export-data-model.md#lttelemetrytypegt).

Vous pouvez ajouter des initialiseurs autant que vous le souhaitez. 


## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor et ITelemetryInitializer

Quelle est la différence entre les processeurs de télémétrie et les initialiseurs de télémétrie ?

* Il existe certains chevauchements dans ce que vous pouvez faire avec eux : à la fois peuvent être utilisées pour ajouter des propriétés à la télémétrie.
* TelemetryInitializers toujours exécuter avant TelemetryProcessors.
* TelemetryProcessors vous permet de remplacer ou de supprimer un élément de télémétrie complètement.
* TelemetryProcessors ne pas traiter de télémétrie de compteur de performance.



## <a name="persistence-channel"></a>Chaîne de persistance 

Si votre application s’exécute, où la connexion internet n’est pas toujours disponible ou lente, envisagez d’utiliser la couche de persistance et la chaîne en mémoire par défaut. 

Le canal de mémoire par défaut perd toute télémétrie qui n’a pas été envoyé au moment où l’application se ferme. Bien que vous puissiez utiliser `Flush()` pour tenter d’envoyer les données restantes dans la mémoire tampon, il reste perd des données si aucune connexion internet, ou si l’application s’arrête avant la transmission est terminée.

En revanche, la couche de persistance tampons de télémesure dans un fichier, avant de l’envoyer sur le portail. `Flush()`garantit que les données sont stockées dans le fichier. Si aucune donnée n’est pas envoyée au moment où l’application se ferme, il reste dans le fichier. Lors du redémarrage de l’application, les données seront envoyées puis, s’il existe une connexion internet. Données s’accumulent dans le fichier tant qu’il est nécessaire, jusqu'à ce qu’une connexion est disponible. 

### <a name="to-use-the-persistence-channel"></a>Pour utiliser la couche de persistance

1. Importer le package NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Inclure ce code dans votre application, dans un emplacement approprié de l’initialisation :
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Utilisez `telemetryClient.Flush()` avant la ferme de votre application, afin de vous assurer de données envoyées sur le portail ou enregistrées dans le fichier.

    Notez que Flush() est synchrone pour la couche de persistance, mais asynchrone pour les autres canaux.

 
La couche de persistance est optimisée pour les scénarios de périphériques, où le nombre d’événements générés par l’application est relativement faible et la connexion est souvent peu fiable. Ce canal va écrire les événements sur le disque dans un stockage fiable tout d’abord et puis essayez de l’envoyer. 

#### <a name="example"></a>Exemple

Supposons que vous souhaitez surveiller les exceptions non gérées. Vous vous abonnez à la `UnhandledException` événement. Dans le rappel, vous incluez un appel de Flush pour vous assurer que la télémétrie est rendue persistante.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Lorsque l’application s’arrête, vous verrez un fichier dans `%LocalAppData%\Microsoft\ApplicationInsights\`, qui contient les événements compressés. 
 
Prochain que démarrage de cette application, le canal sera ce fichier et livrent télémesure pour les analyses de l’Application s’il le peut.

#### <a name="test-example"></a>Exemple de test

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


Le code de la couche de persistance est de [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel). 


## <a name="reference-docs"></a>Documents de référence

* [Vue d’ensemble de l’API](app-insights-api-custom-events-metrics.md)

* [Référence d’ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## <a name="sdk-code"></a>Code du Kit de développement logiciel

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Kit de développement logiciel JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Étapes suivantes


* [Rechercher des événements et des journaux][diagnostic]
* [Échantillonnage](app-insights-sampling.md)
* [Résolution des problèmes][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 
