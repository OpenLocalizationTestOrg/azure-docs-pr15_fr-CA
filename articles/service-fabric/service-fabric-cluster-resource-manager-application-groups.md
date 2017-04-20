<properties
   pageTitle="Gestionnaire de ressources Cluster service Fabric - groupes d’Application | Microsoft Azure"
   description="Vue d’ensemble de la fonctionnalité de groupe de l’Application dans le Gestionnaire de ressources Cluster Service Fabric"
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

# <a name="introduction-to-application-groups"></a>Présentation des groupes d’Application
En règle générale, le Gestionnaire de ressources de Cluster du service de Fabric gère des ressources de cluster en répartissant la charge (représentée par les métriques) uniformément sur l’ensemble du cluster. Service de Fabric gère également la capacité des nœuds dans le cluster et le cluster dans son ensemble par le biais de la notion de capacité. Cela fonctionne bien pour un grand nombre de différents types de charges de travail, mais les modèles qui utilisent de manière intensive différentes Instances d’Application Service Fabric parfois mettre dans les exigences supplémentaires. Des exigences supplémentaires sont généralement :

- Possibilité de réserver de la capacité pour les services d’une Instance de l’Application sur un certain nombre de nœuds
- Possibilité de limiter le nombre total de nœuds d’un ensemble de services au sein d’une application est autorisé à s’exécuter sur
- Définition des capacités sur l’instance de l’application afin de limiter le nombre ou la consommation des ressources des services à l’intérieur

Pour répondre à ces exigences, nous avons développé la prise en charge de ce que nous appelons les groupes d’Application.

## <a name="managing-application-capacity"></a>Gestion de la capacité de l’Application
Capacité de l’application permet de limiter le nombre de nœuds fractionnés par une application, ainsi que la métrique charge que les instances de l’application sur des nœuds individuels. Il peut également être utilisé pour réserver des ressources dans le cluster d’application.

Capacité de l’application peut être définie pour les nouvelles applications lors de leur création ; Il peut également être mis à jour pour les applications existantes qui ont été créées sans spécifier la capacité de l’Application.

### <a name="limiting-the-maximum-number-of-nodes"></a>Le nombre maximal de nœuds
Les cas d’utilisation le plus simple pour la capacité de l’Application sont lors de l’instanciation de l’application doit être limité à un certain nombre maximal de nœuds. Si aucune capacité de l’Application n’est spécifiée, le Gestionnaire de ressources de Cluster Service Fabric instanciera réplicas selon les règles normales (équilibrage ou défragmentation), qui signifie généralement que ses services seront réparties sur tous les nœuds disponibles du cluster, ou si la défragmentation est activée sur un nombre arbitraire mais inférieur de nœuds.

L’image suivante illustre le placement potentiels d’une instance de l’application sans le nombre maximal de nœuds définis et l’application puis même avec un nombre maximal de nœuds définie. Notez qu’il n’y a aucune garantie concernant les réplicas ou les instances dont les services seront obtenir placés ensemble.

![Définir le nombre maximal de nœuds de l’Instance d’application][Image1]

Dans l’exemple de gauche, l’application n’a pas capacité d’Application définie, et il a trois services. CRM est devenu une décision logique pour répartir tous les réplicas sur six nœuds disponibles pour atteindre le meilleur équilibre dans le cluster. Dans l’exemple de droite, nous voyons la même application qui est limité à trois nœuds et où Service Fabric CRM a atteint l’équilibre optimal pour les réplicas des services de l’application.

Le paramètre qui contrôle ce comportement est appelé MaximumNodes. Ce paramètre peut être défini lors de la création de l’application, ou être mis à jour pour une instance de l’application qui était déjà en cours d’exécution, dans lequel cas Service Fabric CRM va limiter les réplicas des services de l’application et le nombre maximal défini de nœuds.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Capacité de charge et les mesures d’application
Groupes d’application vous permettent également de définir les mesures associées à une instance d’une application donnée, ainsi que la capacité de l’application en ce qui concerne ces mesures. Par exemple vous pouvez définir que comme de nombreux services comme vous le souhaitez peuvent être créés dans

Pour chaque mesure, il existe 2 valeurs qui peuvent être définies pour décrire la capacité de cette instance de l’application :

