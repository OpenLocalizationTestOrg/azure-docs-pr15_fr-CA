<properties 
    pageTitle="Configuration des analyses d’Application SDK avec ApplicationInsights.config ou .xml" 
    description="Activer ou désactiver des modules de collecte de données et ajouter des compteurs de performance et d’autres paramètres" 
    services="application-insights"
    documentationCenter="" 
    authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/12/2016" 
    ms.author="awills"/>

# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configuration de l’Application d’idées SDK avec ApplicationInsights.config ou .xml

Le Kit de développement .NET de perspectives Application se compose d’un certain nombre de packages NuGet. Le [package de base](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fournit l’API pour l’envoi de télémétrie pour les analyses de l’Application. [Packages supplémentaires](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) prévoir de télémétrie _modules_ et _les initialiseurs de_ suivi automatique de télémétrie à partir de votre application et de son contexte. En modifiant le fichier de configuration, vous pouvez activer ou désactiver des modules de télémétrie et les initialiseurs et définir les paramètres pour certains d'entre eux.

Le fichier de configuration nommé `ApplicationInsights.config` ou `ApplicationInsights.xml`, selon le type de votre application. Il est automatiquement ajouté à votre projet lorsque vous [installez la plupart des versions du Kit de développement][start]. Il est également ajouté à une application web par le [Moniteur d’état sur un serveur IIS][redfield], ou lorsque vous sélectionnez l’Appplication perspectives d' [extension d’un site Web Azure ou une machine virtuelle](app-insights-azure-web-apps.md).

Il n’est pas un fichier équivalent pour contrôler le [Kit de développement dans une page web][client].

Ce document décrit les sections que vous voyez dans la configuration de fichier, comment ils contrôlent les composants du Kit de développement, et les packages NuGet chargement ces composants.

## <a name="telemetry-modules-aspnet"></a>Modules de télémétrie (ASP.NET)

Chaque module de télémétrie rassemble un certain type de données et utilise l’API de base pour envoyer les données. Les modules sont installés par différents packages NuGet, également ajouter les lignes nécessaires au fichier .config.

Il y a un nœud dans le fichier de configuration pour chaque module. Pour désactiver un module, supprimez le nœud ou la transformer en commentaire.



### <a name="dependency-tracking"></a>Suivi des dépendances

[Suivi des dépendances](app-insights-dependencies.md) collecte télémétrie sur les appels de votre application à des bases de données et les services externes et les bases de données. Pour permettre à ce module fonctionner dans un serveur IIS, vous devez [installer le moniteur d’état][redfield]. Pour l’utiliser dans les applications web Azure ou ordinateurs virtuels, [Sélectionnez l’extension de l’Application aperçu](app-insights-azure-web-apps.md).

Vous pouvez également écrire votre propre dépendance suivi du code à l’aide de l' [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) le package NuGet.

### <a name="performance-collector"></a>Collecteur de performance

La [collecte des compteurs de performance système](app-insights-performance-counters.md) , tels que la charge du processeur, de mémoire et de réseau à partir d’installations de IIS. Vous pouvez spécifier les compteurs à collecter, y compris les compteurs de performance que vous avez vous-même défini.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) le package NuGet.


### <a name="application-insights-diagnostics-telemetry"></a>Application Insights Diagnostics télémétrie

Le `DiagnosticsTelemetryModule` signale les erreurs dans le code d’instrumentation de perspectives d’Application lui-même. Par exemple, si le code ne peut pas accéder aux compteurs de performance ou un `ITelemetryInitializer` lève une exception. Télémétrie trace suivi par ce module apparaît dans la [Recherche de Diagnostic][diagnostic]. Envoie des données de diagnostic à dc.services.vsallin.net.
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) le package NuGet. Si vous installez uniquement ce package, le fichier ApplicationInsights.config n’est pas automatiquement créé. 

### <a name="developer-mode"></a>Mode développeur

`DeveloperModeWithDebuggerAttachedTelemetryModule`force les perspectives d’Application `TelemetryChannel` pour envoyer les données immédiatement, élément d’un télémétrie à la fois, quand un débogueur est attaché au processus de l’application. Cela réduit le laps de temps entre le moment lorsque votre application effectue le suivi de télémétrie et lorsqu’il apparaît sur le portail de perspectives de l’Application. Il entraîne une surcharge importante dans le processeur et la bande passante.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application perspectives sur Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Package NuGet

### <a name="web-request-tracking"></a>Suivi des demandes Web

