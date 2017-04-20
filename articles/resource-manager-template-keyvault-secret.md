<properties
   pageTitle="Modèle de gestionnaire de ressources pour un secret dans une chambre forte clé | Microsoft Azure"
   description="Affiche le schéma du Gestionnaire de ressources pour le déploiement des secrets de coffre-fort de clés via un modèle."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-secret-template-schema"></a>Schéma de modèle secrète clé coffre-fort

Crée un secret qui est stocké dans une chambre forte de clé. Ce type de ressource est souvent déployé comme une ressource enfant de [chambre forte de clé](resource-manager-template-keyvault.md).

## <a name="schema-format"></a>Format de schéma

Pour créer un secret clé coffre-fort, ajouter le schéma suivant à votre modèle. Le mot de passe peut être défini comme ressource soit enfant d’un coffre-fort clé ou en tant que ressources de niveau supérieur. Vous pouvez la définir comme une ressource enfant lorsque le coffre-fort clé est déployé dans le même modèle. Vous devez définir le mot de passe sous la forme d’une ressource de niveau supérieur lorsque le coffre-fort de clé n’est pas déployé dans le même modèle, ou lorsque vous devez créer plusieurs secrets en effectuant une boucle sur le type de ressource. 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- | 
| type de | Enum<br />Obligatoire<br />**secrets** (lorsqu’il est déployé comme une ressource enfant de chambre forte clé) ou<br /> **Microsoft.KeyVault/vaults/secrets** (lorsqu’il est déployé comme une ressource de niveau supérieur)<br /><br />Le type de ressource à créer. |
| apiVersion | Enum<br />Obligatoire<br />**2015-06-01** ou **2014-12-19-aperçu**<br /><br />La version de l’API à utiliser pour la création de la ressource. | 
| nom | Chaîne<br />Obligatoire<br />Un seul mot lors du déploiement sous la forme d’une ressource de l’enfant d’un coffre-fort de clés, ou dans le format **{clé-vault-name} / {secret-name}** lors du déploiement sous la forme d’une ressource de niveau supérieur à ajouter à un coffre-fort de clé existant.<br /><br />Le nom de la clé secrète à créer. |
| propriétés | Objet<br />Obligatoire<br />[objet de propriétés](#properties)<br /><br />Un objet qui spécifie la valeur de la clé secrète à créer. |
| dependsOn | Tableau<br />Facultatif<br />Une liste séparée par des virgules, d’une ressource de noms ou des identificateurs de ressource uniques.<br /><br />La collection de ressources que dépend de ce lien. Si la clé coffre-fort pour le secret est déployé dans le même modèle, inclure le nom de la chambre forte de clé de cet élément pour vous assurer qu’il est déployé en premier. |

<a id="properties" />
### <a name="properties-object"></a>objet de propriétés

| Nom | Valeur |
| ---- | ---- | 
| valeur | Chaîne<br />Obligatoire<br /><br />Valeur à stocker dans le coffre-fort de clé secrète. Lors du passage d’une valeur pour cette propriété, utilisez un paramètre de type **securestring**.  |

    
## <a name="examples"></a>Exemples

Le premier exemple déploie un secret comme une ressource enfant d’un coffre-fort de clé.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

Le deuxième exemple déploie le secret comme une ressource de niveau supérieur qui est stockée dans un coffre-fort de clé existant.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales sur les coffres-forts de clés, consultez [mise en route de la chambre forte de clé Azure](./key-vault/key-vault-get-started.md).
- Pour obtenir un exemple de référencement d’un secret de coffre-fort clés lors du déploiement de modèles, voir [passer des valeurs sécurisées pendant le déploiement](resource-manager-keyvault-parameter.md).


