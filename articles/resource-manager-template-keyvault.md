<properties
   pageTitle="Modèle de gestionnaire de ressources pour la chambre forte clé | Microsoft Azure"
   description="Affiche le schéma du Gestionnaire de ressources pour le déploiement de coffres-forts clés via un modèle."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-template-schema"></a>Schéma de modèle de clé coffre-fort

Crée un clé coffre-fort.

## <a name="schema-format"></a>Format de schéma

Pour créer un coffre-fort de clés, ajoutez le schéma suivant à la section de ressources de votre modèle.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- | 
| type de | Enum<br />Obligatoire<br />**Microsoft.KeyVault/vaults**<br /><br />Le type de ressource à créer. |
| apiVersion | Enum<br />Obligatoire<br />**2015-06-01** ou **2014-12-19-aperçu**<br /><br />La version de l’API à utiliser pour la création de la ressource. | 
| nom | Chaîne<br />Obligatoire<br />Un nom qui est unique dans Azure.<br /><br />Nom du coffre-fort clé à créer. Pensez à l’aide de la fonction [uniqueString](resource-group-template-functions.md#uniquestring) avec votre convention d’affectation de noms pour créer un nom unique, comme indiqué dans l’exemple ci-dessous. |
| emplacement | Chaîne<br />Obligatoire<br />Une région valide pour les coffres-forts principaux. Pour déterminer les zones valides, consultez [prise en charge des régions](resource-manager-supported-services.md#supported-regions).<br /><br />La région pour la chambre forte de clé d’hôte. |
| propriétés | Objet<br />Obligatoire<br />[objet de propriétés](#properties)<br /><br />Objet qui spécifie le type de coffre-fort clé à créer. |
| ressources | Tableau<br />Facultatif<br />Autorisé des valeurs : [ressources secrètes de coffre-fort de clé](resource-manager-template-keyvault-secret.md)<br /><br />Ressources enfants pour la chambre forte de clé. |

<a id="properties" />
### <a name="properties-object"></a>objet de propriétés

| Nom | Valeur |
| ---- | ---- | 
| enabledForDeployment | Valeur booléenne<br />Facultatif<br />**valeur True** ou **false**<br /><br />Indique si le coffre-fort est activé pour le déploiement d’ordinateur virtuel ou le Service Fabric. |
| enabledForTemplateDeployment | Valeur booléenne<br />Facultatif<br />**valeur True** ou **false**<br /><br />Indique si le coffre-fort est activé pour une utilisation dans les déploiements de modèle de gestionnaire de ressources. Pour plus d’informations, voir [passer des valeurs sécurisées pendant le déploiement](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | Valeur booléenne<br />Facultatif<br />**valeur True** ou **false**<br /><br />Indique si le coffre-fort est activé pour le cryptage de volume. |
| tenantId | Chaîne<br />Obligatoire<br />**Identificateur global unique**<br /><br />L’identificateur de client pour l’abonnement. Vous pouvez le récupérer avec l’applet de commande PowerShell [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) ou **compte azure affiche** commande CLI d’Azure. |
| accessPolicies | Tableau<br />Obligatoire<br />[accessPolicies objet](#accesspolicies)<br /><br />Tableau d’objets jusqu'à 16 qui spécifie les autorisations pour l’utilisateur ou l’identité du service. |
| point de stock | Objet<br />Obligatoire<br />[objet de référence](#sku)<br /><br />Le point de stock pour la chambre forte de clé. |

<a id="accesspolicies" />
### <a name="propertiesaccesspolicies-object"></a>properties.accessPolicies objet

| Nom | Valeur |
| ---- | ---- | 
| tenantId | Chaîne<br />Obligatoire<br />**Identificateur global unique**<br /><br />L’identificateur client du locataire Azure Active Directory contenant l' **objectId** dans cette stratégie d’accès |
| objectId | Chaîne<br />Obligatoire<br />**Identificateur global unique**<br /><br />L’identificateur d’objet de l’utilisateur Azure Active Directory ou le principal de service qui auront accès à la chambre forte. Vous pouvez récupérer la valeur de [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) ou les applets de commande PowerShell de [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) ou les commandes CLI d’Azure **utilisateur ad azure** ou **ad azure sp** . |
| autorisations | Objet<br />Obligatoire<br />[autorisations objet](#permissions)<br /><br />Les autorisations accordées sur ce coffre-fort à l’objet Active Directory. |

<a id="permissions" />
### <a name="propertiesaccesspoliciespermissions-object"></a>properties.accessPolicies.permissions objet

| Nom | Valeur |
| ---- | ---- | 
| clés | Tableau<br />Obligatoire<br />**tous les**, **sauvegarde**, **créer**, **décrypter**, **Supprimer**, **crypter**, **obtenir**, **Importer**, **liste**, **restaurer**, **signe**, **unwrapkey**, **mettre à jour**, **Vérifiez**, **wrapkey**<br /><br />Les autorisations accordées sur les clés de ce coffre-fort à cet objet d’Active Directory. Cette valeur doit être spécifiée comme un tableau d’une ou plusieurs valeurs autorisées. |
| secrets | Tableau<br />Obligatoire<br />**tous les** **Supprimer**, **obtenir**, **liste**, **définir**<br /><br />Les autorisations accordées sur les secrets de ce coffre-fort à cet objet d’Active Directory. Cette valeur doit être spécifiée comme un tableau d’une ou plusieurs valeurs autorisées. |

<a id="sku" />
### <a name="propertiessku-object"></a>Properties.SKU objet

| Nom | Valeur |
| ---- | ---- | 
| nom | Enum<br />Obligatoire<br />**standard**ou **premium** <br /><br />Le niveau de service de KeyVault à utiliser.  Standard prend en charge les secrets et clés de logiciel protégé.  Prime prend en charge les clés protégées par HSM. |
| famille | Enum<br />Obligatoire<br />**A** <br /><br />La famille de référence à utiliser. |
 
    
## <a name="examples"></a>Exemples

L’exemple suivant déploie un coffre-fort de clé et un code secret.

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

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Le modèle de démarrage rapide suivants déploie un coffre-fort de clé.

- [Créer la clé coffre-fort](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales sur les coffres-forts de clés, consultez [mise en route de la chambre forte de clé Azure](./key-vault/key-vault-get-started.md).
- Pour obtenir un exemple de référencement d’un secret de coffre-fort clés lors du déploiement de modèles, voir [passer des valeurs sécurisées pendant le déploiement](resource-manager-keyvault-parameter.md).

