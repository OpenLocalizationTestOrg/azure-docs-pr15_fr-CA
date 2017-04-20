<properties
   pageTitle="La limitation dans le Gestionnaire de ressources de cluster Service Fabric | Microsoft Azure"
   description="Apprenez à configurer des limitations fournies par le Gestionnaire de ressources Cluster Service Fabric."
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


# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>Le comportement du Gestionnaire de ressources Cluster Service Fabric de limitation
Même si vous avez correctement configuré le Gestionnaire de ressources de Cluster, le cluster peut obtenir interrompu. Il peut par exemple être nœud ou erreur de domaine défaillances simultanées - ce qui se passerait si qui s’est produite au cours d’une mise à niveau ? Le Gestionnaire des ressources va essayer de son mieux pour corriger tout, mais comme suit les temps vous souhaiterez une « barrière » pour que le cluster lui-même a une chance de se stabiliser (les nœuds qui existeront faire revenir, les conditions réseau réparer eux-mêmes, déploiement des bits de correction). Pour résoudre ce genre de situations, la ressource de Cluster Service Fabric Manager inclut plusieurs limitations. Notez que ces limitations sont relativement sans interruption de service et généralement ne doit pas être utilisées sauf s’il y a eu quelques prudent math fait autour de la quantité de travail parallèle qui peut réellement être effectuée dans le cluster, ainsi qu’une fréquence doivent répondre à ces types de (est) les événements non planifiés reconfiguration macroscopiques (UE : « Très mauvais jours »).

En règle générale, nous vous recommandons en évitant de très mauvais jours grâce à d’autres options (telles que les mises à jour de code normal afin d’éviter les surplanification du cluster dans un premier temps) au lieu de la limitation de votre cluster pour empêcher l’utilisation de ressources lorsqu’il tente de résoudre lui-même). Limitations ont des valeurs par défaut que nous avons trouvé par l’expérience pour être OK de valeurs par défaut, mais vous devez sans doute jeter un coup de œil et les adapter à votre charge réelle attendue. Bien que pas trop contraignant ou chargement, que le cluster est une meilleure pratique que vous pouvez déterminer qu’il existe des cas qui (jusqu'à ce que vous pouvez résoudre les) où vous devez faire quelques limitations en place, même si cela signifie que le cluster prendra plus de temps à stabiliser.

##<a name="configuring-the-throttles"></a>Configuration des limitations
Limitations qui sont incluses par défaut sont les suivants :

-   GlobalMovementThrottleThreshold – contrôle le nombre total de mouvements du cluster sur le temps (définie comme la GlobalMovementThrottleCountingInterval, la valeur en secondes)
-   MovementPerPartitionThrottleThreshold – contrôle le nombre total de mouvements pour n’importe quelle partition de service sur un certain temps (la MovementPerPartitionThrottleCountingInterval, la valeur en secondes)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Sachez que la plupart du temps, que nous avons vu des clients utilisent ces limitations, qu'il a été parce qu’ils étaient déjà un environnement de ressources limitées (telles que la bande passante limitée du réseau dans les nœuds individuels ou des disques qui n’étaient pas à la configuration requise du réplica parallèle les builds qui ont été mis sur les) qui signifie que ces opérations ne réussisse ou peut être lente quand même.  Dans ces situations clients étaient à l’aise pour savoir qui ils étaient afin d’augmenter la quantité de temps que prendrait le cluster pour atteindre un état stable, notamment savoir qui ils pourraient finissent de s’exécuter au moindre fiabilité pendant qu’ils ont été accélérés.

## <a name="next-steps"></a>Étapes suivantes
- Pour savoir plus sur comment le Gestionnaire de ressources de Cluster gère et équilibre la charge du cluster, consultez l’article sur [l’équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
- Le Gestionnaire de ressources de Cluster comporte un grand nombre d’options pour la description du cluster. Pour en savoir plus sur les consultez cet article sur la [Description d’un tissu de Service de cluster](service-fabric-cluster-resource-manager-cluster-description.md)
