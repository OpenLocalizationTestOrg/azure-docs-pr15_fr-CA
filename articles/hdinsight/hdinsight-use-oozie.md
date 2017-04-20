<properties
    pageTitle="Utiliser Hadoop Oozie dans HDInsight | Microsoft Azure"
    description="Utilisez Hadoop Oozie dans HDInsight, un service de données. Découvrez comment définir un flux de travail Oozie et soumettent un travail Oozie."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jgao"/>


# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-in-hdinsight"></a>Utiliser Oozie avec Hadoop pour définir et exécuter un flux de travail dans HDInsight

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Apprenez à Apache Oozie permet de définir un flux de travail et exécuter le flux de travail sur HDInsight. Pour en savoir plus sur le coordinateur de Oozie, consultez [Utiliser en temps un coordinateur Hadoop Oozie avec HDInsight][hdinsight-oozie-coordinator-time]. Pour en savoir plus de la fabrique de données Azure, voir [porc d’utilisation et de la ruche avec Data Factory][azure-data-factory-pig-hive].

Apache Oozie est un système de flux de travail/coordination qui gère les travaux d’Hadoop. Il est intégré à la pile d’Hadoop, et il prend en charge les travaux Hadoop pour Apache MapReduce, Apache porc, la ruche Apache et Apache Sqoop. Il peut également être utilisé pour planifier des tâches qui sont spécifiques à un système, comme les programmes Java ou des scripts de shell.

Le flux de travail que vous implémenterez en suivant les instructions de ce didacticiel contient deux actions :

![Diagramme de flux de travail][img-workflow-diagram]

1. Une action de la ruche s’exécute un script HiveQL pour compter les occurrences de chaque type au niveau du journal dans un fichier log4j. Chaque fichier log4j se compose d’une ligne de champs qui contient un champ [niveau de journal] indiquant le type et la gravité, par exemple :

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    La sortie du script ruche est semblable à :

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Pour plus d’informations sur la ruche, voir [Utilisation de la ruche avec HDInsight][hdinsight-use-hive].

