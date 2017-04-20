<properties
   pageTitle="Configuration de résilience et récupération sur Elasticsearch sur Azure"
   description="Considérations liées à la résilience et de récupération pour Elasticsearch."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>
   
# <a name="configuring-resilience-and-recovery-on-elasticsearch-on-azure"></a>Configuration de résilience et récupération sur Elasticsearch sur Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série](guidance-elasticsearch.md). 

Une fonctionnalité clé de Elasticsearch est la prise en charge qu’il fournit pour la résilience en cas de défaillances de nœud ou événements de partition réseau. La réplication est la manière la plus évidente dans lequel vous pouvez améliorer la souplesse d’un cluster, l’activation de Elasticsearch pour vous assurer que plus d’une copie de tout élément de données est disponible sur des nœuds différents au cas où un nœud doit devenir inaccessible. Si un nœud est temporairement indisponible, les autres nœuds qui contiennent des duplications de données à partir du nœud manquant peuvent servir les données manquantes jusqu'à ce que le problème est résolu. Dans le cas d’un problème de durée plus long, le nœud défaillant peut être remplacé par un nouveau, et Elasticsearch peut restaurer les données sur le nouveau nœud à partir des réplicas.

Ici, nous avons compilé les options de résilience et de récupération disponibles avec Elasticsearch lorsque hébergés dans Azure et décrire certains aspects importants d’un cluster d’Elasticsearch qui vous aideront à réduire les risques de perte de données et de temps de récupération de données étendues.

Cet article illustre également quelques exemples de tests qui ont été effectuées pour afficher les effets des différents types de défaillances dans un cluster de Elasticsearch, et comment le système réagit lorsqu’elle recouvre.

Un cluster Elasticsearch utilise des réplicas pour garantir une disponibilité et d’améliorer les performances de lecture. Les réplicas doivent être stockées sur différents ordinateurs virtuels depuis les milieu des fragments principales qui ils répliquent. L’objectif est que si la machine virtuelle qui héberge un nœud de données échoue ou n’est pas disponible, le système peut continuer de fonctionner à l’aide d’ordinateurs virtuels contenant des réplicas.

## <a name="using-dedicated-master-nodes"></a>L’utilisation de nœuds de maîtres dédiés

Un nœud dans un cluster de Elasticsearch est choisi comme le nœud principal. L’objectif de ce nœud est à effectuer des opérations de gestion de cluster comme :

- Détection de nœuds défaillants et basculement vers les réplicas.

- Déplacement de milieu des fragments pour équilibrer la charge de travail de nœud.

- Récupération milieu des fragments lorsqu’un nœud est remis en ligne.

Vous devez envisager d’utiliser des nœuds de maîtres dédiés dans les clusters critiques et assurez-vous qu’il y a 3 nœuds dédiés dont le seul rôle est d’être maître. Cette configuration réduit le volume de travail gourmandes en ressources que ces nœuds doivent exécuter (pas stocker les données ou gérer des requêtes) et contribue à améliorer la stabilité du cluster. Seulement l’un de ces nœuds est élu, mais les autres contient une copie de l’état du système et peuvent prendre en charge le maître élu échoue.

## <a name="controlling-high-availability-with-azure--update-domains-and-fault-domains"></a>Haute disponibilité avec Azure – mise à jour de domaines et erreur de contrôle 

Ordinateurs virtuels différents peuvent partager le même matériel physique. Dans un centre de données Azure, un seul rack peut héberger un certain nombre de machines virtuelles, et tous ces ordinateurs virtuels partagent un courant d’alimentation source et le commutateur réseau. Une défaillance au niveau du rack peut par conséquent avoir une incidence sur un certain nombre de machines virtuelles. Azure utilise la notion de domaines d’erreur pour tenter de se propager ce risque. Un domaine d’erreur correspond approximativement à un groupe d’ordinateurs virtuels qui partagent le même rack. Pour vous assurer qu’une défaillance au niveau du rack ne bloque pas un nœud et les nœuds hébergeant toutes ses réplicas simultanément, vous devez vous assurer que les ordinateurs virtuels sont distribuées sur plusieurs domaines d’erreur.

