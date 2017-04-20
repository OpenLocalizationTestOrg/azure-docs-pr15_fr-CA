<properties 
    pageTitle="Surveiller l’utilisation et les performances des applications de bureau Windows" 
    description="Analyser l’utilisation et les performances de votre application de bureau de Windows avec des HockeyApp et des perspectives de l’Application." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="awills"/>

# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Surveiller l’utilisation et les performances dans les applications de bureau Windows

*Idées d’application est en mode Aperçu.*

[HockeyApp](https://hockeyapp.net) et les [Perspectives d’Application Visual Studio](app-insights-overview.md) vous permettent de surveiller l’application déployée pour l’utilisation et les performances.

> [AZURE.IMPORTANT] Nous vous recommandons de [HockeyApp](https://hockeyapp.net) pour distribuer et surveiller des applications de bureau et de périphérique. Avec HockeyApp, vous pouvez gérer la distribution, le test direct et commentaires des utilisateurs, ainsi que surveiller le rapports d’incident et d’utilisation. Vous pouvez également [exporter et interroger votre télémétrie avec Analytique](app-insights-hockeyapp-bridge-app.md).

> Bien que la télémétrie peut être envoyé aux analyses de l’Application à partir d’une application de bureau, il s’agit principalement utiles pour le débogage et le protocole expérimental.


## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Pour envoyer la télémétrie aux analyses de l’Application à partir d’une application Windows

1. Dans le [portail Azure](https://portal.azure.com), [créez une ressource d’idées d’Application](app-insights-create-new-resource.md). Pour le type d’application, choisissez ASP.NET app.
2. Effectuer une copie de la clé de l’Instrumentation. Trouver la clé dans la liste déroulante Essentials de la nouvelle ressource que vous venez de créer. 
3. Dans Visual Studio, modifier les packages NuGet de votre projet d’application et ajoutez Microsoft.ApplicationInsights.WindowsServer. (Ou choisissez Microsoft.ApplicationInsights si vous souhaitez simplement le système API, sans les modules de la collection standard de télémétrie.)
4. Définissez la clé de l’instrumentation dans votre code :

    `TelemetryConfiguration.Active.InstrumentationKey = "`*votre clé*`";` 

    ou ApplicationInsights.config (si vous avez installé un des packages standard de télémétrie) :
 
    `<InstrumentationKey>`*votre clé*`</InstrumentationKey>` 

    Si vous utilisez ApplicationInsights.config, assurez-vous que ses propriétés dans l’Explorateur de solutions sont définies **Build Action = teneur, Copy to Output Directory = copier**.
5. [Utilisation de l’API](app-insights-api-custom-events-metrics.md) pour envoyer de télémétrie.
6. Exécuter votre application et voir la télémétrie dans la ressource que vous avez créée dans le portail Azure.

## <a name="telemetry"></a>Exemple de code

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Étapes suivantes

* [Créer un tableau de bord](app-insights-dashboards.md)
* [Recherche de diagnostic](app-insights-diagnostic-search.md)
* [Explorez les métriques](app-insights-metrics-explorer.md)
* [Écrire des requêtes Analytique](app-insights-analytics.md)
 
