<properties
    pageTitle="Toutes les rubriques pour service de fabrique de données | Microsoft Azure"
    description="Tableau de toutes les rubriques du service Azure appelé Data Factory qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>


# <a name="all-topics-for-azure-data-factory-service"></a>Toutes les rubriques pour service de fabrique de données Azure

Cette rubrique répertorie toutes les rubriques qui s’applique directement au service de **Données fabrique** d’Azure. Vous pouvez rechercher cette page Web pour les mots clés à l’aide de **Ctrl + F**, pour trouver les sujets d’intérêt en cours.




## <a name="new"></a>Nouveau

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 1 | [Déplacement des données à partir d’Amazon Redshift à l’aide de la fabrique de données Azure](data-factory-amazon-redshift-connector.md) | Découvrez comment déplacer des données à partir de Redshift d’Amazon à l’aide de la fabrique de données Azure. |
| 2 | [Déplacement des données à partir d’Amazon Simple Storage Service à l’aide de la fabrique de données Azure](data-factory-amazon-simple-storage-service-connector.md) | Découvrez comment déplacer des données à partir d’Amazon Simple Storage Service (S3) à l’aide de la fabrique de données Azure. |
| 3 | [Azure Data Factory - Assistant copie](data-factory-azure-copy-wizard.md) | Obtenir des informations sur l’utilisation de l’Assistant de copie de données en usine Azure pour copier des données à partir de sources de données prises en charge dans les récepteurs. |
| 4 | [Didacticiel : Créer votre première fabrique de données Azure à l’aide des API REST de fabrique de données](data-factory-build-your-first-pipeline-using-rest-api.md) | Dans ce didacticiel, vous créez un tuyau d’Azure Data Factory exemple à l’aide des API REST de fabrique de données. |
| 5 | [Didacticiel : Créer un pipeline avec une activité de copie à l’aide de l’API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) | Dans ce didacticiel, vous créez un tuyau fabrique de données Azure avec une activité de copie à l’aide de l’API .NET. |
| 6 | [Didacticiel : Créer un pipeline avec une activité de copie à l’aide des API REST](data-factory-copy-activity-tutorial-using-rest-api.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide des API REST. |
| 7 | [Assistant de copie de données en usine](data-factory-copy-wizard.md) | Obtenir des informations sur l’utilisation de l’Assistant de copie de données en usine pour copier des données à partir de sources de données prises en charge dans les récepteurs. |
| 8 | [Passerelle de gestion des données](data-factory-data-management-gateway.md) | Configurer une passerelle de données pour déplacer les données entre local et le nuage. Utiliser la passerelle de gestion des données dans Azure Data Factory pour déplacer vos données. |
| 9 | [Déplacer les données d’une base de données de Cassandra sur site à l’aide de la fabrique de données Azure](data-factory-onprem-cassandra-connector.md) | Découvrez comment déplacer des données d’une base de données de Cassandra sur site à l’aide de la fabrique de données Azure. |
| 10 | [Déplacement des données à l’aide d’Azure Data Factory de MongoDB](data-factory-on-premises-mongodb-connector.md) | Découvrez comment déplacer des données de base MongoDB Azure Data Factory. |
| 11 | [Déplacement des données à partir de la force de vente à l’aide d’Azure Data Factory](data-factory-salesforce-connector.md) | Obtenir des informations sur la façon de déplacer des données à partir de la force de vente à l’aide d’Azure Data Factory. |


## <a name="updated-articles-data-factory"></a>Articles mis à jour, Data Factory

Cette section répertorie les articles qui ont été mis à jour récemment, où la mise à jour a été grand ou importantes. Pour chaque article mis à jour, un extrait de code approximative du texte démarque supplémentaire s’affiche. Les articles ont été mis à jour au sein de la plage de dates de **2016-08-22** pour **2016-10-05**.

| &nbsp; | Article | Texte mis à jour, l’extrait de code | Mise à jour lorsque |
| --: | :-- | :-- | :-- |
| 12 | [Azure Data Factory - journal des modifications de l’API .NET](data-factory-api-change-log.md) | Cet article fournit des informations sur les modifications apportées sur le Factory de données Azure SDK avec une version spécifique. Vous pouvez trouver le dernier package NuGet pour Azure Data Factory ici (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) **Version 4.11.0** des ajouts de fonctionnalités : / les types suivants de services liés qui ont été ajoutés : - OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx) - AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx) - AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / types dataset suivants ont été ajoutés : - MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx) - AmazonS3Dataset (https://msdn.microsoft.com/library/mt765112.aspx) / les types de source suivantes ont été ajoutées. :-MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) **Version 4.10.0** / les propriétés optionnelles suivantes ont été ajoutées à TextFormat :-Ski | 2016-09-22 |
| 13 | [Déplacement des données vers et à partir du Blob Azure à l’aide de la fabrique de données Azure](data-factory-azure-blob-connector.md) |  / copyBehavior / définit le comportement de copie lorsque la source est BlobSource ou système de fichiers.  / **PreserveHierarchy :** conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source dans le dossier source est identique pour le chemin d’accès relatif du fichier cible dans le dossier cible... br /... br /. **FlattenHierarchy :** tous les fichiers du dossier source sont dans le premier niveau du dossier cible. Les fichiers de la cible ont les nom généré automatiquement. .br /... br /. **MergeFiles : (par défaut)** fusionne tous les fichiers du dossier source vers un fichier. Si le nom de fichier/Blob est spécifié, le nom du fichier fusionné serait le nom spécifié ; dans le cas contraire, seraient de nom de fichier généré automatiquement.  / Ne / **BlobSource** prend également en charge ces deux propriétés pour la compatibilité descendante. / **treatEmptyAsNull**: Spécifie s’il faut traiter null ou une chaîne vide comme valeur null. / **skipHeaderLineCount** - indique le nombre de lignes doit être ignoré. Il est applicable uniquement lorsque dataset d’entrée utilise TextFormat. De même, **BlobSink** prend en charge les th | 2016-09-28 |
| 14 | [Créer des pipelines prédictives à l’aide des activités d’apprentissage d’ordinateur Azure](data-factory-azure-ml-batch-execution-activity.md) | **Service Web nécessite plusieurs entrées** Si le service web accepte plusieurs entrées, utilisez la propriété **webServiceInputs** au lieu d’utiliser **webServiceInput**. Groupes de données qui sont référencées par le **webServiceInputs** doit également être incluse dans l’activité d' **entrées**. Dans votre expérience ML d’Azure, entrée du service web et les voies de sortie et les paramètres globaux ont des noms par défaut (« input1 », « input2 ») que vous pouvez personnaliser. Les noms que vous utilisez pour les paramètres globalParameters, webServiceOutputs et webServiceInputs doivent correspondre exactement les noms dans les expériences. Vous pouvez afficher la charge utile de demande exemple sur la page d’aide de l’exécution par lots pour votre point de terminaison Azure ML vérifier le mappage attendu.    {« nom » : « PredictivePipeline », « propriétés » : {« description » : « utiliser le modèle de AzureML », « activités » : {« nom » : « MLActivity », « type » : « AzureMLBatchExecution », « description » : « analyse de prévision sur les commandes d’entrée », « entrées » : {« nom » : « inputDataset1 »}, {« nom » : « inputDatase | 2016-09-13 |
| 15 | [Copier une activité guide des performances et optimisation](data-factory-copy-activity-performance.md) | 1. **établir une planification initiale**. Pendant la phase de développement, testez votre pipeline à l’aide d’activité de la copie par rapport à un échantillon de données représentatives. Vous pouvez utiliser le Factory de données découper le modèle (données-factory-planification-et-execution.md time-series-datasets-and-data-slices) pour limiter la quantité de données que vous manipulez.  Collecter les temps d’exécution et les caractéristiques de performances à l’aide de l' **analyse et gestion App**. Choisissez **de moniteurs et de gérer** sur votre page d’accueil de Data Factory. Dans l’arborescence, sélectionnez le **dataset de sortie**. Dans la liste **Activité** , cliquez sur l’activité de copie de s’exécuter. **Activité Windows** répertorie la durée d’activité de copie et de la taille des données qui sont copiées. Le débit est répertorié dans **l’Explorateur de Windows d’activité**. Pour en savoir plus sur l’application, voir moniteur et de gérer les pipelines de l’usine de données Azure à l’aide de l’analyse et l’application de gestion (données-factory-moniteur-gérer-app.md).  ! Détails (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn de l’exécution des activités | 2016-09-27 |
| 16 | [Didacticiel : Créer un pipeline avec une activité de copie à l’aide de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) |   Notez les points suivants :-le groupe de données **type** est définie sur **AzureBlob**.     - **linkedServiceName** est définie sur **AzureStorageLinkedService**. Vous avez créé ce service lié à l’étape 2.     - **folderPath** est définie avec le conteneur de **adftutorial** . Vous pouvez également spécifier le nom d’un objet blob dans le dossier à l’aide de la propriété de **nom de fichier** . Étant donné que vous ne spécifiez pas le nom de l’objet blob, les données à partir de tous les objets BLOB dans le conteneur sont considérées comme des données d’entrée.    -le **type** de format a la valeur **TextFormat** - il y a deux champs dans le texte fichier ΓÇô **FirstName** et **LastName** ΓÇô séparé par une virgule (**columnDelimiter**) - la **disponibilité** est définie pour **toutes les heures** (**fréquence** est réglée sur **l’heure** et **l’intervalle** est défini à **1**). Par conséquent, Data Factory recherche les données d’entrée toutes les heures dans le dossier racine du conteneur blob (**adftutorial**) que vous avez spécifié.  Si vous ne spécifiez pas un **nom de fichier** pour un groupe de données **d’entrée** , tous les fichiers BLOB à partir du dossier d’entrée (**folderPath**) sont consid | 2016-09-29 |
| 17 | [Créer, surveiller et gérer à l’aide du Kit de développement .NET Data Factory de fabriques de données Azure](data-factory-create-data-factories-programmatically.md) | Notez l’ID d’application et le mot de passe (secret client) et l’utiliser dans la procédure pas à pas. **Abonnement Azure d’obtenir et ID de client** Si vous n’avez pas la version la plus récente de PowerShell installé sur votre ordinateur, suivez les instructions de la procédure pour installer et configurer Azure PowerShell (... / powershell-install-configure.md) article pour l’installer. 1. Démarrer PowerShell d’Azure et exécutez la commande suivante 2. Exécutez la commande suivante, puis entrez le nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter au portail Azure.         Connexion-AzureRmAccount si que vous avez un seul abonnement Azure associé à ce compte, il est inutile d’effectuer les deux étapes suivantes. 3. Exécutez la commande suivante pour afficher tous les abonnements pour ce compte.       Get-AzureRmSubscription 4. Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **NameOfAzureSubscription** par le nom de votre abonnement Azure.       Get-AzureRmSubscription SubscriptionName - NameOfAzureSubscription / AzureRmCo de l’ensemble | 2016-09-14 |
| 18 | [Pipelines et les activités dans l’usine de données Azure](data-factory-create-pipelines.md) |       « start » : « 2016-07-12T00:00:00Z », « fin » : « 2016-07-13T00:00:00Z »}} Notez les points suivants : dans la section activités, il est uniquement une activité dont le **type** est définie pour la **copie**. / L’entrée de l’activité est définie sur **InputDataset** et sortie de l’activité est définie sur **OutputDataset**. / Dans la section **typeProperties** , **BlobSource** est spécifié comme type de source et **SqlSink** est spécifié comme type de récepteurs. Pour une procédure complète de création de ce pipeline, consultez Didacticiel : copier des données depuis le stockage Blob dans la base de données SQL (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). **Pipeline de transformation d’exemple** Dans le pipeline d’exemple suivante, il existe une activité de type **HDInsightHive** dans la section **activités** . Dans cet exemple, l’activité de la ruche de HDInsight (données-factory-ruche-activity.md) transforme les données d’un stockage Azure Blob en exécutant un fichier de script de ruche sur un cluster Azure HDInsight Hadoop.  {« nom » : « TransformPipeline », « p | 2016-09-27 |
| 19 | [Transformer des données dans Azure Data Factory](data-factory-data-transformation-activities.md) | Usine de données prend en charge les activités de transformation de données suivantes qui peuvent être ajoutées à tuyaux (données-factory-créer-pipelines.md) soit individuellement ou en chaîne avec une autre activité. .  AZURE. Remarque pour une procédure pas à pas les instructions pas à pas, voir créer un pipeline avec l’article de ruche transformation (data-factory-build-your-first-pipeline.md). **Activité de la ruche de HDInsight** L’activité de la ruche de HDInsight dans un pipeline de données Factory exécute les requêtes de ruche sur votre propre ou à la demande basés sur Windows/Linux de HDInsight du cluster. Consultez l’article (données-factory-ruche-activity.md) de ruche activité pour plus d’informations sur cette activité. **Activité de porc de HDInsight** L’activité HDInsight porc dans un pipeline de données Factory exécute les requêtes de porc sur votre propre ou à la demande basés sur Windows/Linux de HDInsight du cluster. Consultez l’article (données-factory-porc-activity.md) de porc activité pour plus d’informations sur cette activité. **Activité de HDInsight MapReduce** L’activité HDInsight MapReduce dans un pipeline de données usine exécute les programmes MapReduce sur y | 2016-09-26 |
| 20 | [Données de planification en usine et de l’exécution](data-factory-scheduling-and-execution.md) | CopyActivity2 s’exécute uniquement si le CopyActivity1 a été exécuté avec succès et Dataset2 est disponible. Ici est le pipeline exemple JSON : {« nom » : « ChainActivities », « propriétés » : {« description » : « Exécuter les activités dans l’ordre, » « activités » : {« type » : « Copy », « typeProperties » : {« source » : {« type » : « BlobSource »}, « récepteur » : {« type » : « BlobSink », « copyBehavior » : « PreserveHierarchy », « writeBatchSize » : 0, « writeBatchTimeout » : « 00 : 00:00 »}}, « inputs » : {« nom » : « Dataset1 »}, « sorties » : {« nom » : « Dataset2 »}, « stratégie » : {« timeout » : « 01 : 00:00 »}, « planificateur » : {« fréquence » : « Heure », « interval » : 1}, « nom » : « CopyFromBlob1ToBlob2 », « description » : « Copie de données à partir d’un objet blob à un autre »}, {« type » : « Copy », « typeProperties » : {« source » : {« type » : « BlobSource »}, « récepteur » : {« type » : « BlobSink », « writeBatchSize » : 0, « writeBatchTimeout » : « 00 : 00:00 »}}, « dans | 2016-09-28 |





## <a name="tutorials"></a>Didacticiels

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 21 | [Didacticiel : Créer votre premier pipeline pour traiter des données à l’aide d’Hadoop cluster](data-factory-build-your-first-pipeline.md) | Ce didacticiel Azure Data Factory vous montre comment créer et planifier une fabrique de données qui traite des données à l’aide du script de la ruche sur un cluster Hadoop. |
| 22 | [Didacticiel : Créer votre première fabrique de données Azure à l’aide du modèle de gestionnaire de ressources Azure](data-factory-build-your-first-pipeline-using-arm.md) | Dans ce didacticiel, vous créez un tuyau d’Azure Data Factory exemple à l’aide d’un modèle de gestionnaire de ressources Azure. |
| 23 | [Didacticiel : Créer votre première fabrique de données Azure à l’aide du portail Azure](data-factory-build-your-first-pipeline-using-editor.md) | Dans ce didacticiel, vous créez un tuyau d’Azure Data Factory exemple à l’aide de la fabrique d’éditeur des données dans le portail Azure. |
| 24 | [Didacticiel : Créer votre première fabrique de données Azure à l’aide de PowerShell d’Azure](data-factory-build-your-first-pipeline-using-powershell.md) | Dans ce didacticiel, vous créez un tuyau d’Azure Data Factory exemple à l’aide de PowerShell d’Azure. |
| 25 | [Didacticiel : Usine de données création de votre première Azure à l’aide de Visual Studio de Microsoft](data-factory-build-your-first-pipeline-using-vs.md) | Dans ce didacticiel, vous créez un tuyau d’Azure Data Factory exemple à l’aide de Visual Studio. |
| 26 | [Didacticiel : Créer un pipeline avec une activité de copie à l’aide du portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) | Dans ce didacticiel, vous créez un tuyau fabrique de données Azure avec une activité de copie à l’aide de l’éditeur de la fabrique de données dans le portail Azure. |
| 27 | [Didacticiel : Créer un pipeline avec une activité de copie à l’aide de PowerShell d’Azure](data-factory-copy-activity-tutorial-using-powershell.md) | Dans ce didacticiel, vous créez un tuyau fabrique de données Azure avec une activité de copie à l’aide de PowerShell d’Azure. |
| 28 | [Didacticiel : Créer un pipeline avec une activité de copie à l’aide de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | Dans ce didacticiel, vous créez un tuyau fabrique de données Azure avec une activité de copie à l’aide de Visual Studio. |
| 29 | [Copier des données depuis le stockage Blob pour la base de données SQL à l’aide de la fabrique de données](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Ce didacticiel vous montre comment utiliser les activités de copie dans un pipeline Azure Data Factory pour copier des données depuis le stockage Blob dans la base de données SQL. |
| 30 | [Didacticiel : Créer un pipeline avec une activité de copie de l’Assistant de copie de données en usine](data-factory-copy-data-wizard-tutorial.md) | Dans ce didacticiel, vous créez un tuyau fabrique de données Azure avec une activité de copie à l’aide de l’Assistant copie pris en charge par Data Factory |



## <a name="data-movement"></a>Déplacement des données

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 31 | [Déplacement des données vers et à partir du Blob Azure à l’aide de la fabrique de données Azure](data-factory-azure-blob-connector.md) | Apprenez à copier des données blob dans Azure Data Factory. Utilisez notre exemple : comment faire pour copier des données vers et depuis le stockage Blob Azure et base de données de SQL Azure. |
| 32 | [Déplacement des données vers et à partir du magasin de LAC de données Azure à l’aide de la fabrique de données Azure](data-factory-azure-datalake-connector.md) | Découvrez comment déplacer des données vers/depuis le magasin de LAC de données Azure à l’aide de la fabrique de données Azure |
| 33 | [Déplacement des données vers et à partir de DocumentDB à l’aide de la fabrique de données Azure](data-factory-azure-documentdb-connector.md) | Découvrez comment déplacer des données vers/à partir de la collection d’Azure DocumentDB à l’aide de la fabrique de données Azure |
| 34 | [Déplacement des données vers et à partir de la base de données de SQL Azure à l’aide de la fabrique de données Azure](data-factory-azure-sql-connector.md) | Découvrez comment déplacer des données depuis/vers SQL Azure de base de données à l’aide de la fabrique de données Azure. |
| 35 | [Déplacement des données vers et à partir de l’entrepôt de données de SQL Azure à l’aide de la fabrique de données Azure](data-factory-azure-sql-data-warehouse-connector.md) | Découvrez comment déplacer des données vers/depuis le magasin de données SQL Azure à l’aide de la fabrique de données Azure |
| 36 | [Déplacement des données vers et à partir de la Table d’Azure à l’aide de la fabrique de données Azure](data-factory-azure-table-connector.md) | Découvrez comment déplacer des données vers/depuis le stockage par Table Azure à l’aide de la fabrique de données Azure. |
| 37 | [Copier une activité guide des performances et optimisation](data-factory-copy-activity-performance.md) | Obtenir des informations sur les facteurs essentiels qui affectent les performances de transfert de données dans une usine de données Azure, lorsque vous utilisez l’activité de la copie. |
| 38 | [Déplacement des données à l’aide des activités de copie](data-factory-data-movement-activities.md) | Obtenir des informations sur le déplacement des données dans les pipelines de données usine : migration des données entre les magasins de nuage et entre un magasin local et une banque de nuage. Utilisez l’activité de la copie. |
| 39 | [Notes de version de la passerelle de gestion des données](data-factory-gateway-release-notes.md) | Notes de mise à jour de données Management Gateway tory |
| 40 | [Déplacement des données à partir de très sur site à l’aide de la fabrique de données Azure](data-factory-hdfs-connector.md) | Découvrez comment déplacer des données à partir de très sur site à l’aide de la fabrique de données Azure. |
| 41 | [Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle analyse et application de gestion](data-factory-monitor-manage-app.md) | Découvrez comment utiliser de suivi et d’application de gestion pour surveiller et gérer les pipelines et les fabriques de données Azure. |
| 42 | [Déplacement des données entre des sources local et le nuage avec la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) | Configurer une passerelle de données pour déplacer les données entre local et le nuage. Utiliser la passerelle de gestion des données dans Azure Data Factory pour déplacer vos données. |
| 43 | [Déplacer les données source à partir d’un OData à l’aide de la fabrique de données Azure](data-factory-odata-connector.md) | Découvrez comment déplacer des données à partir de sources d’OData à l’aide de la fabrique de données Azure. |
| 44 | [Déplacer les données des magasins de données à partir d’ODBC à l’aide de la fabrique de données Azure](data-factory-odbc-connector.md) | Découvrez comment déplacer des données à partir de magasins de données ODBC à l’aide de la fabrique de données Azure. |
| 45 | [Déplacement des données à partir de DB2 à l’aide de la fabrique de données Azure](data-factory-onprem-db2-connector.md) | En savoir plus sur la façon de déplacer des données d’une base de données DB2 à l’aide de la fabrique de données Azure |
| 46 | [Déplacement des données vers et à partir d’un système de fichiers local à l’aide d’Azure Data Factory](data-factory-onprem-file-system-connector.md) | Découvrez comment déplacer des données vers et à partir d’un système de fichiers local à l’aide d’Azure Data Factory. |
| 47 | [Déplacement des données de MySQL à l’aide de la fabrique de données Azure](data-factory-onprem-mysql-connector.md) | Découvrez comment déplacer des données à partir de la base de données MySQL à l’aide de la fabrique de données Azure. |
| 48 | [Déplacement des données vers/à partir d’Oracle sur site à l’aide de la fabrique de données Azure](data-factory-onprem-oracle-connector.md) | Découvrez comment déplacer des données entre une base de données Oracle qui est local à l’aide de la fabrique de données Azure. |
| 49 | [Déplacement des données à partir de PostgreSQL à l’aide de la fabrique de données Azure](data-factory-onprem-postgresql-connector.md) | Découvrez comment déplacer des données à partir de la base de données PostgreSQL à l’aide de la fabrique de données Azure. |
| 50 | [Déplacement des données à partir de Sybase à l’aide de la fabrique de données Azure](data-factory-onprem-sybase-connector.md) | Découvrez comment déplacer des données à partir de la base de données Sybase à l’aide de la fabrique de données Azure. |
| 51 | [Déplacement des données à partir de Teradata à l’aide de la fabrique de données Azure](data-factory-onprem-teradata-connector.md) | Obtenir des informations sur le connecteur de Teradata pour le service de fabrique des données qui vous permet de déplacer des données à partir de la base de données Teradata |
| 52 | [Déplacer les données vers et à partir de SQL Server sur site ou sur IaaS (Azure VM) à l’aide de la fabrique de données Azure](data-factory-sqlserver-connector.md) | Découvrez comment déplacer des données vers/à partir d’une base de données SQL Server qui est local ou dans un Azure VM à l’aide de la fabrique de données Azure. |
| 53 | [Déplacement des données à partir d’une source de table Web à l’aide de la fabrique de données Azure](data-factory-web-table-connector.md) | Découvrez comment déplacer des données sur site une table dans une page Web à l’aide de la fabrique de données Azure. |



## <a name="data-transformation"></a>Transformation de données

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 54 | [Créer des pipelines prédictives à l’aide des activités d’apprentissage d’ordinateur Azure](data-factory-azure-ml-batch-execution-activity.md) | Décrit la création créer des pipelines prédictives à l’aide de la fabrique de données Azure et d’Azure Machine Learning |
| 55 | [Calculer des Services liés](data-factory-compute-linked-services.md) | En savoir plus sur les enviornments de calcul que vous pouvez utiliser dans les pipelines Azure Data Factory pour/processus de transformation de données. |
| 56 | [Processus à grande échelle des groupes de données à l’aide de la fabrique de données et de traitement par lots](data-factory-data-processing-using-batch.md) | Décrit comment traiter les énormes volumes de données dans un pipeline Azure Data Factory à l’aide d’une capacité de traitement parallèle de lot d’Azure. |
| 57 | [Transformer des données dans Azure Data Factory](data-factory-data-transformation-activities.md) | Découvrez comment transformer des données ou des données de processus dans l’usine de données Azure à l’aide d’Hadoop, apprentissage automatique ou Azure données lac Analytique. |
| 58 | [Activité de diffusion en continu d’Hadoop](data-factory-hadoop-streaming-activity.md) | Découvrez comment vous pouvez utiliser l’activité de diffusion en continu d’Hadoop dans une usine de données Azure pour exécuter des programmes de diffusion en continu de Hadoop sur un cluster de HDInsight sur la demande/votre propre. |
| 59 | [Activité de la ruche](data-factory-hive-activity.md) | Découvrez comment vous pouvez utiliser l’activité de la ruche dans une usine de données Azure pour exécuter des requêtes de la ruche sur un cluster de HDInsight sur la demande/votre propre. |
| 60 | [Appeler des programmes MapReduce provenant d’une usine de données](data-factory-map-reduce.md) | Découvrez comment traiter les données en exécutant des programmes de MapReduce sur un cluster Azure HDInsight à partir d’une fabrique de données Azure. |
| 61 | [Activité de porc](data-factory-pig-activity.md) | Découvrez comment vous pouvez utiliser l’activité de porcs dans une usine de données Azure pour exécuter des scripts de porc sur un cluster de HDInsight sur la demande/votre propre. |
| 62 | [Appeler des programmes étincelle provenant d’une usine de données](data-factory-spark.md) | Apprenez à appeler des programmes d’allumage à partir d’une fabrique de données Azure à l’aide de l’activité de MapReduce. |
| 63 | [SQL Server stockées activité de procédure](data-factory-stored-proc-activity.md) | Découvrez comment vous pouvez utiliser l’activité de procédure stockée SQL Server pour appeler une procédure stockée dans une base de données de SQL Azure ou un magasin de données SQL Azure à partir d’un pipeline de données usine. |
| 64 | [Utiliser des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md) | Apprenez à créer des activités personnalisées et de les utiliser dans un pipeline Azure Data Factory. |
| 65 | [Exécuter un script SQL-U sur Azure données lac Analytique d’Azure Data Factory](data-factory-usql-activity.md) | Découvrez comment traiter les données en exécutant des scripts de U-SQL Azure données lac Analytique compute service. |



## <a name="samples"></a>Exemples

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 66 | [Azure Data Factory - exemples](data-factory-samples.md) | Fournit des détails sur les exemples fournis avec le service Factory de données Azure. |



## <a name="use-cases"></a>Cas d’usage

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 67 | [Etudes de cas](data-factory-customer-case-studies.md) | Découvrez comment certains de nos clients ont été Azure Data Factory. |
| 68 | [Utiliser la casse - création de profils utilisateur](data-factory-customer-profiling-usecase.md) | Découvrez comment Azure Data Factory est utilisé pour créer un workflow piloté par les données (pipeline) pour définir le profil des clients de jeu. |
| 69 | [Utiliser la casse - recommandations de produits](data-factory-product-reco-usecase.md) | Obtenir des informations sur un cas d’usage implémenté à l’aide de la fabrique de données Azure ainsi que d’autres services. |



## <a name="monitor-and-manage"></a>Surveiller et gérer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 70 | [Surveiller et de gérer les pipelines Azure Data Factory](data-factory-monitor-manage-pipelines.md) | Apprenez à utiliser le portail Azure et Azure PowerShell pour surveiller et gérer les fabriques de données Azure et que vous avez créé des tuyaux. |



## <a name="sdk"></a>KIT DE DÉVELOPPEMENT LOGICIEL

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 71 | [Azure Data Factory - journal des modifications de l’API .NET](data-factory-api-change-log.md) | Décrit les modifications de dernière minute, des ajouts de fonctionnalités, des correctifs de bogues etc.... dans une version spécifique de l’API .NET de la fabrique de données Azure. |
| 72 | [Créer, surveiller et gérer à l’aide du Kit de développement .NET Data Factory de fabriques de données Azure](data-factory-create-data-factories-programmatically.md) | Découvrez comment créer, surveiller et gérer les fabriques de données Azure à l’aide du Kit de développement Data Factory par programme. |
| 73 | [Référence du développeur en usine données Azure](data-factory-sdks.md) | En savoir plus sur les différentes façons de créer, de surveiller et de gérer des fabriques de données Azure |



## <a name="miscellaneous"></a>Divers

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 74 | [Factory de données Azure - Forum aux Questions](data-factory-faq.md) | Forum aux questions sur le Factory de données Azure. |
| 75 | [Azure Data Factory - fonctions et Variables système](data-factory-functions-variables.md) | Fournit une liste des variables système et les fonctions d’Azure Data Factory |
| 76 | [Factory de données Azure - règles d’affectation de noms](data-factory-naming-rules.md) | Décrit les règles d’affectation de noms pour les entités de données usine. |
| 77 | [Résolution des problèmes de Data Factory](data-factory-troubleshoot.md) | Découvrez comment résoudre les problèmes liés à l’aide de la fabrique de données Azure. |

