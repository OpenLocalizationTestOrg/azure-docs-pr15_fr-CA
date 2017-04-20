<properties
   pageTitle="Des sauvegardes SQL Data Warehouse | Microsoft Azure"
   description="Obtenir des informations sur les sauvegardes de base de données intégrée SQL Data Warehouse qui vous permettent de restaurer un entrepôt de données de SQL Azure à un point de restauration ou d’une région géographique différente."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lakshmi1812"
   manager="barbkess"
   editor="monicar"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="lakshmir;barbkess"/>

# <a name="sql-data-warehouse-backups"></a>Sauvegardes SQL Data Warehouse

SQL Data Warehouse propose des sauvegardes locales et géographiques dans le cadre de ses fonctions de sauvegarde de magasin de données. Notamment les snapshots de Blob de stockage Azure et stockage redondant geo. Utilisez des sauvegardes d’entrepôt de données pour restaurer votre entrepôt de données à un point de restauration dans la région principale, ou restaurer à une région géographique différente. Cet article explique les caractéristiques de sauvegardes SQL Data Warehouse.

## <a name="what-is-a-data-warehouse-backup"></a>Ce qu’est une sauvegarde de magasin de données ?

Une sauvegarde de magasin de données consiste les données que vous pouvez utiliser pour restaurer un data warehouse à une heure spécifique.  Dans la mesure où SQL Data Warehouse est un système distribué, une sauvegarde de magasin de données se compose de nombreux fichiers qui sont stockés dans le BLOB Azure. 

