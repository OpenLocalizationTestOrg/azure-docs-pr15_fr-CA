<properties
   pageTitle="Déployer des machines virtuelles de carte d’interface réseau multiples à l’aide d’un modèle dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à déployer des ordinateurs virtuels de carte d’interface réseau multiples à l’aide d’un modèle dans le Gestionnaire de ressources"
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
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="deploy-multi-nic-vms-using-a-template"></a>Déployer des machines virtuelles de carte d’interface réseau multiples à l’aide d’un modèle

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Dans la mesure où à ce point dans le temps ne peut pas avoir les ordinateurs virtuels avec une seule carte réseau et les ordinateurs virtuels avec plusieurs cartes réseau dans le même groupe de ressources, vous allez implémenter les serveurs principaux dans un groupe de ressources et tous les autres composants dans un autre groupe de sécurité. Les étapes ci-dessous utilisent un groupe de ressource nommé *IaaSStory* pour le groupe de ressources principal et *IaaSStory-back-end* pour les serveurs principaux.

## <a name="prerequisites"></a>Conditions préalables

Avant de déployer les serveurs principaux, vous devez déployer le groupe de ressources principal avec toutes les ressources nécessaires pour ce scénario. Pour déployer ces ressources, suivez les étapes ci-dessous.

1. Accédez à [la page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Dans la page de modèle, à droite du **groupe de ressources Parent**, cliquez sur **déployer pour Azure**.
3. Si nécessaire, modifiez les valeurs de paramètre, puis suivez les étapes dans le portail d’aperçu Azure pour déployer le groupe de ressources.

> [AZURE.IMPORTANT] Assurez-vous que votre nom de compte de stockage est uniques. Impossible d’avoir des noms de compte en double stockage dans Azure.

## <a name="understand-the-deployment-template"></a>Comprendre le modèle de déploiement

Avant de déployer le modèle fourni avec cette documentation, assurez-vous que vous comprenez ce qu’il fait. Les étapes ci-dessous vous donnent une bonne vue d’ensemble du modèle en question.

1. Accédez à [la page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Cliquez sur **azuredeploy.json** pour ouvrir le fichier de modèle.
3. Notez le paramètre *osType* répertorié ci-dessous. Ce paramètre permet de sélectionner quelle image de machine virtuelle à utiliser pour le serveur de base de données, ainsi que de plusieurs systèmes d’exploitation les paramètres associés.

        "osType": {
          "type": "string",
          "defaultValue": "Windows",
          "allowedValues": [
            "Windows",
            "Ubuntu"
          ],
          "metadata": {
            "description": "Type of OS to use for VMs: Windows or Ubuntu."
          }
        },

4. Faites défiler la liste des variables et vérifier la définition pour les variables **dbVMSetting** , répertoriés ci-dessous. Il reçoit un des éléments du tableau contenus dans la variable **dbVMSettings** . Si vous êtes familiarisé avec la terminologie du développement logiciel, vous pouvez afficher la variable **dbVMSettings** comme une table de hachage ou un ANSI.

        "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Supposons que vous décidez de déployer des ordinateurs virtuels de Windows en cours d’exécution SQL dans le back-end. La valeur de **osType** sera alors *Windows*, et la variable **dbVMSetting** contient l’élément ci-dessous, qui représente la première valeur de la variable **dbVMSettings** .

          "Windows": {
            "vmSize": "Standard_DS3",
            "publisher": "MicrosoftSQLServer",
            "offer": "SQL2014SP1-WS2012R2",
            "sku": "Standard",
            "version": "latest",
            "vmName": "DB",
            "osdisk": "osdiskdb",
            "datadisk": "datadiskdb",
            "nicName": "NICDB",
            "ipAddress": "192.168.2.",
            "extensionDeployment": "",
            "avsetName": "ASDB",
            "remotePort": 3389,
            "dbPort": 1433
          },

6. Notez que la **vmSize** contient la valeur *Standard_DS3*. Uniquement certaines tailles de machine virtuelle permettant l’utilisation de plusieurs cartes réseau. Vous pouvez vérifier les tailles VM sont multi carte réseau activée en consultant la [vue d’ensemble de la carte d’interface réseau multiples](virtual-networks-multiple-nics.md).
7. Accédez à des **ressources** et notez le premier élément. Il décrit un compte de stockage. Ce compte de stockage permet de conserver les disques de données utilisés par chaque ordinateur virtuel de la base de données. Dans ce scénario, chaque machine virtuelle de la base de données possède un disque du système d’exploitation enregistré dans un stockage standard et deux disques de données stockées dans le stockage SSD (premium).

        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('prmStorageName')]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "Storage Account - Premium"
          },
          "properties": {
            "accountType": "[parameters('prmStorageType')]"
          }
        },

