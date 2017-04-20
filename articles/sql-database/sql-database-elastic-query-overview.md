<properties
    pageTitle="Présentation de requête d’élastique de la base de données de la base de données SQL Azure | Microsoft Azure"
    description="Vue d’ensemble de la fonctionnalité de requête élastique"    
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
    ms.date="04/27/2016"
    ms.author="torsteng" />

# <a name="azure-sql-database-elastic-database-query-overview-preview"></a>Azure présentation de requête élastique de la base de données de la base de données SQL (aperçu)

La fonctionnalité de requête de base de données élastique (en aperçu) vous permet d’exécuter une requête Transact-SQL qui s’étend à plusieurs bases de données dans Azure SQL de base de données (SQLDB). Il vous permet d’effectuer des requêtes inter-base de données pour accéder aux tables distantes et les outils Microsoft et tiers (Excel, PowerBI, Tableau, etc.) à travers des couches de données avec plusieurs bases de données de requête de connexion. À l’aide de cette fonctionnalité, vous pouvez mettre à l’échelle des requêtes aux niveaux de grande quantité de données dans la base de données de SQL et visualiser les résultats dans les rapports de business intelligence (BI).

## <a name="documentation"></a>Documentation

* [Mise en route avec des requêtes de bases de données croisées](sql-database-elastic-query-getting-started-vertical.md)
* [Dans d’autres bases de données à grande échelle cloud](sql-database-elastic-query-getting-started.md)
* [Exécuter des requêtes dans le nuage sharded de bases de données (partitionnés horizontalement)](sql-database-elastic-query-horizontal-partitioning.md)
* [Exécuter des requêtes dans les bases de données de nuage avec différents schémas (partitionnées verticalement)](sql-database-elastic-query-vertical-partitioning.md)
* [SP\_exécuter \_à distance](https://msdn.microsoft.com/library/mt703714)


## <a name="why-use-elastic-queries"></a>Pourquoi utiliser des requêtes élastiques ?

**Base de données SQL Azure**

Exécuter des requêtes dans les bases de données SQL d’Azure complètement dans T-SQL. Cela permet en lecture seule interrogation des bases de données distantes. Cela fournit une option pour les clients de SQL Server sur site actuels migrer des applications à l’aide de noms de trois et quatre partiel ou serveur lié à la base de données SQL.

**Disponible sur la couche standard** Requête élastique est pris en charge sur le niveau de performances Standard en plus de la couche de performances Premium. Reportez-vous à la section sur les limites d’aperçu ci-après sur les limitations de performances pour des niveaux de performances inférieurs.

**Pousser vers les bases de données distantes**

Requêtes élastiques peuvent désormais distribuer les paramètres SQL pour les bases de données à distance pour l’exécution.

**Exécution de la procédure stockée**

Exécuter des appels de procédure stockée distante ou des fonctions à distance à l’aide de [sp\_exécuter \_distant](https://msdn.microsoft.com/library/mt703714).

**Flexibilité**

Tables externes avec requête élastique peuvent maintenant faire référence à des tables distantes avec un schéma différent ou le nom de la table.

## <a name="elastic-database-query-scenarios"></a>Scénarios de requête de base de données élastique

L’objectif est de faciliter l’interrogation des scénarios où plusieurs bases de données apporter des lignes dans un seul ensemble de résultats. La requête peut être constituée soit par l’utilisateur ou l’application directement, ou indirectement par le biais des outils qui sont connectés à la base de données. Ceci est particulièrement utile lors de la création de rapports, à l’aide des outils d’intégration BI ou les données commerciales, ou toute autre application qui ne peut pas être modifiée. Avec une requête élastique, vous pouvez interroger sur plusieurs bases de données à l’aide de la familière de connectivité de SQL Server dans des outils tels que Excel, PowerBI, Tableau ou Cognos.
Une requête élastique permet d’accéder facilement à un ensemble de bases de données via des requêtes émises par SQL Server Management Studio ou Visual Studio et facilite l’interrogation d’inter-base de données Entity Framework ou d’autres environnements ORM. La figure 1 illustre un scénario dans lequel une application cloud (qui utilise la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md)) s’appuie sur une couche de données de plus grande échelle, et une requête élastique est utilisée pour les rapports de bases de données croisées.

**Figure 1** Requête élastique utilisée sur la couche de données à l’échelle

![Requête élastique utilisée sur la couche de données à l’échelle][1]

Scénarios de client pour une requête élastique sont caractérisées par les topologies suivantes :

* **Le partitionnement vertical – requêtes de bases de données croisées** (Topologie 1) : les données sont partitionnées verticalement entre un certain nombre de bases de données dans une couche de données. En général, les différents ensembles de tables résident sur différentes bases de données. Cela signifie que le schéma est différent sur les bases de données différentes. Par exemple, toutes les tables d’inventaire sont sur une base de données alors que toutes les tables de comptables sont sur une seconde base de données. Scénarios d’utilisation courants avec cette topologie nécessitent un pour exécuter des requêtes dans ou pour compiler des rapports sur les tables de plusieurs bases de données.
* **Le partitionnement horizontal – ont** (Topologie 2) : données sont partitionnées horizontalement pour répartir les lignes de données à l’échelle des niveaux. Avec cette approche, le schéma est identique sur toutes les bases de données participantes. Cette approche est également appelée « ont ». Ont peut être effectuée et gérés à l’aide de (1) la base de données élastique outils self-ont (2) ou bibliothèques. Une requête élastique est utilisée pour interroger ou compiler des rapports sur de nombreux milieu des fragments.

> [AZURE.NOTE] Requête de base de données élastique fonctionne mieux pour les scénarios de création de rapports occasionnels où la plupart du traitement peut être effectuée sur la couche données. Pour les charges de travail Création de rapports ou les scénarios de gestion de magasins de données avec des requêtes plus complexes, également envisager d’utiliser le [Magasin de données SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).


## <a name="elastic-database-query-topologies"></a>Topologies de requête de base de données élastiques

### <a name="topology-1-vertical-partitioning--cross-database-queries"></a>Topologie 1 : Le partitionnement Vertical – requêtes de bases de données croisées

Pour commencer à coder, consultez [mise en route avec les requêtes de bases de données croisées (partitionnement vertical)](sql-database-elastic-query-getting-started-vertical.md).

Une requête élastique peut être utilisée pour rendre les données situées dans une base de données SQLDB aux autres bases de données SQLDB. Cela permet des requêtes à partir d’une base de données pour faire référence à des tables dans n’importe quel autre SQLDB base de données distante. La première étape consiste à définir une source de données externes pour chaque base de données distante. La source de données externe est définie dans la base de données locale à partir de laquelle vous souhaitez accéder aux tables situé sur la base de données distante. Aucune modification n’est nécessaire sur la base de données distante. Pour verticales partitionnement scénarios classiques où les bases de données différentes ont des schémas différents, les requêtes élastiques peuvent être utilisées pour implémenter les scénarios d’utilisation courants, tels que l’accès aux données de référence et l’interrogation de bases de données croisées.

**Les données de référence**: topologie 1 est utilisé pour la gestion des données de référence. Dans la figure ci-dessous, les deux tables (T1 et T2) avec les données de référence sont conservées sur une base de données dédiée. À l’aide d’une requête élastique, vous pouvez désormais accéder les tables T1 et T2 à distance à partir d’autres bases de données, comme illustré dans la figure. Topologie d’utilisation 1 si les tables de références sont des requêtes de petite taille ou distants dans la table de référence ont des prédicats sélectives.

**Figure 2** Le partitionnement vertical - l’à l’aide des données de référence de requête pour la requête élastique

![Le partitionnement vertical - l’à l’aide des données de référence de requête pour la requête élastique][3]

**L’interrogation de bases de données croisées**: élastique interroge activer des cas d’usage qui nécessitent des requêtes sur plusieurs bases de données SQLDB. La figure 3 illustre les quatre bases de données différentes : stock, ressources humaines, CRM et produits. Les requêtes exécutées dans une base de données également besoin d’accéder à une ou toutes les autres bases de données. À l’aide d’une requête élastique, vous pouvez configurer votre base de données pour ce cas en exécutant quelques instructions DDL simples sur chacune des quatre bases de données. Après cette configuration unique, l’accès à une table distante est aussi simple que de faire référence à une table locale à partir de requêtes T-SQL ou à partir de vos outils d’analyse Décisionnelle. Cette approche est recommandée si les requêtes distantes ne retournent pas de résultats volumineux.

**Figure 3** Le partitionnement vertical - le sur différentes bases de données à l’aide d’élastique de requête pour la requête

![Le partitionnement vertical - le sur différentes bases de données à l’aide d’élastique de requête pour la requête][4]

### <a name="topology-2-horizontal-partitioning--sharding"></a>De topologie 2 : Partitionnement Horizontal – ont

À l’aide de la requête élastique pour effectuer les tâches de rapport sur un sharded, c'est-à-dire horizontalement partitionnés, la couche données requiert un [mappage d’éclater élastique de la base de données](sql-database-elastic-scale-shard-map-management.md) pour représenter les bases de données de la couche données. En règle générale, uniquement une carte unique partagé est utilisée dans ce scénario, et une base de données dédié avec des fonctions de requête élastique sert de point d’entrée pour les requêtes de rapport. Cette base de données dédiée uniquement doit accéder à la carte partagé. La figure 4 illustre cette topologie et sa configuration avec le mappage de base de données et partagé de requête élastique. Les bases de données dans la couche de données peuvent être de toute version de la base de données de SQL Azure ou l’édition. Pour plus d’informations sur la bibliothèque de client élastique de la base de données et de création de mappages de partagé, consultez [Éclater mapper la gestion](sql-database-elastic-scale-shard-map-management.md).

**Figure 4** Le partitionnement horizontal - l’à l’aide de la requête élastique pour les rapports sur les niveaux de données sharded

![Le partitionnement horizontal - l’à l’aide de la requête élastique pour les rapports sur les niveaux de données sharded][5]

> [AZURE.NOTE] La base de données de requête élastique dédié de la base de données doit être une base de données de base de données SQL v12. Il n’y a pas de restrictions sur les milieu des fragments eux-mêmes.

Pour commencer à coder, consultez [mise en route avec la requête de base de données élastique pour la partition horizontale (ont)](sql-database-elastic-query-getting-started.md).

## <a name="implementing-elastic-database-queries"></a>Implémentation des requêtes de base de données élastique

Les étapes pour implémenter la requête élastique pour les scénarios de partitionnement verticales et horizontales sont décrits dans les sections suivantes. Ils concernent également une documentation plus détaillée pour les différents scénarios de partitionnement.

### <a name="vertical-partitioning---cross-database-queries"></a>Le partitionnement vertical - requêtes de bases de données croisées

Les étapes suivantes configurer les requêtes de la base de données élastique pour des scénarios de partitionnement verticales qui requièrent l’accès à une table que qui se trouve sur les bases de données distantes SQLDB avec le même schéma :

*    Mymasterkey de [créer la clé principale](https://msdn.microsoft.com/library/ms174382.aspx)
*    Mycredential de [Création de base de données étendue d’informations d’identification](https://msdn.microsoft.com/library/mt270260.aspx)
*    Mydatasource [CREATE/DROP SOURCE de données externe](https://msdn.microsoft.com/library/dn935022.aspx) de type **SGBDR**
*    [TABLE externe de CREATE/DROP](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Après avoir exécuté les instructions DDL, vous pouvez accéder à la table à distance « mytable » comme s’il s’agissait d’une table locale. Une base de données SQL Azure automatiquement ouvre une connexion à la base de données distant, traite votre demande sur la base de données distante et retourne les résultats.
Vous trouverez plus d’informations sur les étapes requises pour le scénario de partitionnement vertical dans [une requête élastique pour le partitionnement vertical](sql-database-elastic-query-vertical-partitioning.md).  

### <a name="horizontal-partitioning---sharding"></a>Le partitionnement horizontal - ont

Les étapes suivantes configurer des requêtes élastique pour horizontales partitionnement des scénarios qui requièrent l’accès à un ensemble de table qui sont trouvent sur (généralement) plusieurs SQLDB bases de données distantes :

*    Mymasterkey de [créer la clé principale](https://msdn.microsoft.com/library/ms174382.aspx)
*    Mycredential de [Création de base de données étendue d’informations d’identification](https://msdn.microsoft.com/library/mt270260.aspx)
*    Créer un [mappage d’éclater](sql-database-elastic-scale-shard-map-management.md) représentant votre couche de données à l’aide de la bibliothèque de client élastique de la base de données.   
*    Mydatasource [CREATE/DROP SOURCE de données externe](https://msdn.microsoft.com/library/dn935022.aspx) de type **SHARD_MAP_MANAGER**
*    [TABLE externe de CREATE/DROP](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Une fois que vous avez effectué ces étapes, vous pouvez accéder à la table partitionnée horizontalement « mytable » comme s’il s’agissait d’une table locale. Une base de données SQL Azure automatiquement ouvre plusieurs connexions parallèles pour les bases de données dans lequel les tables sont stockés physiquement, traite les requêtes sur les bases de données distantes et renvoie les résultats.
Vous trouverez plus d’informations sur les étapes requises pour le scénario de partitionnement horizontal dans la [requête élastique pour la partition horizontale](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="t-sql-querying"></a>Interrogation de T-SQL
Une fois que vous avez défini vos tables externes et vos sources de données externes, vous pouvez utiliser des chaînes de connexion SQL Server standard pour se connecter aux bases de données dans laquelle vous avez défini vos tables externes. Vous pouvez ensuite exécuter les instructions T-SQL sur vos tables externes sur cette connexion, avec les restrictions indiquées ci-dessous. Vous trouverez plus d’informations et exemples de requêtes de T-SQL dans les rubriques de la documentation pour [le partitionnement horizontal](sql-database-elastic-query-horizontal-partitioning.md) et [le partitionnement vertical](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Connectivité des outils
Vous pouvez utiliser des chaînes de connexion SQL Server standard pour connecter vos applications et les outils d’intégration BI ou de données aux bases de données qui ont des tables externes. Assurez-vous que SQL Server est pris en charge comme source de données de votre outil. Une fois connecté, reportez-vous à la base de données de requête élastique et les tables externes dans cette base de données comme vous le feriez avec n’importe quel autre de SQL Server de base de données auquel vous vous connectez à avec votre outil.

> [AZURE.IMPORTANT] Authentification à l’aide d’Azure Active Directory avec les requêtes élastiques n’est actuellement pas pris en charge.

## <a name="cost"></a>Coût

Requête élastique est inclus dans le coût des bases de données de base de données de SQL Azure. Les topologies où vos bases de données distantes sont dans un autre centre de données que le point de terminaison élastique de requête sont pris en charge, mais les sorties de données à partir de bases de données distantes sont facturés régulière [taux Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitations de l’aperçu
* Votre première requête élastique en cours d’exécution peut prendre jusqu'à quelques minutes sur le niveau de performances Standard. Ce temps est nécessaire au chargement de la fonctionnalité de requête élastique. améliore les performances de chargement avec des niveaux de performances supérieurs.
* Script de sources de données externes ou les tables externes à partir de SSMS ou SSDT n’est pas encore pris en charge.
* Importation/exportation de base de données de SQL ne prend pas en charge des tables externes et sources de données externes. Si vous devez utiliser l’importation/exportation, déplacer ces objets avant de les exporter et puis de les recréer après l’importation.
* Requête de base de données élastique prend actuellement en charge uniquement les accès en lecture seule à des tables externes. Vous pouvez toutefois utiliser toutes les fonctionnalités T-SQL sur la base de données où la table externe est définie. Cela peut être utile, par exemple, conserver les résultats temporaires à l’aide, par exemple, sélectionnez < liste_de_colonnes > dans < local_table >, ou pour définir des procédures stockées dans la base de données de requête élastique qui font référence à des tables externes.
* À l’exception de nvarchar (max), types LOB ne sont pas pris en charge dans les définitions de la table externe. Pour résoudre ce problème, vous pouvez créer une vue dans la base de données distante qui effectue un cast du type métier en nvarchar (max), définissez votre table externe via la vue au lieu de la table de base et puis effectuer un cast vers le type LOB d’origine dans vos requêtes.
* Colonne de statistiques sur les tables externes n’est actuellement pas pris en charge. Statistiques des tables sont pris en charge, mais doivent être créés manuellement.

## <a name="feedback"></a>Commentaires
Veuillez partager vos commentaires sur votre expérience avec les requêtes élastiques avec nous sur Disqus ci-dessous, les forums MSDN, ou Stackoverflow. Nous sommes intéressés par tous les types de commentaires sur le service (défauts, les bords, des intervalles de fonctionnalité).

## <a name="more-information"></a>Plus d’informations

Vous trouverez plus d’informations sur les scénarios de partitionnement verticales et de l’interrogation de bases de données croisées dans les documents suivants :

* [L’interrogation de bases de données croisées et la vue d’ensemble du partitionnement vertical](sql-database-elastic-query-vertical-partitioning.md)
* Essayez notre didacticiel pas à pas pour complet exemple en cours d’exécution en minutes : [mise en route avec les requêtes de bases de données croisées (partitionnement vertical)](sql-database-elastic-query-getting-started-vertical.md).


Plus d’informations sur les scénarios ont et de partitionnement horizontales sont disponibles ici :

* [Vue d’ensemble de partitionnement et ont horizontal](sql-database-elastic-query-horizontal-partitioning.md)
* Essayez notre didacticiel pas à pas pour complet exemple en cours d’exécution en minutes : [mise en route avec la requête de base de données élastique pour la partition horizontale (ont)](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
