<properties
   pageTitle="Modèle de gestionnaire de ressources de stockage | Microsoft Azure"
   description="Affiche le schéma du Gestionnaire de ressources pour le déploiement des comptes de stockage via un modèle."
   services="azure-resource-manager,storage"
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
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="storage-account-template-schema"></a>Schéma de modèle de compte de stockage

Crée un compte de stockage.

## <a name="schema-format"></a>Format de schéma

Pour créer un compte de stockage, ajoutez le schéma suivant à la section de ressources de votre modèle.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- |
| type de | Enum<br />Obligatoire<br />**Microsoft.Storage/storageAccounts**<br /><br />Le type de ressource à créer. |
| apiVersion | Enum<br />Obligatoire<br />**2015-06-15** ou **2015-05-01-aperçu**<br /><br />La version de l’API à utiliser pour la création de la ressource. | 
| nom | Chaîne<br />Obligatoire<br />Entre 3 et 24 caractères, uniquement des chiffres et des lettres minuscules.<br /><br />Le nom du compte de stockage à créer. Le nom doit être unique dans l’ensemble d’Azure. Envisagez d’utiliser la fonction [uniqueString](resource-group-template-functions.md#uniquestring) avec votre convention d’affectation de noms comme indiqué dans l’exemple ci-dessous. |
| emplacement | Chaîne<br />Obligatoire<br />Une zone qui prend en charge les comptes de stockage. Pour déterminer les zones valides, consultez [prise en charge des régions](resource-manager-supported-services.md#supported-regions).<br /><br />La région pour le compte de stockage de l’hôte. |
| propriétés | Objet<br />Obligatoire<br />[objet de propriétés](#properties)<br /><br />Objet qui spécifie le type de compte de stockage à créer. |

<a id="properties" />
### <a name="properties-object"></a>objet de propriétés

| Nom | Valeur |
| ---- | ---- | 
| accountType | Chaîne<br />Obligatoire<br />**Standard_LRS**, **Standard_ZRS**, **Standard_GRS**, **Standard_RAGRS**ou **Premium_LRS**<br /><br />Le type de compte de stockage. Les valeurs autorisées correspondent aux Standard localement redondants, Standard Zone redondants, géo-redondants Standard, Standard-accès en lecture géo-redondants et Premium localement redondants. Pour plus d’informations sur ces types de compte, voir [réplication de stockage Azure](./storage/storage-redundancy.md ). |

    
## <a name="examples"></a>Exemples

L’exemple suivant déploie un compte de stockage Standard localement redondants avec un nom unique basé sur l’id de groupe de ressources.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Il existe de nombreux modèles de démarrage rapide qui incluent un compte de stockage. Les modèles suivants illustrent quelques scénarios courants :

- [Créer un compte de stockage Standard](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Déploiement simple d’une machine virtuelle de Windows](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Déploiement simple d’une VM Linux](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [Créer un profil CDN, un point de terminaison CDN avec un compte de stockage sous la forme d’origine](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [Créer une batterie de serveurs SharePoint Availabilty élevé avec 9 VM à l’aide de l’Extension de DSC Powershell](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [Déploiement simple de 5 nœud secure Fabric de Service de Cluster avec WAD activé](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Créer un ordinateur virtuel à partir d’une Image Windows avec 4 disques de données vide](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales sur le stockage, consultez [Introduction au stockage Azure de Microsoft](./storage/storage-introduction.md).
- Par exemple, les modèles qui utilisent un compte de stockage avec une Machine virtuelle, voir [déployer Linux VM simple](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) ou [déployer un ordinateur virtuel de Windows simple](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).
