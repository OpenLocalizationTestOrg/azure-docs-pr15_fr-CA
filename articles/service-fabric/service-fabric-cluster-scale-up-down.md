<properties
   pageTitle="Faire évoluer un cluster Service Fabric ou arrière | Microsoft Azure"
   description="Mettre à l’échelle un cluster Service Fabric ou à répondre à la demande en définissant des règles à l’échelle automatique pour chaque ensemble d’échelle de type/VM nœud. Ajouter ou supprimer des nœuds à un cluster de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Faire évoluer un cluster Service Fabric dans ou à l’aide de règles de l’échelle automatique

Jeux d’échelle de machine virtuelle sont une ressource de calcul Azure que vous pouvez utiliser pour déployer et gérer une collection d’ordinateurs virtuels sous la forme d’un ensemble. Chaque type de nœud qui est défini dans un cluster de Service Fabric est configuré comme un ensemble d’échelle VM distinct. Chaque type de nœud peut ensuite être mis à l’échelle dans ou out indépendamment, ont différents ensembles de ports ouverts et peut comporter des mesures de capacité différente. Pour en savoir plus à ce sujet dans le document [Service Fabric nodetypes](service-fabric-cluster-nodetypes.md) . Dans la mesure où le Fabric du Service des types de nœuds de votre cluster sont constitués d’échelle de la machine virtuelle définit au niveau du serveur principal, vous devez définir des règles à l’échelle automatique pour chaque ensemble d’échelle de type/VM nœud.

>[AZURE.NOTE] Votre abonnement doit être suffisamment cœurs pour ajouter de nouveaux ordinateurs virtuels qui composent ce cluster. Il n’existe actuellement, aucune validation de modèle afin que vous obtenez une défaillance de temps de déploiement, si les limites de quota sont atteints.

## <a name="choose-the-node-typevm-scale-set-to-scale"></a>Cliquez sur l’échelle de type/machine virtuelle de nœud défini à l’échelle

Actuellement, vous n’êtes pas en mesure de spécifier les règles à l’échelle automatique pour les jeux d’échelle de machine virtuelle via le portail, donc nous permet d’Azure, PowerShell (1.0 +) liste les types de nœuds et leur ajouter des règles de l’échelle automatique.

Pour obtenir la liste des VM échelle qui composent votre cluster, exécuter les applets de commande suivantes :

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevm-scale-set"></a>Définir des règles à l’échelle automatique pour l’ensemble des nœuds/VM de type échelle

