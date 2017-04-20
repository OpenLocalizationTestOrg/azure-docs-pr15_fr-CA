<properties
   pageTitle="Utilisez MapReduce et PowerShell avec Hadoop | Microsoft Azure"
   description="Découvrez comment utiliser PowerShell pour exécuter à distance des travaux de MapReduce avec Hadoop sur HDInsight."
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
   ms.date="08/29/2016"
   ms.author="larryfr"/>

# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Exécuter des travaux de MapReduce avec Hadoop sur HDInsight à l’aide de PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Ce document fournit un exemple d’utilisation d’Azure PowerShell pour exécuter un travail MapReduce dans un Hadoop sur cluster de HDInsight.

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

- **Un cluster Azure HDInsight (Hadoop sur HDInsight) (Windows ou Linux)**

- **Une station de travail avec PowerShell d’Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a id="powershell"></a>Exécuter une tâche MapReduce à l’aide de PowerShell d’Azure

PowerShell Azure fournit des *applets de commande* qui vous permettent d’exécuter à distance des travaux de MapReduce sur HDInsight. En interne, cela s’effectue à l’aide d’appels reste à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement Templeton) s’exécutant sur le cluster de HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des travaux de MapReduce dans un cluster d’HDInsight à distance.

* **Connexion-AzureRmAccount**: PowerShell Azure authentifie votre abonnement Azure

* **Nouveau-AzureRmHDInsightMapReduceJobDefinition**: crée une nouvelle *définition de tâche* à l’aide des informations spécifiées MapReduce

* **Démarrer-AzureRmHDInsightJob**: envoie la définition de travail à HDInsight, démarre la tâche et renvoie un objet de *travail* qui peut être utilisé pour vérifier l’état de la tâche

* **Attente-AzureRmHDInsightJob**: utilise l’objet job pour vérifier l’état de la tâche. Il attend la fin du travail ou le temps d’attente est dépassé.

* **Get-AzureRmHDInsightJobOutput**: permet de récupérer la sortie de la tâche

Les étapes suivantes expliquent comment utiliser ces applets de commande pour exécuter une tâche dans votre cluster HDInsight.

1. À l’aide d’un éditeur, d’enregistrer le code suivant en tant que **mapreducejob.ps1**. Vous devez remplacer **NOM_CLUSTER** avec le nom de votre cluster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Login-AzureRmAccount
        }

        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        #Define the MapReduce job
        #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
        # -JarFile = the JAR containing the MapReduce application
        # -ClassName = the class of the application
        # -Arguments = The input file, and the output directory
        $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasbs:///example/data/gutenberg/davinci.txt", `
                "wasbs:///example/data/WordCountOutput"

        #Submit the job to the cluster
        Write-Host "Start the MapReduce job..." -ForegroundColor Green
        $wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

        #Wait for the job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
        # Print the output
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Ouvrez une nouvelle invite de **PowerShell d’Azure** . Modifiez les répertoires vers l’emplacement du fichier **mapreducejob.ps1** , puis utilisez la commande suivante pour exécuter le script :

        .\mapreducejob.ps1
    
    Lorsque vous exécutez le script, vous pouvez être invité à authentifier votre abonnement Azure. Vous devrez également fournir le nom de compte HTTPS/Admin et le mot de passe pour l’HDInsight du cluster.

3. Une fois la tâche terminée, vous devriez recevoir une sortie similaire à ce qui suit :

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Cette sortie indique que la tâche s’est terminée correctement.

    > [AZURE.NOTE] Si **code de sortie** est une valeur autre que 0, consultez [résolution des problèmes](#troubleshooting).

    Cet exemple stocke également les fichiers téléchargés vers un fichier **output.txt** dans le répertoire que vous exécutez le script à partir de.

###<a name="view-output"></a>Sortie d’affichage

Ouvrez le fichier **output.txt** dans un éditeur de texte pour voir les mots et les nombres générés par la tâche.

> [AZURE.NOTE] Les fichiers de sortie d’un travail MapReduce sont immuables. Ainsi, si vous exécutez à nouveau cet exemple, vous devez modifier le nom du fichier de sortie.

##<a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n’est renvoyée lorsque le travail est terminé, une erreur peut avoir pendant le traitement. Pour afficher les informations d’erreur pour cette tâche, ajoutez la commande suivante à la fin du fichier **mapreducejob.ps1** et enregistrez-le puis exécutez-le à nouveau.

    # Print the output of the WordCount job.
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Retourne les informations écrites dans STDERR sur le serveur lors de l’exécution de la tâche, et elle peut aider à déterminer les raisons de l’échec du travail.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, Azure PowerShell fournit un moyen simple pour exécuter des travaux de MapReduce sur un cluster HDInsight, surveiller l’état du travail et récupérer la sortie.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales à propos des tâches MapReduce dans HDInsight :

* [Utilisez MapReduce Hadoop de HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)
