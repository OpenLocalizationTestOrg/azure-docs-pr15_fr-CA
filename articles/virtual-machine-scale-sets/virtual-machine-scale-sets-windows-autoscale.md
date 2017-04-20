<properties
    pageTitle="Échelle de Machine virtuelle AutoScale Windows définit | Microsoft Azure"
    description="Configurer autoscaling pour échelle d’ordinateur virtuel Windows défini à l’aide de PowerShell d’Azure"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>Redimensionner automatiquement les ordinateurs dans un jeu d’échelle de machine virtuelle

Jeux d’échelle de machine virtuelle rendent facile à déployer et à gérer les machines virtuelles identiques sous la forme d’un ensemble. Jeux d’échelle fournissent une couche de calcul hautement évolutives et personnalisables pour les applications de hyperscale, et ils prennent en charge les images de la plate-forme Windows, les images de plate-forme Linux, des images personnalisées et les extensions. Pour plus d’informations sur les jeux de l’échelle, reportez-vous à la section [Échelle de Machine virtuelle définit](virtual-machine-scale-sets-overview.md).

Ce didacticiel vous montre comment créer un jeu d’échelle de machines virtuelles Windows et redimensionner automatiquement les ordinateurs dans le jeu. Vous créez l’échelle définie et de mise à l’échelle en créant un modèle de Azure le Gestionnaire de ressources et de déploiement à l’aide de PowerShell d’Azure. Pour plus d’informations sur les modèles, consultez [modèles de création d’un gestionnaire de ressources Azure](../resource-group-authoring-templates.md). Pour plus d’informations sur la mise à l’échelle automatique de jeux d’échelle, reportez-vous à la section [mise à l’échelle automatique et l’échelle de la Machine virtuelle définit](virtual-machine-scale-sets-autoscale-overview.md).

Dans cet article, vous déployez les ressources et les extensions suivantes :

- Microsoft.Storage/storageAccounts
- Microsoft.Network/virtualNetworks
- Microsoft.Network/publicIPAddresses
- Microsoft.Network/loadBalancers
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachines
- Microsoft.Compute/virtualMachineScaleSets
- Microsoft.Insights.VMDiagnosticsSettings
- Microsoft.Insights/autoscaleSettings

Pour plus d’informations sur les ressources du Gestionnaire de ressources, consultez [Azure Compute, réseau et les fournisseurs de stockage dans le Gestionnaire de ressources Azure](../virtual-machines/virtual-machines-windows-compare-deployment-models.md).

## <a name="step-1-install-azure-powershell"></a>Étape 1 : Installer PowerShell Azure

Pour plus d’informations sur l’installation de la version la plus récente de PowerShell d’Azure, en sélectionnant votre abonnement et l’ouverture de session Azure, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>Étape 2 : Créer un groupe de ressources et d’un compte de stockage

1. **Créer un groupe de ressources** – toutes les ressources doivent être déployées sur un groupe de ressources. [Nouveau-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx) permet de créer un groupe de ressources nommé **vmsstestrg1**.

2. **Créer un compte de stockage** – ce compte de stockage est où se trouve le modèle. [Nouveau-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) permet de créer un compte de stockage nommé **vmsstestsa**.

## <a name="step-3-create-the-template"></a>Étape 3 : Créez le modèle
Un modèle de gestionnaire de ressources Azure permet de vous permettant de déployer et de gérer des ressources Azure ensemble à l’aide d’une description de JSON des ressources et des paramètres de déploiement associé.

1. Dans votre éditeur favori, créer le fichier C:\VMSSTemplate.json et ajoutez la structure initiale de JSON pour le modèle de prise en charge.

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
          ]
        }

2. Les paramètres ne sont pas toujours nécessaires, mais ils fournissent un moyen de valeurs d’entrée lorsque le modèle est déployé. Ajouter ces paramètres sous l’élément parent de paramètres que vous avez ajoutée au modèle.

        "vmName": { "type": "string" },
        "vmSSName": { "type": "string" },
        "instanceCount": { "type": "string" },
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" },
        "resourcePrefix": { "type": "string" }

    - Nom de l’ordinateur virtuel séparé qui est utilisé pour accéder aux ordinateurs de l’échelle de la valeur.
    - Le nom du compte de stockage dans lequel le modèle est stocké.
    - Le nombre de machines virtuelles pour créer initialement dans l’échelle de la valeur.
    - Le nom et le mot de passe du compte d’administrateur sur les ordinateurs virtuels.
    - Un préfixe de nom pour les ressources qui sont créés pour prendre en charge de l’ensemble de l’échelle.

