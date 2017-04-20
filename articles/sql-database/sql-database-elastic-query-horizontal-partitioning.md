<properties
    pageTitle="Reporting sur les bases de données à grande échelle nuage | Microsoft Azure"
    description="le paramétrage des requêtes élastiques sur les partitions horizontales"    
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

# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Reporting sur les bases de données à grande échelle cloud (aperçu)

![Exécuter des requêtes dans le milieu des fragments][1]

Les bases de données sharded répartir les lignes sur les données de mise à l’échelle des niveaux. Le schéma est identique sur toutes les bases de données participantes, également connus comme le partitionnement horizontal. À l’aide d’une requête élastique, vous pouvez créer des rapports qui couvrent toutes les bases de données dans une base de données sharded.

Pour un démarrage rapide, consultez [Création de rapports sur les bases de données à grande échelle cloud](sql-database-elastic-query-getting-started.md).

Pour les bases de données non sharded, consultez [requête entre les bases de données de nuage avec différents schémas](sql-database-elastic-query-vertical-partitioning.md). 

 
## <a name="prerequisites"></a>Conditions préalables

* Créer un mappage de partagé à l’aide de la bibliothèque de client élastique de la base de données. voir [Éclater mapper la gestion](sql-database-elastic-scale-shard-map-management.md). Ou utilisez l’exemple d’application de la [mise en route avec les outils de base de données élastique](sql-database-elastic-scale-get-started.md).
* Vous pouvez également voir [effectuer la migration des bases de données existantes pour les bases de données à grande échelle](sql-database-elastic-convert-to-use-elastic-tools.md).
* L’utilisateur doit posséder l’autorisation de modifier n’importe quelle SOURCE de données externe. Cette autorisation est incluse dans l’autorisation de modifier la base de données.
* Autorisations de modifier n’importe quelle SOURCE de données externe sont nécessaires pour faire référence à la source de données sous-jacente.

## <a name="overview"></a>Vue d’ensemble

Ces instructions créent la représentation sous forme de métadonnées de votre couche de données sharded dans la base de données de requête élastique. 