8. Descendez jusqu'à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente la carte réseau utilisée pour l’accès de base de données dans chaque machine virtuelle de la base de données. Notez l’utilisation de la fonction de **copie** de cette ressource. Le modèle permet de déployer autant de machines virtuelles que vous le souhaitez, en fonction du paramètre **dbCount** . Par conséquent, vous devez créer le même montant de cartes réseau pour un accès de base de données, un pour chaque ordinateur virtuel.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB DA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

9. Descendez jusqu'à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente la carte réseau utilisée pour la gestion de chaque machine virtuelle de la base de données. Une fois de plus, vous devez une de ces cartes d’interface réseau pour chaque machine virtuelle de la base de données. Notez l’élément **networkSecurityGroup** , lier un NSG qui permet d’accéder à RDP/SSH pour cette carte réseau uniquement.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB RA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
                  },
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

10. Descendez jusqu'à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente une disponibilité définie pour être partagés par tous les ordinateurs virtuels de base de données. De cette façon, vous garantissez qu’il y aura toujours un ordinateur virtuel dans le jeu en cours d’exécution lors de la maintenance.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/availabilitySets",
          "name": "[variables('dbVMSetting').avsetName]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "AvailabilitySet - DB"
          }
        },

11. Faites défiler jusqu'à la ressource suivante. Cette ressource représente la base de données de machines virtuelles, comme dans le premier quelques lignes répertoriées ci-dessous. Notez l’utilisation de la fonction de **copie** , veiller à ce que plusieurs ordinateurs virtuels sont créés sur le paramètre **dbCount** . Notez également la collection **dependsOn** . Il répertorie deux cartes réseau sont nécessaires pour être créés avant que l’ordinateur virtuel est déployé, ainsi que l’ensemble de la disponibilité et du compte de stockage.

          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
          "location": "[variables('location')]",
          "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
            "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
            "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
          ],
          "tags": {
            "displayName": "VMs - DB"
          },
          "copy": {
            "name": "dbvmcount",
            "count": "[parameters('dbCount')]"
          },

12. Défiler vers le bas dans la ressource d’ordinateur virtuel à l’élément **networkProfile** , comme indiqué ci-dessous. Notez qu’il y a deux cartes d’interface réseau qui est la référence pour chaque machine virtuelle. Lorsque vous créez plusieurs cartes réseau d’un ordinateur virtuel, vous devez définir la propriété **primary** de l’une des cartes réseau *, et le reste de la *valeur False**.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Déployer le modèle ARM pour déployer à l’aide de clic

> [AZURE.IMPORTANT] Assurez-vous de que suivre les étapes [préalables](#Pre-requisites) avant de suivre les instructions ci-dessous.

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle à l’aide de clic à déployer, suivez [ce lien](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), à droite du **groupe de ressources de serveur principal (voir la documentation)** cliquez sur **déployer sur Azure**, remplacez les valeurs de paramètre par défaut, si nécessaire et suivez les instructions dans le portail.

La figure ci-dessous illustre le contenu du nouveau groupe de ressources, après le déploiement.

![Groupe de ressources back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Déployer le modèle à l’aide de PowerShell

Pour déployer le modèle que vous avez téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Exécuter le **`New-AzureRmResourceGroup`** applet de commande pour créer un groupe de ressources à l’aide du modèle.

        New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'

    Sortie attendue :

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        Resources         :
                            Name                 Type                                 Location
                            ===================  ===================================  ========
                            ASDB                 Microsoft.Compute/availabilitySets   westus  
                            DB1                  Microsoft.Compute/virtualMachines    westus  
                            DB2                  Microsoft.Compute/virtualMachines    westus  
                            NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                            wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  

        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Déployer le modèle à l’aide de la CLI d’Azure

Pour déployer le modèle à l’aide de la CLI d’Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé CLI d’Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.
2. Exécuter le **`azure config mode`** commande pour passer en mode de gestionnaire de ressources, comme indiqué ci-dessous.

        azure config mode arm

    Voici la sortie attendue de la commande ci-dessus :

        info:    New mode is arm

3. Ouvrez le [fichier de paramètre](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), sélectionnez son contenu et enregistrer dans un fichier sur votre ordinateur. Pour cet exemple, nous avons enregistré le fichier de paramètres à *parameters.json*.

4. Exécuter le **`azure group deployment create`** applet de commande pour déployer la nouvelle VNet à l’aide du modèle et le paramètre de fichiers téléchargés et de modifier les propriétés. La liste est indiquée après que la sortie explique les paramètres utilisés.

        azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

    Sortie attendue :

        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
