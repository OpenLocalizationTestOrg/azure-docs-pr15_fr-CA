<properties
   pageTitle="Comment créer des NSGs en mode ARM en utilisant un modèle | Microsoft Azure"
   description="Découvrez comment créer et déployer des NSGs de ARM, à l’aide d’un modèle"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="how-to-create-nsgs-using-a-template"></a>Comment faire pour créer NSGs à l’aide d’un modèle

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [créer des NSGs dans le modèle de déploiement classique](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## <a name="nsg-resources-in-a-template-file"></a>Ressources NSG dans un fichier de modèle

Vous pouvez afficher et télécharger le [modèle](https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/NSGs.json).

La section ci-dessous illustre la définition de la partie frontale NSG, en fonction du scénario ci-dessus.

      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('frontEndNSGName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "NSG - Front End"
      },
      "properties": {
        "securityRules": [
          {
            "name": "rdp-rule",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "Internet",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 100,
              "direction": "Inbound"
            }
          },
          {
            "name": "web-rule",
            "properties": {
              "description": "Allow WEB",
              "protocol": "Tcp",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "sourceAddressPrefix": "Internet",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 101,
              "direction": "Inbound"
            }
          }
        ]
      }

Pour associer le NSG au sous-réseau front-end, vous devez modifier la définition de sous-réseau dans le modèle et utilisez l’id de référence pour le NSG.

        "subnets": [
          {
            "name": "[parameters('frontEndSubnetName')]",
            "properties": {
              "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
              }
            }
          }, ...

Notez le même effectué pour le back-end NSG et du sous-réseau back-end dans le modèle.

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Déployer le modèle ARM pour déployer à l’aide de clic

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle à l’aide de clic à déployer, suivez [ce lien](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), cliquez sur **déployer vers Azure**, remplacez les valeurs de paramètre par défaut si nécessaire et suivez les instructions dans le portail.

## <a name="deploy-the-arm-template-by-using-powershell"></a>Déployer le modèle ARM à l’aide de PowerShell

Pour déployer le modèle ARM que vous avez téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Comment faire pour installer et configurer le PowerShell Azure](../powershell-install-configure.md) et suivez les instructions jusqu'à la fin pour signer dans Azure et sélectionnez votre abonnement.

3. Exécuter le **`New-AzureRmResourceGroup`** applet de commande pour créer un groupe de ressources à l’aide du modèle.

        New-AzureRmResourceGroup -Name TestRG -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'

    Sortie attendue :

        ResourceGroupName : TestRG
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        Resources         :
                            Name                Type                                     Location
                            ==================  =======================================  ========
                            sqlAvSet            Microsoft.Compute/availabilitySets       westus  
                            webAvSet            Microsoft.Compute/availabilitySets       westus  
                            SQL1                Microsoft.Compute/virtualMachines        westus  
                            SQL2                Microsoft.Compute/virtualMachines        westus  
                            Web1                Microsoft.Compute/virtualMachines        westus  
                            Web2                Microsoft.Compute/virtualMachines        westus  
                            TestNICSQL1         Microsoft.Network/networkInterfaces      westus  
                            TestNICSQL2         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb1         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb2         Microsoft.Network/networkInterfaces      westus  
                            NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
                            NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
                            TestPIPSQL1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPSQL2         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb2         Microsoft.Network/publicIPAddresses      westus  
                            TestVNet            Microsoft.Network/virtualNetworks        westus  
                            testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
                            testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  

        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Déployer le modèle ARM à l’aide de la CLI d’Azure

Pour déployer le modèle ARM à l’aide de la CLI d’Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé CLI d’Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.
2. Exécuter le **`azure config mode`** commande pour passer en mode de gestionnaire de ressources, comme indiqué ci-dessous.

        azure config mode arm

    Voici la sortie attendue de la commande ci-dessus :

        info:    New mode is arm

4. Exécuter le **`azure group deployment create`** applet de commande pour déployer la nouvelle VNet à l’aide du modèle et le paramètre de fichiers téléchargés et de modifier les propriétés. La liste est indiquée après que la sortie explique les paramètres utilisés.

        azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' -e 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'

    Sortie attendue :

        info:    Executing command group create
        info:    Getting resource group TestRG
        info:    Creating resource group TestRG
        info:    Created resource group TestRG
        info:    Initializing template configurations and parameters
        info:    Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    
        info:    group create command OK

    - **n-(ou--nom)**. Nom du groupe de ressources à créer.
    - **-l (ou--emplacement)**. Région Azure où sera créé le groupe de ressources.
    - **-f (ou--fichier de modèle)**. Chemin d’accès à votre fichier de modèle ARM.
    - **-e (ou--paramètres-fichier)**. Chemin d’accès à votre fichier de paramètres ARM.
