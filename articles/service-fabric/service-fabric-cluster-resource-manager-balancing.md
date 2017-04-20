<properties
   pageTitle="L’équilibrage de votre Cluster avec Azure Service Fabric Cluster Resource Manager | Microsoft Azure"
   description="Introduction à l’équilibrage de votre cluster avec le Gestionnaire de ressources Cluster Service Fabric."
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

# <a name="balancing-your-service-fabric-cluster"></a>L’équilibrage de votre service de cluster de tissu
Le Gestionnaire de ressources de Cluster Service Fabric permet de reporting dynamique de la charge, réagir aux changements dans le cluster, corriger les violations de contrainte et rééquilibrer le cluster si nécessaire. La fréquence à laquelle il ne fait ces choses mais il provoque ? Il existe plusieurs contrôles liés à ce.

Le premier ensemble de contrôles autour d’équilibrage sont un ensemble de compteurs. Ces minuteries déterminent la fréquence à laquelle le Gestionnaire de ressources de Cluster examine l’état du cluster pour les opérations qui doivent être résolus. Il existe trois différentes catégories de travail, chacun avec leur propre horloge correspondant. Ils sont les suivantes :

1.  Positionnement – cette étape traite placez les réplicas avec état ou les instances sans état qui manquent. Ceci couvre les nouveaux services et la gestion des réplicas avec état ou sans état instances qui ont échoué et ont besoin d’être recréée. La suppression et le déplacement des duplications ou instances sont également géré ici.
2.  Contrainte de contrôle cette étape vérifie et corrige les violations des contraintes de placement différent (règles) au sein du système. Exemples de règles sont des choses telles que s’assurer que les nœuds ne sont pas surchargés et que les contraintes de placement d’un service (plus d’informations sur les versions ultérieures) sont respectées.
3.  Équilibrage – cette étape vérifie pour voir si le rééquilibrage proactive est nécessaire en fonction du niveau désiré configuré du solde pour les différentes mesures et, le cas échéant tente de trouver un accord dans le cluster qui est plus équilibré.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configuration des étapes de gestionnaire de ressources de Cluster et les composants Timer
Chacun de ces différents types de corrections, le Gestionnaire de ressources de Cluster peuvent rendre est contrôlée par une minuterie différente qui détermine sa fréquence. Par exemple, si vous souhaitez uniquement traiter les placer de nouvelles charges de travail de service du cluster chaque heure (pour les lots, haut), mais que vous souhaitez équilibrage régulière vérifie toutes les quelques secondes, vous pouvez configurer ce comportement. Lorsque chaque horloge se déclenche, la tâche est planifiée. Par défaut, le Gestionnaire de ressources analyse de son état et applique les mises à jour (toutes les modifications qui sont sont produites depuis la dernière analyse, comme remarquer qu’un nœud est arrêté le traitement par lots) tous les 1/10e de seconde, définit le positionnement et la contrainte vérifient indicateurs chaque seconde, et l’équilibrage marquer toutes les 5 secondes.

ClusterManifest.xml :

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Aujourd'hui nous uniquement effectuer une de ces actions à la fois, dans l’ordre (c’est pourquoi nous faisons référence à ces configurations en tant que « intervalles minimum »)). Il s’agit afin que, par exemple, nous avons déjà répondu à toutes les demandes en attente pour créer de nouveaux réplicas avant de continuer au cluster d’équilibrage. Comme vous pouvez le voir par l’intervalle de temps par défaut spécifié, nous pouvons analyser et vérifiez tout ce que nous devons faire très fréquemment, c'est-à-dire que l’ensemble de modifications que nous rendre à la fin de chaque étape est généralement plus petite : nous ne sommes pas analyse à heures de modifications dans le cluster et essayez de les corriger toutes en même temps, nous essayons de gérer les choses plus ou moins comme ils se produisent, mais avec un traitement par lots lorsque plusieurs événements se produisent à la à la fois. Ainsi, la ressource de Service Fabric manager très réactive aux événements qui se produisent dans le cluster.

Alors que la plupart de ces tâches sont simples (s’il y a violation de contrainte, correctif, s’il existe des services doit être créée, les créer), le Gestionnaire de ressources de Cluster doit également des informations supplémentaires afin de déterminer si le cluster déséquilibré. Pour que nous avons deux autres éléments de configuration : *L’équilibrage des seuils* et des *Seuils d’activité*.

