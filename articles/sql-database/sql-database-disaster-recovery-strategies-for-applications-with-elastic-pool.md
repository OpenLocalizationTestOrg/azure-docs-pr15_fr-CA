<properties 
   pageTitle="Conception de Solutions de nuage pour la reprise après sinistre à l’aide de géo-réplication de base de données SQL | Microsoft Azure"
   description="Apprenez à concevoir votre solution de cloud de reprise après sinistre en choisissant le modèle de basculement de droite."
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
   ms.workload="NA" 
   ms.date="07/16/2016"
   ms.author="sashan"/>

# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pool"></a>Stratégies de récupération après sinistre pour les applications utilisant le Pool élastique de base de données SQL 

Au fil des années, que nous avons appris que les services en nuage ne sont pas infaillibles et grave des incidents peut et va se produire. Base de données de SQL fournit un certain nombre de fonctionnalités pour fournir de la continuité de votre application lorsque ces incidents se produisent. [Pools élastiques](sql-database-elastic-pool.md) et autonome des bases de données prennent en charge le même type de reprise après sinistre. Cet article décrit plusieurs stratégies de reprise après sinistre pour élastique pools que tirer parti de ces fonctionnalités de continuité d’activité de la base de données SQL.

Pour les besoins de cet article, nous utiliserons le modèle d’application ISV de SaaS canonique :

<i>Un nuage moderne basées web application dispositions une SQL de base de données de chaque utilisateur final. L’éditeur de logiciels a un grand nombre de clients et utilise par conséquent de nombreuses bases de données, connus en tant que bases de données de clients. Étant donné que les bases de données de clients ont généralement des modèles d’activité imprévisibles, l’éditeur de logiciels utilise un pool élastique pour rendre la base de données de coût très prévisible sur de longues périodes. Le pool élastique simplifie également la gestion des performances lors de pics d’activité de l’utilisateur. En plus des bases de données clients l’application utilise également plusieurs bases de données pour gérer les profils utilisateur, sécurité, la collecte etc. des modèles d’utilisation. Disponibilité des locataires individuels n’affecte pas la disponibilité de l’application dans son ensemble. Toutefois, la disponibilité et les performances des bases de données de gestion est essentielle pour la fonction de l’application, et si les bases de données de gestion sont en mode hors connexion la totalité de l’application est en mode hors connexion.</i>  

Dans le reste de cet article, nous allons étudier les stratégies de reprise après sinistre couvrant des scénarios à partir des applications sensibles démarrage coût à celles avec des exigences de disponibilité strictes.  

## <a name="scenario-1-cost-sensitive-startup"></a>Scénario 1. Coût de démarrage sensible

<i>Je suis un professionnel de démarrage et que vous suis coût extrêmement sensible.  Pour simplifier le déploiement et la gestion de l’application souhaitée et je suis prêt à avoir un SLA limité pour les clients individuels. Mais je veux afin de garantir l’application comme un tout n’est jamais en mode hors connexion.</i>

Pour satisfaire à l’exigence de simplicité, vous devez déployer toutes les bases de données clients dans un pool élastique dans la région de votre choix de Azure et déployer les bases de données de gestion en tant que bases de données autonomes de réplication géographique. La récupération d’urgence de locataires, utilisez géo-restauration, qui est fourni sans frais supplémentaires. Pour garantir la disponibilité des bases de données de gestion, ils doivent être géo-répliquée dans une autre région (étape 1). Le coût de la configuration de récupération après sinistre dans ce scénario est égal au coût total de bases de données secondaires. Cette configuration est illustrée dans le diagramme suivant.

