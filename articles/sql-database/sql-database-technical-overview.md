<properties
    pageTitle="Quelle est la base de données de SQL ? Présentation de la base de données SQL | Microsoft Azure"
    description="Présentation de la base de données SQL : détails techniques et les fonctionnalités de Microsoft relationnelles de base de données système de gestion (SGBDR) dans le nuage."
    keywords="Introduction à sql, Introduction à sql, ce qui est de la base de données sql"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="shkurhek"/>

# <a name="what-is-sql-database-introduction-to-sql-database"></a>Quelle est la base de données de SQL ? Présentation de la base de données SQL

Base de données de SQL est un service de base de données relationnelle en nuage basé sur le moteur de Microsoft SQL Server leaders sur le marché, avec des fonctions critiques. Base de données de SQL offre des performances prévisibles, l’évolutivité avec aucune interruption, la continuité d’activité et la protection des données — avec administration de proche de zéro. Vous pouvez vous concentrer sur le développement d’applications rapide et accélérer le temps de mise sur le marché, plutôt que Gestion des machines virtuelles et l’infrastructure. Parce qu’il est basé sur le moteur [De SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) , la base de données de SQL prend en charge des outils SQL Server existant, bibliothèques et des API, ce qui facilite le travail pour vous déplacer et d’étendre vers le nuage.

Cet article est une introduction sur les concepts de base de données de SQL et les fonctions liées aux performances, évolutivité et facilité de gestion, avec des liens pour Explorer les détails. Si vous êtes prêt à passer dans, vous pouvez [créer votre première base de données SQL](sql-database-get-started.md) ou [créer un pool d’élasticité de la base de données](sql-database-elastic-pool-create-portal.md) en quelques minutes. Si vous souhaitez obtenir de plus amples, regardez cette vidéo de 30 minutes.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## <a name="adjust-performance-and-scale-without-downtime"></a>Réglage des performances et une évolutivité sans interruption de service

Bases de données SQL est disponible dans Basic, Standard et Premium de *niveaux de service*. Chaque niveau de service offre [différents niveaux de performances et de fonctionnalités](sql-database-service-tiers.md) pour prendre en charge les lightweight aux charges de travail de base de données haute densité. Vous pouvez créer votre première application sur une petite base de données pour quelques euros chaque mois, puis [Modifier la couche de service](sql-database-scale-up.md) manuellement ou par programmation à tout moment que votre application va virale dans le monde entier, sans interruption de service pour votre application ou de vos clients.

Pour de nombreuses entreprises et les applications, la possibilité de créer des bases de données et l’accès vers le haut ou vers le bas les performances de la base de données unique à la demande est suffisante, en particulier si des modèles d’utilisation sont relativement prévisibles. Mais si vous avez des modèles d’utilisation imprévisibles, il peut le rendre difficile à gérer les coûts et votre modèle d’entreprise.

[Pools élastiques](sql-database-elastic-pool.md) dans la base de données de SQL pour résoudre ce problème. Le concept est simple. Vous affecter les performances à un pool et payez les performances collectives du pool plutôt que les performances de la base de données unique. Vous n’avez pas besoin de composer les performances de la base de données vers le haut ou vers le bas. Les bases de données dans le pool, appelée *élastiques bases de données*, à l’échelle automatiquement vers le haut et vers le bas pour la demande de réunion. Des bases de données élastiques consomment mais ne dépassent pas les limites du pool, donc le coût reste prévisible, même si l’utilisation de la base de données n’est pas. De plus, vous pouvez [Ajouter et supprimer des bases de données dans le pool](sql-database-elastic-pool-manage-portal.md), mise à l’échelle de votre application à partir d’un petit nombre de bases de données à des milliers, toutes les tâches dans un budget que vous contrôlez. Pour plus d’informations sur les modèles de conception pour les applications SaaS à l’aide de pools élastiques, voir [Modèles de Design pour les Applications SaaS mutualisée avec la base de données de SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Dans les deux cas, vous allez : simple ou élastique, vous n’êtes pas obligé de. Vous pouvez fusionner les bases de données individuelles avec des pools élastique de la base de données et modifier les niveaux de service de bases de données individuelles et des pools pour créer des designs innovants. En outre, avec la puissance et la portée d’Azure, vous pouvez mix and match des services Azure avec base de données SQL pour répondre à vos besoins de conception unique application moderne, l’efficacité des coûts et des ressources de lecteur et déverrouiller les nouvelles opportunités commerciales.

Mais comment vous pouvez comparer les performances relatives des bases de données et des pools de la base de données ? Comment savez-vous que le taquet droit cliquez sur lorsque vous composez le haut et vers le bas La réponse est l’unité de Transaction de base de données (DTU) pour les bases de données unique et le DTU élastique (eDTU) pour les bases de données élastiques et pools de la base de données. Reportez-vous à la section [les options de base de données SQL et les performances : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md) pour plus de détails.

## <a name="keep-your-app-and-business-running"></a>Conserver votre application et vos activités en cours d’exécution

Secteur principaux 99,99 % disponibilité contrat de niveau service du Azure [(SLA)](http://azure.microsoft.com/support/legal/sla/), alimenté par un réseau mondial de centres de données gérés par Microsoft, vous permet de conserver votre application 24 x 7 en cours d’exécution. Chaque base de données SQL, vous profitez de la protection des données intégrées, de tolérance de panne et de protection des données que vous auriez sinon de concevoir, d’acheter, de créer et de gérer. Même dans ce cas, en fonction des besoins de votre entreprise, vous pouvez exiger des couches supplémentaires de protection afin de garantir à votre application et votre entreprise peuvent récupérer rapidement en cas de sinistre, une erreur ou quelque chose d’autre. La base de données SQL chaque niveau de service offre un autre menu de fonctionnalités que vous pouvez utiliser pour obtenir et en cours d’exécution et de séjour de cette façon. Vous pouvez utiliser la restauration du point-à-temps pour renvoyer une base de données à un état antérieur, jusqu'à 35 jours. En outre, si le centre de données hébergeant les bases de données connaît une panne, vous pouvez basculer de duplications de base de données dans une autre région. Ou bien, vous pouvez utiliser des réplicas pour les mises à niveau ou d’un déménagement à différentes régions.

![Geo-réplication de base de données SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Pour plus d’informations sur les fonctionnalités de continuité d’activité différents disponibles pour les niveaux de service différents, reportez-vous à la section [La continuité d’activité](sql-database-business-continuity.md) .

## <a name="secure-your-data"></a>Sécurisation de vos données
SQL Server a une tradition solide de sécurité des données que la base de données de SQL respectueux avec des fonctionnalités qui limitent l’accès, protègent les données et vous aident à surveiller l’activité. Pour un récapitulatif rapide des options de sécurité dont vous disposez dans la base de données de SQL, consultez [sécurisation de votre base de données SQL](sql-database-security.md) . Consultez le [Centre de sécurité pour le moteur de base de données SQL Server et de base de données SQL](https://msdn.microsoft.com/library/bb510589) pour une vue plus complète de fonctionnalités de sécurité. Et bien, visitez le [Centre de confiance Azure](https://azure.microsoft.com/support/trust-center/security/) pour plus d’informations sur la sécurité de la plate-forme d’Azure.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez lu une présentation de base de données SQL et a répondu à la question « Quelle est base de données SQL ? », vous êtes prêt à :

- Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/sql-database/) pour la base de données unique et des comparaisons de coûts élastique de la base de données et les calculatrices.
- Obtenir des informations sur [les pools élastiques](sql-database-elastic-pool.md).
- Commencer par la [Création de votre première base de données](sql-database-get-started.md).
- [Se connecter et d’interroger avec SSMS](sql-database-connect-query-ssms.md)
- Créez votre première application C#, Java, Node.js, PHP, Python ou Ruby : [bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md)
- Consultez l’index des titres et des descriptions de [toutes les rubriques pour le service de base de données sql Azure](sql-database-index-all-articles.md).