Indique le [code de temps et le résultat de réponse](app-insights-asp-net.md) des requêtes HTTP. 

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Package NuGet de [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### <a name="exception-tracking"></a>Exception de suivi

`ExceptionTrackingTelemetryModule`pistes des exceptions non gérées dans votre application web. Consultez les [défaillances et les exceptions][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Package NuGet de [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-effectue le suivi des [exceptions de tâche non prise en charge](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-effectue le suivi des exceptions non gérées pour les rôles de travail, les services windows et les applications de console.
* [Application perspectives sur Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Package NuGet.

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights

Le package Microsoft.ApplicationInsights fournit l' [API principale](https://msdn.microsoft.com/library/mt420197.aspx) du Kit de développement. Utilisent les autres modules de télémétrie, et vous pouvez également [l’utiliser pour définir votre propre télémétrie](app-insights-api-custom-events-metrics.md).

* Aucune entrée dans ApplicationInsights.config.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) le package NuGet. Si vous installez simplement ce NuGet, aucun fichier .config n’est généré.

## <a name="telemetry-channel"></a>Canal de télémétrie

Le canal de télémétrie gère la mise en mémoire tampon et transmission de télémétrie pour le service informations d’Application. 

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`est le canal par défaut pour les services. Il met les données en mémoire.
* `Microsoft.ApplicationInsights.PersistenceChannel`est une alternative pour les applications console. Il peut enregistrer des données vidées dans un stockage persistant lorsque votre application ferme et enverra au démarrage de l’application à nouveau.


## <a name="telemetry-initializers-aspnet"></a>Initialiseurs de télémétrie (ASP.NET)

Initialiseurs de télémétrie définir les propriétés de contexte qui sont envoyées avec chaque élément de télémétrie. 

Vous pouvez [écrire votre propre initialiseurs](app-insights-api-filtering-sampling.md#add-properties) pour définir les propriétés de contexte.

Les initialiseurs de standard sont toutes définies par les packages Web ou WindowsServer2003 NuGet :


* `AccountIdTelemetryInitializer`définit la propriété AccountId.
* `AuthenticatedUserIdTelemetryInitializer`définit la propriété AuthenticatedUserId définie par le SDK JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer`mises à jour de la `RoleName` et `RoleInstance` propriétés de la `Device` contexte pour tous les éléments de télémétrie avec les informations extraites à partir de l’environnement d’exécution Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer`mises à jour de la `Version` propriété de la `Component` contexte pour tous les éléments de télémétrie avec la valeur extraite de le `BuildInfo.config` fichier produit par MS Build.
* `ClientIpHeaderTelemetryInitializer`mises à jour `Ip` propriétés de la `Location` contexte de tous les éléments de télémétrie basée sur le `X-Forwarded-For` en-tête HTTP de la demande.
* `DeviceTelemetryInitializer`met à jour les propriétés suivantes de le `Device` contexte pour tous les éléments de télémétrie.
 - `Type`a la valeur « PC »
 - `Id`a le nom de domaine de l’ordinateur exécutant l’application web.
 - `OemName`est définie sur la valeur extraite de le `Win32_ComputerSystem.Manufacturer` champ à l’aide de WMI.
 - `Model`est définie sur la valeur extraite de le `Win32_ComputerSystem.Model` champ à l’aide de WMI.
 - `NetworkType`est définie sur la valeur extraite de le `NetworkInterface`.
 - `Language`est définie sur le nom de le `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer`mises à jour de la `RoleInstance` propriété de la `Device` contexte pour tous les éléments de télémétrie avec le nom de domaine de l’ordinateur exécutant l’application web.
* `OperationNameTelemetryInitializer`mises à jour de la `Name` propriété de la `RequestTelemetry` et le `Name` propriété de la `Operation` contexte de tous les éléments de télémétrie basée sur la méthode HTTP, ainsi que les noms de contrôleur ASP.NET MVC et action appelée pour traiter la demande.
* `OperationIdTelemetryInitializer`ou `OperationCorrelationTelemetryInitializer` mises à jour de la `Operation.Id` propriété de contexte de tous les éléments de télémétrie suivi lors du traitement d’une demande avec généré automatiquement `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`mises à jour de la `Id` propriété de la `Session` contexte pour tous les éléments de télémétrie avec la valeur extraite de le `ai_session` cookie généré par le code d’instrumentation ApplicationInsights JavaScript en cours d’exécution dans le navigateur de l’utilisateur. 
* `SyntheticTelemetryInitializer`ou `SyntheticUserAgentTelemetryInitializer` mises à jour de la `User`, `Session` et `Operation` propriétés de contextes de tous les éléments de télémétrie suivies lors de la gestion d’une demande à partir d’une source de synthèse, telles qu’une disponibilité de test ou des robots de moteur de recherche. Par défaut, [Les mesures de l’Explorateur](app-insights-metrics-explorer.md) n’affiche pas de télémétrie synthétique. 

    Le `<Filters>` la valeur identifiant les propriétés des demandes.
* `UserAgentTelemetryInitializer`mises à jour de la `UserAgent` propriété de la `User` contexte de tous les éléments de télémétrie basée sur le `User-Agent` en-tête HTTP de la demande.
* `UserTelemetryInitializer`mises à jour de la `Id` et `AcquisitionDate` propriétés de `User` contexte pour tous les éléments de télémétrie avec les valeurs extraites de la `ai_user` cookie généré par le code d’instrumentation JavaScript de perspectives d’Application en cours d’exécution dans le navigateur de l’utilisateur.
* `WebTestTelemetryInitializer`définit l’id utilisateur, les id de session et les propriétés de source synthétique pour les demandes HTTP provenant de [tests de disponibilité](app-insights-monitor-web-app-availability.md).
Le `<Filters>` la valeur identifiant les propriétés des demandes.

## <a name="telemetry-processors-aspnet"></a>Processeurs de télémétrie (ASP.NET)

Processeurs de télémétrie peuvent de filtrer et de modifier chaque élément de télémétrie juste avant d’être envoyé sur le portail du Kit de développement SDK.

Vous pouvez [écrire vos propres processeurs de télémétrie](app-insights-api-filtering-sampling.md#filtering).


#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processeur de télémétrie d’échantillonnage adapté (à partir de 2.0.0-beta3)

Cette option est activée par défaut. Si votre application envoie un lot de télémétrie, ce processeur supprime la partie.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Le paramètre fournit la cible que l’algorithme s’efforce d’atteindre. Chaque instance du Kit de développement fonctionne de manière indépendante, afin que si votre serveur est un cluster composé de plusieurs ordinateurs, le volume réel de télémétrie sera multiplié en conséquence.

[En savoir plus sur l’échantillonnage](app-insights-sampling.md).



#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processeur de télémétrie d’échantillonnage de taux fixe (de 2.0.0-beta1)

Il existe également une norme [processeur de télémétrie d’échantillonnage](app-insights-api-filtering-sampling.md#sampling) (2.0.1) :

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Paramètres du canal (Java)

Ces paramètres affectent la manière dont le Kit de développement Java doit stocker et vider les données de télémétrie qu’il collecte.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity

Le nombre d’éléments de télémétrie pouvant être stockés dans le stockage en mémoire du Kit de développement. Lorsque ce nombre est atteint, le vidage de la mémoire tampon de télémétrie - c'est-à-dire les télémétrie sont envoyés au serveur de perspectives de l’Application.

-   Min : 1
-   Max. : 1000
-   Par défaut : 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds 

Détermine la fréquence à laquelle les données sont stockées dans le stockage en mémoire doivent être vidées (envoyés aux analyses de l’Application).

-   Min : 1
-   Max : 300
-   Par défaut : 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB

Détermine la taille maximale en Mo qui est allouée pour le stockage persistant sur le disque local. Ce système de stockage est utilisée pour les éléments de télémétrie persistantes qui n’a pas pu être transmis vers le point de terminaison d’Application perspectives. Lorsque la taille de stockage a été atteint, les nouveaux éléments de télémétrie seront ignorées.

-   Min : 1
-   Max. : 100
-   Par défaut : 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey

Cette option détermine la ressource d’idées d’Application dans lequel vos données s’affiche. En général, vous créez une ressource séparée, avec une clé distincte, pour chacune de vos applications.

Si vous souhaitez définir la clé de manière dynamique, par exemple si vous souhaitez envoyer les résultats à partir de votre application à différentes ressources - vous pouvez omettre la clé à partir du fichier de configuration et définir dans le code.

Pour définir la clé pour toutes les instances de TelemetryClient, y compris les modules standard de télémétrie, permet de définir la clé de TelemetryConfiguration.Active. Pour ce faire, dans une méthode d’initialisation, par exemple global.aspx.cs dans un service ASP.NET :

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Si vous souhaitez simplement envoyer un ensemble spécifique d’événements à une autre ressource, vous pouvez définir la clé pour un TelemetryClient spécifique :

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[En savoir plus sur l’API][api].

Pour obtenir une nouvelle clé, [créez une nouvelle ressource dans le portail d’idées d’Application][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

