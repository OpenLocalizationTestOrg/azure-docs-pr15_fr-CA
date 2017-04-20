<properties 
   pageTitle="Base de données SQL Azure Forum aux questions" 
   description="Réponses aux clients de questions courantes demandez nuage de bases de données et base de données de SQL Azure, système de gestion de base de données relationnelle (SGBDR) de Microsoft et de base de données en tant que service dans le nuage." 
   services="sql-database" 
   documentationCenter="" 
   authors="CarlRabeler" 
   manager="jhubbard" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="08/16/2016"
   ms.author="sashan;carlrab"/>

# <a name="sql-database-faq"></a>Base de données SQL Forum aux questions

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Comment l’utilisation de la base de données de SQL s’affiche sur ma facture ? 
Opérations de base de données SQL sur un taux horaire prévisible basé sur les deux le niveau de service + au niveau des performances pour les bases de données unique ou eDTUs par pool d’élastique de la base de données. L’utilisation réelle est calculée et au prorata toutes les heures, afin que votre facture peut afficher des fractions d’une heure. Par exemple, si une base de données existe pour 12 heures par mois, votre facture montre l’utilisation de 0,5 jours. En outre, les niveaux de service + au niveau des performances et d’eDTUs par pool sont décomposés dans la nomenclature pour le rendre plus facile à voir le nombre de jours de base de données utilisée pour chacun dans un seul mois.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Que se passe-t-il si une seule base de données est active pour moins d’une heure, ou utilise un niveau de service plus élevé pendant moins d’une heure ?
Vous êtes facturé pour chaque heure, il existe une base de données à l’aide de la couche de service plus élevée + d’un niveau de performances appliquées au cours de cette heure, quel que soit l’utilisation ou si la base de données a été active pendant moins d’une heure. Par exemple, si vous créez une base de données unique et supprimez cinq minutes plus tard votre facture reflète les frais d’heure d’une base de données. 

Exemples
    
- Si vous créez une base de données de base, puis immédiatement mettre à niveau Standard S1, vous sont facturés au tarif Standard S1 pour la première heure.

- Si vous mettez à niveau une base de données Basic à prime à 10:00. et de la mise à niveau se termine à 1 h 35. le jour suivant, vous sont facturées au taux de primes commençant à 01:00. 

- Si vous mettre à niveau une base de données à partir de la prime de base à 11:00 Il se termine à 2 h 15, puis la base de données est facturée au taux de la prime à 3 h, après laquelle il est facturé au taux de base.

