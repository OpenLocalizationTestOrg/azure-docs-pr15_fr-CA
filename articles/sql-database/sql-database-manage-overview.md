<properties
    pageTitle="La vue d’ensemble : outils de gestion pour SQL de base de données | Microsoft Azure"
    description="Compare les outils et options de gestion de base de données de SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="sstein"/>

# <a name="overview-management-tools-for-sql-database"></a>La vue d’ensemble : outils de gestion pour une base de données de SQL

Cette rubrique présente et compare les outils et les options de gestion des bases de données SQL d’Azure afin que vous pouvez choisir l’outil approprié pour la tâche, votre entreprise et vous. Choisir l’outil approprié dépend du nombre de bases de données vous gérer, la tâche, et la fréquence à laquelle une tâche est effectuée.

## <a name="azure-portal"></a>Azure portal

Le [portail Azure](https://portal.azure.com) est une application basée sur le web où vous pouvez créer, mettre à jour et de supprimer des serveurs logiques et des bases de données et surveiller l’activité de la base de données. Cet outil est idéal si vous êtes novice avec Azure, gestion de plusieurs bases de données, ou que vous devez faire quelque chose rapidement.

Pour plus d’informations sur l’utilisation du portail, voir [Gérer les bases de données de SQL à l’aide du portail Azure](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio et les outils de données de SQL Server dans Visual Studio

SQL Server Management Studio (SSMS) et les outils de données SQL Server (SSDT) sont des outils de clients qui s’exécutent sur votre ordinateur de gestion et de développement de votre base de données dans le nuage. Si vous êtes un développeur d’applications familiarisé avec Visual Studio ou d’autres environnements de développement intégré (IDE), [essayez d’utiliser SSDT dans Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). De nombreux administrateurs de base de données connaissent de SSMS, qui peut être utilisé avec des bases de données SQL d’Azure. [Télécharger la version la plus récente de SSMS](https://msdn.microsoft.com/library/mt238290) et toujours utiliser la version la plus récente lorsque vous travaillez avec une base de données de SQL Azure. Pour plus d’informations sur la gestion de vos bases de données SQL Azure avec SSMS, voir [Gérer les bases de données de SQL à l’aide de SSMS](sql-database-manage-azure-ssms.md).

> [AZURE.IMPORTANT] Utilisez toujours la version la plus récente de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) et les [Outils de données de SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL.


## <a name="powershell"></a>PowerShell

Vous pouvez utiliser PowerShell pour gérer les bases de données et les pools élastique de la base de données et pour automatiser les déploiements de ressource Azure. Microsoft vous recommande de cet outil pour gérer un grand nombre de bases de données et l’automatisation du déploiement et modifications de ressources dans un environnement de production.

Pour plus d’informations, voir [Gérer la base de données SQL avec PowerShell](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Outils de base de données élastiques
Permet d’effectuer des actions telles que les outils de base de données élastique 

* L’exécution d’un script T-SQL par rapport à un ensemble de bases de données à l’aide d’un [élastique travail](sql-database-elastic-jobs-overview.md)
* Déplacement de bases de données de modèle de plusieurs utilisateurs à un modèle de mono-utilisateur avec l' [outil de fusion et fractionnement](sql-database-elastic-scale-overview-split-and-merge.md)
* Gestion des bases de données dans un modèle mono-utilisateur ou plusieurs utilisateurs à l’aide de la [bibliothèque de client évolutivité élastique](sql-database-elastic-database-client-library.md).
 

## <a name="additional-resources"></a>Ressources supplémentaires

- [Gestionnaire de ressources Azure](https://azure.microsoft.com/features/resource-manager/)
- [Automation Azure](https://azure.microsoft.com/documentation/services/automation/)
- [Planificateur d’Azure](https://azure.microsoft.com/documentation/services/scheduler/)