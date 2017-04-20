<properties
    pageTitle="Excel de se connecter à la base de données SQL | Microsoft Azure"
    description="Découvrez comment Microsoft Excel de se connecter à la base de données SQL d’Azure dans le nuage. Importer des données dans Excel pour l’exploration de données et de rapports."
    services="sql-database"
    keywords="se connecter excel à sql, d’importer des données vers excel"
    documentationCenter=""
    authors="joseidz"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/05/2016"
    ms.author="joseidz"/>


# <a name="sql-database-tutorial-connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Didacticiel de la base de données de SQL : Excel de se connecter à une base de données Azure SQL et créer un rapport

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Découvrez comment connecter Excel à une base de données SQL dans le nuage afin de pouvoir importer des données et créer des tableaux et des graphiques en fonction des valeurs dans la base de données. Dans ce didacticiel, que vous allez définir la connexion entre Excel et une table de base de données, enregistrez le fichier qui stocke les données et les informations de connexion pour Excel et créez ensuite un graphique croisé dynamique à partir des valeurs de base de données.

Vous aurez besoin une base de données SQL Azure avant de commencer. Si vous n’en avez pas, consultez [créer votre première base de données SQL](sql-database-get-started.md) pour obtenir une base de données avec des exemples de données et en cours d’exécution dans quelques minutes. Dans cet article, vous allez importer les exemples de données dans Excel à partir de cet article, mais vous pouvez suivre une procédure similaire sur vos propres données.

Vous aurez également besoin d’une copie de Microsoft Excel. Cet article utilise [Microsoft Excel 2016](https://products.office.com/en-US/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Excel de se connecter à une base de données SQL et de créer un fichier odc

1.  Pour utiliser Excel de la base de données SQL, ouvrez Excel et créer un nouveau classeur ou ouvrez un classeur Excel existant.

2.  Dans la barre de menus en haut de la page sur **les données**, cliquez sur **à partir d’autres Sources**, puis cliquez sur **à partir de SQL Server**.

    ![Sélectionner les données source : Excel de se connecter à la base de données SQL.](./media/sql-database-connect-excel/excel_data_source.png)

    L’Assistant connexion de données s’ouvre.

3.  Dans la boîte de dialogue **se connecter au serveur de base de données** , tapez le SQL de base de données **nom de serveur** vous souhaitez vous connecter sous la forme <*nom_serveur*>**. database.windows.net**. Par exemple, **adworkserver.database.windows.net**.

4.  Sous **références de connexion**, cliquez sur **utiliser le nom d’utilisateur et le mot de passe suivant**, tapez le **Nom d’utilisateur** et le **mot de passe** vous définissez pour le serveur de base de données SQL lorsque vous l’avez créé, puis cliquez sur **suivant**.

    ![Tapez les références de nom et de la connexion du serveur](./media/sql-database-connect-excel/connect-to-server.png)

    > [AZURE.TIP] En fonction de votre environnement réseau, vous ne pourrez pas vous connecter ou vous risquez de perdre la connexion si le serveur de base de données SQL n’autorise pas le trafic à partir de votre adresse IP du client. Accédez au [portail Azure](https://portal.azure.com/)et cliquez sur les serveurs SQL, cliquez sur votre serveur, cliquez sur pare-feu sous paramètres ajouter votre adresse IP du client. Pour plus d’informations, consultez [comment configurer les paramètres du pare-feu](sql-database-configure-firewall-settings.md) .

5. Dans la boîte de dialogue **Sélectionner la base de données et de Table** , sélectionnez la base de données que vous souhaitez utiliser dans la liste, puis cliquez sur les tables ou les vues que vous souhaitez utiliser (nous avons choisi **vGetAllCategories**), puis cliquez sur **suivant**.

    ![Sélectionnez une base de données et une table.](./media/sql-database-connect-excel/select-database-and-table.png)

    La boîte de dialogue **Enregistrer un fichier de connexion de données et fin** s’ouvre, dans laquelle vous fournissez des informations sur le fichier de connexion (*.odc) de base de données Office que Excel utilise. Vous pouvez laisser les valeurs par défaut ou personnaliser vos sélections.

6. Vous pouvez laisser les valeurs par défaut, mais notez le **Nom de fichier** en particulier. Une **Description**, un **Nom convivial**et des **Mots clés de recherche** vous aident et autres utilisateurs n’oubliez pas que vous vous connectez et la connexion. Si vous souhaitez que les informations de connexion stockées dans le fichier odc, il peut mettre à jour lorsque vous y connecter, puis cliquez sur **Terminer**, cliquez sur **toujours tenter d’utiliser ce fichier pour actualiser les données** .

    ![Enregistrement d’un fichier odc](./media/sql-database-connect-excel/save-odc-file.png)

    La boîte de dialogue **Importer des données** s’affiche.

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importer les données dans Excel et créer un tableau croisé dynamique
Maintenant que vous avez établi la connexion et que le fichier est créé avec les informations de connexion et les données, vous avez lu pour importer les données.

1. Dans la boîte de dialogue **Importer des données** , cliquez sur l’option souhaitée pour la présentation de vos données dans la feuille de calcul et puis cliquez sur **OK**. Nous avons choisi de **graphique croisé dynamique**. Vous pouvez également choisir de créer une **nouvelle feuille de calcul** ou pour **Ajouter ces données à un modèle de données**. Pour plus d’informations sur les modèles de données, voir [créer un modèle de données dans Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Cliquez sur **Propriétés** pour Explorer les informations sur le fichier odc que vous avez créé à l’étape précédente et choisir les options d’actualisation des données.

    ![Choisir le format des données dans Excel](./media/sql-database-connect-excel/import-data.png)

    La feuille a désormais un tableau croisé dynamique vide et un graphique.

8. Sous **Champs de tableau croisé dynamique**, sélectionnez toutes les cases à cocher pour les champs que vous souhaitez afficher.

    ![Configurer l’état de la base de données.](./media/sql-database-connect-excel/power-pivot-results.png)

> [AZURE.TIP]Si vous souhaitez connecter d’autres feuilles de calcul et de classeurs Excel à la base de données, cliquez sur **données**et cliquez sur **connexions**, cliquez sur **Ajouter**, sélectionnez la connexion que vous avez créé dans la liste, puis cliquez sur **Ouvrir**.
> ![Ouvrir une connexion à partir d’un autre classeur](./media/sql-database-connect-excel/open-from-another-workbook.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment faire pour [se connecter à la base de données SQL avec SQL Server Management Studio](sql-database-connect-query-ssms.md) pour interrogation avancée et l’analyse.
- Découvrez les avantages des [pools élastiques](sql-database-elastic-pool.md).
- Découvrez comment [créer une application web qui se connecte à la base de données SQL sur le serveur principal](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