-   Nombre total d’Application capacité – représente la capacité totale de l’application d’une mesure particulière. Service de Fabric CRM va tenter de limiter la somme des charges de métrique de services de cette application à la valeur spécifiée ; en outre, si les services de l’application utilisent déjà charge cette limite, Gestionnaire de ressources du Cluster Service Fabric permet de désactiver la création de nouveaux services ou de partitions qui entraînent une charge totale dépasser cette limite.
-   Capacité maximale de nœud – indique la charge totale maximale pour les réplicas de services d’applications sur un seul nœud. Si la charge totale sur le nœud dépasse cette capacité, Service Fabric CRM va tenter de déplacer les réplicas vers d’autres nœuds pour que la contrainte de capacité est respectée.

## <a name="reserving-capacity"></a>Réservation de capacité
Une autre utilisation courante pour les groupes d’application est de s’assurer que les ressources du cluster sont réservées pour une instance d’une application donnée, même si l’instance d’application ne possède pas encore des services qu’il contient, ou même si elles ne sont pas consommer les ressources encore. Examinons comment qui se déroule.  

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Spécification d’un nombre minimal de nœuds et de réservation de ressources
Réservation des ressources pour une instance d’application requiert la spécification des deux paramètres supplémentaires : *MinimumNodes* et *NodeReservationCapacity*

- MinimumNodes - comme définissant un nombre maximal de cibles de nœuds qui les services au sein d’une application peuvent s’exécuter sur, vous pouvez également spécifier le nombre minimal d’une application à exécuter sur les nœuds. Ce paramètre définit efficacement le nombre de nœuds que les ressources doivent être réservés sur, au minimum, garantissant ainsi la capacité du cluster dans le cadre de la création de l’instance de l’application.
- NodeReservationCapacity - le NodeReservationCapacity peut être défini pour chaque mesure au sein de l’application. Définit la quantité de charge métrique réservée à l’application sur n’importe quel nœud, où aucun réplica ou les instances des services qu’il contient sont placés.

Examinons un exemple de réservation de capacité :

![Instances d’application définissant la capacité réservée][Image2]

Dans l’exemple de gauche, les applications n’ont pas les capacités des applications définies. Gestionnaire de ressources du Cluster service Fabric équilibrera les réplicas des services enfants et instances ainsi que celles provenant d’autres services (en dehors de l’application) de l’application pour assurer l’équilibre du cluster.

Dans l’exemple de droite, disons que l’application a été créée avec un MinimumNodes de 2, 3 et une application métrique est défini avec une réservation de 20, capacité max sur un nœud de 50 % et une capacité d’application total de 100, la valeur de MaximumNodes Service réserve de la capacité sur les deux nœuds pour l’application de bleue et n’autorise pas les autres réplicas dans le cluster à utiliser cette capacité. Cette capacité d’application réservés sera considérée consommé et comptés par rapport à la capacité de cluster restants.

Lorsqu’une application est créée avec la réservation, le Gestionnaire de ressources de Cluster de réserve de la capacité égale à MinimumNodes * NodeReservationCapacity dans le cluster, mais il ne réservera pas de capacité sur des nœuds spécifiques jusqu'à ce que les réplicas des services de l’application sont créés et placés. Ainsi, pour une souplesse, étant donné que les nœuds sont choisis pour les nouveaux réplicas uniquement lorsqu’elles sont créées. La capacité est réservée sur un nœud spécifique lorsqu’au moins un réplica est placé dessus.

## <a name="obtaining-the-application-load-information"></a>Obtenir des informations sur le chargement de l’application
Pour chaque application la qu’a capacité d’Application défini vous pouvez obtenir les informations sur la charge totale signalée par les réplicas de ses services. Service de Fabric permet les requêtes en PowerShell et API gérées à cet effet.

Par exemple, la charge peut être récupéré à l’aide de l’applet de commande PowerShell suivante :

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

Le résultat de cette requête contient les informations de base sur la capacité de l’Application qui a été spécifié pour l’application, comme les nœuds Minimum et Maximum. Il y aura également des informations sur le nombre de nœuds qui utilise actuellement l’application. Ainsi, pour chaque mesure de charge il y aura des informations sur :
- : Métrique nom de la mesure.
-   Capacité de réserve : La capacité de Cluster qui est réservée dans le cluster pour cette Application.
-   Chargement de l’application : La charge totale des duplications d’enfant de cette Application.
-   Capacité de l’application : Maximum autorisé la valeur de la charge de l’Application.

