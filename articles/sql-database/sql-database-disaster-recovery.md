<properties
   pageTitle="Récupération d’urgence de base de données SQL | Microsoft Azure"
   description="Découvrez comment restaurer une base de données à partir d’un centre de données régional ou panne Azure SQL de base de données Active géo-réplication, possibilités d’et géo-restauration."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurer une base de données de SQL Azure ou le basculement sur incident sur un site secondaire

Une base de données SQL Azure offre les fonctionnalités suivantes pour la récupération en cas de panne :

- [Geo-réplication Active](sql-database-geo-replication-overview.md)
- [Geo-restauration](sql-database-recovery-using-backups.md#point-in-time-restore)

Pour obtenir des informations sur les scénarios de continuité d’activité d’entreprise et les fonctionnalités de prise en charge de ces scénarios, consultez [la continuité d’activité](sql-database-business-continuity.md).

### <a name="prepare-for-the-event-of-an-outage"></a>Préparer pour l’événement de panne de courant

Pour réussir avec récupération pour une autre région de données à l’aide de géo-réplication Active ou sauvegardes geo redondant, vous devez préparer un serveur dans un autre centre de données panne pour devenir le nouveau serveur primaire en cas de besoin se présentent ainsi ont bien définie d’étapes documentées et testées pour assurer une récupération sans heurts. Ces étapes de préparation sont les suivantes :

- Identifiez le serveur logique dans une autre zone à devenir le nouveau serveur principal. Avec Active géo-réplication, il s’agit d’au moins un et peut-être à chacun des serveurs secondaires. Geo-restauration, ceci sera généralement un serveur dans la [région associée](../best-practices-availability-paired-regions.md) pour la région dans laquelle se trouve votre base de données.
- Identifier et éventuellement définir, les règles de pare-feu au niveau du serveur nécessaires sur pour les utilisateurs à accéder à la nouvelle base de données principale.
- Déterminez comment vous allez rediriger les utilisateurs vers le nouveau serveur principal, tel qu’en modifiant les chaînes de connexion ou en modifiant les entrées DNS.
- Identifier et éventuellement de créer les connexions d’accès qui doivent être présents dans la base de données master sur le nouveau serveur primaire et garantir ces connexions disposent des autorisations appropriées dans la base de données master, le cas échéant. Pour plus d’informations, consultez [sécurité de base de données SQL après un sinistre](sql-database-geo-replication-security-config.md)
- Identifier les règles d’alerte qui devront être mis à jour pour faire correspondre à la nouvelle base de données principale.
- La configuration de l’audit dans la base de données principale en cours
- Effectuer une [reprise d’effectuer un zoom avant](sql-database-disaster-recovery-drills.md). Pour simuler une panne de géo-restauration, vous pouvez supprimer ou renommer la base de données source pour provoquer l’échec de connexion d’application. Pour simuler une panne de géo-réplication Active, vous pouvez désactiver l’application web ou virtuel connecté à la base de données ou la base de données de basculement pour provoquer des défaillances de l’application connectity.

## <a name="when-to-initiate-recovery"></a>Début de récupération

L’opération de restauration a une incidence sur l’application. Il requiert une modification de la chaîne de connexion SQL ou à l’aide de DNS et peut entraîner la perte définitive des données. Par conséquent, elle doit être effectuée uniquement lorsque la panne est susceptible de durer plus longtemps que l’objectif de temps de récupération de votre application. Lorsque l’application est déployée en production, vous devez effectuer une surveillance régulière de l’état de l’application et utiliser les points de données suivants pour affirmer que la récupération est justifiée :

1.  Échec de la connectivité permanente à partir de la couche application à la base de données.
2.  Le portail Azure affiche une alerte à propos d’un incident dans la région de forte incidence.
3.  Le serveur de base de données de SQL Azure est marqué comme étant dégradé.

En fonction de la tolérance de votre application à la responsabilité d’entreprise possibles et les interruptions de service, vous pouvez envisager les options de récupération suivantes.

Utilisez l' [Obtenir une base de données récupérables](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) pour obtenir le dernier point de restauration de réplication géographique.

## <a name="wait-for-service-recovery"></a>Attendez que la récupération de service

Le travail des équipes Azure assidûment pour restaurer la disponibilité des services se rapidement que possible, mais en fonction de la racine peut prendre des heures ou des jours.  Si votre application peut tolérer un temps d’arrêt important, que vous pouvez attendre pour la restauration. Dans ce cas, aucune action de votre part n’est requise. Vous pouvez voir l’état du service actuel sur notre [Tableau de bord de santé Azure Service](https://azure.microsoft.com/status/). La récupération de la région de disponibilité de votre application est restaurée.

## <a name="failover-to-geo-replicated-secondary-database"></a>Basculement vers géo-répliquer une base de données secondaire

Si les interruptions de service de votre application peuvent entraîner de responsabilité de l’entreprise vous devez utiliser les bases de données répliquées geo dans votre application. Il permettra à l’application restaurer rapidement la disponibilité dans une autre région dans le cas d’une panne. Découvrez comment [configurer la réplication de la zone géographique](sql-database-geo-replication-portal.md).

Pour restaurer la disponibilité des bases de données, vous devez initier le basculement vers le secondaire géo-répliquées à l’aide d’une des méthodes prises en charge.

Utilisez un des repères suivants pour le basculement vers une base de données secondaire géo-répliquées :

- [Basculement vers le secondaire géo-répliquées à l’aide du portail Azure](sql-database-geo-replication-portal.md)
- [Basculement vers le secondaire géo-répliquées à l’aide de PowerShell](sql-database-geo-replication-powershell.md)
- [Basculement vers le secondaire géo-répliquées à l’aide de T-SQL](sql-database-geo-replication-transact-sql.md)

## <a name="recover-using-geo-restore"></a>Récupérer à l’aide de géo-restauration

Si les interruptions de service de votre application n’entraînent pas de passif de l’entreprise, vous pouvez utiliser géo-restauration en tant que méthode pour restaurer vos bases de données d’application. Il crée une copie de la base de données depuis sa dernière sauvegarde geo redondant.

Utiliser les guides suivants géo-restaurer une base de données dans une nouvelle zone :

- [Geo-restaurer une base de données dans une nouvelle région à l’aide du portail Azure](sql-database-geo-restore-portal.md)
- [Geo-restaurer une base de données dans une nouvelle région à l’aide de PowerShell](sql-database-geo-restore-powershell.md)

## <a name="configure-your-database-after-recovery"></a>Configurer votre base de données après la restauration

Si vous utilisez le basculement de la réplication de la zone géographique ou géo-restauration pour récupérer en cas de panne, il se peut que vous devez vous assurer que la connectivité à nouvelles bases de données est correctement configurée pour que la fonction d’application normal peut reprendre. Il s’agit d’une liste des tâches pour préparer la production de votre base de données récupérée.

### <a name="update-connection-strings"></a>Mettre à jour les chaînes de connexion

Étant donné que votre base de données restaurée doit résider dans un autre serveur, vous devez mettre à jour la chaîne de connexion de votre application pour pointer vers ce serveur.

Pour plus d’informations sur la modification des chaînes de connexion, consultez le langage de développement appropriées pour votre [bibliothèque de connexions](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurer des règles de pare-feu

Vous devez vous assurer que les règles de pare-feu configurées sur le serveur et sur la base de données correspondent à ceux qui ont été configurés sur le serveur principal et de la base de données principale. Pour plus d’informations, consultez [Comment : configurer les paramètres de pare-feu (de base de données SQL Azure)](sql-database-configure-firewall-settings.md).


### <a name="configure-logins-and-database-users"></a>Configurer les connexions et les utilisateurs de la base de données

Vous devez vous assurer que toutes les connexions utilisées par votre application existent sur le serveur qui héberge votre base de données récupérée. Pour plus d’informations, consultez [Configuration de la sécurité pour la réplication de la zone géographique](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] Vous devez configurer et tester vos règles de pare-feu du serveur et les connexions (et leurs autorisations) au cours d’un exercice de restauration après sinistre. Ces objets au niveau du serveur et leur configuration peuvent être pas disponibles au cours de la panne.

### <a name="setup-telemetry-alerts"></a>Configuration des alertes de télémétrie

Vous devez vous assurer que vos paramètres de règle d’alerte existants sont mis à jour pour faire correspondre à la base de données récupérée et l’autre serveur.

Pour plus d’informations sur les règles d’alerte de base de données, reportez-vous à [Recevoir des Notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) et [l’Intégrité des services de suivi](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Activer l’audit

Si l’audit est requis pour accéder à votre base de données, vous devez activer l’audit après la restauration de la base de données. Un bon indicateur que l’audit est requis est que les applications clientes utilisent les chaînes de connexion sécurisée dans un modèle de *. database.secure.windows.net. Pour plus d’informations, consultez [mise en route de l’audit de base de données SQL](sql-database-auditing-get-started.md).


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations sur les sauvegardes de base de données SQL Azure automatisé, consultez [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md)
- Pour en savoir plus sur les scénarios de conception et de restauration de la continuité d’activité commerciale, reportez-vous à la section [scénarios de continuité d’activité](sql-database-business-continuity.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [restauration d’une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