3. Variables peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créés à partir d’une combinaison de valeurs de paramètre. Ajoutez ces variables dans l’élément parent de variables que vous avez ajoutée au modèle.

        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
          "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

  - Noms DNS qui sont utilisés par les interfaces réseau.
    - Les noms d’adresses IP et les préfixes de réseau virtuel et sous-réseaux.
    - Les noms et identificateurs de réseau virtuel, charger équilibreur et les interfaces réseau.
    - Définir des noms de compte de stockage pour les comptes associés avec les ordinateurs de l’échelle.
    - Paramètres de l’extension de Diagnostics qui est installé sur les ordinateurs virtuels. Pour plus d’informations sur l’extension des tests de diagnostic, consultez [créer un ordinateur virtuel Windows avec la surveillance et de diagnostic à l’aide du modèle de gestionnaire de ressources Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

4. Ajoutez la ressource de compte de stockage sous l’élément parent de ressources que vous avez ajoutée au modèle. Ce modèle utilise une boucle pour créer les cinq comptes de stockage recommandée où sont stockées les disques du système d’exploitation et les données de diagnostic. Cet ensemble de comptes peut prendre en charge des ordinateurs virtuels jusqu'à 100 dans un jeu d’échelle, ce qui est le maximum en cours. Chaque compte de stockage nommé avec un indicateur de lettre a été défini dans les variables combinées avec le préfixe que vous fournissez dans les paramètres du modèle.

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Ajoutez la ressource du réseau virtuel. Pour plus d’informations, consultez le [Fournisseur de ressources réseau](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "subnet1",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },

6. Ajouter les ressources d’adresse IP publiques qui sont utilisés par l’équilibreur de charge et d’une interface réseau.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP1')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName1')]"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP2')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName2')]"
            }
          }
        },

7. Ajoutez la ressource d’équilibrage de la charge qui est utilisée par l’ensemble de l’échelle. Pour plus d’informations, consultez [Prise en charge du Gestionnaire de ressources de Azure pour l’équilibrage de la charge](../load-balancer/load-balancer-arm.md).

        {
          "apiVersion": "2015-06-15",
          "name": "[variables('loadBalancerName')]",
          "type": "Microsoft.Network/loadBalancers",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
          ],
          "properties": {
            "frontendIPConfigurations": [
              {
                "name": "loadBalancerFrontEnd",
                "properties": {
                  "publicIPAddress": {
                    "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
                  }
                }
              }
            ],
            "backendAddressPools": [ { "name": "bepool1" } ],
            "inboundNatPools": [
              {
                "name": "natpool1",
                "properties": {
                  "frontendIPConfiguration": {
                    "id": "[variables('frontEndIPConfigID')]"
                  },
                  "protocol": "tcp",
                  "frontendPortRangeStart": 50000,
                  "frontendPortRangeEnd": 50500,
                  "backendPort": 3389
                }
              }
            ]
          }
        },

8. Ajoutez la ressource d’interface réseau qui est utilisée par l’ordinateur virtuel séparé. Dans la mesure où les ordinateurs dans un jeu d’échelle ne sont pas accessibles via une adresse IP publique, un ordinateur virtuel distinct est créé dans le même réseau virtuel d’accès à distance les ordinateurs.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
                  },
                  "subnet": {
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                  }
                }
              }
            ]
          }
        },

9. Ajouter l’ordinateur virtuel séparé dans le même réseau que l’ensemble de l’échelle.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "storageLoop",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_A1" },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "[concat(parameters('resourcePrefix'), 'os1')]",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
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
            }
          }
        },

