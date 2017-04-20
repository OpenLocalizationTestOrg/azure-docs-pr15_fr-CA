<properties
   pageTitle="Défragmentation des métriques de Service Azure Fabric | Microsoft Azure"
   description="Une vue d’ensemble de l’utilisation de défragmentation ou en tant que stratégie de mesures dans le Fabric du Service de livraison"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Défragmentation de mesures et de charge dans un Service de Fabric
Le Gestionnaire de ressources de Cluster Service Fabric est principalement concerné avec l’équilibrage en termes de répartition de la charge : s’assurer que tous les nœuds du cluster sont également utilisées. Il s’agit généralement de la mise en page plus sûre et smartest en termes de survie des échecs dans la mesure où elle permet de s’assurer que tout échec donné ne prend pas le pourcentage de certains volumineux d’une charge de travail donnée. La ressource de Cluster Service Fabric Manager prend en charge une autre stratégie, qui est de la défragmentation. Défragmentation signifie en général qu’au lieu de tenter de distribuer l’utilisation d’une mesure au sein du cluster, nous devrions essayer de consolidation. Il s’agit d’une inversion de chance de notre stratégie normal – au lieu de l’optimisation du cluster pour objectif de réduire l’écart moyen de charge métrique pour une mesure donnée, nous allons commencer l’optimisation pour les augmentations de déviation. Mais pourquoi devrais-je cette stratégie ?

De même, si vous avez répartir la charge équitablement entre les noeuds dans le cluster vous avez consommés une partie des ressources que les nœuds ont à offrir. Normalement ce n’est pas un problème, mais parfois de créer des services qui sont particulièrement importantes et consomment la majeure partie d’un nœud par certaines charges de travail – par exemple 75 % à 95 % des ressources d’un nœud finiriez dédié à une instance de service unique ou d’un réplica. Ce n’est pas un problème, le Gestionnaire de ressources de Cluster détectera à l’heure de création de service dont il a besoin de réorganiser le cluster afin de faire de la place pour cette charge de travail importante et la définir sur la concrétisation, mais en attendant cette charge de travail est en attente d’être planifiés dans le cluster.

Étant donné que la planification de nouvelles charges de travail est généralement au moins un petit temps de latence sensibles, si nous ne rien faire différemment nous peut parfois droite de soufflage par ces SLA s’il existe un grand nombre de services et d’état pour vous déplacer, en particulier si les charges de travail dans le cluster sont généralement volumineux (et par conséquent prend plus de temps pour vous déplacer dans le cluster). En effet, lorsque nous avons mesuré les heures de création de simulations en fonction des données de cluster réels, nous avons vu que si les services ont été suffisamment grands et le cluster a été utilisé plutôt que la création de ces services grands serait ralentie et que nous pouvons en améliorer ce par l’introduction de la stratégie de métriques de défragmentation.

Comme fichier Création ou accès pourrait obtenir ralentie que si un disque dur a été fragmenté et peut être accéléré grâce à la défragmentation du lecteur afin qu’il existait des blocs contigus plus importants, vous pouvez configurer les mesures de défragmentation pour que le Gestionnaire de ressources de Cluster pour essayer de manière proactive comprimer la charge des services en moins de nœuds afin qu’il est (presque) toujours possible pour les services plus importantes , leur permettant de créer rapidement. La plupart des gens n’est pas nécessaire, car les services doivent généralement être petits et, par conséquent, il n’est pas difficile de trouver la place pour les, mais si vous avez des services de grande taille et que vous avez besoin de créer rapidement (et sont prêts à accepter les autres compromis comme impactfulness accrue d’échecs et certaines ressources étant laissé inutilisé d’attente pour les charges de travail planifié) puis la stratégie de défragmentation est pour vous.

Le diagramme ci-dessous donne une représentation visuelle de deux clusters différents, une défragmentation et un qui n’est pas. Dans le cas de tableaux, prendre en compte les mouvements qui seraient nécessaires pour placer un des objets de service plus grandes, si un nouveau ont été créées, par rapport au cluster défragmenté, où il peut être placé immédiatement sur les nœuds 4 ou 5.

![Comparaison équilibrée et défragmentation des Clusters][Image1]

## <a name="defragmentation-pros-and-cons"></a>Avantages et inconvénients défragmentation
Quelles sont donc ces autres compromis de concepts ? Nous vous recommandons de mesure approfondie de vos charges de travail avant de l’activer sur les mesures de la défragmentation. Voici un tableau rapide des éléments à considérer :

| Professionnels de la défragmentation  | Inconvénients de la défragmentation |
|----------------------|----------------------|
|Permet une création plus rapide de grands services | Chargement les concentrés sur moins de nœuds, augmentation des conflits
|Permet de réduire le déplacement des données lors de la création    | Défaillances peuvent avoir un impact sur plusieurs services et provoquer des évolution du plus
|Permet la description complète des exigences et récupération d’espace | Configuration de la gestion des ressources globale plus complexe

Vous pouvez combiner des métriques normales et défragmentée dans un même cluster, et le Gestionnaire de ressources fera de son mieux pour garantir que vous obtiendrez une disposition qui regroupe autant de mesures de la défragmentation, comme il peut tout en essayant de répartir le reste. Vous obtenez exactement les résultats dépend du nombre de mesures par rapport au nombre de métriques de défragmentation et leur poids, la charge actuelle, etc. de l’équilibrage de la.

## <a name="configuring-defragmentation-metrics"></a>Configuration des métriques de défragmentation
Configuration des métriques de défragmentation est une décision globale dans le cluster, et des mesures individuelles peuvent être sélectionnées pour la défragmentation :

ClusterManifest.xml :

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## <a name="next-steps"></a>Étapes suivantes
- Le Gestionnaire de ressources de Cluster comporte un grand nombre d’options pour la description du cluster. Pour en savoir plus sur les consultez cet article sur la [Description d’un tissu de Service de cluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Métriques sont la façon dont le Gestionnaire de ressources de Cluster Service de Fabric gère la consommation et la capacité du cluster. Pour en savoir plus sur comment les configurer et de les extraire [cet article](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
