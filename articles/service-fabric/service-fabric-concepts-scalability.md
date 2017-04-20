<properties
   pageTitle="L’évolutivité des services de Fabric de Service | Microsoft Azure"
   description="Décrit comment mettre à l’échelle des services de Fabric de Service"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="scaling-service-fabric-applications"></a>Mise à l’échelle des applications de Service Fabric
Azure Fabric du Service rend facile de créer des applications évolutives par l’équilibrage de la charge les services, les partitions et les réplicas sur tous les nœuds dans un cluster. Ainsi, le taux d’utilisation maximal des ressources.

Échelle élevée pour les applications de Service Fabric peut être réalisé de deux manières :

1. Mise à l’échelle au niveau de la partition

2. Mise à l’échelle au niveau du nom de service

## <a name="scaling-at-the-partition-level"></a>Mise à l’échelle au niveau de la partition
TISSU de service prend en charge le partitionnement de chaque service en plusieurs partitions plus petites. Le [partitionnement de présentation](service-fabric-concepts-partitioning.md) fournit des informations sur les types de modes de partitionnement sont pris en charge. Les réplicas de chaque partition sont répartis sur les nœuds d’un cluster. Considérons un service qui utilise un schéma de partitionnement sont avec une clé faible 0, une clé forte de 99 et quatre partitions. Dans un cluster à trois nœuds, le service peut être disposé avec quatre réplicas qui partagent les ressources de chaque nœud, comme illustré ici :

![Disposition de partition avec trois nœuds](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

L’augmentation du nombre de nœuds permet de tissu de Service d’utiliser les ressources sur les nouveaux nœuds en déplaçant certaines des réplicas de nœuds vides. En augmentant le nombre de nœuds à quatre, le service a maintenant trois répliques sont en cours d’exécution sur chaque nœud (des différentes partitions), ce qui permet des performances et une meilleure utilisation des ressources.

![Disposition de partition avec quatre nœuds](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## <a name="scaling-at-the-service-name-level"></a>Mise à l’échelle au niveau du nom de service
Une instance de service est une instance spécifique d’un nom d’application et un nom de type de service (voir le [cycle de vie d’application Fabric du Service](service-fabric-application-lifecycle.md)). Lors de la création d’un service, vous spécifiez la partition système (voir [services de Fabric de Service partitionnement](service-fabric-concepts-partitioning.md)) à utiliser.

Il est le premier niveau de la mise à l’échelle par nom de service. Vous pouvez créer de nouvelles instances d’un service, avec différents niveaux de partitionnement, comme vos anciennes instances de service sont actifs. Ainsi, les nouveaux consommateurs de service à utiliser des instances de service de moins occupé, plutôt que ceux qui sont plus occupé.

Une option pour augmenter la capacité, ainsi que les nombres de partition croissante ou décroissante, consiste à créer une nouvelle instance de service avec un nouveau schéma de partition. Cela complique, cependant, comme n’importe quel beaucoup de clients ont besoin de savoir quand et comment utiliser le service nommé différemment.

### <a name="example-scenario-embedded-dates"></a>Exemple de scénario : incorporé des dates
Un scénario possible consisterait à utiliser des informations de date dans le cadre du nom du service. Par exemple, vous pouvez utiliser une instance de service portant un nom spécifique pour tous les clients qui rejoint en 2013 et un autre nom pour les clients qui rejoint en 2014. Ce schéma d’affectation de noms permet d’augmentation de la par programmation les noms en fonction de la date (comme les approches 2014, l’instance de service pour 2014 peut être créé à la demande).

Toutefois, cette approche est basée sur les clients à l’aide des informations d’affectation de noms spécifique à l’application qui est en dehors de l’étendue des connaissances de Fabric du Service.

- *À l’aide d’une convention d’affectation de noms*: dans 2013, lorsque votre application passe live, vous créez un service appelé fabric : application / / service2013. Près du deuxième trimestre 2013, vous créez un autre service, appelé fabric : application / / service2014. Ces deux services sont du même type de service. Dans cette approche, votre client devra utiliser la logique pour construire le nom de service approprié en fonction de l’année.

- *À l’aide d’un service de recherche*: un autre modèle est de fournir un service de recherche secondaires, qui peut fournir le nom du service pour une clé de votre choix. Les nouvelles instances de service peuvent ensuite être créés par le service de recherche. Le service de recherche lui-même ne conserve pas les données d’application, que les données sur les noms de service qu’il crée. Ainsi, par exemple basée sur une année ci-dessus, le client serait tout d’abord contacter le service de recherche pour trouver le nom du service de gestion des données pour une année donnée et puis utiliser ce nom de service pour l’exécution de l’opération en cours. Le résultat de la première recherche peut être mis en cache.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concepts de Service Fabric, consultez les rubriques suivantes :

- [Disponibilité des services de Fabric de Service](service-fabric-availability-services.md)

- [Partitionnement des services de Fabric de Service](service-fabric-concepts-partitioning.md)

- [Définition et gestion de l’état](service-fabric-concepts-state.md)
