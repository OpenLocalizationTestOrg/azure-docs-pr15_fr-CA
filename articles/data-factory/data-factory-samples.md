<properties     
    pageTitle="Azure Data Factory - exemples" 
    description="Fournit des détails sur les exemples fournis avec le service Factory de données Azure." 
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
    ms.date="10/18/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---samples"></a>Azure Data Factory - exemples

## <a name="samples-on-github"></a>Exemples de GitHub
Le [référentiel de GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contient plusieurs exemples pour vous aider à rapidement d’accélération avec le service de fabrique de données Azure (ou) modifier les scripts et l’utiliser dans la propre application. Le dossier Samples\JSON contient des extraits de JSON pour les scénarios courants.

| Exemple | Description |
| :----- | :---------- | 
| [Procédure pas à pas de chargeur automatique de documents](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) | Cet exemple fournit une procédure pas à pas de bout en bout pour le traitement des fichiers journaux à l’aide de la fabrique de données Azure pour transformer des données à partir des fichiers journaux aux analyses. <br/><br/>Dans cette procédure pas à pas, le pipeline Data Factory collecte des journaux d’exemple, les processus et enrichit les données à partir des journaux avec les données de référence et transforme les données pour évaluer l’efficacité d’une campagne marketing qui a été lancée récemment. |
| [Exemples JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) | Cet exemple fournit des exemples JSON pour les scénarios courants. | 
| [Exemple de module de téléchargement de données HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) | Cet exemple présente téléchargement de données à partir d’un point de terminaison HTTP sur le stockage Blob Azure à l’aide d’activité personnalisée de .NET. |
| [Cross AppDomain Dot Net d’activité exemple](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) | Cet exemple vous permet de créer une activité personnalisée .NET qui n’est pas limitée aux versions d’assembly utilisées par le Lanceur du chargeur automatique de documents (par exemple, WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.). |
| [Exécuter un script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |  Cet exemple inclut l’activité personnalisée en usine de données qui peut être utilisée pour appeler RScript.exe. Cet exemple ne fonctionne qu’avec votre propre cluster HDInsight (pas à la demande) qui a déjà R installé dessus. |
| [Appeler des travaux d’allumage sur HDInsight Hadoop cluster](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) | Cet exemple montre comment utiliser MapReduce activité pour appeler un programme d’allumage. Le programme d’allumage copie tout simplement les données à partir d’un conteneur d’Azure Blob à un autre. |
| [Analyse à l’aide d’Azure formation lot notation activité de Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) | Cet exemple montre comment utiliser AzureMLBatchScoringActivity pour appeler un modèle d’apprentissage de Machine Azure qui effectue des analyses de sentiment twitter, score, prévision, etc.. |
| [Analyse de Twitter à l’aide d’activité personnalisée](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |  Cet exemple montre comment utiliser une activité .NET personnalisée pour appeler un modèle d’apprentissage de Machine Azure qui effectue des analyses de sentiment twitter, score, prévision, etc.. |
| [Pipelines paramétrées pour l’apprentissage de la Machine Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) | Cet exemple fournit un code de bout en bout pour C# pour déployer des pipelines N pour le score et le recyclage de chacun avec un paramètre différent de la région où la liste des régions est provenant d’un fichier parameters.txt, qui est inclus avec cet exemple. | 
| [Actualisation des données de référence pour les travaux d’Analytique de flux de données Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |  Cet exemple montre comment utiliser ensemble les Factory de données Azure et d’Azure flux Analytique pour exécuter les requêtes avec des données de référence et l’actualisation des données de référence d’une planification de l’installation. |
| [Pipeline hybride avec Hadoop de Hortonworks local](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) | L’exemple utilise un cluster de Hadoop sur site comme cible de calcul pour exécuter les travaux dans l’usine de données tout comme vous ajouteriez des autres cibles de calcul comme un HDInsight en fonction de cluster Hadoop dans le nuage. |
| [Outil de Conversion JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) | Cet outil vous permet de convertir de JSONs à partir de la version antérieure 2015-07-01-aperçu plus récent ou 2015-07-01-aperçu (par défaut). |  
| [U-SQL exemple d’entrée de fichier](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |  Ce fichier est un exemple de fichier utilisé par une activité U-SQL. | 

## <a name="azure-resource-manager-templates"></a>Modèles de gestionnaire de ressources Azure
Vous trouverez les modèles suivants de gestionnaire de ressources Azure pour Data Factory sur Github. 

| Modèle | Description |
| -------- | ----------- | 
| [Copier depuis le stockage Blob Azure à une base de données SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) | Déploiement de ce modèle de crée une fabrique de données Azure avec un pipeline qui copie les données à partir du stockage des blob Azure spécifié pour la base de données SQL d’Azure |    
| [Copier à partir de la force de vente pour le stockage des objets Blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) | Déploiement de ce modèle crée une fabrique de données Azure un pipeline qui copie les données à partir du compte spécifié de la force de vente dans le stockage blob Azure. |    
| [Transformer les données en exécutant le script de la ruche sur un cluster Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) | Un pipeline qui transforme les données en exécutant l’exemple de script de ruche sur un cluster Azure HDInsight Hadoop déployer ce modèle crée une fabrique de données Azure. | 


## <a name="samples-in-azure-portal"></a>Exemples dans Azure portal
La mosaïque de **pipelines de l’échantillon** sur la page d’accueil de votre fabrique de données vous permet de déployer des pipelines de l’échantillon et les entités associées (groupes de données et services liés) dans sur le factory de données. 

1. Créer une fabrique de données ou ouvrez une fabrique de données existant. Reportez-vous à la section [mise en route avec Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#CreateDataFactory) pour obtenir la procédure pour créer une fabrique de données.
2. Dans la lame **DATA FACTORY** de la fabrique de données, cliquez sur la mosaïque de **pipelines de l’échantillon** .

    ![Mosaïque de pipelines d’exemple](./media/data-factory-samples/SamplePipelinesTile.png)

2. De la lame de **pipelines de l’exemple** , cliquez sur l' **échantillon** que vous souhaitez déployer. 
    
    ![Lame d’exemples de pipelines](./media/data-factory-samples/SampleTile.png)

3. Spécifier les paramètres de configuration pour l’exemple. Par exemple, votre clé de compte et de nom de compte de stockage Azure, nom du serveur SQL d’Azure, base de données, ID utilisateur et mot de passe, etc.. 

    ![Lame de l’échantillon](./media/data-factory-samples/SampleBlade.png)

4. Une fois que vous avez terminé pour spécifier les paramètres de configuration, cliquez sur **créer** pour créer/déployer les tuyaux de l’exemple et les services/les tables liées par les pipelines.
5. Vous consultez l’état de déploiement sur la mosaïque d’exemple que vous avez cliqué précédemment sur la lame de **pipelines de l’échantillon** .

    ![État du déploiement](./media/data-factory-samples/DeploymentStatus.png)

6. Lorsque vous voyez le message **le déploiement a réussi** sur la mosaïque pour l’exemple, fermez la lame de **pipelines de l’échantillon** .  
5. Sur le **FACTORY de données** lame, vous voyez que les services liés, les jeux de données et pipelines sont ajoutés à votre fabrique de données.  

    ![Lame en usine](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## <a name="samples-in-visual-studio"></a>Exemples de Visual Studio

### <a name="prerequisites"></a>Conditions préalables

Vous devez avoir les éléments suivants sur votre ordinateur : 

- 2013 de Visual Studio ou Visual Studio 2015
- Télécharger le SDK Azure pour 2013 de Visual Studio ou Visual Studio 2015. Accédez à la [Page de téléchargement d’Azure](https://azure.microsoft.com/downloads/) et cliquez sur **VS 2013** ou **VS 2015** , dans la section **.NET** .
- Télécharger le plug-in Azure Data Factory plus récent de Visual Studio : [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Si vous utilisez Visual Studio 2013, vous pouvez également mettre à jour le plug-in en procédant comme suit : dans le menu, cliquez sur **Outils** -> **mises à jour et des Extensions de** -> **en ligne** -> **La galerie Visual Studio** -> **Microsoft d’une fabrique de données Azure Tools pour Visual Studio** -> **mise à jour**.

### <a name="use-data-factory-templates"></a>Utiliser les modèles de données en usine

1. Cliquez sur **fichier** dans le menu, pointez sur **Nouveau**et cliquez sur **projet**. 
2. Dans la boîte de dialogue **Nouveau projet** , effectuez les opérations suivantes : 
    1. Sélectionnez **DataFactory** sous **modèles**. 
    2. Dans le volet droit, sélectionnez **Les modèles de données en usine** . 
    3. Entrez un **nom** pour le projet. 
    4. Sélectionnez un **emplacement** pour le projet. 
    5. Cliquez sur **OK**. 

    ![Boîte de dialogue Nouveau projet](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. Dans la boîte de dialogue **Modèles de fabrique de données** , sélectionnez le modèle de l’exemple dans la section **Modèles de cas d’utilisation** et cliquez sur **suivant**. Les étapes suivantes vous guident tout au long de l’aide du modèle **Client de profilage** . Étapes sont similaires pour les autres échantillons. 

    ![Boîte de dialogue Modèles de fabrique de données](./media/data-factory-samples/vs-data-factory-templates-dialog.png) 
7. Dans la boîte de dialogue **Configuration d’usine de données** , cliquez sur **suivant** sur la page des **Notions de base de données en usine** .
8. Sur la page de la **fabrique de données de configuration** , procédez comme suit : 
    1. Sélectionnez **Créer nouvelle fabrique de données**. Vous pouvez également sélectionner le **factory de données existant Utilisation**.
    2. Entrez un **nom** pour le factory de données.
    3. Sélectionnez l' **abonnement Azure** dans lequel vous souhaitez le factory de données à créer. 
    4. Sélectionnez le **groupe de ressources** de la fabrique de données.
    5. Sélectionnez le **Ouest des États-Unis**, **Les États-Unis**ou **En Europe du Nord** pour la **région**.
    6. Cliquez sur **suivant**. 
9. Dans la page **stocke les données de configuration** , spécifiez une existante **base de données Azure SQL** et un **compte de stockage Azure** (ou) / stockage de la base de données de créer et cliquez sur Suivant. 
10. Dans la page **configuration de calcul** , sélectionnez les valeurs par défaut et cliquez sur **suivant**. 
11. Dans la page **Résumé** , passez en revue tous les paramètres et cliquez sur **suivant**. 
12. Dans la page **État du déploiement** , attendez que le déploiement est terminé et cliquez sur **Terminer**.
13. Cliquez droit sur le projet dans l’Explorateur de solutions, puis cliquez sur **Publier**. 
19. Si vous voyez la boîte de dialogue **se connecter à votre compte Microsoft** , entrez vos informations d’identification pour le compte qui a des abonnement Azure et cliquez sur **connexion**.
20. Vous devriez voir la boîte de dialogue suivante :

    ![Boîte de dialogue Publier](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Dans la page de la **fabrique de données de configuration** , procédez comme suit : 
    1. Confirmer cette option **factory de données existant Utilisation** .
    2. La **fabrique de données** vous aviez sélectionner lors de l’utilisation du modèle. 
    6. Cliquez sur **suivant** pour passer à la page **Publier des articles** . (Appuyez sur l' **onglet** à déplacer en dehors du champ de nom à si le bouton **suivant** est désactivé.) 
23. Dans la page **Publier les éléments** , assurez-vous que toutes les fabriques de données entités sont sélectionnées et cliquez sur **suivant** pour passer à la page de **Résumé** .     
24. Passez en revue le résumé, puis cliquez sur **suivant** pour démarrer le processus de déploiement et d’afficher l' **État du déploiement**.
25. Dans la page **État du déploiement** , vous devez voir le statut du processus de déploiement. Une fois le déploiement terminé, cliquez sur Terminer. 

Pour plus d’informations sur l’aide de Visual Studio pour créer des entités de données usine, de les publier sur Azure, consultez [créer votre première fabrique de données (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) .          