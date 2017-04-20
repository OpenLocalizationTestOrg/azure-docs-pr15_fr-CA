<properties
    pageTitle="Base de données de SQL Azure et de performances pour les bases de données individuelles | Microsoft Azure"
    description="Cet article peut vous aider à déterminer le niveau de service à choisir pour votre application. Il recommande également des solutions d’optimisation de votre application pour tirer le maximum de la base de données de SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/13/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-and-performance-for-single-databases"></a>Base de données de SQL Azure et de performances pour les bases de données unique

Une base de données SQL Azure propose trois [niveaux de service](sql-database-service-tiers.md): Basic, Standard et Premium. Chaque niveau de service isole strictement les ressources que votre base de données SQL peut utiliser et garantit des performances prévisibles pour ce niveau de service. Dans cet article, nous vous proposons des conseils qui vous aideront à choisir le niveau de service pour votre application. Nous abordons également les méthodes que vous pouvez optimiser votre application pour tirer le meilleur parti de la base de données de SQL Azure.

>[AZURE.NOTE] Cet article se concentre sur les conseils pour les performances des bases de données unique dans une base de données de SQL Azure. Pour des conseils de performance relatifs aux pools d’élastique de la base de données, consultez [Considérations de prix et de performances pour les pools d’élasticité de la base de données](sql-database-elastic-pool-guidance.md). Notez, cependant, que vous pouvez appliquer un grand nombre des recommandations de réglage dans cet article aux bases de données dans un pool d’élasticité de la base de données et obtenir des avantages de performances similaires.

