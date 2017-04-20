<properties
   pageTitle="Solutions de reprise après sinistre - SQL de base de données Active géo-réplication du nuage | Microsoft Azure"
   description="Apprenez à concevoir des solutions de reprise après sinistre de nuage pour la planification de la continuité d’activité commerciale à l’aide de la réplication géographique pour la sauvegarde de données d’application avec la base de données de SQL Azure."
   keywords="nuage de reprise après sinistre, les solutions de reprise après sinistre, sauvegarde de données d’application, géo-réplication, planification de la continuité d’activité"
   services="sql-database"
   documentationCenter=""
   authors="anosov1960"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="07/20/2016"
   ms.author="sashan"/>

# <a name="design-an-application-for-cloud-disaster-recovery-using-active-geo-replication-in-sql-database"></a>Conception d’une application de reprise après sinistre de cloud à l’aide de géo-réplication Active dans la base de données de SQL


> [AZURE.NOTE] [Geo-réplication Active](sql-database-geo-replication-overview.md) est maintenant disponible pour toutes les bases de données de tous les niveaux.



Apprenez à utiliser des [Géo-réplication Active](sql-database-geo-replication-overview.md) dans la base de données de SQL aux applications de base de données conception robuste échecs régionales et des pannes catastrophiques. Pour la planification de la continuité d’activité commerciale, pensez à la topologie de déploiement d’application, le contrat de niveau de service cible, la latence du trafic et les coûts. Dans cet article, nous examinons des modèles d’application courants et présentez les avantages et les inconvénients de chaque option. Pour plus d’informations sur géo-réplication Active avec les Pools élastique, voir [stratégies de récupération d’urgence Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Modèle de conception 1 : déploiement actif-passif de reprise après sinistre de nuage avec une base de données situé

Cette option convient mieux aux applications présentant les caractéristiques suivantes :

+ Instance active dans une seule région Azure
+ Forte dépendance sur l’accès aux données en lecture-écriture (RW)
+ Connectivité entre-région entre la logique d’application et la base de données n’est pas acceptable en raison de la latence et le trafic de coût    

Dans ce cas, la topologie de déploiement de l’application est optimisée pour le traitement des sinistres régionaux lorsque tous les composants d’application sont concernés et doivent de basculement en tant qu’unité. Pour la redondance géographique, la logique de l’application et la base de données sont répliqués dans une autre région, mais ils ne sont pas utilisés pour l’application de la charge de travail dans les conditions normales. L’application dans la zone secondaire doit être configurée pour utiliser une chaîne de connexion SQL à la base de données secondaire. Gestionnaire de trafic est configuré pour utiliser la [méthode de routage de basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [AZURE.NOTE] [Gestionnaire de trafic Azure](../traffic-manager/traffic-manager-overview.md) est utilisé tout au long de cet article à des fins d’illustration uniquement. Vous pouvez utiliser n’importe quelle solution d’équilibrage de la charge qui prend en charge la méthode de routage de basculement.    

Outre les instances de l’application principale, vous devez envisager de déployer une petite [application de rôle worker](cloud-services-choose-me.md#tellmecs) qui surveille votre base de données principale à l’aide de T-SQL en lecture seule (RO) commandes régulières. Vous pouvez l’utiliser pour déclencher automatiquement le basculement, pour générer une alerte sur la console d’administration de votre application, ou les deux. Pour vous assurer que la surveillance n’est pas concernée par les interruptions de service à l’échelle de la région, vous devez déployer les instances d’application analyse à chaque région et avoir chaque instance connecté à la base de données primaire dans la région principale et la base de données secondaire dans la région. 

> [AZURE.NOTE] Les applications de surveillance doivent être active et sonde de bases de données principales et secondaires. Ce dernier peut servir à détecter une défaillance dans la zone secondaire et une alerte lorsque l’application n’est pas protégée.     

Le diagramme suivant illustre cette configuration avant une panne.

![Configuration de géo-réplication de base de données SQL. Reprise après sinistre en nuage.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Après une panne dans la région principale, l’application de surveillance détecte que la base de données primaire n’est pas accessible et qu’il enregistre une alerte. En fonction de vos SLA de l’application, vous pouvez décider combien de sondes surveillance consécutives doivent échouer avant qu’il déclare une panne de la base de données. Pour obtenir les coordonnées de basculement de la fin de l’application et la base de données, l’application de surveillance doivent être les étapes suivantes :

1. [Mettre à jour le statut de la fin principale](https://msdn.microsoft.com/library/hh758250.aspx) pour déclencher le basculement des points de terminaison.
2. Appelez la base de données secondaire pour [déclencher le basculement de la base de données](sql-database-geo-replication-portal.md).

Après le basculement, l’application traite les demandes de l’utilisateur dans la zone secondaire mais restera cohabiter avec la base de données car la base de données principale se trouve désormais dans la zone secondaire. Ce scénario est illustré par le diagramme suivant. Dans tous les schémas, les lignes continues indiquent les connexions actives, lignes en pointillés indiquent les connexions suspendues et signes « stop » indiquent les déclencheurs de l’action.


![Geo-réplication : Le basculement vers une base de données secondaire. Sauvegarde de données d’application.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Si une panne se produit dans la zone secondaire, le lien de réplication entre le serveur principal et la base de données secondaire est interrompu et l’application de surveillance enregistre une alerte que la base de données primaire est exposée. Cela n’affecte pas les performances de l’application, mais que l’application fonctionne exposés et donc à un risque plus élevé dans le cas de deux régions échouent à la suite.

> [AZURE.NOTE] Nous ne recommandons que les configurations de déploiement avec une seule région de reprise après sinistre. C’est parce que la plupart de la répartition géographique Azure a deux régions. Ces configurations ne protège pas votre application à partir d’un échec catastrophique de deux régions. Dans un cas peu probable d’une telle défaillance, vous pouvez récupérer vos bases de données dans une région tiers à l’aide de [géo - restauration](sql-database-disaster-recovery.md#recovery-using-geo-restore).

Une fois que la panne est atténuée, la base de données secondaire est automatiquement synchronisé avec le serveur principal. Lors de la synchronisation, les performances de l’ordinateur principal peuvent en pâtir légèrement en fonction de la quantité de données qui doivent être synchronisée. Le diagramme suivant illustre une panne dans la zone secondaire.

![Base de données secondaire synchronisée avec primaire. Reprise après sinistre en nuage.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


Principaux **avantages** de ce modèle de conception sont les suivantes :

+ La chaîne de connexion SQL est définie au cours du déploiement de l’application dans chaque région et ne change pas après le basculement.
+ Les performances de l’application ne sont pas concerné par le basculement que l’application et la base de données se trouvent toujours conjointement.

Le principal **inconvénient** est que l’instance d’application redondants dans la zone secondaire est uniquement utilisé pour la reprise après sinistre.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Modèle de Design 2 : déploiement actif pour l’équilibrage de charge d’application
Cette option de récupération après sinistre nuage est particulièrement adaptée aux applications présentant les caractéristiques suivantes :

+ Taux élevé de lectures écritures
+ Latence d’écriture de base de données n’affecte pas l’expérience de l’utilisateur final  
+ Logique en lecture seule peut être séparée de la logique de lecture-écriture à l’aide d’une chaîne de connexion différente
+ Logique en lecture seule ne dépend pas des données entièrement synchronisées avec les dernières mises à jour  

Si vos applications présentent ces caractéristiques, équilibrage de charge les connexions de l’utilisateur final sur plusieurs instances de l’application dans différentes régions peut améliorer les performances et l’expérience de l’utilisateur final. Pour implémenter l’équilibrage de la charge, chaque zone doit avoir une instance active de l’application avec la logique d’en lecture-écriture (RW) connectée à la base de données primaire dans la région principale. La logique en lecture seule (RO) doit être connectée à une base de données secondaire dans la même région que l’instance de l’application. Gestionnaire de trafic doit être configuré pour utiliser [alternée routage](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md) ou le [routage de performances](../traffic-manager/traffic-manager-configure-performance-routing-method.md) avec [contrôle de point de terminaison](../traffic-manager/traffic-manager-monitoring.md) pour chaque instance de l’application.

Comme dans modèle #1, vous devez envisager le déploiement d’une application de surveillance similaire. Mais, contrairement au modèle #1, l’application de surveillance ne sera pas chargée de déclencher le basculement sur incident de point de terminaison.

> [AZURE.NOTE] Bien que ce modèle utilise plusieurs bases de données secondaires, seul des copies secondaires serait utilisé pour le basculement pour les raisons que nous l’avons dit. Ce modèle requiert un accès en lecture seule sur le site secondaire, il doit être géo-réplication Active.

Gestionnaire de trafic doit être configuré pour des performances de routage pour diriger les connexions utilisateur à l’instance d’application le plus proche de l’emplacement géographique de l’utilisateur. Le diagramme suivant illustre cette configuration avant une panne.
![Aucune interruption de service : performances de routage vers le plus proche d’application. Geo-réplication.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Si une panne de la base de données est détectée dans la région principale, vous lancez la reprise de la base de données principal pour une zone secondaire, modification de l’emplacement de la base de données principale. Le Gestionnaire de trafic automatiquement exclut le point final en mode hors connexion à partir de la table de routage, mais continue de routage du trafic d’utilisateur final pour les autres instances en ligne. Étant donné que la base de données primaire est maintenant dans une région différente, toutes les instances en ligne doivent modifier leur chaîne de connexion SQL en lecture-écriture pour vous connecter à la nouvelle primaire. Il est important d’effectuer cette modification avant de lancer le basculement de la base de données. Comme ils pointent toujours vers la base de données dans la même région, les chaînes de connexion SQL en lecture seule doivent demeurer inchangés. Les opérations de basculement sur incident sont les suivantes :  

1. Modifier les chaînes de connexion SQL en lecture-écriture pour pointer vers la nouvelle primaire.
2. Appelez la base de données secondaire désigné dans l’ordre de [basculement de lancement de la base de données](sql-database-geo-replication-portal.md).

Le diagramme suivant illustre la nouvelle configuration après le basculement.
![Configuration après basculement sur incident. Reprise après sinistre en nuage.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

Dans le cas d’une panne dans une zone secondaire, le Gestionnaire de trafic supprime automatiquement le point de terminaison en mode hors connexion dans cette région à partir de la table de routage. Le canal de réplication pour la base de données secondaire dans cette région est suspendu. Les autres zones recevoir le trafic des utilisateurs supplémentaires dans ce scénario, les performances de l’application a un impact sur la panne. Une fois que la panne est atténuée, la base de données secondaire dans la région d’impactés est immédiatement synchronisée avec le serveur principal. Lors de la synchronisation principale de pourrait être légèrement impact sur les performances en fonction de la quantité de données qui doivent être synchronisée. Le diagramme suivant illustre une panne dans une zone secondaire.

![Panne dans zone secondaire. Reprise après sinistre - géo-réplication du nuage.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

Le principal **avantage** de ce modèle de conception est que vous pouvez adapter la charge de travail des applications entre plusieurs zones secondaires pour obtenir les performances optimales de l’utilisateur final. Les **compromis** de cette option sont les suivants :

+ En lecture-écriture des connexions entre les instances de l’application et la base de données ont des variables de latence et de coût
+ Performances de l’application a un impact sur la panne
+ Instances de l’application sont nécessaires pour modifier dynamiquement la chaîne de connexion SQL après le basculement de la base de données.  

> [AZURE.NOTE] Une approche similaire peut être utilisée afin de décharger les charges de travail spécialisées telles que la création de rapports, outils d’analyse décisionnelle ou de sauvegarde. Ces charges de travail consomment généralement des ressources de base de données importante par conséquent, il est recommandé que vous désignez une base de données secondaires pour les avec le niveau de performance correspondant à la charge de travail escomptée.

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Modèle 3 : déploiement actif-passif pour la conservation des données  
Cette option convient mieux aux applications présentant les caractéristiques suivantes :

+ Toute perte de données est un risque élevé. Le basculement de la base de données utilisable uniquement en dernier recours si la panne est définitive.
+ L’application peut fonctionner en mode « lecture seule » pour une période donnée.

Dans ce modèle, l’application passe en mode lecture seule lorsqu’il est connecté à la base de données secondaire. La logique d’application dans la région principale est cohabiter avec la base de données primaire et fonctionne en mode de lecture-écriture (RW). La logique d’application dans la zone secondaire est cohabiter avec la base de données secondaire et est prête à fonctionner en mode lecture seule (RO).  Gestionnaire de trafic doit être configuré pour utiliser le [routage de basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md) avec [contrôle de point de terminaison](../traffic-manager/traffic-manager-monitoring.md) pour les deux instances de l’application.

Le diagramme suivant illustre cette configuration avant une panne.
![Déploiement actif-passif avant le basculement. Reprise après sinistre en nuage.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Lorsque le Gestionnaire de trafic détecte un échec de connexion à la zone principale, il bascule automatiquement le trafic des utilisateurs à l’instance de l’application dans la zone secondaire. Avec ce modèle, il est important que vous **ne le faites pas** de lancer le basculement de la base de données après que la panne est détectée. L’application dans la zone secondaire est activée et fonctionne en mode lecture seule à l’aide de la base de données secondaire. Ceci est illustré par le diagramme suivant.

![Interruption de service : L’Application en mode lecture seule. Reprise après sinistre en nuage.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Une fois l’interruption dans la région principale est atténuée, Gestionnaire de trafic détecte la restauration de la connectivité dans la région principale commutateurs et le trafic des utilisateurs dans l’instance de l’application dans la région principale. Cette instance de l’application reprend et fonctionne en mode lecture-écriture à l’aide de la base de données principale.

> [AZURE.NOTE] Dans la mesure où ce modèle exige un accès en lecture seule sur le site secondaire il nécessite géo-réplication Active.

Dans le cas d’une coupure dans la zone secondaire, le Gestionnaire de trafic marque la fin de l’application dans la région principale « dégradé », et le canal de réplication est suspendu. Toutefois, cette panne n’affecte pas les performances de l’application au cours de la panne. Une fois que la panne est atténuée, la base de données secondaire est immédiatement synchronisée avec le serveur principal. Lors de la synchronisation principale de pourrait être légèrement impact sur les performances en fonction de la quantité de données qui doivent être synchronisée.

![Arrêt : Base de données secondaire. Reprise après sinistre en nuage.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Ce modèle de conception présente plusieurs **avantages**:

+ Il évite la perte de données pendant les interruptions de service temporaires.
+ Il ne nécessite pas de vous permet de déployer une application de surveillance, que la restauration est déclenchée par le Gestionnaire de trafic.
+ Interruptions de service ne dépendant que de la vitesse à laquelle le Gestionnaire de trafic détecte l’échec de la connexion, qui peut être configuré.

Les **compromis** sont les suivants :

+ Application doit être capable de fonctionner en mode lecture seule.
+ Il est nécessaire de basculer dynamiquement à celui-ci lorsqu’il est connecté à une base de données en lecture seule.
+ La reprise des opérations de lecture-écriture nécessite la récupération de la région principale, ce qui signifie que l’accès aux données complet peut être désactivée pour heures, voire des jours.

> [AZURE.NOTE] Dans le cas d’une panne de service permanent dans la région, vous devez manuellement activer le basculement de la base de données et accepter la perte de données. L’application sera fonctionnelle dans la région secondaire avec un accès en lecture-écriture à la base de données.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planification de la continuité d’activité commerciale : choisir une conception d’application de reprise après sinistre de nuage

Votre stratégie de récupération après sinistre nuage spécifique peut combiner ou étendre ces modèles de conception pour mieux répondre aux besoins de votre application.  Comme mentionné précédemment, la stratégie que vous choisissez est basée sur le contrat SLA que vous souhaitez offrir à vos clients et de la topologie de déploiement d’application. Pour vous aider à prendre votre décision, le tableau suivant compare les choix en fonction de la perte de données estimées ou récupération de point (RPO) et estimation du temps de récupération (insérer).

| Modèle | RPO | INSÉR
| :--- |:--- | :---
| Déploiement actif-passif de reprise après sinistre avec un accès de base de données situés | Accès en lecture-écriture < 5 s | Temps de détection de défaillance, appeler des API de basculement + vérification des applications de test
| Déploiement d’actif pour l’équilibrage de charge d’application | Accès en lecture-écriture < 5 s | Temps de détection de panne + appel d’API de basculement + chaîne de connexion SQL modifier + vérification des applications de test
| Déploiement actif-passif pour la conservation des données | Accès en lecture seule < 5 s d’accès en lecture / écriture = zéro | Accès en lecture seule = temps de détection de panne de connectivité + test de vérification d’application <br>Accès en lecture-écriture = temps pour atténuer la panne

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations sur les sauvegardes de base de données SQL Azure automatisé, consultez [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md)
- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [restauration d’une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Active-géo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, voir [copie de la base de données](sql-database-copy.md)
- Pour plus d’informations sur géo-réplication Active avec les Pools élastique, voir [stratégies de récupération d’urgence Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
