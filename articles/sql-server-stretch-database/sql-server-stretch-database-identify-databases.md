<properties
    pageTitle="Identifier des bases de données et les tables de base de données de l’étirement d’en exécutant le Gestionnaire de base de données Stretch | Microsoft Azure"
    description="Apprenez à identifier les bases de données et les tables qui sont candidats à l’échelle de base de données."
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
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identifier des bases de données et les tables de base de données de l’étirement d’en exécutant le Gestionnaire de base de données Stretch

Pour identifier les bases de données et les tables qui sont des candidats pour la base de données de l’échelle, téléchargez le Conseiller de mise à niveau de SQL Server 2016, puis exécutez le Conseiller de base de données d’étirement. Étirement Gestionnaire de base de données identifie également les problèmes de blocage.

## <a name="download-and-install-upgrade-advisor"></a>Télécharger et installer le Conseiller de mise à niveau
Télécharger et installer le Conseiller de mise à niveau à partir [d’ici](http://go.microsoft.com/fwlink/?LinkID=613421). Cet outil n’est pas inclus sur le support d’installation de SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Exécutez le Conseiller de l’étirement de la base de données

1.  Exécutez le Conseiller de mise à niveau.

2.  Sélectionnez des **scénarios**et sélectionnez **Exécuter le conseiller étirement de base de données**.

3.  Sur la blade **d’Exécuter le conseiller étirement de base de données** , cliquez sur **Sélectionner les bases de données à analyser**.

4.  Sur la lame de **Sélectionner des bases de données** , entrez ou sélectionnez le nom du serveur et les informations d’authentification. Cliquez sur **se connecter**.

5.  La liste des bases de données sur le serveur sélectionné s’affiche. Sélectionnez les bases de données que vous souhaitez analyser. Cliquez sur **Sélectionner**.

6.  Sur la blade **d’Exécuter le conseiller étirement de base de données** , cliquez sur **exécuter**.  L’analyse s’exécute.

## <a name="review-the-results"></a>Passez en revue les résultats

1.  Lorsque l’analyse est terminée, sur la blade **d’analyser de bases de données** , sélectionnez une des bases de données qui analyse pour afficher la **résultats de l’analyse** de lame.

    La lame de **résultats de l’analyse** répertorie les tables recommandés dans la base de données sélectionné qui correspondent aux critères de recommandation par défaut.

2.  Dans la liste des tables sur la lame de **résultats de l’analyse** , sélectionnez une des tables pour afficher la **résultats dans le tableau** de lame recommandées.

    Si les problèmes de blocage, la lame de **résultats dans le tableau** répertorie les problèmes de blocage de la table sélectionnée. Pour plus d’informations sur les problèmes de blocage détecté par le Gestionnaire de base de données d’étirement, consultez [Limitations pour étirement de base de données](sql-server-stretch-database-limitations.md).

3.  Dans la liste des problèmes de blocage de la lame de **résultats dans le tableau** , sélectionnez un des problèmes pour afficher plus d’informations sur le problème sélectionné et propose des facteurs d’atténuation. Si vous souhaitez configurer la table sélectionnée pour la base de données de l’échelle, mettre en œuvre les étapes d’atténuation suggérée.

## <a name="next-step"></a>Étape suivante
Activer l’extension de la base de données.

-   Pour permettre l’étirement de base de données dans une **base de données**, consultez [Stretch activer la base de données pour une base de données](sql-server-stretch-database-enable-database.md).

-   Pour activer les extension de base de données sur une autre **table**, lorsque l’extension est déjà activée sur la base de données, consultez [Stretch activer la base de données pour une table](sql-server-stretch-database-enable-table.md).

## <a name="see-also"></a>Voir aussi

[Limitations de l’étirement de la base de données](sql-server-stretch-database-limitations.md)

[Activer l’extension de base de données pour une base de données](sql-server-stretch-database-enable-database.md)

[Activer l’étirement de base de données d’une table](sql-server-stretch-database-enable-table.md)

[Toutes les rubriques pour le service de base de données de Azure SQL Server Stretch](sql-server-stretch-database-index-all-articles.md)
