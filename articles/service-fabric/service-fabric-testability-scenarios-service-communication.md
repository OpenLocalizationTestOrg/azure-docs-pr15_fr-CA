<properties
   pageTitle="Testabilité : Service de communication | Microsoft Azure"
   description="La communication de service-service est un point critique d’intégration d’une application de Service Fabric. Cet article traite des considérations relatives à la conception et les techniques de test."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-testability-scenarios-service-communication"></a>Scénarios de testabilité Fabric du service : Service de communication

Microservices et surface orientée service de styles architecturaux naturellement dans Azure Fabric de Service. Dans ces types d’architectures distribuées, les applications basées sur des composants microservice sont généralement composées de plusieurs services qui doivent communiquer entre eux. Dans le même cas les plus simples, vous avez généralement au moins un service web sans état et un service de stockage de données avec état qui doivent communiquer.

La communication de service-service est un point critique d’intégration d’une application, car chaque service expose une API à distance à d’autres services. Demande de travail avec un jeu de frontières de l’API qui implique généralement des e/s l’attention, avec une grande quantité de test et de validation.

De nombreux aspects sont à effectuer lorsque ces limites de service sont reliées entre elles dans un système distribué :

 - *Protocole de transport*. Allez-vous utiliser HTTP pour une plus grande interopérabilité, ou un protocole binaire personnalisé pour un débit maximal ?
 - *Gestion des erreurs*. Mode de traitement des erreurs permanentes et transitoires ? Que se passe-t-il lorsqu’un service se déplace vers un nœud différent ?
 - *Délais d’attente et la latence*. Dans les applications multicouches, comment chaque couche de service gérera la latence par le biais de la pile et à l’utilisateur ?

Si vous utilisez un des composants de communication de service intégré fournis par le Service Fabric ou si vous générez votre propre, les interactions entre vos services de tests est essentiel pour garantir la résilience dans votre application.

## <a name="prepare-for-services-to-move"></a>Préparer pour les services à déplacer

Instances de service peuvent déplacer au fil du temps. Cela est particulièrement vrai lorsqu’ils sont configurés avec des mesures de charge pour équilibrage de charge personnalisée optimale des ressources. Service de Fabric déplace vos instances de service afin d’optimiser leur disponibilité même lors des mises à niveau, les basculements, horizontale et autres situations qui se produisent sur la durée de vie d’un système distribué.

Comme les services de se déplacement dans le cluster, vos clients et autres services doivent être préparées à gérer deux scénarios lorsque leur communiquer avec un service :

- Le réplica d’instance ou d’une partition de service a été déplacé depuis la dernière fois que vous parlé à elle. Il s’agit d’une partie normale d’un cycle de vie du service, et il ne doit se produire au cours de la durée de vie de votre application.
- Le réplica d’instance ou d’une partition de service est en mouvement. Bien que le basculement d’un service à partir d’un nœud à un autre se produit très rapidement dans un Service de Fabric, il peut être un retard dans la disponibilité si le composant de communication de votre service est lent à démarrer.

Gérer ces scénarios correctement est important pour un système de fluidité. Pour ce faire, gardez à l’esprit que :

- Chaque service peut être connecté à a une *adresse* il écoute (par exemple, HTTP ou WebSocket). Lorsqu’une instance de service ou d’une partition est déplacé, son point de terminaison adresse change. (Il se déplace vers un nœud différent avec une adresse IP différente.) Si vous utilisez les composants de communication intégrées, elles gérera ré-résolution des adresses de service pour vous.
- Il peut y avoir une augmentation de la latence de service en tant que service de l’instance démarre son écouteur à nouveau. Cela dépend de la vitesse à laquelle le service ouvre le port d’écoute après le déplacement de l’instance de service.
- Toutes les connexions existantes doivent être fermé et rouvert une fois que le service s’ouvre sur un nouveau nœud. Un redémarrage ou un arrêt du nœud progressif permet aux connexions existantes de s’arrêter correctement.

### <a name="test-it-move-service-instances"></a>Testez-le : déplacer des instances de service

A l’aide des outils de test de Service Fabric, vous pouvez créer un scénario de test pour tester ces situations de différentes façons :

1. Déplacer un réplica principal d’état d’un service.

    Le réplica principal d’une partition de service dynamique peut être déplacé pour différentes raisons. Utilisez cette option pour cibler le réplica principal d’une partition spécifique pour voir comment vos services de réagissent à la déplacer de façon très contrôlée.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Arrêt d’un nœud.

    Lorsqu’un nœud est arrêté, le Service Fabric déplace toutes les instances de service ou de partitions qui se trouvaient sur ce nœud à l’un des autres nœuds disponibles du cluster. Cela permet de tester une situation dans laquelle un nœud est perdu à partir de votre cluster et toutes les instances de service et devront de réplicas sur ce nœud à déplacer.

    Vous pouvez arrêter un nœud à l’aide de l’applet de commande PowerShell **Stop-ServiceFabricNode** :

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node_1

    ```

## <a name="maintain-service-availability"></a>Maintenir la disponibilité du service

En tant que plate-forme, Fabric du Service est conçu pour fournir une haute disponibilité de vos services. Mais, dans des cas extrêmes, des problèmes d’infrastructure sous-jacente peuvent provoquer une indisponibilité. Il est important de tester ces scénarios, trop.

Avec état services utilisent un système basé sur le quorum pour répliquer des état de haute disponibilité. Cela signifie qu’un quorum de réplicas doit être disponible pour effectuer des opérations d’écriture. Dans rares cas, par exemple une panne généralisée, un quorum de réplicas n’est peut-être pas disponible. Dans ces cas, vous ne serez pas en mesure d’effectuer des opérations d’écriture, mais vous serez toujours en mesure d’effectuer des opérations de lecture.

### <a name="test-it-write-operation-unavailability"></a>Testez-le : écrire l’indisponibilité de l’opération

En utilisant les outils de test de Service Fabric, vous pouvez injecter une défaillance qui entraîne la perte de quorum en tant que test. Bien que ce cas est rare, il est important que les clients et les services qui dépendent d’un service avec état sont préparés pour gérer des situations où ils ne peut pas effectuer d’écrire des requêtes. Il est également important que le service dynamique lui-même est informé de cette possibilité et qu’il peut communiquer normalement il aux appelants.

Vous êtes susceptible d’entraîner des pertes de quorum à l’aide de l’applet de commande PowerShell **Invoke-ServiceFabricPartitionQuorumLoss** :

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Dans cet exemple, nous avons défini `QuorumLossMode` de `QuorumReplicas` pour indiquer que nous voulons induire la perte de quorum sans arrêter tous les réplicas. De cette manière, les opérations de lecture sont toujours possibles. Pour tester un scénario dans lequel une partition complète n’est pas disponible, vous pouvez définir ce commutateur `AllReplicas`.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les actions de testabilité](service-fabric-testability-actions.md)

[En savoir plus sur les scénarios de test](service-fabric-testability-scenarios.md)
