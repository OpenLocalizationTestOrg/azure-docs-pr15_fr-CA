<properties
   pageTitle="Procédure pas à pas de gestionnaire de ressources du modèle | Microsoft Azure"
   description="Une procédure étape par étape d’un modèle de gestionnaire de ressources mise en service d’une architecture de base Azure IaaS."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="navale;tomfitz"/>
   
# <a name="resource-manager-template-walkthrough"></a>Procédure pas à pas de gestionnaire de ressources du modèle

L’une des premières questions lors de la création d’un modèle est « comment démarrer ? ». Il est possible à partir d’un modèle vide, suivant la structure de base décrite dans [l’article du modèle de programmation](resource-group-authoring-templates.md#template-format)et ajouter les ressources et les paramètres appropriés et les variables. Une bonne alternative consisterait à démarrer en passant par la [Galerie de démarrage rapide](https://github.com/Azure/azure-quickstart-templates) et de rechercher des scénarios similaires à celle que vous essayez de créer. Vous pouvez fusionner plusieurs modèles ou en modifier une existante pour l’adapter à votre scénario spécifique. 

Jetons un œil à une infrastructure commune :

* Deux ordinateurs virtuels qui utilisent le même compte de stockage, sont dans le même jeu de disponibilité et sur le même sous-réseau d’un réseau virtuel.
* Une seule carte d’interface réseau et VM l’adresse IP pour chaque ordinateur virtuel.
* Un équilibreur de charge avec une règle sur le port 80 d’équilibrage de la charge

![architecture](./media/resource-group-overview/arm_arch.png)

Cette rubrique vous guide tout au long des étapes de création d’un modèle de gestionnaire de ressources pour cette infrastructure. Le modèle final que vous créez est basé sur un modèle Quickstart appelé [2 VM dans un équilibreur de charge et les règles d’équilibrage de charge](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

Mais, qui est un lot pour générer à la fois, nous allons tout d’abord de créer un compte de stockage et le déployer. Une fois que vous maîtrisez la création du compte de stockage, vous ajoutez les autres ressources et redéployer le modèle pour terminer l’infrastructure.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel type de l’éditeur lors de la création du modèle. Visual Studio fournit des outils qui simplifient le développement de modèle, mais vous n’avez pas besoin de Visual Studio pour compléter ce didacticiel. Pour un didacticiel sur l’utilisation de Visual Studio pour créer un déploiement d’application Web et de base de données SQL, reportez-vous à la section [Création et le déploiement de groupes de ressources Azure via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 

## <a name="create-the-resource-manager-template"></a>Créer le modèle de gestionnaire de ressources

Le modèle est un fichier JSON qui définit toutes les ressources que vous allez déployer. Il vous permet également de pour définir les paramètres qui sont spécifiés au cours du déploiement, les variables qui construit à partir d’autres valeurs et les expressions et les sorties du déploiement. 

Commençons par le modèle plus simple :

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Enregistrez ce fichier sous **azuredeploy.json** (Notez que le modèle peut avoir n’importe quel nom que vous souhaitez simplement qu’il doit être un fichier json).

## <a name="create-a-storage-account"></a>Créer un compte de stockage
Dans la section **ressources** , ajout d’un objet qui définit le compte de stockage, comme illustré ci-dessous. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Vous vous demandez peut-être où ces propriétés et ces valeurs proviennent. Les propriétés **type**, le **nom**, le **apiVersion**et **l’emplacement** sont des éléments standard qui sont disponibles pour tous les types de ressource. Vous pouvez obtenir des informations sur les éléments communs à des [ressources](resource-group-authoring-templates.md#resources). **nom** est défini sur une valeur de paramètre que vous passez pendant le déploiement et **l’emplacement** que l’emplacement utilisé par le groupe de ressources. Nous allons voir comment vous déterminez le **type** et les **apiVersion** dans les sections ci-dessous.

La section **properties** contient toutes les propriétés qui sont spécifiques à un type de ressource particulier. Les valeurs que vous spécifiez dans cette section exactement correspondre à l’opération de placement dans l’API REST pour la création de ce type de ressource. Lors de la création d’un compte de stockage, vous devez fournir un **accountType**. Notez dans l' [API REST pour la création d’un compte de stockage](https://msdn.microsoft.com/library/azure/mt163564.aspx) que la section des propriétés de l’opération reste contient également une propriété **accountType** , et les valeurs autorisées sont documentés. Dans cet exemple, le type de compte est défini sur **Standard_LRS**, mais Impossible de spécifier une autre valeur ou d’autoriser les utilisateurs à passer dans le type de compte en tant que paramètre.

Maintenant nous allons passer à la section **paramètres** et voir comment vous définissez le nom du compte de stockage. Pour plus d’informations sur l’utilisation de paramètres dans les [paramètres](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Ici, vous avez défini un paramètre de type chaîne destinée à contenir le nom du compte de stockage. Vous trouverez la valeur de ce paramètre au cours du déploiement du modèle.

## <a name="deploying-the-template"></a>Déployer le modèle
Nous avons un modèle complet pour la création d’un nouveau compte de stockage. Comme vous le savez, le modèle a été enregistré dans le fichier de **azuredeploy.json** :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Il existe quelques façons de déployer un modèle, comme vous pouvez le voir dans l' [article du déploiement de la ressource](resource-group-template-deploy.md). Pour déployer le modèle à l’aide de PowerShell d’Azure, utilisez :

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

Ou bien, pour déployer le modèle à l’aide de la CLI d’Azure, utilisez :

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

Vous êtes maintenant l’heureux propriétaire d’un compte de stockage !

Les étapes suivantes seront pour ajouter toutes les ressources nécessaires au déploiement de l’architecture décrite au début de ce didacticiel. Vous allez ajouter ces ressources dans le même modèle que vous avez travaillé.

## <a name="availability-set"></a>Ensemble de disponibilité
Après la définition du compte de stockage, vous devez ajouter un attirer défini pour les ordinateurs virtuels. Dans ce cas, il n’y a aucune propriété supplémentaire nécessaire, sa définition est assez simple. Consultez l' [API REST pour la création d’un groupe de disponibilité](https://msdn.microsoft.com/library/azure/mt163607.aspx) de la section Propriétés complètes, dans le cas où vous souhaitez définir les mise à jour de domaine count et pannes domaine compter des valeurs.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Notez que le **nom** est défini à la valeur d’une variable. Pour ce modèle, le nom de l’ensemble de la disponibilité est nécessaire à plusieurs endroits différents. Vous pouvez gérer plus facilement votre modèle en définissant cette valeur une fois et l’utiliser dans plusieurs endroits.

La valeur que vous spécifiez pour le **type** contient à la fois le fournisseur de ressources et le type de ressource. Pour les jeux de la disponibilité, le fournisseur de ressources est **Microsoft.Compute** et le type de ressource est **availabilitySets**. Vous pouvez obtenir la liste des fournisseurs de ressources disponibles en exécutant la commande PowerShell suivante :

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

Ou bien, si vous utilisez la CLI d’Azure, vous pouvez exécuter la commande suivante :
```
    azure provider list
```
Étant donné que dans cette rubrique, vous créez avec les comptes de stockage, des machines virtuelles et des réseaux virtuels, vous allez travailler avec :

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Pour visualiser les types de ressources pour un fournisseur particulier, exécutez la commande PowerShell suivante :

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

Ou bien, pour Azure CLI, la commande suivante va renvoyer les types disponibles au format JSON et l’enregistrer dans un fichier.

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Vous devez voir **availabilitySets** comme l’un des types de **Microsoft.Compute**. Le nom complet du type est **Microsoft.Compute/availabilitySets**. Vous pouvez déterminer le nom du type de ressource pour les ressources dans le modèle de vous.

## <a name="public-ip"></a>Adresse IP publique
Définissez une adresse IP publique. Examinez à nouveau, l' [API REST pour les adresses IP publiques](https://msdn.microsoft.com/library/azure/mt163590.aspx) pour les propriétés à définir.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

Le mode de répartition est défini **dynamique** , mais vous pouvez l’affecter à la valeur que vous avez besoin ou configurez-le pour accepter une valeur de paramètre. Vous avez activé les utilisateurs de votre modèle pour passer une valeur pour l’étiquette de nom de domaine.

Maintenant, nous allons voir comment déterminer **apiVersion**. La valeur que vous spécifiez correspond simplement à la version de l’API REST que vous souhaitez utiliser lors de la création de la ressource. Ainsi, vous pouvez consulter la documentation de l’API REST pour ce type de ressource. Ou bien, vous pouvez exécuter la commande PowerShell suivante pour un type particulier.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Qui renvoie les valeurs suivantes :

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Pour voir les versions API avec Azure CLI, exécutez la même commande **fournisseur azure afficher** illustrée précédemment.

Lorsque vous créez un nouveau modèle, choisissez la version la plus récente de l’API.

## <a name="virtual-network-and-subnet"></a>Sous-réseau et des réseaux virtuels
Créer un réseau virtuel à un sous-réseau. Examinez l' [API REST pour les réseaux virtuels](https://msdn.microsoft.com/library/azure/mt163661.aspx) pour toutes les propriétés à définir.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>L’équilibreur de charge
Maintenant, vous allez créer un équilibreur de charge externe en vis-à-vis. Dans la mesure où ce programme d’équilibrage de charge utilise l’adresse IP publique, vous devez déclarer une dépendance sur l’adresse IP publique dans la section **dependsOn** . Cela signifie que l’équilibreur de charge ne sera pas obtenir déployé jusqu'à ce que l’adresse IP publique a terminé le déploiement. Sans définir cette dépendance, vous recevrez une erreur, car le Gestionnaire de ressources va tenter de déployer les ressources en parallèle et va tenter de définir l’équilibreur de charge pour des adresses IP publiques qui n’existent pas encore. 

Vous allez également créer un pool d’adresses back-end, deux règles de trafic entrant NAT à RDP dans les ordinateurs virtuels et une règle d’équilibrage de la charge avec une sonde de tcp sur le port 80 dans la définition de cette ressource. Extraction de l' [API REST de l’équilibreur de charge](https://msdn.microsoft.com/library/azure/mt163574.aspx) pour toutes les propriétés.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>Interface réseau
Vous allez créer 2 interfaces réseau, une pour chaque ordinateur virtuel. Au lieu de devoir inclure les entrées en double pour les interfaces réseau, vous pouvez utiliser la [fonction de copyIndex()](resource-group-create-multiple.md) pour effectuer une itération sur la boucle de copie (appelée nicLoop) et de créer des interfaces réseau nombres tel que défini dans le `numberOfInstances` variables. L’interface réseau dépend de la création du réseau virtuel et l’équilibreur de charge. Il utilise le sous-réseau défini dans la création du réseau virtuel et l’id de programme d’équilibrage de charge pour configurer le pool d’adresses équilibrage de la charge et les règles NAT entrantes.
Examinez l' [API REST pour les interfaces réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx) pour toutes les propriétés.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>Machine virtuelle
Vous allez créer les ordinateurs virtuels 2, à l’aide de la fonction de copyIndex(), comme vous l’avez fait lors de la création des [interfaces réseau](#network-interface).
La création de l’ordinateur virtuel dépend du compte de stockage, ensemble d’interface et de la disponibilité du réseau. Cet ordinateur virtuel sera créé à partir d’une image du marché, tel que défini dans le `storageProfile` propriété - `imageReference` est utilisée pour définir l’éditeur d’image, offre, sku et la version. Enfin, un profil de diagnostic est configuré pour activer les tests de diagnostic pour la machine virtuelle. 

Pour rechercher les propriétés pertinentes pour une image marketplace, suivez les articles [Sélectionnez images de machine virtuelle Linux](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) ou [images de machine virtuelle Windows](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md) .

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] Pour les images publiées par **des fournisseurs tiers 3**, vous devez spécifier une autre propriété nommée `plan`. Vous trouverez un exemple de [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) dans la galerie de démarrage rapide. 

Vous avez terminé de définir les ressources de votre modèle.

## <a name="parameters"></a>Paramètres

Dans la section Paramètres, définissez les valeurs qui peuvent être spécifiées lorsque vous déployez le modèle. Uniquement définir les paramètres pour les valeurs que vous pensez doit varier au cours du déploiement. Vous pouvez fournir une valeur par défaut pour un paramètre qui est utilisé si aucune n’est pas fournie pendant le déploiement. Vous pouvez également définir les valeurs autorisées comme indiqué pour le paramètre **imageSKU** .

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>Variables

Dans la section variables, vous pouvez définir les valeurs qui sont utilisées dans plusieurs endroits dans votre modèle, ou qui sont construits à partir d’autres variables ou d’expressions. Les variables sont fréquemment utilisés pour simplifier la syntaxe de votre modèle.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Vous venez de terminer le modèle. Vous pouvez comparer votre modèle par rapport au modèle complet dans la [Galerie de démarrage rapide](https://github.com/Azure/azure-quickstart-templates) sous [2 VM avec équilibrage de la charge et le modèle de règles d’équilibrage de charge](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules). Votre modèle peut être légèrement différent en fonction de l’utilisation des numéros de version différents. 

Vous pouvez redéployer le modèle en utilisant les mêmes commandes que vous avez utilisé lors du déploiement du compte de stockage. Vous n’avez pas besoin de supprimer le compte de stockage avant de le déployer de nouveau, car le Gestionnaire de ressources ignorera la recréation des ressources qui existent déjà et qui n’ont pas changé.

## <a name="next-steps"></a>Étapes suivantes

- [Visualiseur de modèle de gestionnaire de ressources Azure (ARMViz)](http://armviz.io/#/) est un excellent outil pour visualiser les modèles ARM, comme ils peuvent devenir trop grands pour comprendre uniquement à partir de la lecture du fichier json.
- Pour en savoir plus sur la structure d’un modèle, consultez [Création d’un gestionnaire de ressources Azure modèles](resource-group-authoring-templates.md).
- Pour en savoir plus sur le déploiement d’un modèle, voir [déploiement d’un groupe de ressources avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy.md)
