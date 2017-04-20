<properties
   pageTitle="Action de testabilité | Microsoft Azure"
   description="Cet article traite de l’actions testabilité trouvées dans le Service Microsoft Azure Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="timlt"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/03/2016"
   ms.author="motanv;heeldin"/>

# <a name="testability-actions"></a>Actions de testabilité
Afin de simuler une infrastructure fiable, Azure Fabric de Service vous fournit, le développeur, avec des moyens pour simuler différents de défaillances réelles et les transitions d’état. Elles sont exposées en tant qu’actions de testabilité. Les actions sont des API de bas niveau qui provoquent une injection d’erreurs spécifiques, une transition d’état ou validation. En combinant ces actions, vous pouvez écrire des scénarios de test complet de vos services.

Service de Fabric fournit que des scénarios de test courants composent de ces actions. Il est vivement recommandé que vous utilisez ces scénarios prédéfinis, qui sont choisis avec soin afin de tester les transitions d’état commun et les cas d’échec. Toutefois, les actions peuvent être utilisées pour créer des scénarios de test personnalisée lorsque vous souhaitez ajouter la couverture pour les scénarios qui ne relèvent pas les scénarios intégrés encore ou qui sont personnalisés adaptés à votre application.

Les implémentations C# des actions sont trouvent dans l’assembly System.Fabric.dll. Le module PowerShell de Fabric système se trouve dans l’assembly Microsoft.ServiceFabric.Powershell.dll. Dans le cadre de l’installation du runtime, le module ServiceFabric PowerShell est installé pour permettre la facilité d’utilisation.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Progressif et actions de pannes anormal
Actions de testabilité sont classées en deux compartiments principaux :

* Erreurs anormal : ces erreurs de simuler des défaillances, comme le redémarrage de l’ordinateur et traiter les incidents. Dans ce cas d’échec, le contexte d’exécution du processus s’arrête brusquement. Cela signifie qu'aucun nettoyage de l’état ne peut s’exécuter avant le démarrage de l’application à nouveau.

* Erreurs progressif : ces erreurs de simuler les actions progressive comme réplica déplace et gouttes déclenchées par l’équilibrage de charge. Dans ce cas, le service reçoit une notification de la fermeture et peut nettoyer l’état avant de quitter.

Pour la validation de meilleure qualité, exécuter la charge de travail de service et de l’entreprise lors de l’induction de diverses erreurs de progressif et anormal. Erreurs anormal exercent des scénarios où le processus de service se ferme brusquement au milieu de certains flux de travail. Cette commande teste le chemin d’accès de restauration une fois que le réplica de service est restauré par le Service Fabric. Cela permettra de tester la cohérence des données et si l’état du service est mis à jour correctement après des échecs. L’autre ensemble de test échecs (le logiciel) que le service est correctement réagit aux réplicas déplacés ce par Fabric du Service. Cette commande teste la gestion de l’annulation dans la méthode RunAsync. Le service doit vérifier le jeton d’annulation en cours de jeu, enregistrer correctement son état et quitter la méthode RunAsync.

## <a name="testability-actions-list"></a>Liste des actions de testabilité

