<properties
   pageTitle="Gestionnaire de ressources Cluster service Fabric - stratégies de positionnement | Microsoft Azure"
   description="Vue d’ensemble des stratégies de placement supplémentaires et les règles de Service des Services de Fabric"
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

# <a name="placement-policies-for-service-fabric-services"></a>Stratégies de positionnement des services de fabric de service
De nombreuses différentes règles supplémentaires que vous risquez de me soucie si votre cluster Service Fabric est fractionné sur un distances géographiques, par exemple plusieurs centres de données ou les régions Azure, ou si votre environnement s’étend sur plusieurs zones du contrôle géopolitique (ou de certains autres cas où vous avez des frontières juridiques ou la stratégie vous intéressent, les distances impliquées ont l’impact des performances/latence réelle). La plupart d'entre elles peut être configurée via les propriétés du nœud et les contraintes de placement, mais certaines sont plus complexes. Pour rendre les choses plus simples, nous fournir ces commmands supplémentaires. Tout comme avec d’autres contraintes de placement, les stratégies de placement peuvent être configurés sur la base d’une instance nommée par le service.

## <a name="specifying-invalid-domains"></a>Spécifiez les domaines non valides
La stratégie de positionnement InvalidDomain vous permet de spécifier qu’un domaine d’erreur particulière n’est pas valide pour cette charge de travail. Cette stratégie garantit qu’un service donné ne s’exécute jamais dans une zone particulière, par exemple pour des raisons de la stratégie d’entreprise ou de géopolitique. Plusieurs domaines non valides peuvent être spécifiés via des stratégies distinctes.

![Exemple de domaine non valide][Image1]

Code :

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Spécifiant les domaines requis
La stratégie de positionnement de domaine requiert que tous les réplicas avec état ou les instances de service sans état pour le service dans le domaine spécifié. Plusieurs domaines requis peuvent être spécifiés par l’intermédiaire des stratégies distinctes.

![Exemple de domaine requis][Image2]

Code :

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Spécifier un domaine par défaut pour les réplicas principales
Le domaine principal par défaut est un contrôle intéressant, dans la mesure où il permet de sélectionner le domaine d’erreur dans laquelle le principal doit être placé s’il est possible de le faire. Lorsque tout est en bon état principal finiront dans ce domaine. Doit être le domaine ou la réplica principal en panne ou être arrêté pour une raison quelconque le serveur principal sera migré vers un autre emplacement. Si cet emplacement n’est pas dans le domaine par défaut, puis lorsque c’est possible le Gestionnaire de ressources du Cluster seront déplacez-le vers le domaine par défaut. Naturellement ce paramètre convient uniquement pour les services de l’état. Cette stratégie est particulièrement utile dans les clusters qui sont fractionnés sur les régions Azure ou plusieurs centres de données. Dans ces situations vous utilisez tous les emplacements pour la redondance, mais vous préférez que les réplicas primaires soient placés dans un certain emplacement afin de fournir une latence inférieure pour les opérations qui, sur le serveur principal (écrit et également par défaut toutes les lectures sont pris en charge par le serveur principal).

![Basculement sur incident et domaines de principales par défaut][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Nécessitant des réplicas sont réparties sur tous les domaines et désactivation de livraison
Une autre stratégie, que vous pouvez spécifier est d’exiger des réplicas pour toujours être distribuées parmi les domaines disponibles de panne. Cela se produit par défaut dans la plupart des cas où le cluster est en bonne santé, cependant il existe des cas dégénérées où les réplicas pour une partition donnée peut finir par temporairement de rassembler dans une panne ou d’une mise à niveau de domaine. Par exemple, supposons que bien que le cluster a 9 nœuds dans des domaines d’erreur 3 (0, 1 et 2) et votre service a 3 répliques et les nœuds qui ont été utilisés pour ces réplicas pannes domaines 1 et 2 s’est arrêté en raison de problèmes de capacité, aucun des autres nœuds dans ces domaines étaient valides. Si le Service Fabric ont été pour construire des remplacements pour ces réplicas, le Gestionnaire de ressources de Cluster est à les placer dans un domaine d’erreur 0, mais qui crée une situation dans laquelle de violation de la contrainte de domaine d’erreur. Elle augmente également le risque que le jeu entier de réplicas peut être perdu (si le lecteur de disquette 0 ont été soit permananently perdu). (Pour plus d’informations sur les contraintes et les contraintes de priorités, en général, consultez [cette rubrique](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) )

Si vous avez déjà vu un avertissement de santé tels que « l’équilibreur de charge a détecté une Violation de contrainte pour ce réplica : fabric : /<some service name> Partition secondaire <some partition ID> viole la contrainte : FaultDomain » que vous avez rencontré cette condition ou quelque chose de semblable. Ces situations sont généralement transitoires (les nœuds ne restent pas vers le bas le long, ou si c’est le cas et que nous devons construire remplacements il sont d’autres nœuds dans les domaines d’erreur approprié qui sont valides), mais il existe certaines charges de travail qui seraient échanges plutôt le risque de perte de tous les réplicas de leurs disponibilité. Nous pouvons le faire en spécifiant la stratégie de « RequireDomainDistribution », ce qui garantit qu’aucun deux réplicas à partir de la même partition ne sont jamais autorisés dans le même domaine de panne ou de mise à niveau.

Certaines charges de travail aurait plutôt le nombre de cibles de duplications (copies d’état) à tout moment (Paris contre les défaillances de domaine total et en sachant qu’ils peuvent généralement récupérer un état local), alors que d’autres utilisateurs au lieu de cela prendrait le temps d’arrêt plus tôt que les problèmes d’exactitude et de la perte des risques. Dans la mesure où la plupart des charges de travail s’exécutent avec plus de 3 réplicas, la valeur par défaut est pas requièrent une distribution du domaine et permet l’équilibrage et le basculement gérer les cas normalement même si cela implique que temporairement un domaine possède plusieurs réplicas emballés dans ce.

Code :

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Maintenant, il serait possible d’utiliser ces configurations pour les services dans un cluster, ce qui n’était pas géographiquement fractionné ? Vous pouvez bien sûr ! Mais il n’est pas une seule raison de trop – en particulier les configurations de domaine requis, non valide et préférée doivent être évitées, sauf si vous exécutez en fait un cluster géographiquement fractionné - il n’y a aucun sens pour tenter de forcer une charge de travail à exécuter dans un seul rack ou à préférer des segments de votre cluster local plutôt qu’un autre que s’il existe différents types de segmentation de matériel ou de la charge de travail sur , et ces cas peuvent être gérés via les contraintes de placement normal.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services d’extraction la rubrique sur les autres configurations de gestionnaire de ressources de Cluster disponibles [en savoir plus sur la configuration des Services](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
