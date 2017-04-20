<properties
   pageTitle="Induire le Chaos dans les clusters du Service Fabric | Microsoft Azure"
   description="À l’aide d’Injection et des API du Cluster Analysis Services pour gérer le Chaos dans le cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induire le Chaos contrôlé dans les clusters du Service Fabric
Systèmes distribués à grande échelle telles que les infrastructures de cloud sont par définition peu fiables. Azure Fabric de Service permet aux développeurs d’écrire des services fiables sur une infrastructure fiable. Pour écrire des services robustes, les développeurs doivent être capable d’induire des erreurs par rapport à ces infrastructures fiable pour tester la stabilité de leurs services.

L’injection d’erreurs et le Cluster Analysis Service (également connu sous le nom le Service d’analyse de panne) offre aux développeurs la possibilité d’induire les actions de défaillance pour tester les services. Toutefois, ciblées défaillances simulées obtenir vous que jusqu'à ce stade. Pour prendre davantage les tests, vous pouvez utiliser le Chaos.

Chaos simule les défauts continues, entrelacés (progressif et anormal) dans le cluster sur de longues périodes. Après avoir configuré le Chaos avec le taux et le type d’erreurs, vous pouvez démarrer ou arrêter via l’API C# ou PowerShell pour générer des erreurs dans le cluster et de votre service.

Pendant l’exécution de Chaos, il génère différents événements qui capturent l’état de l’exécution pour le moment. Par exemple, un ExecutingFaultsEvent contient toutes les erreurs qui sont en cours d’exécution dans cette itération. Un ValidationFailedEvent contient les détails d’une défaillance a été trouvé lors de la validation de cluster. Vous pouvez appeler l’API GetChaosReportAsync pour obtenir l’état des exécutions de Chaos.

## <a name="faults-induced-in-chaos"></a>Erreurs induites au Chaos
Chaos génère des erreurs sur l’ensemble du cluster Service Fabric et compresse les erreurs qui apparaissent dans les mois ou les années en quelques heures. La combinaison de défauts entrelacées avec le taux de défaillance élevé de recherche des cas manquants dans le cas contraire. Cet exercice Chaos conduit à une amélioration significative de la qualité du code du service.

Chaos induit des anomalies dans les catégories suivantes :

 - Redémarrer un nœud
 - Redémarrer un package de code déployé
 - Suppression d’un réplica
 - Redémarrer un réplica
 - Déplacer un réplica principal (configurable)
 - Déplacer un réplica secondaire (configurable)

Chaos s’exécute en plusieurs itérations. Chaque itération se compose des défaillances et de validation de cluster pour la période spécifiée. Vous pouvez configurer le temps passé pour le cluster de stabiliser et de validation réussisse. Si une défaillance se trouve dans la validation de cluster, le Chaos génère et persiste un ValidationFailedEvent avec l’horodatage UTC et les détails de l’échec.

Par exemple, considérez une instance de Chaos qui est définie pour s’exécuter pendant une heure, avec un maximum de trois erreurs simultanées. Chaos induit trois erreurs, puis valide le bon fonctionnement du cluster. Il parcourt le précédent passe d’étape jusqu'à ce qu’il soit explicitement arrêté par le biais de l’API StopChaosAsync ou une heure. Si le cluster est défectueux dans une itération (autrement dit, il ne pas stabiliser dans un délai configuré), Chaos génère une ValidationFailedEvent. Cet événement indique que quelque chose s’est produite et que vous ayez une investigation plus poussée.

Dans sa forme actuelle, le Chaos induit uniquement les défauts de sécurité. Cela implique que, en l’absence de défauts externes, une perte de quorum ou de la perte de données ne se produit jamais.

## <a name="important-configuration-options"></a>Options de configuration importantes
 - **TimeToRun**: Total exécution ce Chaos avant d’être terminé avec succès. Vous pouvez arrêter le Chaos avant qu’elle a exécutée pour la période TimeToRun via l’API StopChaos.
 - **MaxClusterStabilizationTimeout**: la quantité maximale de délai d’attente pour le cluster en bon état avant de vérifier à nouveau dessus. Cette attente est de réduire la charge sur le cluster pendant qu’il est de récupérer. Les vérifications effectuées sont les suivantes :
    - Si l’état de santé du cluster est OK
    - Si l’état de santé du service est OK
    - Si le réplica cible la valeur taille est réalisée pour la partition de service
    - Qu’il existe aucun réplica InBuild
 - **MaxConcurrentFaults**: le nombre maximal d’erreurs simultanées qui sont induites dans chaque itération. Le Chaos le nombre, le plus agressif supérieur est. Ainsi, des basculements sur incident plus complexes et les combinaisons de transition. Chaos garantit que, en l’absence de défauts externes, il n’y a aucune perte de quorum ou les pertes de données, quel que soit une valeur élevée comment cette configuration a.
 - **EnableMoveReplicaFaults**: Active ou désactive les défauts qui causent des réplicas principales ou secondaires à déplacer. Ces erreurs sont désactivés par défaut.
 - **WaitTimeBetweenIterations**: le laps de temps d’attente entre les itérations, c'est-à-dire après un arrondi des défaillances et la validation correspondante.
 - **WaitTimeBetweenFaults**: le laps de temps d’attente entre deux pannes consécutives dans une itération.

## <a name="how-to-run-chaos"></a>Comment exécuter le Chaos
**C# :**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell :**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
