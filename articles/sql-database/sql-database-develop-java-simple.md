<properties
    pageTitle="Se connecter à la base de données de SQL avec JDBC sur Windows à l’aide de Java | Microsoft Azure"
    description="Présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données de SQL Azure. L’exemple utilise JDBC, et il s’exécute sur un ordinateur client Windows."
    services="sql-database"
    documentationCenter=""
    authors="LuisBosquez"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="lbosq"/>


# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Se connecter à la base de données de SQL avec JDBC sur Windows à l’aide de Java


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Cette rubrique présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données de SQL Azure. L’exemple Java s’appuie sur Java Development Kit (JDK) version 1.8. L’exemple se connecte à une base de données de SQL Azure en utilisant le pilote JDBC.

## <a name="step-1--configure-development-environment"></a>Étape 1 : Configurer l’environnement de développement

[Configurer l’environnement de développement pour le développement de Java](https://msdn.microsoft.com/library/mt720658.aspx)

## <a name="step-2-create-a-sql-database"></a>Étape 2 : Créer une base de données SQL

Consultez la [page de mise en route](sql-database-get-started.md) pour savoir comment créer une base de données.  

## <a name="step-3-get-connection-string"></a>Étape 3 : Récupérer la chaîne de connexion

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Si vous utilisez le pilote JDBC de JTDS, alors vous devrez ajouter « ssl = nécessitent » à l’URL de la connexion, chaîne et que vous devez définir l’option suivante pour la machine virtuelle Java «-Djsse.enableCBCProtection=false ». Cette option de JVM désactive un correctif pour un problème de sécurité, assurez-vous que vous comprenez quels risques intervient avant de définir cette option.

## <a name="step-4-run-sample-code"></a>Étape 4 : Exécution des exemples de code

* [Preuve de concept de connexion à SQL à l’aide de Java](https://msdn.microsoft.com/library/mt720656.aspx)

## <a name="next-steps"></a>Étapes suivantes

* Visitez le [Centre pour développeurs Java](/develop/java/).
* Consultez [vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* Plus d’informations sur le [Pilote JDBC de Microsoft pour SQL Server](https://msdn.microsoft.com/library/mt484311.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires 

* [Modèles de design pour les Applications de base de données SQL Azure mutualisée SaaS pour](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Découvrez toutes les [fonctionnalités de base de données de SQL](https://azure.microsoft.com/services/sql-database/)
