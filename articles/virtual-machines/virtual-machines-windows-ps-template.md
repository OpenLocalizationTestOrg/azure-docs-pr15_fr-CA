<properties
    pageTitle="Créer un ordinateur virtuel avec un modèle de gestionnaire de ressources | Microsoft Azure"
    description="Permet de créer facilement une machine virtuelle Windows, utilisez un modèle du Gestionnaire de ressources et PowerShell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Créer une machine virtuelle Windows avec un modèle de gestionnaire de ressources

Cet article présente un modèle de gestionnaire de ressources Azure et vous indique comment utiliser PowerShell pour déployer. Le modèle déploie une seule machine virtuelle exécutant Windows Server dans un nouveau réseau virtuel avec un sous-réseau unique.

Il doit prendre environ 20 minutes pour effectuer les étapes décrites dans cet article.

> [AZURE.IMPORTANT] Si vous souhaitez que votre ordinateur virtuel faisant partie d’un ensemble de disponibilité, l’ajouter à l’ensemble lorsque vous créez la machine virtuelle. Il n’existe actuellement pas d’une façon d’ajouter un ordinateur virtuel pour une disponibilité après qu’il a été créé.

## <a name="step-1-create-the-template-file"></a>Étape 1 : Créer le fichier de modèle

Vous pouvez créer votre propre modèle à l’aide des informations figurant dans les [modèles de la création d’un gestionnaire de ressources Azure](../resource-group-authoring-templates.md). Vous pouvez également déployer des modèles qui ont été créées pour vous à partir de [Modèles de Démarrages rapides d’Azure](https://azure.microsoft.com/documentation/templates/).

1. Ouvrez votre éditeur de texte et ajoutez l’élément de schéma requis et l’élément contentVersion requis :

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [Les paramètres](../resource-group-authoring-templates.md#parameters) ne sont pas toujours nécessaires, mais ils fournissent un moyen de valeurs d’entrée lorsque le modèle est déployé. Après l’élément contentVersion, ajoutez l’élément parameters et ses éléments enfants :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [Variables](../resource-group-authoring-templates.md#variables) peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créés à partir d’une combinaison de valeurs de paramètre. Ajoutez l’élément de variables après la section de paramètres :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
        
4. [Ressources](../resource-group-authoring-templates.md#resources) comme la machine virtuelle, le réseau virtuel et du compte de stockage sont définies suivant dans le modèle. Ajoutez la section de ressources après la section variables :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvn1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
          
    >[AZURE.NOTE] Cet article crée un ordinateur virtuel exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, voir [Naviguer et sélectionnez machine virtuelle Azure des images avec Windows PowerShell et de l’infrastructure du langage commun Azure](virtual-machines-linux-cli-ps-findimage.md).  
            
2. Enregistrez le fichier de modèle en tant que *VirtualMachineTemplate.json*.

## <a name="step-2-create-the-parameters-file"></a>Étape 2 : Créer le fichier de paramètres

Pour spécifier des valeurs pour les paramètres de ressource qui ont été définis dans le modèle, créez un fichier de paramètres qui contient les valeurs qui sont utilisées lorsque le modèle est déployé.

1. Dans l’éditeur de texte, copiez ce contenu JSON dans un nouveau fichier appelé *Parameters.json*:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] Reportez-vous à la section plus d’informations sur [les exigences de nom d’utilisateur et le mot de passe](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

2. Enregistrez le fichier de paramètres.

## <a name="step-3-install-azure-powershell"></a>Étape 3 : Installer PowerShell Azure

Pour plus d’informations sur l’installation de la version la plus récente de PowerShell d’Azure, en sélectionnant votre abonnement et votre compte d’ouverture de session, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="step-4-create-a-resource-group"></a>Étape 4 : Créer un groupe de ressources

Toutes les ressources doivent être déployés dans un [groupe de ressources](../azure-resource-manager/resource-group-overview.md).

1. Obtenir la liste des emplacements disponibles dans laquelle les ressources peuvent être créés.

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. Remplacez la valeur de **$locName** avec un emplacement dans la liste, par exemple **US Central**. Créez la variable.

        $locName = "location name"
        
3. Remplacez la valeur de **$rgName** par le nom du nouveau groupe de ressources. Créez la variable et le groupe de ressources.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Vous devriez voir quelque chose comme dans l’exemple :
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5-create-the-resources-with-the-template-and-parameters"></a>Étape 5 : Créer les ressources avec le modèle et les paramètres

Remplacez la valeur de **$templateFile** avec le chemin d’accès et le nom du fichier de modèle. Remplacez la valeur de **$parameterFile** avec le chemin d’accès et le nom du fichier de paramètres. Créer les variables, puis déployez le modèle. 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] Vous pouvez également déployer des modèles et des paramètres à partir d’un compte de stockage Azure. Pour plus d’informations, reportez-vous [à l’aide de PowerShell Azure avec stockage Azure](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Étapes suivantes

- S’il y a des problèmes avec le déploiement, une étape suivante serait d’examiner les [déploiements de groupe de ressources de résolution des problèmes avec Azure portal](../resource-manager-troubleshoot-deployments-portal.md)
- Apprenez à gérer la machine virtuelle que vous avez créé en passant en revue les [machines virtuelles de gérer à l’aide du Gestionnaire de ressources Azure et PowerShell](virtual-machines-windows-ps-manage.md).