![Figure 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Dans le cas d’une panne dans la région principale, les étapes de récupération à mettre votre application en ligne sont illustrées par le diagramme suivant.

- Immédiatement basculement de la gestion des bases de données (2) à la zone de reprise après sinistre. 
- Modifier la chaîne de connexion de l’application pour pointer vers la zone de reprise après sinistre. Bases de données de clients et de tous les nouveaux comptes seront créées dans la région de la reprise après sinistre. Les clients existants verront leurs données temporairement indisponibles.
- Créer le pool élastique avec la même configuration que le pool d’origine (3). 
- Geo-restauration permet de créer des bases de données (4) copies du locataire. Vous pouvez envisager de déclencher les restaurations individuelles par les connexions de l’utilisateur final ou utiliser certains autre schéma de priorité spécifique d’application.

À ce stade votre application est en ligne dans la région de la reprise après sinistre, mais certains clients rencontreront délai lors de l’accès à leurs données.

![Figure 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Si la panne est temporaire, il est possible que la zone primaire est récupérée par Azure avant que toutes les restaurations soient terminées dans la région de la reprise après sinistre. Dans ce cas, vous devez orchestrer le déplacement de l’application à la région principale. Le processus prendra la procédure illustrée dans le diagramme suivant.
 
- Annuler toutes les requêtes de géo-restauration en attente.   
- Basculement de la base de données de gestion de la région principale (5). Remarque : Après la restauration de la région l’anciens primaires ont deviennent automatiquement des serveurs secondaires. Maintenant, ils seront à nouveau basculer rôles. 
- Modifier la chaîne de connexion de l’application pour qu’elle pointe vers la région principale. Maintenant les bases de données de clients et de tous les nouveaux comptes seront créées dans la région principale. Certains clients existants verront leurs données temporairement indisponibles.   
- Définir toutes les bases de données dans le pool de reprise après sinistre en lecture seule pour vous assurer qu’ils ne peuvent pas être modifiées dans la région de la reprise après sinistre (6). 
- Pour chaque base de données dans le pool de reprise après sinistre qui a été modifié depuis la restauration, renommer ou supprimer des bases de données correspondantes dans le pool principal (7). 
- Copier les bases de données mises à jour à partir du pool de reprise après sinistre dans le pool principal (8). 
- Supprimer le pool de reprise après sinistre (9)

À ce stade votre application sera en ligne dans la région principale de tous les clients bases de données disponibles dans le pool principal.

![Figure 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

La clé de **l’avantage** de cette stratégie est faible coûts couche la redondance des données. Les sauvegardes sont effectuées automatiquement par le service de base de données SQL avec aucune réécriture de l’application et sans coût supplémentaire.  Le coût est engagé uniquement lorsque les bases de données élastiques sont restaurés. Le **compromis** est que la restauration complète de toutes les bases de données clients prend beaucoup de temps. Il dépend le total nombre de restaurations vous lancerez dans la région de la reprise après sinistre et la taille globale des bases de données clients. Même si vous modifier la priorité des restaurations des certains locataires sur d’autres, vous sera en concurrence avec toutes les autres restaurations qui sont établies dans la même région, comme le service arbitrer et limiter pour minimiser l’impact global sur les bases de données de clients existante. En outre, la récupération des bases de données clients ne peut pas démarrer tant que le pool élastique dans la région de la reprise après sinistre est créé.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scénario 2. Application mature avec service hiérarchisé 

<i>Je suis une application SaaS mature avec des offres de services hiérarchisés et différents accords SLA pour les clients à l’essai et pour des clients payants. Pour les clients à l’essai, je dois réduire les coûts autant que possible. Clients à l’essai peuvent prendre le temps d’arrêt, mais je souhaite réduire sa probabilité. Pour les clients, les temps d’arrêt est un risque de vol. Et je souhaite s’assurer que le paiement clients peuvent toujours accéder à leurs données.</i> 

Pour prendre en charge ce scénario, vous devez séparer les locataires d’évaluation de locataires payées en les plaçant dans des pools séparés élastiques. Les clients à l’essai aurait eDTU inférieur par le locataire et SLA inférieur avec un temps de récupération plus long. Les clients seraient dans un pool de plus eDTU par les clients et un SLA plus élevé. Afin de garantir le temps de récupération le plus bas, bases de données clients payeur locataire doivent être répliquée geo. Cette configuration est illustrée dans le diagramme suivant. 

![Figure 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Comme dans le premier scénario, les bases de données de gestion seront très active afin que vous utilisez une base de données répliquée geo autonome pour (1). Ainsi, les performances prévisibles pour l’abonnement du client, les mises à jour de profil et les autres opérations de gestion. La région dans laquelle se trouvent les couleurs primaires de la base de données de gestion seront la principale région et la région dans laquelle se trouvent les serveurs secondaires des bases de données gestion sera la région de la reprise après sinistre.

Bases de données clients payeurs clients auront des bases de données actives dans le pool « payé » mis en service dans la région principale. Vous devez prévoir un pool secondaire portant le même nom dans la zone de reprise après sinistre. Chaque client serait géo-répliquées vers le pool secondaire (2). Cela permet une récupération rapide de toutes les bases de données de clients à l’aide de basculement. 

Si une panne se produit dans la région principale, les étapes de récupération à mettre votre application en ligne sont illustrées dans le diagramme suivant :

![Figure 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

- Bascule immédiatement les bases de données de gestion dans la zone de reprise après sinistre (3).
- Modifier la chaîne de connexion de l’application pour pointer vers la zone de reprise après sinistre. Maintenant les bases de données de clients et de tous les nouveaux comptes seront créées dans la région de la reprise après sinistre. Les clients d’évaluation existants verront leurs données temporairement indisponibles.
- Basculement bases de données du locataire payées au pool dans la région de la reprise après sinistre pour restaurer immédiatement leur disponibilité (4). Étant donné que le basculement est une modification du niveau de métadonnées rapide, vous pouvez envisager une optimisation où les basculements individuels sont déclenchés à la demande par les connexions de l’utilisateur final. 
- Si votre taille d’eDTU pool secondaire était inférieure à celui de l’ordinateur principal, car les bases de données secondaires requises uniquement la capacité de traiter les journaux des modifications alors qu’elles étaient secondaires, vous devez immédiatement augmenter la capacité du pool maintenant pour s’adapter à la charge de travail complète de tous les locataires (5). 
- Créer le nouveau pool élastique avec le même nom et la même configuration dans la région de reprise après sinistre pour les bases de données de clients d’évaluation (6). 
- Une fois le pool de clients d’évaluation est créé, utilisez géo-la fonction Restaurer pour restaurer les bases de données des clients d’évaluation dans le nouveau pool (7). Vous pouvez envisager de déclencher les restaurations individuelles par les connexions de l’utilisateur final ou utiliser certains autre schéma de priorité spécifique d’application.

À ce stade votre application est en ligne dans la région de la reprise après sinistre. Tous les clients ont accès à leurs données, alors que les clients à l’essai seront confrontés en retard accéder à leurs données.

Lorsque la région principale est récupérée par Azure *après* avoir restauré l’application dans la région de reprise après sinistre que vous pouvez poursuivre l’exécution de l’application de cette région ou décider de basculer sur la zone primaire. Si la zone principale est récupéré *avant* le processus de basculement est terminé, vous devez envisager de restauration immédiatement. La restauration prendra la procédure illustrée dans le diagramme suivant : 
 
![Figure 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

- Annuler toutes les requêtes de géo-restauration en attente.   
- Basculement de la base de données de gestion (8). Après la restauration de la région l’ancien serveur principal avait deviennent automatiquement l’image secondaire. Il devient le serveur principal à nouveau.  
- Basculement du fermage payé des bases de données (9). De même, après la restauration de la région les couleurs primaires anciens deviennent automatiquement les serveurs secondaires. Maintenant ils deviendront les couleurs primaires à nouveau. 
- Définir des bases de données d’essai restaurées qui ont été modifiés dans la région de la reprise après sinistre en lecture seule (10).
- Pour chaque base de données dans le pool de clients à l’essai DR qui a changé depuis la restauration, renommer ou supprimer la base de données correspondante dans le pool principal de clients à l’essai (11). 
- Copier les bases de données mises à jour à partir du pool de reprise après sinistre dans le pool principal (12). 
- Supprimer le pool de reprise après sinistre (13) 

> [AZURE.NOTE] L’opération de basculement sur incident est asynchrone. Pour réduire le temps de récupération, il est important d’exécuter commande de basculement des bases de données clients dans des lots d’au moins 20 bases de données. 

La clé de **l’avantage** de cette stratégie est qu’il fournit le contrat SLA plus élevé pour les clients. Elle garantit également que les nouvelles versions d’essai sont débloquées dès que le pool d’évaluation de la reprise après sinistre est créé. Le **compromis** est que cette configuration augmente le coût total des bases de données client en fonction du coût du pool DR secondaire pour les clients payants. En outre, si le pool secondaire a une taille différente, les clients rencontrer performances après basculement sur incident jusqu'à la fin de la mise à niveau de pool dans la région de la reprise après sinistre. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scénario 3. Applications distribuées avec service hiérarchisé

<i>J’ai une application SaaS mature avec des offres de services hiérarchisés. Je souhaite proposer un SLA très agressif à mes clients payants et réduire le risque d’impact lorsque des pannes se produisent car même brève interruption peut entraîner le mécontentement du client. Il est essentiel que les clients peuvent toujours accéder à leurs données. Les versions d’évaluation sont gratuites et un contrat SLA n’est pas disponible pendant la période d’évaluation.</i> 

Pour prendre en charge ce scénario, vous devez avoir trois pools élastiques distincts. Deux pools de taille égale à haute eDTUs par base de données doivent être mis en service dans deux régions différentes pour contenir les bases de données de clients clients payants. Le troisième pool contenant les locataires d’évaluation aurait un eDTUs inférieur par base de données et être mis en service dans une de la région de deux.

Afin de garantir le temps de récupération plus faible pendant les interruptions de service clients de paiement clients bases de données doivent être répliquée geo avec 50 % de bases de données primaires dans chacune des deux régions. De même, chaque région aurait 50 % de la base de données secondaire. Ainsi si une région est en mode hors connexion, 50 % seulement des bases de données de clients payants est affecté et seriez obligé de basculement. Les autres bases de données restent intactes. Cette configuration est illustrée dans le diagramme suivant :

![Figure 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Comme dans les scénarios précédents, les bases de données de gestion seront très active et vous devez les configurer comme autonome répliquées géo bases de données (1). Ainsi, les performances prévisibles de l’abonnement du client, les mises à jour de profil et les autres opérations de gestion. Région A serait la région principale pour les bases de données de gestion, et la zone B sera utilisée pour la récupération des bases de données de gestion.

Bases de données de clients payeurs clients seront également géo-répliquées, mais avec des couleurs primaires et secondaires réparties entre la zone A et la zone B (2). De cette façon, les bases de données primaire de clients affectés par la panne peut basculer vers l’autre région et deviennent disponibles. L’autre moitié des bases de données clients n’est pas affecté du tout. 

Le diagramme suivant illustre les étapes de récupération à entreprendre si une panne se produit dans la région A.

![Figure 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

- Bascule immédiatement les gestion des bases de données à la zone B (3).
- Modifier la chaîne de connexion de l’application pour pointer vers les bases de données de gestion dans la zone B. modifier la gestion des bases de données pour vous assurer que les nouveaux comptes et les bases de données clients seront créées dans la zone B et les bases de données de clients existante seront il n’y trouve ainsi. Les clients d’évaluation existants verront leurs données temporairement indisponibles.
- Basculement bases de données du locataire payées au pool 2 dans la zone de B restaurer immédiatement leur disponibilité (4). Étant donné que le basculement est une modification du niveau de métadonnées rapide, vous pouvez envisager une optimisation où les basculements individuels sont déclenchés à la demande par les connexions de l’utilisateur final. 
- Depuis maintenant pool 2 contient les seules bases de données primaires qu'augmente la charge de travail total dans le pool afin que vous devez augmentez immédiatement sa taille eDTU (5). 
- Créer le nouveau pool élastique avec le même nom et la même configuration de la zone B pour les bases de données d’évaluation clients (6). 
- Une fois la création du pool utiliser géo-la fonction Restaurer pour restaurer la base de données des clients d’évaluation dans le pool (7). Vous pouvez envisager de déclencher les restaurations individuelles par les connexions de l’utilisateur final ou utiliser certains autre schéma de priorité spécifique d’application.


> [AZURE.NOTE] L’opération de basculement sur incident est asynchrone. Pour réduire le temps de récupération, il est important d’exécuter commande de basculement des bases de données clients dans des lots d’au moins 20 bases de données. 

À ce stade votre application est en ligne dans la zone B. Tous les clients ont accès à leurs données, alors que les clients à l’essai seront confrontés en retard accéder à leurs données.

Lorsque la zone A est récupérée vous devez décider si vous souhaitez utiliser la zone B pour les clients à l’essai ou de restauration à l’aide de la liste des clients à l’essai dans une région A. Un critère peut être le % de bases de données des clients d’évaluation modifié depuis la récupération. Que cette décision, vous devrez rééquilibrer les locataires payées entre deux pools. le diagramme suivant illustre le processus lorsque les bases de données des clients d’évaluation restauré vers la région A.  
 
![Figure 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

- Annuler toutes les requêtes de géo-restauration en attente d’évaluation pool de reprise après sinistre.   
- Basculement de la base de données de gestion (8). Après la restauration de la région, l’ancien serveur principal avait est automatiquement devenu secondaire. Il devient le serveur principal à nouveau.  
- Sélectionnez les clients payants bases de données échoue au pool 1 et initier le basculement vers leurs secondaires (9). Après la restauration de la région toutes les bases de données dans le pool 1 est automatiquement devint secondaires. Maintenant 50 % d'entre elles redevient primaires. 
- Réduire la taille du pool de 2 à l’eDTU d’origine (10).
- Ensemble tous les restauré les bases de données d’essai dans la zone B en lecture seule (11).
- Pour chaque base de données dans le pool de reprise après sinistre d’évaluation qui ont été modifiées depuis la récupération, renommez ou supprimez la base de données correspondante dans le pool principal d’évaluation (12). 
- Copier les bases de données mises à jour à partir du pool de reprise après sinistre dans le pool principal (13). 
- Supprimer le pool de reprise après sinistre (14) 

Les principaux **avantages** de cette stratégie sont les suivants :

- Il prend en charge le SLA plus strictes pour les clients, car elle garantit qu’une panne ne peut pas avoir un impact sur plus de 50 % des bases de données clients. 
- Il garantit que les nouvelles versions d’essai sont débloquées dès que la piste DR pool est créée lors de la restauration. 
- Il permet une utilisation plus efficace de la capacité du pool que 50 % des bases de données secondaires dans le pool 1 et 2 de pool sont nécessairement moins actif puis les bases de données primaires.

Le principal, **les compromis** sont les suivants :

- Les opérations sur les bases de données de gestion aura le temps de latence réduits pour les utilisateurs finaux connectés à région A que pour les utilisateurs finaux connectés à région B comme ils sont exécutés par rapport à la partie principale de la base de données de gestion.
- Il nécessite une conception plus complexes de la base de données de gestion. Par exemple, chaque enregistrement de client devrait avoir une balise d’emplacement qui doit être modifié lors du basculement et restauration automatique.  
- Aux clients payants peuvent rencontrer des performances moindres que d’habitude jusqu'à la fin de la mise à niveau de pool dans la zone B. 

## <a name="summary"></a>Résumé

Cet article se concentre sur les stratégies de récupération d’urgence pour la couche de base de données utilisée par une application de mutualisée SaaS ISV. La stratégie que vous choisissez doit reposer sur les besoins de l’application, comme le modèle d’entreprise, le contrat SLA que vous souhaitez offrir à vos clients, budget contrainte etc... Chaque stratégie décrit présente les avantages et les compromis afin que vous pouvez prendre une décision éclairée. En outre, votre application inclura probablement des autres composants Azure. Ainsi, vous devez suivre leurs conseils de continuité d’activité métier et orchestrer la récupération de la couche de base de données avec les. Pour plus d’informations sur la gestion de la récupération des applications de base de données dans Azure, reportez-vous à la [conception de solutions de nuage pour la récupération d’urgence](./sql-database-designing-cloud-solutions-for-disaster-recovery.md) .  


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations sur les sauvegardes de base de données SQL Azure automatisé, consultez [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md)
- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [restauration d’une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Active-géo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, voir [copie de la base de données](sql-database-copy.md)
