<properties 
    pageTitle="Comment faire pour surveiller le Cache de Redis Azure | Microsoft Azure" 
    description="Apprenez à surveiller la santé et les performances des instances de votre Cache de Redis d’Azure" 
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
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-monitor-azure-redis-cache"></a>Comment faire pour surveiller le Cache Redis d’Azure

Cache de Redis Azure fournit plusieurs options pour la surveillance de vos instances de cache. Vous pouvez statistiques épingler des graphiques de mesures pour le Startboard, personnaliser la plage de date et d’heure des graphiques de contrôle, ajouter et supprimer des mesures à partir des diagrammes et définir des alertes lorsque certaines conditions sont remplies. Ces outils permettent de surveiller la santé de vos instances de Cache Redis d’Azure et vous aider à gérer vos applications de mise en cache.

Lorsque les tests de diagnostic du cache sont activés, métrique pour les instances de Cache Redis d’Azure est collectée environ toutes les 30 secondes et stockée afin de pouvoir être affichés dans les graphiques mesures et évalués par les règles d’alerte.

Les mesures de cache sont collectées à l’aide de la Redis commande [INFO](http://redis.io/commands/info) . Pour plus d’informations sur les différentes informations les valeurs utilisées pour chaque cache métrique, reportez-vous à la section [mesures disponibles et le reporting des intervalles](#available-metrics-and-reporting-intervals).

Pour afficher les mesures de cache, [accédez](cache-configure.md#configure-redis-cache-settings) à votre instance de cache dans le [portail Azure](https://portal.azure.com). Métrique pour les instances de Cache Redis d’Azure est accessibles sur la lame **Redis des mesures** .

![Redis métrique][redis-cache-redis-metrics-blade]

>[AZURE.IMPORTANT] Si le message suivant est affiché dans la lame **Redis métriques** , suivez les étapes décrites dans la section [Activer les diagnostics de la mémoire cache](#enable-cache-diagnostics) pour activer les diagnostics de la mémoire cache.
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

La lame de **Redis des mesures** a **analyse** les graphiques qui affichent des indicateurs de cache. Chaque graphique peut être personnalisé en ajoutant ou supprimant des mesures et la modification de l’intervalle de rapport. Pour l’affichage et configuration des alertes et des opérations, la lame **Redis le Cache** a une section relative aux **opérations** qui affiche les **événements** de cache et les **règles d’alerte**.

## <a name="enable-cache-diagnostics"></a>Activer les diagnostics de la mémoire cache

Cache de Redis Azure vous offre la possibilité de disposer de données de diagnostic stockées dans un compte de stockage, vous pouvez utiliser des outils que vous souhaitez accéder et traiter les données directement. Dans l’ordre pour les diagnostics de cache puissent être collectées, stockées et affichées dans le portail Azure, un compte de stockage doit être configuré. Caches dans la même région et d’abonnement partagent le même compte de stockage de diagnostics, et lorsque la configuration est modifiée, elle s’applique à tous les caches de l’abonnement qui se trouvent dans cette région.

Pour activer et configurer les diagnostics de la mémoire cache, accédez à la lame de **Redis de Cache** pour votre instance de cache. Si les tests de diagnostic ne sont pas encore activés, un message s’affiche au lieu d’un graphique de tests de diagnostic.

![Activer les diagnostics de la mémoire cache][redis-cache-enable-diagnostics]

Cliquez sur le message pour afficher la lame de **mesure** et cliquez sur **les paramètres de Diagnostic** pour activer et configurer les paramètres de diagnostic pour l’instance de service de cache.

![Paramètres de diagnostic][redis-cache-diagnostic-settings]

![Configurer les diagnostics][redis-cache-configure-diagnostics]

Cliquez **sur** le bouton pour activer les diagnostics de la mémoire cache et d’afficher la configuration des tests de diagnostic.

Cliquez sur la flèche située à droite du **Compte de stockage** pour sélectionner un compte de stockage pour contenir les données de diagnostic. Pour des performances optimales, sélectionnez un compte de stockage dans la même région que votre cache.

Une fois les paramètres de diagnostics sont configurés, cliquez sur **Enregistrer** pour enregistrer la configuration. Il peut prendre quelques instants pour que les modifications soient prises en compte.

>[AZURE.IMPORTANT] Caches dans la même région et d’abonnement partagent les mêmes paramètres de stockage de diagnostics, et lorsque la configuration est modifiée (diagnostic activé/désactivé ou modification du compte de stockage), elle s’applique à tous les caches de l’abonnement qui se trouvent dans cette région.

Pour afficher les mesures stockées, examiner les tables de votre compte de stockage avec des noms qui commencent par `WADMetrics`. Pour plus d’informations sur l’accès à la métrique stockée en dehors du portail Azure, consultez l’exemple de [contrôle d’accès Redis du Cache de données](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) .

>[AZURE.NOTE] Seulement les mesures qui sont stockés dans le compte de stockage sélectionné sont affichés dans le portail Azure. Si vous modifiez les comptes de stockage, les données du compte de stockage configurés précédemment restent disponibles pour le téléchargement, mais il n’est pas affiché dans le portail Azure.  

## <a name="available-metrics-and-reporting-intervals"></a>Les mesures disponibles et le reporting des intervalles

Mesures du cache sont signalées à l’aide de plusieurs intervalles de reporting, y compris la **dernière heure**, **aujourd'hui**, **semaine dernière**et **personnalisée**. La lame de **mesure** pour chaque graphique de mesures affiche les valeurs de moyennes, minimales et maximales pour chaque métrique dans le graphique, et certaines mesures affichent un total pour l’intervalle de rapport. 

Chaque mesure comprend deux versions. Une mesure des performances pour le cache entier et pour les caches qui utilisent le [clustering](cache-how-to-premium-clustering.md), une seconde version de la mesure qui inclut des mesures `(Shard 0-9)` dans l’exercice de mesures de nom pour un unique partagé dans un cache. Par exemple, si un cache comporte 4 milieu des fragments, `Cache Hits` est le montant total d’accès pour le cache entier, et `Cache Hits (Shard 3)` est simplement le nombre d’accès pour ce partagé du cache.

>[AZURE.NOTE] Même lorsque le cache est inactif sans applications de client actif connecté, vous pouvez constater une activité du cache, telles que les clients connectés, utilisation de la mémoire et les opérations en cours d’exécution. Cette activité est normale lors de l’opération d’une instance de Cache Redis d’Azure.

| Métrique            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Accès au cache        | Le nombre de clés trouvées au cours de l’intervalle de rapport spécifiée. Correspond à `keyspace_hits` à partir de la Redis [les informations](http://redis.io/commands/info) de commande.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Absences dans le cache      | Nombre de recherches ayant échouées au cours de l’intervalle de rapport spécifiée. Correspond à `keyspace_misses` à partir de la commande Redis les informations. Absences dans le cache ne signifient pas nécessairement qu'un problème avec le cache. Par exemple, lorsque vous utilisez le modèle de programmation de cache-aside, une application recherche d’abord dans le cache d’un élément. Si l’élément n’est pas visible (absence de cache), l’élément est récupéré à partir de la base de données et ajouté au cache pour la prochaine fois. Absences dans le cache sont un comportement normal pour le modèle de programmation de cache de mise en jachère. Si le nombre d’échecs du cache est plus élevé que prévu, examinez la logique d’application qui remplit et lit à partir du cache. Si des éléments sont en cours de suppression du cache en raison d’une sollicitation de la mémoire puis il y a peut-être des absences dans le cache, mais une mesure meilleure pour surveiller la pression de mémoire serait `Used Memory` ou `Evicted Keys`. |
| Clients connectés | Le nombre de connexions de client dans le cache pendant l’intervalle de rapport spécifiée. Correspond à `connected_clients` à partir de la commande Redis les informations. Une fois atteinte la [limite de connexion](cache-configure.md#default-redis-server-configuration) , les tentatives de connexion ultérieures au cache échoue. Notez que même s’il n’y a aucune application client actif, il peut toujours être quelques instances de processus internes et des connexions des clients connectés.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Clés supprimés      | Le nombre d’éléments éliminée du cache au cours de l’intervalle de rapport spécifié à la `maxmemory` limite. Correspond à `evicted_keys` à partir de la commande Redis les informations.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Clés ayant expirés      | Le nombre d’articles arrivés à expiration dans le cache pendant l’intervalle de rapport spécifié. Cette valeur correspond au `expired_keys` à partir de la commande Redis les informations.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Obtient              | Nombre d’opérations d’obtenir à partir du cache au cours de l’intervalle de rapport spécifiée. Cette valeur est la somme de ces valeurs à partir des informations Redis commande tout : `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, et `cmdstat_getrange`et est équivalent à la somme du nombre de réponses au cours de l’intervalle de rapport.                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Redis de charge du serveur | Le pourcentage de cycles dans lequel le serveur Redis est occupé à traiter et n’attend ne pas inactif pour les messages. Si ce compteur atteint 100, cela signifie que le serveur Redis a atteint un plafond de performances et de l’unité centrale ne peut pas traiter plus vite tout. Si vous voyez haute Redis de la charge du serveur s’affiche les exceptions d’expiration dans le client. Dans ce cas, vous devez envisager l’évolution verticale ou le partitionnement des données en plusieurs caches.                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Jeux              | Nombre d’opérations de jeu sur le cache pendant l’intervalle de rapport spécifié. Cette valeur est la somme de ces valeurs à partir des informations Redis commande tout : `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`, et `cmdstat_setnx`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Total des opérations  | Le nombre total de commandes traitées par le serveur de cache au cours de l’intervalle de rapport spécifiée. Cette valeur correspond au `total_commands_processed` à partir de la commande Redis les informations. Notez que lorsque le Cache de Redis Azure est uniquement utilisé pour pub/sub il n’y aura aucune métrique de `Cache Hits`, `Cache Misses`, `Gets`, ou `Sets`, mais il y a `Total Operations` des mesures qui reflètent l’utilisation de la mémoire cache pour les opérations de pub/sub.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Mémoire utilisée       | La quantité de mémoire utilisée pour les paires clé/valeur dans le cache en Mo pendant l’intervalle de rapport spécifiée. Cette valeur correspond au `used_memory` à partir de la commande Redis les informations. Cela n’inclut pas de métadonnées ou fragmentation.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Mémoire utilisée RSS   | La quantité de mémoire cache utilisé en Mo pendant l’intervalle de rapport spécifié, y compris la fragmentation et les métadonnées. Cette valeur correspond au `used_memory_rss` à partir de la commande Redis les informations.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| UNITÉ CENTRALE               | L’utilisation du processeur du serveur Cache Redis d’Azure sous forme de pourcentage au cours de l’intervalle de rapport spécifiée. Cette valeur correspond au système d’exploitation `\Processor(_Total)\% Processor Time` compteur de performance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Lecture du cache        | La quantité de données lues à partir du cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de rapport spécifiée. Cette valeur est dérivée de cartes d’interface réseau prenant en charge la machine virtuelle qui héberge le cache et qui n’est pas Redis spécifique. **Cette valeur correspond à la bande passante utilisée par ce cache. Si vous souhaitez définir les alertes pour les limites de la bande passante du réseau côté serveur, puis créez à l’aide de cette `Cache Read` compteur. Consultez [ce tableau](cache-faq.md#cache-performance) pour les limites de la bande passante observée pour cache de différentes tailles et des niveaux de tarification.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Cache d’écriture       | La quantité de données écrites dans le cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de rapport. Cette valeur est dérivée de cartes d’interface réseau prenant en charge la machine virtuelle qui héberge le cache et qui n’est pas Redis spécifique. Cette valeur correspond à la bande passante des données envoyées dans le cache du client.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |


## <a name="how-to-view-metrics-and-customize-charts"></a>Comment faire pour afficher des mesures et de personnaliser des graphiques

Vous pouvez afficher une vue d’ensemble des mesures de votre cache sur la lame **Redis des mesures** . Pour accéder à la **Redis des mesures** à lame choisir **tous les paramètres** > **Redis des mesures**.

![Redis métrique][redis-cache-redis-metrics]


La lame **Redis mesures** contient les graphiques suivants.

| Redis graphique des mesures | Mesures affichées     |
|------------------|-------------------|
| Écrivent du cache lectures du cache et du Cache | Lecture du cache |
|                            | Cache d’écriture |
| Clients connectés      | Clients connectés |
| Nombre de réponses  | Accès au cache        |
|                  | Absences dans le cache      |
| Total commandes   | Total des opérations  |
| Obtient et définit    | Obtient              |
|                  | Jeux              |
| Utilisation de l’UC         | UNITÉ CENTRALE           |
| Utilisation de la mémoire      | Mémoire utilisée   |
|                   | Mémoire utilisée RSS |
| Redis de charge du serveur | Charge du serveur   |
| Nombre de clés | Nombre total de clés |
|           | Clés supprimés |
|           | Clés ayant expirés |


Pour une vue plus détaillée des mesures sur un graphique spécifique et pour personnaliser le graphique, cliquez sur le graphique de votre choix à partir de la lame **Redis des mesures** pour afficher la lame de **mesure** pour ce graphique.

![Lame de métrique][redis-cache-metric-blade]

Toutes les alertes sont définies sur les mesures affichées par un graphique sont répertoriés au bas de la lame de **mesure** pour ce graphique.

Pour ajouter ou supprimer des mesures ou modifier l’intervalle de rapport, choisissez **Modifier le graphique**.

Pour ajouter ou supprimer des mesures dans le graphique, cliquez sur la case à cocher en regard du nom de la mesure. Pour modifier l’intervalle de rapport, cliquez sur l’intervalle souhaité. Pour modifier le **type de graphique**, cliquez sur le style souhaité. Une fois les modifications souhaitées, cliquez sur **Enregistrer**. 

![Modifier le graphique][redis-cache-edit-chart]

Lorsque vous cliquez sur **Enregistrer** les modifications persisteront jusqu'à ce que vous laissez la lame de **mesure** . Lorsque vous revenez plus tard, vous verrez la métrique d’origine et la plage horaire à nouveau. Pour plus d’informations sur la personnalisation de graphiques, voir [métriques de service de moniteur](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Pour afficher les mesures pour une période de temps spécifique dans un graphique, placez la souris sur une des barres spécifiques ou des points sur le graphique qui correspond à l’heure de votre choix, et les mesures de cet intervalle sont affichées.

![Afficher les détails du graphique][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>Comment faire pour surveiller un cache premium avec les clusters

Les caches de prime qui ont l’activation des [clusters](cache-how-to-premium-clustering.md) peuvent avoir jusqu'à 10 milieu des fragments. Chaque partagé a sa propre métrique, et ces mesures sont agrégées afin de fournir des métriques pour le cache dans son ensemble. Chaque mesure comprend deux versions. Une mesure des performances pour le cache entier et une seconde version de la mesure qui inclut des mesures `(Shard 0-9)` dans l’exercice de mesures de nom pour un unique partagé dans un cache. Par exemple, si un cache a 3 milieu des fragments, `Cache Hits` est le montant total d’accès pour le cache entier, et `Cache Hits (Shard 2)` est simplement le nombre d’accès pour ce partagé du cache.

Chaque graphique de surveillance affiche la métrique de niveau supérieur pour le cache, ainsi que les mesures de chaque partagé du cache.

![Moniteur][redis-cache-premium-monitor]

Passage de la souris sur les points de données d’affiche les détails de ce point dans le temps. 

![Moniteur][redis-cache-premium-point-summary]

Les valeurs plus élevées sont généralement les valeurs d’agrégation pour le cache alors que les plus petites valeurs sont les mesures individuelles de l’éclater. Notez que dans cet exemple, il existe trois milieu des fragments et les présences dans le cache sont réparties uniformément sur les milieu des fragments.

![Moniteur][redis-cache-premium-point-shard]

Pour visualiser les détails cliquez sur le graphique pour afficher une vue étendue sur la lame de **mesure** .

![Moniteur][redis-cache-premium-chart-detail]

Par défaut chaque graphique comprend le compteur de performance de cache de niveau supérieur, ainsi que les compteurs de performance pour les milieu des fragments individuels. Vous pouvez personnaliser ces derniers sur la lame de **Modifier le graphique** .

![Moniteur][redis-cache-premium-edit]

Pour plus d’informations sur les compteurs de performance disponibles, reportez-vous à la section [mesures disponibles et le reporting des intervalles](#available-metrics-and-reporting-intervals).


## <a name="operations-and-alerts"></a>Les opérations et les alertes

La section **opérations** sur la lame **Redis le Cache** comporte des sections de **règles d’alerte** et **d’événements** .

![Oeprations][redis-cache-operations-events]

Pour afficher la liste des dernières opérations de cache, cliquez sur le graphique **d’événements** pour afficher la **événements** de lame. Récupération et la régénération des touches d’accès rapide et l’activation et la résolution des règles d’alerte sont des exemples d’opérations. Pour plus d’informations sur chaque événement, cliquez sur l’événement dans la blade **d’événements** .

Pour plus d’informations sur les événements, consultez [affichage des événements et des journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md).

La section **règles d’alerte** affiche le nombre d’alertes pour l’instance de cache. Une règle d’alerte vous permet de surveiller votre instance de cache et de recevoir un e-mail chaque fois qu’une certaine valeur métrique atteint le seuil défini dans la règle. 

Règles d’alerte sont évaluées à environ toutes les cinq minutes, et lorsqu’une règle d’alerte est activée, les notifications configurées sont envoyées. Activations de règle d’alerte et les notifications ne sont pas traitées immédiatement ; Il peut y avoir un délai de plusieurs minutes avant d’activer une règle d’alerte et les notifications sont envoyées.

Règles d’alerte peuvent être affichées et définir à partir de la blade de **métrique** d’un plan de surveillance spécifique, ou à partir de la blade de **règles d’alerte** .

Règles d’alerte ont les propriétés suivantes.

| Propriétés de la règle d’alerte                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ressources                            | La ressource est évaluée par la règle d’alerte. Lorsque vous créez une règle d’alerte à partir d’un Redis cache, le cache est la ressource.                                                                                                                                                                                                                                                                                                                                                  |
| Nom                                | Nom qui identifie de manière unique la règle d’alerte dans l’instance de cache actuelle.                                                                                                                                                                                                                                                                                                                                                                                       |
| Description                         | Description facultative de la règle d’alerte.                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Métrique                              | La métrique pour être surveillé par la règle d’alerte. Pour obtenir une liste des mesures de cache, consultez les mesures disponibles et le reporting des intervalles.                                                                                                                                                                                                                                                                                                                                             |
| Condition                           | L’opérateur de condition pour la règle d’alerte. Les choix possibles sont : supérieur à, supérieur à ou égal à, inférieur à, inférieur ou égal à                                                                                                                                                                                                                                                                                                                             |
| Seuil                           | La valeur utilisée pour la comparer à la mesure à l’aide de l’opérateur spécifié par la propriété condition. En fonction de la mesure, cette valeur peut être en octets par seconde, octets, % ou nombre.                                                                                                                                                                                                                                                                                     |
| Période                              | Spécifie la période sur laquelle la valeur moyenne de la valeur métrique est utilisée pour la comparaison de la règle d’alerte. Par exemple, si la période est sur la dernière heure, la valeur moyenne de la mesure sur l’intervalle d’une heure est utilisée pour la comparaison. Si vous souhaitez être averti lorsque le seuil est atteint en raison d’un pic d’activité, puis un délai plus court est approprié. Pour être averti lorsqu’il existe une activité soutenue supérieure au seuil, utilisez une période plus longue. |
| Service de messagerie et de collaboration des administrateurs | Lorsque la valeur est true, l’administrateur du service et l’administrateur de collaboration sont envoyés par courrier électronique lorsque l’alerte est activée.                                                                                                                                                                                                                                                                                                                                                                    |
| Messagerie de l’administrateur supplémentaires      | Adresse e-mail facultative pour un administrateur supplémentaire être averti lorsque l’alerte est activée.                                                                                                                                                                                                                                                                                                                                                                    |

Qu’une notification est envoyée par l’activation de la règle d’alerte. Une fois que le seuil d’une règle est dépassé et une notification est envoyée, la règle n’est pas nouveau évaluée jusqu'à ce que la métrique inférieure au seuil. Si par la suite, la mesure dépasse le seuil, l’alerte est réactivé et une notification est envoyée.

Pour afficher toutes les règles d’alerte pour votre instance de cache, cliquez sur la partie de **règles d’alerte** de la lame de **Redis de Cache** . Pour afficher uniquement les règles d’alerte qui utilisent une mesure spécifique, accédez à la lame de **mesure** pour le graphique qui contient cette mesure.

![Règles d’alerte][redis-cache-alert-rules]

Pour ajouter une règle d’alerte, cliquez sur **Ajouter une alerte** à partir de la lame de **mesure** ou la lame de **règles d’alerte** . 

Entrez les critères de la règle de votre choix dans la lame de règle **Ajouter une alerte** , puis cliquez sur **OK**. 

![Ajouter une règle d’alerte][redis-cache-add-alert]

>[AZURE.NOTE] Lors de la création d’une règle d’alerte en cliquant sur **Ajouter une alerte** à partir de la blade **métrique** , seulement les mesures affichées sur le graphique dans cette blade s’affichent dans la liste déroulante **métrique** . Lors de la création d’une règle d’alerte en cliquant sur **Ajouter une alerte** à partir de la blade de **règles d’alerte** , tous les paramètres de cache sont disponibles dans la liste déroulante **métrique** .

Une fois qu’une règle d’alerte est enregistrée s’affiche sur la lame de **règles d’alerte** , ainsi que sur la lame de **mesure** pour les graphiques qui affichent la métrique utilisée dans la règle d’alerte. Pour modifier une règle d’alerte, cliquez sur le nom de la règle d’alerte à afficher de la lame de **Modifier la règle** . De la lame de **Modifier la règle** vous pouvez modifier les propriétés de la règle, supprimer ou désactiver la règle d’alerte ou réactiver la règle, s’il a été précédemment désactivé.

>[AZURE.NOTE] Les modifications apportées aux propriétés de la règle peuvent prendre quelques instants avant qu’elles apparaissent sur la lame de **règles d’alerte** ou sur la lame de **mesure** .

Lorsqu’une règle d’alerte est activée, un e-mail est envoyé en fonction de la configuration de la règle d’alerte, et une icône d’avertissement s’affiche dans la partie de **règles d’alerte** sur la lame **Redis le Cache** .

Une règle d’alerte est considérée être résolue lorsque la condition d’alerte n’est plus a la valeur true. Une fois que la condition de la règle d’alerte est résolue, l’icône d’avertissement est remplacé par une coche. Pour plus d’informations sur l’alertes activations et résolutions, cliquez sur la partie **d’événements** sur la lame **Redis le Cache** pour afficher les événements sur la blade **d’événements** .

Pour plus d’informations sur les alertes dans Azure, consultez [réception notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="metrics-on-the-redis-cache-blade"></a>Mesures sur la lame de Redis de Cache

La lame de **Redis de Cache** affiche les catégories suivantes de mesures.

-   [Surveillance des graphiques](#monitoring-charts)
-   [Graphiques de l’utilisation](#usage-charts)

### <a name="monitoring-charts"></a>Surveillance des graphiques

La section **analyse** a **accès et échecs**, **Obtient et définit**, **connexions**et **Total commandes** graphiques.

![Surveillance des graphiques][redis-cache-monitoring-part]

Les graphiques **d’analyse** affichent les mesures suivantes.

| Contrôle graphique | Mesures de cache     |
|------------------|-------------------|
| Nombre de réponses  | Accès au cache        |
|                  | Absences dans le cache      |
| Obtient et définit    | Obtient              |
|                  | Jeux              |
| Connexions      | Clients connectés |
| Total commandes   | Total des opérations  |

Pour plus d’informations sur l’affichage des mesures et de personnalisation de graphiques individuels dans cette section, consultez la section suivante de [l’affichage des mesures et de personnaliser des indicateurs graphiques](#how-to-view-metrics-and-customize-charts) .

### <a name="usage-charts"></a>Graphiques de l’utilisation

La section **utilisation** a la **Charge du serveur Redis**, **Utilisation de la mémoire**, **Bande passante réseau**et les graphiques de **L’utilisation du processeur** et affiche également le **niveau de prix** pour l’instance de cache.

![Graphiques de l’utilisation][redis-cache-usage-part]

Le **niveau de prix** s’affiche la tarification de cache de niveau et peut être utilisé à [l’échelle](cache-how-to-scale.md) le cache vers un niveau de prix différent.

**L’utilisation** de graphiques affichent les mesures suivantes.

| Graphique d’utilisation       | Mesures de cache |
|-------------------|---------------|
| Redis de charge du serveur | Charge du serveur   |
| Utilisation de la mémoire      | Mémoire utilisée   |
| Bande passante réseau | Cache d’écriture   |
| Utilisation de l’UC         | UNITÉ CENTRALE           |

Pour plus d’informations sur l’affichage des mesures et de personnalisation de graphiques individuels dans cette section, consultez la section suivante de [l’affichage des mesures et de personnaliser des indicateurs graphiques](#how-to-view-metrics-and-customize-charts) .
  
<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png



