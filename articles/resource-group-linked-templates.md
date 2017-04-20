<properties
   pageTitle="Lié avec le Gestionnaire de ressources de modèles | Microsoft Azure"
   description="Décrit comment utiliser des modèles liés dans un modèle de gestionnaire de ressources Azure pour créer une solution modulaire de modèle. Montre comment passer des valeurs de paramètres, spécifiez un fichier de paramètres et URL créés dynamiquement."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/02/2016"
   ms.author="tomfitz"/>

# <a name="using-linked-templates-with-azure-resource-manager"></a>À l’aide de modèles liés avec le Gestionnaire de ressources Azure

À partir de dans un modèle de gestionnaire de ressources Azure, vous pouvez lier à un autre modèle, ce qui vous permet de décomposer votre déploiement en un jeu de cibles, modèles d’usage particulier. Comme avec la décomposition d’une application en plusieurs classes de code, décomposition fournit des avantages en termes de test, de réutilisation et lisibilité.  

Vous pouvez passer des paramètres à partir d’un modèle principal à un modèle lié, et ces paramètres peuvent mapper directement vers les paramètres ou les variables exposées par le modèle d’appel. Le modèle lié pouvez également passer une variable de sortie dans le modèle source, permettant un échange bidirectionnel de données entre les modèles.

## <a name="linking-to-a-template"></a>Liaison à un modèle

Vous créez un lien entre deux modèles de par l’ajout d’une ressource de déploiement dans le modèle principal qui pointe vers le modèle lié. Vous définissez la propriété **templateLink** à l’URI du modèle lié. Vous pouvez fournir des valeurs de paramètre pour le modèle lié soit en spécifiant les valeurs directement dans votre modèle en créant un lien vers un fichier de paramètres. L’exemple suivant utilise la propriété de **paramètres** pour spécifier une valeur de paramètre directement.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

Le service Gestionnaire de ressources doit être en mesure d’accéder au modèle lié. Vous ne pouvez pas spécifier un fichier local ou un fichier qui n’est pas disponible sur votre réseau local pour le modèle lié. Vous pouvez uniquement fournir une valeur URI qui inclut **http** ou **https**. Une option consiste à placer votre modèle lié dans un compte de stockage et à utiliser l’URI de cet élément, comme illustré dans l’exemple suivant.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Bien que le modèle lié doit être disponible depuis l’extérieur, il n’a pas besoin d’être accessibles au public. Vous pouvez ajouter à votre modèle à un compte de stockage privé accessible uniquement au propriétaire du compte de stockage. Ensuite, vous créez un jeton de signature (SAS) d’accès partagé pour permettre l’accès pendant le déploiement. Vous ajoutez ce jeton SAS à l’URI pour le modèle lié. Pour obtenir la procédure de paramétrage d’un modèle dans un compte de stockage et de générer un jeton SAS, voir [déployer des ressources avec le Gestionnaire de ressources modèles et Azure PowerShell](resource-group-template-deploy.md) ou [déployer avec modèles du Gestionnaire de ressources et de la CLI d’Azure](resource-group-template-deploy-cli.md). 

L’exemple suivant montre un modèle parent qui établit un lien vers un autre modèle. Le modèle lié est accessible avec un jeton d’associations de sécurité qui est passé comme un paramètre.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

Même si le jeton est passé sous la forme d’une chaîne sécurisée, l’URI du modèle lié, y compris le jeton SAS, est enregistré dans les opérations de déploiement pour ce groupe de ressources. Pour limiter l’exposition, définissez une date d’expiration pour le jeton.

## <a name="linking-to-a-parameter-file"></a>Liaison à un fichier de paramètres

L’exemple suivant utilise la propriété **parametersLink** pour lier à un fichier de paramètres.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

La valeur de l’URI pour le fichier de paramètre lié ne peut pas être un fichier local et doit inclure les **http** ou **https**. Le fichier de paramètres peut également être limité à l’accès via un jeton SAS.

## <a name="using-variables-to-link-templates"></a>Pour lier des modèles à l’aide de variables

