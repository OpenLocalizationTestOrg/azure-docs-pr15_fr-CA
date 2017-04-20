<properties
   pageTitle="Secret Key Vault avec le Gestionnaire de ressources du modèle | Microsoft Azure"
   description="Montre comment passer un secret d’un coffre clé en tant que paramètre au cours du déploiement."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="pass-secure-values-during-deployment"></a>Passer des valeurs sécurisées pendant le déploiement

Lorsque vous devez passer une valeur sécurisée (par exemple, un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur sous la forme d’un secret dans une [Chambre forte de clé Azure](./key-vault/key-vault-whatis.md) et faire référence à la valeur dans d’autres modèles du Gestionnaire de ressources. Vous n'incluez qu’une référence aux données secrètes dans votre modèle, afin que le mot de passe n’est jamais exposée, et vous n’avez pas besoin d’entrer manuellement la valeur pour le mot de passe chaque fois que vous déployez les ressources. Vous spécifiez les utilisateurs ou les entités de service accès au secret.  

## <a name="deploy-a-key-vault-and-secret"></a>Déployer un coffre-fort de clé et le code secret

Pour créer un coffre-fort de clé qui peut être référencé à partir d’autres modèles du Gestionnaire de ressources, vous devez définir la propriété **enabledForTemplateDeployment** sur **true**, et vous devez accorder l’accès à l’utilisateur ou le principal de service qui exécutera le déploiement qui référence le secret.

Pour en savoir plus sur le déploiement d’un coffre-fort de clé et un code secret, voir [schémas de coffre-fort de clé](resource-manager-template-keyvault.md) et [clé coffre-fort secret](resource-manager-template-keyvault-secret.md).

## <a name="reference-a-secret-with-static-id"></a>Faire référence à un secret dont l’id est statique

Vous référencez le secret à partir d’un fichier de paramètres qui transmet les valeurs à votre modèle. Vous référencez le secret en passant l’identificateur de ressource de la chambre forte de clé et le nom de la clé secrète. Dans cet exemple, le secret de la chambre forte de clé doit déjà exister, et vous utilisez une valeur statique pour qu’il l’id de ressource.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Un fichier de l’ensemble du paramètre peut ressembler à :

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

Le paramètre qui accepte le secret doit être une **chaîne securestring**. L’exemple suivant montre les sections pertinentes d’un modèle qui déploie un serveur SQL qui requiert un mot de passe administrateur.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## <a name="reference-a-secret-with-dynamic-id"></a>Faire référence à un secret dont l’id est dynamique

La section précédente a montré comment passer un id de ressource statique pour le secret de la chambre forte de clé. Toutefois, dans certains scénarios, vous devez référencer un secret de coffre-fort clés qui varie selon le déploiement actuel. Dans ce cas, vous ne pouvez pas coder en dur l’id de ressource dans le fichier de paramètres. Malheureusement, vous ne pouvez pas générer dynamiquement l’id de ressource dans le fichier de paramètres parce que les expressions de modèle ne sont pas autorisées dans le fichier de paramètres.

Pour générer de façon dynamique l’id de ressource pour un secret de la chambre forte de clé, vous devez déplacer la ressource dont a besoin le secret dans un modèle imbriqué. Dans votre modèle principal, ajoutez le modèle imbriqué et passer un paramètre qui contient l’id de ressource générée dynamiquement.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales sur les coffres-forts de clés, consultez [mise en route de la chambre forte de clé Azure](./key-vault/key-vault-get-started.md).
- Pour plus d’informations sur l’utilisation d’un coffre-fort de clé avec un ordinateur virtuel, consultez [Considérations relatives à la sécurité pour le Gestionnaire de ressources Azure](best-practices-resource-manager-security.md).
- Pour des exemples complets de clé secrets de référence, consultez [les exemples de coffre-fort de clé](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

