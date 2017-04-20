<properties
   pageTitle="Modèle de gestionnaire de ressources pour des affectations de rôle | Microsoft Azure"
   description="Affiche le schéma du Gestionnaire de ressources pour le déploiement d’une affectation de rôle à travers un modèle."
   services="azure-resource-manager"
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
   ms.date="10/03/2016"
   ms.author="tomfitz"/>

# <a name="role-assignments-template-schema"></a>Schéma de modèle affectations de rôle

Affecte un utilisateur, un groupe ou une entité de service à un rôle à une portée spécifiée.

## <a name="resource-format"></a>Format de ressources

Pour créer une affectation de rôle, ajoutez le schéma suivant à la section de ressources de votre modèle.
    
    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Obligatoire | Description |
| ---- | -------- | ----------- |
| type de | Oui    | Le type de ressource à créer.<br /><br /> Pour le groupe de ressources :<br />**Microsoft.Authorization/roleAssignments**<br /><br />Pour les ressources :<br />**{fournisseur-namespace} / {type de ressource} / fournisseurs/roleAssignments** |
| apiVersion |Oui | La version de l’API à utiliser pour la création de la ressource.<br /><br /> Utilisez **2015-07-01**. | 
| nom | Oui | Un identificateur global unique pour la nouvelle attribution de rôle. |
| dependsOn | N° | Un tableau séparé par des virgules d’une ressource de noms ou des identificateurs de ressource uniques.<br /><br />La collection de ressources que dépend de cette attribution de rôle. Si l’affectation d’un rôle qui est portée à une ressource et que la ressource est déployé dans le même modèle, incluez ce nom de ressource dans cet élément afin de garantir que la ressource est déployée en premier. |
| propriétés | Oui | L’objet de propriétés qui identifie la définition de rôle, principal et étendue. |

### <a name="properties-object"></a>objet de propriétés

| Nom | Obligatoire | Description |
| ---- | -------- | ----------- |
| roleDefinitionId | Oui |  L’identificateur d’une définition de rôle existante à utiliser dans l’affectation de rôle.<br /><br /> Utilisez le format suivant :<br /> **/ subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId | Oui | L’identificateur global unique pour une entité de sécurité existante. Cette valeur correspond à l’id dans le répertoire et peut pointer vers un utilisateur, un principal de service ou un groupe de sécurité. |
| champ d’application | N° | L’étendue à laquelle s’applique cette attribution de rôle.<br /><br />Pour les groupes de ressources, utilisez :<br />**/Subscriptions/ {id d’abonnement} /resourceGroups/ {nom de groupe de ressource}**  <br /><br />Pour les ressources, utilisez :<br />**/ subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>L’utilisation de la ressource d’affectation de rôle

Vous ajoutez une attribution de rôle à votre modèle lorsque vous souhaitez ajouter un utilisateur, un groupe ou un service principal à un rôle au cours du déploiement. Attributions de rôles sont héritées des niveaux supérieurs de la portée, si vous avez déjà ajouté une entité de sécurité à un rôle au niveau de l’abonnement, vous n’avez pas besoin de réassigner pour le groupe de ressources ou la ressource.

Il existe de nombreuses valeurs d’identificateur que vous deviez fournir lors de l’utilisation des affectations de rôle. Vous pouvez récupérer les valeurs à l’aide de PowerShell ou Azure CLI.

### <a name="powershell"></a>PowerShell

Le nom de l’affectation de rôle requiert un identificateur global unique. Vous pouvez générer un nouvel identificateur de **nom** avec :

    $name = [System.Guid]::NewGuid().toString()

Vous pouvez récupérer l’identificateur de la définition de rôle avec :

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Vous pouvez récupérer l’identificateur de l’entité de sécurité avec l’une des commandes suivantes.

Pour un groupe nommé **auditeurs**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Pour un utilisateur nommé **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Pour un service principal nommé **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>CLI Azure

Vous pouvez récupérer l’identificateur de la définition de rôle avec :

    azure role show Reader --json | jq .[].Id -r

Vous pouvez récupérer l’identificateur de l’entité de sécurité avec l’une des commandes suivantes.

Pour un groupe nommé **auditeurs**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Pour un utilisateur nommé **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Pour un service principal nommé **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Exemples

Le modèle suivant reçoit un identificateur pour un rôle et un identificateur pour un utilisateur, un groupe ou une entité de service. Il assigne le rôle au niveau du groupe de ressources.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



Le modèle suivant crée un compte de stockage et attribue le rôle de lecteur pour le compte de stockage. Les identificateurs de deux groupes et le rôle de lecteur ont été inclus dans le modèle pour simplifier le déploiement. Ces valeurs peuvent être récupérées au moment du déploiement par le biais de script et passés comme paramètres.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Les modèles suivants montrent comment utiliser la ressource d’affectation de rôle :

- [Attribuer un rôle intégré au groupe de ressources](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Attribuer un rôle intégré à une machine virtuelle existante](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Attribuer un rôle intégré à plusieurs machines virtuelles d’existantes](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la structure du modèle, consultez [Création d’un gestionnaire de ressources Azure modèles](resource-group-authoring-templates.md).
- Pour plus d’informations à propos du contrôle d’accès basé sur les rôles, consultez [Contrôle d’accès basé sur le rôle de répertoire actif Azure](./active-directory/role-based-access-control-configure.md).
