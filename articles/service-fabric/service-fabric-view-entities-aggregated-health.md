<properties
   pageTitle="Comment afficher les entités Azure Service Fabric agrégées santé | Microsoft Azure"
   description="Décrit comment interroger, afficher et évaluer l’intégrité agrégées des entités Azure Fabric de Service, par le biais de général et les requêtes de santé."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="view-service-fabric-health-reports"></a>Afficher les rapports health Service Fabric
Azure Fabric de Service présente un [modèle de fonctionnement](service-fabric-health-introduction.md) qui comprend des entités santé système les composants et agents de surveillance peuvent signaler des conditions locales qui sont surveillés. La [santé stocker](service-fabric-health-introduction.md#health-store) regroupe toutes les données d’état pour déterminer si les entités sont en bon état.

Prêts à l’emploi, le cluster est rempli avec des rapports de santé envoyés par les composants système. Plus à [utiliser des rapports de santé système pour résoudre les problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

TISSU de service fournit plusieurs moyens d’obtenir l’état de santé agrégée des entités :

- [Service de Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou autres outils de visualisation

- Requêtes de santé (par le biais de PowerShell, API ou autres)

- Général des requêtes qui retournent une liste des entités qui ont la santé sous la forme d’une des propriétés (par le biais de PowerShell, API ou autres)

Pour illustrer ces options, nous allons utiliser un cluster local avec cinq nœuds. À côté du **fabric : / système** application (qui se trouve en dehors de la zone), d’autres applications sont déployées. L’une de ces applications est **fabric : / WordCount**. Cette application contient un service dynamique configuré avec sept réplicas. Car il n’y a que cinq nœuds, les composants du système affichent un avertissement indiquant que la partition est inférieure à count de la cible.

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Santé dans l’Explorateur de tissu de Service
Service de Fabric Explorer fournit un affichage visuel du cluster. Dans l’image ci-dessous, vous pouvez voir que :

- L’application **fabric : / WordCount** est rouge (erreur), car il a un événement d’erreur signalé par **MyWatchdog** de la **disponibilité**de la propriété.

- Un de ses services, **fabric : WordCount/WordCountService** jaune (en avertissement). Dans la mesure où le service est configuré avec des sept réplicas, ils ne peuvent pas tous placés, qu’il sont a seulement cinq nœuds. Bien qu’il n’est pas affichée ici, la partition de service est de couleur jaune en raison de l’état du système. La partition jaune déclenche le service jaune.

- Le cluster est rouge en raison de l’application.

L’évaluation utilise des stratégies par défaut à partir du manifeste de cluster et le manifeste d’application. Ils sont des stratégies strictes et ne tolèrent pas de toute défaillance.

Affichage du cluster avec un Service de Fabric Explorer :

![Affichage du cluster avec un Service de Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Apprenez-en plus sur le [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## <a name="health-queries"></a>Requêtes de santé
TISSU de service expose les requêtes de santé pour chacun des [types d’entités](service-fabric-health-introduction.md#health-entities-and-hierarchy)pris en charge. Ils sont accessibles par le biais de l’API (les méthodes sont accessibles sur le **FabricClient.HealthManager**), les applets de commande PowerShell et reste. Ces requêtes renvoient les informations de santé complet de l’entité : état agrégées, entité sanitaire événements, États de santé des enfants (le cas échéant) et des évaluations incorrectes lorsque l’entité n’est pas en bon état.

> [AZURE.NOTE] Une entité sanitaire est renvoyée lorsqu’il est rempli entièrement dans le magasin d’état de santé. L’entité doit être active (ne pas supprimés) et ont un rapport système. Ses entités parents de la chaîne de hiérarchie doivent également avoir des rapports sur le système. Si une de ces conditions n’est pas satisfaite, les requêtes de santé renvoient une exception qui indique pourquoi l’entité n’est pas retournée.

Les requêtes de santé doivent passer l’identificateur de l’entité, qui dépend du type d’entité. Les requêtes acceptent des paramètres de stratégie de santé facultatif. Si aucune stratégie de santé n’est spécifiés, les [stratégies d’intégrité](service-fabric-health-introduction.md#health-policies) à partir du manifeste d’application ou de cluster sont utilisées pour l’évaluation. Les requêtes acceptent également les filtres pour retourner uniquement les enfants partielles ou événements--celles qui respectent les filtres spécifiés.

> [AZURE.NOTE] Les filtres de sortie sont appliqués sur le côté serveur, afin que la taille de réponse du message est réduite. Il est recommandé qu’utiliser les filtres de sortie afin de limiter les données renvoyées, plutôt que d’appliquer des filtres sur le côté client.

La santé d’une entité contient :

- L’état de santé agrégées de l’entité. Calculé par la banque de santé basée sur les stratégies d’intégrité, entité sanitaire rapports et états de la santé des enfants (le cas échéant). Apprenez-en plus sur [l’évaluation de la santé d’entité](service-fabric-health-introduction.md#entity-health-evaluation).  

- Les événements d’état de l’entité.

- La collection des États de santé de tous les enfants des entités qui peuvent avoir des enfants. Les États de santé contiennent les identificateurs de l’entité et l’état de santé agrégées. Pour obtenir la santé complet pour un enfant, appelez le fonctionnement de la requête pour le type d’entité enfant et passez l’identificateur enfant.

- Évaluations non intègres qui pointent vers l’état qui a déclenché l’état de l’entité, si l’entité n’est pas en bon état.

## <a name="get-cluster-health"></a>Obtenir le bon fonctionnement du cluster
Renvoie l’état de santé de l’entité de cluster et contient les États de santé d’applications et de nœuds (enfants du cluster). Entrée :

- [Facultatif] La stratégie de santé de cluster permet d’évaluer les nœuds et les événements de cluster.

- [Facultatif] La santé stratégie mappage d’application, avec les stratégies d’intégrité permet de remplacer les stratégies de manifeste d’application.

- [Facultatif] Les filtres des événements, des nœuds et des applications qui spécifient quelles entrées sont d’intérêt et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou les avertissements et erreurs). Tous les événements, les nœuds et les applications sont utilisées pour évaluer l’intégrité d’entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir le bon fonctionnement du cluster, vous devez créer un `FabricClient` et appelez la méthode [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) sur son **HealthManager**.

L’appel suivant obtient l’état de santé du cluster :

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Le code suivant obtient l’état de santé du cluster à l’aide de filtres et une stratégie de contrôle de cluster personnalisée pour les applications et les nœuds. Il crée [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx), qui contient les informations d’entrée.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de santé du cluster est [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

L’état du cluster est cinq nœuds, l’application système et un tissu : / WordCount configuré comme indiqué.

L’applet de commande suivante obtient le bon fonctionnement du cluster à l’aide de stratégies d’intégrité par défaut. L’état de santé agrégées est avertissement, car le fabric : application de WordCount est avertissement. Notez comment les mauvais état évaluations fournissent des détails sur les conditions qui a déclenché la santé agrégée.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

L’applet de commande PowerShell suivante obtient l’état de santé du cluster à l’aide d’une stratégie d’application personnalisée. Il filtre les résultats pour obtenir uniquement les erreurs ou les applications d’avertissement et les nœuds. Par conséquent, aucun nœuds ne sont retournés, qu’ils sont tous en bon état. Que le tissu : / WordCount application respecte le filtre d’applications. Étant donné que la stratégie personnalisée spécifie pour considérer les avertissements comme des erreurs pour le fabric : / application WordCount, l’application est évaluée comme une erreur et n’est donc le cluster.

```powershell
PS c:\> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

### <a name="rest"></a>RESTE
Vous pouvez obtenir le bon fonctionnement du cluster avec un [obtenir la demande](https://msdn.microsoft.com/library/azure/dn707669.aspx) ou une [demande POST](https://msdn.microsoft.com/library/azure/dn707696.aspx) qui inclut des stratégies d’intégrité décrits dans le corps.

## <a name="get-node-health"></a>Obtenir l’état du nœud
Retourne l’état d’une entité de nœud et contient les événements d’état signalés sur le nœud. Entrée :

- [Obligatoire] Le nom du nœud qui identifie le nœud.

- [Facultatif] Les paramètres de stratégie de santé cluster utilisés pour évaluer l’intégrité.

- [Facultatif] Filtre les événements qui indiquent les entrées qui sont d’intérêt et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou les avertissements et erreurs). Tous les événements sont utilisés pour évaluer l’intégrité d’entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir l’état du nœud via l’API, créer un `FabricClient` et appelez la méthode [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) sur son HealthManager.

Le code suivant obtient l’état de santé de nœud pour le nom du nœud spécifié :

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Le code suivant obtient l’état de santé de nœud pour le nom du nœud spécifié et passe dans le filtre d’événements et de stratégie personnalisée par le biais de [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état du nœud est [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .
L’applet de commande suivante obtient l’état de santé du nœud à l’aide de stratégies d’intégrité par défaut :

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

L’applet de commande suivante obtient l’état de santé de tous les nœuds du cluster :

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir l’état de nœud avec un [obtenir la demande](https://msdn.microsoft.com/library/azure/dn707650.aspx) ou une [demande POST](https://msdn.microsoft.com/library/azure/dn707665.aspx) qui inclut des stratégies d’intégrité décrits dans le corps.

## <a name="get-application-health"></a>Obtenir l’état de l’application
Retourne l’état d’une entité de l’application. Il contient les États de santé de l’application déployée et les enfants du service. Entrée :

- [Obligatoire] Le nom de l’application (URI) qui identifie l’application.

- [Facultatif] La stratégie d’intégrité d’application permet de remplacer les stratégies de manifeste d’application.

- [Facultatif] Les filtres des événements, des services et des applications déployées qui indiquent les entrées présentent un intérêt et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou les avertissements et erreurs). Tous les événements, les services et les applications déployées sont utilisées pour évaluer l’intégrité d’entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir l’état de l’application, vous devez créer un `FabricClient` et appelez la méthode [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) sur son HealthManager.

Le code suivant obtient l’état de l’application pour le nom de l’application spécifiée (URI) :

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Le code suivant obtient l’état de l’application pour le nom de l’application spécifiée (URI), avec des filtres et des stratégies personnalisées spécifiés via [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de l’application est [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

L’applet de commande suivant renvoie l’état de santé de la **fabric : / WordCount** application :

```powershell
PS c:\>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

L’applet de commande PowerShell suivante transmet des stratégies personnalisées. Il filtre également les enfants et les événements.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir la santé des applications avec un [obtenir la demande](https://msdn.microsoft.com/library/azure/dn707681.aspx) ou une [demande POST](https://msdn.microsoft.com/library/azure/dn707643.aspx) qui inclut des stratégies d’intégrité décrits dans le corps.

## <a name="get-service-health"></a>Obtenir le fonctionnement du service
Retourne l’état d’une entité de service. Il contient les États de santé de partition. Entrée :

- [Obligatoire] Le nom du service (URI) qui identifie le service.

- [Facultatif] La stratégie d’intégrité d’application utilisée pour substituer la stratégie du manifeste de l’application.

- [Facultatif] Filtres pour les événements et les partitions qui spécifient quelles entrées sont d’intérêt et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou les avertissements et erreurs). Tous les événements et les partitions sont utilisées pour évaluer l’intégrité d’entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir le fonctionnement du service par le biais de l’API, créer un `FabricClient` et appelez la méthode [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) sur son HealthManager.

L’exemple suivant obtient l’état de santé d’un service avec le nom de service spécifié (URI) :

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Le code suivant obtient le fonctionnement du service pour le nom de service spécifié (URI), spécification de filtres et stratégie personnalisée via [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de santé du service est [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

L’applet de commande suivante obtient l’état de santé du service à l’aide de stratégies d’intégrité par défaut :

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir le fonctionnement du service à [obtenir la demande](https://msdn.microsoft.com/library/azure/dn707609.aspx) ou une [demande POST](https://msdn.microsoft.com/library/azure/dn707646.aspx) qui inclut des stratégies d’intégrité décrits dans le corps.

## <a name="get-partition-health"></a>Obtenir l’état de la partition
Retourne l’état d’une entité de la partition. Il contient les États de santé de réplica. Entrée :

- [Obligatoire] ID (GUID) qui identifie la partition de la partition.

- [Facultatif] La stratégie d’intégrité d’application utilisée pour substituer la stratégie du manifeste de l’application.

- [Facultatif] Filtres de réplicas qui indiquent les entrées et les événements sont d’intérêt et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou les avertissements et erreurs). Tous les événements et les réplicas sont utilisés pour évaluer l’intégrité d’entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir la santé de la partition par le biais de l’API, créer un `FabricClient` et appelez la méthode [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) sur son HealthManager. Pour spécifier des paramètres facultatifs, créer [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de la partition est [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

L’applet de commande suivante obtient l’état de toutes les partitions de la **fabric : WordCount/WordCountService** service :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir l’état de partition avec un [obtenir la demande](https://msdn.microsoft.com/library/azure/dn707683.aspx) ou une [demande POST](https://msdn.microsoft.com/library/azure/dn707680.aspx) qui inclut des stratégies d’intégrité décrits dans le corps.

## <a name="get-replica-health"></a>Obtenir l’état de réplica
Renvoie l’état de santé d’un réplica avec état de service ou d’une instance de service sans état. Entrée :

- [Obligatoire] L’ID (GUID) et réplica ID de partition qui identifie le réplica.

- [Facultatif] Les paramètres de stratégie de santé application utilisées pour remplacer les stratégies de manifeste d’application.

- [Facultatif] Filtre les événements qui indiquent les entrées qui sont d’intérêt et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou les avertissements et erreurs). Tous les événements sont utilisés pour évaluer l’intégrité d’entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir l’état de santé du réplica via l’API, vous devez créer un `FabricClient` et appelez la méthode [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) sur son HealthManager. Pour spécifier des paramètres avancés, utilisez [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de santé du réplica est [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

L’applet de commande suivante obtient l’état de santé du réplica principal pour toutes les partitions du service :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir l’état de réplica avec un [obtenir la demande](https://msdn.microsoft.com/library/azure/dn707673.aspx) ou une [demande POST](https://msdn.microsoft.com/library/azure/dn707641.aspx) qui inclut des stratégies d’intégrité décrits dans le corps.

## <a name="get-deployed-application-health"></a>Obtenir l’état de l’application déployée
Renvoie l’état de santé d’une application déployée sur une entité de nœud. Il contient les États de santé package service déployé. Entrée :

- [Obligatoire] Le nom de l’application (URI) et nom du nœud (string) qui identifie l’application déployée.

- [Facultatif] La stratégie d’intégrité d’application permet de remplacer les stratégies de manifeste d’application.

- [Facultatif] Filtres pour les événements et les packages de service déployé qui spécifient quelles entrées sont d’intérêt et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou les avertissements et erreurs). Tous les événements et les services déployés sont utilisés pour évaluer l’intégrité d’entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir l’état de santé d’une application déployée sur un nœud via l’API, vous devez créer un `FabricClient` et appelez la méthode [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) sur son HealthManager. Pour spécifier des paramètres facultatifs, utilisez [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de l’application déployée est [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) . Pour savoir où une application est déployée, exécutez [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) et examinez les enfants de l’application déployée.

L’applet de commande suivante obtient l’état de santé de la **fabric : / WordCount** application déployée sur **_Node_2**.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir la santé application déployée avec un [obtenir la demande](https://msdn.microsoft.com/library/azure/dn707644.aspx) ou une [demande POST](https://msdn.microsoft.com/library/azure/dn707688.aspx) qui inclut des stratégies d’intégrité décrits dans le corps.

## <a name="get-deployed-service-package-health"></a>Obtenir la santé package service déployé
Retourne l’état d’une entité de package service déployé. Entrée :

- [Obligatoire] Le nom de l’application (URI), le nom de nœud (chaîne) et service name (chaîne) qui identifient le package de service déployé du manifeste.

- [Facultatif] La stratégie d’intégrité d’application utilisée pour substituer la stratégie du manifeste de l’application.

- [Facultatif] Filtre les événements qui indiquent les entrées qui sont d’intérêt et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou les avertissements et erreurs). Tous les événements sont utilisés pour évaluer l’intégrité d’entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir l’état de santé d’un package de service déployé par l’intermédiaire de l’API, vous devez créer un `FabricClient` et appelez la méthode [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) sur son HealthManager. Pour spécifier des paramètres facultatifs, utilisez [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de santé du package service déployé est [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) . Pour voir où une application est déployée, exécutez [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) et consultez les applications déployées. Pour voir quel packages se trouvent dans une application de service, examinez les enfants de package service déployé dans la sortie de [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) .

L’applet de commande suivante obtient l’état de santé du package de service **WordCountServicePkg** de la **fabric : / WordCount** application déployée sur **_Node_2**. L’entité a le rapports de **System.Hosting** pour la réussite du lot-service et point d’entrée, enregistrement et l’activation réussie-type de service.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir la santé package service déployé avec un [obtenir la demande](https://msdn.microsoft.com/library/azure/dn707677.aspx) ou une [demande POST](https://msdn.microsoft.com/library/azure/dn707689.aspx) qui inclut des stratégies d’intégrité décrits dans le corps.

## <a name="health-chunk-queries"></a>Requêtes de segment de santé
Les requêtes de segment de santé peuvent renvoyer des enfants de cluster multi niveau (de manière récursive), par des filtres d’entrée. Il prend en charge les filtres avancés qui permettent de flexibilité exprimer les enfants spécifiques à renvoyer, identifiées par leur identificateur unique ou autre identificateur de groupe et/ou l’état de santé. Par défaut, aucun enfant n’est inclus, par opposition aux commandes de santé qui comprennent toujours des enfants de premier niveau.

Les [requêtes de santé](service-fabric-view-entities-aggregated-health.md#health-queries) renvoie uniquement les enfants de premier niveau de l’entité spécifiée par les filtres requis. Pour obtenir les enfants des enfants, vous devez appeler les API de santé supplémentaire pour chaque entité d’intérêt. De même, pour obtenir l’état de santé d’entités spécifiques, vous devez appeler une santé API pour chaque entité de votre choix. La requête de segment filtrage avancée vous permet de demander plusieurs éléments d’intérêt dans une même requête, en réduisant la taille des messages et le nombre de messages.

La valeur de la requête de bloc est que vous pouvez obtenir d’état pour plusieurs entités de cluster (potentiellement toutes les entités de cluster commençant à la racine requis) en un seul appel. Vous pouvez exprimer les requêtes complexes de santé tels que :

- Retourner uniquement les applications à erreur et pour les applications comprennent tous les services à avertissement | erreur. Pour les services de le renvoyée, incluez toutes les partitions.

- Renvoyer uniquement l’état de santé de 4 applications, spécifiés par leur nom.

- Retourner uniquement la santé des applications d’un type d’application de votre choix.

- Retour déployées toutes les entités sur un nœud. Retourne toutes les applications, tous les déploiement des applications sur le nœud spécifié et tous les packages de service déployé sur ce nœud.

- Retourner tous les réplicas à l’erreur. Retourne toutes les applications, les services, les partitions et les seuls les réplicas à l’erreur.

- Renvoyer toutes les applications. Pour un service spécifié, incluez toutes les partitions.

Actuellement, la requête de bloc de santé est exposée uniquement pour l’entité de cluster. Elle renvoie un morceau de santé de cluster, qui contient :

- L’état du cluster agrégée.

- La liste de segment d’état de santé des nœuds qui respectent des filtres d’entrée.

- La liste de segment d’état de santé des applications qui respectent des filtres d’entrée. Chaque segment d’état de santé application contient une liste de blocs avec tous les services qui respectent une liste de blocs avec toutes les applications déployées qui respectent les filtres et les filtres d’entrée. Même pour les enfants des services et des applications déployées. De cette manière, toutes les entités de cluster peuvent être potentiellement renvoyées si nécessaire, de manière hiérarchique.

### <a name="cluster-health-chunk-query"></a>Requête de bloc de santé de cluster
Renvoie l’état de santé de l’entité de cluster et contient les segments d’état de santé hiérarchique enfants requis. Entrée :

- [Facultatif] La stratégie de santé de cluster permet d’évaluer les nœuds et les événements de cluster.

- [Facultatif] La santé stratégie mappage d’application, avec les stratégies d’intégrité permet de remplacer les stratégies de manifeste d’application.

- [Facultatif] Filtres pour les applications qui indiquent les entrées et les nœuds sont d’intérêt et le résultat doivent être renvoyés. Les filtres sont spécifiques à un entité ou au groupe d’entités ou sont applicables à toutes les entités à ce niveau. La liste de filtres peut contenir un filtre général et/ou des filtres pour les identificateurs spécifiques à grain fin des entités retournées par la requête. Si elle est vide, les enfants ne sont pas retournés par défaut.
Apprenez-en plus sur les filtres à [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) et [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx). La manière récursive d’application filtres peuvent spécifier les filtres avancés pour les enfants.

Le résultat de segment inclut les enfants qui respectent les filtres.

Actuellement, la requête de segment ne renvoie pas les évaluations défectueuses ou des événements de l’entité. Que des informations supplémentaires peuvent être obtenues à l’aide de la requête d’état de cluster existante.

### <a name="api"></a>API
Pour obtenir un segment d’état de cluster, créez un `FabricClient` et appelez la méthode [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) sur son **HealthManager**. Vous pouvez passer dans [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) pour décrire les stratégies d’intégrité et filtres avancés.

Le code suivant obtient le segment d’état de cluster des filtres avancés.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de santé du cluster est [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

Le code suivant obtient les nœuds que s’ils sont dans l’erreur à l’exception d’un nœud spécifique, qui doit toujours être retourné.

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

L’applet de commande suivante obtient le segment du cluster avec les filtres d’application.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

L’applet de commande suivant retourne toutes les entités déployées sur un nœud.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir le segment de la santé de cluster avec un [obtenir la demande](https://msdn.microsoft.com/library/azure/mt656722.aspx) ou une [demande POST](https://msdn.microsoft.com/library/azure/mt656721.aspx) qui inclut des stratégies d’intégrité et les filtres avancés décrits dans le corps.

## <a name="general-queries"></a>Requêtes générales
Requêtes générales renvoient une liste d’entités de Service Fabric d’un type spécifié. Ils sont exposés via l’API (via les méthodes sur **FabricClient.QueryManager**), les applets de commande PowerShell et autres. Ces requêtes d’agrégation sous-requêtes à partir de plusieurs composants. Un d’eux est du [stocker de santé](service-fabric-health-introduction.md#health-store), qui remplit l’état de santé agrégées pour chaque résultat de requête.  

> [AZURE.NOTE] Requêtes générales renvoient l’état de santé agrégées de l’entité et ne contiennent pas de données de santé riche. Si une entité n’est pas correcte, vous pouvez suivre avec les requêtes de santé pour obtenir toutes ses informations de santé, y compris les évaluations défectueuses, des États de santé des enfants et des événements.

Si des requêtes générales renvoient un état inconnu pour une entité, il est possible que le magasin d’état de santé n’a pas de données complètes sur l’entité. Il est également possible qu’une sous-requête dans le magasin d’état de santé n’a pas été réussie (par exemple, une erreur de communication s’est produite, ou le magasin d’état a été accéléré). Suivi avec une requête de santé pour l’entité. Si la sous-requête produit des erreurs transitoires, tels que des problèmes de réseau, cette requête de suivi peut réussir. Il peut également vous donner plus de détails à partir du magasin de santé à propos de la raison pour laquelle l’entité n’est pas exposée.

Les requêtes qui contiennent des **HealthState** pour les entités sont les suivantes :

- Liste de nœuds : retourne les nœuds de la liste dans le cluster (paginé).
  - API : [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell : Get-ServiceFabricNode
- Liste des applications : retourne la liste des applications dans le cluster (paginé).
  - API : [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell : Get-ServiceFabricApplication
- Liste de service : retourne la liste des services dans une application (paginée).
  - API : [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell : Get-ServiceFabricService
- Liste de partitions : retourne la liste des partitions dans un service (paginé).
  - API : [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell : Get-ServiceFabricPartition
- Liste des réplicas : retourne la liste de réplicas d’une partition (paginée).
  - API : [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell : Get-ServiceFabricReplica
- Déployer la liste des applications : renvoie la liste des applications déployées sur un nœud.
  - API : [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell : Get-ServiceFabricDeployedApplication
- Déployer la liste des packages de service : retourne la liste des packages de service dans une application déployée.
  - API : [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell : Get-ServiceFabricDeployedApplication

> [AZURE.NOTE] Certaines requêtes renvoient des résultats paginés. Le retour de ces requêtes est une liste dérivée de [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx). Si les résultats ne correspondent pas à un message, une page est renvoyée et un ContinuationToken qui effectue le suivi où l’énumération s’est arrêté. Vous devez continuer à appeler de la même requête et passez le jeton de continuation à partir de la requête précédente pour obtenir les résultats suivants.

### <a name="examples"></a>Exemples

Le code suivant obtient les applications défaillantes dans le cluster :

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

L’applet de commande suivante obtient les détails de l’application pour le fabric : / application de WordCount. Notez que l’état de santé est au niveau d’avertissement.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

L’applet de commande suivante obtient les services avec un état d’avertissement :

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## <a name="cluster-and-application-upgrades"></a>Mises à niveau de cluster et d’application
Au cours d’une mise à niveau contrôlée du cluster et des applications, Service Fabric vérifie la santé pour vous assurer que tout ce qui reste sain. Si une entité est incorrecte comme évaluée à l’aide de stratégies d’intégrité configurée, la mise à niveau s’applique des stratégies de mise à niveau spécifiques pour déterminer l’action suivante. La mise à niveau peut être suspendu pour permettre l’interaction avec l’utilisateur (telles que la fixation des conditions d’erreur ou la modification des stratégies), ou il peut automatiquement restaurer la version correcte précédente.

Au cours d’une mise à niveau de *cluster* , vous pouvez obtenir l’état de mise à niveau de cluster. L’état de mise à niveau inclut les évaluations défectueuses, qui pointent vers ce qui ne fonctionne pas correctement dans le cluster. Si la mise à niveau est annulée en raison de problèmes de santé, l’état de mise à niveau garde en mémoire les derniers raisons défectueux. Cette information peut aider les administrateurs à examiner la cause du problème après que la mise à niveau annulée ou arrêtée.

De même, au cours d’une mise à niveau de *l’application* , les évaluations défectueuses sont contenues dans l’état de mise à niveau d’application.

L’exemple suivant montre l’état de mise à niveau d’application pour un tissu modifié : / application de WordCount. Une surveillance a rapporté une erreur sur l’un de ses réplicas. La mise à niveau est propagée car les contrôles ne sont pas respectées.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Plus d’informations sur [mise à niveau de l’application de Service de Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Utiliser les évaluations d’intégrité pour résoudre les problèmes
Chaque fois qu’il existe un problème avec le cluster ou une application, examinez l’état du cluster ou de l’application pour identifier quel est le problème. Les évaluations défectueuses fournissent des détails sur ce qui a déclenché l’état défectueux en cours. Si vous le souhaitez, vous pouvez approfondir les entités enfant défectueux pour identifier la cause.

> [AZURE.NOTE] Les évaluations de mauvais état affichent que la raison première de l’entité est évaluée à l’état en cours. Il peut y avoir plusieurs autres événements qui déclenchent cet état, mais ils ne sont pas répercutées dans les évaluations. Pour obtenir plus d’informations, consulter les entités santé pour connaître tous les rapports dans le cluster défectueux.

## <a name="next-steps"></a>Étapes suivantes
[Utiliser des rapports de santé système pour résoudre les problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Ajouter des rapports personnalisés de health Service Fabric](service-fabric-report-health.md)

[Comment signaler et vérifier l’intégrité du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Surveiller et diagnostiquer des services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Mise à niveau des applications de service Fabric](service-fabric-application-upgrade.md)
