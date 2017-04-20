<properties
   pageTitle="Créer un entrepôt de données SQL avec TSQL | Microsoft Azure"
   description="Apprenez à créer un entrepôt de données de SQL Azure avec TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Créer une base de données SQL Data Warehouse à l’aide de Transact-SQL (TSQL).

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Cet article vous montre comment créer un entrepôt de données SQL à l’aide de T-SQL.

## <a name="prerequisites"></a>Conditions préalables

Pour commencer, vous devez : 

- **Compte Azure**: visitez [D’essai Azure][] ou [Crédits d’Azure MSDN][] pour créer un compte.
- **Azure SQL server**: pour plus d’informations, consultez [créer un serveur logique de la base de données de SQL Azure avec le portail Azure][] ou [créer un serveur logique de la base de données de SQL Azure avec PowerShell][] .
- **Groupe de ressources**: utiliser le même groupe de ressources comme votre serveur Azure SQL ou reportez-vous à la section [comment créer un groupe de ressources][].
- **Environnement d’exécution de T-SQL**: vous pouvez utiliser [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][]ou [SSMS][] pour exécuter T-SQL.

> [AZURE.NOTE] Création d’un entrepôt de données SQL peut entraîner un nouveau service facturable.  Pour plus d’informations sur la tarification, consultez [tarification d’entrepôt de données SQL][] .

## <a name="create-a-database-with-visual-studio"></a>Créer une base de données avec Visual Studio

Si vous ne connaissez pas Visual Studio, consultez l’article [Requêtes Azure SQL Data Warehouse (Visual Studio)][].  Pour démarrer, ouvrez l’Explorateur d’objets SQL Server dans Visual Studio et connectez-vous au serveur qui héberge votre base de données SQL Data Warehouse.  Une fois connecté, vous pouvez créer un entrepôt de données SQL en exécutant la commande SQL suivante sur la base de données **maître** .  Cette commande crée la base de données MySqlDwDb avec un objectif de DW400 de Service et permettre à la base de données atteindre une taille maximale de 10 To.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Créer une base de données avec sqlcmd

Vous pouvez également exécuter la même commande avec sqlcmd en exécutant la commande suivante à une invite de commande.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Le classement par défaut n’est ne pas spécifié est SQL_Latin1_General_CP1_CI_AS d’assembler.  Le `MAXSIZE` peut être compris entre 250 Go et 240 to.  Le `SERVICE_OBJECTIVE` peut être comprise entre DW100 et DW2000 [DWU][].  Pour obtenir une liste de toutes les valeurs valides, consultez la documentation de MSDN pour [Créer la base de données][].  Le paramètre MAXSIZE et SERVICE_OBJECTIVE peuvent être modifiée avec une commande de T-SQL [ALTER DATABASE][] .  Le classement d’une base de données ne peut pas être modifié après la création.   Avec précaution lors de la modification de la SERVICE_OBJECTIVE en tant que la modification de DWU provoque un redémarrage des services, qui annule toutes les requêtes en cours de vol.  Modification MAXSIZE ne redémarre pas les services comme c’est juste une opération simple de métadonnées.

## <a name="next-steps"></a>Étapes suivantes

Après votre entrepôt de données SQL a terminé la mise en service vous pouvez [charger des exemples de données][] ou Découvrez comment [développer][], [charger][]ou [migrer][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Requête SQL Azure Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[effectuer la migration]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md
[charge]: sql-data-warehouse-overview-load.md
[charger des exemples de données]: sql-data-warehouse-load-sample-databases.md
[Créer un serveur logique de base de données de SQL Azure avec le portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Créer un serveur logique de base de données de SQL Azure avec PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Comment faire pour créer un groupe de ressources]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CRÉER LA BASE DE DONNÉES]: https://msdn.microsoft.com/library/mt204021.aspx
[MODIFIER LA BASE DE DONNÉES]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Prix de l’entrepôt de données SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Version d’essai Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN crédits Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
