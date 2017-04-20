<properties 
    pageTitle="La Transformation des données : Données de processus et de la transformation | Microsoft Azure" 
    description="Découvrez comment transformer des données ou des données de processus dans l’usine de données Azure à l’aide d’Hadoop, apprentissage automatique ou Azure données lac Analytique." 
    keywords="transformation de données, les données de processus, transformer les données, l’activité de transformation"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/23/2016" 
    ms.author="shlo"/>

# <a name="transform-data-in-azure-data-factory"></a>Transformer des données dans Azure Data Factory
> [AZURE.SELECTOR]
[La ruche](data-factory-hive-activity.md)  
[Porcs](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop en flux continu](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[La procédure stockée](data-factory-stored-proc-activity.md)
[Données lac Analytique U-SQL](data-factory-usql-activity.md)
[.NET personnalisés](data-factory-use-custom-activities.md)
   

## <a name="overview"></a>Vue d’ensemble 
Cet article explique les activités de transformation des données dans une usine de données Azure que vous pouvez utiliser pour transformer et traite les données brutes dans les prévisions et les analyses. Une activité de transformation s’exécute dans un environnement informatique comme HDInsight d’Azure cluster ou un lot d’Azure. Il fournit des liens vers des articles avec des informations détaillées sur chaque activité de transformation.
 
Usine de données prend en charge les données transformation activités suivantes qui peuvent être ajoutées à des [tuyaux](data-factory-create-pipelines.md) soit individuellement ou en chaîne avec une autre activité.

> [AZURE.NOTE] Pour une procédure pas à pas des instructions détaillées, reportez-vous à l’article [créer un pipeline de transformation de la ruche](data-factory-build-your-first-pipeline.md) .  

## <a name="hdinsight-hive-activity"></a>Activité de la ruche de HDInsight
L’activité de la ruche de HDInsight dans un pipeline de données Factory exécute les requêtes de ruche sur votre propre ou à la demande basés sur Windows/Linux de HDInsight du cluster. Consultez l’article de [La ruche l’activité](data-factory-hive-activity.md) pour plus d’informations sur cette activité. 

## <a name="hdinsight-pig-activity"></a>Activité de porc de HDInsight
L’activité HDInsight porc dans un pipeline de données Factory exécute les requêtes de porc sur votre propre ou à la demande basés sur Windows/Linux de HDInsight du cluster. Consultez l’article de [Porc activité](data-factory-pig-activity.md) pour plus d’informations sur cette activité. 

## <a name="hdinsight-mapreduce-activity"></a>Activité de HDInsight MapReduce
L’activité HDInsight MapReduce dans un pipeline de données Factory exécute les programmes MapReduce sur votre propre ou à la demande basés sur Windows/Linux de HDInsight du cluster. Reportez-vous à l’article [MapReduce activité](data-factory-map-reduce.md) pour plus d’informations sur cette activité.

Vous pouvez utiliser MapReduce activité pour exécuter des programmes d’allumage sur votre cluster HDInsight Spark. Pour plus d’informations, reportez-vous à la section [programmes appeler une étincelle provenant d’une usine de données Azure](data-factory-spark.md) .

## <a name="hdinsight-streaming-activity"></a>Activité de flux de HDInsight
L’activité de diffusion en continu de HDInsight dans un pipeline de données Factory exécute Hadoop de diffusion en continu des programmes sur votre propre ou à la demande basés sur Windows/Linux de HDInsight du cluster. Consultez [HDInsight de diffusion en continu de l’activité](data-factory-hadoop-streaming-activity.md) pour plus d’informations sur cette activité.

## <a name="machine-learning-activities"></a>Activités d’apprentissage machine
Azure Data Factory vous permet de créer facilement des pipelines qui utilisent un service web de Azure Machine Learning publié pour analytique prédictive. À l’aide de l' [Activité d’exécution du lot](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) dans un pipeline Azure Data Factory, vous pouvez appeler un service web de formation de l’ordinateur pour effectuer des prévisions sur les données de lot.

Au fil du temps, les modèles prédictifs dans l’apprentissage Machine score des expériences doivent être formés à l’aide de nouveaux jeux de données d’entrée. Une fois que vous avez terminé avec le recyclage, à mettre à jour le service web de score avec le modèle d’apprentissage automatique retrained. Vous pouvez utiliser l' [Activité de ressource mise à jour](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) pour mettre à jour le service web avec le modèle nouvellement formé.  

Pour plus d’informations sur ces activités d’apprentissage de l’ordinateur, reportez-vous à la section [activités d’apprentissage d’ordinateur utilisé](data-factory-azure-ml-batch-execution-activity.md) . 

## <a name="stored-procedure-activity"></a>Activité de procédure stockée
Vous pouvez utiliser l’activité de la procédure stockée SQL Server dans un pipeline de données usine pour appeler une procédure stockée dans un des magasins de données suivants : Azure SQL base de données, entrepôt de données de SQL Azure, SQL Server de la base de données de votre entreprise ou une machine virtuelle d’Azure. Consultez l’article [d’Activité de procédure stockée](data-factory-stored-proc-activity.md) pour plus de détails.  

## <a name="data-lake-analytics-u-sql-activity"></a>Activité de données Analytique de LAC U-SQL
Activité de données lac Analytique U-SQL exécute un script de U-SQL sur un cluster Azure données lac Analytique. Consultez l’article de [Données Analytique U-SQL activité](data-factory-usql-activity.md) pour plus d’informations. 

## <a name="net-custom-activity"></a>Activité personnalisée de .NET
Si vous avez besoin transformer des données d’une manière qui n’est pas pris en charge par l’usine de données, vous pouvez créer une activité personnalisée avec votre propre logique de traitement de données et utiliser l’activité dans le pipeline. Vous pouvez configurer l’activité .NET personnalisée à exécuter à l’aide d’un service de traitement par lots d’Azure, soit un cluster Azure HDInsight. Consultez l’article [d’activités personnalisées d’utilisation](data-factory-use-custom-activities.md) pour plus d’informations. 

Vous pouvez créer une activité personnalisée pour exécuter des scripts de R sur votre cluster HDInsight avec R installé. Reportez-vous [à l’aide d’exécuter le Script R Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Les environnements de calcul
Vous créez un service lié à l’environnement de calcul et que vous utilisez le service lié lors de la définition d’une activité de transformation. Il existe deux types d’environnements de calcul prises en charge par Data Factory. 

1. **À la demande**: dans ce cas, l’environnement informatique est entièrement gérée par Data Factory. Il est automatiquement créé par le service Data Factory avant un travail est soumis pour traiter les données et supprimé lorsque la tâche est terminée. Vous pouvez configurer et contrôler les paramètres précis de l’environnement de calcul de la demande pour l’exécution du travail, la gestion de cluster et actions d’amorçage. 
2. **Mettez votre propre**: dans ce cas, vous pouvez enregistrer votre propre environnement informatique (par exemple le cluster HDInsight) comme un service lié dans Data Factory. L’environnement informatique est géré par vous et le service Factory de données utilise pour exécuter les activités. 

Consultez l’article de [Calculer des Services liés](data-factory-compute-linked-services.md) à en savoir plus sur Calculer des services pris en charge par Data Factory. 


## <a name="summary"></a>Résumé
Usine de données Azure prend en charge les activités de transformation de données suivantes et les environnements de calcul pour les activités. Les activités de transformation peuvent être ajoutées à des tuyaux soit individuellement ou chaînées avec une autre activité.

Activité de transformation de données |  Environnement informatique 
:----------------------- | :--------------------
[La ruche](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Porcs](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop en continu](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Machine d’activités de formation : l’exécution du lot et des ressources de mise à jour](data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[Procédure stockée](data-factory-stored-proc-activity.md) | SQL Azure, SQL Azure Data Warehouse ou de SQL Server |
[Données Analytique de LAC U-SQL](data-factory-usql-activity.md) | Données Azure lac Analytique 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou un lot d’Azure
   

