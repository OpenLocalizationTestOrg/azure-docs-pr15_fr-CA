<properties
   pageTitle="Modèle de gestionnaire de ressources pour les verrous de ressources | Microsoft Azure"
   description="Affiche le schéma du Gestionnaire de ressources pour le déploiement des verrous sur les ressources d’un modèle."
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

# <a name="resource-locks-template-schema"></a>Schéma de modèle de verrous de ressources

Crée un verrou sur une ressource et ses enfants de ressources.

## <a name="schema-format"></a>Format de schéma

Pour créer un verrou, ajoutez le schéma suivant à la section de ressources de votre modèle.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Obligatoire | Description |
| ---- | -------- | ----------- |
| type de | Oui | Le type de ressource à créer.<br /><br />Pour les ressources :<br />**{namespace} / {type} / fournisseurs/verrous**<br /><br/>Pour les groupes de ressources :<br />**Microsoft.Authorization/locks** |
| apiVersion | Oui | La version de l’API à utiliser pour la création de la ressource.<br /><br />Utilisation :<br />**01 / 01/2015**<br /><br /> |
| nom | Oui | Valeur qui spécifie la ressource à verrouiller et un nom pour le verrou. Peuvent comporter jusqu'à 64 caractères et ne peut pas contenir <>, %, &, ?, ou les caractères de contrôle.<br /><br />Pour les ressources :<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />Pour les groupes de ressources :<br />**{lockname}** |
| dependsOn | N° | Une liste séparée par des virgules, d’une ressource de noms ou des identificateurs de ressource uniques.<br /><br />La collection de ressources que dépend de ce verrou. Si les ressources que sont de verrouillage sont déployé dans le même modèle, incluez ce nom de ressource dans cet élément afin de garantir que la ressource est déployée en premier. | 
| propriétés | Oui | Objet qui identifie le type de verrou et des remarques sur le verrou.<br /><br />Voir [objet properties](#properties-object). |  

### <a name="properties-object"></a>objet de propriétés

| Nom | Obligatoire | Description |
| ---- | -------- | ----------- |
| niveau   | Oui | Le type de verrou à appliquer à l’étendue.<br /><br />**CannotDelete** - les utilisateurs peuvent modifier la ressource mais pas le supprimer.<br />**ReadOnly** : les utilisateurs peuvent lire à partir d’une ressource, mais ils ne peuvent pas supprimer ou exécuter des actions sur celle-ci. |
| Notes   | N° | Description du verrou. Peut contenir jusqu'à 512 caractères. |


## <a name="how-to-use-the-lock-resource"></a>L’utilisation de la ressource de verrouillage

Vous ajoutez cette ressource à votre modèle pour empêcher les actions spécifiées sur une ressource. Le verrou s’applique à tous les utilisateurs et les groupes.

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès à **Microsoft.Authorization/** * ou * *Microsoft.Authorization/locks/* ** actions. Des rôles prédéfinis, uniquement **propriétaire** et **utilisateur accès administrateur ** sont accordées à ces actions. Pour plus d’informations à propos du contrôle d’accès basé sur les rôles, consultez [Contrôle d’accès basé sur les rôles d’Azure](./active-directory/role-based-access-control-configure.md).

Le verrou est appliqué à la ressource spécifiée et de toutes les ressources enfants.

Vous pouvez supprimer un verrou avec la commande PowerShell **Supprimer-AzureRmResourceLock** ou avec l' [opération de suppression](https://msdn.microsoft.com/library/azure/mt204562.aspx) de l’API REST.

## <a name="examples"></a>Exemples

L’exemple suivant applique un verrou ne peut pas supprimer une application web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

L’exemple suivant applique un verrou ne peut pas supprimer le groupe de ressources.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la structure du modèle, consultez [Création d’un gestionnaire de ressources Azure modèles](resource-group-authoring-templates.md).
- Pour plus d’informations sur les verrous, reportez-vous à la section [ressources de verrouillage avec le Gestionnaire de ressources Azure](resource-group-lock-resources.md).
