<properties
   pageTitle="Utiliser Hadoop porc avec PowerShell dans HDInsight | Microsoft Azure"
   description="Découvrez comment soumettre des travaux de porc à un cluster Hadoop sur HDInsight à l’aide de PowerShell d’Azure."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-powershell"></a>Exécuter des travaux de porc à l’aide de PowerShell

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit un exemple d’utilisation de PowerShell d’Azure à soumettre des tâches de porc à un Hadoop sur cluster de HDInsight. Porcs vous permet d’écrire MapReduce travaux à l’aide d’un langage (Latin de porc), qui modélise les transformations de données, plutôt que de mapper et de réduire les fonctions.

> [AZURE.NOTE] Ce document ne fournit pas une description détaillée de ce que font les instructions Latin de porc utilisées dans les exemples. Pour plus d’informations sur le Latin de porc utilisées dans cet exemple, consultez [Utilisation des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md).

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants.

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Une station de travail avec PowerShell d’Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a id="powershell"></a>Exécuter des travaux de porc à l’aide de PowerShell

PowerShell Azure fournit des *applets de commande* qui vous permettent d’exécuter à distance des travaux de porc sur HDInsight. En interne, cela s’effectue à l’aide d’appels reste à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement Templeton) s’exécutant sur le cluster de HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des travaux de porc sur un cluster distant de HDInsight :

* **AzureRmAccount-connexion**: authentifie Azure PowerShell à votre abonnement Azure

* **Nouveau-AzureRmHDInsightPigJobDefinition**: crée une nouvelle *définition de travail* en utilisant les instructions de porc Latin spécifiées

* **Démarrer-AzureRmHDInsightJob**: envoie la définition de travail à HDInsight, démarre la tâche et renvoie un objet de *travail* qui peut être utilisé pour vérifier l’état de la tâche

* **Attente-AzureRmHDInsightJob**: utilise l’objet job pour vérifier l’état de la tâche. Il attendra jusqu'à ce que le travail est terminé, ou que le délai d’attente a été dépassé.

* **Get-AzureRmHDInsightJobOutput**: permet de récupérer la sortie de la tâche

Les étapes suivantes expliquent comment utiliser ces applets de commande pour exécuter une tâche sur votre cluster de HDInsight.

1. À l’aide d’un éditeur, d’enregistrer le code suivant en tant que **pigjob.ps1**. Vous devez remplacer **NOM_CLUSTER** avec le nom de votre cluster HDInsight.

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential

        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"


        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. Ouvrez une nouvelle invite de commande Windows PowerShell. Modifiez les répertoires vers l’emplacement du fichier **pigjob.ps1** , puis utilisez la commande suivante pour exécuter le script :

        .\pigjob.ps1
        
    Vous devrez tout d’abord de se connecter à votre abonnement Azure. Ensuite, vous demandera le nom HTTPs/Admin et le mot de passe pour l’HDInsight du cluster.

7. Une fois la tâche terminée, il doit retourner d’informations semblable au suivant :

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n’est renvoyée lorsque le travail est terminé, une erreur peut avoir pendant le traitement. Pour afficher les informations d’erreur pour cette tâche, ajoutez la commande suivante à la fin du fichier **pigjob.ps1** et enregistrez-le puis exécutez-le à nouveau.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Ceci renverra les informations écrites dans STDERR sur le serveur lors de l’exécution de la tâche, et elle peut aider à déterminer les raisons de l’échec du travail.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, Azure PowerShell fournit un moyen simple pour exécuter des travaux de porc sur un cluster HDInsight, surveiller l’état du travail et récupérer la sortie.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les porcs dans HDInsight :

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
