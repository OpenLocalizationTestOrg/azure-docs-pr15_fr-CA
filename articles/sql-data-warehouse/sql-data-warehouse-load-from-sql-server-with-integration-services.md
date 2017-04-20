<properties
   pageTitle="Charger des données à partir de SQL Server dans des entrepôts de données SQL (SSIS) de Azure | Microsoft Azure"
   description="Vous montre comment créer un package SQL Server Integration Services (SSIS) pour déplacer des données à partir d’un large éventail de sources de données SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;sonyama;barbkess"/>

# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Charger des données à partir de SQL Server dans des entrepôts de données SQL (SSIS) de Azure

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)


Créer un package SQL Server Integration Services (SSIS) pour charger des données à partir de SQL Server dans l’entrepôt de données SQL Azure. Vous pouvez éventuellement de restructuration, de transformer et de nettoyer les données lorsqu’elles traversent le flux de données SSIS.

Dans ce didacticiel, vous allez :

- Créez un nouveau projet Integration Services dans Visual Studio.
- Se connecter à des sources de données, y compris SQL Server (en tant que source) et SQL Data Warehouse (en tant que destination).
- Concevoir un package SSIS qui charge des données à partir de la source vers la destination.
- Exécutez le package SSIS pour charger les données.

Ce didacticiel utilise SQL Server comme source de données. SQL Server peut s’exécuter dans un local ou sur une machine virtuelle Azure.

## <a name="basic-concepts"></a>Concepts de base

Le package est l’unité de travail dans SSIS. Les packages associés sont regroupés dans des projets. Vous créez des projets et les packs de conception dans Visual Studio avec des outils de données de SQL Server. Le processus de conception est un processus visuel dans lequel vous faites glisser et déposez les composants depuis la boîte à outils vers l’aire de conception, les connectez et définissez leurs propriétés. Après avoir terminé votre package, vous pouvez éventuellement la possibilité de le déployer sur SQL Server pour la sécurité, de surveillance et de gestion complète.

## <a name="options-for-loading-data-with-ssis"></a>Options de chargement des données avec SSIS

SQL Server Integration Services (SSIS) est un ensemble d’outils flexibles qui fournit de nombreuses options pour la connexion et chargement de données dans Data Warehouse de SQL.

1. Une Destination de NET ADO permet de se connecter au magasin de données SQL. Ce didacticiel utilise une Destination de NET ADO, car il comporte les options de configuration minimum.
2. Une Destination OLE DB permet de se connecter au magasin de données SQL. Cette option peut fournir des performances légèrement meilleures que la Destination de NET ADO.
3. Utilisez la tâche de téléchargement de Blob Azure pour reclasser les données dans le stockage Blob Azure. Puis utilisez la tâche SQL exécution de SSIS pour lancer un script Polybase qui charge les données dans l’entrepôt de données SQL. Cette option fournit les meilleures performances parmi les trois options répertoriées ici. Pour obtenir la tâche de téléchargement de Blob Azure, téléchargez [Microsoft SQL Server 2016 Integration Services Feature Pack pour Azure][]. Pour en savoir plus sur Polybase, consultez le [Guide de le PolyBase][].

## <a name="before-you-start"></a>Avant de commencer

Pour exécuter ce didacticiel, vous devez :

1. **SQL Server Integration Services (SSIS)**. SSIS est un composant de SQL Server et nécessite une version d’évaluation ou une version sous licence de SQL Server. Pour obtenir une version d’évaluation de SQL Server 2016 aperçu, consultez les [Évaluations de SQL Server][].
2. **Visual Studio**. Pour obtenir le Visual Studio 2015 version Community Edition gratuite, reportez-vous à la section [Communauté de Visual Studio][].
3. **Outils de données de SQL Server de Visual Studio (SSDT)**. Pour obtenir les outils de données de SQL Server pour Visual Studio 2015, consultez [Outils SQL Server données télécharger (SSDT)][].
4. **Exemples de données**. Ce didacticiel utilise des exemples de données stockées dans la base de données AdventureWorks dans SQL Server comme la source de données à charger dans le magasin de données SQL. Pour obtenir la base de données AdventureWorks, consultez [Bases de données AdventureWorks 2014][].
5. **Autorisations et la base de données de l’entrepôt de données SQL A**. Ce didacticiel se connecte à une instance SQL Data Warehouse et charge les données. Vous devez disposer d’autorisations pour créer un tableau et chargement des données.
6. **Une règle de pare-feu**. Vous devez créer une règle de pare-feu sur SQL Data Warehouse avec l’adresse IP de votre ordinateur local avant de télécharger des données vers le Data Warehouse de SQL.

## <a name="step-1-create-a-new-integration-services-project"></a>Étape 1 : Créer un nouveau projet Integration Services

