<properties
    pageTitle="Former un nouveau service web à l’aide des applets de commande PowerShell de gestion d’apprentissage Machine | Microsoft Azure"
    description="Découvrez comment former un modèle et mettre à jour le service web pour utiliser le modèle nouvellement formé dans la formation de Machine Azure à l’aide des applets de commande PowerShell de gestion d’apprentissage Machine par programme."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Former un nouveau service web à l’aide des applets de commande PowerShell de gestion d’apprentissage Machine

Vous dispenser un service web, vous mettez à jour la définition de service web prédictive pour faire référence au nouveau modèle formé.  

## <a name="prerequisites"></a>Conditions préalables

Vous devez avoir configuré une expérience de formation et une expérience prédictive comme indiqué dans [modèles de dispenser une formation de Machine par programme](machine-learning-retrain-models-programmatically.md). 

>[AZURE.IMPORTANT] L’expérience de prévision doit être déployé comme un ordinateur Azure (nouveau) sur le Gestionnaire de ressources de formation service web. 
 
Pour plus d’informations sur les services web de déploiement, voir [déploiement d’un service web de formation de Machine Azure](machine-learning-publish-a-machine-learning-web-service.md).

Ce processus nécessite que vous avez installé les applets de commande formation de Machine Azure. Pour installer les applets de commande d’apprentissage automatique d’informations, consultez la référence des [Applets de commande Azure Machine formation](https://msdn.microsoft.com/library/azure/mt767952.aspx) sur MSDN.

À partir de la sortie de reconversion a copié les informations suivantes :

* BaseLocation
* RelativeLocation

Les étapes sont les suivantes :

1.  Connectez-vous à votre compte Azure le Gestionnaire de ressources.
2.  Obtenir la définition de service web
3.  Exporter la définition du Service Web en tant que JSON
4.  Mettre à jour la référence à l’objet blob d’ilearner dans le JSON.
5.  Importer le JSON dans une définition de Service Web
6.  Mise à jour du service web avec la nouvelle définition de Service Web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Connectez-vous à votre compte Azure le Gestionnaire de ressources

Vous devez vous connecter à votre compte Azure à partir de l’environnement PowerShell à l’aide de l’applet de commande [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition"></a>Obtenir la définition de Service Web

Ensuite, obtenez le Service Web en appelant l’applet de commande [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . La définition du Service Web est une représentation interne du modèle qualifié du service web et n’est pas directement modifiable. Vérifiez que vous récupérez la définition du Service Web pour votre expérience prédictive et pas votre expérience de formation.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Pour déterminer le nom du groupe de ressources d’un service web existant, exécutez l’applet de commande Get-AzureRmMlWebService sans paramètres pour afficher les services web dans votre abonnement. Recherchez le service web et observez son ID de service web. Le nom du groupe de ressources est le quatrième élément dans le code, juste après l’élément *resourceGroups* . Dans l’exemple suivant, le nom de groupe de ressources est par défaut-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Également, pour déterminer le nom du groupe de ressources d’un service web existant, ouvrez une session sur le portail Microsoft Azure Machine Learning Web Services. Sélectionnez le service web. Le nom de groupe de ressources est le cinquième élément de l’URL du service web, juste après l’élément *resourceGroups* . Dans l’exemple suivant, le nom de groupe de ressources est par défaut-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exporter la définition du Service Web en tant que JSON

Pour modifier la définition pour le modèle formé à utiliser le nouvellement formé le modèle, vous devez d’abord utiliser l’applet de commande [Exportation-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) pour l’exporter dans un fichier de format JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Mettre à jour la référence à l’objet blob d’ilearner dans le JSON.

Dans les ressources, recherchez le [modèle formé], mettre à jour la valeur de *l’uri* dans le nœud *locationInfo* avec l’URI de l’objet blob d’ilearner. L’URI est générée en combinant la *BaseLocation* et le *RelativeLocation* à partir de la sortie de la BES de recyclage d’appel.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>Importer le JSON dans une définition de Service Web

Vous devez utiliser la cmdlet [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) pour convertir le fichier modifié de JSON dans une définition de Service Web que vous pouvez utiliser pour mettre à jour de l’expérience Predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Mise à jour du service web avec la nouvelle définition de Service Web

Enfin, vous utilisez applet de commande [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) mettre à jour l’expérience prédictive.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Résumé

À l’aide des applets de commande PowerShell de formation Machine gestion, vous pouvez mettre à jour le modèle formé d’un Service Web prédictive, ce qui permet des scénarios tels que :

* Modèle périodique de recyclage avec de nouvelles données.
* Distribution d’un modèle à des clients dans le but de leur permettant de dispenser le modèle à l’aide de leurs propres données.
