<properties
    pageTitle="Connecter des machines virtuelles Azure pour journal Analytique | Microsoft Azure"
    description="Pour Windows et Linux machines virtuelles en cours d’exécution dans Azure, il est recommandé de métriques et les fichiers journaux collectés en installant l’extension du journal Analytique Azure VM. Vous pouvez utiliser le portail Azure ou PowerShell pour installer l’extension de la machine virtuelle journal Analytique sur Azure VM."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="richrund"/>

# <a name="connect-azure-virtual-machines-to-log-analytics"></a>Connecter des machines virtuelles Azure pour journal Analytique

Pour les ordinateurs Windows et Linux, la méthode recommandée pour la collecte des journaux et des métriques est en installant l’agent Analytique du journal.

La façon la plus simple pour installer l’agent de journal Analytique sur les machines virtuelles Azure est par le biais de l’Extension de machine virtuelle journal Analytique.  À l’aide de l’extension simplifie le processus d’installation et configure automatiquement l’agent pour envoyer des données à l’espace de travail Analytique de journal que vous spécifiez. L’agent est également mis à niveau automatiquement, s’assurer que vous disposez des fonctionnalités et des correctifs les plus récents.

Pour les machines virtuelles Windows, vous activez l’extension de la machine virtuelle *Microsoft Agent de surveillance* .
Pour les machines virtuelles de Linux, vous activez l’extension de la machine virtuelle *OMS Agent pour Linux* .

En savoir plus sur [les extensions Azure VM](../virtual-machines/virtual-machines-windows-extensions-features.md) et le [Linux agent] (... / virtual-machines/virtual-machines-linux-agent-user-guide.md).

Lorsque vous utilisez la collection basée sur l’agent pour les données de journal, vous devez configurer les [sources de données dans le journal Analytique](log-analytics-data-sources.md) pour spécifier les journaux et les mesures que vous souhaitez collecter.

>[AZURE.IMPORTANT] Si vous configurez des journaux Analytique pour indexer les données journal à l’aide des [diagnostics de Windows Azure](log-analytics-azure-storage.md), et vous configurez l’agent pour collecter les mêmes journaux, les journaux sont collectées deux fois. Vous sont facturés pour les sources de données. Si vous avez l’agent installé, vous devez collecter des données de journal à l’aide de l’agent uniquement - ne configurez pas Analytique des journaux pour collecter les données du journal de diagnostics de Windows Azure.

Il existe trois méthodes pour activer l’extension de la machine virtuelle Analytique du journal :

+ À l’aide du portail Azure
+ À l’aide de PowerShell d’Azure
+ À l’aide d’un modèle de gestionnaire de ressources Azure

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Activer l’extension de la mémoire virtuelle dans le portail Azure