Si votre cluster a plusieurs types de nœud, puis répétez pour chaque échelle de types/machine virtuelle de nœud définit que vous souhaitez mettre à l’échelle (ou). Prendre en compte le nombre de nœuds que vous devez disposer avant de configurer la mise à l’échelle automatique. Le nombre de nœuds que vous devez posséder pour le type de nœud principal est piloté par le niveau de fiabilité que vous avez choisi. Apprenez-en plus sur les [niveaux de fiabilité](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  Mise à l’échelle vers le bas du nœud principal, tapez inférieurs à la marque numérique minimale du cluster instable ou détruire. Cela peut entraîner la perte de données pour vos applications et les services système.

La fonctionnalité à l’échelle automatique n’est actuellement pas pilotée par les charges que vos applications peuvent signaler au Service de Fabric. Ainsi, pour l’instant à l’échelle automatique vous obtenez est purement piloté par les performances compteurs qui sont émis par chacun de la machine virtuelle mise à l’échelle instances du jeu.  

Suivez ces instructions [pour définir l’échelle automatique pour chaque ensemble d’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] Dans une échelle vers le bas du scénario, à moins que votre type de nœud a un niveau de durabilité de l’or ou en argent vous devez appeler l' [applet de commande Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) avec le nom de nœud approprié.

## <a name="manually-add-vms-to-a-node-typevm-scale-set"></a>Ajouter manuellement des ordinateurs virtuels d’un nœud type/VM échelle

Suivez les instructions/exemple dans la [Galerie de modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) pour modifier le nombre d’ordinateurs virtuels dans chaque Nodetype. 

>[AZURE.NOTE] Ajout d’ordinateurs virtuels prend du temps, donc n’attendent ne pas les ajouts à être instantanée. Donc envisagez d’ajouter de la capacité et dans le temps, permettant à plus de 10 minutes avant de la capacité de la machine virtuelle n’est disponible que pour les réplicas / à placer des instances de service.

## <a name="manually-remove-vms-from-the-primary-node-typevm-scale-set"></a>Supprimer manuellement les ordinateurs virtuels de l’ensemble d’échelle de type/VM nœud principal

>[AZURE.NOTE] Les services de système de tissu de service s’exécutent dans le type de nœud principal de votre cluster. Afin de ne doit jamais arrêter ou réduire le nombre d’instances de ces types de nœuds inférieure à ce que le niveau de fiabilité le justifie. Consultez [les détails sur les niveaux de fiabilité ici](service-fabric-cluster-capacity.md). 

Vous devez exécuter des étapes une machine virtuelle dans l’exemple suivant à la fois. Ainsi, pour les services système (et de vos services avec état) arrêter normalement sur l’instance VM à supprimer et les nouveaux réplicas créés sur d’autres nœuds.

1. Exécutez [ServiceFabricNode-désactiver](https://msdn.microsoft.com/library/mt125852.aspx) avec intention de 'RemoveNode' désactiver le nœud que vous souhaitez supprimer (la plus haute instance dans ce type de nœud).

2. Exécutez [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) pour vous assurer que le nœud a été déplacé en effet sur désactivé. Si ce n’est pas le cas, patientez jusqu'à ce que le nœud est désactivé. Vous ne pouvez pas Dépêchez-vous cette étape.

2. Suivez les instructions/exemple dans la [Galerie de modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) pour modifier le nombre de machines virtuelles par une dans ce Nodetype. L’instance supprimée est l’instance de machine virtuelle plus élevé. 

3. Répétez les étapes 1 à 3, le cas échéant, mais ne pourrait jamais évoluer verticalement, le nombre d’instances dans les types de nœud principal inférieur à ce qui garantit le niveau de fiabilité. Consultez [les détails sur les niveaux de fiabilité ici](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevm-scale-set"></a>Supprimer manuellement des ordinateurs virtuels à partir de l’ensemble principal/VM de type échelle

>[AZURE.NOTE] Pour un service dynamique, vous avez besoin d’un certain nombre de nœuds pour être toujours à maintenir la disponibilité et la conservation de l’état de votre service. Au minimum, vous devez le nombre de nœuds égale au nombre d’ensemble cibles réplica de la partition de service. 

Vous devez l’exécuter suit une machine virtuelle dans l’exemple suivant à la fois. Ainsi, pour les services système (et de vos services avec état) arrêter normalement sur l’instance de machine virtuelle que vous supprimez et nouveaux réplicas créés autre emplacement.

1. Exécutez [ServiceFabricNode-désactiver](https://msdn.microsoft.com/library/mt125852.aspx) avec intention de 'RemoveNode' désactiver le nœud que vous souhaitez supprimer (la plus haute instance dans ce type de nœud).

2. Exécutez [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) pour vous assurer que le nœud a été déplacé en effet sur désactivé. Si ce n’est pas le cas, attendez que le nœud est désactivé. Vous ne pouvez pas Dépêchez-vous cette étape.

2. Suivez les instructions/exemple dans la [Galerie de modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) pour modifier le nombre de machines virtuelles par une dans ce Nodetype. Ceci va supprimer l’instance de machine virtuelle plus élevé. 

3. Répétez les étapes 1 à 3, le cas échéant, mais ne pourrait jamais évoluer verticalement, le nombre d’instances dans les types de nœud principal inférieur à ce qui garantit le niveau de fiabilité. Consultez [les détails sur les niveaux de fiabilité ici](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Les comportements que vous pouvez observer dans l’Explorateur de tissu de Service

Lorsque vous mettre à l’échelle d’un cluster de l’Explorateur de tissu de Service reflète le nombre de nœuds (échelle de machine virtuelle définie instances) qui font partie du cluster.  Toutefois, lorsque vous redimensionnez un cluster, vous verrez l’instance supprimée nœud/VM affichée dans un état défectueux, sauf si vous appelez [cmd de supprimer-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx) avec le nom de nœud approprié.   

Voici l’explication de ce comportement.

Les nœuds répertoriés dans l’Explorateur de tissu de Service sont une réflexion de quels services système Service Fabric (FM spécifiquement) connaît le nombre de nœuds, le cluster a/a. Lorsque vous redimensionnez l’échelle de la machine virtuelle vers le bas, la machine virtuelle a été supprimée mais service de FM système considère toujours que que le nœud (ce qui a été mappé à la machine virtuelle qui a été supprimée) reviendra. Donc Service Fabric Explorer continue à afficher ce nœud (même si l’état de fonctionnement peut être erreur ou unknown).

Pour vous assurer qu’un nœud est supprimé lors de la suppression d’un ordinateur virtuel, vous avez deux options :

1) Choisissez un niveau de durabilité de l’or ou en argent (disponible prochainement) pour les types de nœuds de votre cluster, qui permet l’intégration de l’infrastructure. Qui puis supprimera automatiquement les nœuds de notre état services (FM) du système lorsque vous mettre à l’échelle vers le bas.
Consultez [les détails sur les niveaux de durabilité ici](service-fabric-cluster-capacity.md)

2) Une fois que l’instance de VM a été mise à l’échelle vers le bas, vous devez appeler l' [applet de commande Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] Clusters de service Fabric nécessitent un certain nombre de nœuds pour être opérationnel à tout moment afin de maintenir la disponibilité et la conservation de l’état - dénommé « maintenance de quorum ». Ainsi, il est généralement dangereux d’arrêter tous les ordinateurs du cluster, sauf si vous avez effectué en premier lieu une [sauvegarde complète de votre état](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Étapes suivantes
Lisez également obtenir des informations sur la planification de la capacité du cluster, mise à niveau d’un cluster et partitionnement des services suivants :

- [Planification de la capacité de votre cluster](service-fabric-cluster-capacity.md)
- [Mises à niveau de cluster](service-fabric-cluster-upgrade.md)
- [Services d’une partition pour l’échelle maximale](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
