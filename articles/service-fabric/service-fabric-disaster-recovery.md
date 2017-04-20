<properties
   pageTitle="Reprise d’Azure Fabric du Service | Microsoft Azure"
   description="Azure Fabric de Service offre les fonctions nécessaires pour faire face à tous les types d’incidents. Cet article décrit les types de catastrophes qui peuvent se produire et comment y faire face."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="seanmck"/>

# <a name="disaster-recovery-in-azure-service-fabric"></a>Reprise après sinistre dans Azure Fabric de Service

Un élément essentiel de fournir une application de nuage à disponibilité élevée consiste à s’assurer qu’il peut survivre à tous les différents types de défaillances, y compris ceux qui sont en dehors de votre contrôle. Cet article décrit la disposition physique d’un cluster d’Azure Fabric de Service dans le contexte d’incidents potentiels et fournit des conseils sur la façon de traiter avec telles catastrophes de limiter ou d’éliminer le risque de perte de données ou les interruptions de service.

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Agencement de Fabric du Service de clusters dans Azure

Pour comprendre le risque posé par les différents types de défaillances, il est utile de savoir comment les clusters sont physiquement disposés dans Azure.

Lorsque vous créez un cluster Service Fabric dans Azure, vous devez choisir une région où il sera hébergé. L’infrastructure Azure configure ensuite les ressources de cluster dans la région, notamment le nombre de machines virtuelles (VM) que demandé. Examinons plus en détail comment et où les ordinateurs virtuels sont en service.

### <a name="fault-domains"></a>Domaines d’erreur

Par défaut, les ordinateurs virtuels du cluster sont répartis dans des groupes logiques appelés domaines d’erreur (FDs), dont les ordinateurs basés sur des défaillances potentielles dans le matériel de l’hôte de segment. En particulier, si deux VM réside dans deux FDs distincts, vous pouvez être sûr qu’ils ne partagent pas le même commutateur réseau ou de la source d’alimentation. Par conséquent, un réseau local ou une panne de courant affectant un ordinateur virtuel n’affecte pas l’autre, ce qui permet de rééquilibrer la charge de travail de l’ordinateur ne répond pas au sein du cluster Service Fabric.

Vous pouvez visualiser la mise en page de votre cluster entre les domaines d’erreur à l’aide de la carte de cluster fournie dans [l’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md):

![Nœuds répartis sur plusieurs domaines de panne dans l’Explorateur de tissu de Service][sfx-cluster-map]

>[AZURE.NOTE] L’autre axe dans la carte de cluster affiche les domaines de mise à niveau, lequel regroupent des nœuds en fonction des activités de maintenance planifiée. Clusters de service Fabric dans Azure sont toujours disposés sur cinq domaines de mise à niveau.

### <a name="geographic-distribution"></a>Répartition géographique

Il y a actuellement [26 régions Azure partout dans le monde][azure-regions], avec plusieurs plus annoncé. Une région peut contenir un ou plusieurs centres de données physiques en fonction de la demande et de la disponibilité des endroits appropriés, parmi d’autres facteurs. Notez, cependant, que même dans les régions qui contiennent plusieurs centres de données physiques, aucune garantie n’est que VM de votre cluster est réparties uniformément sur les emplacements physiques. Actuellement, en effet, tous les ordinateurs virtuels d’un cluster donné sont provisionnés dans un site physique unique.

## <a name="dealing-with-failures"></a>Traitement des défaillances

Il existe plusieurs types de défaillances qui peuvent avoir un impact sur votre cluster, chacun avec son propre atténuation. Nous allons examiner les par ordre de probabilité de se produire.

### <a name="individual-machine-failures"></a>Échecs d’ordinateur individuel

Comme mentionné, échecs liés aux ordinateurs individuels, au sein de la machine virtuelle ou dans le matériel ou les logiciels qui l’héberge au sein d’un domaine d’erreur, ne poser aucun risque sur leurs propres. TISSU de service sera généralement détecte la défaillance quelques secondes et répondre en fonction de l’état du cluster. Par exemple, si le nœud a été hébergeant les réplicas d’une partition principales, un nouveau serveur principal est choisi à partir de duplications secondaire de la partition. Lorsque Azure fait passer l’ordinateur défaillant sauvegarder, il va rejoindre le cluster automatiquement et prendre une fois de plus sur sa part de la charge de travail.

### <a name="multiple-concurrent-machine-failures"></a>Plusieurs pannes simultanées de machine

Alors que les domaines d’erreur réduisant de manière significative les risques d’échec de machine simultanées, il est toujours possible pour plusieurs échecs aléatoires mettre simultanément plusieurs ordinateurs dans un cluster.

En général, dans la mesure où la majorité des nœuds restent disponibles, le cluster continue à fonctionner, même s’il est au moins de capacité que les réplicas avec état obtenir conditionnées dans un plus petit ensemble d’ordinateurs et de moins d’instances sans état sont disponibles afin de répartir la charge.

#### <a name="quorum-loss"></a>Perte de quorum

