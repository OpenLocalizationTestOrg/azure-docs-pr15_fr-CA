<properties
   pageTitle="Résoudre les problèmes avec les rapports d’intégrité système | Microsoft Azure"
   description="Décrit les rapports d’état envoyés par les composants d’Azure Fabric de Service et leur utilisation pour la résolution des problèmes de cluster ou des problèmes d’application."
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

# <a name="use-system-health-reports-to-troubleshoot"></a>Utiliser des rapports de santé système pour résoudre les problèmes

Azure Fabric du Service état composants prêts à l’emploi sur toutes les entités dans le cluster. Le [stockage de santé](service-fabric-health-introduction.md#health-store) crée et supprime les entités basées sur les rapports du système. Il organise également les dans une hiérarchie qui capture des interactions de l’entité.

> [AZURE.NOTE] Pour comprendre les concepts liés à la santé, en savoir plus au [modèle de fonctionnement de Service Fabric](service-fabric-health-introduction.md).

Rapports d’intégrité système fournissent une visibilité sur le cluster et les fonctionnalités de l’application et indicateur des problèmes par le biais de la santé. Pour les applications et les services, les rapports d’intégrité système vérifient que les entités sont implémentées et sont comportent correctement du point de vue Fabric du Service. Les rapports n’offrent pas de n’importe quel contrôle du fonctionnement de la logique métier du service ou de la détection de processus bloqués. Services utilisateur peuvent enrichir les données de santé avec des informations spécifiques à leur logique.

> [AZURE.NOTE] Agents de surveillance les rapports d’intégrité sont visibles uniquement *une fois que* les composants système de créent une entité. Lorsqu’une entité est supprimée, le magasin d’état de santé supprime automatiquement tous les rapports d’intégrité lui est associés. S’applique lors de la création d’une nouvelle instance de l’entité (par exemple, une nouvelle instance de réplica de service est créée). Tous les rapports associés à l’ancienne instance sont supprimés et nettoyés de la banque.

Le composant système rapports sont identifiées par la source, qui commence par le «**système.**» préfixe. Agents de surveillance ne peut pas utiliser le même préfixe pour leurs sources, comme des rapports avec des paramètres incorrects sont rejetées.
Examinons certains rapports système à comprendre ce qui les déclenche et comment corriger les problèmes possibles qu’ils représentent.

> [AZURE.NOTE] Service de Fabric continue d’ajouter des rapports sur les conditions d’intérêt qui améliorent la visibilité de ce qui se passe dans le cluster et l’application.

## <a name="cluster-system-health-reports"></a>Rapports d’intégrité de système de cluster
L’entité de santé du cluster est créée automatiquement dans le magasin d’état de santé. Si tout fonctionne correctement, il n’a pas un état du système.

### <a name="neighborhood-loss"></a>Perte de voisinage
**System.Federation** signale une erreur lorsqu’il détecte une perte de voisinage. Le rapport est à partir des nœuds individuels, et l’ID de nœud est inclus dans le nom de propriété. Si un cercle est perdu dans l’anneau de Service Fabric entière, vous pouvez généralement vous attendre deux événements (les deux côtés de l’état d’écart). Si plusieurs cercles sont perdus, il y a plus d’événements.

Le rapport indique le délai d’expiration du bail global en tant que la durée de vie. Le rapport est renvoyé chaque moitié de la durée de vie de tant que la condition reste active. L’événement est automatiquement supprimé lorsqu’il arrive à expiration. Supprimer lorsque le comportement expiré permet de s’assurer que le rapport est nettoyé à partir du magasin de santé correctement, même si le nœud de déclaration est arrêté.

- **SourceId**: System.Federation
- **Propriété**: commence par **Voisinage** et inclut des informations de nœud
- **Prochaines étapes**: recherchez pourquoi le voisinage est perdu (par exemple, vérifier la communication entre les nœuds du cluster).

## <a name="node-system-health-reports"></a>Rapports d’intégrité de système de nœud
**System.FM**, qui représente le service de gestionnaire de basculement, est l’autorité qui gère les informations sur les nœuds de cluster. Chaque nœud doit avoir un rapport à partir de System.FM afficher son état. Les entités de nœud sont supprimées lorsque l’état du nœud est supprimé (voir [RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)).

### <a name="node-updown"></a>Nœud de haut en bas
System.FM signale comme OK lorsque le nœud rattache à l’anneau (il est en cours d’exécution). Il signale une erreur lorsque le nœud de départ de l’anneau (elle est activée, de mise à niveau ou simplement parce qu’il a échoué). La hiérarchie de santé générée par le magasin d’état de santé effectue une action sur les entités déployées en corrélation avec les rapports de nœud System.FM. Il considère que le nœud virtuel parent de toutes les entités déployés. Les entités déployées sur ce nœud sont exposées par le biais de requêtes si le nœud est signalé comme étant en service par System.FM, avec la même instance que l’instance associée aux entités. Lors de la System.FM indique que le nœud est arrêté ou redémarré (une nouvelle instance), le magasin d’état de santé nettoie automatiquement les entités déployées qui peuvent exister uniquement sur le nœud vers le bas ou sur l’instance précédente du nœud.

- **SourceId**: System.FM
- **Propriété**: état
- **Prochaines étapes**: si le nœud est en panne pour une mise à niveau, il doit s’afficher en une fois qu’il a été mis à niveau. Dans ce cas, l’état de santé doit être remis à OK. Si le nœud ne réapparaissent ou si elle échoue, le problème a besoin de plus d’enquête.

L’exemple suivant affiche l’événement System.FM avec un état d’intégrité de OK pour un nœud :

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### <a name="certificate-expiration"></a>Expiration du certificat
**System.FabricNode** signale un avertissement lorsque les certificats utilisés par le nœud sont bientôt expirer. Il existe trois certificats par nœud : **Certificate_cluster**, **Certificate_server**et **Certificate_default_client**. Lorsque l’expiration est d’au moins deux semaines, l’état de l’état est OK. Lorsque l’expiration est dans deux semaines, le type de rapport est un avertissement. Durée de vie de ces événements est infinie, et ils sont supprimées lorsqu’un nœud quitte le cluster.

- **SourceId**: System.FabricNode
- **Propriété**: commence par **certificat** et contient plus d’informations sur le type de certificat
- **Prochaines étapes**: mettre à jour les certificats s’ils sont près d’expiration.

### <a name="load-capacity-violation"></a>Violation de capacité de charge
L’équilibreur de charge Service Fabric signale un avertissement si elle détecte une violation de capacité du nœud.

 - **SourceId**: System.PLB
 - **Propriété**: commence par **capacité**
 - **Prochaines étapes**: vérifier fourni métriques et afficher la capacité en cours sur le nœud.

## <a name="application-system-health-reports"></a>Rapports d’intégrité système application
**System.CM**, qui représente le service de Cluster Manager, est l’autorité qui gère les informations relatives à une application.

### <a name="state"></a>État
System.CM signale comme OK lorsque l’application a été créée ou mis à jour. Il informe le magasin d’état de santé lorsque l’application a été supprimée, afin qu’il peut être supprimé à partir de la banque.

- **SourceId**: System.CM
- **Propriété**: état
- **Prochaines étapes**: si l’application a été créée, elle doit inclure l’état de santé du Gestionnaire du Cluster. Dans le cas contraire, vérifiez l’état de l’application par l’émission d’une requête (par exemple, l’applet de commande PowerShell * *Get-ServiceFabricApplication-ApplicationName *applicationName***).

L’exemple suivant montre l’événement d’état sur le **fabric : / WordCount** application :

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>Rapports d’intégrité de système de service
**System.FM**, qui représente le service de gestionnaire de basculement, est l’autorité qui gère les informations sur les services.

### <a name="state"></a>État
System.FM signale comme OK lorsque le service a été créé. Il supprime l’entité du magasin de santé lorsque le service a été supprimé.

- **SourceId**: System.FM
- **Propriété**: état

L’exemple suivant montre l’événement d’état sur le service **fabric : WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>Violation de réplicas non placées
**System.PLB** signale un avertissement s’il ne peut pas trouver un emplacement pour un ou plusieurs réplicas de service. Le rapport est supprimé lorsqu’il arrive à expiration.

- **SourceId**: System.FM
- **Propriété**: état
- **Prochaines étapes**: Vérifiez les contraintes du service et l’état actuel de l’emplacement.

L’exemple suivant montre une violation d’un service configuré avec 7 réplicas cible dans un cluster à 5 nœuds :

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


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
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
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
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>Rapports d’intégrité de système de partition
**System.FM**, qui représente le service de gestionnaire de basculement, est l’autorité qui gère les informations sur les partitions de service.

### <a name="state"></a>État
System.FM signale comme OK lorsque la partition a été créée et est en bon état. Il supprime l’entité du magasin de santé lors de la suppression de la partition.

Si la partition est inférieure le nombre minimal de réplica, il signale une erreur. Si la partition n’est pas sous le nombre minimal de réplica, mais qu’il est sous le nombre de réplicas de cible, il signale un avertissement. Si la partition est perte de quorum, System.FM signale une erreur.

Autres événements importants incluent un avertissement lors de la reconfiguration prend plus de temps que prévu, et lorsque la build prend plus de temps que prévu. Attendu heures pour la génération et la reconfiguration sont configurables en fonction de scénarios de service. Par exemple, si un service a un téraoctet de l’état, tel que SQL de base de données, la génération prend plue d’un service avec une petite quantité d’état.

- **SourceId**: System.FM
- **Propriété**: état
- **Prochaines étapes**: si l’état n’est pas OK, il est possible que certains réplicas pas créés, ouverts ou promus correctement principale ou secondaire. Dans de nombreux cas, la cause est un bogue de service dans l’implémentation d’ouvrir ou de modifier le rôle.

L’exemple suivant montre une partition en bon état :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

L’exemple suivant montre l’état de santé d’une partition qui est inférieure à count de réplica cible. L’étape suivante consiste à obtenir la description de la partition, qui montre comment elle est configurée : **MinReplicaSetSize** est 2 et **TargetReplicaSetSize** est sept. Puis obtenir le nombre de nœuds dans le cluster : cinq. Dans ce cas, deux réplicas ne peuvent pas être placés.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>Violation de contrainte de réplica
**System.PLB** signale un avertissement si elle détecte une violation de contrainte de réplica et que vous ne pouvez pas placer des réplicas de la partition.

- **SourceId**: System.PLB
- **Propriété**: commence par **ReplicaConstraintViolation**

## <a name="replica-system-health-reports"></a>Rapports d’intégrité de système de réplica
**System.RA**, qui représente le composant agent de reconfiguration, est l’autorité de l’état de réplica.

### <a name="state"></a>État
**System.RA** signale comme OK lorsque le réplica a été créé.

- **SourceId**: System.RA
- **Propriété**: état

L’exemple suivant montre un réplica en bon état :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>État ouvert du réplica
La description de ce rapport d’état contient l’heure de début (temps universel) lors de l’appel de l’API a été appelé.

**System.RA** signale un avertissement si le réplica ouvert est plus long que la période configurée (par défaut : 30 minutes). Si l’API a un impact sur la disponibilité du service, le rapport est émis beaucoup plus rapidement (un intervalle configurable, avec une valeur par défaut de 30 secondes). Le temps mesuré inclut la durée de l’open replicator et le service est ouvert. La propriété passe à OK si l’ouverture se termine.

- **SourceId**: System.RA
- **Propriété**: **ReplicaOpenStatus**
- **Prochaines étapes**: si l’état n’est pas OK, examinez pourquoi le réplica ouvert prend plus de temps que prévu.

### <a name="slow-service-api-call"></a>Appel de l’API service lente
Rapport de **System.RAP** et de **System.Replicator** un avertissement si un appel au code utilisateur service prend plus de temps que la durée configurée. L’avertissement est désactivée lorsque l’appel se termine.

- **SourceId**: System.RAP ou System.Replicator
- **Propriété**: le nom de l’API lente. La description fournit plus de détails sur l’heure de que l’API a été en attente.
- **Prochaines étapes**: recherchez pourquoi l’appel prend plus longtemps que prévu.

L’exemple suivant montre une partition et une perte de quorum, et les étapes de l’enquête effectuées pour savoir pourquoi. Un des réplicas a un état d’avertissement, et vous offre son état de santé. Il indique que l’opération de service prend plus de temps que prévu, un événement signalé par System.RAP. Après réception de cette information, l’étape suivante consiste à examiner le code de service et de vérifier il. Dans ce cas, l’implémentation de **RunAsync** du service avec état lève une exception non gérée. Les réplicas sont recyclage, afin que vous ne voyiez pas les réplicas dans l’état d’avertissement. Vous pouvez réessayer l’obtention de l’état de santé et voir les différences dans l’ID de réplica. Dans certains cas, les tentatives peuvent vous donner.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Lorsque vous démarrez l’application défectueuse sous le débogueur, les fenêtres des événements de diagnostic affiche l’exception levée à partir de RunAsync :

![Événements de diagnostic 2015 Visual Studio : Échec de RunAsync dans un tissu : / HelloWorldStatefulApplication.][1]

Événements de diagnostic 2015 Visual Studio : Échec de RunAsync dans **fabric : / HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>File d’attente de réplication complète
**System.Replicator** signale un avertissement si la file d’attente de réplication est plein. Sur le serveur primaire, cela se produit généralement car un ou plusieurs réplicas secondaires sont lents accuser réception des opérations. Sur le serveur secondaire, cela se produit généralement lorsque le service est lent à appliquer les opérations. L’avertissement est désactivée lorsque la file d’attente ne soit plus plein.

- **SourceId**: System.Replicator
- **Propriété**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, selon le rôle de réplica

### <a name="slow-naming-operations"></a>Ralentir les opérations d’attribution de noms

Santé de rapports **System.NamingService** sur son réplica principal lors d’une opération d’attribution de noms est plus longue qu’acceptable. Exemples d’opérations de nommage sont [CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) ou [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx). Plusieurs méthodes sont accessibles sous FabricClient, par exemple dans [les méthodes de gestion de service](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx) ou des [méthodes de gestion de propriété](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx).

> [AZURE.NOTE] Le service d’attribution de noms résout les noms de service vers un emplacement dans le cluster et permet aux utilisateurs de gérer des propriétés et des noms de service. Il s’agit d’un tissu de Service partitionnée persistant de service. Une des partitions représente le propriétaire de l’autorité, qui contient des métadonnées relatives à tous les services et les noms de Service Fabric. Les noms de Service Fabric sont mappés à des partitions différentes, appelées partitions du nom du propriétaire, le service est extensible. Apprenez-en plus sur le [service de noms](service-fabric-architecture.md).

Lorsqu’une opération d’attribution de noms prend plus longtemps que prévu, l’opération est marquée avec un état d’avertissement sur le *réplica principal de la partition de service d’attribution de noms qui fait Office de l’opération*. Si l’opération se termine avec succès, l’avertissement est désactivée. Si l’opération se termine avec une erreur, l’état de santé inclut plus d’informations sur l’erreur.

- **SourceId**: System.NamingService
- **Propriété**: commence par préfixe **Duration_** et identifie l’opération lente et le nom de Service Fabric sur lequel l’opération est appliquée. Par exemple, si créer service au fabric de nom : / MyApp/MyService prend trop de temps, la propriété est Duration_AOCreateService.fabric:/MyApp/MyService. AO pointe vers le rôle de la partition d’attribution de noms pour ce nom et cette opération.
- **Prochaines étapes**: vérification pourquoi l’opération d’attribution de noms échoue. Chaque opération peut avoir des causes initiales différentes. Par exemple, supprimer service peut-être être bloqué sur un nœud, car l’hôte de l’application conserve son blocage sur un nœud en raison d’un bogue de l’utilisateur dans le code de service.

L’exemple suivant illustre une opération de création de service. L’opération a duré plus longtemps que la durée configurée. AO de tentatives et envoie le travail non. NON terminé la dernière opération avec délai d’expiration. Dans ce cas, le même réplica est principal pour les AO et aucun rôle.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Rapports d’intégrité de système de DeployedApplication
**System.Hosting** est l’autorité sur les entités déployées.

### <a name="activation"></a>Activation
System.Hosting signale comme OK lorsqu’une application a été activée avec succès sur le nœud. Dans le cas contraire, il signale une erreur.

- **SourceId**: System.Hosting
- **Propriété**: l’Activation, y compris la version de déploiement
- **Prochaines étapes**: si l’application ne fonctionne pas correctement, recherchez pourquoi l’activation a échoué.

L’exemple suivant illustre l’activation réussie :

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Télécharger
**System.Hosting** signale une erreur si le téléchargement du package application échoue.

- **SourceId**: System.Hosting
- **Propriété**: * *télécharger :*RolloutVersion***
- **Prochaines étapes**: recherchez pourquoi le téléchargement a échoué sur le nœud.

## <a name="deployedservicepackage-system-health-reports"></a>Rapports d’intégrité de système de DeployedServicePackage
**System.Hosting** est l’autorité sur les entités déployées.

### <a name="service-package-activation"></a>Activation du service de package
System.Hosting signale que OK si l’activation du service de package sur le nœud a réussi. Dans le cas contraire, il signale une erreur.

- **SourceId**: System.Hosting
- **Propriété**: Activation
- **Prochaines étapes**: recherchez pourquoi l’activation a échoué.

### <a name="code-package-activation"></a>Activation du code lot
**System.Hosting** signale comme OK pour chaque package de code si l’activation a réussi. Si l’activation échoue, il signale un avertissement tel que configuré. Si **CodePackage** ne parvient pas à activer ou se termine avec une erreur supérieure à la configuration de **CodePackageHealthErrorThreshold**, hébergement signale une erreur. Si un package de service contient plusieurs packages de code, un rapport de l’activation est généré pour chacune d’elles.

- **SourceId**: System.Hosting
- **Propriété**: utilise le préfixe **CodePackageActivation** et contient le nom du package code et le point d’entrée en tant que * *CodePackageActivation :*CodePackageName*:*SetupEntryPoint/point d’entrée* ** (par exemple, **CodePackageActivation:Code:SetupEntryPoint**)

### <a name="service-type-registration"></a>Inscription du type de service
**System.Hosting** signale que OK si le type de service a été correctement enregistré. Il signale une erreur si l’inscription n’a pas été effectuée dans le temps (telle que définie à l’aide de **ServiceTypeRegistrationTimeout**). Si le type de service est enregistré à partir du nœud, il s’agit, car le temps d’exécution a été fermé. Hébergement signale un avertissement.

- **SourceId**: System.Hosting
- **Propriété**: utilise le préfixe **ServiceTypeRegistration** et contient le nom de type de service (par exemple, **ServiceTypeRegistration:FileStoreServiceType**)

L’exemple suivant montre un lot sain service déployé :

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Télécharger
**System.Hosting** signale une erreur si le téléchargement du package échoue.

- **SourceId**: System.Hosting
- **Propriété**: * *télécharger :*RolloutVersion***
- **Prochaines étapes**: recherchez pourquoi le téléchargement a échoué sur le nœud.

### <a name="upgrade-validation"></a>Mise à niveau de validation
**System.Hosting** signale une erreur si la validation au cours de la mise à niveau échoue ou si la mise à niveau échoue sur le nœud.

- **SourceId**: System.Hosting
- **Propriété**: utilise le préfixe **FabricUpgradeValidation** et contient la version de mise à niveau
- **Description**: pointe sur l’erreur s’est produite

## <a name="next-steps"></a>Étapes suivantes
[Afficher les rapports health Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Comment signaler et vérifier l’intégrité du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Surveiller et diagnostiquer des services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Mise à niveau des applications de service Fabric](service-fabric-application-upgrade.md)
