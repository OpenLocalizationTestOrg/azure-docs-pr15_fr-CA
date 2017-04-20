<properties 
   pageTitle="Mise en route d’Analytique de LAC de données Azure à l’aide de PowerShell d’Azure | Azure" 
   description="Découvrez comment utiliser PowerShell Azure pour créer un compte de banque de données lac, créer un travail de données lac Analytique à l’aide de SQL-U et soumettre la tâche. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Didacticiel : mise en route d’Analytique de LAC de données Azure à l’aide de PowerShell d’Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment PowerShell Azure permet de créer des comptes d’Azure données lac Analytique, définir les tâches de données lac Analytique dans [SQL-U](data-lake-analytics-u-sql-get-started.md)et soumettre des travaux aux comptes lac de données analytiques. Pour plus d’informations sur les données lac Analytique, consultez [vue d’ensemble de l’Analytique de LAC de données Azure](data-lake-analytics-overview.md).

Dans ce didacticiel, vous allez développer un travail qui lit un onglet séparé les fichier TSV (valeurs) et le convertit en un fichier séparés par des virgules (CSV). Pour parcourir le didacticiel même à l’aide d’autres outils pris en charge, cliquez sur les onglets en haut de cette section.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Une station de travail avec PowerShell d’Azure**. Voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).
    
##<a name="create-data-lake-analytics-account"></a>Créer le compte de données lac Analytique

Vous devez avoir un compte de données lac Analytique avant de pouvoir exécuter toutes les tâches. Pour créer un compte Analytique lac de données, vous devez spécifier les éléments suivants :

- **Groupe de ressources Azure**: compte de A données lac Analytique doit être créé au sein d’un groupe de ressources d’Azure. [Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md) vous permet de travailler avec les ressources de votre application en tant que groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre application dans une opération unique et coordonnée.  

    Pour énumérer les groupes de ressources dans votre abonnement :
    
        Get-AzureRmResourceGroup
    
    Pour créer un nouveau groupe de ressources :

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"

- **Nom du compte Analytique données lac**
- **Emplacement**: parmi les centres de données Azure qui prend en charge les données lac Analytique.
- **Compte du lac de données par défaut**: chaque compte Analytique lac de données possède un compte de LAC de données par défaut.

    Pour créer un nouveau compte du lac de données :

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

    > [AZURE.NOTE] Le nom de compte du lac de données doit uniquement contenir des minuscules et chiffres.



**Pour créer un compte Analytique lac de données**

1. Ouvrez PowerShell ISE à partir de votre station de travail Windows.
2. Exécutez le script suivant :

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
        
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
        
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
        
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
        
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

##<a name="upload-data-to-data-lake"></a>Télécharger les données vers données lac

Dans ce didacticiel, vous traiterez des journaux de recherche.  Le journal de recherche peut être stocké dans le magasin de données lac ou de stockage des objets Blob Azure. 

Un exemple de fichier de journal de recherche a été copié dans un conteneur d’Azure Blob public. Le script PowerShell suivant permet de télécharger le fichier sur votre poste de travail et ensuite télécharger le fichier pour le compte de la banque de données lac par défaut de votre compte de données lac Analytique.

    $dataLakeStoreName = "<The default Data Lake Store account name>"
    
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

Le script PowerShell suivant vous indique comment obtenir le nom de la banque de données lac par défaut pour un compte Analytique lac de données :


    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

>[AZURE.NOTE] Le portail Azure fournit une interface utilisateur pour copier les exemples de fichiers de données pour le compte de la banque de données lac par défaut. Pour obtenir des instructions, reportez-vous à la section [Mise en route d’Analytique de LAC de données Azure à l’aide du portail Azure](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Stockage des objets Blob Azure peut également accéder à données lac Analytique.  Pour le transfert de données pour le stockage des objets Blob Azure, reportez-vous [à l’aide de PowerShell Azure avec stockage Azure](../storage/storage-powershell-guide-full.md).

##<a name="submit-data-lake-analytics-jobs"></a>Soumettre les tâches de données lac Analytique

Les tâches de données lac Analytique sont écrites en langage SQL de l’U. Pour en savoir plus sur U-SQL, consultez [mise en route de langage d’U-SQL](data-lake-analytics-u-sql-get-started.md) et [référence du langage SQL-U](http://go.microsoft.com/fwlink/?LinkId=691348).

**Pour créer un script de travail données lac Analytique**

- Créez un fichier texte avec le script de U-SQL suivant et enregistrez le fichier texte à votre station de travail :

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Ce script SQL-U lit le fichier de données source à l’aide de **Extractors.Tsv()**et crée un fichier csv à l’aide de **Outputters.Csv()**. 
    
    Ne modifiez pas les deux chemins d’accès, sauf si vous copiez le fichier source dans un autre emplacement.  Données lac Analytique va créer le dossier de sortie si elle n’existe pas.
    
    Il est plus simple d’utiliser les chemins d’accès relatifs pour les fichiers stockés par défaut des données des comptes de LAC. Vous pouvez également utiliser des chemins d’accès absolus.  Par exemple 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés.  La syntaxe pour les fichiers stockés dans le compte de stockage Azure lié est :
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Conteneur de Blob Azure avec BLOB public ou d’autorisations d’accès de conteneurs publics ne sont pas actuellement pris en charge.    
    
    
**Pour soumettre la tâche**

1. Ouvrez PowerShell ISE à partir de votre station de travail Windows.
2. Exécutez le script suivant :

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
        
        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    Dans le script, le fichier de script SQL-U est stocké dans c:\tutorials\data-lake-analytics\copyFile.usql. Mettre à jour le chemin d’accès du fichier vous en conséquence.
 
Une fois la tâche terminée, vous pouvez utiliser les applets de commande suivantes pour le fichier de la liste et télécharger le fichier :
    
    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
    
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
    
    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
    
    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Voir aussi

- Pour consulter le didacticiel même à l’aide d’autres outils, cliquez sur les sélecteurs de l’onglet en haut de la page.
- Pour voir une requête plus complexe, consultez [les journaux de site Web d’analyse à l’aide d’Azure données lac Analytique](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications de U-SQL, consultez [scripts de développer U-SQL à l’aide des outils de LAC de données pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour en savoir plus U-SQL, consultez [mise en route du lac de données Azure Analytique U-SQL language](data-lake-analytics-u-sql-get-started.md).
- Pour les tâches de gestion, consultez [Gérer des Analytique des lac Azure données avec Azure Portal](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de données lac Analytique, consultez [vue d’ensemble de l’Analytique de LAC de données Azure](data-lake-analytics-overview.md).