Vous pouvez installer l’agent de journal Analytique et se connecter à l’ordinateur virtuel Azure qui s’exécute sur via le [portail Azure](https://portal.azure.com).

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Pour installer l’agent du journal Analytique et de connecter l’ordinateur virtuel dans un espace de travail Analytique du journal

1.  Connectez-vous au [portail Azure](http://portal.azure.com).
2.  Sélectionnez **Parcourir** sur le côté gauche du portail, puis accédez au **Journal Analytique (OMS)** et sélectionnez-le.
3.  Dans la liste des espaces de travail Analytique du journal, sélectionnez celui que vous souhaitez utiliser avec la machine virtuelle d’Azure.  
    ![Espaces de travail OMS](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4.  Sous **gestion analytique du journal**, sélectionnez les **ordinateurs virtuels**.  
    ![Ordinateurs virtuels](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5.  Dans la liste des **ordinateurs virtuels**, sélectionnez l’ordinateur virtuel sur lequel vous souhaitez installer l’agent. L' **état de la connexion OMS** pour la machine virtuelle indique qu’il n’est **pas connecté**.  
    ![Machine virtuelle non connecté](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6.  Dans les détails de votre machine virtuelle, sélectionnez **se connecter**. L’agent est automatiquement installé et configuré pour votre espace de travail du journal Analytique. Ce processus prend quelques minutes, au cours de laquelle l’état de connexion de l’OMS est la *connexion en cours...*  
    ![Connectez la machine virtuelle](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7.  Après avoir installé et connecter à l’agent, l’état de **connexion de l’OMS** sera mis à jour pour afficher **cet espace de travail**.  
    ![Connecté](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)


## <a name="enable-the-vm-extension-using-powershell"></a>Activer l’extension de la machine virtuelle à l’aide de PowerShell

Il existe des commandes différentes pour des machines virtuelles classiques Azure et les ordinateurs virtuels de gestionnaire de ressources. Voici des exemples de classique et Gestionnaire de ressources des machines virtuelles.

Pour les ordinateurs virtuels classiques, utilisez l’exemple PowerShell suivant :

```
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Pour les ordinateurs virtuels de gestionnaire de ressources, utilisez l’exemple PowerShell suivant :

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```
Lorsque vous configurez votre ordinateur virtuel à l’aide de PowerShell, vous devez fournir **l’ID de l’espace de travail** et de la **Clé primaire**. Dans la page **paramètres** du portail OMS, ou à l’aide de PowerShell comme indiqué dans l’exemple précédent, vous pouvez trouver l’Id et la clé.

![ID de l’espace de travail et de la clé primaire](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

## <a name="deploy-the-vm-extension-using-a-template"></a>Déploiement de l’extension de la machine virtuelle à l’aide d’un modèle

En utilisant le Gestionnaire de ressources Azure, vous pouvez créer un modèle simple (au format JSON) qui définit le déploiement et la configuration de votre application. Ce modèle est appelé un modèle de gestionnaire de ressources et offre un moyen déclaratif pour définir le déploiement. À l’aide d’un modèle, vous pouvez à plusieurs reprises déployer votre application tout au long du cycle de vie d’application et avoir la certitude que vos ressources sont déployées dans un état cohérent.

En incluant l’agent Analytique de journal en tant que partie de votre modèle de gestionnaire de ressources, vous pouvez vous assurer que chaque machine virtuelle est préconfiguré pour signaler à votre espace de travail du journal Analytique.

Pour plus d’informations sur le Gestionnaire de ressources de modèles, consultez [modèles de création d’un gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

Voici un exemple d’un modèle de gestionnaire de ressources qui est utilisé pour déployer un ordinateur virtuel exécutant Windows avec l’extension de l’Agent de surveillance de Microsoft installée. Ce modèle est un modèle typique de la virtualisation, avec les ajouts suivants :

+ paramètres workspaceId et workspaceName
+ Section extension de ressource Microsoft.EnterpriseCloud.Monitoring
+ Sorties à rechercher la workspaceId et le workspaceSharedKey


```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMD workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

Vous pouvez déployer un modèle à l’aide de la commande PowerShell suivante :

```
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-windows-virtual-machines"></a>Résolution des problèmes de Machines virtuelles Windows

Si l’extension de l’agent de *L’Agent de surveillance de Microsoft* VM n’est pas l’installation ou création de rapports, vous pouvez effectuer les étapes suivantes pour résoudre le problème.

1. Vérifiez si l’agent Azure VM est installé et opérationnel correctement selon la procédure décrite dans [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
  + Vous pouvez également consulter le fichier journal de l’agent de machine virtuelle`C:\WindowsAzure\logs\WaAppAgent.log`
  + Si le journal n’existe pas, l’agent de la machine virtuelle n’est pas installé.
    - [Installation de l’Agent de VM d’Azure sur VMs classiques](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md)
2. Confirmer le qu'exécution de la tâche de pulsation extension Microsoft Agent de surveillance à l’aide des étapes suivantes :
  + Se connecter à l’ordinateur virtuel
  + Ouvrez le Planificateur de tâches et recherchez les `update_azureoperationalinsight_agent_heartbeat` tâches
  + Confirmez la tâche est activée et exécute toutes les minutes
  + Archiver le fichier journal de pulsation`C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Consulter les fichiers de journaux d’extension Microsoft Agent VM de surveillance dans`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Assurez-vous que l’ordinateur virtuel peut exécuter des scripts PowerShell
4. Vérifiez les autorisations sur C:\Windows\temp n’ont pas été modifiées
5. Afficher l’état de l’Agent de surveillance de Microsoft en tapant ce qui suit dans une fenêtre PowerShell avec élévation de privilèges sur l’ordinateur virtuel`  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Passer en revue les fichiers de journal d’installation Microsoft Agent de surveillance dans`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Pour plus d’informations, consultez [Dépannage des extensions Windows](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md).

## <a name="troubleshooting-linux-virtual-machines"></a>Résolution des problèmes d’ordinateurs virtuels Linux

Si l’extension de l’agent *Agent OMS pour Linux* VM n’est pas l’installation ou création de rapports, vous pouvez effectuer les étapes suivantes pour résoudre le problème.

1. Si le statut de l’extension est *inconnue* de vérifier si l’agent Azure VM est installé et de travail correctement par révision de fichier journal de l’agent de machine virtuelle`/var/log/waagent.log`
  + Si le journal n’existe pas, l’agent de la machine virtuelle n’est pas installé.
  - [Installer l’Agent Azure VM sur Linux VM](../virtual-machines/virtual-machines-linux-agent-user-guide.md)
2. Pour les autres États défectueux, passez en revue l’Agent OMS pour extension de Linux VM des fichiers journaux `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` et`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Si le statut de l’extension est en bon état, mais les données ne sont pas en cours de téléchargement de l’Agent de l’OMS pour les fichiers journaux de Linux dans revoir`/var/opt/microsoft/omsagent/log/omsagent.log`

Pour plus d’informations, consultez [Dépannage des extensions de Linux](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md).


## <a name="next-steps"></a>Étapes suivantes

+ Configurer les [sources de données dans le journal Analytique](log-analytics-data-sources.md) pour spécifier les journaux et les métriques pour collecter.
+ Pour collecter des données à partir de virtual machines [Analytique de journal ajouter des solutions à partir de la galerie de Solutions](log-analytics-add-solutions.md).
+ [Collecter des données à l’aide des tests de diagnostic Azure](log-analytics-azure-storage.md) pour d’autres ressources qui s’exécutent dans Azure.

Pour les ordinateurs qui ne sont pas dans Azure, vous pouvez installer l’agent du journal Analytique en utilisant les méthodes décrites dans les articles suivants :

+ [Connecter des ordinateurs Windows à Analytique du journal](log-analytics-windows-agents.md)
+ [Connecter les ordinateurs Linux pour journal Analytique](log-analytics-linux-agents.md)
