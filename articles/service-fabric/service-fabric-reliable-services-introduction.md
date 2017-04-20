<properties
   pageTitle="Vue d’ensemble du modèle de programmation de Service fiable du Fabric Service | Microsoft Azure"
   description="En savoir plus sur le modèle de programmation de Service fiable du Service Fabric et commencer à écrire vos propres services."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="vturecek; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="masnider;vturecek"/>

# <a name="reliable-services-overview"></a>Vue d’ensemble des Services fiable
Azure Fabric du Service simplifie l’écriture et de gestion des Services fiables avec et sans état. Ce document traite de la :

- Le modèle de programmation des Services fiables pour les services sans et avec état.
- Les choix que vous devez effectuer lors de l’écriture d’un Service fiable.
- Des scénarios et des exemples d’utilisation des Services fiables et comment ils sont écrits.

Services fiables est un des modèles de programmation disponibles sur un Service de Fabric. Pour plus d’informations sur le modèle de programmation fiable acteurs, consultez [Introduction aux acteurs de Service Fabric fiable](service-fabric-reliable-actors-introduction.md).

Service de fabric, un service est composé de configuration, le code d’application et (éventuellement) de l’état.

Service de Fabric gère la durée de vie des services, de l’approvisionnement et le déploiement par le biais de mise à niveau et de suppression, via la [Gestion des applications Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Quels sont les Services fiables ?
Services fiables vous offre un modèle de programmation simple, puissant, de niveau supérieur pour vous aider à exprimer ce qui est important pour votre application. Avec le modèle de programmation des Services fiables, vous obtenez :

- Pour les services de l’état, le modèle de programmation des Services fiables vous permet de manière cohérente et fiable stocker votre droit de l’état à l’intérieur de votre service à l’aide de Collections fiable. Il s’agit d’un ensemble de classes de collection hautement disponible qui seront familiers à quiconque a utilisé des collections C# simple. En règle générale, les services nécessaires pour la gestion d’état fiable des systèmes externes. Avec les Collections fiable, vous pouvez stocker votre état à côté de votre ordinateur avec la même haute disponibilité et la fiabilité que vous attendez à partir des magasins externes hautement disponibles et les latence supplémentaire améliorations apportées par cohabiter sur le calcul et l’état.

- Un modèle simple pour exécuter votre propre code qui ressemble vous sont utilisés pour des modèles de programmation. Votre code comporte un point d’entrée bien définis et de la gestion du cycle de vie.

- Un modèle enfichable de communication. Utilisez le transport de votre choix, par exemple HTTP avec les [API Web](service-fabric-reliable-services-communication-webapi.md), WebSocket, les protocoles TCP personnalisés, etc.. Services fiables fournissent une grande l’emploi des options que vous pouvez utiliser, ou vous pouvez fournir votre propre.

## <a name="what-makes-reliable-services-different"></a>Ce qui rend des Services fiables différente ?
Services fiables dans un Service de Fabric est différent de services que vous avez écrit avant. Service de Fabric offre fiabilité, disponibilité, cohérence et évolutivité.  

- **Fiabilité**--votre service reste même dans des environnements peu fiables où vos ordinateurs peuvent échouer ou d’accès à des problèmes de réseau.

- **Disponibilité**, votre service sera accessible et la réactivité. (Cela ne signifie pas que vous ne pouvez pas les services qui ne peut pas être trouvés ou atteinte à partir de l’extérieur.)

- **Évolutivité**: Services sont dissociés de matériel spécifique et peuvent augmenter ou diminuer de façon nécessaire à l’ajout ou la suppression de matériel ou de ressources virtuelles. Services sont facilement partitionnées (en particulier dans le cas dynamique) pour vous assurer que les parties indépendantes du service peuvent mettre à l’échelle et répond aux échecs de façon indépendante. Enfin, Service Fabric encourage les services comme léger en autorisant des milliers de services pour être mis en service dans un processus unique, plutôt que de nécessitant ou de dédier des instances du système d’exploitation entiers à une seule instance de chaque charge de travail.

- **Cohérence**: les informations stockées dans ce service peut être garantie pour être cohérent (s’applique uniquement aux services avec état - informations supplémentaires sur cette version ultérieure)

## <a name="service-lifecycle"></a>Service de cycle de vie
Si votre service est avec état ou sans état, un cycle de vie simple qui vous permet de brancher rapidement dans votre code et de mise en route fournissent des Services fiables.  Il y a seulement une ou deux méthodes que vous devez implémenter pour obtenir votre service opérationnel.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - il s’agit d’où le service définit la pile de communication qu’il souhaite utiliser. La pile de communication, telles que les [API Web](service-fabric-reliable-services-communication-webapi.md), est ce qui définit le point de terminaison écoute ou les points de terminaison pour le service (comment les clients seront y accéder). Il définit également comment les messages qui apparaissent fin d’interagir avec le reste du code de service.

- **RunAsync** - il s’agit d’où votre service s’exécute sa logique métier. Le jeton d’annulation fourni est un signal lorsque ce travail doit s’arrêter. Par exemple, si vous avez un service qui doit extraire les messages d’une file d’attente fiable et de les traiter en permanence, voici où ce travail se produit.

### <a name="service-startup"></a>Démarrage du service

Les principaux événements du cycle de vie d’un Service fiables sont les suivants :

1. L’objet de service (la chose qui dérive le service sans état ou avec état) est construit.

2. La `CreateServiceReplicaListeners` / `CreateServiceInstanceListeners` méthode est appelée, en affectant au service une chance de retourner un ou plusieurs écouteurs de communication de son choix.
  - Notez que cela est facultatif, bien que la plupart des services expose un point de terminaison directement.

3. Une fois créés, les écouteurs de la communication, il est ouvert.
  - Écouteurs de communication ont une méthode appelée `OpenAsync()`, qui est appelée à ce stade et qui retourne l’adresse d’écoute pour le service. Si votre Service fiable utilise l’une de l’ICommunicationListeners intégré, cela est géré pour vous.

4. Une fois que l’écouteur de communication est ouvert, le `RunAsync()` méthode sur le service principal est appelé.
  - Notez que `RunAsync()` est facultatif. Si le service effectue son travail directement en réponse à des appels uniquement, il n’est pas nécessaire des mettre en œuvre `RunAsync()`.

### <a name="service-shutdown"></a>Arrêt du service

Lorsque le service est arrêté (à être supprimé, déplacé ou mis à niveau) l’ordre d’appel est mis en miroir : tout d’abord, le jeton d’annulation détenues par `RunAsync()` est annulée ; puis `CloseAsync()` est appelée sur les écouteurs de la communication.

Il existe quelques points importants à noter concernant l’arrêt des services avec état :

- Service de Fabric ne promeut pas un autre réplica de votre service à l’état principal jusqu'à ce que `CloseAsync` et `RunAsync` ont renvoyé. Si vous utilisez un écouteur de communication intégrées, les `CloseAsync` méthode est gérée pour vous.

- S’il n’existe aucune limite de temps sur le retour de ces méthodes, vous immédiatement de perdre la possibilité d’écrire à des Collections fiable et par conséquent ne peut pas y effectuer un travail réel. Il est recommandé que vous revenez aussi rapidement que possible lors de la réception de la demande d’annulation.

## <a name="example-services"></a>Services d’exemple
Sachant ce modèle de programmation, jetons un œil rapide à deux services pour voir comment ces parties s’imbriquent.

### <a name="stateless-reliable-services"></a>Services fiables sans état
Un service sans état est celui dans lequel il n’existe littéralement aucun état maintenu dans le service ou l’état qui est présente est entièrement jetable et ne nécessite pas synchronisation, de réplication, de persistance ou de haute disponibilité.

Par exemple, considérez une calculatrice qui n’a aucune mémoire et reçoit toutes les conditions et les opérations à effectuer à la fois.

Dans ce cas, le RunAsync() du service peut être vide, dans la mesure où il n’y a aucun arrière-plan-traitement des tâches que doit faire le service. Lorsque le service de calculatrice est créé, il retournera un ICommunicationListener (par exemple l' [API Web](service-fabric-reliable-services-communication-webapi.md)) qui ouvre un point de terminaison écoute sur certains ports. Ce point de terminaison écoute va se raccorder aux différentes méthodes (exemple : « Ajouter (n1, n2) ») qui définissent l’API publique de la calculatrice.

Lorsqu’un appel est effectué à partir d’un client, la méthode appropriée est appelée, et le service de calculatrice effectue les opérations sur les données fournies et retourne le résultat. Il ne stocke pas tout état.

Ne pas stocker n’importe quel état interne rend cette calculatrice exemple très simple. Mais la plupart des services ne sont pas véritablement sans état. Au lieu de cela, ils externaliser leur état à un autre magasin. (Par exemple, n’importe quelle application web qui s’appuie sur la conservation de l’état de session dans un magasin de sauvegarde ou le cache n’est pas complètement sans état.)

Un exemple courant de comment les services sans état sont utilisées en Service est considérée comme un serveur frontal qui expose le API orienté public pour une application web. Le service frontal communique ensuite avec services avec état d’une demande de l’utilisateur. Dans ce cas, les appels des clients sont dirigés vers un port connu, par exemple 80, où le service sans état est à l’écoute. Ce service sans état reçoit l’appel et détermine si l’appel provient d’une personne de confiance, comme ainsi que le service auquel il est destiné.  Ensuite, le service sans état transfère l’appel à la partition correcte du service avec état et attend une réponse. Lorsque le service sans état reçoit une réponse, il répond au client d’origine.

### <a name="stateful-reliable-services"></a>Avec état Services fiables
Un service avec état est celui qui doit avoir une partie de l’état conservé cohérente et présent pour le service de la fonction. Considérons un service qui constamment calcule une moyenne mobile d’une valeur en fonction des mises à jour qu’il reçoit. Pour ce faire, il doit avoir le jeu actuel de demandes entrantes dont il a besoin pour le processus, ainsi que la moyenne actuelle. Tout service qui récupère, traite et stocke les informations dans un magasin externe (par exemple un Azure blob ou table magasin aujourd'hui) est dynamique. Simplement, il conserve son état dans le magasin d’état externe.

La plupart des services stocker aujourd'hui leur état à l’extérieur, dans la mesure où le magasin externe est ce que fournit la fiabilité, disponibilité, l’évolutivité et la cohérence pour cet état. Service de fabric, avec état services ne sont pas requis pour stocker leur état à l’extérieur ; TISSU de service s’occupe de ces exigences pour le code de service et l’état du service.

Supposons que nous voulons écrire un service qui accepte les demandes d’une série de conversions qui doivent être effectuées sur une image et l’image qui doit être convertie.  Pour ce service, il retourne un récepteur de communication (supposons API Web) qui ouvre un port de communication et permet des soumissions via une API comme `ConvertImage(Image i, IList<Conversion> conversions)`. Dans cette API, le service pourrait prendre les informations et stocker la demande dans une file d’attente fiable et puis retourner un jeton au client afin qu’il peut effectuer le suivi de la demande (dans la mesure où les demandes peuvent prendre du temps).

Dans ce service, RunAsync peut être plus complexe. Le service peut avoir une boucle à l’intérieur de son RunAsync qui extrait les demandes de IReliableQueue, effectue les conversions répertoriées et stocke les résultats dans la IReliableDictionary, afin que lorsque le client revient, ils peuvent obtenir leurs images converties. Pour vous assurer que même en cas de problème l’image n’est perdue, ce Service fiable est extrait de la file d’attente, effectuer les conversions et stocker le résultat dans une transaction. Dans ce cas, le message n’est réellement supprimé uniquement à partir de la file d’attente et les résultats sont stockés dans le dictionnaire de résultat lorsque les conversions sont terminées. En cas de problème dans le milieu (par exemple, l’ordinateur qu'exécute sur cette instance du code), la demande reste dans la file d’attente qui attendent d’être traités à nouveau.

Une chose à noter concernant ce service est qu’il peut sembler un service .NET normal. La seule différence est que les structures de données en cours d’utilisation (IReliableQueue et IReliableDictionary) sont fournis par le Service de Fabric et sont donc apportées hautement fiable, disponible et cohérente.

## <a name="when-to-use-reliable-services-apis"></a>Quand utiliser les API des Services fiables
Si une des opérations suivantes caractérisent de service nécessaires à votre application, vous devez envisager les API des Services fiables :

- Vous avez besoin fournir le comportement de l’application sur plusieurs unités d’état (par exemple, les commandes et les éléments de ligne de commande).

- État de votre application peut être modélisée naturellement comme fiable des dictionnaires et des files d’attente.

- L’état doit être hautement disponible avec accès de faible latence.

- Votre application doit contrôler la concurrence ou la granularité d’opérations traitées sur une ou plusieurs Collections fiable.

- Vous souhaitez gérer les communications ou contrôler le schéma de partitionnement de votre service.

- Votre code a besoin d’un environnement d’exécution de threads libres.

- Votre application doit créer dynamiquement ou de détruire des dictionnaires fiables ou les files d’attente lors de l’exécution.

- Vous devez contrôler la sauvegarde fournies par le Service de Fabric et de restaurer les fonctionnalités état * de votre service par programme.

- Votre application doit mettre à jour l’historique des modifications pour ses unités d’état *.

- Vous souhaitez développer ou consommer des état de personnalisés, de développé tiers fournisseurs *.

> [AZURE.NOTE] * Fonctionnalités disponibles dès la disponibilité générale du Kit de développement logiciel.


## <a name="next-steps"></a>Étapes suivantes
+ [Démarrage rapide des Services fiable](service-fabric-reliable-services-quick-start.md)
+ [L’utilisation avancée de Services fiables](service-fabric-reliable-services-advanced-usage.md)
+ [Le modèle de programmation fiable acteurs](service-fabric-reliable-actors-introduction.md)
