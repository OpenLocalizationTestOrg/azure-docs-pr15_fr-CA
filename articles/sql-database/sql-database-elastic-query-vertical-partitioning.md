<properties
    pageTitle="Requête sur plusieurs bases de données de nuage avec un schéma différent | Microsoft Azure"
    description="le paramétrage des requêtes de bases de données croisées sur les partitions verticales"    
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
    ms.date="05/27/2016"
    ms.author="torsteng" />

# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Exécuter des requêtes dans des bases de données de nuage avec différents schémas (aperçu)

![Requête sur plusieurs tables de bases de données différentes][1]

Bases de données partitionnée verticalement utilisent différents ensembles de tables dans des bases différentes. Cela signifie que le schéma est différent sur les bases de données différentes. Par exemple, toutes les tables d’inventaire sont sur une base de données alors que toutes les tables de comptables sont sur une seconde base de données. 

## <a name="prerequisites"></a>Conditions préalables

* L’utilisateur doit posséder l’autorisation de modifier n’importe quelle SOURCE de données externe. Cette autorisation est incluse dans l’autorisation de modifier la base de données.
* Autorisations de modifier n’importe quelle SOURCE de données externe sont nécessaires pour faire référence à la source de données sous-jacente.

## <a name="overview"></a>Vue d’ensemble

**Remarque**: contrairement avec partitionnement horizontal, ces instructions DDL ne dépendent pas de la définition d’une couche de données avec un mappage partagé par le biais de la bibliothèque de client élastique de la base de données.