2.  Une action Sqoop exporte la sortie de HiveQL à une table dans une base de données SQL d’Azure. Pour plus d’informations sur Sqoop, reportez-vous à la section [Sqoop d’Hadoop utilisation avec HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Pour les versions Oozie prises en charge sur les clusters de HDInsight, consultez [Nouveautés dans les versions de cluster Hadoop fournies par HDInsight ?] [hdinsight-versions].

###<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Une station de travail avec PowerShell d’Azure**. 

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
    
    Pour exécuter les scripts Windows PowerShell, vous devez exécuter en tant qu’administrateur et la valeur de la stratégie d’exécution *RemoteSigned*. Pour plus d’informations, consultez [les scripts de Windows PowerShell exécuter][powershell-script].

##<a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Définir les flux de travail Oozie et le script de HiveQL connexe

Définitions de workflows Oozie sont écrits en hPDL (un XML Process Definition Language). Le nom de fichier de flux de travail par défaut est *workflow.xml*. Voici le fichier de flux de travail que vous utiliserez dans ce didacticiel.

    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>

Il existe deux actions définies dans le workflow. L’action de début est *RunHiveScript*. Si l’action s’exécute avec succès, l’action suivante est *RunSqoopExport*.

Le RunHiveScript a plusieurs variables. Vous allez passer les valeurs lors de l’envoi du travail de Oozie à partir de votre station de travail à l’aide de PowerShell d’Azure.

<table border = "1">
<tr><th>Variables de flux de travail</th><th>Description</th></tr>
<tr><td>${jobTracker}</td><td>Spécifie l’URL de la mise hors tension du travail Hadoop. Utiliser <strong>jobtrackerhost:9010</strong> dans HDInsight version 3.0 et 2.1.</td></tr>
<tr><td>${nameNode}</td><td>Spécifie l’URL du nœud nom Hadoop. Utiliser l’adresse par défaut du fichier système, par exemple, <i>wasbs : / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
<tr><td>${Nom}</td><td>Spécifie le nom de la file d’attente qui est soumise à la tâche. Utilisez la <strong>valeur par défaut</strong>.</td></tr>
</table>

<table border = "1">
<tr><th>Variable d’action de la ruche</th><th>Description</th></tr>
<tr><td>${hiveDataFolder}</td><td>Spécifie le répertoire source de la commande de la ruche Create Table.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Spécifie le dossier de sortie de l’instruction INSERT remplacer.</td></tr>
<tr><td>${hiveTableName}</td><td>Spécifie le nom de la table de la ruche qui fait référence aux fichiers de données log4j.</td></tr>
</table>

<table border = "1">
<tr><th>Variable d’action Sqoop</th><th>Description</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Spécifie la chaîne de connexion de base de données SQL d’Azure.</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>Spécifie la table de base de données Azure SQL où les données seront exportées à.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Spécifie le dossier de sortie de l’instruction de la ruche insérer remplacer. C’est le même dossier pour l’exportation de Sqoop (exportation-dir).</td></tr>
</table>

Pour plus d’informations sur les flux de travail Oozie et l’utilisation des actions de flux de travail, consultez la [documentation Apache Oozie 4.0] [ apache-oozie-400] (pour HDInsight version 3.0) ou la [documentation d’Apache Oozie 3.3.2] [ apache-oozie-332] (pour HDInsight version 2.1).


L’action de la ruche dans le flux de travail appelle un fichier de script HiveQL. Ce fichier de script contient trois instructions de HiveQL :

    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **Instruction du DROP TABLE** supprime la table de ruche de log4j si elle existe.
2. **Instruction de création de la TABLE** crée une table externe de ruche log4j qui pointe vers l’emplacement du fichier journal log4j. Le délimiteur de champ est «, ». Le séparateur de ligne par défaut est « \n ». Une table externe ruche est utilisée pour éviter le fichier de données en cours de suppression à partir de l’emplacement d’origine si vous souhaitez exécuter le flux de travail Oozie plusieurs fois.
3. **Instruction de l’insérer remplacer** compte les occurrences de chaque type de journal au niveau de la table de la ruche log4j et enregistre le résultat dans un objet blob dans le stockage Azure.


Il existe trois variables utilisées dans le script :

- ${hiveTableName}
- ${hiveDataFolder}
- ${hiveOutputFolder}

Le fichier de définition de flux de travail (workflow.xml dans ce didacticiel) transmet ces valeurs à ce script HiveQL au moment de l’exécution.

Le fichier de flux de travail et le fichier HiveQL sont stockés dans un conteneur d’objet blob.  Le script PowerShell que vous utiliserez plus loin dans ce didacticiel copie les deux fichiers dans le compte de stockage par défaut. 

##<a name="submit-oozie-jobs-using-powershell"></a>Soumettre des travaux d’Oozie à l’aide de PowerShell

Azure PowerShell ne fournit actuellement des applets de commande pour définir des tâches de Oozie. Vous pouvez utiliser l’applet de commande **Invoke-RestMethod** pour appeler des services web de Oozie. L’API de services web Oozie est une API de JSON HTTP REST. Pour plus d’informations sur l’API de services web Oozie, consultez la [documentation Apache Oozie 4.0] [ apache-oozie-400] (pour HDInsight version 3.0) ou la [documentation d’Apache Oozie 3.3.2] [ apache-oozie-332] (pour HDInsight version 2.1).

Le script PowerShell dans cette section effectue les étapes suivantes :

1. Se connecter à Azure.
2. Créer un groupe de ressources Azure. Pour plus d’informations, voir [Utiliser PowerShell de Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).
3. Créer un serveur de base de données de SQL Azure, une base de données SQL d’Azure et deux tables. Ils sont utilisés par l’action Sqoop dans le flux de travail.

    Le nom de table est *log4jLogCount*.

4. Créer un cluster de HDInsight utilisé pour exécuter des tâches de Oozie.

    Pour examiner le cluster, vous pouvez utiliser le portail Azure ou Azure PowerShell.

5. Copiez le fichier de flux de travail oozie et le fichier de script HiveQL pour le système de fichiers par défaut.

    Les deux fichiers sont stockés dans un conteneur d’objet Blob public.
    
    - Copiez le script HiveQL (useoozie.hql) dans le stockage Azure (wasbs:///tutorials/useoozie/useoozie.hql).
    - Copiez workflow.xml vers wasbs:///tutorials/useoozie/workflow.xml.
    - Copiez le fichier de données (/ example/data/sample.log) à wasbs:///tutorials/useoozie/data/sample.log.
     
6. Soumettre une tâche Oozie.

    Pour examiner les résultats des travaux OOzie, utilisez Visual Studio ou autres outils pour vous connecter à la base de données de SQL Azure.

Voici le script.  Vous pouvez exécuter le script à partir de Windows PowerShell ISE. Vous devez uniquement configurer les 7 premiers variables.

    #region - provide the following values
    
    $subscriptionID = "<Enter your Azure subscription ID>"
    
    # SQL Database server login credentials used for creating and connecting
    $sqlDatabaseLogin = "<Enter SQL Database Login Name>"
    $sqlDatabasePassword = "<Enter SQL Database Login Password>"
    
    # HDInsight cluster HTTP user credential used for creating and connectin
    $httpUserName = "admin"  # The default name is "admin"
    $httpPassword = "<Enter HDInsight Cluster HTTP User Password>"
    
    # Used for creating Azure service names
    $nameToken = "<Enter an Alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion
    
    #region - variables
    
    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial
    
    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10
    
    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"
    
    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion
    
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"
    
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{
        Login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
    }
    #endregion
    
    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion
    
    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
    
        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
    
        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $sqlLoginCredentials `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
    
        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress
    
        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    #endregion
    
    #region - Create and validate Azure SQL database
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    
    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    #endregion
    
    #region - Create SQL database tables
    Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green
    
    $sqlDatabaseTableName = "log4jLogsCount"
    $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
            [Level] [nvarchar](10) NOT NULL,
            [Total] float,
        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
        (
        [Level] ASC
        )
        )"
    
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()
    
    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jCountTable
    $cmd.ExecuteNonQuery()
    
    $conn.close()
    #endregion
    
    #region - Create HDInsight cluster
    
    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
    
    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    
    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultBlobContainerName 
    
    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion
    
    #region - copy Oozie workflow and HiveQL files
    
    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
    
    # Both files are stored in a public Blob
    $publicBlobContext = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
    
    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    
    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "useooziewf.hql"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/useooziewf.hql" `
        -Force
    
    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "workflow.xml"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/workflow.xml" `
        -Force
    
    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/workflow.xml
    
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/useooziewf.hql
    
    #endregion
    
    #region - copy the sample.log file
    
    Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
    
    Start-CopyAzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -SrcContainer $defaultBlobContainerName `
        -SrcBlob "example/data/sample.log"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -destBlob "$destFolder/data/sample.log" 
    
    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/data/sample.log
    
    #endregion
    
    #region - submit Oozie job
    
    $storageUri="wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"
    
    $oozieJobName = $namePrefix + "OozieJob"
    
    #Oozie WF variables
    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10
    
    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
    
    #Sqoop action variables
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    
    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
    
    <property>
        <name>nameNode</name>
        <value>$storageUrI</value>
    </property>
    
    <property>
        <name>jobTracker</name>
        <value>jobtrackerhost:9010</value>
    </property>
    
    <property>
        <name>queueName</name>
        <value>default</value>
    </property>
    
    <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
    </property>
    
    <property>
        <name>hiveScript</name>
        <value>$hiveScript</value>
    </property>
    
    <property>
        <name>hiveTableName</name>
        <value>$hiveTableName</value>
    </property>
    
    <property>
        <name>hiveDataFolder</name>
        <value>$hiveDataFolder</value>
    </property>
    
    <property>
        <name>hiveOutputFolder</name>
        <value>$hiveOutputFolder</value>
    </property>
    
    <property>
        <name>sqlDatabaseConnectionString</name>
        <value>&quot;$sqlDatabaseConnectionString&quot;</value>
    </property>
    
    <property>
        <name>sqlDatabaseTableName</name>
        <value>$SQLDatabaseTableName</value>
    </property>
    
    <property>
        <name>user.name</name>
        <value>$httpUserName</value>
    </property>
    
    <property>
        <name>oozie.wf.application.path</name>
        <value>$oozieWFPath</value>
    </property>
    
    </configuration>
    "@
    
    Write-Host "Checking Oozie server status..." -ForegroundColor Green
    $clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus
    
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieServerSatus = $jsonResponse[0].("systemMode")
    Write-Host "Oozie server status is $oozieServerSatus."
    
    # create Oozie job
    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
    Write-Host "`n--------`n$OoziePayload`n--------"
    $clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug
    
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieJobId = $jsonResponse[0].("id")
    Write-Host "Oozie job id is $oozieJobId..."
    
    # start Oozie job
    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
    $clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug
    
    # get job status
    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
    
    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
    $clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $JobStatus = $jsonResponse[0].("status")
    
    while($JobStatus -notmatch "SUCCEEDED|KILLED")
    {
        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")
        $jobStatus
    }
    
    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green
    
    #endregion


**Exécuter à nouveau le didacticiel**

Pour ré-exécuter le flux de travail, vous devez supprimer les éléments suivants :

- Le fichier de sortie script ruche
- Les données de la table log4jLogsCount

Voici un exemple de script PowerShell que vous pouvez utiliser :

    $resourceGroupName = "<AzureResourceGroupName>"
    
    $defaultStorageAccountName = "<AzureStorageAccountName>"
    $defaultBlobContainerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServerName = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

##<a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à définir un flux de travail Oozie et l’exécution d’un travail de Oozie à l’aide de PowerShell. Pour plus d’informations, consultez les articles suivants :

- [Utiliser un coordinateur Oozie temporels avec HDInsight][hdinsight-oozie-coordinator-time]
- [Mise en route avec Hadoop ruche dans HDInsight pour analyser l’utilisation d’un combiné mobile][hdinsight-get-started]
- [Utilisez le stockage des objets Blob Azure avec HDInsight][hdinsight-storage]
- [Administrer les HDInsight à l’aide de PowerShell][hdinsight-admin-powershell]
- [Téléchargement des données pour les projets de Hadoop de HDInsight][hdinsight-upload-data]
- [Utilisez Sqoop avec Hadoop dans HDInsight][hdinsight-use-sqoop]
- [Utilisez la ruche avec Hadoop sur HDInsight][hdinsight-use-hive]
- [Utilisez des porcs avec Hadoop sur HDInsight][hdinsight-use-pig]
- [Développer les programmes Java MapReduce pour HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