1. Lancez Visual Studio 2015.
2. Dans le menu **fichier** , sélectionnez Nouveau **| Projet**.
3. Accédez à le **installé | Modèles | Analyse décisionnelle | Services d’intégration** types de projets.
4. Sélectionnez le **projet Integration Services**. Fournir des valeurs pour le **nom** et **l’emplacement**et cliquez sur **OK**.

Visual Studio ouvre et crée un nouveau projet Integration Services (SSIS). Visual Studio ouvre le concepteur pour le seul nouveau package SSIS (Package.dtsx) dans le projet. Vous voyez les zones d’écran suivantes :

- Sur la gauche, la **boîte à outils** de composants SSIS.
- Au milieu, l’aire de conception, avec plusieurs onglets. Vous utilisez généralement au moins le **Flux de contrôle** et les onglets de **Flux de données** .
- Sur la droite, l' **Explorateur de solutions** et les volets de **Propriétés** .

    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Étape 2 : Créer le flux de données de base

1. Faites glisser une tâche de flux de données à partir de la boîte à outils vers le centre de l’aire de conception (sous l’onglet **Flux de contrôle** ).

    ![][02]

2. Double-cliquez sur la tâche de flux de données pour basculer vers l’onglet flux de données.
3. Dans la liste d’autres Sources dans la boîte à outils, faites glisser une Source ADO.NET à l’aire de conception. Avec l’adaptateur de source étant toujours sélectionné, modifiez son nom à **la source de SQL Server** dans le volet **Propriétés** .
4. Dans la liste des autres Destinations dans la boîte à outils, faites glisser une Destination ADO.NET à l’aire de conception sous la Source ADO.NET. Avec l’adaptateur de destination étant toujours sélectionné, modifiez son nom à la **destination du Data Warehouse de SQL** dans le volet **Propriétés** .

    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Étape 3 : Configurer l’adaptateur de source

1. Double-cliquez sur l’adaptateur de source pour ouvrir l' **Éditeur de code Source de ADO.NET**.

    ![][03]

2. Dans l’onglet **Gestionnaire de connexion** de l' **Éditeur de code Source ADO.NET**, cliquez sur le bouton **Nouveau** en regard de la liste du **Gestionnaire de connexions ADO.NET** pour ouvrir la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** et créer des paramètres de connexion pour la base de données SQL Server à partir de laquelle ce didacticiel charge les données.

    ![][04]

3. Dans la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** , cliquez sur le bouton **Nouveau** pour ouvrir la boîte de dialogue **Gestionnaire de connexion** et de créer une nouvelle connexion de données.

    ![][05]

4. Dans la boîte de dialogue **Gestionnaire de connexion** , procédez comme suit.

    1. Pour le **fournisseur**, sélectionnez le fournisseur de données SqlClient.
    2. **Nom du serveur**, entrez le nom de SQL Server.
    3. Dans la section **Ouvrir une session sur le serveur** , sélectionnez ou entrez les informations d’authentification.
    4. Dans la section **connexion à une base de données** , sélectionnez la base de données AdventureWorks.
    5. Cliquez sur **tester la connexion**.
    
        ![][06]
    
    6. Dans la boîte de dialogue qui indique les résultats de test de la connexion, cliquez sur **OK** pour revenir à la boîte de dialogue **Connection Manager** .
    7. Dans la boîte de dialogue **Gestionnaire de connexion** , cliquez sur **OK** pour revenir à la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** .
 
5. Dans la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** , cliquez sur **OK** pour revenir à l' **Éditeur de code Source de ADO.NET**.
6. Dans l' **Éditeur de code Source ADO.NET**, dans la liste **nom de la table ou la vue** , sélectionnez la table **Sales.SalesOrderDetail** .

    ![][07]

7. Cliquez sur **Aperçu** pour voir les 200 premières lignes de données dans la table source dans la boîte de dialogue **Aperçu des résultats de requête** .

    ![][08]

8. Dans la boîte de dialogue **Aperçu des résultats de requête** , cliquez sur **Fermer** pour retourner à l' **Éditeur de code Source de ADO.NET**.
9. Dans l' **Éditeur de code Source ADO.NET**, cliquez sur **OK** pour terminer la configuration de la source de données.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Étape 4 : Se connecter à l’adaptateur de source pour l’adaptateur de destination

1. Sélectionnez l’adaptateur de source sur l’aire de conception.
2. Cliquez sur la flèche bleue qui s’étend de l’adaptateur de source et faites-le glisser dans l’éditeur de destination jusqu'à ce qu’il s’enclenche.

    ![][10]

    Dans un package SSIS classique, vous utilisez un certain nombre d’autres composants de la boîte à outils SSIS entre la source et la destination de restructuration, de transformer et de nettoyer vos données lorsqu’elles traversent le flux de données SSIS. Pour que cet exemple reste aussi simple que possible, nous allons reliant la source directement vers la destination.

## <a name="step-5-configure-the-destination-adapter"></a>Étape 5 : Configuration de l’adaptateur de destination

1. Double-cliquez sur l’adaptateur de destination pour ouvrir l' **Éditeur de Destination de ADO.NET**.

    ![][11]

