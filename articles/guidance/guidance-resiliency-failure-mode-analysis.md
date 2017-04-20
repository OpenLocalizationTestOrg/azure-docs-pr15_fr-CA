<properties
   pageTitle="Analyse de l’échec en mode | Microsoft Azure"
   description="Instructions pour effectuer une analyse de mode de défaillance pour les solutions en nuage basées sur Azure."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="mwasson"/>

# <a name="azure-resiliency-guidance-failure-mode-analysis"></a>Conseils de résilience Azure : analyse de mode de défaillance

Analyse du mode de défaillance (FMA) est un processus pour la création de résilience dans un système, en identifiant les points de défaillance possibles dans le système. Le FMA doit faire partie des phases de conception et l’architecture, afin que vous puissiez générer des récupération après une panne dans le système à partir du début.

Voici le processus général d’effectuer un FMA : 

1. Identifier tous les composants dans le système. Inclure les dépendances externes, telles que comme fournisseurs d’identité, les services tiers et ainsi de suite.   

2. Pour chaque composant, identifier les défaillances potentielles qui peuvent se produire. Un seul composant peut avoir plusieurs modes de défaillance. Par exemple, vous devez prendre en compte les échecs de lecture et échecs d’écriture séparément, car les atténuations d’impact et possible sera différentes.

3. Taux de chaque mode de défaillance en fonction de son niveau de risque global. Tenez compte des facteurs suivants :  

    - Quelle est la probabilité de l’échec. Il est relativement courant ? Très rares ? Vous n’avez pas besoin des chiffres exacts ; l’objectif est d’aider le rang de la priorité.

    - Quel est l’impact sur l’application, en termes de disponibilité, la perte de données, coût et interruption de service ? 

4. Pour chaque mode de défaillance, déterminer comment l’application répond et récupérer. Prendre en compte les compromis en termes de coût et de l’application de complexité.   

Comme point de départ pour votre processus FMA, cet article contient un catalogue des modes de défaillance potentiels et leurs mesures d’atténuation. Le catalogue est organisé par la technologie ou service Azure, ainsi qu’une catégorie générale pour la conception d’au niveau de l’application. Le catalogue n’est pas exhaustif, mais elle couvre la plupart des principales Azure services. 


## <a name="app-service"></a>Service d’application

### <a name="app-service-app-shuts-down"></a>Application de Service de l’application s’arrête.

**Détection**. Causes possibles :

- Arrêt planifié
    
    - Un opérateur d’arrêt de l’application ; par exemple, en utilisant le portail Azure.

    - L’application a été déchargée car il était inactif. (Uniquement si la `Always On` paramètre est désactivé.)

- Arrêt inattendu

    - L’application se bloque.

    - Une instance de l’application Service VM n’est plus disponible.


L’enregistrement Application_End intercepte la fermeture du domaine d’application (incident de processus souple) et est le seul moyen pour intercepter les arrêts de domaine d’application.

**Récupération**

- Si l’arrêt était attendu, permet d’événement d’arrêt de l’application s’arrête. Par exemple, dans ASP.NET, utilisez le `Application_End` méthode.

- Si l’application a été déchargée lorsqu’il est inactif, il est automatiquement redémarré sur la demande suivante. Toutefois, vous subirez la « à froid » de coût. 

- Pour empêcher l’application d’être déchargée lorsqu’il est inactif, activez la `Always On` dans l’application web. Reportez-vous à la section [Configuration des applications web dans le Service d’application Azure][app-service-configure].

- Pour empêcher un opérateur d’arrêter l’application, un verrou de ressource avec la valeur `ReadOnly` niveau. Consultez les [ressources avec le Gestionnaire de ressources Azure][rm-locks].

- En cas de panne de l’application ou une application Service VM n’est pas disponible, application Service redémarre automatiquement l’application. 


**Tests de diagnostic**. Journaux d’application et les journaux de serveur web. Reportez-vous à la section [Activer l’enregistrement des diagnostics pour les applications web dans le Service d’application Azure][app-service-logging].


### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>Un utilisateur particulier permet de requêtes incorrectes à plusieurs reprises ou surcharge du système. 

**Détection**. Authentifier les utilisateurs et comporter un ID d’utilisateur dans le journal applications.

**Récupération**

- Utiliser la [Gestion des API Azure] [ api-management] aux demandes de limitation de l’utilisateur. Reportez-vous à la section [limitation avec Azure API de gestion de demande avancée][api-management-throttling]

- Bloquer l’utilisateur.

**Tests de diagnostic**. Journal de toutes les demandes d’authentification.


