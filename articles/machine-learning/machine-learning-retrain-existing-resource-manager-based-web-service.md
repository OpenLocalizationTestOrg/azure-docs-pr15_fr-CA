<properties
    pageTitle="Dispenser un service web prédictive existant | Microsoft Azure"
    description="Découvrez comment former un modèle et le service web pour utiliser le modèle nouvellement formé dans Azure Machine formation de mise à jour."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="v-donglo"/>


# <a name="retrain-an-existing-predictive-web-service"></a>Dispenser un service web prédictive existant

Ce document décrit le processus de reconversion pour le scénario suivant :

* Vous avez une expérience de formation et une expérience de prévision que vous avez déployé sous la forme d’un service web operationalized.
* Vous avez de nouvelles données que vous souhaitez que votre service web prédictives à utiliser pour effectuer son score.

Partir de votre service web existant et les expériences, vous devez procédez comme suit :

1. Mettre à jour le modèle.
    1. Modifier votre expérience de formation pour les sorties et les entrées de service web.
    2. Déployer l’expérience de formation sous la forme d’un service web de reconversion.
    3. Lot l’exécution du Service (BES de l’expérience formation) permet de former le modèle.
2. Utiliser les applets de commande PowerShell de formation de Machine Azure mise à jour de l’expérience prédictive.
    1.  Connectez-vous à votre compte Azure le Gestionnaire de ressources.
    2.  Obtenir la définition de service web.
    3.  Exporter la définition du service web en tant que JSON.
    4.  Mettre à jour la référence à l’objet blob d’ilearner dans le JSON.
    5.  Importer le JSON dans une définition de service web.
    6.  Mettre à jour le service web avec une nouvelle définition de service web.

## <a name="deploy-the-training-experiment"></a>Déployer l’expérience de formation

Pour déployer l’expérience de formation sous la forme d’un service web de réadaptation, vous devez ajouter le web service entrées et sorties au modèle. En connectant un module de *Sortie du Service Web* à l’expérience * [Modèle de Train] [ train-model] * module, que vous activez l’expérience de formation produire un nouveau modèle de formation que vous pouvez utiliser dans votre expérience prédictive. Si vous disposez d’un module *d’Évaluation de modèle* , vous pouvez également joindre la sortie du service web pour obtenir les résultats de l’évaluation en tant que sortie.

Pour mettre à jour votre expérience de formation :

1. Connexion d’un module *d’Entrée du Service Web* à votre entrée de données (par exemple, un module de *Nettoyage de données manquants* ). En général, vous souhaitez vous assurer que vos données d’entrée sont traitées de la même manière que vos données d’origine de la formation.
2. Connexion d’un module de *Sortie du Service Web* à la sortie de votre *Modèle de Train* de module.
3. Si vous avez un module *d’Évaluation de modèle* et que vous souhaitez exporter les résultats de l’évaluation, connexion d’un module de *Sortie du Service Web* à la sortie de votre module *d’Évaluer le modèle* .

Exécutez votre expérience.

Ensuite, vous devez déployer l’expérience de formation sous la forme d’un service web qui produit un modèle formé et des résultats d’évaluation de modèle.  

En bas de la zone d’essai, cliquez sur **La valeur d’un Service Web**et sélectionnez **Web Service de déploiement [nouveau]**. Le portail Azure Machine Learning Web Services ouvre la page **Déployer le Service Web** . Tapez un nom pour votre service web et choisir un plan de paiement, puis cliquez sur **déployer**. Vous pouvez uniquement utiliser la méthode d’exécution du traitement par lots pour créer des modèles bien formés.


## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Former le modèle avec de nouvelles données à l’aide d’environnements d’initialisation

Pour cet exemple, nous utilisons C# pour créer l’application de reconversion. Vous pouvez également utiliser l’exemple de code Python ou R pour accomplir cette tâche.

Pour appeler les API de réadaptation :

1. Créez une application de console C# dans Visual Studio (**New** > **projet** > **Bureau Windows** > **Application Console**).
2.  Ouvrez une session sur le portail de Services Web de formation Machine.
3.  Cliquez sur le service web avec lequel vous travaillez.
2.  Cliquez sur **utiliser**.
3.  Au bas de la page de **consommer** , dans la section de **l’Exemple de Code** , cliquez sur **traitement par lots**.
5.  Copier le code C# pour l’exécution du traitement par lots et collez-le dans le fichier Program.cs. Assurez-vous que l’espace de noms reste intact.

