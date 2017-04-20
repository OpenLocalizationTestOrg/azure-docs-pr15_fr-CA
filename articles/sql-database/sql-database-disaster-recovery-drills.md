<properties 
   pageTitle="Exercices de récupération d’urgence de base de données SQL | Microsoft Azure" 
   description="Découvrez les conseils et meilleures pratiques pour l’utilisation de la base de données de SQL Azure pour effectuer les exercices de récupération d’urgence qui vous aide à maintenir votre mission critique les applications professionnelles résistants aux pannes et des interruptions de service." 
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
   ms.date="07/31/2016"
   ms.author="sstein; sashan"/>

#<a name="performing-disaster-recovery-drill"></a>Exécution de perçage de récupération après sinistre

Il est recommandé que la validation de la compatibilité avec les applications de flux de travail de récupération est effectuée régulièrement. Vérifier le comportement de l’application et les conséquences de la perte de données ou l’interruption implique que le basculement est un art. Il est également une exigence par la plupart des normes de l’industrie dans le cadre de la certification de la continuité d’activité d’entreprise.

Effectuer un exercice de récupération après incident est constitué par :

- Simulation panne de couche de données
- Récupération 
- Valider la restauration de valider l’intégrité des applications

Selon la manière dont vous avez [conçu votre application pour la continuité d’activité](sql-database-business-continuity.md), le flux de travail d’exécuter la perceuse peut peuvent varier. Ci-après, nous décrivons les meilleures pratiques à réaliser un exercice de restauration après sinistre dans le contexte de base de données de SQL Azure. 

##<a name="geo-restore"></a>Geo-restauration

Pour éviter la perte de données potentielle lors de l’exécution d’un exercice de restauration après sinistre, nous vous recommandons la perceuse à l’aide d’un environnement de test en créant une copie de l’environnement de production et son utilisation pour vérifier le processus de basculement de l’application.
 
####<a name="outage-simulation"></a>Simulation de panne

Pour simuler la panne, vous pouvez supprimer ou renommer la base de données source. Cela entraînera l’échec de connexion d’application. 

####<a name="recovery"></a>Récupération

- Effectuer la géo-la restauration de la base de données dans un autre serveur, comme décrit [ici](sql-database-disaster-recovery.md). 
- Modifier la configuration de l’application pour se connecter à des bases de données restaurées et suivez le guide de [configurer une base de données après la restauration](sql-database-disaster-recovery.md) pour achever la récupération.

####<a name="validation"></a>Validation

- Terminer la perceuse en vérifiant la récupération de valider l’intégrité des application (chaînes de connexion, connexions, test des fonctionnalités de base ou autre part de validations de procédures d’approbations d’application standard).

##<a name="geo-replication"></a>Geo-réplication

Pour une base de données qui est protégé à l’aide de la réplication géographique l’exercice drill impliquera un basculement planifié vers la base de données secondaire. Le basculement planifié garantit que le serveur principal et les bases de données secondaires restent synchronisés lorsque les rôles sont activées. À la différence du basculement non planifié, cette opération n’implique pas de perte de données, afin que la perceuse qui peut être effectuée dans l’environnement de production. 

####<a name="outage-simulation"></a>Simulation de panne

Pour simuler la panne, vous pouvez désactiver l’application web ou un ordinateur virtuel connecté à la base de données. Ainsi, les échecs de connectivité pour les clients web.

####<a name="recovery"></a>Récupération

- Vérifiez que la configuration de l’application dans la région de la reprise après sinistre pointe vers le premier secondaire qui deviendra principal de nouveau accessible. 
- Effectuer [planifié de basculement](sql-database-geo-replication-powershell.md#initiate-a-planned-failover) pour rendre la base de données secondaire à un nouveau serveur principal
- Suivez le guide de [configuration d’une base de données après la restauration](sql-database-disaster-recovery.md) pour achever la récupération.

####<a name="validation"></a>Validation

- Terminer la perceuse en vérifiant la récupération de valider l’intégrité des application (chaînes de connexion, connexions, test des fonctionnalités de base ou autre part de validations de procédures d’approbations d’application standard).


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations sur les scénarios de continuité d’activité d’entreprise, reportez-vous à la section [scénarios de continuité d’activité](sql-database-business-continuity.md)
- Pour obtenir des informations sur les sauvegardes de base de données SQL Azure automatisé, consultez [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [restauration d’une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Active-géo-réplication](sql-database-geo-replication-overview.md)  