## <a name="removing-application-capacity"></a>Supprimer la capacité de l’Application
Une fois les paramètres de capacité de l’Application sont définis pour une application, ils peuvent être supprimés à l’aide des applets de commande PowerShell ou de mise à jour Application API. Par exemple :

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Cette commande supprime tous les paramètres de capacité de l’Application à partir de l’application et Gestionnaire de ressources du Cluster Service Fabric démarrera traitant de cette application comme toute autre application dans le cluster qui n’a pas les paramètres définis. L’effet de la commande est immédiate et Gestionnaire de ressources de Cluster supprime tous les paramètres de capacité de l’Application pour cette application ; les spécifier à nouveau nécessiterait une API d’Application de mise à jour d’être appelée avec les paramètres appropriés.

## <a name="restrictions-on-application-capacity"></a>Restrictions sur la capacité de l’Application
Il existe plusieurs restrictions sur les paramètres de capacité de l’Application qui doivent être respectées. En cas d’erreurs de validation, la création ou la mise à jour de l’application est rejetée avec une erreur.
Tous les paramètres d’entiers doivent être des nombres positifs.
En outre, pour chaque paramètre de restrictions sont les suivantes :

-   MinimumNodes ne doit jamais être supérieur à MaximumNodes.
-   Si les capacités d’une mesure de charge sont définies, ils doivent suivre ces règles :
  - Capacité de réserve de nœud ne doit pas être supérieure à la capacité maximale du nœud. Par exemple, vous ne peut pas limiter la capacité de mesure « UC » sur le nœud à 2 unités et essayez de réserver 3 unités sur chaque nœud.
  - Si MaximumNodes est spécifié, le produit de MaximumNodes et de la capacité maximale de nœud ne doit pas être supérieur à la capacité de l’Application. Par exemple, si vous définissez la capacité maximale du nœud pour mesure de charge « UC » à 8 et que vous définissez les nœuds au Maximum sur 10, capacité d’Application Total doit être supérieure à 80 pour cette mesure de charge.

Les restrictions sont appliquées à la fois lors de la création d’applications (côté client) et au cours de la mise à jour de l’application (côté serveur). Lors de la création, il s’agit d’un exemple d’une violation claire des exigences depuis MaximumNodes < MinimumNodes et la commande échouera dans le client avant que la demande est envoyée même cluster de Service Fabric :

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

Voici un exemple de mise à jour non valide. Si nous prendre une application existante et mettre à jour les nœuds au maximum à une valeur, la mise à jour passe :

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

Après cela, nous pouvons tenter de mettre à jour les nœuds minimales :

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

Le client n’a pas suffisamment de contexte relatives à l’application afin qu’il permettra la mise à jour passer au cluster Service Fabric. Toutefois, dans le cluster, Service Fabric validera le nouveau paramètre avec les paramètres existants et ne pourra pas l’opération de mise à jour car les nœuds minimale ennemi de valeur est supérieure à la valeur de nœuds au maximum. Dans ce cas, les paramètres de capacité de l’Application reste inchangées.

Ces restrictions sont mises en place dans l’ordre pour le Gestionnaire de ressources Cluster pour être en mesure de fournir le meilleur placement pour les réplicas de services d’applications.

## <a name="how-not-to-use-application-capacity"></a>Comment ne pas utiliser la capacité de l’Application

-   N’utilisez pas la capacité de l’Application pour contraindre l’application à un sous-ensemble de nœuds : bien que Fabric de Service permet de garantir que les nœuds au Maximum soit respecté pour chaque application qui a la capacité d’Application spécifiée, les utilisateurs ne peuvent pas décider quels nœuds il sera instancié sur. Ceci peut être réalisé à l’aide des contraintes de placement pour les services.
-   N’utilisez pas la capacité de l’Application pour vous assurer que les deux services à partir de la même application sont toujours placés à côté de l’autre. Ceci est possible à l’aide de l’AFFINITE entre les services et l’affinité peut être limitée aux services qui doivent être placés entre eux.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services d’extraction la rubrique sur les autres configurations de gestionnaire de ressources de Cluster disponibles [en savoir plus sur la configuration des Services](service-fabric-cluster-resource-manager-configure-services.md)
- Pour savoir plus sur comment le Gestionnaire de ressources de Cluster gère et équilibre la charge du cluster, consultez l’article sur [l’équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
- Démarrer à partir du début et [obtenir une Introduction au Gestionnaire de ressources Cluster Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Pour plus d’informations sur la façon dont les mesures généralement lire des [Métriques de charge Service de Fabric](service-fabric-cluster-resource-manager-metrics.md)
- Le Gestionnaire de ressources de Cluster comporte un grand nombre d’options pour la description du cluster. Pour en savoir plus sur les consultez cet article sur la [Description d’un tissu de Service de cluster](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
