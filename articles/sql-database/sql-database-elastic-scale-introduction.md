<properties
    pageTitle="L’évolution horizontale avec la base de données de SQL Azure | Microsoft Azure"
    description="Logiciel en tant que développeurs d’un Service (SaaS) peut facilement créer élastiques et évolutives des bases de données dans le cloud à l’aide de ces outils"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove"/>

# <a name="scaling-out-with-azure-sql-database"></a>Mise à l’échelle avec la base de données de SQL Azure

Vous pouvez aisément faire évoluer les bases de données SQL d’Azure en utilisant les outils de **Base de données élastique** . Ces fonctionnalités et outils vous permettent d’utiliser les ressources virtuellement illimitée de la base de données de **Base de données de SQL Azure** pour créer des solutions pour les charges de travail transactionnelles et en particulier les logiciels sous la forme d’une application de Service (SaaS). Les fonctionnalités de base de données élastiques sont comprend les éléments suivants :

* [Bibliothèque client de base de données élastique](sql-database-elastic-database-client-library.md): la bibliothèque cliente est une fonctionnalité qui vous permet de créer et de tenir à jour les bases de données sharded.  Reportez-vous à la section [mise en route avec les outils de base de données élastique](sql-database-elastic-scale-get-started.md).
* [Outil de fusion et fractionnement de base de données élastique](sql-database-elastic-scale-overview-split-and-merge.md): déplace les données entre les bases de données sharded. Cela est utile pour déplacer des données d’une base de données partagée pour une base de données de mono-utilisateur (ou vice versa). Consultez [la base de données élastique didacticiel d’outil de fusion et fractionnement](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Travaux d’élastique de la base de données](sql-database-elastic-jobs-overview.md) (aperçu) : travaux permet de gérer un grand nombre de bases de données SQL d’Azure. Exécuter facilement les opérations administratives, comme les changements de schéma, gestion des informations d’identification, mises à jour des données de référence, collecte des données de performances ou collection de télémétrie de clients (client) à l’aide de tâches.
* [Requête de base de données élastique](sql-database-elastic-query-overview.md) (aperçu) : vous permet d’exécuter une requête Transact-SQL qui s’étend à plusieurs bases de données. Cela permet une connexion à des outils de reporting (Excel, PowerBI, Tableau, etc.).
* [Transactions élastiques](sql-database-elastic-transactions-overview.md): cette fonctionnalité vous permet d’exécuter des transactions qui couvrent plusieurs bases de données dans la base de données de SQL Azure. Transactions de base de données élastique sont disponibles pour les applications .NET à l’aide de ADO .NET et intégrant l’expérience de programmation courantes à l’aide des [classes de System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

L’illustration ci-dessous montre une architecture qui inclut les **fonctionnalités d’élastique de la base de données** par rapport à une collection de bases de données.

Dans ce graphique, les couleurs de la base de données représentent des schémas. Bases de données avec la même couleur partagent le même schéma.

1. Un ensemble de **bases de données SQL d’Azure** sont hébergés sur Azure utilisant ont architecture.
2. La **bibliothèque de client de base de données élastique** est utilisée pour gérer un ensemble partagé.
3. Un sous-ensemble des bases de données sont placés dans un **pool d’élastique de la base de données**. (Voir [ce qu’est un pool ?](sql-database-elastic-pool.md)).
4. Un **travail élastique de base de données** s’exécute planifiée ou ad hoc T-SQL scripts sur toutes les bases de données.
5. L' **outil de fusion-split** est utilisé pour déplacer des données à partir d’un éclater à l’autre.
6. La **requête de base de données élastique** vous permet d’écrire une requête qui couvre toutes les bases de données dans l’ensemble partagé.
7. **Transactions élastiques** vous permet d’exécuter des transactions qui couvrent plusieurs bases de données. 


![Outils de base de données élastiques][1]


## <a name="why-use-the-tools"></a>Pourquoi utiliser les outils ?

Obtention d’élasticité et l’échelle pour les applications en nuage a été simple pour les ordinateurs virtuels et le stockage des objets blob--simplement ajouter ou soustraire des unités ou augmentent la puissance. Mais il reste un défi pour le traitement des données dans les bases de données relationnelles avec état. Problèmes apparus dans ces scénarios :

* La croissance et la réduction de capacité pour l’article de la base de données relationnelle de votre charge de travail.
* Gestion des zones réactives sur lesquelles peuvent se présentent un sous-ensemble spécifique de données – par exemple un particulièrement sollicité client (clients).

Traditionnellement, les scénarios semblables ont été résolus en investissant dans des serveurs de base de données de plus grande échelle pour prendre en charge de l’application. Toutefois, cette option est limitée dans le nuage dans lequel tout le traitement se produit sur le matériel de base prédéfinis. Au lieu de cela, distribution des données et de traitement sur plusieurs bases de données structurés de manière identique (un modèle horizontale appelé « ont ») fournit une alternative aux approches traditionnelles de l’extrapolation à la fois en termes de coût et l’élasticité.

## <a name="horizontal-and-vertical-scaling"></a>La mise à l’échelle horizontale et verticale

La figure ci-dessous illustre les dimensions horizontales et verticales de la mise à l’échelle, qui sont des méthodes de base qu'elastiques bases de données peuvent être mis à l’échelle.

![Horizontal par rapport à l’évolution verticale][2]

Mise à l’échelle horizontale fait référence à l’ajout ou la suppression des bases de données afin d’adapter les performances globales ou de capacité. Cela est également appelée « mise à l’échelle ». Ont, dans lequel les données sont partitionnées sur une collection de bases de données structurées de façon identique, est une méthode commune pour implémenter la mise à l’échelle horizontale.  

Mise à l’échelle verticale fait référence pour augmenter ou diminuer le niveau de performance d’une base de données : il s’agit également « montée en charge. »

La plupart des applications de base de données à l’échelle du nuage utilise une combinaison de ces deux stratégies. Par exemple, un logiciel comme une application de Service permet la mise à l’échelle horizontale pour provisionner de nouveaux clients finaux et la mise à l’échelle verticale permettent de la base de données de chaque client fin agrandir ou réduire des ressources selon les besoins de la charge de travail.

* Mise à l’échelle horizontale est géré à l’aide de la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md).

