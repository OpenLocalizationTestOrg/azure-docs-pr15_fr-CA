<properties
    pageTitle="Vue d’ensemble du développement de base de données SQL | Microsoft Azure"
    description="Obtenir des informations sur les bibliothèques de connectivité disponibles et les meilleures pratiques pour les applications se connectant à la base de données de SQL."
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="annemill"/>

# <a name="sql-database-development-overview"></a>Vue d’ensemble du développement de base de données SQL
Cet article décrit les considérations de base dont un développeur doit être conscient lors de l’écriture de code de connexion à la base de données de SQL Azure.

## <a name="language-and-platform"></a>Plate-forme et langage
Exemples de code sont disponibles pour diverses plates-formes et langages de programmation. Vous trouverez des liens vers les exemples de code sur : 

* Plus d’informations : [les bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md)

## <a name="resource-limitations"></a>Limitations de ressource
Une base de données SQL Azure gère les ressources disponibles pour une base de données à l’aide de deux mécanismes différents : l’application de limites et de la gouvernance de ressources.

* Plus d’informations : [les limites des ressources de base de données de SQL Azure](sql-database-resource-limits.md)

## <a name="security"></a>Sécurité
Une base de données SQL Azure fournit des ressources pour limiter l’accès, la protection des données et la surveillance des activités sur une base de données SQL.

* Plus d’informations : [sécurisation de votre base de données SQL](sql-database-security.md)

## <a name="authentication"></a>Authentification
* Une base de données SQL Azure prend en charge les utilisateurs de l’authentification SQL Server et les connexions, ainsi que [l’authentification Azure Active Directory](sql-database-aad-authentication.md) utilisateurs et connexions.
* Vous devez spécifier une base de données particulière, au lieu de l’utilisation de la base de données *master* par défaut.
* Vous ne pouvez pas utiliser l’instruction Transact-SQL à **utiliser myDatabaseName ;** sur la base de données de SQL pour basculer vers une autre base de données.
* Plus d’informations : [sécurité de la base de données de SQL : gestion de la sécurité d’accès et de connexion de base de données](sql-database-manage-logins.md)

## <a name="resiliency"></a>Résilience
Lorsqu’une erreur passagère se produit lors de la connexion à la base de données de SQL, votre code doit renouveler l’appel.  Nous vous recommandons que réessayer logique de backoff utilisation logique, afin qu’il ne pas saturer la base de données SQL avec une nouvelle tentative simultanée de plusieurs clients.

* Exemples de code : pour les exemples de code qui illustrent les tentatives de logique, consultez les exemples correspondant à la langue de votre choix à : [bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md)
* Plus d’informations : [les messages d’erreur pour les programmes de clients de la base de données SQL](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Gestion des connexions
* Dans la logique de connexion client, remplacer le délai par défaut est 30 secondes.  La valeur par défaut de 15 secondes est trop courte pour connexions qui dépendent d’internet.
* Si vous utilisez un [pool de connexions](http://msdn.microsoft.com/library/8xx3tyca.aspx), veillez à fermer la connexion de l’instantanée de votre programme pas activement l’utilise et ne prépare pas à réutiliser.

## <a name="network-considerations"></a>Considérations sur le réseau
* Sur l’ordinateur qui héberge votre programme client, assurez-vous que le pare-feu autorise les communications TCP sortantes sur le port 1433.  Plus d’informations : [configurer un pare-feu de base de données de SQL Azure](sql-database-configure-firewall-settings.md)
* Si votre programme client se connecte à SQL de base de données V12 alors que votre client s’exécute sur une machine virtuelle Azure (VM), vous devez ouvrir certaines plages de port sur l’ordinateur virtuel. Plus d’informations : [Ports autres que le 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
* Parfois, les connexions client à V12 de base de données SQL Azure ignorer le proxy et interagissent directement avec la base de données. Ports de 1433 sont importants. Plus d’informations : [Ports autres que le 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Ont des données avec évolutivité élastique
Évolutivité élastique simplifie le processus de mise à l’échelle à l’extraction (et). 

* [Modèles de design pour les Applications de base de données SQL Azure mutualisée SaaS pour](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)
* [Mise en route avec l’évolutivité élastique Aperçu avant de la base de données SQL Azure](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Étapes suivantes

Découvrez toutes les [fonctionnalités de base de données de SQL](https://azure.microsoft.com/services/sql-database/).
