<properties 
    pageTitle="Comment faire pour résoudre les problèmes de Cache de Redis Azure | Microsoft Azure" 
    description="Découvrez comment résoudre les problèmes courants liés au Cache de Redis d’Azure." 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-troubleshoot-azure-redis-cache"></a>Comment faire pour résoudre les problèmes de Cache de Redis Azure

Cet article fournit des conseils pour le dépannage des catégories suivantes de problèmes de Cache de Redis d’Azure.

-   [Résolution des problèmes de côté client](#client-side-troubleshooting) - cette section fournit des instructions sur l’identification et la résolution des problèmes provoqués par l’application qui se connecte au Cache de Redis d’Azure.
-   [Résolution des problèmes du côté serveur](#server-side-troubleshooting) - cette section fournit des instructions sur l’identification et la résolution des problèmes causés sur le côté serveur de Cache de Redis d’Azure.
-   [Exceptions de délai d’attente de StackExchange.Redis](#stackexchangeredis-timeout-exceptions) - cette section fournit des informations sur le dépannage de problèmes lors de l’utilisation du client StackExchange.Redis.


>[AZURE.NOTE] Plusieurs étapes de dépannage dans ce guide incluent des instructions pour exécuter les commandes Redis et surveiller diverses métriques de performances. Pour plus d’informations et des instructions, consultez les articles correspondants dans la section [informations supplémentaires](#additional-information) .

## <a name="client-side-troubleshooting"></a>Résolution des problèmes du côté client


Cette section traite de la résolution des problèmes qui se produisent en raison d’une condition sur l’application cliente.

-   [Pression de mémoire sur le client](#memory-pressure-on-the-client)
-   [Rafales du trafic](#burst-of-traffic)
-   [Client élevée de l’UC](#high-client-cpu-usage)
-   [Bande passante du côté client dépassée](#client-side-bandwidth-exceeded)
-   [Taille de demande/réponse volumineuse](#large-requestresponse-size)
-   [Que sont devenus mes données dans Redis ?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Pression de mémoire sur le client

#### <a name="problem"></a>Problème

La pression de mémoire sur l’ordinateur client conduit à toutes sortes de problèmes de performances qui peuvent retarder le traitement de données qui a été envoyés par l’instance Redis sans délai. Lorsque la pression de mémoire est atteint, le système a généralement pour les données de la page de la mémoire physique de la mémoire virtuelle sur disque. Cette *page est défaillant* entraîne le système ralentit considérablement.

#### <a name="measurement"></a>Mesure 

1.  Surveiller l’utilisation de la mémoire sur l’ordinateur pour vous assurer qu’elle ne dépasse pas de mémoire disponible. 
2.  Moniteur de la `Page Faults/Sec` compteur de performance. La plupart des systèmes s’ont des défauts de page matériels même pendant le fonctionnement normal, observez les pics de ce compteur de performance de défauts de page qui correspondent à des délais d’attente.

#### <a name="resolution"></a>Résolution

Mise à niveau de votre client pour un client de plus grand taille de mémoire virtuelle avec plus de mémoire ou Approfondissez vos modèles d’utilisation mémoire afin de réduire la mémoire consuption.


### <a name="burst-of-traffic"></a>Rafales du trafic

#### <a name="problem"></a>Problème

Pics de trafic combiné avec une mauvaise `ThreadPool` paramètres peuvent entraîner des retards dans le traitement des données déjà envoyées par le serveur Redis, mais non encore consommé du côté client.

#### <a name="measurement"></a>Mesure 

Moniteur comment votre `ThreadPool` statistiques de changent avec le temps à l’aide de code [comme suit](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Vous pouvez également consulter le `TimeoutException` message de StackExchange.Redis. Voici un exemple :

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Dans le message ci-dessus, il existe plusieurs problèmes qui sont intéressants :

 1. Notez que dans la `IOCP` section et la `WORKER` section que vous avez une `Busy` valeur qui est supérieure à la `Min` valeur. Cela signifie que votre `ThreadPool` besoin de paramètres de réglage.
 2. Vous pouvez également voir `in: 64221`. Cela indique que 64211 octets ont été reçus au niveau de la couche de socket du noyau, mais n’ont pas été lues par l’application (par exemple, StackExchange.Redis). En général, cela signifie que votre application n’est pas lire des données à partir du réseau, de façon aussi rapidement que le serveur est envoyer à vous.

#### <a name="resolution"></a>Résolution

Configurer vos [Paramètres de pool de threads](https://gist.github.com/JonCole/e65411214030f0d823cb) pour vous assurer que votre pool de threads sera rapidement évoluer selon des scénarios de transmission en rafale.


### <a name="high-client-cpu-usage"></a>Client élevée de l’UC

#### <a name="problem"></a>Problème

Utilisation élevée de l’UC sur le client est une indication que le système ne peut effectuer le travail qu’il a été demandé d’effectuer. Cela signifie que le client ne peut pas traiter une réponse Redis en temps voulu, même si Redis a envoyé la réponse très rapidement.

#### <a name="measurement"></a>Mesure

Surveillez l’utilisation de l’UC de grand système par le biais du portail Azure ou le compteur de performance associé. Veillez à ne pas surveiller les *processus* UC car un processus unique peut avoir de faible utilisation de l’UC en même temps que le système global du processeur peut être élevé. Regardez des pics dans l’utilisation de l’UC qui correspondent aux délais d’attente. Par processeur élevé, vous pouvez également voir haute `in: XXX` les valeurs dans les `TimeoutException` comme décrit dans la section des [rafales du trafic](#burst-of-traffic) des messages d’erreur.

>[AZURE.NOTE] StackExchange.Redis 1.1.603 et versions ultérieures incluent le `local-cpu` métrique de `TimeoutException` messages d’erreur. Assurez-vous que vous utilisez la version la plus récente du [package NuGet de StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Il y a corrigés en permanence en cours dans le code afin de rendre celui-ci plus fiable pour les délais d’attente est essentiel de disposer de la version la plus récente.

#### <a name="resolution"></a>Résolution

Mise à niveau vers une plus grande taille de mémoire virtuelle avec plus de capacité de processeur ou de rechercher la cause de l’UC. 



### <a name="client-side-bandwidth-exceeded"></a>Bande passante du côté client dépassée

#### <a name="problem"></a>Problème

Les ordinateurs clients de taille différentes ont des limites sur la bande passante du réseau qu’ils sont disponibles. Si le client dépasse la bande passante disponible, puis données ne seront pas traitées sur le côté client aussi rapidement que le serveur expéditeur. Cela peut entraîner des délais d’attente.

#### <a name="measurement"></a>Mesure

Surveiller l’évolution de l’utilisation de la bande passante au fil du temps à l’aide de code [comme suit](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Notez que ce code peut s’exécutent pas correctement dans certains environnements avec des autorisations restreintes (par exemple, les sites web Azure).

#### <a name="resolution"></a>Résolution 

Augmenter la taille de mémoire virtuelle du Client ou de réduire la consommation de bande passante réseau.


### <a name="large-requestresponse-size"></a>Taille de demande/réponse volumineuse

#### <a name="problem"></a>Problème

Une demande/réponse volumineuse peut entraîner des délais d’attente. Par exemple, supposons que la valeur de délai d’expiration configurée sur votre client est de 1 seconde. Votre application demande deux clés (par exemple, 'A' et 'B') en même temps (à l’aide de la même connexion réseau physique). La plupart des clients prennent en charge les « Pipelining » de requêtes, telles que les deux demandes de 'A' et 'B' sont envoyés sur le réseau au serveur un après l’autre sans attendre les réponses. Le serveur envoie les réponses dans le même ordre. Si la réponse « A » est grande suffisamment il peut manger la plupart d’expiration pour les demandes suivantes. 

L’exemple suivant illustre ce scénario. Dans ce scénario, demande 'A' et 'B' sont envoyées rapidement, le serveur commence à envoyer des réponses 'A' et 'B' rapidement, mais en raison du temps de transfert de données, 'B' coincé derrière l’autre demande et expire même si le serveur a répondu rapidement.

  	|-------- 1 Second Timeout (A)----------|
  	|-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Mesure

Il est difficile à mesurer. En fait, vous devez instrumenter votre code client pour effectuer le suivi des réponses et les grandes requêtes. 

#### <a name="resolution"></a>Résolution

1.  Redis est optimisé pour un grand nombre de petites valeurs, plutôt que de quelques valeurs de grande taille. Il est préférable de décomposer vos données connexes avec des valeurs inférieures. Consultez le [Quelle est la plage de taille de valeur idéale pour redis ? Est de 100 Ko trop grande ?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) POST pour plus d’informations sur pourquoi des valeurs inférieures sont recommandées.
2.  Augmenter la taille de votre machine virtuelle (pour le client et le serveur de Cache Redis), pour obtenir des capacités de bande passante supérieures, réduisant le temps de transfert de données pour les réponses plus grandes. Notez que l’obtention de plus de bande passante sur uniquement sur le serveur ou uniquement sur le client peuvent ne pas suffire. Mesurer l’utilisation de la bande passante et comparer les capacités de la taille de la machine virtuelle que vous avez actuellement.
3.  Augmenter le nombre de `ConnectionMultiplexer` objets utilisation et demandes de répétition alternée sur des connexions différentes.


### <a name="what-happened-to-my-data-in-redis"></a>Que sont devenus mes données dans Redis ?

#### <a name="problem"></a>Problème

Prévu pour certaines données dans mon instance de Cache de Redis d’Azure, mais il ne semble pas être il.

##### <a name="resolution"></a>Résolution

Consultez [que sont devenus mes données dans Redis ?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) pour des causes et résolutions possibles.


## <a name="server-side-troubleshooting"></a>Résolution des problèmes du côté serveur

Cette section traite de la résolution des problèmes qui se produisent en raison d’une condition sur le serveur de cache.

-   [Pression de mémoire sur le serveur](#memory-pressure-on-the-server)
-   [Utilisation élevée du processeur / de la charge du serveur](#high-cpu-usage-server-load)
-   [Bande passante du côté serveur dépassée](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Pression de mémoire sur le serveur

#### <a name="problem"></a>Problème

La pression de mémoire sur le serveur conduit à toutes sortes de problèmes de performances qui peuvent retarder le traitement des demandes. Lorsque la pression de mémoire est atteint, le système a généralement pour les données de la page de la mémoire physique de la mémoire virtuelle sur disque. Cette *page est défaillant* entraîne le système ralentit considérablement. Il existe plusieurs causes possibles de cette sollicitation de la mémoire : 

1.  Vous avez rempli le cache à pleine capacité de données. 
2.  Redis voit la fragmentation de la mémoire haute - plus souvent provoquée par le stockage des objets de grande taille (Redis est optimisé pour un petits objets - consultez la [Quelle est la plage de taille de valeur idéale pour redis ? Est de 100 Ko trop grande ?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) POST pour plus de détails). 

#### <a name="measurement"></a>Mesure

Redis expose deux mesures qui peuvent vous aider à identifier ce problème. La première est `used_memory` et l’autre est `used_memory_rss`. [Ces mesures](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) sont disponibles dans le portail Azure ou par le biais de la commande [Redis les informations](http://redis.io/commands/info) .

#### <a name="resolution"></a>Résolution

Il existe plusieurs modifications possibles que vous pouvez apporter pour l’aider à garder de l’utilisation de la mémoire :

1. [Configurer une stratégie de mémoire](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) et de définir des délais d’expiration sur vos clés. Notez que cela peut ne pas suffire si vous disposez de la fragmentation.
2. [Configurer une valeur maxmemory réservés](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) qui est assez grand pour compenser la fragmentation de mémoire.
3. Diviser vos objets mis en cache volumineux dans le plus petit des objets connexes.
4. [Échelle](cache-how-to-scale.md) à un cache de taille supérieure.
5. Si vous utilisez un [cache premium avec cluster Redis activée](cache-how-to-premium-clustering.md) , vous pouvez [augmenter le nombre de milieu des fragments](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Utilisation élevée du processeur / de la charge du serveur

#### <a name="problem"></a>Problème

Utilisation élevée du processeur peut signifier que le côté client peut échouer traiter une réponse Redis en temps voulu, même si Redis a envoyé la réponse très rapidement.

#### <a name="measurement"></a>Mesure

Surveillez l’utilisation de l’UC de grand système par le biais du portail Azure ou le compteur de performance associé. Veillez à ne pas surveiller les *processus* UC car un processus unique peut avoir de faible utilisation de l’UC en même temps que le système global du processeur peut être élevé. Regardez des pics dans l’utilisation de l’UC qui correspondent aux délais d’attente.

#### <a name="resolution"></a>Résolution

L' [échelle](cache-how-to-scale.md) à la taille du cache niveau capacité processeur ou examiner la cause de l’UC. 

### <a name="server-side-bandwidth-exceeded"></a>Bande passante du côté serveur dépassée

#### <a name="problem"></a>Problème

Instances de cache de taille différentes ont des limites sur la bande passante du réseau qu’ils sont disponibles. Si le serveur est supérieure à la bande passante disponible, données non envoyées au client plus rapidement. Cela peut entraîner des délais d’attente.

#### <a name="measurement"></a>Mesure

Vous pouvez surveiller le `Cache Read` métrique, c'est-à-dire la quantité de données lues à partir du cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de rapport spécifiée. Cette valeur correspond à la bande passante utilisée par ce cache. Si vous souhaitez définir les alertes pour les limites de la bande passante du réseau côté serveur, vous pouvez créer à l’aide de cette `Cache Read` compteur. Comparer vos lectures avec les valeurs de [ce tableau](cache-faq.md#cache-performance) pour les limites de la bande passante observée pour cache de différentes tailles et des niveaux de tarification.

#### <a name="resolution"></a>Résolution

Si vous êtes toujours près de la bande passante maximale observée pour la taille de cache et de couche tarification, envisagez [mise à l’échelle](cache-how-to-scale.md) d’un niveau de tarification ou une taille disposant d’une plus grande bande passante du réseau, en utilisant les valeurs de [ce tableau](cache-faq.md#cache-performance) comme guide.


## <a name="stackexchangeredis-timeout-exceptions"></a>Exceptions de délai d’attente de StackExchange.Redis

StackExchange.Redis utilise le paramètre nommé de configuration `synctimeout` pour les opérations synchrones qui a une valeur par défaut de 1 000 ms. Si un appel synchrone ne termine pas dans le délai imparti, le client StackExchange.Redis lève une erreur de temporisation similaire à l’exemple suivant.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Ce message d’erreur contient des mesures qui permettent de vous indiquer la cause et résoudre le problème. Le tableau suivant contient des détails sur les mesures de message d’erreur.

| Métrique de message d’erreur | Détails                                                                                                                                                                                                                                          |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| inst       | Dans la dernière tranche de temps : 0 commandes ont été délivrés.                                                                                                                                                                                              |
| Mgr        | Exécute le Gestionnaire du socket `socket.select` qui signifie qu’il demande au système d’exploitation pour indiquer un socket qui a quelque chose à faire ; essentiel : le lecteur pas activement lit à partir du réseau, car il ne pense pas que quelque chose à faire est |
| file d’attente      | Il y a 73 total des opérations en cours                                                                                                                                                                                                        |
| qu         | 6 des opérations en cours dans la file d’attente en attente et n’ont pas encore été écrites sur le réseau sortant                                                                                                                                                           |
| QS         | 67 des opérations en cours de he ont été envoyées au serveur, mais une réponse n’est pas encore disponible. La réponse peut être `Not yet sent by the server` ou`sent by the server but not yet processed by the client.`                                                   |
| QC         | 0 des opérations en cours ont vu des réponses mais n’ont pas encore été marquée comme étant terminée en raison de l’attente sur la boucle d’exécution                                                                                                                                      |
| wR         | Il existe un writer actif (ce qui signifie que les demandes en attente 6 ne sont pas ignorés) octets/activewriters                                                                                                                                                   |
| dans         | Il n’y a aucun lecteur active et zéro octets sont disponibles pour la lecture de la carte d’interface réseau octets/activereaders                                                                                                                                               |


### <a name="steps-to-investigate"></a>Étapes à suivre pour étudier

1. Comme meilleure pratique Assurez-vous que vous utilisez le modèle suivant pour vous connecter lorsque vous utilisez le client StackExchange.Redis.


        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


    Pour plus d’informations, voir [se connecter au cache à l’aide de StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

2. Assurez-vous que votre Cache de Redis Azure et l’application cliente sont dans la même région dans Azure. Par exemple, vous pouvez obtenir des délais d’attente lorsque votre cache américains Extrême-Orient mais le client est en ouest des États-Unis et ne complète pas la demande dans le `synctimeout` intervalle, ou vous pouvez obtenir des délais d’attente lors du débogage de votre ordinateur de développement local. 

    Il est vivement recommandé de disposer du cache et dans le client dans la même région d’Azure. Si vous avez un scénario qui inclut la région entre les appels, vous devez définir le `synctimeout` l’intervalle à une valeur supérieure à l’intervalle de 1 000 ms par défaut en incluant une `synctimeout` propriété dans la chaîne de connexion. L’exemple suivant montre un extrait de chaîne de connexion de cache StackExchange.Redis avec un `synctimeout` de Microsoft 2000.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...

3. Assurez-vous que vous utilisez la version la plus récente du [package NuGet de StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Il y a corrigés en permanence en cours dans le code afin de rendre celui-ci plus fiable pour les délais d’attente est essentiel de disposer de la version la plus récente.

4. S’il existe des demandes de mise en route liés par les limitations de bande passante sur le serveur ou le client, il prendra plus de temps pour se terminer et de provoquer ainsi les délais d’attente. Pour voir si votre délai est due à la bande passante du réseau sur le serveur, consultez [la bande passante du côté serveur dépassée](#server-side-bandwidth-exceeded). Pour voir si votre délai est due à la bande passante du réseau client, voir [la bande passante du côté Client dépassée](#client-side-bandwidth-exceeded).

6. Vous lors de l’obtention du processeur liés sur le serveur ou sur le client ?
    -   Vérifiez si vous l’obtention liés par UC sur votre client, qui peut provoquer la demande ne pas être traitées au sein du `synctimeout` intervalle, ce qui provoque un dépassement de délai. Déplacement vers une plus grande taille de client ou de la distribution de la charge peut contribuer à ce contrôle. 
    -   Vérifier si vous obtenez des UC lié sur le serveur en surveillant le `CPU` [métrique de performances du cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). En tant que Redis est lié au processeur de requêtes peuvent provoquer ces demandes au délai d’attente. Pour contourner ce problème, vous pouvez distribuer la charge sur plusieurs milieu des fragments dans un cache de prime ou mise à niveau vers un niveau de tarification ou de plus grande taille. Pour plus d’informations, reportez-vous à la section [Dépassement de bande passante côté serveur](#server-side-bandwidth-exceeded).

7. Existe-t-il des commandes prenant beaucoup de temps à traiter sur le serveur ? Commandes qui prennent beaucoup de temps à traiter sur le serveur redis de longue peut provoquer des délais d’attente. Voici quelques exemples de commandes longues `mget` avec un grand nombre de clés, `keys *` ou mal écrit les scripts lua. Vous pouvez vous connecter à votre instance de Cache Redis d’Azure en utilisant le client redis-cli ou utiliser la [Console de Redis](cache-configure.md#redis-console) et exécutez la commande [SlowLog](http://redis.io/commands/slowlog) pour voir s’il existe des demandes prend plus de temps que prévu. Serveur de redis et StackExchange.Redis sont optimisés de nombreuses petites requêtes plutôt que moins de demandes volumineux. Répartir les données en segments plus petits peut améliorer les choses ici. 

    Pour plus d’informations sur la connexion au point de terminaison SSL en Cache Azure Redis à l’aide de la cli-redis et stunnel, consultez le blog de [l’Annonce de la sortie ASP.NET Session fournisseur d’état de Redis version d’évaluation](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) . Pour plus d’informations, consultez [SlowLog](http://redis.io/commands/slowlog).

8. Une charge serveur Redis élevée peut provoquer des délais d’attente. Vous pouvez surveiller la charge du serveur en surveillant le `Redis Server Load` [métrique de performances du cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Une charge serveur de 100 (valeur maximale) signifie que le serveur redis a été occupé, avec aucun temps d’inactivité, de traitement des demandes. Pour voir si certaines requêtes prennent tous la capacité du serveur, exécutez la commande SlowLog, comme décrit dans le paragraphe précédent. Pour plus d’informations, reportez-vous à la section [utilisation de l’UC élevée / chargement du serveur](#high-cpu-usage-server-load).

9. Existait ou tout autre événement côté client qui pourraient avoir causé un spots de réseau ? Si un événement à l’échelle le nombre d’instances du client vers le haut ou vers le bas, ou déploiement d’une nouvelle version du client ou à l’échelle automatique est activé, vérifiez sur le client (web, rôle de travail ou un VM Iaas) ? Dans nos tests, de que nous avons constaté qu’autoscale ou mise à l’échelle de haut/bas risque connectivité réseau sortant peut être perdue pendant plusieurs secondes. Code de StackExchange.Redis résiste à ces événements et se reconnectera. Pendant ce temps de reconnexion des demandes dans la file d’attente peuvent expirer.

10. Existait une demande importante précédant de plusieurs demandes de petite taille au Cache Redis qui a expiré ? Le paramètre `qs` dans l’erreur du message vous indique le nombre de requêtes ont été envoyé du client au serveur, mais n’avez pas encore traité une réponse. Cette valeur peut ne cessent d’augmenter, car StackExchange.Redis utilise une seule connexion TCP et peut lire uniquement une réponse à la fois. Même si la première opération a expiré, il n’empêche pas les données envoyées vers/depuis le serveur, et autres demandes sont bloqués jusqu'à ce qu’il est terminé, à l’origine de délais d’attente. Une solution consiste à réduire les risques de délais d’attente en garantissant que votre cache est assez grand pour votre charge de travail et en fractionnant les grandes valeurs en segments plus petits. Une autre solution possible est d’utiliser un pool de `ConnectionMultiplexer` objets de votre client, puis choisissez le moins chargé `ConnectionMultiplexer` lors de l’envoi d’une nouvelle demande. Ceci ne devrait empêcher un seul délai de provoquer également un délai d’attente d’autres requêtes.

11. Si vous utilisez `RedisSessionStateprovider`, assurez-vous que vous avez correctement défini le délai d’attente de nouvelle tentative. `retrytimeoutInMilliseconds`doit être supérieur à `operationTimeoutinMilliseonds`, sinon aucune nouvelle tentative n’aura lieu. Dans l’exemple suivant `retrytimeoutInMilliseconds` est définie sur 3000. Pour plus d’informations, consultez [Fournisseur d’état de Session ASP.NET pour le Cache de Redis Azure](cache-aspnet-session-state-provider.md) et [comment utiliser les paramètres de configuration du fournisseur d’état de Session et le fournisseur de Cache de sortie](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).


    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


12. Vérifier l’utilisation de la mémoire sur le serveur de Cache de Redis Azure en [surveillant](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` et `Used Memory`. Si une stratégie d’éviction est en place, Redis démarre la suppression de clés lorsque `Used_Memory` atteint la taille du cache. Dans l’idéal, `Used Memory RSS` doit uniquement être légèrement supérieure à `Used memory`. Une différence importante signifie la fragmentation de mémoire (interne ou externe. Lors de la `Used Memory RSS` est inférieure à `Used Memory`, cela signifie que la partie de la mémoire cache a été remplacé par le système d’exploitation. Si cela se produit, vous pouvez prévoir des latences importantes. Car Redis n’a pas de contrôle sur la correspondance entre ses allocations de pages de mémoire hautes `Used Memory RSS` est souvent le résultat d’un pic d’utilisation de la mémoire. Lorsque Redis libère de la mémoire, la mémoire est envoyée à l’allocateur et l’allocateur ou n’accordent pas la mémoire revenir au système. Il peut y avoir une différence entre les `Used Memory` la consommation de mémoire et de la valeur comme indiqué par le système d’exploitation. Il peut être fait de la mémoire a été utilisé et publié par Redis, mais pas donné au système. Pour atténuer les problèmes de mémoire, vous pouvez effectuer les étapes suivantes.
    -   Mettre à jour le cache à une taille plus grande afin que vous n’exécutez pas contre les limitations de mémoire sur le système.
    -   Définir des délais d’expiration dans les clés afin que les anciennes valeurs sont éliminées de manière proactive.
    -   Moniteur de la la `used_memory_rss` cache métrique. Lorsque cette valeur est proche de la taille de leur cache, vous devez commencer à voir les problèmes de performances. Répartir les données sur plusieurs milieu des fragments si vous utilisez un cache premium, ou mise à niveau vers un cache de taille supérieure.

    Pour plus d’informations, reportez-vous à [La pression de mémoire sur le serveur](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informations supplémentaires

-   [Offre de Redis de Cache et de la taille dois-je utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
-   [Comment puis-je évaluer et tester les performances de mon cache ?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Comment puis-je exécuter des commandes de Redis ?](cache-faq.md#how-can-i-run-redis-commands)
-   [Comment faire pour surveiller le Cache Redis d’Azure](cache-how-to-monitor.md)