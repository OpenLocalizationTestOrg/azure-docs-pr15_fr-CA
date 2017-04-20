<properties
    pageTitle="Utiliser Hadoop Sqoop dans HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser PowerShell d’Azure à partir d’une station de travail à exécuter Sqoop d’importation et d’exportation entre un cluster Hadoop et une base de données SQL d’Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="run-sqoop-jobs-using-azure-powershell-for-hadoop-in-hdinsight"></a>Exécuter des tâches de Sqoop à l’aide de PowerShell d’Azure pour Hadoop dans HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Azure PowerShell pour exécuter des travaux de Sqoop dans HDInsight pour importer et exporter entre cluster de HDInsight et de la base de données Azure SQL ou de la base de données SQL Server.

> [AZURE.NOTE] Les étapes décrites dans cet article peuvent être utilisés avec un cluster un HDInsight basée sur Linux ou Windows ; Toutefois, ces étapes fonctionnent uniquement à partir d’un client Windows. Pour les autres méthodes de soumission de projet, cliquez sur le sélecteur de tabulations en haut de l’article.


###<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Une station de travail avec PowerShell d’Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Cluster A Hadoop dans HDInsight**. Reportez-vous à la section [créer un cluster et la base de données SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

    
## <a name="run-sqoop-using-powershell"></a>Exécutez Sqoop à l’aide de PowerShell

Le script PowerShell suivant prétraite le fichier source et l’exporte dans une base de données Azure SQL :

    $resourceGroupName = "<AzureResourceGroupName>"
    $hdinsightClusterName = "<HDInsightClusterName>"

    $httpUserName = "admin"
    $httpPassword = "<Password>"

    $defaultStorageAccountName = $hdinsightClusterName + "store"
    $defaultBlobContainerName = $hdinsightClusterName


    $sqlDatabaseServerName = $hdinsightClusterName + "dbserver"
    $sqlDatabaseName = $hdinsightClusterName + "db"
    $sqlDatabaseLogin = "sqluser"
    $sqlDatabasePassword = "<Password>"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion
        
    #region - pre-process the source file
        
    Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green
        
    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"
        
    # Define the connection string
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
    # Create block blob objects referencing the source and destination blob.
    $storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $defaultStorageAccountName
    $storageContainer = ($storageAccount |Get-AzureStorageContainer -Name $defaultBlobContainerName).CloudBlobContainer
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
        
    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)
        
    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
        
    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")
        
        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)
        
            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }
        
        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }
        
    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)
        
    #endregion
        
    #region - export the log file from the cluster to the SQL database
        
    Write-Host "Exporting the log file ..." -ForegroundColor Green

    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
        
    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
        
    $tableName_log4j = "log4jlogs"
    $exportDir_log4j = "/tutorials/usesqoop/data"
    $sqljdbcdriver = "/user/oozie/share/lib/sqoop/sqljdbc41.jar"
        
    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
        -Files $sqljdbcdriver

    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
        
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
    #endregion

##<a name="limitations"></a>Limitations

* Exportation en bloc - des HDInsight de base avec Linux, le connecteur de Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données de SQL Azure ne prend pas en charge insertions en bloc.

* Le traitement par lots - avec HDInsight basé sur Linux, lorsque vous utilisez la `-batch` passer lors de l’exécution des insertions, Sqoop effectue plusieurs insertions plutôt que le traitement par lot des opérations d’insertion.

##<a name="next-steps"></a>Étapes suivantes

Maintenant, vous avez appris comment utiliser Sqoop. Pour plus d’informations, voir :

- [Utilisation des Oozie avec HDInsight](hdinsight-use-oozie.md): action de Sqoop d’utilisation dans un flux de travail Oozie.
- [Analyse des données de retard vol à l’aide de HDInsight](hdinsight-analyze-flight-delay-data.md): utilisez la ruche pour analyser le vol retarder les données et utilisez Sqoop pour exporter des données vers une base de données SQL d’Azure.
- [Télécharger des données à HDInsight](hdinsight-upload-data.md): rechercher d’autres méthodes de chargement des données de stockage des objets Blob HDInsight/Azure.


[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
