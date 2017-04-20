<properties
   pageTitle="Gestionnaire de ressources Cluster service Fabric - affinité | Microsoft Azure"
   description="Présentation de la configuration des affinités pour les Services de Fabric de Service"
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

# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configuration et l’utilisation de l’affinité de service dans le Service Fabric

L’affinité est un contrôle qui est fourni principalement pour aider à faciliter la transition des applications monolithiques plus importantes dans le monde du nuage et de microservices. Qui dit qu’il permet également dans certains cas comme une optimisation légitime pour améliorer les performances des services, bien que cela peut avoir des effets secondaires.

Supposons que vous importez une application plus grande, ou une juste n’a pas été conçu avec microservices à l’esprit, au Service de Fabric. Cette transition est réellement commune, et nous avons plusieurs clients (internes et externes) dans cette situation. Vous démarrez en soulevant l’ensemble de votre application dans l’environnement, surtout incluse lors de l’obtention et l’exécution. Vous démarrez ensuite diviser en différents services plus petites que tous communiquer entre eux.

Il y a une section « Oops... ». Le « Oops » se situe généralement dans une de ces catégories :

1. Certains composant X dans l’application monolithique a une dépendance non documentée sur composant Y, et nous vient d’avoir en services distincts. Dans la mesure où elles sont en cours d’exécution sur différents nœuds du cluster, ils sont rompus.
2.  Ces éléments communiquent via (local canaux nommé | mémoire partagée | fichiers sur le disque), mais je dois être en mesure de mettre à jour indépendamment pour accélérer un peu. Je vais supprimer la dépendance dure plus tard.
3.  Tout se passe bien, mais il s’avère que ces deux composants sont en fait très bavards/performances sensibles. Lorsqu’ils les déplacés en services distincts globale latence ou les performances de l’application tanked a augmenté. Par conséquent, l’application globale ne répond pas aux attentes.

Dans ce cas nous ne souhaitez pas perdre notre travail de refactorisation et ne souhaitez pas revenir au modèle monolithique, mais nous avons besoin d’une certaine façon de la localité. Il est conservé jusqu'à ce que nous pouvons revoir les composants fonctionnent naturellement en tant que services, soit jusqu'à ce que nous pouvons résoudre les attentes de performance un autre moyen, si possible.

Que faire ? Ainsi vous pouvez essayer d’activation de l’affinité.

## <a name="how-to-configure-affinity"></a>Comment faire pour configurer l’affinité
Pour définir une affinité, vous définissez une relation entre deux services d’affinité. Vous pouvez considérer comme « pointe » un service à un autre et en indiquant que « ce service ne peut fonctionner lorsque que le service est en cours d’exécution. » Parfois nous appelons à l’affinité pour une relation parent/enfant (où vous pointez l’enfant au parent). L’affinité permet de s’assurer que les réplicas ou les instances d’un service sont placés sur les nœuds de mêmes que les réplicas ou les instances d’un autre.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Options d’affinité différents
L’affinité est représentée par l’intermédiaire d’un de plusieurs jeux de corrélations et comporte deux modes différents. Le mode plus courants d’affinité est ce que nous appelons NonAlignedAffinity. Dans NonAlignedAffinity, les réplicas ou les instances des différents services sont placés sur les nœuds de mêmes. L’autre mode est AlignedAffinity. Affinité alignée est utile qu’avec les services de l’état. Configuration des deux services avec état pour avoir aligné affinité permet de s’assurer que les couleurs primaires de ces services sont placés sur les nœuds de mêmes que chacun des autres. Elle entraîne également chaque paire de serveurs secondaires pour être placés sur les nœuds du mêmes de ces services. Il est également possible (bien que moins commun) de configurer NonAlignedAffinity pour les services de l’état. Pour NonAlignedAffinity les réplicas différents des deux services dynamique devrait être colocalisés sur les nœuds du mêmes, mais aucune tentative n’est établie afin d’aligner leurs couleurs primaires ou des serveurs secondaires.

![Modes d’affinité et de leurs effets][Image1]

### <a name="best-effort-desired-state"></a>Meilleur état d’effort nécessaire
Il existe quelques différences entre l’affinité et architectures monolithiques. Beaucoup d'entre eux sont car une relation d’affinité est conseillée. Les services dans une relation d’affinité sont fondamentalement différentes entités qui peuvent échouer et être déplacées séparément. Il existe également des causes pour pourquoi se briser une relation d’affinité. Par exemple, limites de capacité où seuls certains des objets de service dans la relation de l’affinité peuvent tenir sur un nœud donné. Dans ces cas même s’il existe une relation d’affinité en place, il ne peut pas être appliquée en raison d’autres contraintes. S’il est possible d’appliquer toutes les autres contraintes et affinité ultérieurement que la violation de la contrainte d’affinité est corrigée automatiquement.  

### <a name="chains-vs-stars"></a>Chaînes et étoiles
Aujourd'hui nous ne sommes pas en mesure de chaînes de modèle de relations d’affinité. Cela signifie qui est un service qui est un enfant dans une relation d’affinité, ne peut pas être un parent dans une autre relation d’affinité. Si vous souhaitez modéliser ce type de relation, vous devez effectivement il modèle sous la forme d’une étoile, plutôt qu’une chaîne. Pour ce faire, l’enfant plus bas serait être apparenté au parent de l’enfant « middle » à la place. En fonction de la disposition de vos services, cela peut nécessiter la création d’un service « espace réservé » pour servir de parent de plusieurs enfants.

![Chaînes et étoiles dans le cadre des relations d’affinité][Image2]

Une autre chose à noter à propos des relations d’affinité aujourd'hui est qu’ils sont directionnels. Cela signifie que la règle de « l’affinité » applique seulement que l’enfant où est le parent. Si par exemple le parent soudainement bascule vers un autre nœud puis le Gestionnaire de ressources de Cluster ne réellement pense qu’il existe un problème jusqu'à ce qu’elle constate que l’enfant ne se trouve pas avec un parent ; la relation n’est pas appliquée immédiatement.

### <a name="partitioning-support"></a>Prise en charge du partitionnement
La dernière chose à remarquer concernant l’affinité est que l’affinité relations ne sont pas prises en charge dans laquelle le parent est partitionné. Il s’agit de quelque chose que nous pouvons prendre en charge par la suite, mais aujourd'hui il n’est pas autorisé.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services d’extraction la rubrique sur les autres configurations de gestionnaire de ressources de Cluster disponibles [en savoir plus sur la configuration des Services](service-fabric-cluster-resource-manager-configure-services.md)
- Pour de nombreuses raisons où les personnes utilisent l’affinité, comme la limitation des services à un petit ensemble de machines et essayez de regrouper la charge d’un ensemble de services, est mieux pris en charge par des groupes d’applications. Extraire des [Groupes d’applications](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
