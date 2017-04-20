<properties
   pageTitle="Diagnostics d’acteurs et de surveillance | Microsoft Azure"
   description="Cet article décrit les diagnostics et les performances de fonctionnalités d’analyse dans le runtime d’acteurs fiable de tissu de Service, y compris les événements et les compteurs de performance émis par celle-ci."
   services="service-fabric"
   documentationCenter=".net"
   authors="abhishekram"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="abhisram"/>

# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Tests de diagnostic et de surveillance des performances pour les acteurs fiable
Le runtime d’acteurs fiable émet [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) événements et [compteurs de performances](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Ceux-ci fournissent des informations sur le fonctionne de l’exécution et aident à l’analyse des performances et le dépannage.

## <a name="eventsource-events"></a>Événements de la source d’événement
Le nom du fournisseur de source d’événement pour le runtime d’acteurs fiable est « Microsoft-ServiceFabric-acteurs ». Événements provenant de cette source d’événement apparaissent dans la fenêtre de [Diagnostics événements](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) lorsque l’application de l’acteur est en cours [de débogage dans Visual Studio](service-fabric-debugging-your-application.md).

Exemples d’outils et de technologies qui permettent de collecte et/ou d’affichage des événements de la source d’événement sont [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [Journalisation sémantique](https://msdn.microsoft.com/library/dn774980.aspx)et la [Bibliothèque TraceEvent de Microsoft](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Mots clés
Tous les événements qui appartiennent à la source d’événement acteurs fiables sont associés à un ou plusieurs mots-clés. Cela permet de filtrer les événements collectés. Les bits de mot clé suivants sont définis.

|Bit|Description|
|---|---|
|0 x 1|Définir des événements importants qui résument le fonctionnement du runtime acteurs de Fabric.|
|0 x 2|Ensemble d’événements qui décrivent les appels de méthode d’acteur. Pour plus d’informations, consultez la [rubrique d’introduction sur les acteurs](service-fabric-reliable-actors-introduction.md#actors).|
|0 x 4|Ensemble d’événements liés à l’état de l’acteur. Pour plus d’informations, consultez la rubrique sur la [Gestion d’état acteur](service-fabric-reliable-actors-state-management.md).|
|0 x 8|Ensemble d’événements liés à tour d’accès concurrentiel dans l’acteur. Pour plus d’informations, consultez la rubrique sur la [simultanéité](service-fabric-reliable-actors-introduction.md#concurrency).|

## <a name="performance-counters"></a>Compteurs de performance
Le runtime d’acteurs fiable définit les catégories de compteurs de performances suivants.

|Catégorie|Description|
|---|---|
|Acteur de tissu de service|Compteurs spécifiques à Azure Fabric de Service acteurs, par exemple, le temps nécessaire pour enregistrer l’état de l’acteur|
|Méthode de service Fabric acteur|Compteurs spécifiques pour les méthodes implémentées par les acteurs de la Fabric du Service, par exemple la fréquence à laquelle une méthode de l’acteur est appelée|

Chacune des catégories ci-dessus a un ou plusieurs compteurs.

L’application [Analyseur de performances Windows](https://technet.microsoft.com/library/cc749249.aspx) qui est disponible par défaut dans le système d’exploitation de Windows peut être utilisée pour collecter et afficher des données de compteur de performance. [Les tests de diagnostic Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) est une autre option pour la collecte des données de compteur de performance et de chargement vers les tables Azure.

### <a name="performance-counter-instance-names"></a>Noms d’instance de compteur de performance
Un cluster qui possède un grand nombre de services d’acteur ou de partitions de service acteur aura un grand nombre d’instances de compteur de performance acteur. Les noms d’instance de compteur de performance peuvent aider à identifier la méthode de [partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) et acteur spécifique (le cas échéant) que l’instance de compteur de performance est associé.

#### <a name="service-fabric-actor-category"></a>Catégorie de service Fabric acteur
Pour la catégorie de `Service Fabric Actor`, les noms d’instance de compteur sont dans le format suivant :

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* est la représentation sous forme de chaîne de l’ID de partition de Fabric de Service qui est associé à l’instance de compteur de performance. L’ID de partition est un GUID, et sa représentation sous forme de chaîne est générée par le biais de la [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) méthode comprenant le spécificateur de format « D ».

*ActorRuntimeInternalID* est la représentation sous forme de chaîne d’un entier 64 bits qui est généré par le runtime d’acteurs de tissu pour son usage interne. Il est inclus dans le nom instance de compteur de performance pour garantir son unicité et éviter les conflits avec les autres noms d’instance des compteurs de performance. Les utilisateurs ne doivent pas tenter d’interpréter cette partie du nom d’instance du compteur performances.

Voici un exemple d’un nom d’instance de compteur pour un compteur qui appartient à la `Service Fabric Actor` catégorie :

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

Dans l’exemple ci-dessus, `2740af29-78aa-44bc-a20b-7e60fb783264` est la représentation sous forme de chaîne de l’ID de partition de Service Fabric, et `635650083799324046` est l’ID de 64 bits qui est généré pour l’exécution interne.

#### <a name="service-fabric-actor-method-category"></a>Catégorie de la méthode de service Fabric acteur
Pour la catégorie de `Service Fabric Actor Method`, les noms d’instance de compteur sont dans le format suivant :

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* est le nom de la méthode d’acteur qui est associé à l’instance de compteur de performance. Le format du nom de la méthode est déterminé selon une logique dans le runtime de Fabric acteurs qui équilibre la lisibilité du nom avec contraintes sur la longueur maximale des noms instance de compteur de performances de Windows.

*ActorsRuntimeMethodId* est la représentation sous forme de chaîne d’un entier de 32 bits qui est généré par le runtime d’acteurs de tissu pour son usage interne. Il est inclus dans le nom instance de compteur de performance pour garantir son unicité et éviter les conflits avec les autres noms d’instance des compteurs de performance. Les utilisateurs ne doivent pas tenter d’interpréter cette partie du nom d’instance du compteur performances.

*ServiceFabricPartitionID* est la représentation sous forme de chaîne de l’ID de partition de Fabric de Service qui est associé à l’instance de compteur de performance. L’ID de partition est un GUID, et sa représentation sous forme de chaîne est générée par le biais de la [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) méthode comprenant le spécificateur de format « D ».

*ActorRuntimeInternalID* est la représentation sous forme de chaîne d’un entier 64 bits qui est généré par le runtime d’acteurs de tissu pour son usage interne. Il est inclus dans le nom instance de compteur de performance pour garantir son unicité et éviter les conflits avec les autres noms d’instance des compteurs de performance. Les utilisateurs ne doivent pas tenter d’interpréter cette partie du nom d’instance du compteur performances.

Voici un exemple d’un nom d’instance de compteur pour un compteur qui appartient à la `Service Fabric Actor Method` catégorie :

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

Dans l’exemple ci-dessus, `ivoicemailboxactor.leavemessageasync` est le nom de la méthode `2` est l’ID 32 bits généré pour une utilisation interne du runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` est la représentation sous forme de chaîne de l’ID de partition de Service Fabric, et `635650083804480486` est l’ID de 64 bits généré pour interne du runtime.

## <a name="list-of-events-and-performance-counters"></a>Liste des événements et des compteurs de performance

### <a name="actor-method-events-and-performance-counters"></a>Les compteurs de performances et les événements de méthode acteur
Le runtime d’acteurs fiable émet les événements suivants liés à des [méthodes de l’acteur](service-fabric-reliable-actors-introduction.md#actors).

|Nom de l’événement|ID d’événement|Niveau|Mot clé|Description|
|---|---|---|---|---|
|ActorMethodStart|7|Commentaires|0 x 2|Acteurs runtime est sur le point d’appeler une méthode d’acteur.|
|ActorMethodStop|8|Commentaires|0 x 2|Une méthode d’acteur est terminée. Autrement dit, appel asynchrone du moment de l’exécution la méthode acteur a renvoyé et terminée de la tâche retournée par la méthode de l’acteur.|
|ActorMethodThrewException|9|Avertissement|0 x 3|Une exception a été levée pendant l’exécution d’une méthode d’acteur, soit lors de l’appel asynchrone du moment de l’exécution la méthode acteur lors de l’exécution de la tâche retournée par la méthode de l’acteur. Cet événement indique une sorte de défaillance dans le code de l’acteur qui a besoin d’enquête.|

Le runtime d’acteurs fiable publie des compteurs de performance suivants relatifs à l’exécution des méthodes de l’acteur.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Méthode de service Fabric acteur|Appels par seconde|Nombre de fois que la méthode de service acteur est appelée par seconde|
|Méthode de service Fabric acteur|Moyennes millisecondes par appel|Temps nécessaire pour exécuter la méthode de service acteur en millisecondes|
|Méthode de service Fabric acteur|Exceptions levées/Sec|Nombre de fois que l’acteur service méthode a levé une exception par seconde|

### <a name="concurrency-events-and-performance-counters"></a>Compteurs de performances et les événements d’accès concurrentiel
Le runtime d’acteurs fiable émet les événements suivants liés à [l’accès concurrentiel](service-fabric-reliable-actors-introduction.md#concurrency).

|Nom de l’événement|ID d’événement|Niveau|Mot clé|Description|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Commentaires|0 x 8|Cet événement est enregistré au début de chaque nouveau tour d’un acteur. Il contient le nombre d’appels d’acteur qui attendent d’acquérir le verrou par-acteur qui applique tour la concurrence d’accès en attente.|

Le runtime d’acteurs fiable publie des compteurs de performance suivants liés à l’accès concurrentiel.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Acteur de tissu de service|# des appels d’acteur en attente de verrouillage de l’acteur|Nombre d’appels d’acteur en attente d’acquérir le verrou par-acteur qui met en œuvre la concurrence tour en attente|
|Acteur de tissu de service|Attendent de millisecondes moyens par verrou|Durée (en millisecondes) d’acquérir le verrou par-acteur qui met en œuvre la simultanéité reposant sur la tour|
|Acteur de tissu de service|Verrou du acteur de millisecondes moyen|Durée (en millisecondes) pour lequel le par-acteur verrouillage|

### <a name="actor-state-management-events-and-performance-counters"></a>Les compteurs de performances et les événements de gestion d’état acteur
Le runtime d’acteurs fiable émet les événements suivants liés à la [Gestion d’état acteur](service-fabric-reliable-actors-state-management.md).

|Nom de l’événement|ID d’événement|Niveau|Mot clé|Description|
|---|---|---|---|---|
|ActorSaveStateStart|10|Commentaires|0 x 4|Acteurs runtime est sur le point d’enregistrer l’état de l’acteur.|
|ActorSaveStateStop|11|Commentaires|0 x 4|Acteurs runtime a terminé l’enregistrement de l’état de l’acteur.|

Le runtime d’acteurs fiable publie des compteurs de performance suivants liés à la gestion d’état acteur.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Acteur de tissu de service|Millisecondes moyens par l’opération de l’état de sauvegarde|Temps nécessaire pour enregistrer l’état de l’acteur en millisecondes|
|Acteur de tissu de service|Millisecondes moyens par action de l’état de charge|Temps nécessaire pour charger l’état d’acteur en millisecondes|

### <a name="events-related-to-actor-replicas"></a>Événements liés aux réplicas de l’acteur
Le runtime d’acteurs fiable émet les événements suivants liés aux [réplicas de l’acteur](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Nom de l’événement|ID d’événement|Niveau|Mot clé|Description|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|D’information|0 x 1|Réplica de l’acteur devient rôle principal. Cela implique que les acteurs pour cette partition seront créées à l’intérieur de ce réplica.|
|ReplicaChangeRoleFromPrimary|2|D’information|0 x 1|Réplica de l’acteur devient rôle non primaire. Cela implique que les acteurs pour cette partition seront créées n’est plus à l’intérieur de ce réplica. Aucune nouvelle demande ne sera remis aux acteurs déjà créés dans ce réplica. Les acteurs doit être détruits après avoir effectué toutes les demandes en cours.|

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Les événements d’activation et de désactivation Actor et compteurs de performances
Le runtime d’acteurs fiable émet les événements suivants liés à la [désactivation et l’activation de l’acteur](service-fabric-reliable-actors-lifecycle.md).

|Nom de l’événement|ID d’événement|Niveau|Mot clé|Description|
|---|---|---|---|---|
|ActorActivated|5|D’information|0 x 1|Un acteur a été activé.|
|ActorDeactivated|6|D’information|0 x 1|Un acteur a été désactivé.|

Le runtime d’acteurs fiable publie des compteurs de performance suivants liés à la désactivation et l’activation de l’acteur.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Acteur de tissu de service|Nombre moyen de millisecondes de l’OnActivateAsync|Temps nécessaire pour exécuter la méthode de OnActivateAsync en millisecondes|

### <a name="actor-request-processing-performance-counters"></a>Compteurs de performance de traitement de la requête acteur
Lorsqu’un client appelle une méthode via un objet de proxy acteur, il entraîne un message de demande qui est envoyé sur le réseau pour le service de l’acteur. Le service traite le message de demande et envoie une réponse au client. Le runtime d’acteurs fiable publie des compteurs de performance suivants liés au traitement des demandes acteur.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Acteur de tissu de service|# des demandes en attente|Nombre de demandes en cours de traitement dans le service|
|Acteur de tissu de service|Millisecondes moyens par requête|Temps (en millisecondes) par le service de traitement d’une demande|
|Acteur de tissu de service|Moyennes millisecondes pour la désérialisation de demande|Durée (en millisecondes) pour désérialiser le message de demande d’acteur lorsqu’il est reçu par le service|
|Acteur de tissu de service|Millisecondes moyens pour la sérialisation de la réponse|Durée (en millisecondes) pour sérialiser le message de réponse d’acteur au niveau du service avant que la réponse est envoyée au client|

## <a name="next-steps"></a>Étapes suivantes
 - [Comment acteurs fiables utiliser le Service Fabric](service-fabric-reliable-actors-platform.md)
 - [Documentation de référence API d’acteur](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemples de code](https://github.com/Azure/servicefabric-samples)