| Action | Description | API managée | Applet de commande PowerShell | Erreurs GRACEFUL/anormal |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Supprime l’état de test à partir du cluster dans le cas d’un arrêt incorrect du test pilote. | CleanTestStateAsync | Supprimer-ServiceFabricTestState | Ne s’applique pas |
| InvokeDataLoss | Provoque la perte de données dans une partition de service. | InvokeDataLossAsync | Appel de code non-ServiceFabricPartitionDataLoss | Progressif |
| InvokeQuorumLoss | Place une partition de service avec état donné en perte de quorum. | InvokeQuorumLossAsync | Appel de code non-ServiceFabricQuorumLoss | Progressif |
| Déplacer le principal | Déplace le réplica principal spécifié d’un service dynamique vers le nœud de cluster spécifié. | MovePrimaryAsync | Déplacement-ServiceFabricPrimaryReplica | Progressif |
| Déplacement secondaire | Déplace le réplica secondaire en cours d’un service dynamique vers un nœud de cluster différent. | MoveSecondaryAsync | Déplacement-ServiceFabricSecondaryReplica | Progressif |
| RemoveReplica | Simule une défaillance du réplica en supprimant un réplica à partir d’un cluster. Ceci va fermer le réplica et passera au rôle 'None', supprimer tout de l’état du cluster. | RemoveReplicaAsync | Supprimer-ServiceFabricReplica | Progressif |
| RestartDeployedCodePackage | Simule un échec du processus code package en redémarrant un package de code déployé sur un nœud dans un cluster. Cela annule le processus de package de code, qui redémarre tous les réplicas de service utilisateur hébergés dans ce processus. | RestartDeployedCodePackageAsync | Redémarrage-ServiceFabricDeployedCodePackage | Anormal |
| RestartNode | Simule la défaillance d’un nœud de cluster Service Fabric en redémarrant un nœud. | RestartNodeAsync | Redémarrage-ServiceFabricNode | Anormal |
| RestartPartition | Simule un scénario de panne d’électricité panne d’électricité ou de cluster datacenter en redémarrant certains ou tous les réplicas d’une partition. | RestartPartitionAsync | Redémarrage-ServiceFabricPartition | Progressif |
| RestartReplica | Simule une défaillance du réplica en redémarrer un réplica persistant dans un cluster, en fermant le réplica et en rouvrant. | RestartReplicaAsync | Redémarrage-ServiceFabricReplica | Progressif |
| StartNode | Démarre un nœud dans un cluster est déjà arrêté. | StartNodeAsync | Démarrer-ServiceFabricNode | Ne s’applique pas |
| StopNode | Simule une défaillance de nœud à l’arrêt d’un nœud dans un cluster. Le nœud reste vers le bas jusqu'à ce que StartNode est appelée. | StopNodeAsync | Stop-ServiceFabricNode | Anormal |
| ValidateApplication | Valide la disponibilité et l’intégrité de tous les services de Fabric de Service au sein d’une application, généralement après induire des erreurs dans le système. | ValidateApplicationAsync | Test-ServiceFabricApplication | Ne s’applique pas |
| ValidateService | Valide la disponibilité et l’état d’un service de Fabric de Service, généralement après induire des erreurs dans le système. | ValidateServiceAsync | Test-ServiceFabricService | Ne s’applique pas |

## <a name="running-a-testability-action-using-powershell"></a>Exécution d’une action de test à l’aide de PowerShell

Ce didacticiel vous montre comment exécuter une action de test à l’aide de PowerShell. Vous allez apprendre à exécuter une action de test par rapport à un cluster (une case) local ou sur un cluster Azure. Microsoft.Fabric.Powershell.dll : le module PowerShell de tissu de Service--est installé automatiquement lorsque vous installez Microsoft Service Fabric MSI. Le module est chargé automatiquement lorsque vous ouvrez une invite de commandes PowerShell.

Segments de didacticiels :

