<properties
    pageTitle="Déployer une application web à l’aide de MSDeploy avec les certificats ssl et de nom d’hôte"
    description="Un modèle de gestionnaire de ressources Azure permet de déployer une application web à l’aide de MSDeploy et configurer un certificat SSL et de nom d’hôte personnalisé"
    services="app-service\web"
    manager="erikre"
    documentationCenter=""
    authors="jodehavi"
    />

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="john.dehavilland"/>

# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>Déployer une application web avec MSDeploy, nom d’hôte personnalisé et un certificat SSL

Ce guide passe en revue la création d’un déploiement de bout en bout pour une application Web de Azure, exploitant MSDeploy ainsi que l’ajout d’un nom d’hôte personnalisé et d’un certificat SSL au modèle ARM.

Pour plus d’informations sur la création de modèles, consultez [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

###<a name="create-sample-application"></a>Créer l’exemple d’Application

Vous déployez une application web ASP.NET. La première étape consiste à créer une application web simple (ou vous pouvez choisir d’utiliser un, auquel cas vous pouvez ignorer cette étape).

Ouvrez Visual Studio 2015 et choisissez Fichier > Nouveau projet. Dans la boîte de dialogue qui s’affiche, choisissez Web > ASP.NET Web Application. Sous modèles, choisissez Web et choisissez le modèle MVC. Sélectionnez _Modifier le type d’authentification_ à _Aucune authentification_. Il s’agit simplement pour créer l’exemple d’application aussi simples que possible.

À ce stade, vous aurez une prêt à l’emploi dans le cadre de votre processus de déploiement ASP.Net web application base.

###<a name="create-msdeploy-package"></a>Créer le package MSDeploy

Étape suivante consiste à créer le package pour déployer l’application web vers Azure. Pour ce faire, enregistrez votre projet et puis exécutez la procédure suivante à partir de la ligne de commande :

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Cette opération crée un package compressé sous le dossier PackageLocation. L’application est maintenant prête à être déployée, que vous pouvez à présent générer un modèle Azure le Gestionnaire de ressources pour ce faire.

###<a name="create-arm-template"></a>Créer le modèle ARM
Tout d’abord, nous allons commencer avec un modèle ARM de base qui permet de créer une application web et un hébergement (Notez que les paramètres et les variables ne figurent pas par souci de concision).

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

Ensuite, vous devez modifier la ressource de l’application web pour mettre une ressource MSDeploy imbriquée. Cela vous permettra de référence le package créé précédemment et indique le Gestionnaire de ressources Azure MSDeploy permet de déployer le package sur le WebApp Azure. Vous trouverez ci-dessous la ressource Microsoft.Web/sites avec la ressource MSDeploy imbriquée :

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Maintenant, vous remarquerez que la ressource MSDeploy utilise une propriété **packageUri** qui est définie comme suit :

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Cette **packageUri** prend l’uri du compte de stockage qui pointe sur le compte de stockage dans lequel vous allez télécharger votre zip package pour. Le Gestionnaire de ressources Azure exploiteront les [Signatures de l’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) pour extraire le package vers le bas localement à partir du compte de stockage lorsque vous déployez le modèle. Ce processus sera automatisé via un PowerShell script qui télécharge le package, puis appeler l’API de gestion Azure pour créer les clés requises et transfèrent ces informations dans le modèle en tant que paramètres (*_artifactsLocation* et *_artifactsLocationSasToken*). Vous devez définir des paramètres pour le dossier et le nom de fichier du package est téléchargé vers sous le conteneur de stockage.

Vous devez ensuite ajouter dans une autre ressource imbriquée pour configurer les liaisons de nom d’hôte afin d’exploiter un domaine personnalisé. Vous devez d’abord vous assurer que vous possédez le nom d’hôte et configurer pour qu’il soit vérifiée par Azure que vous possédez, consultez [configurer un nom de domaine personnalisé dans le Service d’application Azure](web-sites-custom-domain-name.md). Une fois cette opération effectuée, vous pouvez ajouter les éléments suivants à votre modèle dans la section de ressources de Microsoft.Web/sites :

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

Enfin, vous devez ajouter une autre ressource au niveau supérieure, Microsoft.Web/certificates. Cette ressource contient votre certificat SSL et se trouvent au même niveau que votre application web et le plan de l’hébergement.

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

Vous devez avoir un certificat SSL valide pour définir cette ressource. Une fois que vous avez ce certificat valide vous devez extraire les octets pfx sous la forme d’une chaîne en base 64. Une option pour extraire ce consiste à utiliser la commande PowerShell suivante :

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

Vous pourriez ensuite le passer comme paramètre à votre modèle de déploiement ARM.

À ce stade, le modèle ARM est prêt.

###<a name="deploy-template"></a>Déployer le modèle

Les dernières étapes sont à assembler le tout dans un déploiement complet de bout en bout. Pour faciliter le déploiement que vous pouvez utiliser le script de **Déploiement-AzureResourceGroup.ps1** PowerShell est ajouté lorsque vous créez un projet du groupe de ressources Azure dans Visual Studio à l’aide sur le téléchargement de tous les artefacts requises dans le modèle. Elle exige que vous avez créé un compte de stockage que vous souhaitez utiliser à l’avance. Pour cet exemple, j’ai créé un compte de stockage partagé pour le package.zip à télécharger. Le script exploiteront AzCopy pour télécharger le package pour le compte de stockage. Vous transmettez dans votre emplacement de dossier artefact et le script télécharge automatiquement tous les fichiers dans le répertoire sur le conteneur de stockage nommé. Vous devez ensuite mettre à jour les liaisons SSL pour mapper le nom d’hôte personnalisé avec votre certificat SSL après l’appel à AzureResourceGroup.ps1 à la déployer.

PowerShell suivante montre le déploiement complet appelant la déployer-AzureResourceGroup.ps1 :

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

À ce stade votre application doit être déployée et vous devriez pouvoir accéder via https://www.yourcustomdomain.com