De même, ordinateurs virtuels peuvent être récupérées par le [Contrôleur de tissu Azure](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) pour effectuer les mises à niveau de système d’exploitation et de maintenance. Azure alloue les ordinateurs virtuels pour mettre à jour des domaines. En cas d’un événement d’entretien planifié, VMs uniquement dans un domaine unique de mise à jour sont effectuées à tout moment. Ordinateurs virtuels dans d’autres domaines de la mise à jour sont laissés en cours d’exécution jusqu'à ce que les ordinateurs virtuels dans le domaine de la mise à jour mis à jour sont remis en ligne. Par conséquent, vous devez également vous assurer que hébergeant des nœuds et des réplicas de machines virtuelles appartiennent à des domaines différents de mise à jour dès que possible.

> [AZURE.NOTE] Pour plus d’informations sur les domaines de la panne et mise à jour, voir [Gérer la disponibilité des ordinateurs virtuels][].

Vous ne pouvez affecter explicitement d’un ordinateur virtuel à un domaine de la mise à jour spécifique et d’un domaine d’erreur. Cette allocation est contrôlée par Azure lors de la création d’ordinateurs virtuels. Toutefois, vous pouvez spécifier que les ordinateurs virtuels doivent être créés en tant que partie d’un jeu de disponibilité. Ordinateurs virtuels dans le même jeu de disponibilité seront réparties sur les domaines de mise à jour et d’erreur. Si vous créez manuellement des ordinateurs virtuels, Azure crée chaque disponibilité avec deux domaines d’erreur et de cinq domaines de mise à jour. Ordinateurs virtuels sont affectés à ces domaines d’erreur et mettre à jour des domaines, cycle rond, comme les autres machines virtuelles sont mis en service, comme suit : 

| MACHINE VIRTUELLE | Domaine d’erreur | Mettre à jour le domaine |
|----|--------------|---------------|
|  1 |            0 |             0 |
|  2 |            1 |             1 |
|  3 |            0 |             2 |
|  4 |            1 |             3 |
|  5 |            0 |             4 |
|  6 |            1 |             0 |
|  7 |            0 |             1 |

> [AZURE.IMPORTANT] Si vous créez des ordinateurs virtuels à l’aide du Gestionnaire de ressources Azure, chaque ensemble de disponibilité peut être allouée jusqu'à 3 domaines de panne et mise à jour de 20. Il s’agit d’un argument imparable pour l’utilisation du Gestionnaire de ressources.

En règle générale, placez tous les ordinateurs virtuels qui ont la même fonction dans le même jeu de disponibilité, mais créer des ensembles de disponibilité différents pour les ordinateurs virtuels qui exécutent des fonctions différentes. Avec Elasticsearch, de que cela signifie que vous devez envisager disponibilité au moins distincte de création de jeux pour :

- Ordinateurs virtuels de l’hébergement des nœuds de données.
- Ordinateurs virtuels de l’hébergement de nœuds clients (si vous les utilisez).
- Machines virtuelles des nœuds maîtres d’hébergement.

En outre, vous devez vous assurer que chaque nœud dans un cluster est conscient du domaine de la mise à jour et qu’il appartient au domaine d’erreur. Ces informations peuvent aider à s’assurer que Elasticsearch ne pas créer de milieu des fragments et des réplicas dans la même erreur et mettre à jour les domaines, en minimisant les risques d’un partagé et ses réplicas à partir de la mise hors ligne en même temps. Vous pouvez configurer un nœud Elasticsearch afin de refléter la distribution du matériel du cluster en configurant la [prise de conscience de répartition partagé](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness). Par exemple, vous pouvez définir une paire d’attributs du nœud personnalisé appelé *faultDomain* et *updateDomain* dans le fichier elasticsearch.yml, comme suit :

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

Dans ce cas, les attributs sont définis à l’aide des valeurs contenues dans la * \${FAULTDOMAIN}* et * \${UPDATEDOMAIN}* variables d’environnement au démarrage de Elasticsearch. Vous devez également ajouter les entrées suivantes au fichier Elasticsearch.yml pour indiquer que *faultDomain* et *updateDomain* sont des attributs de la prise de conscience d’allocation et spécifient les ensembles de valeurs acceptables pour ces attributs :

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

Sensibilisation de répartition partagé conjointement avec le [filtrage de répartition partagé](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering) vous permet de spécifier de manière explicite les nœuds qui peuvent héberger le milieu des fragments pour un index donné.

