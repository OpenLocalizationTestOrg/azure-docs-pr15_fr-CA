<properties
   pageTitle="Utilisez la ruche d’Hadoop avec PowerShell dans HDInsight | Microsoft Azure"
   description="Utilisation de PowerShell pour exécuter des requêtes de la ruche dans Hadoop sur HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-using-powershell"></a>Exécuter des requêtes de ruche à l’aide de PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ce document fournit un exemple d’utilisation de PowerShell d’Azure dans le mode de groupe de ressources Azure pour exécuter des requêtes de la ruche dans un Hadoop sur cluster de HDInsight.

> [AZURE.NOTE] Ce document ne fournit pas une description détaillée de ce que font les instructions HiveQL sont utilisées dans les exemples. Pour plus d’informations sur le HiveQL qui est utilisée dans cet exemple, consultez [Utilisation de la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md).


**Conditions préalables**

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants.

- **Un cluster Azure HDInsight (Hadoop sur HDInsight) (Windows ou Linux)**
- **Une station de travail avec PowerShell d’Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a name="run-hive-queries-using-azure-powershell"></a>Exécuter des requêtes de ruche à l’aide de PowerShell d’Azure

PowerShell Azure fournit des *applets de commande* qui vous permettent d’exécuter à distance des requêtes de ruche sur HDInsight. En interne, cela s’effectue à l’aide d’appels reste à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement Templeton) s’exécutant sur le cluster de HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des requêtes de la ruche dans un cluster d’HDInsight à distance :

* **Ajouter-AzureRmAccount**: PowerShell Azure authentifie votre abonnement Azure

* **Nouveau-AzureRmHDInsightHiveJobDefinition**: crée une nouvelle *définition de travail* en utilisant les instructions HiveQL spécifiées

* **Démarrer-AzureRmHDInsightJob**: envoie la définition de travail à HDInsight, démarre la tâche et renvoie un objet de *travail* qui peut être utilisé pour vérifier l’état de la tâche

* **Attente-AzureRmHDInsightJob**: utilise l’objet job pour vérifier l’état de la tâche. Il attendra jusqu'à ce que le travail est terminé ou le temps d’attente est dépassé.

* **Get-AzureRmHDInsightJobOutput**: permet de récupérer la sortie de la tâche

* **Invoke-AzureRmHDInsightHiveJob**: utilisé pour exécuter des instructions de HiveQL. Cela bloque la requête se termine, puis renvoie les résultats

* **Utilisez-AzureRmHDInsightCluster**: définit le cluster en cours à utiliser pour la commande **Invoke-AzureRmHDInsightHiveJob**

Les étapes suivantes expliquent comment utiliser ces applets de commande pour exécuter une tâche dans votre cluster de HDInsight :

1. À l’aide d’un éditeur, d’enregistrer le code suivant en tant que **hivejob.ps1**. Vous devez remplacer **NOM_CLUSTER** avec le nom de votre cluster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Ouvrez une nouvelle invite de **PowerShell d’Azure** . Modifiez les répertoires vers l’emplacement du fichier **hivejob.ps1** , puis utilisez la commande suivante pour exécuter le script :

        .\hivejob.ps1

    Lorsque le script s’exécute, le système vous demandera d’entrer les informations d’identification du compte HTTPS/Admin pour votre cluster. Vous pouvez également être invité à vous connecter à votre abonnement Azure.
    
7. Une fois la tâche terminée, il doit retourner d’informations semblable au suivant :

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Comme mentionné précédemment, **Invoke-ruche** peut servir à exécuter une requête et attendre la réponse. Utilisez les commandes suivantes et remplacez **NOM_CLUSTER** avec le nom de votre cluster :

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

    La sortie ressemble à ce qui suit :

        2012-02-03  18:35:34    SampleClass0    [ERROR] incorrect   id
        2012-02-03  18:55:54    SampleClass1    [ERROR] incorrect   id
        2012-02-03  19:25:27    SampleClass4    [ERROR] incorrect   id

    > [AZURE.NOTE] Pour les requêtes plus longues HiveQL, vous pouvez utiliser l’applet de commande PowerShell de Azure **Ici-chaînes** ou les fichiers de script de HiveQL. L’extrait de code suivant montre comment utiliser l’applet de commande **Invoke-ruche** pour exécuter un fichier de script HiveQL. Le fichier de script de HiveQL doit être téléchargé vers wasbs : / /.
    >
    > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
    >
    > Pour plus d’informations sur les **Chaînes ici**, reportez-vous <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">à l’aide de Windows PowerShell ici-chaînes</a>.

##<a name="troubleshooting"></a>Résolution des problèmes

Si aucune information n’est renvoyée lorsque le travail est terminé, une erreur peut avoir pendant le traitement. Pour afficher des informations d’erreur pour ce projet, ajoutez le code suivant à la fin du fichier **hivejob.ps1** et enregistrez-le puis exécutez-le à nouveau.

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Retourne les informations qui sont écrites dans STDERR sur le serveur lors de l’exécution de la tâche, et elle peut aider à déterminer les raisons de l’échec du travail.

##<a name="summary"></a>Résumé

Comme vous pouvez le voir, Azure PowerShell fournit un moyen simple pour exécuter des requêtes de la ruche dans un cluster de HDInsight, surveiller l’état du travail et récupérer la sortie.

##<a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations générales sur la ruche dans HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
