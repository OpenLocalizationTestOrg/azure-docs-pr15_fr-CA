<properties
   pageTitle="L’utilisation avancée de Services fiables | Microsoft Azure"
   description="Obtenir des informations sur une utilisation avancée du Service Fabric des services fiables pour plus de souplesse dans vos services."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>L’utilisation avancée de la modèle de programmation des Services fiables
Azure Fabric du Service simplifie l’écriture et de gestion des services fiables avec et sans état. Ce guide traite des utilisations avancées de Services fiables pour obtenir plus de souplesse et de contrôle sur vos services. Avant de lire ce guide, familiarisez-vous avec [Le modèle de programmation des Services fiables](service-fabric-reliable-services-introduction.md).

Avec ou sans état de services ont deux points d’entrée principal pour le code utilisateur :

 - `RunAsync`est un point d’entrée à usage général pour votre code de service.
 - `CreateServiceReplicaListeners`et `CreateServiceInstanceListeners` est pour l’ouverture des écouteurs de la communication des demandes du client.
 
Ces points de deux entrée suffisent à la plupart des services. Dans de rares cas, lorsque plus de contrôle sur le cycle de vie d’un service est requis, les événements de cycle de vie supplémentaires sont disponibles.

## <a name="stateless-service-instance-lifecycle"></a>Cycle de vie de service sans état instance

Cycle de vie d’un service sans état est très simple. Un service sans état peut uniquement être ouvert, fermé ou abandonné. `RunAsync`dans un service sans état est exécuté lorsqu’une instance de service est ouvert et annulée lorsqu’une instance de service est fermée ou annulée. 

Bien que `RunAsync` devrait suffire dans presque tous les cas, l’ouvrir, fermer et abandonner les événements de service sans état sont également disponibles :

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
  OnOpenAsync est appelé lorsque l’instance de service sans état est prêt à être utilisé. Tâches d’initialisation de service étendu peuvent être démarrées à cette heure.

- `Task OnCloseAsync(CancellationToken)`
  OnCloseAsync est appelé lorsque l’instance de service sans état va être arrêté de manière gracieuse. Cela peut se produire lorsque du code est en cours de mise à niveau, l’instance de service est déplacée en raison de l’équilibrage de la charge ou détection d’une erreur transitoire. OnCloseAsync peut être utilisé pour fermer toutes les ressources, arrêter tout traitement en arrière-plan, terminer l’enregistrement de l’état externe ou fermer les connexions existantes en toute sécurité.

- `void OnAbort()`
  OnAbort est appelé lorsque l’instance de service sans état est forcé arrêté. Ceci est généralement appelé lors de la détection d’une erreur permanente sur le nœud, ou lorsque le Service Fabric ne peut pas gérer fiable de cycle de vie de l’instance de service en raison d’une défaillance interne.

## <a name="stateful-service-replica-lifecycle"></a>Cycle de vie de réplica avec état de service

Cycle de vie d’un réplica de service avec état est beaucoup plus complexe qu’une instance de service sans état. En outre pour ouvrir, fermer et annuler des événements, un réplica avec état service subit des modifications de rôle au cours de sa durée de vie. Lorsqu’un réplica avec état service change de rôle, la `OnChangeRoleAsync` est déclenché :

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
  OnChangeRoleAsync est appelé lorsque le réplica avec état de service est la modification de rôle, par exemple principal ou secondaire. Réplicas primaires figurent l’état écriture (sont autorisés à créer et écrire dans des Collections fiable). Les réplicas secondaires sont donnés état de lecture (peut uniquement lire à partir de Collections fiable existantes). La plupart du travail dans un service d’état est effectué sur le réplica principal. Les réplicas secondaires peuvent effectuer la validation en lecture seule, de génération de rapports, de l’exploration de données ou d’autres tâches en lecture seule.

Dans un service dynamique, seul le réplica principal a un accès en écriture à l’état et est donc en général que le service effectue le travail réel. Le `RunAsync` méthode dans un service dynamique est exécuté uniquement lorsque le réplica avec état de service est le principal. Le `RunAsync` méthode est annulée lorsque les rôles d’un réplica principal change à partir de la primaire, ainsi que pendant les événements de fermer et d’abandon. 

À l’aide de la `OnChangeRoleAsync` événement vous permet d’effectuer le travail selon le rôle de réplica ainsi que dans la réponse à la modification du rôle.

Un service dynamique offre également les mêmes événements de cycle de vie des quatre sous la forme d’un service sans état, avec la même sémantique et les cas d’utilisation :

- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`



## <a name="next-steps"></a>Étapes suivantes
Pour des rubriques plus avancées liées au Service de Fabric, consultez les articles suivants :

- [Configuration dynamique des Services fiables](service-fabric-reliable-services-configuration.md)

- [Introduction de health service Fabric](service-fabric-health-introduction.md)

- [À l’aide de rapports d’intégrité système pour la résolution des problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Configuration des Services avec le Gestionnaire de ressources Cluster Service Fabric](service-fabric-cluster-resource-manager-configure-services.md)
