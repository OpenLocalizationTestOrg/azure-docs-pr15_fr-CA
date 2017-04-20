<properties
   pageTitle="Coût de service Gestionnaire de ressources du Cluster Fabric : mouvement | Microsoft Azure"
   description="Vue d’ensemble des coûts de déplacement pour les services de Fabric de Service"
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

# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Coût de mouvement de service permettant d’influencer le choix du Gestionnaire de ressources de Cluster
Un facteur important à prendre en compte lorsque vous essayez de déterminer les modifications à apporter à un cluster et le score d’une solution est le coût global de la réalisation de cette solution.

Déplacement de service instances ou réplicas de coûts UC temps et la bande passante au minimum. Pour les services de l’état, il coûte également la quantité d’espace sur le disque dont vous avez besoin pour créer une copie de l’état avant d’éteindre les anciens réplicas. Clairement, vous pouvez réduire le coût de toute solution de gestionnaire de ressources du Cluster de tissu Azure Service apparaît avec. Mais vous ne voulez pas ignorer les solutions qui permettraient d’améliorer considérablement l’allocation des ressources du cluster.

Gestionnaire de ressources de cluster a deux méthodes de calcul des coûts et de les limiter, même lorsqu’il tente de gérer le cluster en fonction de ses autres objectifs. Le premier est que, lorsque le Gestionnaire de ressources de Cluster prévoit une nouvelle mise en page pour le cluster, il compte chaque déplacement qui serait. Dans un cas simple, si vous obtenez deux solutions à l’aide de la même manière globale solde (score) à la fin, puis prenez l’une avec le coût le plus bas (nombre total de déplacements).

Cela fonctionne très bien. Mais comme valeur par défaut ou les charges statiques, il est peu probable dans tout système complexe que tous les déplacements sont égales. Certains sont susceptibles d’être beaucoup plus coûteuse.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Modifier les coûts de déplacement et les facteurs à prendre en compte d’un réplica
Comme les rapports de charge (une autre fonctionnalité Gestionnaire de ressources du Cluster), vous permettent le service de reporting automatique le coût du service est pour vous déplacer à un moment donné.

Code :

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost a quatre niveaux : nulle, faible, moyen et élevé. Il s’agit par rapport aux autres, à l’exception de zéro. Zéro signifie que le déplacement d’un réplica est gratuit et ne doit pas compter sur le score de la solution. Les coûts de déplacement élevé est *pas* une garantie que le réplica ne déplacez, juste qu’il n’est pas déplacé sauf s’il existe une bonne raison.

![Coûts de déplacement comme un facteur dans le choix des réplicas de mouvement][Image1]

MoveCost vous permet de trouver les solutions qui entraînent le minimum de perturbations globale et sont plus faciles à atteindre tout en arrivant toujours au solde équivalent. Notion d’un service de coût peut être par rapport à beaucoup de choses. Les facteurs les plus courantes pour calculer les coûts de déplacement sont :

- Le montant de l’état ou les données que le service doit déplacer.
- Le coût de déconnexion des clients. Le coût du déplacement d’un réplica principal est généralement plus élevé que le coût du déplacement d’un réplica secondaire.
- Le coût de l’interruption d’une opération en cours. Niveau de certaines opérations sur les données de la banque ou les opérations effectuées en réponse à l’appel d’un client sont coûteuses. Après un certain point, vous ne souhaitez pas les arrêter si vous n’êtes pas obligé. Par conséquent, pendant la durée de l’opération, vous en relief le coût afin de réduire la probabilité que le réplica du service ou l’instance se déplace. Une fois l’opération terminée, vous la placez retour à la normale.

## <a name="next-steps"></a>Étapes suivantes
- Gestionnaire de ressources du Cluster service Fabric utilise des métriques pour gérer la consommation et la capacité du cluster. Pour plus d’informations sur les mesures et comment les configurer, consultez [la consommation de ressources de gestion et de la charge dans Fabric de Service avec des mesures](service-fabric-cluster-resource-manager-metrics.md).
- Pour savoir comment le Gestionnaire de ressources de Cluster gère et équilibre la charge du cluster, consultez [votre Fabric du Service de cluster d’équilibrage](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
