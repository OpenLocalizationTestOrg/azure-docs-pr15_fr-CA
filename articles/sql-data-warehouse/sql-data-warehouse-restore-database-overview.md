<properties
   pageTitle="Restauration de SQL Data Warehouse | Microsoft Azure"
   description="Vue d’ensemble des options de restauration de base de données pour la récupération d’une base de données dans l’entrepôt de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/29/2016"
   ms.author="lakshmir;barbkess;sonyama"/>


# <a name="sql-data-warehouse-restore"></a>Restauration du magasin de données SQL

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Portail][]
- [PowerShell][]
- [RESTE][]

SQL Data Warehouse propose restaurations locales et géographiques dans le cadre de sa sinistre d’entrepôt de données restauration. Sauvegardes d’entrepôt de données permet de restaurer votre entrepôt de données à un point de restauration dans la région principale, ou utilisez les sauvegardes redondantes géo restaurer à une région géographique différente. Cet article explique les caractéristiques de la restauration d’un data warehouse.

## <a name="what-is-a-data-warehouse-restore"></a>En quoi consiste une restauration d’entrepôt de données ?

Une restauration d’entrepôt de données est un nouvel entrepôt de données qui est créée à partir d’une sauvegarde de configuration existant ou d’un entrepôt de données supprimées. L’entrepôt de données restaurées recrée l’entrepôt de données sauvegardées à un moment donné. Dans la mesure où SQL Data Warehouse est un système distribué, une restauration d’entrepôt de données est créée à partir de plusieurs fichiers de sauvegarde sont stockés dans le BLOB Azure. 

Restauration de la base de données est un élément essentiel de toute stratégie de récupération après sinistre et de continuité d’activité entreprise car elle crée de nouveau vos données après une corruption accidentelle ou de suppression.

Pour plus d’informations, voir :

-  [Sauvegardes SQL Data Warehouse](sql-data-warehouse-backups.md)
-  [Vue d’ensemble de la continuité de l’activité commerciale](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Points de restauration de magasin de données

Comme des avantages de l’utilisation du stockage de prime d’Azure, SQL Data Warehouse utilise des instantanés de Blob de stockage Azure pour sauvegarder le magasin de données principal. Chaque snapshot a un point de restauration qui représente l’heure de début de la capture instantanée. Pour restaurer un data warehouse, vous choisissez un point de restauration et émettez une commande de restauration.  

SQL Data Warehouse restaure toujours la sauvegarde d’un nouvel entrepôt de données. Vous pouvez conserver l’entrepôt de données restaurées et celle en cours ou supprimer un d’eux. Si vous souhaitez remplacer l’entrepôt de données en cours avec l’entrepôt de données restaurées, vous pouvez le renommer.

Si vous devez restaurer un entrepôt de données supprimés ou mis en pause, vous pouvez [créer un ticket de support](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Restauration geo redondant

Si vous utilisez le stockage redondant géographique, vous pouvez restaurer l’entrepôt de données pour votre [Centre de données associés](../best-practices-availability-paired-regions.md) dans une région géographique différente. L’entrepôt de données est restaurée à partir de la dernière sauvegarde quotidienne. 

## <a name="restore-timeline"></a>Restaurer la barre de planning

Vous pouvez restaurer une base de données à un point de restauration disponibles dans les sept derniers jours. Des snapshots toutes les quatre à huit heures et sont disponibles pendant sept jours. Lorsqu’une capture instantanée est plue de sept jours, elle expire et son point de restauration n’est plus disponible.

## <a name="restore-costs"></a>Les coûts de restauration

Les frais de stockage pour l’entrepôt de données restaurées sont facturé au taux de prime le stockage Azure. 

Si vous suspendez un entrepôt de données restaurées, vous sont facturés pour le stockage au taux de prime le stockage Azure. L’avantage de la suspension, que vous ne sont pas facturés pour les ressources informatiques DWU.

Pour plus d’informations sur la tarification de l’entrepôt de données SQL, consultez [Tarification de magasin de données SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Utilise pour la restauration

La principale utilisation de restauration d’entrepôt de données est pour récupérer les données après la perte de données accidentelle ou de corruption.

Vous pouvez également utiliser Restauration d’entrepôt de données afin de conserver une sauvegarde pour plus de sept jours. Une fois que la sauvegarde est restaurée, vous avez l’entrepôt de données en ligne et pouvez suspendre indéfiniment pour le calcul des économies. La base de données en pause entraîne des frais de stockage au taux de prime le stockage Azure. 

## <a name="related-topics"></a>Rubriques connexes

### <a name="scenarios"></a>Scénarios

- Pour une vue d’ensemble de la continuité d’activité d’entreprise, consultez [vue d’ensemble de la continuité de l’activité commerciale](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

Pour effectuer une restauration d’entrepôt de données, restauration à l’aide de :

- Azure portal, consultez la rubrique [restauration d’un data warehouse à l’aide du portail Azure](sql-data-warehouse-restore-database-portal.md)
- Applets de commande PowerShell, consultez la rubrique [restauration d’un data warehouse à l’aide des applets de commande PowerShell](sql-data-warehouse-restore-database-powershell.md)
- Placez les API, reportez-vous à la section [restauration d’un data warehouse à l’aide de l’API reste](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Vue d’ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTE]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
