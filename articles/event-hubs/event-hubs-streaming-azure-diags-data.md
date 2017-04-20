<properties
    pageTitle="Diffusion en continu de données Azure Diagnostics dans le chemin d’accès à chaud à l’aide de concentrateurs d’événement | Microsoft Azure"
    description="Illustre comment configurer les Diagnostics Azure avec les concentrateurs d’événement de bout en bout, y compris les instructions pour les scénarios courants."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/14/2016"
    ms.author="sethm" />

# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Diffusion en continu de données Azure Diagnostics dans le chemin d’accès rapide à l’aide de concentrateurs d’événement

Diagnostics de Windows Azure fournit des méthodes souples pour collecter des statistiques et des journaux à partir d’ordinateurs virtuels de services cloud (VMs) et de transférer les résultats vers le stockage Azure. À partir de la période de mars 2016 (Kit de développement logiciel 2.9), vous pouvez récepteur Diagnostics aux sources de données entièrement personnalisée et transférer des données de chemin réactif en secondes à l’aide de [Concentrateurs d’événement Azure](https://azure.microsoft.com/services/event-hubs/).

Les types de données pris en charge sont les suivantes :

- Événements de suivi pour Windows (ETW)
- Compteurs de performance
- Journaux des événements Windows
- Journaux d’application
- Journaux d’infrastructure Diagnostics Azure

Cet article vous explique comment configurer les Diagnostics d’Azure avec les concentrateurs d’événement de bout en bout. Guide est également fournie pour les scénarios courants suivants :

- Comment personnaliser les journaux et les mesures qui obtiennent des sinked à des concentrateurs d’événement
- Comment modifier les configurations de chaque environnement.
- Comment afficher les données de flux d’événements concentrateurs
- Comment faire pour résoudre les problèmes liés à la connexion  

## <a name="prerequisites"></a>Conditions préalables

Les concentrateurs événement réception dans Azure Diagnostics est pris en charge dans les Services en nuage, les ordinateurs virtuels, les jeux d’échelle de Machine virtuelle et Fabric Service depuis la 2.9 SDK Azure et les outils correspondants Azure pour Visual Studio.

- Extension de Diagnostics Azure 1.6 ([Azure SDK pour 2.9 de .NET ou une version ultérieure](https://azure.microsoft.com/downloads/) cible cela par défaut)
- [Visual Studio 2013 ou version ultérieure](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Configurations existantes de Diagnostics d’Azure dans une application à l’aide d’un fichier *.wadcfgx* et une des méthodes suivantes :
    - Visual Studio : [Configuration des Diagnostics pour les Services en nuage Azure et les Machines virtuelles](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
    - De Windows PowerShell : [Activer les diagnostics de Services en nuage Azure à l’aide de PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Espace de noms de concentrateurs événement mis en service par l’article de la [mise en route avec les concentrateurs d’événement](./event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Établir une connexion au récepteur d’événement concentrateurs Azure Diagnostics

Diagnostics de Windows Azure récepteurs toujours des journaux et des métriques par défaut pour un compte de stockage Azure. Une application peut en outre récepteur à des concentrateurs de l’événement en ajoutant une nouvelle section de **récepteurs** à l’élément **WadCfg** dans la section **PublicConfig** du fichier *.wadcfgx* . Dans Visual Studio, le fichier *.wadcfgx* se trouve dans le chemin suivant : **Projet de Service Cloud** > **rôles** >  **(RoleName)** > fichier de**diagnostics.wadcfgx** .

```
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```

Dans cet exemple, l’URL du concentrateur est définie dans l’espace de noms qualifié complet de l’événement de supervision : espace de noms de concentrateurs d’événement + « / » + nom de concentrateur de l’événement.  

URL du concentrateur de l’événement s’affiche dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkID=213885) sur le tableau de bord de concentrateurs de l’événement.  

Le nom de **récepteur** peut être défini à n’importe quelle chaîne valide tant que la même valeur est utilisée régulièrement dans tout le fichier de configuration.

> [AZURE.NOTE]  Il peut y avoir des récepteurs supplémentaires, tels *applicationInsights* configuré dans cette section. Diagnostics de Windows Azure permet à un ou plusieurs récepteurs à définir si chaque récepteur est également déclaré dans la section **PrivateConfig** .  

Le récepteur d’événement concentrateurs doit également être déclaré et défini dans la section **PrivateConfig** du fichier de configuration de *.wadcfgx* .

```
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="" key="" endpoint="" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
</PrivateConfig>
```

Le `SharedAccessKeyName` valeur doit correspondre à une clé de Signature de l’accès partagé (SAS) et la stratégie qui a été défini dans l’espace de noms de **Concentrateurs de l’événement** . Accédez au tableau de bord événement concentrateurs dans [Azure portal](https://manage.windowsazure.com), cliquez sur l’onglet **configurer** et définir une stratégie nommée (par exemple, « SendRule ») qui a des autorisations *Envoyer* . Le **StorageAccount** est également déclaré dans **PrivateConfig**. Il est inutile de modifier les valeurs ici s’ils travaillent. Dans cet exemple, nous laisser les valeurs vide, qui est un signe qu’un actif en aval définira les valeurs. Par exemple, le fichier de configuration d’environnement *ServiceConfiguration.Cloud.cscfg* définit les approprié à l’environnement, les noms et les clés.  

> [AZURE.WARNING] La clé d’événement concentrateurs SAS est stockée en texte brut dans le fichier *.wadcfgx* . Souvent, cette clé est archivée dans le contrôle de code source ou est disponible en tant qu’actif dans votre serveur de builds, vous devez le protéger comme il convient. Nous vous conseillons une clé SAS ici avec autorisations *Envoyer uniquement* afin qu’un utilisateur malveillant peut écrire dans le concentrateur d’événements, mais pas écouter ou gérer.

## <a name="configure-azure-diagnostics-logs-and-metrics-to-sink-with-event-hubs"></a>Configurer les journaux de Diagnostics d’Azure et les mesures pour le récepteur d’événement concentrateurs

Comme indiqué précédemment, toutes les données de diagnostic personnalisés et par défaut, c'est-à-dire métriques et les journaux, est automatiquement sinked au stockage Azure dans les intervalles configurés. Avec les concentrateurs d’événement et de tout récepteur supplémentaire, vous pouvez spécifier n’importe quel nœud racine ou feuille dans la hiérarchie pour être sinked avec le concentrateur d’événements. Cela inclut les événements ETW, les compteurs de performance, les journaux des événements Windows et les journaux d’application.   

Il est important de prendre en compte le nombre de points de données doit en fait être transféré vers concentrateurs d’événement. En général, les développeurs transfèrent à chaud-chemin d’accès de données à faible latence qui doivent être utilisées et interprétées rapidement. Systèmes de surveillent des alertes ou des règles de l’échelle sont des exemples. Un développeur peut également configurer une banque autre analyse ou recherche magasin--par exemple, Azure flux Analytique, Elasticsearch, un système de surveillance personnalisé ou un système de surveillance favori des autres.

Voici quelques exemples de configurations.

```
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
</PerformanceCounters>
```

Dans l’exemple suivant, le récepteur est appliqué sur le nœud **compteurs de performance** de parent dans la hiérarchie, ce qui signifie que tous les enfants de **compteurs de performance** seront envoyées à concentrateurs d’événement.  

```
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```

Dans l’exemple précédent, le récepteur est appliqué aux seuls trois compteurs : **En file d’attente des demandes**, **Les demandes rejetées**et **% temps processeur**.  

L’exemple suivant montre comment un développeur peut limiter la quantité de données envoyées pour être les mesures critiques qui sont utilisés pour la santé de ce service.  

```
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```

Dans cet exemple, le récepteur est appliqué aux journaux et est filtré uniquement pour la trace au niveau de l’erreur.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Déploiement et mise à jour d’une configuration d’application et les tests de diagnostic de Services en nuage

Visual Studio fournit le chemin d’accès plus simple pour déployer l’application et la configuration de récepteur d’événement concentrateurs. Pour afficher et modifier le fichier, ouvrir le fichier *.wadcfgx* dans Visual Studio, le modifier et l’enregistrer. Le chemin d’accès est le **Projet de Service Cloud** > **rôles** > **(RoleName)** > **diagnostics.wadcfgx**.  

À ce stade, tout déploiement déploiement mise à jour et actions dans Visual Studio, Visual Studio Team System et toutes les commandes ou les scripts qui reposent sur MSBuild et utilisation du **/t : publier** cible incluent la *.wadcfgx* dans le processus d’empaquetage. En outre, les déploiements et les mises à jour de déploiement le fichier vers Azure à l’aide de l’extension de l’agent Azure Diagnostics appropriée sur vos ordinateurs virtuels.

Après avoir déployé l’application et la configuration des Diagnostics d’Azure, vous pouvez immédiatement voir l’activité dans le tableau de bord du concentrateur de l’événement. Cela indique que vous êtes prêt à passer à l’affichage des données à chaud-chemin d’accès dans l’outil de client ou de l’analyse de port d’écoute de votre choix.  

Dans la figure suivante, le tableau de bord de concentrateurs d’événement affiche sain envoi de données de diagnostic pour le concentrateur d’événements commençant quelques temps après 11 PM. C'est-à-dire lorsque l’application a été déployée avec un fichier de mise à jour *.wadcfgx* , et que le récepteur a été configuré correctement.

![][0]  

> [AZURE.NOTE] Lorsque vous effectuez des mises à jour dans le fichier de configuration de Diagnostics d’Azure (.wadcfgx), il est recommandé que vous poussez les mises à jour à l’ensemble de l’application, ainsi que la configuration à l’aide de la publication de Visual Studio, ou d’un script Windows PowerShell.  

## <a name="view-hot-path-data"></a>Afficher les données de chemin d’accès à chaud

Comme indiqué précédemment, il existe de nombreux exemples d’utilisation pour écouter et traitement des données d’événement concentrateurs.

Une approche simple consiste à créer une application console de test réduit pour écouter le concentrateur d’événements et d’imprimer le flux de sortie. Vous pouvez placer le code suivant, qui est expliqué plus en détail dans [mise en route avec les concentrateurs d’événement](./event-hubs-csharp-ephcs-getstarted.md)), dans une application console.  

Notez que l’application de console doit inclure le [package d’événement processeur hôte Nuget](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

N’oubliez pas de remplacer les valeurs de crochets dans la fonction **Main** avec les valeurs de vos ressources.   

```
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event Hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sink"></a>Résoudre les problèmes de récepteur d’événement concentrateurs

- Le concentrateur d’événements n’affiche pas d’activité d’événement entrant ou sortant comme prévu.

    Vérifiez que votre concentrateur d’événements soit correctement configuré. Toutes les informations de connexion dans la section **PrivateConfig** de *.wadcfgx* doivent correspondre les valeurs de votre ressource, comme dans le portail. Assurez-vous que vous disposez d’une stratégie SAS (« SendRule » dans l’exemple) définie dans le portail et que l’option *Envoyer* une autorisation est accordée.  

- Après une mise à jour, le concentrateur d’événements n’affiche plus l’activité d’événement entrant ou sortant.

    Tout d’abord, assurez-vous que les informations du concentrateur d’événements et de la configuration sont correcte, comme expliqué précédemment. Parfois, le **PrivateConfig** est réinitialisé dans une mise à jour du déploiement. La solution recommandée est à modifier tous les *.wadcfgx* dans le projet, puis envoyer une mise à jour de l’application complète. Si ce n’est pas possible, assurez-vous que la mise à jour des diagnostics exécute un push d’un **PrivateConfig** complet qui inclut la clé SAS.  

- J’ai essayé les suggestions et le concentrateur d’événements ne fonctionne toujours pas.

    Essayez de rechercher dans la table de stockage Azure qui contient les erreurs et les journaux de diagnostic Azure lui-même : **WADDiagnosticInfrastructureLogsTable**. Une option consiste à utiliser un outil tel que [Explorateur de stockage Azure](http://www.storageexplorer.com) pour vous connecter à ce compte de stockage, afficher cette table et ajouter une requête de datage au cours des 24 dernières heures. Vous pouvez utiliser l’outil pour exporter un fichier .csv et l’ouvrir dans une application telle que Microsoft Excel. Excel vous permet de facilement rechercher des chaînes de carte d’appel, par exemple **EventHubs**, pour voir quel erreur est signalée.  

## <a name="next-steps"></a>Étapes suivantes

• [En savoir plus sur les concentrateurs d’événement](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Appendice : Terminer l’exemple de fichier (.wadcfgx) de configuration de Diagnostics d’Azure

```
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount />
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

Le complémentaire *ServiceConfiguration.Cloud.cscfg* pour cet exemple ressemble à ceci.

```
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png