Les exemples précédents ont montré les valeurs d’URL codées en dur pour les liaisons de modèle. Cette approche peut fonctionner pour un modèle simple, mais il ne fonctionne pas correctement lorsque vous travaillez avec un grand ensemble de modèles modulaires. Au lieu de cela, vous pouvez créer une variable statique qui contient une URL de base pour le modèle principal et créer dynamiquement des URL pour les modèles liés à partir de cette URL de base. L’avantage de cette approche est de pouvoir facilement déplacer ou une branche dans le modèle, car vous ne devez modifier la variable statique dans le modèle principal. Le modèle principal passe les URI correct dans l’ensemble du modèle décomposé.

L’exemple suivant montre comment utiliser une URL de base pour créer les deux URL pour les modèles liés (**sharedTemplateUrl** et **vmTemplate**). 

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

Vous pouvez également utiliser [le déploiement()](resource-group-template-functions.md#deployment) pour obtenir l’URL de base pour le modèle actuel et l’utiliser pour obtenir l’URL pour les autres modèles dans le même emplacement. Cette approche est utile si votre dossier de modèles change (peut-être en raison de la gestion des versions) ou si vous souhaitez éviter le codage en dur des URL dans le fichier de modèle. 

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## <a name="conditionally-linking-to-templates"></a>Sous certaines conditions la liaison à des modèles

Vous pouvez lier à différents modèles en passant une valeur de paramètre qui est utilisée pour construire l’URI du modèle lié. Cette approche fonctionne bien lorsque vous devez spécifier au cours du déploiement lié le modèle à utiliser. Par exemple, vous pouvez spécifier un modèle à utiliser pour un compte de stockage existant et un autre modèle à utiliser pour un nouveau compte de stockage.

L’exemple suivant montre un paramètre pour un nom de compte de stockage et un paramètre pour spécifier si le compte de stockage est nouveau ou existant.

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

Vous créez une variable pour le modèle URI qui inclut la valeur du paramètre nouveau ou existant.

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

Vous fournissez cette valeur de la variable pour les ressources de déploiement.

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

L’URI correspond à un modèle nommé **existingStorageAccount.json** ou **newStorageAccount.json**. Créer des modèles pour les URI.

L’exemple suivant montre le modèle **existingStorageAccount.json** .

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

L’exemple suivant montre le modèle **newStorageAccount.json** . Notez que le modèle de compte l’objet de compte de stockage, comme le stockage existant, est retourné dans les sorties. Le modèle principal fonctionne avec un modèle lié.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## <a name="complete-example"></a>Exemple complet

Les modèles d’exemple suivants montrent des modèles liés une disposition simplifiée pour illustrer des concepts décrits dans cet article. Il suppose que les modèles ont été ajoutés au même conteneur dans un compte de stockage avec un accès public hors tension. Le modèle lié passe une valeur dans le modèle principal dans la section des **sorties** .

Le fichier **parent.json** se compose de :

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "linkedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('linkedTemplate').outputs.result]"
        }
      }
    }

Le fichier **helloworld.json** se compose de :

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {
        "result": {
            "value": "Hello World",
            "type" : "string"
        }
      }
    }
    
Dans PowerShell, vous obtenez un jeton pour le conteneur et de déployez les modèles avec :

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

Dans l’infrastructure du langage commun Azure, vous obtenez un jeton pour le conteneur et de déployez les modèles avec le code suivant. Actuellement, vous devez fournir un nom pour le déploiement lors de l’utilisation d’un modèle URI qui inclut un jeton SAS.  

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

Vous êtes invité à fournir le jeton SAS en tant que paramètre. Vous devez faire précéder le jeton avec **?**.

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir des informations sur la définition de l’ordre de déploiement de vos ressources, consultez [définition des dépendances dans les modèles du Gestionnaire de ressources Azure](resource-group-define-dependencies.md)
- Pour savoir comment définir une ressource mais créent de nombreuses instances de celui-ci, reportez-vous à la section [Création de plusieurs instances de ressources dans le Gestionnaire de ressources Azure](resource-group-create-multiple.md)
