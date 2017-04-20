<properties 
    pageTitle="Déplacer les données vers une base de données de SQL Azure pour l’apprentissage de Machine Azure | Azure" 
    description="Créer une Table de SQL et de charger les données à la Table de SQL" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016"
    ms.author="bradsev" /> 

# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Déplacer les données vers une base de données de SQL Azure pour l’apprentissage de Machine Azure

Cette rubrique décrit les options pour transférer les données à partir de fichiers plats (formats CSV ou TSV) ou à partir des données stockées dans une de SQL Server sur site à une base de données SQL d’Azure. Ces tâches de déplacement des données vers le nuage font partie du processus Team données scientifiques.

Une rubrique qui décrit les options de déplacement des données vers un de SQL Server sur site pour l’apprentissage de l’ordinateur, voir [déplacer des données vers SQL Server sur une machine virtuelle Azure](machine-learning-data-science-move-sql-server-virtual-machine.md).

Les liens de **menu** suivants vers les rubriques qui décrivent comment faire pour ajouter des données dans les environnements où les données peuvent être stockées et traitées pendant le processus de Science de données (TDSP) équipe de cible.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Le tableau suivant récapitule les options de déplacement des données vers une base de données de SQL Azure.

<b>SOURCE</b> |<b>DESTINATION : Base de données SQL Azure</b> |
-------------- |--------------------------------|
<b>Fichiers plats (CSV ou TSV mis en forme)</b> |<a href="#bulk-insert-sql-query">Requête SQL d’insertion en bloc |
<b>SQL Server sur site</b> | 1. <a href="#export-flat-file">exporter vers un fichier plat<br> 2. <a href="#insert-tables-bcp">Assistant de Migration de base de données SQL<br> 3. <a href="#db-migration">de la base de données back up et de restauration<br> 4. <a href="#adf">Factory de données azure |


## <a name="prereqs"></a>Conditions préalables
Les procédures décrites ici requièrent que vous ayez :

* Un **abonnement Azure**. Si vous ne disposez pas d’un abonnement, vous pouvez vous inscrire pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Un **compte de stockage Azure**. Vous utilisez un compte de stockage Azure pour stocker les données dans ce didacticiel. Si vous n’avez pas un compte de stockage Azure, voir l’article [créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) . Après avoir créé le compte de stockage, vous devez obtenir la clé du compte utilisée pour accéder au stockage. Reportez-vous à la section [Affichage, de copie et de touches d’accès de stockage régénérer](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Accès à une **base de données SQL Azure**. Si vous devez configurer une base de données de SQL Azure, [Mise en route de la base de données SQL de Microsoft Azure](../sql-database/sql-database-get-started.md) fournit des informations sur la façon de configurer une nouvelle instance d’une base de données SQL Azure.
* Installation et configuration **Azure PowerShell** localement. Pour obtenir des instructions, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

**Données**: les processus de migration sont illustrées à l’aide de [NYC Taxi dataset](http://chriswhong.com/open-data/foil_nyc_taxi/). Le groupe de données NYC Taxi contient des informations sur les données de voyage et de foires et est disponible, comme noté ce poste, sur le stockage des objets blob Azure : [Données de Taxi NYC](http://www.andresmh.com/nyctaxitrips/). Un exemple et une description de ces fichiers sont fournis dans la [Description de Dataset NYC Taxi voyages](machine-learning-data-science-process-sql-walkthrough.md#dataset).
 
Vous pouvez adapter les procédures décrites ici à un ensemble de vos propres données ou suivez les étapes décrites à l’aide du groupe de données NYC Taxi. Pour charger le dataset NYC Taxi dans votre base de données de SQL Server sur site, suivez la procédure décrite dans les [Données d’importation en bloc dans la base de données de SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Ces instructions sont destinées à un SQL Server sur une Machine virtuelle de Azure, mais la procédure de téléchargement vers le SQL Server sur site est le même.


## <a name="file-to-azure-sql-database"></a>Déplacement des données à une base de données Azure SQL à partir d’une source de fichier plat

Données dans les fichiers plats (CSV ou TSV formaté) peuvent être déplacées vers une base de données Azure SQL à l’aide d’une requête SQL d’insertion en bloc.

### <a name="bulk-insert-sql-query"></a>Requête SQL d’insertion en bloc

Les étapes de la procédure à l’aide de la requête SQL d’insertion en bloc sont similaires à celles décrites dans les sections de déplacer des données d’une source de fichier plat pour SQL Server sur un ordinateur virtuel d’Azure. Pour plus d’informations, reportez-vous à la section [Requête de SQL Bulk Insert](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).


##<a name="sql-on-prem-to-sazure-sql-database"></a>Déplacement des données à partir de SQL Server sur site à une base de données SQL d’Azure

Si la source de données est stocké dans une de SQL Server sur site, il existe différentes possibilités pour le déplacement des données à une base de données Azure SQL :

1. [Exporter vers un fichier plat](#export-flat-file) 
2. [Assistant de Migration de base de données SQL](#insert-tables-bcp)
3. [De la base de données back up et de restauration](#db-migration)
4. [Usine de données Azure](#adf)

Pour les trois premières étapes sont semblables à ces sections dans le [déplacement des données vers SQL Server sur une machine virtuelle Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) qui couvrent les mêmes procédures. Des liens vers les sections appropriées dans cette rubrique sont fournies dans les instructions suivantes.

###<a name="export-flat-file"></a>Exporter vers un fichier plat

Les étapes de cette exportation vers un fichier plat sont similaires à celles visées dans les [Exporter vers un fichier plat](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file).

###<a name="insert-tables-bcp"></a>Assistant de Migration de base de données SQL

Les étapes d’utilisation de l’Assistant de Migration de base de données SQL sont semblables à celles décrites dans [l’Assistant de Migration de base de données SQL](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration).

###<a name="db-migration"></a>De la base de données back up et de restauration

Les étapes à l’aide de la base de données de sauvegarde et de restauration sont similaires à celles visées dans la [base de données de sauvegarde et de restauration](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup).

###<a name="adf"></a>Usine de données Azure

La procédure de déplacement des données vers une base de données Azure SQL avec le Factory de données Azure (CAD) est fournie dans la rubrique de [déplacer des données à partir d’un serveur SQL sur site de SQL Azure avec Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md). Cette rubrique indique comment déplacer des données d’une base de données de SQL Server sur site à une base de données Azure SQL via le stockage Blob Azure à l’aide du chargeur automatique de documents. 

Envisagez d’utiliser le chargeur automatique de documents lorsque les données doivent être migrées en permanence dans un scénario hybride qui accède à des ressources sur site et de nuage, et lorsque les données sont traitées ou doivent être modifié ou disposer d’une logique métier pour l’ajouter en cours de migration. Chargeur automatique de documents permet la planification et le suivi des tâches à l’aide de simples scripts JSON qui gèrent le transfert des données sur une base périodique. Chargeur automatique de documents dispose également d’autres fonctionnalités telles que la prise en charge pour les opérations complexes.




