<properties
   pageTitle="À l’aide souhaitée état Configuration avec des jeux d’échelle de Machine virtuelle | Microsoft Azure"
   description="Échelle de l’ordinateur virtuel définit avec l’Extension DSC Azure"
   services="virtual-machine-scale-sets"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machine-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Échelle de l’ordinateur virtuel définit avec l’Extension DSC Azure

[Jeux d’échelle d’ordinateur virtuel (VMSS)](virtual-machine-scale-sets-overview.md) peut être utilisé avec le Gestionnaire d’extension de [Configuration d’état souhaité (Azure DSC)](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md) . VMSS fournit un moyen de déployer et de gérer un grand nombre de machines virtuelles et pouvez ELASTIQUEMENT mise à l’échelle et l’extraction en réponse à charger. DSC est utilisé pour configurer des ordinateurs virtuels en tant qu’ils sont en ligne afin qu’ils exécutent le logiciel de production.

## <a name="differences-between-deploying-to-vm-and-vmss"></a>Différences entre le déploiement de la machine virtuelle et VMSS

La structure du modèle sous-jacent pour VMSS est légèrement différente à partir d’un ordinateur virtuel unique. Plus précisément, un ordinateur virtuel unique déploie des extensions sous le nœud desmachines « virtuelles ». Est une entrée de type « extensions » où DSC est ajouté au modèle

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Un nœud VMSS a une section « Propriétés » avec le « VirtualMachineProfile », « extensionProfile » attribut. DSC est ajoutée sous « extensions »

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-vmss"></a>Problème de VMSS

Le comportement de VMSS est identique à celui d’un ordinateur virtuel unique. Lorsqu’un nouvel ordinateur virtuel est créé, il est automatiquement mis en service avec l’extension DSC. Si une version plus récente de la WMF est requis par l’extension, l’ordinateur virtuel redémarre avant la mise en ligne. Une fois qu’il est en ligne, il télécharge le .zip de configuration DSC et mettre en service sur l’ordinateur virtuel. Vous trouverez plus de détails dans [La vue d’ensemble de Azure DSC Extension](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## <a name="next-steps"></a>Étapes suivantes ##
Examinez le [modèle Azure le Gestionnaire de ressources pour l’extension de DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Découvrez comment l' [extension de DSC gère en toute sécurité les informations d’identification](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md). 

Pour plus d’informations sur le Gestionnaire d’extension DSC d’Azure, consultez [Introduction au gestionnaire d’extension Azure Configuration d’état souhaitée](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). 

Pour plus d’informations sur PowerShell DSC, [visitez le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 


