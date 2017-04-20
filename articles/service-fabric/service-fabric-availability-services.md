<properties
   pageTitle="Disponibilité des services de Fabric de Service | Microsoft Azure"
   description="Décrit la détection de pannes, le basculement et la reprise des services"
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

# <a name="availability-of-service-fabric-services"></a>Disponibilité des services de Fabric de Service
Les services de Fabric de Service Azure peuvent être avec ou sans. Cet article donne une vue d’ensemble de la Fabric du Service gère la disponibilité d’un service en cas de défaillance.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilité des services de Fabric de Service sans état
Un service sans état est un service d’application qui n’a pas un [état permanent local](service-fabric-concepts-state.md).

Création d’un service sans état requiert la définition d’un nombre d’instances, qui est le nombre d’instances de service sans état qui doit être en cours d’exécution dans le cluster. Il s’agit du nombre de copies de la logique d’application qui sera instanciée dans le cluster. L’augmentation du nombre d’instances est la méthode recommandée de mise à l’échelle d’un service sans état.

Lorsqu’une erreur est détectée sur n’importe quelle instance d’un service sans état, une nouvelle instance est créée sur un autre nœud éligible dans le cluster.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilité des services de Fabric de Service avec état
Un service dynamique possède un état qui lui est associé. Service de fabric, un service dynamique est modélisé sous la forme d’un jeu de réplicas. Chaque réplique est une instance du code du service qui possède une copie de l’état. Les opérations de lecture et d’écriture sont effectuées dans un réplica (appelé principal). Les modifications apportées à l’état d’opérations d’écriture sont *répliquées* sur plusieurs autres réplicas (appelés serveurs secondaires actives). La combinaison de principales et actives des réplicas secondaires est le jeu de réplicas du service.

Il peut y avoir qu’un seul réplica principal service de lire et d’écrire des requêtes, mais il peut y avoir plusieurs réplicas secondaires actives. Le nombre de répliques de secondaires actives est configurable, et un plus grand nombre de réplicas peut tolérer un plus grand nombre de logiciels et des défaillances matérielles.

Dans le cas d’une erreur (lorsque le réplica principal tombe en panne), Service de Fabric rend un réplica secondaire active le nouveau réplica principal. Ce réplica secondaire actif possède déjà la version mise à jour de l’état (par *réplication*), et peut poursuivre le traitement de lecture supplémentaire et des opérations d’écriture.

Ce concept--d’un réplica à un principal ou actif secondaire--est connu en tant que le rôle de réplica.

### <a name="replica-roles"></a>Rôles de réplica
Le rôle d’un réplica est utilisé pour gérer le cycle de vie de l’état géré par ce réplica. Un réplica dont le rôle est principaux services les demandes de lecture. Il traite également requêtes d’écriture par son état de mise à jour et la réplication des modifications vers les secondaires actives dans son jeu de réplicas. Le rôle d’un secondaire active doit recevoir des modifications de l’état qui le réplica principal a répliqué et mettre à jour son affichage de l’état.

>[AZURE.NOTE] Modèles de programmation de haut niveau telles que le [framework d’acteurs fiable](service-fabric-reliable-actors-introduction.md) abstraction le concept de rôle de réplica du développeur.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concepts de Service Fabric, consultez les rubriques suivantes :

- [Évolutivité des services de Fabric de Service](service-fabric-concepts-scalability.md)

- [Partitionnement des services de Fabric de Service](service-fabric-concepts-partitioning.md)

- [Définition et gestion de l’état](service-fabric-concepts-state.md)