Ajoutez le package NuGet Microsoft.AspNet.WebApi.Client, comme indiqué dans les commentaires. Pour ajouter la référence à Microsoft.WindowsAzure.Storage.dll, vous devrez tout d’abord installer la [bibliothèque de client pour les services de stockage Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

La capture d’écran suivante affiche la page de **consommer** dans le portail Azure Machine formation Web Services.

![Utiliser la page][1]

### <a name="update-the-apikey-declaration"></a>Mise à jour de la déclaration d’apikey

Localisez la déclaration **apikey** :

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Dans la section **informations de base de la consommation** de la page de **consommer** , recherchez la clé primaire et le copier à la déclaration de **apikey** .

### <a name="update-the-azure-storage-information"></a>Mettre à jour les informations de stockage Azure

L’exemple de code BES télécharge un fichier à partir d’un lecteur local (par exemple, « C:\temp\CensusIpnput.csv ») pour le stockage Azure, traite et écrit les résultats sur le stockage Azure.  

Pour mettre à jour les informations de stockage Azure, vous devez récupérer le nom du compte de stockage, clé et les informations de conteneur pour votre compte de stockage à partir du portail classique Azure, et puis la mise à jour de la correspondi après l’exécution de votre expérience, le flux de travail qui en résulte doit être semblable au suivant :

![Flux de travail qui en résulte après l’exécution de][4]valeurs de gn dans le code.

1. Connectez-vous au portail Azure classique.
1. Dans la colonne de navigation de gauche, cliquez sur **stockage**.
1. Dans la liste des comptes de stockage, sélectionnez un pour stocker le modèle retrained.
1. En bas de la page, cliquez sur **Gérer les touches d’accès rapide**.
1. Copier et enregistrer la **Clé d’accès primaire** et fermer la boîte de dialogue.
1. En haut de la page, cliquez sur **les conteneurs**.
1. Sélectionnez un conteneur existant, ou créer un nouveau et enregistrer le nom.

Recherchez les *StorageAccountName*, les *StorageAccountKey*et les déclarations de *StorageContainerName* et mettre à jour les valeurs que vous avez enregistré à partir du portail classique.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Vous devez également vous assurer que le fichier d’entrée est disponible à l’emplacement que vous spécifiez dans le code.

### <a name="specify-the-output-location"></a>Spécifier l’emplacement de sortie

Lorsque vous spécifiez l’emplacement de sortie dans la charge utile de demande, l’extension du fichier qui est spécifié dans *RelativeLocation* doit être spécifiée en tant que `ilearner`. Voir l’exemple suivant :

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Voici un exemple de sortie de réadaptation : ![sortie de recyclage][6]

## <a name="evaluate-the-retraining-results"></a>Évaluer les résultats de la reconversion

Lorsque vous exécutez l’application, la sortie inclut l’URL et le jeton de signatures d’accès partagé qui sont nécessaires pour accéder aux résultats de l’évaluation.

Vous pouvez voir les résultats de performances du modèle retrained en combinant les *BaseLocation*, *RelativeLocation*et *SasBlobToken* à partir des résultats de sortie pour *output2* (comme illustré dans l’image de sortie de réadaptation précédente) et collez l’URL complète dans la barre d’adresse du navigateur.  

Examinez les résultats pour déterminer si le modèle qui vient d’être formé fonctionne assez bien pour remplacer l’existant.

Copier le *BaseLocation*, *RelativeLocation*et *SasBlobToken* à partir des résultats de sortie.

## <a name="retrain-the-web-service"></a>Former le service web

Vous dispenser un service web, vous mettez à jour la définition de service web prédictive pour faire référence au nouveau modèle formé. La définition du service web est une représentation interne du modèle qualifié du service web et n’est pas directement modifiable. Vérifiez que vous récupérez la définition du service web pour votre expérience prédictive et pas votre expérience de formation.

## <a name="sign-in-to-azure-resource-manager"></a>Connectez-vous au Gestionnaire de ressources Azure

Vous devez vous connecter à votre compte Azure à partir de l’environnement PowerShell à l’aide de l’applet de commande [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition-object"></a>Obtenir l’objet de définition de Service Web

Ensuite, obtenir l’objet de définition de Service Web en appelant l’applet de commande [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) .

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Pour déterminer le nom du groupe de ressources d’un service web existant, exécutez l’applet de commande Get-AzureRmMlWebService sans paramètres pour afficher les services web dans votre abonnement. Recherchez le service web et observez son ID de service web. Le nom du groupe de ressources est le quatrième élément dans le code, juste après l’élément *resourceGroups* . Dans l’exemple suivant, le nom de groupe de ressources est par défaut-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Ou bien, pour déterminer le nom du groupe de ressources d’un service web existant, vous connecter au portail Azure Machine Learning Web Services. Sélectionnez le service web. Le nom de groupe de ressources est le cinquième élément de l’URL du service web, juste après l’élément *resourceGroups* . Dans l’exemple suivant, le nom de groupe de ressources est par défaut-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exporter l’objet de définition de Service Web en tant que JSON

Pour modifier la définition du modèle formé pour utiliser le modèle nouvellement formé, vous devez d’abord utiliser l’applet de commande [Exportation-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) pour l’exporter dans un fichier au format JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Mise à jour de la référence à l’objet blob ilearner

Dans les ressources, recherchez le [modèle formé], mettre à jour la valeur de *l’uri* dans le nœud *locationInfo* avec l’URI de l’objet blob d’ilearner. L’URI est générée en combinant la *BaseLocation* et le *RelativeLocation* à partir de la sortie de la BES de recyclage d’appel.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importer le JSON en un objet de définition de Service Web

Vous devez utiliser la cmdlet [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) pour convertir le fichier modifié de JSON en un objet de définition de Service Web que vous pouvez utiliser pour mettre à jour de l’expérience predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Mise à jour du service web

Enfin, utilisez l’applet de commande [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) mettre à jour l’expérience prédictive.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -

[1]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
