<properties
   pageTitle="Déployer plusieurs Instances des ressources | Microsoft Azure"
   description="Permet d’opération de copie et de tableaux dans un modèle de gestionnaire de ressources Azure pour itérer plusieurs fois lors du déploiement de ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/30/2016"
   ms.author="tomfitz"/>

# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>Créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure

Cette rubrique vous indique comment itérer au sein de votre modèle de gestionnaire de ressources Azure pour créer plusieurs instances d’une ressource.

## <a name="copy-copyindex-and-length"></a>copie, copyIndex et longueur

Au sein de la ressource pour créer plusieurs fois, vous pouvez définir un objet de **copie** qui spécifie le nombre de fois pour effectuer une itération. La copie prend le format suivant :

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Vous pouvez accéder à la valeur d’itération actuelle avec la fonction **copyIndex()** , comme illustré ci-dessous dans la fonction concat.

    [concat('examplecopy-', copyIndex())]

Lors de la création de plusieurs ressources à partir d’un tableau de valeurs, vous pouvez utiliser la fonction **length** pour spécifier le nombre. Vous fournissez le tableau comme paramètre à la fonction de la longueur.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## <a name="use-index-value-in-name"></a>Utilisez la valeur d’index de nom

Vous pouvez utiliser la copie opération créer plusieurs instances d’une ressource sont nommées de manière unique en fonction de l’index à incrémentation. Par exemple, vous pouvez souhaiter ajouter un numéro unique à la fin de chaque nom de ressource qui est déployé. Pour déployer trois sites web nommé :

- examplecopy-0
- examplecopy-1
- examplecopy-2.

Utilisez le modèle suivant :

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>Valeur d’index de l’offset

Vous remarquerez que dans l’exemple précédent qui passe de la valeur d’index de zéro à 2. Pour décaler la valeur d’index, vous pouvez passer une valeur dans la fonction **copyIndex()** , comme **copyIndex(1)**. Le nombre d’itérations pour exécuter est toujours spécifié dans l’élément copy, mais la valeur de copyIndex est décalée de la valeur spécifiée. Ainsi, en utilisant le même modèle que l’exemple précédent, mais en spécifiant **copyIndex(1)** serait déployer des trois sites web nommé :

- examplecopy-1
- examplecopy-2
- examplecopy-3

## <a name="use-copy-with-array"></a>Utiliser une copie avec tableau
   
L’opération de copie est particulièrement utile lorsque vous travaillez avec des tableaux, car vous pouvez parcourir chaque élément du tableau. Pour déployer trois sites web nommé :

- examplecopy-Contoso
- Fabrikam-examplecopy
- examplecopy-Coho

Utilisez le modèle suivant :

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Bien sûr, vous définissez le nombre de copies à une valeur différente de la longueur du tableau. Par exemple, vous pourriez créer un tableau avec de nombreuses valeurs et alors transmettre une valeur de paramètre qui spécifie le nombre d’éléments du tableau à déployer. Dans ce cas, vous définissez le nombre de copies comme indiqué dans le premier exemple. 

## <a name="depending-on-resources-in-a-loop"></a>En fonction des ressources dans une boucle

Vous pouvez spécifier qu’une ressource être déployé après une autre ressource à l’aide de l’élément **dependsOn** . Lorsque vous déployez une ressource qui dépend de la collection de ressources dans une boucle, vous permet de fournir le nom de la boucle de la copie de l’élément **dependsOn** . L’exemple suivant indique comment déployer 3 comptes de stockage avant de déployer l’ordinateur virtuel. La définition complète de la Machine virtuelle n’est pas affichée. Notez que l’élément copy a **nom** **storagecopy** et l’élément **dependsOn** pour les ordinateurs virtuels est également défini sur **storagecopy**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
                "copy": { 
                    "name": "storagecopy", 
                    "count": 3 
                }
            },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
        ],
        "outputs": {}
    }

## <a name="looping-on-a-nested-resource"></a>Boucle sur une ressource imbriquée

Vous ne pouvez pas utiliser une boucle de la copie d’une ressource imbriquée. Si vous devez créer plusieurs instances d’une ressource que vous définissez en général comme imbriqué dans une autre ressource, vous devez plutôt créer la ressource comme une ressource de niveau supérieur et définissez la relation avec la ressource parent via les propriétés **type** et le **nom** .

Par exemple, que vous définissez généralement un groupe de données comme une ressource imbriquée dans une usine de données.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Pour créer plusieurs instances de groupes de données, vous devez modifier votre modèle comme indiqué ci-dessous. Notez le type qualifié complet et le nom inclut le nom d’usine de données.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## <a name="create-multiple-instances-when-copy-wont-work"></a>Créer plusieurs instances lors de la copie ne fonctionne pas.

Vous pouvez uniquement utiliser la **copie** sur des types de ressources, et non sur les propriétés d’un type de ressource. Cela peut créer des problèmes pour vous lorsque vous souhaitez créer plusieurs instances d’un élément qui fait partie d’une ressource. Un scénario courant consiste à créer plusieurs disques de données pour une Machine virtuelle. Vous ne pouvez pas utiliser la **copie** avec les disques de données, car **dataDisks** est une propriété sur l’ordinateur virtuel, et non pas son propre type de ressource. Au lieu de cela, vous créez un tableau avec autant de disques de données qui vous seront nécessaires et passer le nombre réel de disques de données pour créer. Dans la définition de la machine virtuelle, vous utilisez la fonction de **prendre** pour obtenir uniquement le nombre d’éléments que vous souhaitez réellement à partir du tableau.

Un exemple complet de ce modèle est indiqué dans le modèle [créer un ordinateur virtuel avec une sélection dynamique de disques de données](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

Les sections correspondantes du modèle de déploiement sont présentées ci-dessous. Un lot du modèle a été supprimé pour sélectionner les sections impliquées dans créer dynamiquement un nombre de disques de données. Notez le paramètre **numDataDisks** qui vous permet de passer le nombre de disques à créer. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```


## <a name="next-steps"></a>Étapes suivantes
- Si vous souhaitez en savoir plus sur les sections d’un modèle, consultez [Création de modèles de gestionnaire de ressources Azure](./resource-group-authoring-templates.md).
- Pour toutes les fonctions que vous pouvez utiliser dans un modèle, consultez [Les fonctions de modèle de gestionnaire de ressources Azure](./resource-group-template-functions.md).
- Pour savoir comment déployer votre modèle, consultez [déploiement d’une application avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy.md).