## <a name="balancing-thresholds"></a>Équilibrage des seuils
Un seuil de l’équilibrage est le principal contrôle pour déclencher le rééquilibrage proactive (n’oubliez pas que la minuterie est seulement de la fréquence à laquelle le Gestionnaire de ressources du Cluster doit vérifier, cela ne signifie pas que quelque chose se produit). Le seuil de l’équilibrage définit comment déséquilibré le cluster doit être pour une mesure spécifique dans le Gestionnaire de ressources Cluster à prendre en compte il déséquilibré et équilibrage du déclencheur.

Équilibrage des seuils sont définis sur une base par le système métrique dans le cadre de la définition du cluster. Pour plus d’informations sur les mesures de l’extraction de [cet article](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

Le seuil de l’équilibrage d’une mesure est un rapport. Si le montant de la charge sur le nœud plus chargé divisé par le montant de la charge sur le nœud moins chargé dépasse ce nombre, le cluster est considéré comme déséquilibré et équilibrage sera déclenché la prochaine fois que le Gestionnaire de ressources de Cluster vérifie (par défaut, jamais de 5 secondes, en tant que régi par le MinLoadBalancingInterval, ci-dessus).

![Exemple de seuil de l’équilibrage][Image1]

Dans cet exemple simple, chaque service est consomme une unité de mesure de certains. Dans l’exemple supérieure, la charge maximale sur un nœud est 5 et la valeur minimale est 2. Supposons que le seuil de contrepartie pour cette mesure est de 3. Par conséquent, le cluster est considéré comme dans l’exemple supérieur, équilibrée et aucun équilibrage ne sera déclenchée lorsque le Gestionnaire de ressources de Cluster vérifie (dans la mesure où le rapport dans le cluster est de 5/2 = 2,5 et qui est inférieure au seuil de contrepartie spécifié de 3).

Dans l’exemple du bas, la charge maximale sur un nœud est 10, alors que la valeur minimale est 2, ce qui entraîne un taux de 5. Cela place le cluster dépasse le seuil d’équilibrage conçu de 3 pour cette mesure. En conséquence, une exécution rebalancing globale sera planifiée prochaine fois l’équilibrage de la minuterie se déclenche. Notez que parce que le coup d’une recherche de contrepartie ne signifie pas que quoi que ce soit se déplace - parfois le cluster est déséquilibré, mais la situation ne peut pas être améliorée - mais dans une situation comme celle-ci (au moins par défaut) certains certainement Node3 sera distribuée la charge. Notez que puisque nous n’utilisons pas une approche gourmande une charge peut également être distribuée à Node2 depuis qui entraînerait la minimisation des différences générales entre les nœuds, mais nous pouvons prévoir que la majorité de la charge s’afficherait à Node3.

![Équilibrage des Actions d’exemple de seuil][Image2]

Notez que la mise en dessous du seuil de contrepartie n’est pas un objectif explicit – l’équilibrage des seuils sont simplement un *déclencheur* qui indique au Gestionnaire de ressources de Cluster qui doit se présenter dans le cluster pour déterminer quelles améliorations il peut établir, le cas échéant.

## <a name="activity-thresholds"></a>Seuils d’activité
Dans certains cas, bien que les nœuds sont relativement déséquilibrés, le montant *total* de la charge dans le cluster est faible. Cela peut être simplement dû à l’heure de la journée, ou parce que le cluster est nouvelle et uniquement lors de l’obtention d’amorcé. Dans les deux cas, vous ne souhaitez ne peut-être pas le cluster d’équilibrage, car il est en fait très peu, voire être acquise – vous dépenserez simplement les ressources réseau et de calcul pour déplacer des éléments, sans rendre absolue de la différence de temps. Étant donné que nous voulons éviter cela, il existe un autre contrôle dans le Gestionnaire de ressources, connu sous le nom des seuils d’activité, qui vous permet de spécifier certains absolu de la limite inférieure d’activité – si aucun nœud ne possède au moins cette quantité de charge puis équilibrage ne sera pas déclenché même si le seuil de l’équilibrage est atteint.

Supposons que nous avons des rapports avec les totaux suivants destinés à la consommation sur les nœuds par exemple. Supposons également que nous conserver notre seuil équilibrage de 3 pour cette mesure, mais maintenant nous avons un seuil d’activité 1 536. Dans le premier cas, alors que le cluster est déséquilibré par le seuil d’équilibrage aucun nœud répond à ce seuil minimum d’activité, afin de nous laisser les choses tel quel. Dans l’exemple du bas, Node1 est moyen dépasse le seuil d’activité, l’équilibrage sera effectuée (dans la mesure où à la fois le seuil de l’équilibrage et le seuil de l’activité de la mesure sont dépassé)

![Exemple de seuil d’activité][Image3]

Tout comme l’équilibrage des seuils, des seuils d’activité sont définis par le système métrique via la définition du cluster :

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Notez que les seuils d’activité et de l’équilibrage sont tous les deux liés à la métrique - équilibrage ne sont déclenchés que si le dépassement des seuils d’équilibrage et une activité pour la même mesure. Par conséquent, si nous dépassent le seuil de l’équilibrage de la mémoire et le seuil d’activité pour l’UC, l’équilibrage ne déclenche pas tant que les seuils restants (équilibrage de seuil pour le processeur) et le seuil d’activité de la mémoire ne sont pas dépassées.

## <a name="balancing-services-together"></a>Services d’équilibre entre eux
Quelque chose d’intéressant à noter est que si le cluster est déséquilibré ou non est une décision de l’ensemble du cluster, mais la façon dont nous allez résoudre est déplacement des réplicas de service individuelles et les instances autour. Cela s’applique, droit ? Si la mémoire est empilé sur un nœud, plusieurs réplicas ou instances susceptibles de favoriser il, il pourrait exigent le déplacement des duplications avec état ou sans état instances qui utilisent la mesure concernée, déséquilibrée.

Il peut arriver que si un client appelle nous haut ou fichier un ticket indiquant qu’un service qui n’a pas été déséquilibré a été déplacé. Comment peut il se produire qu’un service est déplacé même si toutes les mesures du service ont été équilibrés, même parfaitement c’est le cas, au moment de l’autre le déséquilibre ? Voyons !

Prenons par exemple quatre services Service1, Service2, Service3 et Service4. Rapports de Service1 contre des mesures Metric1 et Metric2, Service2 contre Metric2 et Mmetric3, Service3 contre Metric3 et Metric4 et Service4 par rapport à certains Metric99 métrique. Sûrement, vous pouvez voir où nous allons ici. Nous avons une chaîne ! Du point de vue du Gestionnaire de ressources de Cluster, nous n’avons vraiment pas quatre services indépendants, nous disposons d’un ensemble de services connexes (Service1, Service2 et Service3) et un autre qui est désactivé sur sa propre.

![Services d’équilibre entre eux][Image4]

Il est donc possible qu’un déséquilibre dans Metric1 peut provoquer des duplications ou des instances de Service3 pour vous déplacer. Ces mouvements sont généralement assez limités, mais peut être plus importante en fonction exactement comment déséquilibré Metric1 obtenu et les modifications qui étaient nécessaires dans le cluster afin du pour corriger. Nous pouvons également dire avec certitude qu’un déséquilibre dans les mesures de 1, 2 ou 3 ne provoquera jamais mouvements dans Service4 – il n’y aura aucun point car les réplicas ou instances appartenant à Service4 autour peut ne font absolument rien pour avoir un impact sur le solde des mesures 1, 2 ou 3.

Le Gestionnaire de ressources de Cluster détermine automatiquement quels services sont liés, dans la mesure où les services ont été ajoutés, supprimés, ou avaient la modification de leur configuration métrique – par exemple, entre deux exécutions de l’équilibrage de Service2 peuvent avoir été reconfigurés pour supprimer Metric2. Cela rompt la chaîne entre Service1 et même2. Désormais au lieu de deux groupes de services, vous avez trois :

![Services d’équilibre entre eux][Image5]

## <a name="next-steps"></a>Étapes suivantes
- Métriques sont la façon dont le Gestionnaire de ressources de Cluster Service de Fabric gère la consommation et la capacité du cluster. Pour en savoir plus sur comment les configurer et de les extraire [cet article](service-fabric-cluster-resource-manager-metrics.md)
- Coût du mouvement est une façon de signalisation pour le Gestionnaire de ressources de Cluster que certains services sont plus coûteux à déplacer que d’autres. Pour plus d’informations sur les coûts de déplacement, reportez-vous à [cet article](service-fabric-cluster-resource-manager-movement-cost.md)
- Le Gestionnaire de ressources de Cluster possède plusieurs limitations que vous pouvez configurer pour ralentir évolution dans le cluster. Elles ne sont pas normalement nécessaires, mais si vous en avez besoin vous pouvez en savoir plus sur les [ici](service-fabric-cluster-resource-manager-advanced-throttling.md)


[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
