<properties
    pageTitle="Services Web de formation Azure Machine : Déploiement et la consommation | Microsoft Azure"
    description="Ressources pour le déploiement et la consommation de services web."
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
    ms.date="10/12/2016"
    ms.author="v-donglo"/>

# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Services Web de formation Azure Machine : Déploiement et la consommation

Azure Machine d’apprentissage vous permet de déployer des workflows et des modèles en tant que services web-apprentissage automatique. Ces services web peuvent ensuite servir à appeler les modèles d’apprentissage d’ordinateur à partir d’applications sur Internet pour faire des prévisions en temps réel ou en mode batch. Étant donné que les services web sont RESTful, vous pouvez les appeler à partir de divers langages de programmation et plates-formes, comme .NET et Java et à partir d’applications, comme Excel.

Les sections suivantes fournissent des liens vers des procédures pas à pas, le code et la documentation pour vous aider à démarrer.

## <a name="deploy-a-web-service"></a>Déployer un service web

### <a name="with-azure-machine-learning-studio"></a>Avec apprentissage automatique Azure Studio

Studio de formation de machine et le portail Microsoft Azure Web Machine Learning Services vous aident à déployer et à gérer un service web sans écrire de code.

Les liens suivants fournissent des informations générales sur le déploiement d’un nouveau service web :

* Pour une vue d’ensemble sur la façon de déployer un nouveau service web qui est basé sur le Gestionnaire de ressources Azure, voir [déploiement d’un nouveau service web](machine-learning-webservice-deploy-a-web-service.md).
* Pour une procédure pas à pas sur la façon de déployer un service web, voir [déploiement d’un service web de formation de Machine Azure](machine-learning-publish-a-machine-learning-web-service.md).
* Pour une procédure pas à pas complète sur la façon de créer et de déployer un service web, consultez [procédure étape 1 : créer un espace de travail de formation de Machine](machine-learning-walkthrough-1-create-ml-workspace.md).
* Pour obtenir des exemples spécifiques déploiement un service web, consultez :

    * [Procédure pas à pas, étape 5 : Déployer le service web de formation de Machine Azure](machine-learning-walkthrough-5-publish-web-service.md)
    * [Comment faire pour déployer un service web à plusieurs régions](machine-learning-how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Avec le fournisseur de ressources web services API (API du Gestionnaire de ressources Azure)

Le fournisseur de ressources de formation de Machine Azure pour les services web permet le déploiement et la gestion des services web à l’aide d’appels de l’API REST. Pour plus d’informations, consultez la référence de la [Machine formation Web Service (reste)](https://msdn.microsoft.com/library/azure/mt767538.aspx) sur MSDN.

### <a name="with-powershell-cmdlets"></a>Avec les applets de commande PowerShell

Azure fournisseur de ressources d’apprentissage automatique pour les services web permet le déploiement et la gestion des services web à l’aide des applets de commande PowerShell.

Pour utiliser les applets de commande, vous devez vous connecter à votre compte Azure à partir de l’environnement PowerShell à l’aide de l’applet de commande [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) . Si vous ne connaissez pas l’appel de commandes PowerShell qui reposent sur le Gestionnaire de ressources, reportez-vous [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Pour exporter votre expérience prédictive, utilisez [Cet exemple de code](https://github.com/ritwik20/AzureML-WebServices). Après avoir créé le fichier .exe à partir du code, vous pouvez taper :

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Exécution de l’application crée un modèle JSON de service web. Pour utiliser le modèle pour déployer un service web, vous devez ajouter les informations suivantes :

* Nom de compte de stockage et de la clé

    Vous pouvez obtenir le nom du compte de stockage et de la clé à partir du [portail Azure](https://portal.azure.com/) ou l' [Azure portal classique](http://manage.windowsazure.com/).
* ID de plan d’engagement

    Vous pouvez obtenir l’ID de plan à partir du portail [Azure Machine Learning Web Services](https://services.azureml.net) en vous connectant et en cliquant sur un nom de plan.

Ajouter au modèle de JSON en tant qu’enfants du nœud de *Propriétés* au même niveau que le nœud *MachineLearningWorkspace* .

Voici un exemple :

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consultez les articles suivants et les exemples de code pour plus de détails :

* Référence des [Applets de commande Azure Machine formation]( https://msdn.microsoft.com/library/azure/mt767952.aspx) sur MSDN
* [Procédure pas à pas](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) d’exemple sur GitHub

## <a name="consume-the-web-services"></a>Consommer des services web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>À partir des Services Web Formation Azure Machine (test) l’interface utilisateur

Vous pouvez tester votre service web à partir du portail Azure Machine Learning Web Services. Cela comprend le test du service de requête-réponse (RR) et les interfaces de service d’exécution du lot (BES).

* [Déployer un nouveau service web](machine-learning-webservice-deploy-a-web-service.md)
* [Déploiement d’un service web de formation de Machine Azure](machine-learning-publish-a-machine-learning-web-service.md)
* [Procédure pas à pas, étape 5 : Déployer le service web de formation de Machine Azure](machine-learning-walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>À partir d’Excel

Vous pouvez télécharger un modèle Excel qui utilise le service web :

* [Utilisation d’un service web de formation de Machine Azure à partir d’Excel](machine-learning-consuming-from-excel.md)
* [Macro complémentaire Excel pour Azure Machine Learning Web Services](machine-learning-excel-add-in-for-web-services.md)


### <a name="from-a-rest-based-client"></a>À partir d’un client basée sur REST

Services Web de formation Azure Machine sont API RESTful. Vous pouvez utiliser ces API à partir de différentes plates-formes, .NET, Python, R, Java, etc.. La page de **consommer** pour votre service web sur le [portail Microsoft Azure Machine formation Web Services](https://services.azureml.net) comporte des exemples de code qui peut vous aider à démarrer. Pour plus d’informations, consultez [comment consommer un service web de formation de Machine Azure qui a été déployé à partir d’une Machine d’essai de formation](machine-learning-consume-web-services.md).