10. Ajoutez la que ressource de jeu de l’échelle de l’ordinateur virtuel et spécifier l’extension de diagnostics qui est installée sur tous les ordinateurs virtuels dans l’ensemble de l’échelle. De nombreux paramètres pour cette ressource sont similaires avec la ressource d’ordinateur virtuel. Les principales différences sont l’élément de capacité qui spécifie le nombre d’ordinateurs virtuels dans l’ensemble de l’échelle et les upgradePolicy qui spécifie comment les mises à jour sont apportées à des machines virtuelles. L’ensemble de l’échelle n’est pas créé tant que tous les comptes de stockage sont créés comme spécifié par l’élément dependsOn.

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "2016-03-30",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "Standard_A1",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              },
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "MicrosoftWindowsServer",
                      "offer": "WindowsServer",
                      "sku": "2012-R2-Datacenter",
                      "version": "latest"
                    }
                  },
                  "osProfile": {
                    "computerNamePrefix": "[parameters('vmSSName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                  },
                  "networkProfile": {
                    "networkInterfaceConfigurations": [
                      {
                        "name": "networkconfig1",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                                },
                                "loadBalancerBackendAddressPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                                  }
                                ],
                                "loadBalancerInboundNatPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                                  }
                                ]
                              }
                            }
                          ]
                        }
                      }
                    ]
                  },
                  "extensionProfile": {
                    "extensions": [
                      {
                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                        "properties": {
                          "publisher": "Microsoft.Azure.Diagnostics",
                          "type": "IaaSDiagnostics",
                          "typeHandlerVersion": "1.5",
                          "autoUpgradeMinorVersion": true,
                          "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount": "[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11. Ajoutez la ressource autoscaleSettings qui définit comment l’ensemble d’échelle s’ajuste en fonction de l’utilisation du processeur sur les ordinateurs de l’ensemble de l’échelle.

            {
              "type": "Microsoft.Insights/autoscaleSettings",
              "apiVersion": "2015-04-01",
              "name": "[concat(parameters('resourcePrefix'),'as1')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              ],
              "properties": {
                "enabled": true,
                "name": "[concat(parameters('resourcePrefix'),'as1')]",
                "profiles": [
                  {
                    "name": "Profile1",
                    "capacity": {
                      "minimum": "1",
                      "maximum": "10",
                      "default": "1"
                    },
                    "rules": [
                      {
                        "metricTrigger": {
                          "metricName": "\\Processor(_Total)\\% Processor Time",
                          "metricNamespace": "",
                          "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                          "timeGrain": "PT1M",
                          "statistic": "Average",
                          "timeWindow": "PT5M",
                          "timeAggregation": "Average",
                          "operator": "GreaterThan",
                          "threshold": 50.0
                        },
                        "scaleAction": {
                          "direction": "Increase",
                          "type": "ChangeCount",
                          "value": "1",
                          "cooldown": "PT5M"
                        }
                      }
                    ]
                  }
                ],
                "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              }
            }

    Pour ce didacticiel, ces valeurs sont importants :

    - **metricName** - cette valeur est le même que le compteur de performance que nous avons défini dans la variable wadperfcounter. Collecte de l’extension de Diagnostics à l’aide de cette variable, le **processeur (_Total)\% temps processeur** compteur.
    - **metricResourceUri** - cette valeur est l’identificateur de ressource de l’ensemble d’échelle de machine virtuelle.
    - **timeGrain** : cette valeur est la granularité des mesures qui sont collectées. Dans ce modèle, il est défini sur une minute.
    - **statistique** – cette valeur détermine la façon dont les mesures sont combinées pour s’adapter à l’action de mise à l’échelle automatique. Les valeurs possibles sont : moyenne, Min, Max. Dans ce modèle, l’utilisation totale du processeur moyenne des machines virtuelles est collectée.
    - **durée** : cette valeur est l’intervalle de temps dans laquelle les données de l’instance sont collectées. Il doit être compris entre 5 et 12 heures.
    - **timeAggregation** – cette valeur détermine la façon dont les données collectées doivent être combinées dans le temps. La valeur par défaut est moyenne. Les valeurs possibles sont : moyenne, Minimum, Maximum, dernier, Total, comptage.
    - **opérateur** – cette valeur est l’opérateur qui est utilisé pour comparer les données de mesure et le seuil. Les valeurs possibles sont : est égal à, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    - **seuil** : cette valeur est la valeur qui déclenche l’action de l’échelle. Dans ce modèle, les ordinateurs sont ajoutés à l’échelle définie lors de l’utilisation moyenne du processeur entre les ordinateurs de l’ensemble est supérieure à 50 %.
    - **direction** – cette valeur détermine l’action à entreprendre lorsque le seuil est atteint. Les valeurs possibles sont à augmenter ou diminuer. Dans ce modèle, le nombre d’ordinateurs virtuels dans l’ensemble de l’échelle est augmenté si le seuil est supérieure à 50 % dans la fenêtre de temps définie.
    - **type** , cette valeur est le type d’action qui doit se produire et qui doit être définie sur ChangeCount.
    - **valeur** : cette valeur est le nombre d’ordinateurs virtuels qui sont ajoutés ou supprimés de l’ensemble de l’échelle. Cette valeur doit être 1 ou supérieur. La valeur par défaut est 1. Dans ce modèle, le nombre d’ordinateurs sur l’échelle de jeu augmente de 1 lorsque le seuil est atteint.
    - **ralentissement** – cette valeur est la quantité de temps d’attente depuis la dernière opération de mise à l’échelle avant la prochaine action se produit. Cette valeur doit être entre 1 minute et une semaine.

12. Enregistrez le fichier de modèle.    

## <a name="step-4-upload-the-template-to-storage"></a>Étape 4 : Téléchargez le modèle de stockage

Le modèle peut être téléchargé dans la mesure où vous connaissez le nom et la clé primaire du compte de stockage que vous avez créé à l’étape 1.

1.  Dans la fenêtre Microsoft Azure PowerShell, définir une variable qui indique le nom du compte de stockage que vous avez créé à l’étape 1.

            $storageAccountName = "vmstestsa"

2.  Définir une variable qui indique la clé primaire du compte de stockage.

            $storageAccountKey = "<primary-account-key>"

    Vous pouvez obtenir cette clé en cliquant sur l’icône de clé lors de l’affichage de la ressource de compte de stockage dans le portail Azure.

3.  Créez l’objet de contexte de compte de stockage qui est utilisé pour valider les opérations avec le compte de stockage.

            $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

4.  Créez le conteneur pour stocker le modèle.

            $containerName = "templates"
            New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob

5.  Télécharger le fichier modèle pour le nouveau conteneur.

            $blobName = "VMSSTemplate.json"
            $fileName = "C:\" + $BlobName
            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx

## <a name="step-5-deploy-the-template"></a>Étape 5 : Déployer le modèle

Maintenant que vous avez créé le modèle, vous pouvez démarrer le déploiement de ressources. Utilisez cette commande pour démarrer le processus :

    New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"

Lorsque vous appuyez sur entrer, vous êtes invité à fournir des valeurs pour les variables que vous avez affecté. Fournir ces valeurs :

    vmName: vmsstestvm1
      vmSSName: vmsstest1
      instanceCount: 5
      adminUserName: vmadmin1
      adminPassword: VMpass1
      resourcePrefix: vmsstest

Il devrait prendre environ 15 minutes pour toutes les ressources à déployer avec succès.

>[AZURE.NOTE] Possibilité du portail vous permet également de déployer les ressources. Utilisez le lien suivant : « https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>"

## <a name="step-6-monitor-resources"></a>Étape 6 : Ressources de moniteur

Vous pouvez obtenir des informations sur les jeux d’échelle de machine virtuelle à l’aide de ces méthodes :

 - Le portail Azure - vous obtiendrez actuellement une quantité limitée d’informations via le portail.
 - L' [Explorateur de ressources Azure](https://resources.azure.com/) - cet outil est la meilleure pour l’exploration de l’état actuel de l’ensemble de l’échelle. Suivez ce chemin d’accès et vous devriez voir la vue d’instance de l’ensemble d’échelle que vous avez créé :

        subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - PowerShell Azure - Utilisez cette commande pour obtenir des informations :

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

        Or

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

 - Se connecter à l’ordinateur virtuel séparé comme vous le feriez pour n’importe quel autre ordinateur, et vous pouvez ensuite accéder à distance les ordinateurs virtuels de l’échelle définie pour analyser les processus individuels.

>[AZURE.NOTE] Une API REST complète pour obtenir des informations sur les jeux de l’échelle se trouvent dans [l’Échelle de la Machine virtuelle définit](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>Étape 7 : Supprimer les ressources

Car vous sont facturés pour les ressources utilisées dans Azure, il est toujours une bonne pratique pour supprimer les ressources qui ne sont plus nécessaires. Vous ne devez supprimer séparément de chaque ressource d’un groupe de ressources. Vous pouvez supprimer le groupe de ressources et toutes ses ressources sont automatiquement supprimés.

    Remove-AzureRmResourceGroup -Name vmsstestrg1

Si vous souhaitez conserver votre groupe de ressources, vous pouvez supprimer l’échelle définie uniquement.

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"

## <a name="next-steps"></a>Étapes suivantes

- Gérer le jeu d’échelle que vous venez de créer en utilisant les informations dans la [Gestion des machines virtuelles dans une échelle de Machine virtuelle définie](virtual-machine-scale-sets-windows-manage.md).
- En savoir plus sur la mise à l’échelle verticale en consultant [l’échelle verticale avec des jeux de l’échelle de l’ordinateur virtuel](virtual-machine-scale-sets-vertical-scale-reprovision.md)
- Rechercher des exemples de moniteur d’Azure fonctionnalités dans [PowerShell de moniteur Azure rapide démarrer les échantillons](../monitoring-and-diagnostics/insights-powershell-samples.md) d’analyse
- En savoir plus sur les fonctionnalités de notification dans [utiliser des actions autoscale pour envoyer des notifications d’alerte e-mail et webhook dans le moniteur d’Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
- Découvrez comment les [journaux d’audit de l’utilisation pour envoyer des notifications d’alerte e-mail et webhook dans le moniteur d’Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
