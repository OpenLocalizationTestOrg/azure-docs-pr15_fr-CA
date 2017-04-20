<properties
    pageTitle="Performances et prix de pool élastique de la base de données SQL"
    description="Informations de tarification spécifiques pour les pools élastique de la base de données."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="05/27/2016"
    ms.author="srinia"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="elastic-database-pool-billing-and-pricing-information"></a>Facturation de pool élastique de la base de données et les informations de tarification

Les pools élastique de la base de données sont à payer par les caractéristiques suivantes :

- Un pool élastique est facturé lors de sa création, même lorsqu’il n’y aucune base de données dans le pool.
- Un pool élastique est facturé toutes les heures. Il s’agit de la fréquence de contrôle de même en ce qui concerne les performances des bases de données unique.
- Si un pool élastique est redimensionné à un nouveau montant d’eDTUs, puis le pool n’est pas facturé selon le volume d’eDTUS jusqu'à la fin de l’opération de redimensionnement. Cela suit le même modèle que la modification du niveau de performances des bases de données autonomes.


- Le prix d’un pool élastique est basé sur le nombre d’eDTUs du pool. Le prix d’un pool élastique est indépendant du nombre et du taux d’utilisation des bases de données élastiques au sein de celui-ci.
- Prix est calculé par (nombre de pool eDTUs) x (prix unitaire par eDTU).

Le prix d’eDTU pour un pool élastique est supérieur au prix DTU d’unité pour une base de données autonome dans le même niveau de service. Pour plus d’informations, consultez [la tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). 


Pour comprendre les niveaux de service et d’eDTUs, consultez [performances et les options de base de données SQL](sql-database-service-tiers.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créer un pool d’élasticité de la base de données](sql-database-elastic-pool-create-portal.md)
- [Surveiller, de gérer et de dimensionner un pool élastique de la base de données](sql-database-elastic-pool-manage-portal.md)
- [Les options de base de données SQL et les performances : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md)
- [Script PowerShell pour identifier les bases de données appropriées pour un pool d’élasticité de la base de données](sql-database-elastic-pool-database-assessment-powershell.md)