Les sauvegardes de base de données sont un élément essentiel de toute stratégie de récupération après incident et de continuité d’activité business car elles protègent vos données de toute corruption accidentelle ou de suppression. Pour plus d’informations, consultez [vue d’ensemble de la continuité de l’activité commerciale](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Redondance des données

SQL Data Warehouse protège vos données en stockant vos données dans le stockage de prime Azure localement redondants (LRS). Cette fonctionnalité de stockage Azure stocke plusieurs copies synchrones des données dans le centre de données locales, de garantir la protection de données transparent s’il y a défaillance localisée. La redondance des données garantit que vos données peuvent survivre à des problèmes d’infrastructure tels que les défaillances de disque. La redondance des données garantit la continuité d’activité avec une tolérance de pannes et d’une infrastructure hautement disponible.

Pour en savoir plus sur :

- Le stockage Azure Premium, consultez [Introduction au stockage de prime Azure](../storage/storage-premium-storage.md).
- Stockage redondant localement, voir [réplication de stockage Azure](../storage/storage-redundancy.md#locally-redundant-storage).


## <a name="azure-storage-blob-snapshots"></a>Instantanés de stockage Blob Azure

Comme des avantages de l’utilisation du stockage de prime d’Azure, SQL Data Warehouse utilise des captures instantanées de Blob de stockage Azure localement l’entrepôt de données de sauvegarde. Vous pouvez restaurer un entrepôt de données à un point de restauration du snapshot. Des snapshots toutes les huit heures au minimum et sont disponibles pendant sept jours.  

Pour en savoir plus sur :

- Instantanés de blob Azure, consultez [créer une capture instantanée de blob](../storage/storage-blob-snapshots.md).


## <a name="geo-redundant-backups"></a>Sauvegardes geo redondant

Toutes les 24 heures, SQL Data Warehouse stocke le data warehouse complet de stockage Standard. L’entrepôt de données complet est créé pour le correspond à l’heure de la dernière capture instantanée. Le stockage standard appartient à un compte de stockage redondant géo accès en lecture (RA-GRS). La fonctionnalité de stockage de Azure RA-GRS réplique les fichiers de sauvegarde à un [Centre de données associés](../best-practices-availability-paired-regions.md). Cette réplication géographique garantit que vous pouvez restaurer le magasin de données au cas où vous ne pouvez pas accéder les snapshots dans votre région principale. 

Cette fonctionnalité est activée par défaut. Si vous ne souhaitez pas utiliser les sauvegardes geo redondant, vous pouvez choisir de. 

>[AZURE.NOTE] Dans le stockage Azure, la *réplication* du terme fait référence à la copie des fichiers d’un emplacement à un autre. Le de SQL *réplication de la base de données* fait référence à maintenir plusieurs bases de données secondaire synchronisée avec une base de données principale. 

Pour en savoir plus sur :
- Stockage redondant géo, voir [réplication de stockage Azure](../storage/storage-redundancy.md).
- Stockage de RA-GRS, consultez [stockage redondant geo d’accès en lecture](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Planification de la sauvegarde et de la période de rétention de l’entrepôt de données

SQL Data Warehouse crée des instantanés de vos entrepôts de données en ligne toutes les quatre à huit heures et conserve chaque instantané pendant sept jours. Vous pouvez restaurer votre base de données en ligne à l’un des points de restauration au cours des sept derniers jours. 

Pour voir le démarrage de la dernière capture instantanée, exécutez cette requête sur le magasin de données SQL en ligne. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Si vous avez besoin de conserver une capture instantanée de plus de sept jours, vous pouvez restaurer un point de restauration pour un nouvel entrepôt de données. Une fois la restauration terminée, SQL Data Warehouse commence la création des captures instantanées sur le nouveau data warehouse. Si vous ne faites pas les modifications vers le nouvel entrepôt de données, les instantanés restent vides et par conséquent le coût de la capture instantanée est minime. Vous pourriez également suspendre la base de données pour empêcher la création d’instantanés SQL Data Warehouse.


### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>Que se passe-t-il à mon de conservation alors que mon magasin de données est suspendue ?

SQL Data Warehouse ne crée pas de snapshots et n’expire pas de snapshots pendant la suspension d’un data warehouse. L’âge de la capture instantanée ne change pas lorsque l’entrepôt de données est en pause. Rétention d’instantané est basée sur le nombre de jours de que l’entrepôt de données est en ligne, pas les jours de calendrier.

Par exemple, si une capture instantanée démarre le 1 octobre à 16 h 00 et le data warehouse est suspendu le 3 octobre à 16 h 00, la capture instantanée est de deux jours. Chaque fois que le data warehouse se remet en ligne la capture instantanée est de deux jours. Si l’entrepôt de données est en ligne le 5 octobre à 16 h 00, la capture instantanée est de deux jours et le reste pendant plus de cinq jours.

Lorsque l’entrepôt de données revient en ligne, SQL Data Warehouse reprend les nouveaux clichés et expiration des snapshots lorsqu’ils ont plus de sept jours de données.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>Combien de temps est la période de rétention pour un entrepôt de données déplacées ?
Lors de la suppression d’un data warehouse, le data warehouse et les captures instantanées sont enregistrées pendant sept jours et puis supprimées. Vous pouvez restaurer l’entrepôt de données à un des points de restauration enregistrés.

> [AZURE.IMPORTANT] Si vous supprimez une instance de serveur SQL logique, toutes les bases de données qui appartiennent à l’instance sont également supprimés et ne peuvent pas être récupérés. Vous ne pouvez pas restaurer un serveur supprimé.

## <a name="data-warehouse-backup-costs"></a>Coûts de sauvegarde de magasin de données

Le coût total de l’entrepôt de données principal et que sept jours d’instantanés d’Azure Blob est arrondi à la plus proche to. Par exemple, si votre data warehouse est de 1,5 To et les instantanés utilisent 100 Go, vous êtes facturé pour 2 To de données à des taux de prime le stockage Azure. 

>[AZURE.NOTE] Chaque capture instantanée est vide initialement et augmente à mesure que vous apportez des modifications au magasin de données principal. Toutes les captures instantanées augmentent en volume, comme les changements de magasin de données. Par conséquent, les frais de stockage pour les snapshots augmentent en fonction du taux de change.

Si vous utilisez le stockage redondant géo, vous payez des frais de stockage distinct. Le stockage redondant geo est facturé au taux standard de l’accès en lecture sur stockage redondant (RA-GRS).

Pour plus d’informations sur la tarification de l’entrepôt de données SQL, consultez [Tarification de magasin de données SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>L’utilisation de sauvegardes de base de données

La principale utilisation de sauvegardes de magasin de données SQL est de restaurer le data warehouse à l’un des points de restauration dans la période de rétention.  

- Pour restaurer un data warehouse SQL, voir [restaurer un entrepôt de données SQL](sql-data-warehouse-restore-database-overview.md).


## <a name="related-topics"></a>Rubriques connexes

### <a name="scenarios"></a>Scénarios

- Pour une vue d’ensemble de la continuité d’activité d’entreprise, consultez [vue d’ensemble de la continuité de l’activité commerciale](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

- Pour restaurer un data warehouse, consultez [restauration d’un data warehouse SQL](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