Si vous avez besoin de mettre à l’échelle au-delà du nombre de domaines d’erreur et mettre à jour des domaines dans un jeu de disponibilité, vous pouvez créer des ordinateurs virtuels dans les jeux de disponibilité supplémentaires. Toutefois, vous devez comprendre que nœuds dans les jeux de disponibilité différentes peuvent être récupérées de maintenance simultanément. Essayez de vous assurer que chaque partagé et au moins un de ses réplicas sont contenus dans le même jeu de disponibilité.

> [AZURE.NOTE] Il existe actuellement une limite de 100 VM par jeu de disponibilité. Pour plus d’informations, reportez-vous à la section [abonnement Azure et les limites de service, les quotas et les contraintes](../azure-subscription-service-limits.md).

### <a name="backup-and-restore"></a>Sauvegarde et restauration

L’utilisation de réplicas ne fournit pas une protection complète à partir d’une défaillance grave (comme supprimer accidentellement l’ensemble du cluster). Vous devez vous assurer que vous sauvegardez les données d’un cluster régulièrement, et que vous disposez d’une stratégie testée et pour restaurer le système à partir de ces sauvegardes.

Utilisez la Elasticsearch de la [capture instantanée API et restauration](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) : élastique ne limiter ces. >> pour sauvegarder et restaurer les index. Les snapshots peuvent être enregistrés dans un système de fichiers partagé. Vous pouvez également les plug-ins sont disponibles qui peut écrire des snapshots au système de fichiers (très) ( [plug-in de très](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) Hadoop distribué ou stockage Azure (le [plug-in d’Azure](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository)).

Lorsque vous sélectionnez le mécanisme de stockage des captures instantanées, tenez compte des points suivants :

- Vous pouvez utiliser le [stockage Azure](https://azure.microsoft.com/services/storage/files/) pour implémenter un système de fichiers partagé qui est accessible à partir de tous les nœuds.

- Utilisez uniquement le plug-in très si vous exécutez Elasticsearch conjointement avec Hadoop.

- Le plug-in très vous demande de désactiver le Gestionnaire de sécurité Java en cours d’exécution à l’intérieur de l’instance Elasticsearch de la machine virtuelle de Java (JVM).

- Le plug-in très prend en charge un système de fichiers compatible très autant que la configuration d’Hadoop correcte est utilisée avec Elasticsearch.

  
## <a name="handling-intermittent-connectivity-between-nodes"></a>Connectivité intermittente de gestion entre les nœuds

Les problèmes intermittents de réseau, VM redémarre après une maintenance de routine sur le centre de données, et autres événements semblables peuvent provoquer des nœuds temporairement inaccessibles. Dans ces situations, où l’événement est susceptible d’être à courte durée de vie, la surcharge de rééquilibrer au milieu des fragments apparaît deux fois dans les quick suite (une fois lorsque la panne est détectée et à nouveau lorsque le nœud deviennent visibles au maître) peut devenir une charge mémoire importante qui affecte les performances. Vous pouvez empêcher l’inaccessibilité du nœud temporaire à l’origine de la forme de base à rééquilibrer le cluster en définissant le *retardée\_délai* propriété d’un index, ou pour tous les index. L’exemple ci-dessous définit le délai de 5 minutes :

```http
PUT /_all/settings
{
    "settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
    }
}
```

Pour plus d’informations, voir [allocation retardant lorsqu’un nœud quitte](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html).

Dans un réseau qui est sujet à des interruptions, vous pouvez également modifier les paramètres qui configurent un maître pour détecter le moment où un autre nœud n’est plus accessible. Ces paramètres font partie intégrante du module de [détection de zen](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen) fourni avec Elasticsearch, et vous pouvez les définir dans le fichier Elasticsearch.yml. Par exemple, le paramètre *discovery.zen.fd.ping.retries* spécifie combien de fois un nœud maître va tenter de ping sur un autre nœud dans le cluster avant de décider de qui a échoué. Ce paramètre par défaut est 3, mais vous pouvez le modifier comme suit :

```yaml
discovery.zen.fd.ping_retries: 6
```

## <a name="controlling-recovery"></a>Contrôle de récupération

Lors de la connexion à un nœud est rétablie après une panne, tout milieu des fragments sur ce nœud devez être récupérées pour les mettre à jour. Par défaut, Elasticsearch récupère le milieu des fragments dans l’ordre suivant :

- Par date de création d’index inversé. Index plus récentes sont restaurés avant les index les plus anciens.

- Par le nom de l’index inversé. Tout d’abord les index ont des noms qui sont dans l’ordre alphanumérique supérieures à d’autres utilisateurs seront restaurés.

Si certains index est plus critiques que d’autres, mais qui ne correspondent pas à ces critères, vous pouvez substituer la priorité des index en définissant la propriété *index.priority* . Index avec une valeur plus élevée pour cette propriété seront restaurés avant les index ayant une valeur inférieure :

```http
PUT low_priority_index
{
    "settings": {
        "index.priority": 1
    }
}

PUT high_priority_index
{
    "settings": {
        "index.priority": 10
    }
}
```

Pour plus d’informations, consultez [Définition de priorités de récupération Index](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization).

Vous pouvez surveiller le processus de récupération pour un ou plusieurs index à l’aide de la * \_restauration* API :

```http
GET /high_priority_index/_recovery?pretty=true
```

Pour plus d’informations, consultez [Récupération de l’index](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery).

> [AZURE.NOTE] Un cluster avec le milieu des fragments nécessitant une reprise aura un état *jaune* pour indiquer que le milieu des fragments ne sont actuellement disponibles. Lorsque tous les au milieu des fragments sont disponibles, l’état du cluster doit rétablir le *vert*. Un cluster avec un état *rouge* indique qu’un ou plusieurs milieu des fragments sont physiquement manquants, il peut être nécessaire de restaurer les données à partir d’une sauvegarde.

## <a name="preventing-split-brain"></a>Empêcher le fractionnement 

Un fractionnement peut se produire si les connexions entre les nœuds échouent. Si un nœud principal deviendrait inaccessible à une partie du cluster, une élection a lieu dans le segment de réseau qui reste joignable et un autre nœud deviendra le maître. Dans un cluster mal configuré, il est possible pour chaque partie du cluster pour que les différentes formes de base entraînant des incohérences de données ou de corruption. Ce phénomène est appelé un *fractionnement*.

Vous pouvez réduire les risques d’un fractionnement en configurant la *minimale\_master\_nœuds* propriété du module de découverte, dans le fichier elasticsearch.yml. Cette propriété spécifie le nombre de nœuds doit être disponible pour permettre l’élection d’un masque. L’exemple suivant définit la valeur de cette propriété à 2 :

```yaml
discovery.zen.minimum_master_nodes: 2
```

Cette valeur doit être définie pour la majorité inférieure au nombre de nœuds qui sont en mesure de remplir le rôle de maître. Par exemple, si votre cluster a 3 nœuds maîtres, *minimum\_master\_nœuds* doit être défini sur 2. Si vous avez 5 nœuds maîtres, *minimum\_master\_nœuds* doit être définie sur 3. Dans l’idéal, vous devez disposer d’un nombre impair de nœuds principaux.

> [AZURE.NOTE] Il est possible pour un fractionnement se produise si plusieurs nœuds du même cluster de maîtres sont lancés simultanément. Alors que cette occurrence est rare, vous pouvez l’empêcher en démarrant les nœuds en série avec un léger retard (5 secondes) entre chacun d’eux. 

## <a name="handling-rolling-updates"></a>Gestion des mises à jour successives

Si vous effectuez une mise à niveau du logiciel aux nœuds vous-même (par exemple, la migration vers une version plus récente ou l’exécution d’un correctif), vous devrez effectuer un travail sur des nœuds individuels qui nécessitent la mise hors connexion tout en conservant le reste du cluster disponible. Dans ce cas, envisagez d’implémenter le processus suivant. 

1. Assurez-vous que cette réaffectation partagé est retardée suffisamment pour empêcher le maître élu de rééquilibrer le milieu des fragments d’un nœud manquant sur le reste du cluster. Par défaut, réaffectation d’éclater est retardée pendant une minute, mais vous pouvez augmenter la durée si un nœud est susceptible de ne pas être disponible pour une période plus longue. L’exemple suivant augmente le délai de 5 minutes :

    ```http
    PUT /_all/_settings
    {
        "settings": {
            "index.unassigned.node_left.delayed_timeout": "5m"
        }
    }
    ```

    > [AZURE.IMPORTANT] Vous pouvez également désactiver réaffectation d’éclater complètement en définissant le *cluster.routing.allocation.enable* du cluster sur *Aucun*. Toutefois, évitez d’utiliser cette approche si les nouveaux index sont susceptibles d’être créés alors que le nœud est en mode hors connexion, cela peut entraîner des échec résultant dans un cluster avec le statut rouge allocation d’index.

2. Arrêter Elasticsearch sur le nœud à gérer. Si Elasticsearch est en cours d’exécution en tant que service, vous pourrez peut-être interrompre le processus de manière contrôlée à l’aide d’une commande de système d’exploitation. L’exemple suivant montre comment arrêter le service Elasticsearch sur un seul nœud en cours d’exécution sur Ubuntu :

    ```bash
    service elasticsearch stop
    ```

    Vous pouvez également utiliser l’API d’arrêt directement sur le nœud :

    ```http
    POST /_cluster/nodes/_local/_shutdown
    ```

3. Effectuer la maintenance nécessaire sur le nœud

4. Redémarrez le nœud et attendez qu’il se joindre au cluster.

5. Allocation d’éclater réactiver :

    ```http
    PUT /_cluster/settings
    {
        "transient": {
            "cluster.routing.allocation.enable": "all"
        }
    }
    ```

> [AZURE.NOTE] Si vous avez besoin de conserver plusieurs nœuds, répétez les étapes 2&ndash;4 sur chaque nœud avant l’allocation d’éclater la réactivation.

Si vous le pouvez, arrêtez l’indexation de nouvelles données au cours de ce processus. Cela vous permet de réduire le temps de récupération lorsque des nœuds sont remis en ligne et rejoindre le cluster.

Méfiez-vous des mises à jour automatiques pour les éléments, tels que la JVM (idéalement, désactiver mises à jour automatiques pour ces éléments), en particulier lorsque vous exécutez Elasticsearch sous Windows. L’agent de mise à jour de Java peut télécharger la version la plus récente du Java automatiquement, mais peut-être nécessiter Elasticsearch être redémarré pour que la mise à jour soit prise en compte. Cela peut entraîner une perte temporaire de nœuds, selon la configuration de l’agent de mise à jour de Java non coordonnée. Cela peut également entraîner dans différentes instances de Elasticsearch dans le même cluster exécutant différentes versions de la JVM qui peut entraîner des problèmes de compatibilité.

## <a name="testing-and-analyzing-elasticsearch-resilience-and-recovery"></a>Test et analyse Elasticsearch résilience et récupération

Cette section décrit une série de tests ont été effectués pour évaluer la résilience et de récupération d’un cluster d’Elasticsearch contenant trois nœuds de données et trois maîtres.

Les scénarios suivants ont été testés : 

- Défaillance d’un nœud et redémarrez sans perte de données. Un nœud de données est arrêté et redémarré au bout de 5 minutes. Elasticsearch a été configuré pour ne pas pour réaffecter le milieu des fragments manquants dans cet intervalle, afin de déplacer le milieu des fragments ne naît aucune e/s supplémentaires. Lorsque le nœud a redémarré, le processus de récupération apporte au milieu des fragments sur ce nœud à jour.

- Défaillance de nœud avec perte de données catastrophique. Un nœud de données est arrêté, et les données qu’il contient sont effacées pour simuler la défaillance grave du disque. Le nœud est ensuite redémarré (après 5 minutes), effectivement agissant comme un remplacement pour le nœud d’origine. Le processus de récupération nécessite de reconstruire les données manquantes pour ce nœud et peut entraîner le déplacement du milieu des fragments détenues sur les autres nœuds.

- Défaillance d’un nœud et redémarrez sans perte de données, mais avec réallocation d’éclater. Un nœud de données est arrêté, et au milieu des fragments qu’il contient sont redistribuées à d’autres nœuds. Le nœud est ensuite redémarré et réaffectation plus se produit pour rééquilibrer le cluster.

- Déploiement de mises à jour. Chaque nœud dans le cluster est arrêté et redémarré après un court intervalle pour simuler des machines en cours de redémarrage après une mise à jour de logiciel. Un seul nœud est arrêté à tout moment. Milieu des fragments ne sont pas réaffectées lorsqu’un nœud est vers le bas.

Chaque scénario a été la même charge de travail, y compris un mélange de requêtes de filtre, les agrégations et les tâches de réception de données, tandis que les nœuds ont été mis hors ligne et récupéré. La majeure partie des opérations d’insertion dans la charge de travail chaque 1000 documents enregistrés et ont été exécutées sur un seul index lors de l’agrégation et de requêtes de filtre utilisé un index distinct contenant des millions de plusieurs documents. Il s’agissait pour permettre les performances des requêtes pour être évaluées séparément à partir des insertions en bloc. Chaque index contenue cinq milieu des fragments et un réplica.

Les sections suivantes résument les résultats de ces tests, signaler toute dégradation des performances lorsqu’un nœud est en mode hors connexion ou en cours de restauration, ainsi que les erreurs qui ont été signalés. Les résultats sont présentés sous forme de graphique, les points d’un ou plusieurs nœuds sont manquants et d’estimer le temps nécessaire pour récupérer entièrement et d’atteindre un niveau de performances qui était présente avant les nœuds hors semblable au système de mise en surbrillance.

> [AZURE.NOTE] Les faisceaux de tests permettant d’effectuer ces tests est disponibles en ligne. Vous pouvez adapter et utiliser ces faisceaux pour vérifier la résistance et la récupération de vos propres configurations de cluster. Pour plus d’informations, consultez [exécution résilience Elasticsearch des tests automatisés][].

## <a name="node-failure-and-restart-with-no-data-loss-results"></a>Défaillance d’un nœud et un redémarrage sans perte de données : résultats

<!-- TODO; reformat this pdf for display inline --> 

Les résultats de ce test s’affichent dans le fichier [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario1.pdf). Les graphiques indiquent le profil de performance de la charge de travail et les ressources physiques pour chaque nœud du cluster. La partie initiale des graphiques montrent le système qui exécute normalement pendant environ 20 minutes, à partir de laquelle le nœud 0 est arrêté pendant 5 minutes avant le redémarrage. Les statistiques pendant 20 minutes supplémentaires sont illustrées ; le système prend environ 10 minutes afin de récupérer et de se stabiliser. Ceci est illustré par le taux de transaction et les temps de réponse pour les charges de travail différentes.

Notez les points suivants :

- Au cours de l’essai, aucune erreur n’ont été signalés. Aucune donnée n’a été perdue, et toutes les opérations s’est terminée correctement.

- Les taux de transaction pour les trois types d’opération (insertion en bloc, requête d’agrégation et requête de filtre) est supprimée et les temps de réponse moyen a augmenté pendant que le nœud 0 était hors connexion.

- Au cours de la période de récupération, le taux de transaction et les temps de réponse pour la requête d’agrégation et de la requête de filtre les opérations ont été progressivement restaurées. Les performances d’insertion en bloc de récupération un peu avant de diminuer. Toutefois, ceci est probablement en raison du volume de données à l’origine de l’index utilisé par l’insertion en bloc pour la croissance et les taux de transaction pour cette opération peuvent se ralentir avant même de nœud 0 est mis hors connexion.

- Le graphique d’utilisation du processeur pour le nœud 0 affiche l’activité réduite pendant la phase de récupération, c’est à cause du disque accru et une activité réseau provoquée par le mécanisme de récupération, le nœud a avec toutes les données qu’il a manqué alors qu’il est en mode hors connexion et mettre à jour les au milieu des fragments qu’il contient.

- Au milieu des fragments d’index ne sont pas exactement distribuées équitablement sur tous les nœuds. Il y a deux index contenant 5 milieu des fragments et 1 réplica chaque, pour un total de 20 milieu des fragments. Deux nœuds contient donc 6 milieu des fragments alors que les deux autres détiennent 7 chaque. Ceci est évident dans les graphiques de l’utilisation du processeur au cours de la période initiale de 20 minutes, nœud 0 est moins occupé que les deux autres. Une fois la récupération terminée, certains commutation semble se produire comme nœud 2 apparaît pour devenir le nœud plus encombré.

    
## <a name="node-failure-with-catastrophic-data-loss-results"></a>Défaillance d’un nœud sans perte de données catastrophique : résultats

<!-- TODO; reformat this pdf for display inline -->

Les résultats de ce test sont représentées dans le fichier [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario2.pdf). Comme avec le premier test, la partie initiale des graphiques montre le système qui exécute normalement pendant environ 20 minutes, à quel nœud point 0 est arrêté pendant 5 minutes. Au cours de cet intervalle, les données Elasticsearch sur ce nœud sont supprimées, simulation de perte de données catastrophique, avant le redémarrage. Récupération complète apparaît et 12 à 15 minutes avant de restaurer les niveaux de performances visible avant le test.

Notez les points suivants :

- Au cours de l’essai, aucune erreur n’ont été signalés. Aucune donnée n’a été perdue, et toutes les opérations s’est terminée correctement.

- Les taux de transaction pour les trois types d’opération (insertion en bloc, requête d’agrégation et requête de filtre) est supprimée et les temps de réponse moyen a augmenté pendant que le nœud 0 était hors connexion. À ce stade, le profil des performances du test est identique au premier scénario. Ce n’est pas surprenant que, à ce stade, les scénarios sont identiques.

- Au cours de la période de récupération, le taux de transaction élevés et un temps de réponse ont été restaurés, bien que pendant ce temps il était beaucoup plus de volatilité dans les figures. Ceci est probablement dû à du travail supplémentaire que les nœuds du cluster exécutent des, fournissant les données pour restaurer au milieu des fragments manquants. Ce travail supplémentaire est évident dans l’utilisation du processeur, l’activité du disque et graphiques d’activité réseau.

- Le graphique d’utilisation du processeur pour les nœuds de 0 et 1 affiche l’activité réduite pendant la phase de récupération, que c’est en raison du disque accrue et une activité réseau causée par le processus de récupération. Dans le premier scénario, seulement survenir avec le nœud en cours de restauration, mais dans ce cas il est probable que la plupart des données manquantes pour le nœud 0 est en cours de restauration à partir du nœud 1.

- L’activité d’e/s de nœud 0 est effectivement réduite par rapport au premier scénario. Cela peut résulter de l’efficacité des e/s de la simple copie des données pour un ensemble partagé plutôt que sur la série de plus petites demandes d’e/s requises pour mettre un partagé existant à jour.

- L’activité du réseau pour tous les nœuds de trois indiquent des pics d’activité que les données transmises et reçues entre les nœuds. Dans le scénario 1, seul le nœud 0 exposé en tant qu’activité de réseau, mais cette activité semblait être prolongée pour une période plus longue. Là encore, cette différence peut résulter de l’efficacité de la transmission des données pour un partagé toute comme une requête unique, au lieu de la série de petites demandes reçues lors de la récupération d’un partagé.

## <a name="node-failure-and-restart-with-shard-reallocation-results"></a>Défaillance d’un nœud et un redémarrage avec réallocation d’éclater : résultats

<!-- TODO; reformat this pdf for display inline -->

Le fichier [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario3.pdf) illustre les résultats de ce test. Comme avec le premier test, la partie initiale des graphiques montrent le système qui exécute normalement pendant environ 20 minutes, à quel nœud point 0 est arrêté pendant 5 minutes. À ce stade, le cluster Elasticsearch tente de recréer au milieu des fragments manquants et rééquilibrer au milieu des fragments sur les nœuds restants. Après le nœud de 5 minutes, 0 est remis en ligne, et encore une fois le cluster a rééquilibrer au milieu des fragments. Performances est restauré après 12 à 15 minutes.

Notez les points suivants :

- Au cours de l’essai, aucune erreur n’ont été signalés. Aucune donnée n’a été perdue, et toutes les opérations s’est terminée correctement.

- Les taux de transaction pour les trois types d’opération (insertion en bloc, requête d’agrégation et requête de filtre) est supprimée et les temps de réponse moyen a sensiblement augmenté lors de nœud 0 en mode hors connexion par rapport aux deux tests précédentes. C’est en raison de l’activité du cluster plus recréer au milieu des fragments manquants et de rééquilibrer le cluster comme l’attestent les chiffres de relief d’activité de disque et de réseau pour les nœuds 1 et 2 dans cette période.

- Au cours de la période après que le nœud 0 revient en ligne, le temps de réponse et le taux de transaction restent volatiles.

- Les processeur l’utilisation du disque activité graphiques et nœud 0 affiche très réduite prendre les premières mesures lors de la phase de récupération. Cela est dû à ce stade, le nœud 0 ne traite pas de toutes les données. Après une période d’environ 5 minutes, le nœud des rafales en action < RBC : cela m’a fait snort à haute voix. Je suis ne provenant pas avec pour dire cela bien mieux.  >> comme indiqué par l’augmentation soudaine dans l’activité de l’UC, disque et réseau. Cela est probablement dû au redistribuer le milieu des fragments sur les nœuds de cluster. Puis, le nœud 0 indique une activité normale.
  
## <a name="rolling-updates-results"></a>Déploiement de mises à jour : résultats

<!-- TODO; reformat this pdf for display inline -->

Les résultats de ce test, dans le fichier [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario4.pdf), montrent comment chaque nœud est mis hors connexion, puis restaurée à nouveau à la suite. Chaque nœud est mis hors tension pendant 5 minutes avant le redémarrage au point où le nœud suivant dans la séquence est arrêté.

Notez les points suivants :

- Alors que chaque nœud est revenue à la normale, les performances en termes de débit et de réactivité restent raisonnablement même.

- Activité du disque augmente pour chaque nœud pendant une courte période, comme il est remis en ligne. Il s’agit très probablement en raison du processus de récupération reprise toutes les modifications qui sont sont produites alors que le nœud était inactif.

- Lorsqu’un nœud est mis hors connexion, les pics d’activité de réseau se produisent dans les nœuds restants. PICS peut également se produisent lorsqu’un nœud a redémarré.

- Après que le nœud final est recyclé, le système entre dans une période de volatilité considérable. Cela est probablement causé par le processus de récupération avoir à synchroniser les modifications entre chaque nœud et vérifiez que tous les réplicas et leurs milieu des fragments correspondants sont cohérents. À un moment donné, ce consécutifs causes d’effort insérer des opérations au délai d’attente et échouer. Les erreurs signalées à chaque cas ont été :

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

Expérimentation ultérieure a montré que présentation d’un délai de quelques minutes entre les cycles de chaque nœud éliminé cette erreur, il est probablement provoqué par un conflit entre le processus de récupération tentative de restauration simultanée de plusieurs nœuds et l’essentiel des opérations, essayez de stocker des milliers de nouveaux documents d’insertion.


## <a name="summary"></a>Résumé

Les tests effectués indiqué que :

- Elasticsearch a été extrêmement résistant aux modes les plus courants de défaillance susceptible de se produire dans un cluster.

- Elasticsearch pouvez restaurer rapidement si un cluster bien conçu est soumise à la perte de données catastrophique sur un nœud. Cela peut se produire si vous configurez Elasticsearch pour enregistrer les données dans le stockage éphémère et que le nœud est configuré par la suite après un redémarrage. Ces résultats montrent que même dans ce cas, les risques liés à l’utilisation du stockage éphémère sont probables compensé par les avantages de performances qui fournit de cette classe de stockage.

- Dans les trois premiers scénarios, aucune erreur ne lors dans l’insertion en bloc simultané, l’agrégation et charges de travail de requête filtre un nœud a été mis hors ligne et récupérés.

- Uniquement le dernier scénario indiqué de perte de données potentielle, et cette perte affecté uniquement les nouvelles données ajoutées. Il est recommandé dans les applications effectuant l’acquisition de données pour atténuer ce risque en effectuant à nouveau les opérations d’insertion qui ont échoué car le type de l’erreur signalée est très probablement temporaire.

- Les résultats du dernier test montrent également que si vous effectuez la maintenance planifiée des nœuds dans un cluster, performances profiteront si vous autorisez plusieurs minutes entre les cycles d’un nœud et le suivant. En cas d’imprévus (par exemple, le centre de données recyclage des nœuds après avoir effectué une mise à jour du système d’exploitation), vous avez moins de contrôle sur quand et comment les nœuds sont arrêtés et redémarrés. Le conflit qui survient lors de l’Elasticsearch tente de récupérer l’état du cluster après une panne de nœud séquentiel peut entraîner des délais d’attente et les erreurs. 

[Gérer la disponibilité des Machines virtuelles]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[Exécution des Tests de résistance Elasticsearch automatique]: guidance-elasticsearch-running-automated-resilience-tests.md