### <a name="a-bad-update-was-deployed"></a>Un échec de la mise à jour a été déployée.

**Détection**. Surveiller l’état de l’application via le portail Azure (voir les [performances des applications web moniteur Azure][app-insights-web-apps]) ou implémenter le [modèle de contrôle de point de terminaison santé][health-endpoint-monitoring-pattern].

**Récupération**. Utiliser plusieurs [emplacements de déploiement] [ app-service-slots] et de restaurer le déploiement de la dernière bonne. Pour plus d’informations, consultez [architecture de référence d’application web de base][ra-web-apps-basic].


## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="openid-connect-oidc-authentication-fails"></a>Se connecter à OpenID (OIDC) l’authentification échoue.

**Détection**. Modes de défaillance possibles sont les suivantes :

1. Annonce Azure n’est pas disponible ou ne peut pas être atteint à cause d’un problème de réseau. La redirection vers le point de terminaison de l’authentification échoue et le middleware OIDC lève une exception.
2. Les clients Active Directory Azure n’existent pas. La redirection vers le point de terminaison d’authentification renvoie un code d’erreur HTTP, et le middleware OIDC lève une exception.
3. Ne peut pas authentifier les utilisateurs. Aucune stratégie de détection n’est nécessaire ; Annonce Azure gère les échecs de connexion.

**Récupération**

1. Intercepter des exceptions non gérées dans le middleware.
2. Gérer les `AuthenticationFailed` les événements.
3. Rediriger l’utilisateur vers une page d’erreur.
4. Tentatives de l’utilisateur.


## <a name="azure-search"></a>Recherche Azure

### <a name="writing-data-to-azure-search-fails"></a>Échec de l’écriture de données à la recherche d’Azure.

**Détection**. Catch `Microsoft.Rest.Azure.CloudException` erreurs.

**Récupération**

Le [Kit de développement .NET recherche] [ search-sdk] automatiquement les tentatives après erreurs passagères. Toutes les exceptions levées par le Kit de développement logiciel du client doivent être traitées comme des erreurs de non transitoire.

La stratégie de nouvelles tentatives par défaut utilise le recul exponentielle. Pour utiliser une stratégie différente de nouvelle tentative, appelez `SetRetryPolicy` sur la `SearchIndexClient` ou `SearchServiceClient` classe. Pour plus d’informations, reportez-vous à la section [Tentatives automatiques][auto-rest-client-retry].

**Tests de diagnostic**. Utiliser la [recherche le trafic Analytique][search-analytics].


### <a name="reading-data-from-azure-search-fails"></a>Échec de la lecture des données de recherche d’Azure.

**Détection**. Catch `Microsoft.Rest.Azure.CloudException` erreurs.

**Récupération**

Le [Kit de développement .NET recherche] [ search-sdk] automatiquement les tentatives après erreurs passagères. Toutes les exceptions levées par le Kit de développement logiciel du client doivent être traitées comme des erreurs de non transitoire.

La stratégie de nouvelles tentatives par défaut utilise le recul exponentielle. Pour utiliser une stratégie différente de nouvelle tentative, appelez `SetRetryPolicy` sur la `SearchIndexClient` ou `SearchServiceClient` classe. Pour plus d’informations, reportez-vous à la section [Tentatives automatiques][auto-rest-client-retry].

**Tests de diagnostic**. Utiliser la [recherche le trafic Analytique][search-analytics].



## <a name="cassandra"></a>Cassandra


### <a name="reading-or-writing-to-a-node-fails"></a>Échec de lecture ou d’écriture à un nœud.

**Détection**. Intercepter l’exception. Pour les clients .NET, il s’agira généralement `System.Web.HttpException`. Autre client peut avoir d’autres types d’exceptions.  Pour plus d’informations, consultez [Gestion des erreurs de Cassandra fait à droite](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right).

**Récupération**

