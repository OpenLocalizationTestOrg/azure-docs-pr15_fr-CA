<properties
    pageTitle="& Options de performances de la base de données de SQL : niveaux de Service | Microsoft Azure"
    description="Comparer les fonctionnalités de continuité d’activité des performances et de l’entreprise de la base de données SQL des niveaux de service afin d’équilibrer le coût et la capacité de mise à l’échelle."
    keywords="options de base de données, les performances de la base de données"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/10/2016"
    ms.author="carlrab"/>

# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Les options de base de données SQL et les performances : comprendre ce qui est disponible dans chaque niveau de service

[Base de données de SQL Azure](sql-database-technical-overview.md) propose trois niveaux de service avec plusieurs niveaux de performances pour gérer les différentes charges de travail. Chaque niveau de performances fournit un ensemble croissant de ressources conçu pour offrir un débit supérieur de plus en plus. Vous pouvez gérer chaque base de données dans son propre [niveau de service](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) avec son propre niveau de performances. Vous pouvez également gérer plusieurs bases de données dans un [pool élastique](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) avec un ensemble de ressources partagées. Les ressources disponibles pour les bases de données autonomes sont exprimées en unités de base de données de Transaction (DTUs) et pour les pools élastiques élastique DTUs ou eDTUs. Pour plus d’informations sur DTUs et eDTUs, consultez [un DTU](sql-database-what-is-a-dtu.md). 

Dans les deux cas, les niveaux de service incluent **basique**, **Standard**et **Premium**. Options de base de données dans ces niveaux sont similaires pour les bases de données autonomes et pools élastiques, mais il existe des considérations supplémentaires pour les pools élastiques. Cet article fournit les détails de niveaux de service pour les bases de données autonomes et les pools élastiques.

## <a name="service-tiers-and-database-options"></a>Les niveaux de service et les options de base de données
Basique, Standard, et les niveaux de service Premium ont tous une SLA de 99,99 % de disponibilité et offrent des performances prévisibles, options de continuité d’activité d’entreprise flexible, les fonctionnalités de sécurité et de facturation horaire. Le tableau suivant fournit des exemples des niveaux mieux adaptés aux charges de travail d’application.

| Niveau de service | Charges de travail cibles |
|---|---|
| **Base** | Mieux adaptée pour une petite base de données, en général une seule opération active à un moment donné de la prise en charge. Exemples de bases de données utilisées pour le développement ou le test, ou de petites applications peu utilisaient. |
| **Standard** | L’option Aller à la plupart des applications de cloud, prise en charge de plusieurs requêtes simultanées. Exemples d’applications web ou de groupe de travail. |
| **Premium** | Conçu pour un important volume de transactions, prise en charge de nombreux utilisateurs simultanés et nécessitant le niveau le plus élevé de continuité d’activité. Les applications critiques de la prise en charge des bases de données sont des exemples. |