Si une majorité des réplicas pour la partition de l’état d’un service aller vers le bas, cette partition passe à l’état appelé « perte de quorum ». À ce stade, Service Fabric cesse de permettant l’écriture sur cette partition pour s’assurer que son état reste cohérente et fiable. En effet, nous choisissons d’accepter une période d’indisponibilité pour s’assurer que les clients ne sont pas dit que leurs données ont été enregistrées lorsque qu’il n’était pas. Notez que si vous avez choisi dans autorisent les lectures à partir de duplications secondaires de ce service avec état, vous pouvez continuer à effectuer des opérations de lecture tandis que, dans cet état. Une partition reste perte de quorum, jusqu'à ce qu’un nombre suffisant de réplicas revenir ou jusqu'à ce que l’administrateur de cluster force le système à passer à l’aide de l' [API de réparation-ServiceFabricPartition][repair-partition-ps].

>[AZURE.WARNING] Exécution d’une action de réparation alors que le réplica principal est hors service entraîne la perte de données.

Services système peuvent également subir quorum perte, à l’impact qui est spécifique au service en question. Par exemple, perte de quorum dans le service d’attribution de noms aura un impact sur la résolution de noms, alors que la perte de quorum dans le service de gestionnaire de basculement bloquera les basculements et création d’un nouveau service. Notez que contrairement à pour leurs propres services, tentative de réparation du système n’est *pas* recommandée. Au lieu de cela, il est préférable d’attendre que les réplicas bas de retour.

#### <a name="minimizing-the-risk-of-quorum-loss"></a>En réduisant le risque de perte de quorum

Vous pouvez réduire le risque de perte de quorum en augmentant la taille du jeu de réplica cible pour votre service. Il est utile de considérer le nombre de réplicas vous avez besoin en termes de nombre de noeuds indisponibles, que vous pouvez tolérer en une fois, tout en restant disponible pour les écritures, en gardant à l’esprit cette application ou mises à niveau de cluster peuvent rendre les nœuds temporairement indisponible, en plus des défaillances matérielles.

Prenons les exemples suivants, en supposant que vous avez configuré vos services pour un MinReplicaSetSize de trois, le plus petit nombre recommandé pour les services de production. Avec un TargetReplicaSetSize de trois (un serveur principal et deux secondaires), une défaillance matérielle au cours d’une mise à niveau (deux réplicas vers le bas) va entraîner la perte de quorum et de votre service devient en lecture seule. Ou bien, si vous avez cinq réplicas, être capable de résister à deux défaillances pendant la mise à niveau (trois réplicas vers le bas) comme les deux réplicas restants peuvent se former encore un quorum dans le jeu de réplicas minimale.

### <a name="data-center-outages-or-destruction"></a>Interruptions de centre de données ou la destruction

Dans de rares cas, les centres de données physiques peuvent devenir temporairement indisponibles en raison de la perte de connectivité réseau ou d’alimentation. Dans ces cas, vos clusters de tissu de Service et les applications de la même façon ne seront pas disponibles, mais vos données seront conservées. Pour les clusters en cours d’exécution dans Azure, vous pouvez afficher les mises à jour sur les interruptions de service sur la [page d’état Azure][azure-status-dashboard].

Dans l’événement hautement improbable qu’un centre de données physique est détruit, les clusters de Fabric de Service hébergés il sera perdues, ainsi que leur état.

Pour protéger contre cette éventualité, il est extrêmement important de régulièrement [votre état de sauvegarde](service-fabric-reliable-services-backup-restore.md) dans un magasin de géo-redondants et vous assurer que vous avez validé la possibilité de la restaurer. La fréquence à laquelle vous effectuez une sauvegarde sera fonction de votre objectif de point de récupération (RPO). Même si vous n’avez pas totalement implémenté de sauvegarde et restauration encore, vous devez implémenter un gestionnaire pour le `OnDataLoss` événement afin que vous pouvez vous connecter lorsqu’il se produit comme suit :

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>Défaillances logicielles et autres sources de perte de données

Comme une cause de la perte de données, les erreurs de code dans les services, les erreurs humaines opérationnelles et failles de sécurité sont plus courantes que des échecs de centre de données étendu. Toutefois, dans tous les cas, la stratégie de récupération est le même : effectuer des sauvegardes régulières de tous les services avec état et l’exercice de votre capacité à restaurer cet état.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment simuler différentes défaillances à l’aide de l' [infrastructure de testabilité](service-fabric-testability-overview.md)
- Lire les autres ressources de haute disponibilité et de reprise après sinistre. Microsoft a publié une grande quantité de conseils sur ces sujets. Alors que certains de ces documents font référence à des techniques spécifiques pour une utilisation dans d’autres produits, qu’ils contiennent de nombreuses méthodes conseillées générales que vous pouvez appliquer dans le contexte de Service Fabric ainsi :
 - [Liste de vérification de disponibilité](../best-practices-availability-checklist.md)
 - [Exécution d’une extraction de récupération après sinistre](../sql-database/sql-database-disaster-recovery-drills.md)
 - [Reprise après sinistre et une haute disponibilité pour les applications Azure][dr-ha-guide]


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