* Mise à l’échelle verticale s’effectue à l’aide des applets de commande PowerShell de Azure pour modifier le niveau de service, ou en plaçant les bases de données dans un pool élastique.

## <a name="sharding"></a>Ont

*Ont* est une technique qui permet de répartir de grandes quantités de données de structure identique sur un certain nombre de bases de données indépendantes. Il est particulièrement populaire auprès des développeurs de nuage création de logiciel sous la forme d’une offre de Service (SAAS) pour les clients finaux ou des entreprises. Ces clients sont souvent appelés « locataires ». Ont peut être nécessaire pour plusieurs raisons :  

* La quantité totale de données est trop grande pour tenir dans les limites d’une seule base de données
* Le débit de la transaction de la charge de travail globale dépasse les capacités d’une seule base de données
* Locataires peuvent nécessiter une isolation physique entre eux, afin que les bases de données distinctes sont nécessaires pour chaque client
* Devrez peut-être différentes sections d’une base de données se trouvent dans différentes zones géographiques pour des raisons de géopolitiques, de performances ou de respect de la réglementation.

Dans d’autres scénarios, tels que la réception de données à partir des périphériques distribués, ont peut servir pour remplir un jeu de bases de données sont organisées temporairement. Par exemple, une base de données distincte peut être dédié à chaque jour ou chaque semaine. Dans ce cas, la clé ont peut être un nombre entier représentant la date (présente dans toutes les lignes des tables sharded) et les requêtes d’extraction des informations pour une plage de dates doivent être routés par l’application pour le sous-ensemble de bases de données couvrant la plage en question.

Ont fonctionne mieux lorsque toutes les transactions dans une application peuvent être limitée à une seule valeur d’une clé ont. Qui permet de s’assurer que toutes les transactions seront locales à une base de données spécifique.

## <a name="multi-tenant-and-single-tenant"></a>Mutualisée et mono-utilisateur

Certaines applications utilisent l’approche la plus simple de création d’une base de données distincte pour chaque client. C’est le **modèle d’ont mono-utilisateur** qui offre une isolation, possibilité de sauvegarde/restauration et ressource mise à l’échelle au niveau de granularité du locataire. Avec ont des mono-utilisateur, chaque base de données est associé à une valeur d’ID clients spécifiques (ou la valeur de la clé client), mais cette clé ne sont pas toujours nécessairement présente dans les données elles-mêmes. Il est responsable de l’application pour router chaque demande à la base de données appropriée, et la bibliothèque cliente peut simplifier ceci.

![Mono-utilisateur et mutualisée][4]

D’autres scénarios pack ensemble plusieurs locataires dans les bases de données, plutôt que de les isoler dans les bases de données distinctes. Il s’agit d’un **modèle de mutualisée ont** typique – et il peut être piloté par le fait qu’une application gère un nombre important de très petits clients. Dans plusieurs utilisateurs ont, les lignes dans les tables de la base de données sont conçus pour transporter une clé qui identifie l’ID de client ou la clé de l’ont. Là encore, la couche application est responsable du routage de demande d’un client la base de données appropriée, et cela peut être pris en charge par la bibliothèque cliente élastique de la base de données. En outre, la sécurité au niveau de la ligne peut servir à filtrer les lignes de chaque client peut accéder – pour plus d’informations, consultez [applications mutualisées avec les outils de base de données élastique et la sécurité de niveau ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribution des données entre les bases de données peuvent s’avérer nécessaires avec le modèle mutualisée ont, et cette opération est facilitée par l’outil de fusion et fractionnement de base de données élastique. Pour plus d’informations sur les modèles de conception pour les applications SaaS à l’aide de pools élastiques, voir [Modèles de Design pour les Applications SaaS mutualisée avec la base de données de SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Déplacer les données à partir de plusieurs unique-location de bases de données

Lors de la création d’une application SaaS, il est courant de proposer à leurs prospects une version d’évaluation du logiciel. Dans ce cas, il est plus rentable d’utiliser une base de données partagée pour les données. Toutefois, lorsqu’un prospect est un client, une base de données mono-utilisateur est préférable dans la mesure où il offre de meilleures performances. Si le client a créé les données au cours de la période d’évaluation, utilisez l' [outil de fusion et fractionnement](sql-database-elastic-scale-overview-split-and-merge.md) pour déplacer les données à partir de la mutualisée à la nouvelle base de données mono-utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Pour un exemple d’application illustrant la bibliothèque cliente, reportez-vous à la section [mise en route avec les outils de Datababase élastique](sql-database-elastic-scale-get-started.md).

Pour convertir les bases de données existantes pour utiliser les outils, voir [effectuer la migration des bases de données existantes à l’horizontale](sql-database-elastic-convert-to-use-elastic-tools.md).

Pour voir les caractéristiques du pool élastique de la base de données, consultez [Considérations de prix et de performances pour un pool d’élasticité de la base de données](sql-database-elastic-pool-guidance.md), ou créer un nouveau pool avec le [didacticiel](sql-database-elastic-pool-create-portal.md).  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