Voici les trois niveaux de service de base de données de SQL Azure que vous pouvez choisir (performances est mesurée en unités de débit de base de données, ou [DTUs](sql-database-what-is-a-dtu.md):

- **Base**. Le service de base niveau offre une bonne performance prévisibilité pour chaque base de données, à l’heure sur l’heure. Dans une base de données de base, des ressources suffisantes prend en charge les bonnes performances dans une petite base de données qui ne possède pas plusieurs demandes simultanées.
- **Standard**. Le niveau de service Standard offre les meilleures performances prévisibilité et repousse les limites des bases de données qui ont plusieurs demandes simultanées, telles que des applications web et de groupe de travail. Lorsque vous choisissez une base de données de niveau de service Standard, vous pouvez dimensionner votre application de base de données en fonction des performances prévisibles, minute par minute.
- **Prime**. Le niveau de service Premium offre des performances prévisibles, deuxième sur la seconde, pour chaque base de données de la prime. Lorsque vous choisissez le niveau de service Premium, vous pouvez dimensionner votre application de base de données basée sur la charge maximale pour cette base de données. Le plan supprime le cas dans les performances écart peut entraîner de petites requêtes prend plus de temps que prévu dans les opérations sensibles à la latence. Ce modèle peut simplifier considérablement les cycles de validation de produit et le développement pour les applications qui ont besoin de faire des observations fortes sur pointe les besoins en ressources, écart de performances ou la latence des requêtes.

À chaque niveau de service, vous définissez le niveau de performances, afin d’avoir la possibilité de payer uniquement la capacité dont vous avez besoin. Vous pouvez [Ajuster la capacité](sql-database-scale-up.md), le haut ou vers le bas, comme les modifications de la charge de travail. Par exemple, si votre charge de travail de base de données est élevé au cours de la saison de courses back-à l’école, vous pouvez augmenter le niveau de performance de la base de données pendant une période définie, juillet à septembre. Vous pouvez le réduire lorsque votre saison se termine. Vous pouvez réduire ce que vous payez en optimisant votre environnement en nuage à du caractère saisonnier de votre entreprise. Ce modèle fonctionne aussi bien pour les cycles de lancement du produit logiciel. Une équipe de test peut allouer la capacité lors de l’exécution du test et puis relâchez cette capacité lorsqu’ils ont fini de tester. Dans un modèle de demande de capacité, vous payez pour une capacité que vous avez besoin et évitez les dépenses en matière de ressources dédiées qui vous utiliserez rarement.

## <a name="why-service-tiers"></a>Pourquoi les niveaux de service ?

Bien que chaque charge de travail de base de données peut différer, de niveaux de service vise à assurer la prévisibilité des performances à différents niveaux de performances. Clients avec des besoins en ressources de base de données à grande échelle peuvent fonctionner dans un environnement informatique plus dédié.

### <a name="common-service-tier-use-cases"></a>Cas d’usage de niveau de service commun

#### <a name="basic"></a>Base

- **Vous découvrez avec la base de données de SQL Azure**. Les applications qui sont en cours de développement souvent inutile de hauts niveaux de performances. Bases de données de base sont un environnement idéal pour le développement de base de données, à un coût faible.
- **Vous avez une base de données avec un utilisateur unique**. Les applications qui associent un seul utilisateur avec une base de données, généralement n’ont des exigences strictes de performances et de la concurrence d’accès. Ces applications sont des candidats pour la couche de service de base.

#### <a name="standard"></a>Standard

- **Votre base de données a plusieurs demandes simultanées**. Les applications qui desservent plus d’un utilisateur à la fois généralement doivent les niveaux de performances supérieurs. Par exemple, des sites Web qui obtiennent le trafic modéré ou applications départementales qui nécessitent plus de ressources sont de bons candidats pour le niveau de service Standard.

#### <a name="premium"></a>Premium

La plupart des cas d’usage couche service Premium ont un ou plusieurs de ces caractéristiques :

- **Charger des pics élevés**. Une application qui nécessite beaucoup d’UC, de mémoire ou d’entrée/sortie (e/s) d’achever ses opérations nécessite un niveau dédié et hautes performances. Par exemple, une opération de base de données appelée à consommer plusieurs cœurs d’UC pendant une période prolongée est un candidat pour le niveau de service Premium.
- **Nombre de demandes simultanées**. Certaines applications de base de données service plusieurs requêtes concurrentes, par exemple, lorsque le service d’un site Web qui a un volume élevé de trafic. Standard et de base des niveaux de service limitent le nombre de requêtes simultanées par base de données. Les applications qui nécessitent le plus de connexions devra choisir une taille de réserve appropriée pour gérer le nombre maximal de demandes nécessaires.
- **Faible latence**. Certaines applications doivent garantir une réponse à partir de la base de données en un minimum de temps. Si une procédure stockée est appelée dans le cadre d’une opération de client plus large, vous pouvez avoir une exigence d’avoir un retour de cet appel dans pas plus de 20 millisecondes, 99 pour cent du temps. Les avantages de ce type d’application de la couche de service Premium, afin de vous assurer que la puissance de calcul requise est disponible.

Le niveau de service dont vous avez besoin pour votre base de données SQL dépend des exigences de charge de pointe pour chaque dimension de la ressource. Certaines applications utilisent une quantité importante d’une ressource, mais ont des exigences importantes pour d’autres ressources.

## <a name="service-tier-capabilities-and-limits"></a>Limites et les capacités de niveau de Service
Chaque niveau de performances et de la couche de service est associé à différentes limites de caractéristiques et de performances. Le tableau suivant décrit ces caractéristiques pour une seule base de données.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

Les sections suivantes ont plus d’informations sur l’affichage d’utilisation liée à ces limites.

### <a name="maximum-in-memory-oltp-storage"></a>Stockage de maximum en mémoire OLTP

La vue **sys.dm_db_resource_stats** permet de contrôler votre utilisation du stockage Azure en mémoire. Pour plus d’informations sur la surveillance, consultez [stockage de moniteur en mémoire OLTP](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] Actuellement, aperçu de (OLTP) de traitement des transactions en ligne Azure en mémoire sont pris en charge uniquement pour les bases de données individuelles. Vous ne pouvez pas l’utiliser dans des bases de données dans des pools d’élasticité de la base de données.

### <a name="maximum-concurrent-requests"></a>Nombre maximal de requêtes simultané

Pour voir le nombre de demandes simultanées, exécutez cette requête Transact-SQL sur votre base de données SQL :

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Pour analyser la charge de travail d’une base de données SQL Server sur site, modifier cette requête pour filtrer sur la base de données spécifique que vous souhaitez analyser. Par exemple, si vous disposez d’une base de données local nommé MyDatabase, cette requête Transact-SQL renvoie le nombre de demandes simultanées dans cette base de données :

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Ceci est juste une capture instantanée à un moment unique dans le temps. Pour obtenir une meilleure compréhension de votre charge de travail et les exigences en matière de demandes simultanées, vous devez collecter de nombreux exemples dans le temps.

### <a name="maximum-concurrent-logins"></a>Maximum connexions simultanées

Vous pouvez analyser vos modèles utilisateur et des applications pour avoir une idée de la fréquence des connexions. Vous pouvez également exécuter les charges réelles dans un environnement de test pour vous assurer que vous rencontrez pas cela, ou autres limites qu'aborder dans cet article. Il n’est pas une simple requête ou vue de gestion dynamique (DMV) qui peut afficher des concurrents compte d’ouverture de session ou l’historique.

Si plusieurs clients utilisent la même chaîne de connexion, le service authentifie chaque ouverture de session. Si 10 utilisateurs vous connecter simultanément à une base de données en utilisant les mêmes nom d’utilisateur et mot de passe, il y aura 10 connexions simultanées. Cette limite s’applique uniquement à la durée de la connexion et l’authentification. Si la même 10 connexion à la base de données dans l’ordre, le nombre de connexions simultanées ne serait jamais supérieur à 1.

>[AZURE.NOTE] Actuellement, cette limite ne s’applique pas aux bases de données dans des pools d’élasticité de la base de données.

### <a name="maximum-sessions"></a>Nombre maximal de sessions

Pour voir le nombre de sessions actives en cours, exécutez cette requête Transact-SQL sur votre base de données SQL :

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Si vous analysez une charge de travail de SQL Server sur site, modifiez la requête pour vous concentrer sur une base de données spécifique. Cette requête permet de déterminer les besoins possibles d’une session pour la base de données si vous envisagez de transférer vers la base de données de SQL Azure.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Encore une fois, ces requêtes retournent un nombre de point-à-temps. Si vous collectez plusieurs échantillons au fil du temps, vous aurez la meilleure connaissance de votre session utiliser.

Pour une analyse de base de données SQL, vous pouvez obtenir des statistiques d’historique sur les sessions. **Sys.resource_stats**de requête et utilisez la colonne **active_session_count** . Consultez la section suivante pour plus d’informations sur l’utilisation de ce mode d’affichage.

## <a name="monitor-resource-use"></a>Surveiller l’utilisation des ressources
Deux vues peuvent vous aider à surveiller l’utilisation des ressources pour une base de données SQL par rapport à son niveau de service :

- [Sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>Sys.dm_db_resource_stats
Vous pouvez utiliser la vue [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dans chaque base de données SQL. Le **sys.dm_db_resource_stats** affiche des données d’utilisation ressource récentes par rapport à la couche de service. Les pourcentages moyens d’UC, d’e/s de données, les écritures de journal et la mémoire sont enregistrées toutes les 15 secondes et sont conservées pendant 1 heure.

Étant donné que cette vue offre un aspect plus granuleux à l’utilisation des ressources, utilisez **sys.dm_db_resource_stats** première pour toute analyse de l’état actuel ou la résolution des problèmes. Par exemple, cette requête montre l’utilisation de ressources moyen et maximal pour la base de données en cours sur la dernière heure :

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Pour les autres requêtes, consultez les exemples dans [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>Sys.resource_stats

La vue [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) dans la base de données **master** a des informations supplémentaires qui peuvent vous aider à surveiller les performances de votre base de données SQL à son niveau de performances et le niveau de service spécifique. Les données sont collectées toutes les cinq minutes et sont maintenues pendant environ 35 jours. Cet affichage est utile pour une analyse historique à long terme de l’utilisation des ressources dans votre base de données SQL.

Le graphique suivant montre l’utilisation des ressources du processeur pour une base de données Premium avec le niveau de performance P2 pour chaque heure en une semaine. Ce graphique commence un lundi, montre 5 jours de travail et un week-end, lorsque moins importante se produit sur l’application.

![Utilisation des ressources de base de données SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

À partir des données, cette base de données a actuellement un pic de charge de processeur de juste plus de 50 % d’utilisation du processeur par rapport au niveau de performances P2 (MIDI, mardi). Si UC est le facteur principal dans le profil des ressources de l’application, vous pouvez décider que P2 est le niveau de performances afin de garantir que la charge de travail tienne toujours. Si vous prévoyez une application à évoluer dans le temps, il est judicieux de disposer d’une mémoire tampon de ressource supplémentaire afin que l’application n’atteint pas la limite de niveaux de performances jamais. Si vous augmentez le niveau de performance, vous pouvez éviter les erreurs visible par les clients qui peuvent se produire lorsqu’une base de données n’a pas assez de puissance pour traiter les demandes de manière efficace, en particulier dans des environnements sensibles à la latence. Un exemple est une base de données qui prend en charge une application qui peint des pages Web basées sur les résultats des appels de base de données.

Notez qu’autres types d’applications peuvent interpréter le graphique même différemment. Par exemple, si une application essaie de traiter les données de paie chaque jour et a le même graphique, ce type de modèle de « par lots » peut procéder correctement à un niveau de performances de P1. Le niveau de performance P1 a 100 DTUs par rapport à 200 DTUs au niveau des performances P2. Le niveau de performance P1 fournit la moitié les performances du niveau de performances P2. Ainsi, 50 pour cent de l’utilisation de l’UC dans P2 est égale à 100 pour cent d’utilisation du processeur dans P1. Si l’application n’a pas de délais d’attente, il peut avoir une importance pas si une tâche prend 2 heures ou 2,5 heures, si elle obtient fait aujourd'hui. Une application dans cette catégorie permet probablement un niveau de performances P1. Vous pouvez tirer parti du fait qu’il existe des périodes de temps au cours de la journée lorsque l’utilisation des ressources est inférieure, afin que tout « grand PIC » peut déborde sur dans un des creux plus tard dans la journée. Le niveau de performance P1 peut être bon pour ce type d’application (et économies), tant que les travaux peuvent terminer à temps chaque jour.

Expose de la base de données SQL Azure consommé des informations sur la ressource pour chaque base de données active dans la vue **sys.resource_stats** de la base de données **maître** de chaque serveur. Les données de la table sont regroupées pour des intervalles de 5 minutes. Avec les niveaux de service Basic, Standard et Premium, les données peuvent prendre plus de 5 minutes pour apparaître dans la table, afin que ces données ne sont plus utiles pour l’analyse de l’historique au lieu de près en temps réel analyse. Interrogez la vue de **sys.resource_stats** pour consulter l’historique récent d’une base de données et pour vérifier si la réservation que vous avez choisi remis les performances souhaitées lorsque cela est nécessaire.

>[AZURE.NOTE] Vous devez être connecté à la base de données **principale** de votre serveur de base de données logique SQL pour interroger les **sys.resource_stats** dans les exemples suivants.

Cet exemple vous montre comment les données de cette vue sont exposées :

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![La vue de catalogue sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

L’exemple suivant vous montre différentes manières que vous pouvez utiliser la vue de catalogue **sys.resource_stats** pour obtenir des informations sur l’utilisation des ressources dans votre base de données SQL :

1. Pour consulter les ressources de la semaine dernière utilisation pour l’userdb1 de la base de données, vous pouvez exécuter cette requête :

        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;

2. Pour évaluer le fonctionnement de votre charge de travail ajuste le niveau de performance, vous devez consulter chaque aspect de la métrique de la ressource : CPU, lectures, écritures, nombre de travailleurs et nombre de sessions. Voici une requête modifiée à l’aide de **sys.resource_stats** pour signaler les valeurs moyennes et maximales de ces mesures de ressources :

        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. Grâce à ces informations sur les valeurs moyennes et maximales de chaque mesure de la ressource, vous pouvez évaluer correctement votre charge de travail s’intègre le niveau de performance que vous avez choisi. En règle générale, les valeurs moyennes de **sys.resource_stats** vous donnent une bonne planification à utiliser par rapport à la taille cible. Il convient de votre module de mesure principal. Pour obtenir un exemple, vous utilisez peut-être le niveau de service Standard avec un niveau de performance S2. La moyenne utiliser des pourcentages de CPU et e/s des lectures et écritures sont au-dessous de 40 %, le nombre moyen de travailleurs est inférieure à 50 et le nombre moyen de sessions est inférieure à 200. Votre charge de travail peut tenir dans le niveau de performance S1. Il est facile de voir si votre base de données tienne dans les limites de session et le travailleur. Pour voir si une base de données s’intègre dans un niveau de performances inférieur en ce qui concerne les UC, lectures et écritures, divisez le nombre DTU du niveau de performances inférieur selon le nombre DTU de votre niveau actuel de performances et puis multiplier le résultat par 100 :

    * *S1 DTU / S2 DTU* 100 = 20 / 50* 100 = 40 **

    Le résultat est la différence de performances relatives entre les niveaux de performance de deux sous forme de pourcentage. Si votre utilisation des ressources ne dépasse pas ce montant, votre charge de travail peut tenir dans le niveau de performances inférieur. Toutefois, vous avez besoin de consulter toutes les plages de valeurs d’utilisation de ressources et de déterminer, en pourcentage, la fréquence à laquelle se tiendraient votre charge de travail de base de données dans le niveau de performances inférieur. La requête suivante renvoie le pourcentage d’ajustement par dimension de ressource, selon le seuil de 40 % qui nous calculés dans cet exemple :

        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    En fonction de votre objectif de niveau de service de base de données (SLO), vous pouvez décider si votre charge de travail s’intègre dans le niveau de performances inférieur. Si votre charge de travail de base de données SLO est 99,9 % et que la requête précédente renvoie des valeurs supérieures à 99,9 % pour toutes les dimensions de trois ressources, probablement de votre charge de travail peut intégrer le niveau de performances inférieur.

    En examinant le pourcentage d’ajustement vous donne également un aperçu si vous avez atteindre le niveau de performances supérieur suivant pour répondre à vos LENTS. Par exemple, userdb1 montre l’utilisation de l’UC suivante pour la semaine précédente :

  	| Pourcentage moyen de processeur | Pourcentage de CPU maximal |
  	|---|---|
  	| 24,5 | 100,00 |

    Moyenne du processeur est environ à un quart de la limite du niveau de performances, qui tiendraient dans le niveau de performance de la base de données. Toutefois, la valeur maximale indique que la base de données atteint la limite du niveau de performances. Vous avez besoin atteindre le niveau de performances supérieur suivant ? Vous devez étudier la façon plusieurs fois votre charge de travail d’atteint 100 pour cent et comparer à votre charge de travail de base de données SLO.

        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent’
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    Si cette requête renvoie une valeur inférieure à 99,9 % pour une des dimensions trois ressources, envisagez un déplacement vers le niveau de performances supérieur suivant ou techniques de réglage des applications permet de réduire la charge sur la base de données SQL.

4. Cet exercice prend également en compte votre augmentation de la charge de travail projeté dans le futur.

## <a name="tune-your-application"></a>Réglage de votre application.

Dans traditionnelles sur site de SQL Server, le processus de planification de la capacité initiale souvent est séparant le processus d’exécution d’une application en production. Acheter des licences de matériel et de produit sont tout d’abord, et réglage des performances est effectué par la suite. Lorsque vous utilisez la base de données de SQL Azure, il est judicieux d’interweave le processus d’une application en cours d’exécution et de paramétrage. Avec le modèle de paiement pour la capacité à la demande, vous pouvez optimiser votre application pour utiliser les ressources minimales maintenant, au lieu d’overprovisioning sur le matériel en fonction des estimations des plans de croissance future pour une application, qui sont souvent incorrectes. Certains clients peuvent choisir de ne pas régler une application et choisissez plutôt d’overprovision des ressources matérielles. Cette approche peut s’avérer utile si vous ne souhaitez pas modifier une application clé pendant une période occupée. Toutefois, le réglage d’une application permettant de réduire les besoins de ressources et inférieurs factures mensuelles lorsque vous utilisez les niveaux de service dans la base de données de SQL Azure.

### <a name="application-characteristics"></a>Caractéristiques de l’application

Bien que les niveaux de service de base de données de SQL Azure est conçus pour améliorer la stabilité des performances et la prévisibilité pour une application, certaines méthodes conseillées peuvent vous aider à optimiser votre application pour mieux tirer parti des ressources à un niveau de performances. Bien que de nombreuses applications ont des gains de performances significatifs simplement en la faisant passer à un niveau de performances supérieur ou niveau de service, certaines applications nécessitent un réglage supplémentaire pour bénéficier d’un niveau de service supérieur. Pour améliorer les performances, envisagez de réglage de l’application supplémentaire pour les applications qui présentent ces caractéristiques :

- **Applications qui ont des performances lentes en raison du comportement « bavardages »**. Les applications bavardes effectuer des opérations d’accès aux données excessive qui sont sensibles à la latence du réseau. Vous devrez peut-être modifier ces types d’applications afin de réduire le nombre d’opérations d’accès aux données à la base de données SQL. Par exemple, vous pouvez améliorer les performances de l’application à l’aide de techniques telles que le traitement par lots de requêtes ad-hoc ou en déplaçant les requêtes à des procédures stockées. Pour plus d’informations, consultez [requêtes par lots](#batch-queries).
- **Bases de données avec une charge de travail intensive qui ne peut pas être pris en charge par une machine unique complète**. Bases de données qui dépassent les ressources de niveau de performance le plus élevé de prime pourraient bénéficier de la mise à l’échelle la charge de travail. Pour plus d’informations, reportez-vous à la section [ont des bases de données croisées](#cross-database-sharding) et [fonctionnels de partitionnement](#functional-partitioning).
- **Applications qui ont des requêtes non optimaux**. Applications, en particulier ceux de la couche d’accès aux données, qui ont mal réglé les requêtes peuvent ne pas bénéficient d’un niveau de performances supérieur. Cela inclut les requêtes qui ne disposent pas d’une clause WHERE, qui ont des index manquants ou déjà obsolètes des statistiques. Ces applications bénéficient des techniques d’optimisation des performances de requête standard. Pour plus d’informations, consultez [les index manquants](#missing-indexes) et [l’Optimisation de requêtes et de l’affinage](#query-tuning-and-hinting).
- **Conception d’accéder aux applications qui ont des données non optimaux**. Les applications qui ont des problèmes de concurrence d’accès données inhérente, par exemple le blocage, peuvent ne pas bénéficient d’un niveau de performances supérieur. Envisagez de réduire les allers-retours sur la base de données de SQL Azure par la mise en cache de données avec le service de mise en cache Azure ou une autre technologie de mise en cache côté client. Reportez-vous à [la mise en cache de la couche Application](#application-tier-caching).

## <a name="tuning-techniques"></a>Techniques de réglage
Dans cette section, nous examinons certaines techniques que vous pouvez utiliser pour optimiser la base de données de SQL Azure pour obtenir les meilleures performances pour votre application et l’exécuter au niveau de performances inférieur. Certaines de ces techniques correspondent aux traditionnels SQL Server réglage des meilleures pratiques, mais d’autres sont spécifiques à la base de données de SQL Azure. Dans certains cas, vous pouvez examiner les ressources consommées pour une base de données rechercher les zones afin de mieux adapter et étendre les techniques traditionnelles de SQL Server à travailler dans la base de données de SQL Azure.

### <a name="azure-portal-tools"></a>Outils de portail Azure
Vous avez trouverez les deux outils dans Azure portal qui peut vous aider à analyser et résolvent les problèmes de performances avec votre base de données SQL :

- [Analyse des performances de requête](sql-database-query-performance.md)
- [Gestionnaire de base de données SQL](sql-database-advisor.md)

Le portail Azure a plus d’informations sur les deux de ces outils et comment les utiliser. Afin de diagnostiquer et corriger les problèmes, nous vous recommandons tout d’abord les outils dans le portail Azure. Nous vous conseillons d’utiliser le manuel de réglage des approches qui nous décrire par la suite, index et réglage de requête, dans des cas particuliers qui manquent.

### <a name="missing-indexes"></a>Index manquants
Un problème courant dans les performances de base de données OLTP est lié à la conception physique de la base de données. Souvent, les schémas de base de données sont conçus et fournis sans essais à grande échelle (soit en charge ou dans le volume de données). Malheureusement, les performances d’un plan de requête peuvent être acceptable à petite échelle mais dégradent sensiblement sous les volumes de données au niveau de la production. La source la plus courante de ce problème est l’absence d’index appropriés pour répondre à des filtres ou autres restrictions dans une requête. Souvent, manifestes d’index manquant sous forme de tableau d’analyse lors d’une recherche d’index peut suffire.

Dans cet exemple, le plan de requête sélectionné utilise une analyse lors de la recherche d’un suffirait :

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Un plan de requête avec index manquants](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Une base de données SQL Azure peut vous aider à rechercher et correctif manquant commune conditions d’index. DMV qui est intégrées dans la base de données de SQL Azure consultez les compilations de requête dans laquelle un index peut réduire de manière significative le coût estimé pour exécuter une requête. Au cours de l’exécution de la requête, base de données de SQL effectue le suivi de la fréquence de chaque plan de requête est exécutée et effectue le suivi de l’écart estimé entre le plan de requête en cours d’exécution et celui fictif dans lequel cet index existait. Vous pouvez utiliser ces DMV à deviner rapidement les modifications apportées à la conception physique de la base de données peuvent améliorer le coût total de la charge de travail pour une base de données et de sa charge de travail réelle.

Vous pouvez utiliser cette requête pour évaluer le potentiels index manquants :

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

Dans cet exemple, la requête a généré cette suggestion :

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Après sa création, cette même instruction SELECT sélectionne un plan différent, qui utilise une recherche au lieu d’une analyse, puis exécute le plan plus efficacement :

![Un plan de requête avec index corrigés](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Le point clé est que la capacité d’e/s d’un système de marchandise partagé, est plus limitée que celle d’une machine serveur dédié. Il existe une prime sur la réduction des e/s inutiles pour profiter au maximum du système dans le DTU de chaque niveau de performances des niveaux de service de base de données de SQL Azure. Conception d’une base de données physique appropriée choix peut considérablement améliorer la latence des requêtes individuelles, améliorer le débit des demandes simultanées traitées par unité d’échelle et réduire les coûts requis pour satisfaire la requête. Pour plus d’informations sur l’index manquant DMV, voir [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Optimisation de requêtes et de repères
L’optimiseur de requêtes de base de données de SQL Azure est semblable à l’optimiseur de requêtes SQL Server traditionnel. La plupart des méthodes conseillées pour l’optimisation des requêtes et de comprendre le raisonnement limitations du modèle pour l’optimiseur de requête s’appliquent également à la base de données de SQL Azure. Si vous réglez des requêtes dans la base de données de SQL Azure, vous pouvez obtenir l’avantage supplémentaire de réduire les besoins en ressources d’agrégation. Votre application peut être en mesure d’exécuter un coût inférieur à celui d’untuned équivalent car il peut s’exécuter à un niveau de performances inférieur.

Par exemple qui est courant dans SQL Server et qui s’applique également à la base de données de SQL Azure, comment l’optimiseur de requête « détecte » paramètres. Lors de la compilation, l’optimiseur de requêtes évalue la valeur actuelle du paramètre pour déterminer si elle peut générer un plan de requête plus efficace. Bien que cette stratégie peut souvent mener à un plan de requête qui est considérablement plus rapide qu’un plan compilé sans les valeurs de paramètre connus, il travaille actuellement pourtour à la fois dans SQL Server et dans la base de données de SQL Azure. Le paramètre n’est parfois pas détectée et parfois le paramètre est détectée mais le plan généré est non optimaux pour l’ensemble des valeurs de paramètre dans une charge de travail. Microsoft inclut des indicateurs de requête (directives) afin que vous pouvez spécifier plus délibérément les intentions et substituer le comportement par défaut de la détection des paramètres. Souvent, si vous utilisez des indicateurs, vous pouvez résoudre les cas dans lesquels le comportement par défaut de SQL Server ou la base de données de SQL Azure est imparfait pour une charge de travail de client spécifique.

L’exemple suivant illustre la façon dont le processeur de requêtes peut générer un plan non optimaux à la fois pour les performances et les ressources requises. Cet exemple montre également que si vous utilisez un indicateur de requête, vous pouvez réduire les exigences de temps et de ressources exécution de la requête pour votre base de données SQL :

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

Le code de programme d’installation crée une table qui a travers la distribution des données. Le plan de requête optimal diffère selon le paramètre sélectionné. Malheureusement, le comportement de cache de plan ne recompilez toujours de la requête en fonction de la valeur du paramètre plus courants. Par conséquent, il est possible pour un plan non optimaux mettre en cache et utilisé pour de nombreuses valeurs, même si un autre plan peut être un meilleur choix de plan en moyenne. Le plan de requête crée ensuite deux procédures stockées qui sont identiques, mais un a un indicateur de requête spécial.

**Exemple, partie 1**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Exemple, partie 2**

(Nous vous recommandons d’attendre au moins 10 minutes avant de commencer la partie 2 de l’exemple, afin que les résultats sont différents dans les données de télémétrie résultantes.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Chaque partie de cet exemple tente d’exécuter une instruction insert paramétrable 1 000 fois (pour créer une charge suffisante pour l’utiliser comme un ensemble de données de test). Lors de l’exécution des procédures stockées, le processeur de requêtes examine la valeur du paramètre qui est passée à la procédure au cours de sa première compilation (« détection des paramètres »). Le processeur met en cache le plan qui en résulte et l’utilise pour les appels ultérieurs, même si la valeur du paramètre est différente. Le plan optimal ne peut pas être utilisé dans tous les cas. Parfois, vous avez besoin guider l’optimiseur pour choisir un plan qui est préférable pour le cas de base plutôt que dans le cas spécifique à partir de lorsque la requête a été compilée pour la première fois. Dans cet exemple, le plan initial génère un plan de « analyse » qui lit toutes les lignes pour chaque valeur qui correspond au paramètre de rechercher :

![Optimisation à l’aide d’un plan d’analyse de requêtes](./media/sql-database-performance-guidance/query_tuning_1.png)

Parce que nous avons exécuté la procédure à l’aide de la valeur 1, le plan résultant était optimal pour la valeur 1 mais était non optimaux pour toutes les autres valeurs dans la table. Le résultat probable, n’est pas ce que vous pouvez si vous deviez sélectionner chaque plan de façon aléatoire, car le plan s’exécute plus lentement et utilise plus de ressources.

Si vous exécutez le test avec `SET STATISTICS IO` la valeur `ON`, le travail d’analyse logique dans cet exemple est effectué en tâche de fond. Vous pouvez voir qu’il y a 1,148 lectures effectuées par le régime (qui n’est pas efficace, si le cas moyen doit retourner qu’une seule ligne) :

![Optimisation à l’aide d’une analyse logique de requêtes](./media/sql-database-performance-guidance/query_tuning_2.png)

La deuxième partie de l’exemple utilise un indicateur de requête pour indiquer à l’optimiseur d’utiliser une valeur spécifique au cours du processus de compilation. Dans ce cas, il force le processeur de requêtes pour ignorer la valeur qui est passée comme paramètre, et à la place de supposer `UNKNOWN`. Cela fait référence à une valeur ayant la fréquence moyenne de la table (en ignorant l’inclinaison). Le plan qui en résulte est un plan de recherche qui est plus rapide et utilise moins de ressources, de moyenne, que le plan de la partie 1 de cet exemple :

![Optimisation de requêtes à l’aide d’un indicateur de requête](./media/sql-database-performance-guidance/query_tuning_3.png)

Vous pouvez voir l’effet dans la table **sys.resource_stats** (il existe un retard dans l’exécution de l’essai et lorsque les données remplissent la table). Pour cet exemple, partie 1, exécutées au cours de la période de 22:25:00 et 2ème partie exécutée à 22:35:00. Notez que la fenêtre de temps antérieure utilisé plus de ressources dans cette fenêtre de temps que celle ultérieure (en raison de l’amélioration de l’efficacité du plan).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Exemple de paramétrage des requêtes](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Bien que le volume dans cet exemple est intentionnellement small, l’effet des paramètres non optimaux peut être considérable, en particulier sur les bases de données volumineuses. La différence, dans des cas extrêmes, peut être entre secondes pour les cas rapides et heures pour les cas lentes.

Vous pouvez examiner les **sys.resource_stats** pour déterminer si la ressource d’un test utilise plus ou moins de ressources qu’un autre test. Lorsque vous comparez des données, séparez la durée des tests afin qu’ils ne soient pas dans la même fenêtre de 5 minutes dans la vue **sys.resource_stats** . Le but de cet exercice est de réduire le montant total des ressources utilisées et ne pas pour réduire les ressources de pointe. En règle générale, optimiser un morceau de code de latence permet également de réduire la consommation de ressources. Assurez-vous que les modifications que vous apportez à une application sont nécessaires, et que les modifications n’affectent pas négativement la satisfaction du client pour une personne qui peut être à l’aide d’indicateurs de requête dans l’application.

Si une charge de travail possède un ensemble de requêtes se répètent, il est souvent utile de capturer et à valider au caractère optimal de vos choix de plan car il détermine l’unité de taille de ressource minimal requise pour héberger la base de données. Une fois que vous devez le valider, parfois réexaminer les plans pour vous aider à vous assurer qu’ils n'ont pas dégradées. Pour plus d’informations sur les [indicateurs de requête (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Ont des bases de données croisées
Étant donné que la base de données de SQL Azure s’exécute sur le matériel, les limites de capacité pour une seule base de données sont inférieurs à une installation de SQL Server locale traditionnelle. Certains clients utilisent des techniques ont pour répartir les opérations de base de données sur plusieurs bases de données lorsque les opérations ne tiennent pas dans les limites d’une seule base de données dans la base de données de SQL Azure. La plupart des clients qui utilisent des techniques ont dans la base de données de SQL Azure répartir leurs données sur un axe unique sur plusieurs bases de données. Pour cette approche, vous devez comprendre que les applications OLTP effectuent souvent des transactions qui s’appliquent à une seule ligne ou à un petit groupe de lignes du schéma.

>[AZURE.NOTE] Base de données de SQL fournit désormais une bibliothèque pour vous aider à ont. Pour plus d’informations, consultez [vue d’ensemble de la bibliothèque élastique de la base de données client](sql-database-elastic-database-client-library.md).

Par exemple, si une base de données est le nom du client, de commande et de détails de la commande (par exemple, l’exemple traditionnel base de données Northwind fournie avec SQL Server), vous pourriez fractionner ces données dans plusieurs bases de données par le regroupement d’un client avec la commande associée et les informations détaillées de commande. Vous pouvez garantir que les données du client restent dans une seule base de données. L’application est divisée en différents clients bases de données, de façon efficace en répartissant la charge entre plusieurs bases de données. Avec ont, non seulement les clients peuvent éviter la limite de taille maximale de la base de données et base de données de SQL Azure peut également traiter des charges de travail qui sont nettement plus volumineux que les limites des niveaux de performance différents, dans la mesure où chaque base de données individuel s’intègre dans son DTU.

Ont de la base de données ne réduit pas la capacité de la ressource globale pour une solution, mais il est très efficace au niveau de la prise en charge des solutions de très grande taille qui sont réparties sur plusieurs bases de données. Chaque base de données peut s’exécuter à un niveau de performances différentes pour prendre en charge de très grande taille, des bases de données « effectifs » avec les besoins en ressources haute.

### <a name="functional-partitioning"></a>Partitionnement fonctionnel
Les utilisateurs de SQL Server combinent souvent un grand nombre de fonctions dans une seule base de données. Par exemple, si une application contient la logique permettant de gérer le stock pour un magasin, cette base de données est peut-être logique associée au suivi des stocks, commandes fournisseur, des procédures stockées et des vues indexées ou matérialisées qui gèrent la création de rapports de fin de mois. Cette technique, il est plus facile à administrer la base de données pour des opérations telles que la sauvegarde, mais elle nécessite également la taille du matériel permettant de gérer la charge maximale sur toutes les fonctions d’une application.

Si vous utilisez une architecture d’évolutivité dans la base de données de SQL Azure, il est judicieux de diviser les différentes fonctions d’une application dans différentes bases de données. À l’aide de cette technique, chaque application peut évoluer indépendamment. Une application devient plus occupée (et la charge sur la base de données), l’administrateur peut choisir les niveaux de performances indépendants pour chaque fonction dans l’application. À la limite, avec cette architecture, une application peut être supérieure à une machine de produit unique peut gérer dans la mesure où la charge est répartie sur plusieurs ordinateurs.

### <a name="batch-queries"></a>Requêtes par lots
Pour les applications qui accèdent aux données à l’aide de gros volumes, fréquentes, effectuez une requête ad hoc, une quantité importante de temps de réponse est passée sur la communication réseau entre la couche application et la couche de base de données de SQL Azure. Même lorsque l’application et la base de données de SQL Azure sont dans le même centre de données, la latence du réseau entre les deux peut être agrandie opérations d’accès par un grand nombre de données. Pour réduire le réseau round voyages pour les opérations d’accès aux données, envisagez d’utiliser l’option lot soit de requêtes ad hoc ou de les compiler que des procédures stockées. Si les requêtes ad hoc par lot, vous pouvez envoyer plusieurs requêtes sous la forme d’un traitement par lots volumineux en un seul déplacement à base de données de SQL Azure. Si vous compilez les requêtes ad hoc dans une procédure stockée, vous pourriez obtenir le même résultat que si vous les traitement par lots. À l’aide d’une procédure stockée vous offre également l’avantage d’augmenter les risques de mise en cache les plans de requête de base de données de SQL Azure afin de pouvoir utiliser à nouveau la procédure stockée.

Certaines applications sont gourmandes en écriture. Parfois, vous pouvez réduire la charge d’e/s sur une base de données en considérant comment traiter par lots les écritures simultanément. Souvent, cela est aussi simple que l’utilisation de transactions explicites au lieu des transactions de validation automatique dans les procédures stockées et les lots ad hoc. Pour une évaluation des différentes techniques que vous pouvez utiliser, consultez [Batching des techniques pour les applications de base de données SQL Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Faites des essais avec votre propre charge de travail pour trouver le bon modèle pour le traitement par lots. Veillez à comprendre qu’un modèle peut avoir la cohérence transactionnelle légèrement différentes garanties. Recherche de la charge de travail droit réduit l’utilisation des ressources nécessite de trouver la bonne combinaison de performances et la cohérence des compromis.

### <a name="application-tier-caching"></a>La mise en cache d’une application de couche
Certaines applications de base de données ont des charges de travail lourdes en lecture. La mise en cache des calques peut réduire la charge sur la base de données et peut réduire potentiellement le niveau de performances requis pour prendre en charge une base de données à l’aide de base de données de SQL Azure. Avec [Cache de Redis d’Azure](https://azure.microsoft.com/services/cache/), si vous disposez d’une charge de travail lourdes en lecture, vous pouvez lire les données une seule fois (ou peut-être une fois par ordinateur de la couche application, en fonction de la façon dont il est configuré), puis stocker ces données à l’extérieur de votre base de données SQL. Il s’agit d’un moyen de réduire la charge de la base de données (processeur et e/s de lecture), mais il existe un effet sur la cohérence comme les données lues dans le cache peuvent être synchronisées avec les données dans la base de données. Bien que dans de nombreuses applications, un niveau d’incohérence est acceptable, qui n’est pas vrai pour toutes les charges de travail. Vous devez bien comprendre les exigences de l’application avant d’implémenter une stratégie de mise en cache de couche application.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les niveaux de service, consultez les [performances et les options de base de données SQL](sql-database-service-tiers.md)
- Pour plus d’informations sur les pools élastique de la base de données, reportez-vous à la section [qu’est un pool de la base de données élastique Azure ?](sql-database-elastic-pool.md)
- Pour plus d’informations sur les performances et les pools élastique de la base de données, reportez-vous à la section [Quand envisager un pool élastique de la base de données](sql-database-elastic-pool-guidance.md)