1. [CRÉER LA CLÉ PRINCIPALE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRÉER L’ÉTENDUE DE LA BASE DE DONNÉES INFORMATIONS D’IDENTIFICATION](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRÉER LA SOURCE DE DONNÉES EXTERNE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRÉER UNE TABLE EXTERNE](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 créer la clé principale de base de données de la portée et des informations d’identification 

Les informations d’identification sont utilisée par la requête élastique pour se connecter à vos bases de données distantes.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
>[AZURE.NOTE] Assurez-vous que le *»\<nom d’utilisateur\>"* pas *"@servername"* suffixe. 

## <a name="12-create-external-data-sources"></a>1.2 créer des sources de données externes

Syntaxe :

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                            
            (TYPE = SHARD_MAP_MANAGER,
                    LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Exemple 

    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );
 
Récupérer la liste des sources de données externes en cours : 

    select * from sys.external_data_sources; 

Votre carte partagé fait référence à la source de données externe. Une requête élastique utilise la source de données externe et le mappage partagé sous-jacent pour énumérer les bases de données qui participent à la couche données. Les mêmes informations d’identification sont utilisées pour lire le plan à éclater et accéder aux données sur les milieu des fragments lors du traitement d’une requête élastique. 

## <a name="13-create-external-tables"></a>1.3 créer des tables externes 
 
Syntaxe :  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  
    
    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Exemple**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 
    
    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
        SCHEMA_NAME = 'orders', 
        OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Récupérer la liste des tables externes à partir de la base de données en cours : 

    SELECT * from sys.external_tables; 

Supprimer les tables externes :

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Remarques

Les données\_clause SOURCE définit la source de données externe (un mappage partagé) qui est utilisée pour la table externe.  

Le schéma\_nom et objet\_les clauses nom mappent la définition de la table externe à une table dans un schéma différent. En cas d’omission, le schéma de l’objet distant est supposé pour être « dbo » et son nom est supposé pour être identique au nom d’une table externe en cours de définition. Cela est utile si le nom de la table distante est déjà effectuée dans la base de données où vous souhaitez créer la table externe. Par exemple, vous souhaitez définir une table externe pour obtenir une vue globale des affichages catalogue ou DMV sur vos données à l’échelle des niveaux. Dans la mesure où les affichages catalogue et les DMV existe déjà localement, vous ne pouvez pas utiliser leur nom pour la définition de la table externe. Au lieu de cela, utilisez un nom différent et de la vue de catalogue ou de la DMV nom dans le schéma\_nom et/ou objet\_les clauses de nom. (Voir l’exemple ci-dessous.) 

La clause DISTRIBUTION spécifie la distribution de données utilisée pour cette table. Le processeur de requêtes utilise les informations fournies dans la clause DISTRIBUTION pour créer les plans de requête plus efficaces.  

1. **SHARDED** signifie données sont partitionnées horizontalement entre les bases de données. La clé de partitionnement pour la distribution des données est le paramètre **< sharding_column_name >** .
2. **RÉPLIQUÉ** signifie que des copies identiques de la table sont présents sur chaque base de données. Il est de votre responsabilité de vous assurer que les réplicas sont identiques sur les bases de données.
3. **Pleine\_ROBIN** signifie que la table est partitionnée horizontalement à l’aide d’une méthode de distribution de dépendent de l’application. 

**Référence de couche de données**: la table externe DDL fait référence à une source de données externe. La source de données externe spécifie un mappage partagé qui fournit les informations nécessaires pour localiser toutes les bases de données dans votre couche de données à la table externe. 


### <a name="security-considerations"></a>Considérations sur la sécurité 

Les utilisateurs ayant accès à la table externe d’accéder automatiquement aux tables sous-jacentes à distance sous les informations d’identification dans la définition de source de données externe. Évitez non désirés d’élévation des privilèges via les informations d’identification de la source de données externe. Utilisez GRANT ou REVOKE pour une table externe comme s’il s’agissait d’une table normale.  

Une fois que vous avez défini vos tables externes et votre source de données externe, vous pouvez désormais utiliser T-SQL complet sur vos tables externes.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exemple : interrogation de bases de données partitionnées horizontales 

La requête suivante effectue une jointure tridirectionnelle entre les entrepôts, les commandes et lignes de commande et utilise plusieurs agrégats et un filtre sélectif. Il suppose que le partitionnement (1) horizontal (ont) et (2) que les entrepôts, les commandes et lignes de commande sont sharded par la colonne d’id magasin et que la requête élastique peut cohabiter les jointures sur les milieu des fragments et traiter la partie coûteuse de la requête sur les milieu des fragments en parallèle. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 
 
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

Utiliser des chaînes de connexion SQL Server standard pour connecter votre application, vos outils d’intégration BI et les données de la base de données avec vos définitions de table externe. Assurez-vous que SQL Server est pris en charge comme source de données de votre outil. Puis référence la base de données de requête élastique comme toute autre base de données SQL Server connecté à l’outil et utiliser les tables externes à partir de votre outil ou votre application comme si elles étaient des tables locales. 

## <a name="best-practices"></a>Meilleures pratiques 

* Assurez-vous que la base de données du point de terminaison de requête élastique est autorisé à accéder à la base de données shardmap et tous les milieu des fragments à travers les pare-feu de base de données SQL.  

* Valider ou de mettre en œuvre de la distribution des données définie par la table externe. Si la distribution de données réelles est différente de la distribution spécifiée dans la définition de table, vos requêtes peuvent donner des résultats inattendus. 

* Actuellement, les requête élastique n’effectue pas d’élimination d’éclater lorsque les prédicats sur la clé ont lui permettait d’exclure sans risque certains milieu des fragments de traitement.

* Requête élastique idéal pour les requêtes où la plupart du calcul peut être effectuée sur les milieu des fragments. En règle générale, vous obtenez les meilleures performances de requête avec des prédicats de filtre sélective qui peut être évaluée sur le milieu des fragments ou des jointures sur les clés de partitionnement qui peuvent être exécutées d’une manière alignés sur les partitions sur tous les milieu des fragments. Autres modèles de requête pour charger de grandes quantités de données depuis les milieu des fragments pour le nœud de tête et peuvent être lent

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
