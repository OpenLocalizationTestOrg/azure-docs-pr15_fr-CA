<properties
   pageTitle="Déployer un ordinateur virtuel avec une adresse IP statique publique à l’aide d’un modèle dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à déployer des ordinateurs virtuels avec une adresse IP statique publique à l’aide d’un modèle dans le Gestionnaire de ressources"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-a-template"></a>Déployer un ordinateur virtuel avec une adresse IP statique publique à l’aide d’un modèle

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-resources-in-a-template-file"></a>Ressources IP publiques dans un fichier de modèle

Vous pouvez afficher et télécharger le [modèle](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

La section ci-dessous illustre la définition de la ressource IP public, en fonction du scénario ci-dessus.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Notez la propriété **publicIPAllocationMethod** , qui est la valeur *statique*. Cette propriété peut être soit *statique*ou *dynamique* (valeur par défaut). La valeur statique garantit que l’adresse IP publique affectée ne changera jamais.

La section ci-dessous affiche l’association de l’adresse IP publique avec une interface réseau.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Notez la propriété **publicIPAddress** en pointant sur l' **Id** d’une ressource nommée **variables('webVMSetting').pipName**. Qui est le nom de la ressource IP public ci-dessus.

Enfin, l’interface réseau ci-dessus est répertorié dans la propriété **networkProfile** de la machine virtuelle en cours de création.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Déployer le modèle à l’aide de cliquez sur déployer

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle à l’aide de clic pour déployer, cliquez sur **déployer vers Azure** dans le fichier Readme.md pour le modèle [d’ordinateur virtuel avec PIP statique](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) . Remplacer les valeurs de paramètre par défaut si vous le souhaitez, puis entrez des valeurs pour les paramètres vides.  Suivez les instructions dans le portail pour créer un ordinateur virtuel avec une adresse IP publique.

## <a name="deploy-the-template-by-using-powershell"></a>Déployer le modèle à l’aide de PowerShell

Pour déployer le modèle que vous avez téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Comment faire pour installer et configurer le PowerShell Azure](../powershell-install-configure.md) et suivez les instructions des étapes 1 à 3.

2. Dans une console PowerShell, exécutez l’applet de commande **New-AzureRmResourceGroup** pour créer un nouveau groupe de ressources, si nécessaire. Si vous avez déjà créé un groupe de ressources, passez à l’étape 3.

        New-AzureRmResourceGroup -Name PIPTEST -Location westus

    Sortie attendue :

        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. Dans une console PowerShell, exécutez l’applet de commande **New-AzureRmResourceGroupDeployment** pour déployer le modèle.

        New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
            -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

    Sortie attendue :

        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : <Deployment date> <Deployment time>
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0

        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         

        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Déployer le modèle à l’aide de la CLI d’Azure

Pour déployer le modèle à l’aide de la CLI d’Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé CLI d’Azure, suivez les étapes décrites dans l’article [d’installer et de configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md) , puis les étapes de connexion de l’interface CLI à votre abonnement dans la section « Utiliser la connexion azure à s’authentifier de manière interactive » de cet article de [se connecter à un abonnement Azure à partir de l’Interface de ligne de commande Azure (Azure CLI)](../xplat-cli-connect.md) .
2. Exécutez la commande **mode config azure** pour passer en mode de gestionnaire de ressources, comme indiqué ci-dessous.

        azure config mode arm

    Voici la sortie attendue de la commande ci-dessus :

        info:    New mode is arm

3. Ouvrez le [fichier de paramètre](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), sélectionnez son contenu et enregistrer dans un fichier sur votre ordinateur. Pour cet exemple, les paramètres sont enregistrés dans un fichier nommé *parameters.json*. Modifier les valeurs de paramètre dans le fichier si vous le souhaitez, mais au minimum, il est recommandé de modifier la valeur pour le paramètre adminPassword à un unique mot de passe complexe.

4. Exécutez l’applet de commande **groupe azure déploiement crée** pour déployer le nouveau VNet en utilisant les fichiers de modèle et de paramètres vous avez téléchargé et modifier les propriétés. Dans la commande ci-dessous, remplacez <path> avec le chemin d’accès, vous avez enregistré le fichier. 

        azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json

    Sortie attendue (listes de valeurs des paramètres utilisées) :

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
