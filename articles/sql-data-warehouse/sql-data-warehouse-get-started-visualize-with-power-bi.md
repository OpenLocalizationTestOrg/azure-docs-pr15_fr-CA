<properties
   pageTitle="Visualiser les données de magasin de données SQL avec alimentation BI Microsoft Azure"
   description="Visualiser les données de magasin de données SQL avec alimentation BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# <a name="visualize-data-with-power-bi"></a>Visualiser les données avec alimentation BI

> [AZURE.SELECTOR]
- [Alimentation BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprentissage automatique Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Ce didacticiel vous montre comment BI d’alimentation permet de se connecter au magasin de données SQL et créer quelques visualisations de base.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## <a name="prerequisites"></a>Conditions préalables

Pour exécuter ce didacticiel, vous devez :

- Un entrepôt de données SQL est préchargé avec la base de données AdventureWorksDW. Pour configurer cela, voir [créer un entrepôt de données SQL][] et choisissez de charger les données d’exemple. Si vous déjà avez un data warehouse mais n’avez pas d’exemples de données, vous pouvez [charger manuellement les données de l’exemple][].


## <a name="1-connect-to-your-database"></a>1. Connectez-vous à votre base de données

Ouvrez BI de puissance et se connecter à votre base de données AdventureWorksDW :

1. Connectez-vous au [portail Azure][].
2. Cliquez sur **bases de données SQL** et choisissez votre base de données AdventureWorks SQL Data Warehouse.

    ![Trouver votre base de données][1]

3. Cliquez sur le bouton « Ouvrir dans BI de puissance ».

    ![Bouton d’alimentation BI][2]

4. Vous devez maintenant voir la page de connexion de Data Warehouse de SQL affiche l’adresse web de votre base de données. Cliquez sur Suivant.

    ![Câble d’alimentation du BI][3]

6. Entrez votre nom d’utilisateur du serveur SQL d’Azure et mot de passe et vous sera entièrement connecté à votre base de données SQL Data Warehouse.

    ![Connexion d’alimentation BI][4]

7. Une fois que vous êtes connecté à BI d’alimentation, cliquez sur le groupe de données AdventureWorksDW sur la lame de gauche. La base de données s’ouvre.

    ![AdventureWorksDW ouvert de puissance BI][5]



## <a name="2-create-a-report"></a>2. création d’un rapport

Vous êtes maintenant prêt à BI d’alimentation permet d’analyser vos exemples de données AdventureWorksDW. Pour effectuer l’analyse, AdventureWorksDW a une vue appelée AggregateSales. Cette vue contient quelques-unes des mesures clés pour l’analyse des ventes de la société.

1. Pour créer un mappage de valeur de vente en fonction du code postal, dans le volet de droite de champs, cliquez sur la vue AggregateSales pour la développer. Cliquez sur les colonnes de code postal et de montant pour les sélectionner.

    ![Alimentation BI sélectionnez AggregateSales][6]

    Alimentation BI reconnaît automatiquement il s’agit des données géographiques et placez-le dans un mappage pour vous.

    ![Cartographie de puissance du BI][7]

2. Cette étape crée un graphique à barres qui indique le montant des ventes par revenu du client. Pour créer ce détailler l’affichage AggregateSales. Cliquez sur le champ de montant. Faites glisser le champ revenu du client vers la gauche et déposez-le dans l’axe.

    ![Axe de sélection d’alimentation BI][8]

    Nous avons déplacé le graphique à barres sur la côté gauche.

    ![Barre de BI de puissance][9]

3. Cette étape crée un graphique en courbes qui indique le montant des ventes par date de commande. Pour créer ce détailler l’affichage AggregateSales. Cliquez sur montant et date commande. Dans les visualisations de colonne, cliquez sur l’icône de graphique en courbes ; Il s’agit de la première icône dans la deuxième ligne sous les visualisations.

    ![Graphique en courbes select BI d’alimentation][10]

    Vous avez maintenant un état qui affiche les trois différentes visualisations de données.

    ![Ligne de BI de puissance][11]

Vous pouvez enregistrer votre progression à tout moment en cliquant sur **fichier** , **Enregistrer**la sélection.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que nous avons donné du temps pour le préchauffage avec les exemples de données, reportez-vous à la section Comment [développer][], [charger][]ou [migrer][]. Ou consultez sur le [site Web de Power BI][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[effectuer la migration]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md
[charge]: sql-data-warehouse-overview-load.md
[charger les données d’exemple manuellement]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Créez un entrepôt de données SQL]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure portal]: https://portal.azure.com/
[Site Web de Power BI]: http://www.powerbi.com/
