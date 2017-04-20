<properties
   pageTitle="Permet de charger des données dans Data Warehouse de SQL Studio de plate-forme de données de Redgate | Microsoft Azure"
   description="Apprenez à utiliser un Studio de plate-forme de données de Redgate pour les scénarios de gestion de magasins de données."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>


# <a name="load-data-with-redgate-data-platform-studio"></a>Charger les données avec Studio de plate-forme de données Redgate

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)
- [Fabrique de données](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

Ce didacticiel vous montre comment utiliser des [Studio de plate-forme de données de Redgate](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) pour déplacer des données à partir d’une de SQL Server sur site à l’entrepôt de données SQL Azure. Studio de plate-forme de données applique les correctifs de compatibilité et les optimisations les plus appropriées afin qu’il soit le plus rapide pour la mise en route de SQL Data Warehouse.

> [AZURE.NOTE] [Redgate](http://www.red-gate.com) est un partenaire Microsoft depuis longtemps qui propose différents outils de SQL Server. Cette fonctionnalité dans un Studio de plate-forme de données soit disponible librement pour une utilisation commerciale et non commerciale.

## <a name="before-you-begin"></a>Avant de commencer
### <a name="create-or-identify-resources"></a>Créez ou identifiez des ressources

Avant de commencer ce didacticiel, vous devez disposer :

- **De base de données de SQL Server sur site**: les données que vous souhaitez importer dans le Data Warehouse de SQL doivent provenir d’une de SQL Server sur site (2008 R2 de version ou une version ultérieure). Studio de plate-forme de données ne peut pas importer des données directement à partir d’une base de données de SQL Azure, ou à partir de fichiers texte.
- **Compte de stockage Azure**: Studio de plate-forme de données transfère les données dans le stockage Blob Azure avant de le charger dans SQL Data Warehouse. Le compte de stockage doit utiliser le modèle de déploiement « Gestionnaire de ressources » (par défaut) au lieu du modèle de déploiement « Classique ». Si vous n’avez pas un compte de stockage, vous apprenez à créer un compte de stockage. 
- **SQL Data Warehouse**: ce didacticiel déplace les données à partir de SQL Server sur site vers SQL Data Warehouse, vous devez avoir un entrepôt de données en ligne. Si vous ne disposez pas déjà d’un data warehouse, vous apprenez à créer un entrepôt de données SQL Azure.

> [AZURE.NOTE] Les performances sont améliorées si le compte de stockage et de l’entrepôt de données sont créées dans la même région.

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Étape 1 : Se connecter à un Studio de plate-forme de données avec votre compte Azure
Ouvrez votre navigateur web et accédez au site Web de [Studio de plate-forme de données](https://www.dataplatformstudio.com/) . Connectez-vous en utilisant le même compte Azure que vous avez utilisé pour créer l’entrepôt de données et de compte de stockage. Si votre adresse de courriel n’est associée à un travail ou le compte de l’établissement et un compte Microsoft, veillez à choisir le compte qui a accès à vos ressources.

> [AZURE.NOTE] Si c’est la première fois à l’aide de Studio de plate-forme de données, vous êtes invité à autoriser l’application à gérer vos ressources Azure.

## <a name="step-2-start-the-import-wizard"></a>Étape 2 : Démarrer l’Assistant Importation
Dans l’écran principal STD, sélectionnez l’importation au lien de l’entrepôt de données SQL Azure pour démarrer l’Assistant importation.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Étape 3 : Installer la passerelle de Studio de plate-forme de données
Pour vous connecter à votre base de données de SQL Server sur site, vous devez installer la passerelle STD. La passerelle est un agent de client qui fournit l’accès à votre environnement sur site, extrait les données et le télécharge sur votre compte de stockage. Vos données ne passent jamais via les serveurs de Redgate. Pour installer la passerelle :

1.  Cliquez sur le lien **Créer une passerelle**
2. Téléchargez et installez la passerelle en utilisant le programme d’installation fourni

![][2]

> [AZURE.NOTE] La passerelle peut être installée sur n’importe quel ordinateur disposant d’un accès réseau à la base de données de SQL Server source. Il accède à la base de données SQL Server à l’aide de l’authentification Windows avec les informations d’identification de l’utilisateur actuel.

Une fois installé, l’état de la passerelle devient connecté et vous pouvez sélectionner suivant.

## <a name="step-4-identify-the-source-database"></a>Etape 4 : Identifier la base de données source
Dans la zone de texte *Entrez le nom du serveur* , entrez le nom du serveur qui héberge votre base de données, puis cliquez sur **suivant**. Puis, dans le menu déroulant, sélectionnez la base de données que vous souhaitez importer les données.

![][3]

DPS inspecte la base de données sélectionnée pour les tables à importer. Par défaut, DPS importe toutes les tables dans la base de données. Vous pouvez sélectionner ou désélectionner les tables en développant le lien de toutes les Tables. Sélectionnez le bouton suivant pour aller de l’avant.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Étape 5 : Choisissez un compte de stockage pour reclasser les données
STD vous demande d’indiquer un emplacement pour les données. Choisissez un compte de stockage existant à partir de votre abonnement, puis cliquez sur **suivant**.

> [AZURE.NOTE] DPS crée un nouveau conteneur de l’objet blob dans le compte de stockage choisi et utiliser un dossier distinct pour chaque importation.

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Étape 6 : Sélectionnez un entrepôt de données
Ensuite, vous sélectionnez une base de données de [Magasin de données SQL Azure](http://aka.ms/sqldw) en ligne pour importer les données. Une fois que vous avez sélectionné votre base de données, vous devez entrer les informations d’identification pour se connecter à la base de données, puis cliquez sur **suivant**.

![][5]

> [AZURE.NOTE] DPS fusionne les tables de source de données dans le data warehouse. STD vous avertit si le nom de la table requiert qu’il remplace les tables existantes dans le data warehouse. Vous pouvez supprimer tous les objets existants dans l’entrepôt de données en cochant supprimer tous les objets existants avant l’importation.

## <a name="step-7-import-the-data"></a>Étape 7 : Importation des données
DPS confirme que vous souhaitez importer les données. Cliquez simplement sur le bouton Importer de début pour commencer l’importation des données.

![][6]

DPS affiche une visualisation qui indique la progression de l’extraction et le chargement des données à partir du SQL Server sur site et la progression de l’importation dans Data Warehouse de SQL.

![][7]

Une fois l’importation terminée, DPS affiche un résumé de l’importation de données et un rapport des modifications des correctifs de compatibilité qui ont été effectuées.

![][8]

## <a name="next-steps"></a>Étapes suivantes
Pour Explorer les données dans l’entrepôt de données de SQL, commencez par l’affichage :

- [Requête SQL Azure Data Warehouse (Visual Studio)][]
- [Visualiser les données avec alimentation BI][]

Pour en savoir plus sur données plate-forme Studio de Redgate :

- [Visitez la page d’accueil DPS](http://www.dataplatformstudio.com/)
- [Regardez une démonstration du STD sur Channel 9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Pour une vue d’ensemble d’autres moyens de migrer et de charger vos données SQL Data Warehouse, consultez :

- [Migration de votre solution d’entrepôt de données de SQL][]
- [Charger des données dans l’entrepôt de données SQL Azure](./sql-data-warehouse-overview-load.md)

Pour plus de conseils de développement, consultez la [vue d’ensemble du développement SQL Data Warehouse](./sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Requête SQL Azure Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualiser les données avec alimentation BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migration de votre solution d’entrepôt de données de SQL]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md