## <a name="how-does-elastic-database-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Comment l’utilisation du pool élastique de la base de données s’affiche sur ma facture et que se passe-t-il lorsque je modifie eDTUs par pool ?
Frais de pool élastique de la base de données s’affichent sur votre facture sous DTUs élastique (eDTUs), les incréments indiqué sous eDTUs par pool dans [la page tarification](https://azure.microsoft.com/pricing/details/sql-database/). Il est gratuit par base de données des pools élastique de la base de données. Vous êtes facturé pour chaque heure qu'un pool existe à l’eDTU le plus élevé, quel que soit l’utilisation ou si le pool a été actif pendant moins d’une heure. 

Exemples

- Si vous créez un pool élastique Standard de la base de données avec 200 eDTUs à 11:18 h 00, ajout de cinq bases de données dans la liste, vous sont facturés pour 200 eDTUs pour l’heure ensemble, commençant à 11 h 00 dans le reste de la journée.
- Sur 2 jours, à 5 h 05, base de données 1 commence à consommer de 50 eDTUs et reste stable dans la journée. Bases de données de 2-5 varient entre 0 et 80 eDTUs. Au cours de la journée, vous ajoutez les cinq autres bases de données qui utilisent des variables eDTUs tout au long de la journée. Jour 2 est facturée à 200 eDTU de toute une journée. 
- Le jour 3, à 5 h vous ajoutez un autre 15 bases de données. Utilisation de la base de données augmente tout au long de la journée et le point où vous décidez d’augmenter les eDTUs pour le pool de 200 à 400, à 8 h 05 Frais au niveau 200 eDTU étaient en vigueur jusqu'à 20 h et s’élève à 400 eDTUs pour quatre heures restantes. 

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-database-pool-show-up-on-my-bill"></a>Comment l’utilisation de géo-réplication Active dans un pool d’élasticité de la base de données s’affiche sur ma facture ?
Contrairement aux bases de données individuelles, [Géo-réplication Active](sql-database-geo-replication-overview.md) avec les bases de données élastiques n’affiche pas un impact direct de facturation.  Vous ne paient l’eDTUs mis en service pour chaque pool (pool principal et secondaire pool)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Comment l’impact de l’utilisation de la fonctionnalité d’audit sur ma facture ? 
L’audit est intégré dans le service de base de données SQL sans supplémentaire de coût et est disponible pour les bases de données Basic, Standard et Premium. Toutefois, pour stocker les journaux d’audit, l’audit utilise qu'un compte de stockage Azure et les taux pour les tables et les files d’attente dans le stockage Azure s’appliquent en fonction de la taille de votre journal d’audit.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-database-pools"></a>Comment trouver le niveau de service adéquat à niveaux et des performances pour les bases de données individuelles et les pools élastique de la base de données ? 
Il existe quelques outils à votre disposition. 

- Pour les bases de données sur site, utilisez le [Conseiller de dimensionnement DTU](http://dtucalculator.azurewebsites.net/) afin de recommander les bases de données et DTUs requis et d’évaluer plusieurs bases de données pour les pools d’élasticité de la base de données.
- Si une seule base de données serait bénéficient d’un pool, moteur d’intelligent d’Azure recommande un pool élastique de la base de données s’il voit un modèle d’utilisation historiques qui justifie. Consultez [moniteur et de gérer un pool élastique de la base de données avec le portail Azure](sql-database-elastic-pool-manage-portal.md). Pour plus d’informations sur la façon de faire les calculs vous-même, reportez-vous à la section [Considérations de prix et de performances pour un pool d’élasticité de la base de données](sql-database-elastic-pool-guidance.md)
- Pour voir si vous devez composer une seule base de données vers le haut ou vers le bas, consultez le [Guide des performances des bases de données unique](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>À quelle fréquence puis-je pour modifier le niveau de service niveau ou les performances d’une base de données unique ? 
V12 de bases de données, vous pouvez modifier le niveau de service (entre basique, Standard et Premium) ou le niveau de performance au sein d’un niveau de service (par exemple, S1 à S2) aussi souvent que vous le souhaitez. Pour les anciennes bases de données, vous pouvez modifier le niveau de performances ou de niveau de service à un total de quatre fois dans une période de 24 heures.

##<a name="how-often-can-i-adjust-the-edtus-per-pool"></a>La fréquence à laquelle ajuster l’eDTUs par pool ? 
Aussi souvent que vous le souhaitez.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-database-pool"></a>Combien de temps faut-il pour modifier le niveau de service niveau ou les performances d’une base de données unique ou déplacer une base de données et un pool élastique de la base de données ? 
Modification de la couche de service d’une base de données et le déplacement dans un pool requièrent la base de données à copier sur la plate-forme sous la forme d’une opération en arrière-plan. Modification de la couche de service peut prendre de quelques minutes à plusieurs heures selon la taille des bases de données. Dans les deux cas, les bases de données restent en ligne et disponible au cours du déplacement. Pour plus d’informations sur la modification des bases de données individuelles, voir [modification de la couche de service d’une base de données](sql-database-scale-up.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quand dois-je utiliser une seule base de données et des bases de données élastiques ? 
En général, les pools élastique de la base de données sont conçus pour un [modèle de logiciel en tant que service (SaaS) application](sql-database-design-patterns-multi-tenancy-saas-applications.md)typique, où il existe une base de données par les clients ou les clients. Achat de bases de données individuelles et overprovisioning pour répondre à la variable et de pics de la demande pour chaque base de données sont souvent pas le coût efficace. Avec les pools, vous gérez les performances collectives du pool et les bases de données évoluer automatiquement. 

Moteur intelligent de d’Azure recommande un pool pour les bases de données lorsqu’un modèle d’utilisation le justifie. Pour plus d’informations, consultez [recommandations de niveau de tarification de la base de données SQL](sql-database-service-tier-advisor.md). Pour obtenir des instructions détaillées sur le choix entre les bases de données unique et souple, reportez-vous à la section [Considérations de prix et de performances pour les pools d’élasticité de la base de données](sql-database-elastic-pool-guidance.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>Que signifie avoir jusqu'à 200 % de votre espace de stockage maximale de la base de données mis en service pour le stockage de sauvegarde ? 
Stockage de sauvegarde est le stockage associé à vos sauvegardes de base de données automatisée qui sont utilisés pour [Point-de-temps restauration](sql-database-recovery-using-backups.md#-point-in-time-restore) et [Geo de restauration](sql-database-recovery-using-backups.md#geo-restore). Base de données SQL de Microsoft Azure fournit jusqu'à 200 % de votre espace de stockage mis en service maximale de la base de données de stockage de sauvegarde sans coût supplémentaire. Par exemple, si vous disposez d’une instance de base de données Standard avec une taille de base de données provisionnée de 250 Go, vous sont fournis avec 500 Go de stockage de sauvegarde sans frais supplémentaires. Si votre base de données dépasse le stockage de sauvegarde fourni, vous pouvez choisir de réduire la période de rétention en contactant le Support Azure ou à payer pour le stockage de sauvegarde supplémentaire facturé au taux standard de l’accès en lecture sur stockage redondant (RA-GRS). Pour plus d’informations sur la facturation de RA-GRS, consultez Détails de la tarification de stockage.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Je déplace à partir de l’entreprise/Web sur de nouveaux niveaux de service, que dois-je savoir ?
Bases de données SQL Web et Business Azure sont retraités maintenant. Les niveaux basique, Standard, Premium et élastique remplacent les bases de données Web et de l’entreprise retraite. Nous avons des questions supplémentaires qui devraient vous aider dans cette période de transition. [Forum aux questions du coucher de soleil Business Edition et Web](sql-database-web-business-sunset-faq.md)

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Ce qu’est un retard de réplication lors de la réplication géo une base de données entre les deux régions au sein de la même région Azure ?  
Nous sommes actuellement en charge un RPO de cinq secondes et le délai de réplication a été inférieure à que lorsque le géo-secondaire est hébergé dans le Azure recommandé région appariée et le même niveau de service.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Quel est un retard de réplication lorsque géo-secondaire est créée dans la même région, comme la base de données principale ?  
En fonction de données empiriques, il n’est pas trop différence entre intra-région et un retard de réplication de zone entre lorsque l’Azure recommandé appariée région est utilisée. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>S’il existe une défaillance du réseau entre les deux régions, comment la logique de nouvelle tentative fonctionne lorsque géo-réplication est configurée ?  
S’il existe une déconnexion, nous Réessayer toutes les 10 secondes pour rétablir les connexions.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Que puis-je faire pour garantir qu’une modification critique sur la base de données principale est répliquée ?
Geo-secondaire est une duplication asynchrone et nous n’essayez pas de faire parfaitement synchronisé avec le serveur principal. Mais nous fournit une méthode pour forcer la synchronisation pour garantir la réplication des modifications critiques (par exemple, les mises à jour du mot de passe). Synchronisation forcée affecte les performances, car il bloque le thread appelant jusqu'à ce que toutes les transactions validées sont répliquées. Pour plus d’informations, consultez [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Quels sont les outils disponibles pour surveiller le retard de réplication entre la base de données principale et géo-secondaire ?
Nous exposer le retard de réplication en temps réel entre la base de données primaire et géo-secondaire via une vue. Pour plus d’informations, consultez [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).