- [Exécuter une action sur un cluster d’un emploi](#run-an-action-against-a-one-box-cluster)
- [Exécuter une action sur un cluster Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Exécuter une action sur un cluster d’un emploi

Pour exécuter une action de test par rapport à un cluster local, tout d’abord de se connecter au cluster et ouvrez l’invite de PowerShell en mode administrateur. Examinons l’action **Restart-ServiceFabricNode** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Ici l’action **Restart-ServiceFabricNode** est exécutée sur un nœud nommé « Node1 ». Le mode de saisie semi-automatique indique qu’il ne doit pas vérifier si l’action de redémarrage-nœud réussi. Spécifier le mode de saisie semi-automatique dans la mesure où « Vérifier » pour vérifier si l’action de redémarrage réussi. Au lieu de spécifier directement le nœud par son nom, vous pouvez le spécifier via une clé de partition et le type de réplica, comme suit :

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Redémarrage-ServiceFabricNode** permet de redémarrer un nœud Fabric du Service dans un cluster. Ceci va arrêter le processus Fabric.exe, qui va redémarrer tous les système utilisateur et service service réplicas hébergés sur ce nœud. Pour tester votre service à l’aide de cette API, vous aide à découvrir des bogues suivant les chemins de récupération avec basculement. Il permet de simuler des défaillances de nœud dans le cluster.

La capture d’écran suivante illustre la commande de testabilité **Restart-ServiceFabricNode** en action.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

La sortie de la première **Get-ServiceFabricNode** (une applet de commande du module PowerShell de tissu de Service) indique que le cluster local comporte cinq nœuds : Node.1 à Node.5. Après l’exécution de l’action de testabilité (applet de commande) **ServiceFabricNode-redémarrer** sur le nœud, nommé Node.4, nous voyons que le temps de fonctionnement du nœud a été réinitialisé.

### <a name="run-an-action-against-an-azure-cluster"></a>Exécuter une action sur un cluster Azure

Exécute une action de testabilité (à l’aide de PowerShell) par rapport à un cluster Azure est semblable à l’action en cours d’exécution sur un cluster local. La seule différence est qu’avant de pouvoir exécuter l’action, plutôt que sur le cluster local, vous devez tout d’abord de se connecter au cluster Azure.

## <a name="running-a-testability-action-using-c35"></a>Exécution d’une action de test à l’aide de C & #35 ;

Pour exécuter une action de test à l’aide de C#, vous devez d’abord vous connecter au cluster à l’aide de FabricClient. Obtenir les paramètres nécessaires à l’exécution de l’action. Des paramètres différents peuvent être utilisés pour exécuter la même action.
En examinant l’action RestartServiceFabricNode, l’une pour l’exécuter consiste en utilisant les informations de nœud (nom de nœud et ID d’instance de nœud) du cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explication du paramètre :

- **CompleteMode** Spécifie que le mode ne doit pas vérifier si l’action de redémarrage réussi. Spécifier le mode de saisie semi-automatique dans la mesure où « Vérifier » pour vérifier si l’action de redémarrage réussi.  
- **OperationTimeout** définit la quantité de temps de l’opération se termine avant une exception TimeoutException est levée.
- **CancellationToken** permet un appel en attente d’être annulée.

Au lieu de spécifier directement le nœud par son nom, vous pouvez le spécifier via une clé de partition et le type de réplica.

Pour plus d’informations, consultez [PartitionSelector et ReplicaSelector](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector et ReplicaSelector

### <a name="partitionselector"></a>PartitionSelector
PartitionSelector est une application d’assistance exposée dans la testabilité et est utilisé pour sélectionner une partition spécifique sur lequel effectuer toutes les actions de la testabilité. Il peut être utilisé pour sélectionner une partition spécifique si l’ID de partition est connu à l’avance. Ou, vous pouvez fournir la clé de partition, l’opération peut résoudre l’ID de partition en interne. Vous avez également la possibilité de sélectionner une partition aléatoire.

Pour utiliser cette application d’assistance, créer l’objet PartitionSelector et sélectionnez la partition à l’aide d’une des méthodes Select *. Passez dans l’objet PartitionSelector de l’API qui en a besoin. Si aucune option n’est sélectionnée, la valeur par défaut est une partition aléatoire.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector est une application d’assistance exposée dans la testabilité et est utilisé pour sélectionner un réplica sur lequel effectuer toutes les actions de la testabilité. Il peut être utilisé pour sélectionner un réplica spécifique si l’ID de réplica est connu à l’avance. En outre, vous avez la possibilité de sélectionner un réplica principal ou secondaire aléatoire. ReplicaSelector dérive de PartitionSelector, vous devez sélectionner à la fois la duplication et la partition sur laquelle vous souhaitez effectuer la testabilité.

Pour utiliser cette application d’assistance, créer un objet ReplicaSelector et définir la façon dont vous souhaitez sélectionner le réplica et la partition. Vous pouvez ensuite le passer dans l’API qui en a besoin. Si aucune option n’est activée, par défaut un réplica aléatoire et une partition aléatoire.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Étapes suivantes

- [Scénarios de test](service-fabric-testability-scenarios.md)
- Comment faire pour tester votre service
   - [Simuler des défaillances au cours des charges de travail de service](service-fabric-testability-workload-tests.md)
   - [Échecs de communication du service-service](service-fabric-testability-scenarios-service-communication.md)