2. Dans l’onglet **Gestionnaire de connexion** de l' **Éditeur de Destination de ADO.NET**, cliquez sur le bouton **Nouveau** en regard de la liste du **Gestionnaire de connexion** pour ouvrir la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** et créer des paramètres de connexion pour la base de données de l’entrepôt de données SQL Azure dans lequel ce didacticiel charge les données.
3. Dans la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** , cliquez sur le bouton **Nouveau** pour ouvrir la boîte de dialogue **Gestionnaire de connexion** et de créer une nouvelle connexion de données.
4. Dans la boîte de dialogue **Gestionnaire de connexion** , procédez comme suit.
    1. Pour le **fournisseur**, sélectionnez le fournisseur de données SqlClient.
    2. **Nom du serveur**, entrez le nom de l’entrepôt de données SQL.
    3. Dans la section **Ouvrir une session sur le serveur** , sélectionnez **authentification d’utiliser SQL Server** et entrez les informations d’authentification.
    4. Dans la section **connexion à une base de données** , sélectionnez une base de données SQL Data Warehouse existante.
    5. Cliquez sur **tester la connexion**.
    6. Dans la boîte de dialogue qui indique les résultats de test de la connexion, cliquez sur **OK** pour revenir à la boîte de dialogue **Connection Manager** .
    7. Dans la boîte de dialogue **Gestionnaire de connexion** , cliquez sur **OK** pour revenir à la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** .
5. Dans la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** , cliquez sur **OK** pour revenir à l' **Éditeur de Destination de ADO.NET**.
6. Dans l' **Éditeur de Destination de ADO.NET**, cliquez sur **Nouveau** en regard de la liste **d’utiliser une table ou une vue** pour ouvrir la boîte de dialogue **Create Table** pour créer une nouvelle table de destination avec une liste de colonnes correspondant à la table source.

    ![][12a]

7. Dans la boîte de dialogue **Créer une Table** , procédez comme suit.

    1. Modifier le nom de la table de destination à **SalesOrderDetail**.
    2. Supprimez la colonne **rowguid** . Le type de données **uniqueidentifier** n’est pas pris en charge dans le magasin de données SQL.
    3. Modifier le type de données de la colonne **LineTotal** dans **money**. Type de données **decimal** n’est pas pris en charge dans le magasin de données SQL. Pour obtenir des informations sur les types de données pris en charge, reportez-vous à [CREATE TABLE (entrepôt de données de SQL Azure, Parallel Data Warehouse)][].
    
        ![][12b]
    
    4. Cliquez sur **OK** pour créer la table et retourner à l' **Éditeur de Destination de ADO.NET**.

8. Dans l' **Éditeur de Destination de ADO.NET**, sélectionnez l’onglet **mappages** pour voir comment les colonnes de la source sont mappés aux colonnes dans la destination.

    ![][13]

9. Cliquez sur **OK** pour terminer la configuration de la source de données.

## <a name="step-6-run-the-package-to-load-the-data"></a>Étape 6 : Exécutez le package pour charger les données

Exécutez le package en cliquant sur le bouton **Démarrer** dans la barre d’outils ou en sélectionnant une des options **d’exécution** dans le menu **Déboguer** .

Lorsque le package commence à s’exécuter, vous voyez les engrenages jaunes pour indiquer l’activité, ainsi que le nombre de lignes traitées jusqu'à présent.

![][14]

Lorsque le package est terminée, vous voyez les coches vertes pour indiquer la réussite ainsi que le nombre total de lignes de données chargées à partir de la source vers la destination.

![][15]

Félicitations ! Vous avez utilisé avec succès de SQL Server Integration Services pour charger des données dans l’entrepôt de données SQL Azure.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le flux de données SSIS. Démarrer ici : [Flux de données][].
- Apprenez à déboguer et à résoudre les problèmes de votre droit de packages dans l’environnement de design. Démarrer ici : [Outils de dépannage pour le développement de packages][].
- Apprenez à déployer vos projets SSIS et les packages Integration Services Server ou vers un autre emplacement de stockage. Démarrer ici : [déploiement de projets et des Packages][].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[Guide des PolyBase]: https://msdn.microsoft.com/library/mt143171.aspx
[Télécharger des outils de données de SQL Server (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CRÉER TABLE (entrepôt de données SQL Azure, entrepôt de données en parallèle)]: https://msdn.microsoft.com/library/mt203953.aspx
[Flux de données]: https://msdn.microsoft.com/library/ms140080.aspx
[Outils de dépannage pour le développement de packages]: https://msdn.microsoft.com/library/ms137625.aspx
[Déploiement de projets et de Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Services d’intégration de Microsoft SQL Server 2016 Feature Pack pour Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[Évaluation de SQL Server]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Communauté de Visual Studio]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[Bases de données AdventureWorks 2014]: https://msftdbprodsamples.codeplex.com/releases/view/125550
