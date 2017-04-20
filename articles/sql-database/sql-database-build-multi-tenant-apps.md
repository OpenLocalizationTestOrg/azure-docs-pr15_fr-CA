<properties
   pageTitle="Base de données SQL Azure génère des applications mutualisées avec efficacité et d’Isolation"
   description="Découvrez comment la base de données de SQL génère mutualisées apps"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="builds-multi-tenant-apps-with-azure-sql-database-with-isolation-and-efficiency"></a>Génère des applications mutualisées avec base de données SQL Azure avec efficacité et d’Isolation

## <a name="leverage-elastic-pools-and-build-more-efficient-multi-tenant-apps"></a>Exploiter les pools élastiques et de créer des applications plus efficaces de plusieurs utilisateurs

Si vous êtes un dev SaaS écrire une application mutualisée et gère de nombreux clients, vous souvent faire des compromis au client performances, gestion et sécurité. Avec Azure SQL de base de données élastique de base de données des Pools, vous n’avez plus à faire ce compromis. Ces pools permettent de gérer et de surveiller les applications mutualisées et bénéficiez des avantages de l’isolation d’un client-par-bases de données. Consultez les [modèles de conception pour les Applications de base de données SQL Azure mutualisée SaaS pour](sql-database-design-patterns-multi-tenancy-saas-applications.md).

![build-multi-client-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## <a name="auto-scaling-you-control"></a>Mise à l’échelle à contrôler

Pools de redimensionner automatiquement les performances et la capacité de stockage pour bases de données élastiques à la volée. Vous pouvez contrôler les performances affectés à un pool, ajouter ou supprimer élastiques bases de données à la demande et définissent les performances des bases de données élastiques sans affecter le coût global du pool. Cela signifie que vous n’avez pas à vous soucier de la gestion de l’utilisation de bases de données individuelles.

[Lire la documentation](sql-database-elastic-pool.md)

## <a name="intelligent-management-of-your-environment"></a>Gestion intelligente de votre environnement

Recommandations de dimensionnement intégré identifient de façon proactive les bases de données qui pourraient bénéficier de pools. Ces recommandations permettent l’analyse « what-if » pour l’optimisation de rapide atteindre vos objectifs de performances. Enrichi suivi des performances et résolution des problèmes de tableaux de bord vous aident à visualiser l’utilisation du pool historique.

[Lire la documentation](sql-database-elastic-pool-guidance.md)

## <a name="performance-and-price-to-meet-your-needs"></a>Performances et prix adaptés à vos besoins

Basique, Standard, et les pools de prime vous offrent un large éventail de performances, stockage et les options de tarification. Les pools peuvent contenir des bases de données jusqu'à 400 élastiques. Les bases de données élastiques peuvent ajuster automatiquement jusqu'à 1000 unités de transaction de la base de données élastique (eDTU).

[Lire la documentation](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## <a name="elastic-tools"></a>Outils élastiques

Les fonctionnalités de base de données SQL pour aider à gérer les activités opérationnelles entre plusieurs bases de données sont en plus des pools élastiques :

**Effectuer des requêtes de bases de données croisées et la création de rapports.**  
[Requête de base de données élastique](sql-database-elastic-query-overview.md) vous permet exécuter des requêtes ou rapports sur les bases de données dans votre pool élastique et accéder aux données distantes stockées dans plusieurs bases de données de votre pool à la fois.

**Exécution de transactions des bases de données croisées.**  
[Transactions élastique de bases de données](sql-database-elastic-transactions-overview.md) vous permettent d’exécuter des transactions qui couvrent plusieurs bases de données dans les bases de données SQL et exécuter des opérations (par exemple, lors du traitement des transactions financières sur bases de données, ou lors de la mise à jour du stock dans une base de données et les commandes).

**Exécute les mêmes opérations sur plusieurs bases de données.**  
[Travaux d’élastique de la base de données](sql-database-elastic-jobs-overview.md) d’exécuter des opérations administratives telles que la reconstruction d’index ou de mise à jour des schémas dans chaque base de données dans le pool élastique.

Accédez à la page d’accueil pour voir ce que l’autre base de données de SQL peut offrir.
[Regarde](https://azure.microsoft.com/services/sql-database/) 

## <a name="next-steps"></a>Étapes suivantes

Obtenir un [abonnement gratuit d’Azure](https://azure.microsoft.com/get-started/) et de [créer votre première base de données de SQL Azure](sql-database-get-started.md).

## <a name="additional-resources"></a>Ressources supplémentaires

Découvrez toutes les [fonctionnalités de base de données de SQL](https://azure.microsoft.com/services/sql-database/).
 
Consultez [présentation technique de la base de données de SQL](sql-database-technical-overview.md).  