>[AZURE.NOTE] Les éditions entreprise et Web sont annulées. Lire le [Forum aux questions du coucher de soleil](https://azure.microsoft.com/pricing/details/sql-database/web-business/) si vous envisagez de continuer à utiliser les éditions entreprise et Web.

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Les niveaux de performances et de niveaux de service de base de données autonome
Pour les bases de données autonomes, il y a plusieurs niveaux de performance au sein de chaque niveau de service. Vous avez la possibilité de choisir le niveau qui répond le mieux aux besoins de votre charge de travail. Si vous avez besoin mettre à l’échelle vers le haut ou vers le bas, vous pouvez facilement modifier les niveaux de votre base de données. Pour plus d’informations, consultez [modification des niveaux de Service de base de données et des niveaux de Performance](sql-database-scale-up.md) .

Caractéristiques de performances ci-après s’appliquent aux bases de données créées à l’aide de [V12 de base de données SQL](sql-database-v12-whats-new.md). Quel que soit le nombre de bases de données hébergées, votre base de données obtenait un ensemble de garanties de ressources et les caractéristiques de performances de votre base de données ne sont pas affectés.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] Pour une explication détaillée de toutes les autres lignes de cette table de niveaux de service, voir les [limites et les capacités de niveau de Service](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Niveaux de service de pool élastique et performances dans eDTUs
En plus de la création et la mise à l’échelle d’une base de données autonome, vous avez également la possibilité de gérer plusieurs bases de données au sein d’un [pool élastique](sql-database-elastic-pool.md). Toutes les bases de données dans un pool élastique partagent un ensemble commun de ressources. Les caractéristiques de performance sont mesurés par les *Unités de Transaction de base de données élastique* (eDTUs). Comme avec les bases de données autonomes, pools sont fournis dans les trois niveaux de service : **basique**, **Standard**et **Premium**. Pour les pools, les niveaux de service de trois toujours définissent les limites de performances et des fonctionnalités.

Les pools permettent de partager et de consommer des ressources DTU sans avoir à affecter un niveau de performances spécifiques à chaque base de données dans le pool de bases de données. Par exemple, une base de données autonome dans un pool Standard pouvez accéder à partir de 0 eDTUs à l’eDTU maximale de la base de données que vous configurer lorsque vous configurez le pool. Les pools permettent plusieurs bases de données avec des charges de travail variables pour utiliser efficacement les ressources d’eDTU disponibles à l’ensemble du pool. Pour plus d’informations, reportez-vous à la section [Considérations de prix et de performances pour un pool élastique](sql-database-elastic-pool-guidance.md) .

Le tableau suivant décrit les caractéristiques de niveaux de service de pool.

[AZURE.INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Chaque base de données au sein d’un pool respecte également les caractéristiques de base de données autonome pour cette couche. Par exemple, le pool de base a une limite pour le nombre de sessions maximal par pool de 4800-28800, mais une base de données individuel au sein d’un pool de base a une limite de base de données de 300 sessions.

## <a name="choosing-a-service-tier"></a>Choix d’un niveau de service

Pour choisir un niveau de service, commencez par déterminer si la base de données doit être une base de données autonome ou faire partie d’un pool élastique. 

### <a name="choosing-a-service-tier-for-a-standalone-database"></a>Choix d’un niveau de service pour une base de données autonome

Pour choisir un niveau de service pour une base de données autonome, commencez par déterminer les fonctionnalités de base de données dont vous avez besoin pour choisir votre édition de base de données SQL :

- Taille de la base de données (maximum de base, maximum de 250 Go pour le Standard de 2 Go et 500 Go à 1 To maximum de prime - selon le niveau de performance)
- Période de rétention de sauvegarde de base de données (7 jours pour Basic, 35 jours pour Standard et 35 jours pour Premium)

Après avoir déterminé la version de la base de données SQL, vous êtes prêt à déterminer le niveau de performance de la base de données (nombre de DTUs). Vous pouvez le deviner, puis [mettre à l’échelle vers le haut ou vers le bas de manière dynamique](sql-database-scale-up.md) basé sur l’expérience réelle. Vous pouvez également utiliser la [Calculatrice de DTU](http://dtucalculator.azurewebsites.net/) de estimer le nombre de DTUs nécessaire. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Le choix d’un niveau de service pour un pool d’élasticité de la base de données.

Pour choisir le niveau de service pour un pool d’élasticité de la base de données, commencez par déterminer les fonctionnalités de base de données dont vous avez besoin pour choisir le niveau de service pour le pool.

- Taille de la base de données (2 Go pour Basic, 250 Go pour Standard et 500 Go pour Premium)
- Période de rétention de sauvegarde de base de données (7 jours pour Basic, 35 jours pour Standard et 35 jours pour Premium)
- Nombre de bases de données par pool (400 pour Basic, 400 pour la norme et 50 pour Premium)
- Par pool de stockage maximum (117 Go pour Basic, 1200 standard et 750 pour la prime)

Une fois que vous avez déterminé le niveau de service pour le pool, vous êtes prêt à déterminer le niveau de performance pour le regroupement (eDTUs). Vous pouvez le deviner, et puis [évoluer ou à l ' échelle dynamiquement](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) selon une expérience réelle. Vous pouvez utiliser la [Calculatrice de DTU](http://dtucalculator.azurewebsites.net/) de estimer le nombre de DTUs nécessaire pour chaque base de données dans un pool afin de déterminer la limite supérieure pour le pool.

## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur la tarification de ces niveaux de [Prix de base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Découvrez les détails des [pools élastiques](sql-database-elastic-pool-guidance.md) et [Considérations de prix et de performances pour les pools élastiques](sql-database-elastic-pool-guidance.md).
- Découvrez comment [le moniteur, gérer et redimensionner les pools élastiques](sql-database-elastic-pool-manage-portal.md) et [Analyseur de performances de bases de données autonomes](sql-database-single-database-monitor.md).
- Maintenant que vous savez sur les niveaux de la base de données SQL, les essayer avec un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) et découvrez [comment créer votre première base de données SQL](sql-database-get-started.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Modèles de design pour les applications SaaS mutualisées à l’aide de la base de données de SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
- [Cours de vidéo Microsoft Virtual Academy fonctionnalités élastique de la base de données dans la base de données de SQL Azure](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
