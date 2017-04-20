<properties 
    pageTitle="Déployer une application web qui est liée à un référentiel de GitHub" 
    description="Un modèle du Gestionnaire de ressources Azure permet de déployer une application web qui contient un projet à partir d’un référentiel de GitHub." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2016" 
    ms.author="cephalin"/>

# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>Déployer une application web liée à un référentiel de GitHub

Dans cette rubrique, vous apprendrez comment créer un modèle de gestionnaire de ressources Azure qui déploie une application web qui est liée à un projet dans un référentiel de GitHub. Vous apprendrez comment définir les ressources qui sont déployés et comment définir les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements ou personnaliser pour répondre à vos besoins.

Pour plus d’informations sur la création de modèles, consultez [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

Pour le modèle complet, voir [Application Web lié au modèle de GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-deploy"></a>Ce que vous allez déployer

Avec ce modèle, vous allez déployer une application web qui contient le code d’un projet dans GitHub.

Pour exécuter le déploiement automatique, cliquez sur le bouton suivant :

[![Déployer vers Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL

URL de référentiel de GitHub qui contient le projet à déployer. Ce paramètre contient une valeur par défaut, mais cette valeur est uniquement destinée à vous montrer comment fournir l’URL de référentiel. Vous pouvez utiliser cette valeur lorsque vous testez le modèle, mais vous devez fournir l’URL à votre propre référentiel lorsque vous travaillez avec le modèle.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>branche

La branche du référentiel à utiliser lors du déploiement de l’application. La valeur par défaut est master, mais vous pouvez fournir le nom de n’importe quelle branche dans le référentiel que vous souhaitez déployer.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## <a name="resources-to-deploy"></a>Ressources pour le déploiement

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Application Web

Crée l’application web qui est liée au projet dans GitHub. 

Vous spécifiez le nom de l’application web via le paramètre **siteName** et l’emplacement de l’application web par le biais du paramètre **siteLocation** . Dans l’élément **dependsOn** , le modèle définit l’application web en tant que dépendant du service d’hébergement de plan. Car elle est tributaire du plan d’hébergement, l’application web n’est pas créée tant que le plan de l’hébergement a terminé en cours de création. L’élément **dependsOn** est uniquement utilisé pour spécifier l’ordre de déploiement. Si vous ne marquez pas l’application web en tant que dépendant sur le plan de l’hébergement, Gestionnaire de ressources Azure va tenter de créer les ressources en même temps, et vous pouvez recevoir une erreur si l’application web est créée avant le programme d’hébergement.

L’application web dispose également d’une ressource de l’enfant qui est définie dans la section **ressources** ci-dessous. Cette ressource enfant définit le contrôle de code source pour le projet déployé avec l’application web. Dans ce modèle, le contrôle de code source est lié à un référentiel particulier de GitHub. Le référentiel de GitHub est défini avec le code **« RepoUrl » : « https://github.com/davidebbo-test/Mvc52Application.git »** vous pouvez coder en dur l’URL de référentiel lorsque vous souhaitez créer un modèle à plusieurs reprises déploie un projet unique tout en nécessitant le nombre minimal de paramètres.
Au lieu de coder en dur l’URL de référentiel, vous pouvez ajouter un paramètre de l’URL de référentiel et utilisez cette valeur pour la propriété **RepoUrl** .

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Commandes à exécuter de déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 