1. [CRÉER LA CLÉ PRINCIPALE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRÉER L’ÉTENDUE DE LA BASE DE DONNÉES INFORMATIONS D’IDENTIFICATION](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRÉER LA SOURCE DE DONNÉES EXTERNE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRÉER UNE TABLE EXTERNE](https://msdn.microsoft.com/library/dn935021.aspx) 


## <a name="create-database-scoped-master-key-and-credentials"></a>Créer la clé principale de base de données de la portée et des informations d’identification 

Les informations d’identification sont utilisée par la requête élastique pour se connecter à vos bases de données distantes.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**Remarque**    Assurez-vous que le *<username>* pas *"@servername"* suffixe. 

## <a name="create-external-data-sources"></a>Créer des sources de données externes

Syntaxe :

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

**Important**   Le paramètre TYPE doit être défini à un **SGBDR**. 

### <a name="example"></a>Exemple 

L’exemple suivant illustre l’utilisation de l’instruction CREATE pour les sources de données externes. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 
 
Pour récupérer la liste des sources de données externes en cours : 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tables externes 

Syntaxe :

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 
    
    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Exemple  

    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

L’exemple suivant montre comment récupérer la liste des tables externes à partir de la base de données en cours : 

    select * from sys.external_tables; 

### <a name="remarks"></a>Remarques

Requête élastique étend la syntaxe existante de la table externe pour définir les tables externes qui utilisent des sources de données externes de type SGBDR. Une définition de table externe pour le partitionnement vertical couvre les aspects suivants : 

* **Schéma**: la table externe DDL définit un schéma qui permettent de vos requêtes. Le schéma fourni dans la définition de la table externe doit correspondre au schéma des tables de la base de données distant où sont stockées les données réelles. 

* **Référence de base de données distante**: la table externe DDL fait référence à une source de données externe. La source de données externe spécifie le nom du serveur logique et le nom de la base de données de la base de données distant où sont stockées les données réelles du tableau. 

La syntaxe pour créer des tables externes en utilisant une source de données externe comme indiqué dans la section précédente, est comme suit : 

La clause DATA_SOURCE définit la source de données externe (par exemple, la base de données distante en cas de partitionnement vertical) qui est utilisée pour la table externe.  

Les clauses SCHEMA_NAME et OBJECT_NAME offrent la possibilité de mapper la définition de la table externe à une table dans un schéma différent sur la base de données distante, ou une table avec un nom différent, respectivement. Cela est utile si vous souhaitez définir une table externe à une vue de catalogue ou DMV sur votre base de données distante, ou de toute autre situation où le nom de la table distante est déjà utilisé localement.  

L’instruction DDL suivante supprime une définition existante de la table externe dans le catalogue local. Il n’influe pas sur la base de données distante. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Autorisations pour créer/supprimer une TABLE externe**: autorisations de modifier n’importe quelle SOURCE de données externe sont nécessaires pour la table externe DDL qui est également nécessaire pour faire référence à la source de données sous-jacente.  

## <a name="security-considerations"></a>Considérations sur la sécurité
Les utilisateurs ayant accès à la table externe d’accéder automatiquement aux tables sous-jacentes à distance sous les informations d’identification dans la définition de source de données externe. Vous devez soigneusement gérer les accès à la table externe afin d’éviter les indésirables d’élévation des privilèges via les informations d’identification de la source de données externe. Les autorisations de SQL standard peuvent être utilisées pour accorder ou refuser l’accès à une table externe comme s’il s’agissait d’une table normale.  


## <a name="example-querying-vertically-partitioned-databases"></a>Exemple : interrogation verticalement partitionné de bases de données 

La requête suivante effectue une jointure tridirectionnelle entre les deux tables locales pour les commandes et lignes de commande et de la table distante pour les clients. Il s’agit d’un exemple du cas d’usage de données référence de requête élastique : 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>La procédure stockée pour l’exécution de T-SQL à distance : sp\_execute_remote

Requête élastique présente également une procédure stockée qui fournit un accès direct au milieu des fragments. La procédure stockée est appelée [sp\_exécuter \_distant](https://msdn.microsoft.com/library/mt703714) et peut être utilisé pour exécuter le code de T-SQL ou de procédures stockées distantes sur des bases de données distantes. Il accepte les paramètres suivants : 

* Nom de source de données (nvarchar) : le nom de la source de données externe de type SGBDR. 
* Requête (nvarchar) : requête le T-SQL à exécuter sur chaque partagé. 
* Déclaration de paramètre (nvarchar) - facultative : chaîne avec les définitions de type de données pour les paramètres utilisés dans le paramètre de requête (par exemple, sp_executesql). 
* Liste de valeurs de paramètre - facultatif : séparées par des virgules de liste de valeurs de paramètre (par exemple, sp_executesql).

Le sp\_exécuter\_à distance utilise la source de données externe fournie dans les paramètres d’invocation pour exécuter l’instruction T-SQL donnée sur les bases de données distantes. Il utilise les informations d’identification de la source de données externe pour se connecter à la base de données du Gestionnaire de shardmap et de bases de données distantes.  

Exemple : 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 


  
## <a name="connectivity-for-tools"></a>Connectivité des outils

Vous pouvez utiliser des chaînes de connexion SQL Server standard pour connecter vos outils d’intégration de BI et les données de bases de données sur le serveur de base de données SQL ayant activé de requête élastique et tables externes définies. Assurez-vous que SQL Server est pris en charge comme source de données de votre outil. Faites ensuite référence à la base de données de requête élastique et ses tables externes comme n’importe quel autre SQL Server base de données que vous devez vous connecter à avec votre outil. 

## <a name="best-practices"></a>Meilleures pratiques 
 
* Assurez-vous que la base de données du point de terminaison de requête élastique est autorisé à accéder à la base de données distante par l’activation de l’accès aux Services d’Azure dans sa configuration de pare-feu de base de données SQL. Vérifiez également que les informations d’identification fournies dans la définition de source de données externe peuvent se connecter à la base de données distante et qu’il détient des autorisations pour accéder à la table distante.  

* Requête élastique idéal pour les requêtes où la plupart du calcul peut être effectuée sur les bases de données distantes. En règle générale, vous obtenez les meilleures performances de requête avec des prédicats de filtre sélective qui peut être évaluée sur les bases de données distantes ou des jointures qui peuvent être effectuées entièrement sur la base de données distante. Autres modèles de requête pour charger de grandes quantités de données à partir de la base de données distante et peuvent être lent. 


## <a name="next-steps"></a>Étapes suivantes

Pour interroger les bases de données partitionnées horizontalement (également connu sous le nom sharded bases de données), consultez [requêtes sur les bases de données sharded cloud (partitionnés horizontalement)](sql-database-elastic-query-horizontal-partitioning.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
