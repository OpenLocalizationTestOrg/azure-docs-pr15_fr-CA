<properties 
    pageTitle="Appeler des programmes étincelle provenant d’une usine de données Azure" 
    description="Apprenez à appeler des programmes d’allumage à partir d’une fabrique de données Azure à l’aide de l’activité de MapReduce." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="spelluru"/>

# <a name="invoke-spark-programs-from-data-factory"></a>Appeler des programmes étincelle provenant d’une usine de données
## <a name="introduction"></a>Introduction
Vous pouvez utiliser l’activité MapReduce dans un pipeline de données usine pour exécuter des programmes d’allumage sur votre cluster HDInsight Spark. Reportez-vous à l’article [MapReduce activité](data-factory-map-reduce.md) pour plus d’informations sur l’utilisation de l’activité avant la lecture de cet article. 

## <a name="spark-sample-on-github"></a>Exemple d’allumage sur GitHub
[Allumage - exemple Data Factory sur GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) montre comment MapReduce activité permet d’appeler un programme d’allumage. Le programme d’allumage copie tout simplement les données à partir d’un conteneur d’Azure Blob à un autre. 

## <a name="data-factory-entities"></a>Entités de données en usine
Le dossier **d’Allumage-CAD/src/ADFJsons** contient les fichiers pour les entités de données usine (services liés, des groupes de données, pipeline).  

Il existe deux **services liés** dans cet exemple : stockage Azure et Azure HDInsight. Indiquez votre nom de stockage Azure et les valeurs de clé dans **StorageLinkedService.json** et clusterUri, nom d’utilisateur et mot de passe dans **HDInsightLinkedService.json**.

Il existe deux **groupes de données** dans cet exemple : **input.json** et **output.json**. Ces fichiers sont situés dans le dossier **jeux de données** .  Ces fichiers représentent des groupes de données d’entrée et de sortie de l’activité MapReduce

Pipelines de l’exemple se trouvent dans le dossier **ADFJsons/Pipeline** . Passez en revue un pipeline pour comprendre comment appeler un programme d’allumage à l’aide de l’activité de MapReduce. 

L’activité MapReduce est configurée pour appeler **com.adf.sparklauncher.jar** dans le conteneur **adflibs** votre stockage Azure (spécifié dans le StorageLinkedService.json). Le code source de ce programme est dans l’allumage-CAD/src/main/java/com/CAD/dossier et il appelle allumage-submit et exécuter des tâches d’allumage. 

> [AZURE.IMPORTANT] 
> Lire [README. TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) pour les informations supplémentaires et les plus récentes avant à l’aide de l’exemple. 
>  
> Utilisez votre propre cluster HDInsight Spark avec cette approche pour appeler les programmes d’allumage à l’aide de l’activité de MapReduce. À l’aide d’un cluster d’HDInsight à la demande n’est pas pris en charge.   


## <a name="see-also"></a>Voir aussi
- [Activité de la ruche](data-factory-hive-activity.md)
- [Activité de porc](data-factory-pig-activity.md)
- [MapReduce activité](data-factory-map-reduce.md)
- [Activité de diffusion en continu d’Hadoop](data-factory-hadoop-streaming-activity.md)
- [Appeler des scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
