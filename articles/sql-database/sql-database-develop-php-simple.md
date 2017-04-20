<properties
    pageTitle="Se connecter à la base de données de SQL à l’aide de PHP sur Windows | Microsoft Azure"
    description="Présente un exemple de programme PHP qui se connecte à la base de données de SQL Azure à partir d’un client Windows et fournit des liens vers les composants logiciels nécessaires requises par le client."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Se connecter à la base de données de SQL à l’aide de PHP sous Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Cette rubrique illustre comment vous pouvez vous connecter à la base de données de SQL Azure à partir d’une application cliente écrite dans PHP qui s’exécute sur Windows.

## <a name="step-1--configure-development-environment"></a>Étape 1 : Configurer l’environnement de développement

[Configurer l’environnement de développement pour le développement PHP](https://msdn.microsoft.com/library/mt720663.aspx)

## <a name="step-2-create-a-sql-database"></a>Étape 2 : Créer une base de données SQL

Consultez la [page de mise en route](sql-database-get-started.md) pour savoir comment créer une base de données exemple.  Il est important que vous suivez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.


## <a name="step-3-get-connection-details"></a>Étape 3 : Obtenir les détails de la connexion

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## <a name="step-4-run-sample-code"></a>Étape 4 : Exécution des exemples de code

* [Preuve de concept de connexion à SQL à l’aide de PHP](https://msdn.microsoft.com/library/mt720665.aspx)
* [Connexion élastique à SQL avec PHP](https://msdn.microsoft.com/library/mt720667.aspx)


## <a name="next-steps"></a>Étapes suivantes

* Consultez [vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* Plus d’informations sur le [Pilote de PHP de Microsoft pour SQL Server](https://msdn.microsoft.com/library/dn865013.aspx)
* Pour plus d’informations sur l’installation de PHP et l’utilisation, consultez [Accès à SQL Server bases de données avec PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## <a name="additional-resources"></a>Ressources supplémentaires 

* [Modèles de design pour les Applications de base de données SQL Azure mutualisée SaaS pour](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Découvrez toutes les [fonctionnalités de base de données de SQL](https://azure.microsoft.com/services/sql-database/)
