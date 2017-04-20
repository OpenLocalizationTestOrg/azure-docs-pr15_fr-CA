<properties
    pageTitle="Déboguer et analyser les services Hadoop avec des dumps de tas | Microsoft Azure"
    description="Collecter des dumps de tas pour les services d’Hadoop et placer à l’intérieur du compte de stockage Azure Blob pour le débogage et l’analyse automatiquement."
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
    ms.date="10/19/2016"
    ms.author="jgao"/>


# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Fait un dump de tas de collecte dans le stockage Blob pour déboguer et analyser les services Hadoop

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Dumps de tas contiennent un instantané de la mémoire de l’application, y compris les valeurs des variables au moment de que la création de l’image. Pour qu’ils soient très utiles pour diagnostiquer des problèmes qui se produisent au moment de l’exécution. Dumps de tas peuvent être automatiquement collectées pour les services d’Hadoop et placés à l’intérieur du compte de stockage Azure Blob d’un utilisateur sous HDInsightHeapDumps /. 

La collection des dumps de tas pour différents services doit être activée pour les services de clusters individuels. La valeur par défaut de cette fonctionnalité doit être désactivée pour un cluster. Les dumps de tas peuvent être volumineux, il est recommandé de surveiller le compte de stockage Blob où elles sont enregistrées dès que la collection a été activée.

> [AZURE.NOTE] Les informations contenues dans cet article ne s’applique qu’à la HDInsight de basés sur Windows. Pour plus d’informations sur les HDInsight de fonctionnant sous Linux, voir [activer des dumps de tas pour les services d’Hadoop sur HDInsight de basé sur Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="eligible-services-for-heap-dumps"></a>Services éligibles pour des dumps de tas

Vous pouvez activer des dumps de tas pour les services suivants :

*  **hcatalog** - tempelton
*  **ruche** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **fils** - resourcemanager, nodemanager, timelineserver
*  **très** - datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Éléments de configuration qui permettent à des dumps de tas

Pour activer des dumps de tas pour un service, vous devez définir les éléments de configuration approprié dans la section de ce service, qui est spécifié par **nom_service**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

La valeur de **service_name** peut être un des services énumérés ci-dessus : tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, ou namenode.

## <a name="enable-using-azure-powershell"></a>Activer l’utilisation de PowerShell d’Azure

Par exemple, pour activer des dumps de tas à l’aide de PowerShell d’Azure pour jobhistoryserver, vous procédez comme :

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Activer à l’aide du Kit de développement .NET

Par exemple, pour activer des dumps de tas à l’aide du Kit de développement .NET de HDInsight Azure pour jobhistoryserver, vous procédez comme :

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
