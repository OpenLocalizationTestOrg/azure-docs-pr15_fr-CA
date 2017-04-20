<properties
   pageTitle="Créer un magasin de données SQL à l’aide de PowerShell | Microsoft Azure"
   description="Créer un magasin de données SQL à l’aide de PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-sql-data-warehouse-using-powershell"></a>Créer SQL Data Warehouse à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Cet article vous montre comment créer un entrepôt de données SQL à l’aide de PowerShell.

## <a name="prerequisites"></a>Conditions préalables

Pour commencer, vous devez :

- **Compte Azure**: visitez [D’essai Azure][] ou [Crédits d’Azure MSDN][] pour créer un compte.
- **Azure SQL server**: pour plus d’informations, consultez [créer un serveur logique de la base de données de SQL Azure avec le portail Azure][] ou [créer un serveur logique de la base de données de SQL Azure avec PowerShell][] .
- **Groupe de ressources**: utiliser le même groupe de ressources comme votre serveur Azure SQL ou reportez-vous à la section [comment créer un groupe de ressources][].
- **PowerShell version 1.0.3 ou supérieur**: vous pouvez vérifier votre version en exécutant **Get-Module - ListAvailable-nom Azure**.  La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][].  Pour plus d’informations sur l’installation de la version la plus récente, consultez [comment installer et configurer Azure PowerShell][].

> [AZURE.NOTE] Création d’un entrepôt de données SQL peut entraîner un nouveau service facturable.  Pour plus d’informations sur la tarification, consultez [tarification d’entrepôt de données SQL][] .

## <a name="create-a-sql-data-warehouse"></a>Créez un entrepôt de données SQL

1. Ouvrez Windows PowerShell.
2. Exécuter cette applet de commande pour vous connecter au Gestionnaire de ressources Azure.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Sélectionnez l’abonnement que vous souhaitez utiliser pour votre session actuelle.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Créer la base de données. Cet exemple crée une base de données nommée « mynewsqldw », avec le niveau objectif de service « DW400 », sur le serveur nommé « sqldwserver1 », qui se trouve dans le groupe de ressources nommé « mywesteuroperesgp1 ».

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
    ```

Les paramètres requis sont les suivants :

- **RequestedServiceObjectiveName**: le montant de [DWU][] que vous demandez.  Les valeurs prises en charge sont : DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 et DW6000.
- **DatabaseName**: le nom de l’entrepôt de données SQL que vous créez.
- **Nom du serveur**: le nom du serveur que vous utilisez pour la création (il faut V12).
- **ResourceGroupName**: groupe de ressources que vous utilisez.  Pour rechercher des ressources disponibles dans votre abonnement, les groupes utilisent Get-AzureResource.
- **Édition**: doit être « Data Warehouse » pour créer un entrepôt de données SQL.

Les paramètres facultatifs sont les suivants :

- **CollationName**: le classement par défaut si non spécifié est SQL_Latin1_General_CP1_CI_AS.  Impossible de modifier le classement dans une base de données.
- **MaxSizeBytes**: une base de données la taille maximale par défaut est de 10 Go.


Pour plus d’informations sur les options de paramètres, consultez [New-AzureRmSqlDatabase][] et [Créer une base de données (SQL Azure Data Warehouse)][].

## <a name="next-steps"></a>Étapes suivantes

Une fois votre entrepôt de données SQL a terminé la mise en service, vous souhaiterez essayer de [charger les données d’exemple][] ou Découvrez comment [développer][], [charger][]ou [migrer][].

Si vous souhaitez plus d’informations sur comment gérer SQL Data Warehouse par programmation, consultez notre article sur l’utilisation des [applets de commande PowerShell et autres API][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[effectuer la migration]: ./sql-data-warehouse-overview-migrate.md
[développer]: ./sql-data-warehouse-overview-develop.md
[charge]: ./sql-data-warehouse-load-with-bcp.md
[chargement des données d’exemple]: ./sql-data-warehouse-load-sample-databases.md
[Applets de commande PowerShell et les autres API]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Comment faire pour installer et configurer Azure PowerShell]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Créer un serveur logique de base de données de SQL Azure avec le portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Créer un serveur logique de base de données de SQL Azure avec PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Comment faire pour créer un groupe de ressources]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[Nouvelle-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Créer la base de données (SQL Azure Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Prix de l’entrepôt de données SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Version d’essai Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN crédits Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
