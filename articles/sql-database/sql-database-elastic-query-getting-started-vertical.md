<properties
    pageTitle="Mise en route avec des requêtes de bases de données croisées (partitionnement vertical) | Microsoft Azure"   
    description="Comment faire pour utiliser une requête élastique avec les bases de données partitionnées verticalement"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="torsteng" />

# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Mise en route avec des requêtes de bases de données croisées (partitionnement vertical) (aperçu)

Élastique une requête (aperçu) pour la base de données de SQL Azure vous permet d’exécuter des requêtes T-SQL qui s’étendent à plusieurs bases de données à l’aide d’un point de connexion unique. Cette rubrique s’applique aux [partitionnée verticalement les bases de données](sql-database-elastic-query-vertical-partitioning.md).  

Une fois terminé, vous serez à même : Apprenez à configurer et à utiliser une base de données de SQL Azure pour exécuter des requêtes qui couvrent plusieurs bases de données liées. 

Pour plus d’informations sur la fonctionnalité de requête élastique de la base de données, consultez [vue d’ensemble de la requête de base de données SQL d’Azure élastique de la base de données](sql-database-elastic-query-overview.md). 

## <a name="create-the-sample-databases"></a>Créer des bases de données exemples

Nous devons créer deux bases de données **clients** et **commandes**, soit dans les serveurs logiques identiques ou différents.   

Exécutez les requêtes suivantes sur la base de données de **commandes** pour créer la table de **OrderInformation** et d’entrée de l’exemple de données. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Exécutez maintenant, à la suite de requêtes sur la base de données **clients** pour créer la table de **CustomerInformation** et d’entrée de l’exemple de données. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>Créer des objets de base de données
### <a name="database-scoped-master-key-and-credentials"></a>Portée de la base de données de la clé principale et les informations d’identification

1. Ouvrez SQL Server Management Studio ou les outils de données de SQL Server dans Visual Studio.
2. Se connecter à la base de données de commandes et exécutez la commande T-SQL suivante :

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  

    Le « username » et « password » doivent être le nom d’utilisateur et le mot de passe utilisé pour se connecter à la base de données de clients.
    Authentification à l’aide d’Azure Active Directory avec les requêtes élastiques n’est actuellement pas pris en charge.

### <a name="external-data-sources"></a>Sources de données externes
Pour créer une source de données externe, exécutez la commande suivante sur la base de données de commandes : 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Tables externes
Créer une table externe sur la base de données de commandes, qui correspond à la définition de la table CustomerInformation :

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Exécuter un exemple de requête T-SQL de la base de données élastique

Une fois que vous avez défini votre source de données externe et les tables externes, vous pouvez désormais utiliser T-SQL pour interroger les tables externes. Sur la base de données de commandes, exécutez cette requête : 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Coût

Actuellement, la fonctionnalité de requête de base de données élastique est incluse dans le coût de votre base de données de SQL Azure.  

Informations de tarification, consultez [Tarification de base de données SQL](/pricing/details/sql-database). 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->
