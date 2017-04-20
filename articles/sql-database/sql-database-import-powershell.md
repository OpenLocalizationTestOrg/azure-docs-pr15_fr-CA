<properties
    pageTitle="Importer un fichier de type sac à DOS pour créer une base de données Azure SQL à l’aide de PowerShell | Microsoft Azure"
    description="Importer un fichier de type sac à DOS pour créer une base de données Azure SQL à l’aide de PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/31/2016"
    ms.author="sstein"/>

# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importer un fichier de type sac à DOS pour créer une base de données Azure SQL à l’aide de PowerShell

**Base de données unique**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Cet article fournit des instructions sur la création d’une base de données Azure SQL en important un fichier de [type sac à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) avec PowerShell.

La base de données est créée à partir d’un fichier de type sac à DOS (.bacpac) importé à partir d’un conteneur d’objet blob Azure Storage. Si vous n’avez pas un fichier de type sac à DOS dans le stockage Azure, consultez [Archiver une base de données Azure SQL dans un fichier de type sac à DOS à l’aide de PowerShell](sql-database-export-powershell.md). Si vous avez déjà un fichier de type sac à DOS qui n’est pas dans le stockage Azure, [Utilisez AzCopy pour télécharger facilement à votre compte de stockage Azure](../storage/storage-use-azcopy.md#blob-upload).

> [AZURE.NOTE] Une base de données SQL Azure crée et gère les sauvegardes pour chaque base de données utilisateur que vous pouvez restaurer automatiquement. Pour plus d’informations, reportez-vous à la section [de la base de données SQL des sauvegardes automatisées](sql-database-automated-backups.md).


Pour importer une base de données SQL, vous devez les éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Version d’évaluation gratuite** en haut de cette page, puis revenir à la fin de cet article.
- Un fichier de type sac à dos de la base de données que vous souhaitez importer. Le type sac à DOS doit se trouver dans un conteneur de blob de [compte de stockage Azure](../storage/storage-create-storage-account.md) .



[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="set-up-the-variables-for-your-environment"></a>Définir les variables de votre environnement

Il existe quelques variables où vous devez remplacer les valeurs de l’exemple avec les valeurs spécifiques de votre base de données et de votre compte de stockage.

Le nom du serveur doit être un serveur qui existe dans l’abonnement sélectionné à l’étape précédente. Il doit être le serveur de la base de données à créer dans. Importation d’une base de données directement dans un pool élastique n’est pas pris en charge. Mais vous pouvez tout d’abord importer dans une base de données unique et déplacez la base de données vers un pool.

Le nom de la base de données est le nom souhaité pour la nouvelle base de données.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Les variables suivantes sont à partir du compte de stockage dans lequel se trouve votre type sac à DOS. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage pour obtenir ces valeurs. Vous pouvez trouver la clé d’accès principal en cliquant sur **tous les paramètres** et les **clés** du serveur lame de votre compte de stockage.

Le nom de l’objet blob est le nom d’un fichier de type sac à DOS existant que vous souhaitez créer la base de données. Vous devez inclure l’extension .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Exécution de le [Get-Credential] (https://msdn.microsoft.com/library/azure/hh849815(v=azure.300\).aspx) d’applet de commande ouvre une fenêtre vous demandant votre nom d’utilisateur et mot de passe. Permet d’entrer l’ouverture de session admin et le mot de passe pour le serveur de base de données SQL ($ServerName ci-dessus) et non le nom d’utilisateur et le mot de passe pour votre compte Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importer la base de données

Cette commande envoie une demande au service de base de données d’importation. Selon la taille de votre base de données, l’opération d’importation peut prendre un certain temps pour terminer.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Surveiller la progression de l’opération

Après l’exécution de [New-AzureRmSqlDatabaseImport] (https://msdn.microsoft.com/library/azure/mt707793(v=azure.300\).aspx), vous pouvez vérifier le statut de la demande en exécutant le [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Script d’importation de PowerShell de base de données SQL


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus pour se connecter à et d’interroger une base de données SQL importées, consultez [se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