- Chaque [client de Cassandra](https://wiki.apache.org/cassandra/ClientOptions) a ses propres stratégies de nouvelle tentative et ses capacités. Pour plus d’informations, consultez [Gestion des erreurs de Cassandra bien][cassandra-error-handling].
- Utilisez un déploiement compatibles rack, avec des nœuds de données distribuées sur les domaines d’erreur.
- Déploiement dans plusieurs régions avec cohérence de quorum local. Si une panne non transitoires, basculer dans une autre région.

**Tests de diagnostic**. Journaux d’application


## <a name="cloud-service"></a>Service de cloud

### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>Les rôles Web ou travailleur sont arrêtés de façon inattendue.

**Détection**. La [RoleEnvironment.Stopping] [ RoleEnvironment.Stopping] événement est déclenché.

**Récupération**. Remplacer le [RoleEntryPoint.OnStop] de[ RoleEntryPoint.OnStop] méthode pour nettoyer correctement les. Pour plus d’informations, consultez [La droite permet de gérer les événements d’Azure OnStop] [ onstop-events] (blog).


## <a name="documentdb"></a>DocumentDB

### <a name="reading-data-from-documentdb-fails"></a>Échec de la lecture des données à partir de DocumentDB.

**Détection**. Catch `System.Net.Http.HttpRequestException` ou `Microsoft.Azure.Documents.DocumentClientException`. 

**Récupération**

- Le Kit de développement logiciel tente automatiquement de renvoyer les tentatives ayant échouées. Pour définir le nombre de tentatives et le délai d’attente maximal, configurer `ConnectionPolicy.RetryOptions`. Le client déclenche les exceptions sont au-delà de la stratégie de nouvelle tentative ou ne sont pas des erreurs transitoires. 

- Si le service DocumentDB limite le client, il retourne une erreur HTTP 429. Vérifiez le code d’état les `DocumentClientException`. Si vous obtenez l’erreur 429 régulièrement, envisagez d’augmenter la valeur de débit de la collection DocumentDB.

- Répliquer la base de données DocumentDB sur deux ou plusieurs régions. Tous les réplicas sont lisibles. À l’aide du SDK de client, spécifiez le `PreferredLocations` paramètre. Il s’agit d’une liste ordonnée des régions Azure. Vous recevrez toutes les lectures à la première zone disponible dans la liste. Si la requête échoue, le client essaiera les autres régions dans la liste, dans l’ordre. Pour plus d’informations, consultez [développement avec les comptes de plusieurs régions DocumentDB][docdb-multi-region].

**Tests de diagnostic**. Consigner toutes les erreurs sur le client.


### <a name="writing-data-to-documentdb-fails"></a>Écriture de données sur DocumentDB échoue.

**Détection**. Catch `System.Net.Http.HttpRequestException` ou `Microsoft.Azure.Documents.DocumentClientException`. 

**Récupération**

- Le Kit de développement logiciel tente automatiquement de renvoyer les tentatives ayant échouées. Pour définir le nombre de tentatives et le délai d’attente maximal, configurer `ConnectionPolicy.RetryOptions`. Le client déclenche les exceptions sont au-delà de la stratégie de nouvelle tentative ou ne sont pas des erreurs transitoires. 

- Si le service DocumentDB limite le client, il retourne une erreur HTTP 429. Vérifiez le code d’état les `DocumentClientException`. Si vous obtenez l’erreur 429 régulièrement, envisagez d’augmenter la valeur de débit de la collection DocumentDB.

- Répliquer la base de données DocumentDB sur deux ou plusieurs régions. Si la région principale tombe en panne, un autre région sera promue pour écrire. Vous pouvez également déclencher un basculement manuellement. Le Kit de développement logiciel effectue la découverte automatique et routage, afin que le code de l’application continue de fonctionner après un basculement. Au cours de la période de basculement (minutes), les opérations d’écriture aura une latence plus élevée, comme le Kit de développement logiciel détecte la nouvelle zone d’écriture. Pour plus d’informations, consultez [développement avec les comptes de plusieurs régions DocumentDB][docdb-multi-region].

- Comme un secours, conserver le document dans une file d’attente de sauvegarde et traiter la file d’attente ultérieurement.

**Tests de diagnostic**. Consigner toutes les erreurs sur le client.


## <a name="elasticsearch"></a>Elasticsearch

### <a name="reading-data-from-elasticsearch-fails"></a>Échec de la lecture des données à partir de Elasticsearch.

**Détection**. Intercepter l’exception appropriée pour le particulier [client de Elasticsearch] [ elasticsearch-client] en cours d’utilisation. 

**Récupération**

- Utiliser un mécanisme de nouvelle tentative. Chaque client a ses propres stratégies de nouvelle tentative. 

- Déployer plusieurs nœuds de Elasticsearch et d’utiliser la réplication pour une haute disponibilité.

Pour plus d’informations, consultez [Exécution de Elasticsearch sur Azure][elasticsearch-azure].

**Tests de diagnostic**. Vous pouvez utiliser les outils de surveillance pour Elasticsearch ou consigner toutes les erreurs côté client avec la charge utile. Reportez-vous à la section « Analyse » dans [l’Exécution de Elasticsearch sur Azure][elasticsearch-azure].


### <a name="writing-data-to-elasticsearch-fails"></a>Écriture de données sur Elasticsearch échoue.

**Détection**. Intercepter l’exception appropriée pour le particulier [client de Elasticsearch] [ elasticsearch-client] en cours d’utilisation.  

**Récupération**

- Utiliser un mécanisme de nouvelle tentative. Chaque client a ses propres stratégies de nouvelle tentative. 
 
- Si l’application peut tolérer un niveau de cohérence réduite, envisagez d’écriture avec `write_consistency` définition de `quorum`.

Pour plus d’informations, consultez [Exécution de Elasticsearch sur Azure][elasticsearch-azure].

**Tests de diagnostic**. Vous pouvez utiliser les outils de surveillance pour Elasticsearch ou consigner toutes les erreurs côté client avec la charge utile. Reportez-vous à la section « Analyse » dans [l’Exécution de Elasticsearch sur Azure][elasticsearch-azure].


## <a name="queue-storage"></a>Stockage de la file d’attente

### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>Écriture d’un message à la file d’attente d’Azure stockage échoue régulièrement.

**Détection**. Après *N* tentatives, l’opération d’écriture échoue.

**Récupération**

- Stocker les données dans un cache local et transfère les écritures vers un stockage plus tard, lorsque le service est disponible.
- Créer une file d’attente secondaire et écrire dans cette file d’attente si la file d’attente principale n’est pas disponible.

**Tests de diagnostic**. Utiliser les [mesures de stockage][storage-metrics].


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>L’application ne peut pas traiter un message particulier à partir de la file d’attente. 

**Détection**. Spécifique à l’application. Par exemple, le message contient des données non valides, ou la logique métier échoue pour une raison quelconque. 

**Récupération**

Déplacer le message vers une file d’attente distincte. Exécuter un processus distinct pour examiner les messages dans cette file d’attente.

Prendre en compte à l’aide de files d’attente de messagerie de Bus de Service Azure, qui fournit une [file d’attente de lettres mortes] [ sb-dead-letter-queue] fonctionnalité à cet effet.

Remarque : Si vous utilisez des files d’attente de stockage avec WebJobs, le SDK WebJobs fournit la gestion de messages poison intégrés. Apprendre [à utiliser le stockage Azure de file d’attente avec le Kit de développement de WebJobs][sb-poison-message].

**Tests de diagnostic**. Utiliser la journalisation de l’application.


## <a name="redis-cache"></a>Redis de Cache

### <a name="reading-from-the-cache-fails"></a>Échec de la lecture à partir du cache.

**Détection**. Catch `StackExchange.Redis.RedisConnectionException`.

**Récupération**

1. Retenter au point de défaillance transitoire. Azure Redis cache prend en charge intégrée de relance par le biais de voir les [instructions de nouvelle tentative de Redis de Cache][redis-retry].
2. Traiter les défaillances non transitoires comme une absence de cache et revenir à la source de données d’origine.

**Tests de diagnostic**. Utilisez les [diagnostics du Cache de Redis][redis-monitor].


### <a name="writing-to-the-cache-fails"></a>Échec de l’écriture dans le cache.

**Détection**. Catch `StackExchange.Redis.RedisConnectionException`.

**Récupération**

1. Retenter au point de défaillance transitoire. Azure Redis cache prend en charge intégrée de relance par le biais de voir les [instructions de nouvelle tentative de Redis de Cache][redis-retry].
2. Si l’erreur est non transitoires, l’ignorer et laisser les autres transactions à écrire ultérieurement dans le cache.

**Tests de diagnostic**. Utilisez les [diagnostics du Cache de Redis][redis-monitor].


## <a name="sql-database"></a>Base de données SQL

### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>Impossible de se connecter à la base de données dans la région principale.

**Détection**. Échec de la connexion.

**Récupération**

Au préalable : La base de données doit être configuré pour la réplication de zone géographique active. Consultez [SQL de base de données géo-réplication]Active[sql-db-replication].

- Pour les requêtes, lire à partir d’un réplica secondaire. 
- Pour les insertions et les mises à jour, basculez manuellement sur un réplica secondaire. Consultez [initier un basculement planifié ou de base de données de SQL Azure][sql-db-failover].

Le réplica utilise une chaîne de connexion différente, vous devez mettre à jour la chaîne de connexion dans votre application.


### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>Client manque de connexions dans le pool de connexions.

**Détection**. Catch `System.InvalidOperationException` erreurs. 

**Récupération**

- Recommencez l’opération.
- Sous la forme d’un plan d’atténuation, isoler les pools de connexion pour chaque cas d’usage, afin qu’un cas d’utilisation ne peut pas monopoliser toutes les connexions.
- Augmenter les pools de connexion maximale.

**Tests de diagnostic**. Journaux de l’applications.


### <a name="database-connection-limit-is-reached"></a>Limite de connexion de base de données est atteinte. 

**Détection**. Une base de données SQL Azure limite le nombre des travailleurs simultanées, des connexions et des sessions. Les limites dépendent de la couche de service. Pour plus d’informations, reportez-vous à la section [limites de ressource de base de données de SQL Azure][sql-db-limits].

Pour détecter ces erreurs, intercepter `System.Data.SqlClient.SqlException` et vérifiez la valeur de `SqlException.Number` pour le code d’erreur SQL. Pour obtenir la liste des codes d’erreur appropriés, voir [des codes d’erreur SQL pour les applications de client de base de données SQL : base de données d’erreur de connexion et d’autres problèmes][sql-db-errors].

**Récupération**. Ces erreurs sont considérées comme étant transitoires, afin que la nouvelle tentative peut résoudre le problème. Si vous avez atteint systématiquement ces erreurs, envisagez la mise à l’échelle de la base de données.

**Tests de diagnostic**. - [Sys.event_log] [ sys.event_log] requête retourne les connexions de base de données réussies, les échecs de connexion et les interblocages.

- Créer une [règle d’alerte] [ azure-alerts] pour les échecs de connexions.

- Activer [l’audit de base de données de SQL] [ sql-db-audit] et vérifier les échecs de connexion.


## <a name="service-bus-messaging"></a>Service de messagerie de Bus

### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>Échec de la lecture d’un message à partir d’une file d’attente de Bus de Service.

**Détection**. Intercepter des exceptions à partir du Kit de développement logiciel du client. La classe de base pour les exceptions de Bus de Service est [MessagingException][sb-messagingexception-class]. Si l’erreur est transitoire, le `IsTransient` propriété a la valeur true. 

Pour plus d’informations, voir [Bus de Service d’échanges exceptions][sb-messaging-exceptions].

**Récupération**

1. Retenter au point de défaillance transitoire. Voir [Bus de Service Réessayer les instructions][sb-retry].

2. Les messages qui ne peuvent pas être remis à tout receiver sont placés dans une *file d’attente de lettres mortes*. Cette file d’attente permet de voir les messages qui n’ont pas peuvent être reçues. Il n’y a aucun nettoyage automatique de la file d’attente de lettres mortes. Messages y restent jusqu'à ce que vous les récupérez explicitement. Voir [files d’attente de lettres mortes de vue d’ensemble du Service de Bus][sb-dead-letter-queue].


### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>Écrit un message dans un Bus de Service la file d’attente échoue.

**Détection**. Intercepter des exceptions à partir du Kit de développement logiciel du client. La classe de base pour les exceptions de Bus de Service est [MessagingException][sb-messagingexception-class]. Si l’erreur est transitoire, le `IsTransient` propriété a la valeur true. 

Pour plus d’informations, voir [Bus de Service d’échanges exceptions][sb-messaging-exceptions].

**Récupération**

1. Le client Service Bus tente automatiquement de renvoyer après erreurs transitoires. Par défaut, il utilise le recul exponentielle. Après le nombre maximal de nouvelles tentatives ou le délai d’attente maximal, le client lève une exception. Pour plus d’informations, voir [Bus de Service Réessayer les instructions][sb-retry].

2. Si le quota de file d’attente est dépassé, le client lève [QuotaExceededException][QuotaExceededException]. Le message d’exception donne plus de détails. Décharger certains messages de la file d’attente avant une nouvelle tentative et envisagez d’utiliser le modèle de disjoncteur pour éviter les tentatives continues alors que le quota est dépassé. Assurez-vous également que la propriété [BrokeredMessage.TimeToLive] n’est pas définie trop élevée. 

3. Dans une région, la résilience peut être améliorée à l’aide [des files d’attente partitionnées ou rubriques][sb-partition]. Un non partitionné une file d’attente ou une rubrique est affecté à une banque d’informations. Si cette banque d’informations n’est pas disponible, toutes les opérations sur cette file d’attente ou la rubrique échouera. Une rubrique ou une file d’attente partitionnée est partitionnée sur plusieurs banques de messagerie. 

4. Pour plus de fiabilité, créez deux espaces de noms de Bus de Service dans différentes régions et répliquer les messages. Vous pouvez utiliser soit une réplication active ou passive.

    - Réplication Active : le client envoie tous les messages à des files d’attente. Le récepteur est à l’écoute sur des files d’attente. Messages avec un identificateur unique, la balise afin que le client peut rejeter les messages en double.

    - Réplication passive : le client envoie le message à une file d’attente. Si une erreur se produit, le client se replie sur la file d’attente. Le récepteur est à l’écoute sur des files d’attente. Cette approche réduit le nombre de doublons de messages qui sont envoyés. Toutefois, le récepteur doit encore gérer des messages en double.

    Pour plus d’informations, consultez [exemple de GeoReplication] [ sb-georeplication-sample] et les [meilleures pratiques pour l’isolation des applications contre les interruptions de Service Bus et les sinistres][sb-outages].


### <a name="duplicate-message"></a>Dupliquer le message.

**Détection**. Examiner la `MessageId` et `DeliveryCount` propriétés du message.

**Récupération**

- Si possible, concevez votre message des opérations de transformation pour être équipotentes. Dans le cas contraire, stocke un ID de message de messages qui sont déjà traitées et vérifier le code avant de traiter un message.

- Activer la détection des doublons, en créant la file d’attente avec `RequiresDuplicateDetection` la valeur true. Avec ce paramètre, les Bus de Service supprime automatiquement tout message qui est envoyé avec le même `MessageId` sous la forme d’un message précédent.  Notez les points suivants :

    -  Ce paramètre empêche la placées dans la file d’attente des messages en double. Il n’empêche pas un récepteur de traitement de plusieurs fois le même message.

    - Détection des doublons a un laps de temps. Si un doublon est envoyé au-delà de cette fenêtre, ne pas être détecté.

**Tests de diagnostic**. Journal des messages en double.


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>L’application ne peut pas traiter un message particulier à partir de la file d’attente. 

**Détection**. Spécifique à l’application. Par exemple, le message contient des données non valides, ou la logique métier échoue pour une raison quelconque. 

**Récupération**

Il existe deux modes d’échec à prendre en compte. 

- Le récepteur détecte la panne. Dans ce cas, déplacer le message vers la file d’attente de lettres mortes. Ultérieurement, exécuter un processus distinct pour examiner les messages dans la file d’attente de lettres mortes.

- Le récepteur échoue au cours du traitement du message &mdash; , par exemple, en raison d’une exception non gérée. Pour gérer ce cas, utilisez `PeekLock` mode. Dans ce mode, si le verrou expire, le message devient disponible pour les autres destinataires. Si le message dépasse le nombre maximal de remise ou la durée de vie, le message est automatiquement déplacé vers la file d’attente de lettres mortes.

Pour plus d’informations, consultez [vue d’ensemble du Service de Bus files d’attente de lettres mortes][sb-dead-letter-queue].

**Tests de diagnostic**. Chaque fois que l’application déplace un message dans la file d’attente de lettres mortes, écrire un événement dans les journaux de l’application.


## <a name="service-fabric"></a>TISSU de service

### <a name="a-request-to-a-service-fails"></a>Une demande à un service tombe en panne.

**Détection**. Le service retourne une erreur.

**Récupération**

- Recherchez à nouveau les proxy (`ServiceProxy` ou `ActorProxy`) et appeler de nouveau la méthode de service/acteur. 

- **Service dynamique**. Encapsuler des opérations sur les collections fiables dans une transaction. Si une erreur se produit, la transaction sera annulée. La demande, si extraites d’une file d’attente, est traitée de nouveau. 
 
- **Service sans état**. Si le service conserve les données dans un magasin externe, toutes les opérations doivent être équipotentes.


**Tests de diagnostic**. Journal de l’application

### <a name="service-fabric-node-is-shut-down"></a>Nœud de service Fabric est arrêté.

**Détection**. Un jeton d’annulation est passé au service `RunAsync` méthode. Service de Fabric annule la tâche avant l’arrêt du nœud.

**Récupération**. Utiliser le jeton d’annulation à la détection d’un arrêt. Lorsque le Service Fabric demande l’annulation, fin de travail et quitter `RunAsync` aussi rapidement que possible.

**Tests de diagnostic**. Journaux d’application


## <a name="storage"></a>Stockage

### <a name="writing-data-to-azure-storage-fails"></a>Échec de l’écriture de données dans le stockage Azure

**Détection**. Le client reçoit des erreurs lors de l’écriture.

**Récupération**

1. Recommencez l’opération, pour récupérer des défaillances temporaires. [Réessayer la stratégie] de la[ Storage.RetryPolicies] le client SDK gère cette valeur automatiquement.
2. Implémentez le modèle de disjoncteur pour éviter le stockage écrasante.
3. Si N de tentatives échoue, effectuer un secours progressif. Par exemple :

    - Stocker les données dans un cache local et transfère les écritures vers un stockage plus tard, lorsque le service est disponible.
    - Si l’action d’écriture était dans une étendue transactionnelle, compenser la transaction.

**Tests de diagnostic**. Utiliser les [mesures de stockage][storage-metrics].


### <a name="reading-data-from-azure-storage-fails"></a>Échec de la lecture des données de stockage Azure.

**Détection**. Le client reçoit des erreurs lors de la lecture.

**Récupération**

1. Recommencez l’opération, pour récupérer des défaillances temporaires. [Réessayer la stratégie] de la[ Storage.RetryPolicies] le client SDK gère cette valeur automatiquement.
2. Pour le stockage de RA-GRS, en cas d’échec de la lecture à partir de la primaire, essayez de lire à partir de l’extrémité secondaire. Le Kit de développement logiciel du client peut gérer cela automatiquement. Voir [réplication de stockage Azure][storage-replication].
3. Si *N* de tentatives échoue, entreprendre une action de secours à se dégrader progressivement. Par exemple, si une image du produit ne peut être récupérée à partir du stockage, afficher une image d’espace réservé générique.

**Tests de diagnostic**. Utiliser les [mesures de stockage][storage-metrics].


## <a name="virtual-machine"></a>Machine virtuelle

### <a name="connection-to-a-backend-vm-fails"></a>Échec de la connexion à un ordinateur virtuel de back-end.

**Détection**. Erreurs de connexion réseau.

**Récupération**

- Déployer des ordinateurs virtuels de back-end au moins deux dans un jeu de disponibilité, derrière un équilibreur de charge.

- Si l’erreur de connexion est transitoire, parfois TCP va correctement réessayer d’envoyer le message. 

- Implémenter une stratégie de nouvelle tentative dans l’application. 

- Pour les erreurs persistantes ou non transitoires, implémenter le [disjoncteur] [ circuit-breaker] modèle.

- Si la machine virtuelle appel dépasse sa limite de sortie de réseau, la file d’attente sortante rempliront. Si la file d’attente sortante est constamment saturée, envisagez l’évolution horizontale. 

**Tests de diagnostic**. Journal des événements au niveau des limites de service.

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>Instance de la machine virtuelle devenue indisponible ou incorrecte.

**Détection**. Configurer un équilibreur de charge [sonde de santé] [ lb-probe] qui indique si l’instance de la machine virtuelle est en bon état. La sonde doit vérifier si les fonctions essentielles sont répond correctement. 

**Récupération**. Pour chaque couche application, placer plusieurs instances de la machine virtuelle dans le même jeu de disponibilité et placer un équilibreur de charge devant les ordinateurs virtuels. En cas d’échec de la sonde de santé, l’équilibreur de charge cesse d’envoyer de nouvelles connexions à l’instance incorrecte.

**Tests de diagnostic**. -Utiliser l’équilibreur de charge [analytique du journal][lb-monitor].
- Configurer votre système de surveillance pour surveiller tous les points de terminaison de contrôle d’état.


### <a name="operator-accidentally-shuts-down-a-vm"></a>Opérateur arrête accidentellement une machine virtuelle.

**Détection**. N/A

**Récupération**. Un verrou de ressource avec la valeur `ReadOnly` niveau. Consultez les [ressources avec le Gestionnaire de ressources Azure][rm-locks].

**Tests de diagnostic**. Utiliser les [journaux d’activité Azure][azure-activity-logs].


## <a name="webjobs"></a>WebJobs

### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>Travail continu s’arrête lorsque l’hôte SCM est inactif.

**Détection**. Passer un jeton d’annulation à la fonction WebJob. Pour plus d’informations, voir [arrêt progressif][web-jobs-shutdown]. 

**Récupération**. Activer la `Always On` dans l’application web. Pour plus d’informations, consultez [tâches d’arrière-plan de s’exécuter avec WebJobs][web-jobs].


## <a name="application-design"></a>Conception de l’application

### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>Application ne peut pas gérer un pic dans les demandes entrantes.

**Détection**. Dépend de l’application. Symptômes courants :

- Le site Web commence à retourner des codes d’erreur HTTP 5xx.
- Les services dépendants, tels que la base de données ou de stockage, démarrent limiter les requêtes. Recherchez les erreurs HTTP, tels que HTTP 429 (trop nombreuses demandes), en fonction du service.
- Longueur de file d’attente HTTP augmente.

**Récupération**

- Évoluer pour gérer l’augmentation de la charge. 

- Minimiser les échecs pour éviter d’avoir les pannes en cascade à perturber l’application entière. Stratégies d’atténuation sont les suivantes :

    - Implémenter le [Modèle de limitation] [ throttling-pattern] afin d’éviter les systèmes back-end écrasante.
    - Utilisez [l’audit de charge basé sur la file d’attente] [ queue-based-load-leveling] de demandes de mémoire tampon et de les traiter à un rythme approprié.
    - Classer par priorité de certains clients. Par exemple, si l’application a niveaux gratuites et payantes, limiter les clients sur la couche libre, mais les clients non payés. Voir [modèle de file d’attente de priorité][priority-queue-pattern].

**Tests de diagnostic**. Utiliser cette [Application de Service de diagnostic][app-service-logging]. Utiliser un service comme [Azure journal Analytique][azure-log-analytics], [Perspectives d’Application][app-insights], ou [Nouvelle Relic] [ new-relic] pour l’aider à comprendre les journaux de diagnostic.


### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>Échec d’une des opérations dans un workflow ou d’une transaction distribuée.

**Détection**. Après *N* tentatives, elle échoue toujours.

**Récupération**

- Sous la forme d’un plan d’atténuation, implémenter le [Planificateur Agent superviseur] [ scheduler-agent-supervisor] modèle pour gérer l’ensemble du flux. 
- Ne pas réessayer sur les délais d’attente. Il existe un taux de réussite de faible pour cette erreur. 
- Travail de la file d’attente, afin de renouveler la tentative ultérieurement.

**Tests de diagnostic**. Consigner toutes les opérations (réussies et échouées), y compris les actions de compensation. Utilisez les ID, de corrélation afin que vous pouvez effectuer le suivi de toutes les opérations dans la même transaction.


### <a name="a-call-to-a-remote-service-fails"></a>Un appel à un service distant échoue.

**Détection**. Code d’erreur HTTP.

**Récupération**

1. Retenter au point de défaillance transitoire. 
2. Si l’appel échoue après *N* tentatives, effectuer une action de secours. (Spécifique à l’application.)
3. Implémenter le [modèle de disjoncteur] [ circuit-breaker] pour éviter les pannes en cascade. 

**Tests de diagnostic**. Connecter tous les échecs d’appel à distance.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le processus FMA, consultez [résilience par la conception des services en nuage] [ resilience-by-design-pdf] (téléchargement PDF).

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: ../api-management/api-management-sample-flexible-throttling.md
[app-insights]: ../application-insights/app-insights-overview.md
[app-insights-web-apps]: ../application-insights/app-insights-azure-web-apps.md
[app-service-configure]: ../app-service-web/web-sites-configure.md
[app-service-logging]: ../app-service-web/web-sites-enable-diagnostic-log.md
[app-service-slots]: ../app-service-web/web-sites-staged-publishing.md
[auto-rest-client-retry]: https://github.com/Azure/autorest/blob/master/Documentation/clients-retry.md
[azure-activity-logs]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[azure-alerts]: ../monitoring-and-diagnostics/insights-alerts-portal.md
[azure-log-analytics]: ../log-analytics/log-analytics-overview.md
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[docdb-multi-region]: ../documentdb/documentdb-developing-with-multiple-regions.md
[elasticsearch-azure]: guidance-elasticsearch-running-on-azure.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: ../load-balancer/load-balancer-monitor-log.md
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: guidance-web-apps-basic.md
[redis-monitor]: ../redis-cache/cache-how-to-monitor.md
[redis-retry]: ../best-practices-retry-service-specific.md#cache-redis-retry-guidelines
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: ../resource-group-lock-resources.md
[sb-dead-letter-queue]: ../service-bus-messaging/service-bus-dead-letter-queues.md
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: ../service-bus-messaging/service-bus-messaging-exceptions.md
[sb-outages]: ../service-bus/service-bus-outages-disasters.md#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: ../service-bus-messaging/service-bus-partitioning.md
[sb-poison-message]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#poison
[sb-retry]: ../best-practices-retry-service-specific.md#service-bus-retry-guidelines
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: ../search/search-traffic-analytics.md
[sql-db-audit]: ../sql-database/sql-database-auditing-get-started.md
[sql-db-errors]: ../sql-database/sql-database-develop-error-messages.md#resource-governanance-errors
[sql-db-failover]: ../sql-database/sql-database-geo-replication-failover-portal.md
[sql-db-limits]: ../sql-database/sql-database-resource-limits.md
[sql-db-replication]: ../sql-database/sql-database-geo-replication-overview.md
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: ../storage/storage-redundancy.md
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: ../app-service-web/web-sites-create-web-jobs.md
[web-jobs-shutdown]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful

