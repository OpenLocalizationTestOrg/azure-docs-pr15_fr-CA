<properties
    pageTitle="Étirer la vue d’ensemble de la base de données | Microsoft Azure"
    description="Découvrez comment étirement de la base de données de migre vos données à froid en toute sécurité et en toute transparence vers le nuage Microsoft Azure."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="stretch-database-overview"></a>Étirer la vue d’ensemble de la base de données

Étirement de base de données migre vos données à froid en toute sécurité et en toute transparence vers le nuage Microsoft Azure.

Si vous souhaitez simplement commencer avec étirement de base de données immédiatement, consultez [mise en route en exécutant la base de données pour étirer Assistant Activer](sql-server-stretch-database-wizard.md).

## <a name="what-are-the-benefits-of-stretch-database"></a>Quels sont les avantages de l’étirement de base de données ?
Étirement de base de données offre les avantages suivants :

### <a name="provides-cost-effective-availability-for-cold-data"></a>Fournit des coûts\-disponibilité efficace pour les données à froid
Étirer à chaud et à froid des données transactionnelles dynamiquement à partir de SQL Server vers Microsoft Azure avec base de données de SQL Server Stretch. Contrairement au stockage de ces données par défaut, vos données sont toujours en ligne et disponibles pour la requête. Vous pouvez fournir des plannings de rétention de données plus sans rompre la banque pour les tables volumineuses comme l’historique des commandes client. Bénéficier du faible coût de Azure plutôt que mise à l’échelle coûteux, sur\-locaux de stockage. Vous choisissez le niveau de tarification et que vous configurez des paramètres dans le portail Azure pour garder le contrôle des coûts. Mettre à l’échelle vers le haut ou vers le bas en fonction des besoins. Visitez la page de [Prix de base de données SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) pour plus de détails.

### <a name="doesnt-require-changes-to-queries-or-applications"></a>Ne nécessite pas les modifications apportées à des requêtes ou des applications
Accéder à vos données de SQL Server en toute transparence quel que soit le s’il s’agit\-locaux ou étiré vers le nuage.  Vous définissez la stratégie qui détermine où les données sont stockées, et que SQL Server gère le déplacement des données en arrière-plan. L’ensemble du tableau est toujours en ligne et pouvant être interrogé. Et, base de données de l’échelle ne nécessite pas les modifications apportées aux requêtes existantes ou des applications, l’emplacement des données est totalement transparent pour l’application.

### <a name="streamlines-on-premises-data-maintenance"></a>Rationalise sur\-locaux la gestion des données
Réduire sur\-locaux de gestion et stockage pour vos données. Sauvegardes pour votre sur\-locaux de données s’exécutent plus rapidement et de fin dans la fenêtre de maintenance. Pour la partie de nuage de vos données, les sauvegardes s’exécutent automatiquement. Votre sur\-les besoins de stockage locaux sont considérablement réduits. Stockage Azure peut être de 80 % moins cher que l’ajout d’on\-SSD des locaux.

### <a name="keeps-your-data-secure-even-during-migration"></a>Conserve vos données sécurisées même lors de la migration
Profitez de la tranquillité d’esprit que vous étirez vos applications les plus importantes en toute sécurité vers le nuage. De SQL Server sont toujours cryptés fournit un cryptage de vos données en mouvement. Sécurité de niveau ligne (RLS) et d’autres fonctionnalités de sécurité avancées de SQL Server fonctionnent également avec base de données de l’étirement pour protéger vos données.

## <a name="what-does-stretch-database-do"></a>Quel est le rôle de base de données de l’échelle ?
Après avoir activé l’étirement de base de données pour une instance de SQL Server, une base de données et au moins une table, base de données de l’étirement commence en mode silencieux migrer vos données vers Azure.

-   Si vous stockez des données dans une table distincte, vous pouvez migrer l’ensemble du tableau.

-   Si votre table contient des données à la fois à chaud et à froid, vous pouvez spécifier une fonction de filtre pour sélectionner les lignes à migrer.

**Vous n’êtes pas obligé de modifier les requêtes existantes et des applications clientes.** Vous avez encore d’un accès transparent aux données locales et à distance, même au cours de la migration des données. Il existe une petite quantité de latence pour les requêtes distantes, mais vous rencontrez uniquement cette latence lors de l’interrogation de données à froid.

**Que étirement de base de données permet de s’assurer qu’aucune donnée n’est perdue** en cas d’échec lors de la migration. Il possède également une logique de pour gérer les problèmes de connexion peuvent se produire lors de la migration. Une vue de gestion dynamique fournit l’état de la migration.

**Vous pouvez suspendre la migration des données** pour résoudre les problèmes sur le serveur local ou pour optimiser la bande passante réseau disponible.

![Vue d’ensemble de l’étirement de la base de données][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>Est la base de données de l’étirement pour vous ?
Si vous pouvez effectuer les instructions suivantes, base de données de l’étirement peut aider à répondre à vos besoins et à résoudre vos problèmes.

|Si vous êtes un décideur|Si vous êtes un utilisateur DBA|
|------------------------------|-------------------|
|Je dois conserver des données transactionnelles pour un certain temps.|La taille de Mes tableaux est mise en route du contrôle.|
|J’ai parfois interroger les données à froid.|Mes utilisateurs dire qu’ils veulent accéder à ces données, mais elles l’utilisent rarement.|
|Je dispose d’applications, y compris les anciennes applications, que vous ne souhaitez pas mettre à jour.|Dois-je conserver achat et ajout de stockage.|
|Je veux trouver un moyen de réaliser des économies sur le stockage.|Impossible de sauvegarder ou de restaurer ces tables volumineuses dans le contrat SLA.|

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>Quel type de bases de données et de tables sont candidats à l’échelle de base de données ?
Étirement de base de données cible avec de grandes quantités de données, généralement stockées dans un petit nombre de tables de bases de données transactionnelles. Ces tables peuvent contenir plus d’un milliard de lignes.

Si vous utilisez la fonction temporelle de table de SQL Server 2016, utilisez étirement de base de données à transférer tout ou partie de la table de l’historique associé aux coûts\-stockage efficace dans Azure. Pour plus d’informations, consultez [Gérer rétention de données historiques dans les Tables système version temporelle](https://msdn.microsoft.com/library/mt637341.aspx).

Étirer le Conseiller de base de données, une fonctionnalité de SQL Server 2016 Upgrade Advisor, permet d’identifier les bases de données et les tables de base de données de l’échelle. Pour plus d’informations, consultez [identification de bases de données et des tables de base de données de l’échelle](sql-server-stretch-database-identify-databases.md). Pour en savoir plus sur les problèmes de blocage potentiels, consultez [Limitations pour étirement de base de données](sql-server-stretch-database-limitations.md).

## <a name="test-drive-stretch-database"></a>Tester le lecteur de base de données d’étirement
**Testez le lecteur de base de données de l’étirement de la base de données exemple AdventureWorks.** Pour obtenir la base de données AdventureWorks, télécharger au moins le fichier de base de données et le fichier d’exemples et de scripts à partir [d’ici](https://www.microsoft.com/download/details.aspx?id=49502). Après avoir restauré la base de données exemple à une instance de SQL Server 2016, décompressez le fichier d’exemples et ouvrir le fichier d’exemples de DB Stretch à partir du dossier de l’étirement de la base de données. Exécuter les scripts dans ce fichier pour vérifier l’espace utilisé par vos données avant et après avoir activé l’étirement de la base de données, pour suivre la progression de la migration des données et pour confirmer que vous pouvez continuer à interroger les données existantes et en insérer de nouvelles données à la fois pendant et après la migration des données.

## <a name="next-step"></a>Étape suivante
**Identifier les bases de données et les tables qui sont candidats à l’échelle de base de données.** Téléchargez le Conseiller de mise à niveau de SQL Server 2016, puis exécutez le Conseiller de base de données de Stretch pour identifier les bases de données et les tables qui sont candidats à l’échelle de base de données. Étirement Gestionnaire de base de données identifie également les problèmes de blocage. Pour plus d’informations, consultez [identification de bases de données et des tables de base de données de l’échelle](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png
