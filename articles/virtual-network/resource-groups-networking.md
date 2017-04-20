<properties
   pageTitle="Vue d’ensemble du fournisseur de ressources réseau | Microsoft Azure"
   description="En savoir plus sur le nouveau fournisseur de ressources de réseau dans le Gestionnaire de ressources Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="network-resource-provider"></a>Fournisseur de ressources de réseau
Un besoin sous-jacente dans la réussite de l’entreprise d’aujourd'hui, est la possibilité de créer et de gérer des applications prenant en charge de réseau à grande échelle d’une manière agile, flexible, sécurisée et reproductible. Le Gestionnaire de ressources Azure (ARM) vous permet de créer de telles applications, comme un ensemble unique de ressources de groupes ressources. Ces ressources sont gérées par l’intermédiaire de différents fournisseurs de ressources sous ARM.

Azure le Gestionnaire de ressources s’appuie sur des fournisseurs de ressources différents pour fournir l’accès à vos ressources. Il existe trois fournisseurs de ressources principal : réseau, de stockage et de calcul. Ce document décrit les caractéristiques et les avantages d’un fournisseur de ressources réseau, y compris :

- **Métadonnées** – vous pouvez ajouter des informations aux ressources à l’aide de balises. Ces balises peuvent être utilisées pour effectuer le suivi de l’utilisation des ressources sur les abonnements et les groupes de ressources.
- **Meilleur contrôle de votre réseau** - réseau de ressources sont faiblement couplés et vous pouvez les contrôler de manière plus granulaire. Cela signifie que vous disposez d’une plus grande souplesse dans la gestion des ressources réseau.
- **Configuration plus rapide** - car les ressources du réseau sont faiblement couplés, vous pouvez créer et gérer les ressources réseau en parallèle. Ceci a considérablement réduit le temps de configuration.
- **Contrôle d’accès rôle** - RBAC fournit des rôles par défaut, avec une étendue de sécurité spécifique, en plus de permettre la création de rôles personnalisés pour une gestion sécurisée.
- **Gestion plus facile et le déploiement** , il est plus facile à déployer et à gérer des applications dans la mesure où vous pouvez pouvez créer une pile d’ensemble de l’application comme un ensemble unique de ressources dans un groupe de ressources. Et plus rapides à déployer, dans la mesure où vous pouvez déployer en fournissant une charge utile JSON de modèle.
- **Personnalisation rapide** - vous pouvez utiliser des modèles de style déclaratif pour activer la personnalisation rapide et reproductible des déploiements.
- **Personnalisation reproductible** - vous pouvez utiliser des modèles de style déclaratif pour activer la personnalisation rapide et reproductible des déploiements.
- **Interfaces de gestion** , vous pouvez utiliser une des interfaces suivantes pour gérer vos ressources :
    - RESTE en fonction des API
    - PowerShell
    - KIT DE DÉVELOPPEMENT .NET
    - Kit de développement logiciel Node.JS
    - Java SDK
    - CLI Azure
    - Portail de l’aperçu
    - Langue du modèle ARM

## <a name="network-resources"></a>Ressources réseau
Vous pouvez désormais gérer les ressources réseau de façon indépendante, au lieu de les gérés via une ressource de calcul unique (une machine virtuelle). Cela garantit un degré plus élevé de flexibilité et de souplesse dans la composition d’une infrastructure d’échelle complexes et de grande taille dans un groupe de ressources.

Une vue conceptuelle d’un exemple de déploiement impliquant une application multicouche est présentée ci-dessous. Chaque ressource que vous consultez, par exemple des cartes réseau, les adresses IP publiques et les ordinateurs virtuels, peut être géré séparément.

![Modèle de ressource réseau](./media/resource-groups-networking/Figure2.png)

Chaque ressource contient un jeu commun de propriétés et leur propriété individuelle définie. Les propriétés communes sont les suivantes :

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**nom**|Nom de ressource unique. Chaque type de ressource possède ses propres restrictions d’affectation de noms.|PIP01, VM01, NIC01|
|**emplacement**|Azure région dans laquelle se trouve la ressource|westus, eastus|
|**ID**|Identification des URI de base unique|/Subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

Vous pouvez vérifier les propriétés individuelles des ressources dans les sections ci-dessous.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Interfaces de gestion
Vous pouvez gérer vos ressources réseau Azure à l’aide des interfaces différentes. Dans ce document nous allons nous concentrer sur les câbles de ces interfaces : API REST et les modèles.

### <a name="rest-api"></a>API REST
Comme mentionné précédemment, les ressources réseau peuvent être gérés via une variété d’interfaces, notamment l’API REST, Kit de développement .NET, Node.JS SDK, Kit de développement logiciel Java, PowerShell, CLI, Azure Portal et modèles.

ELLE reste conforme à la spécification du protocole HTTP 1.1. La structure générale de URI de l’API est présentée ci-dessous :

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Et les paramètres d’accolades représentent les éléments suivants :

- **id de l’abonnement** - votre id d’abonnement Azure.
- **namespace du fournisseur de ressources** - espace de noms pour le fournisseur utilisé. La valeur de fournisseur de ressources réseau est *Microsoft.Network*.
- **nom de la région** - le nom de la région Azure

Les méthodes HTTP suivantes sont prises en charge lors d’appels à l’API REST :

- **Placer** - permet de créer une ressource d’un type donné, modifier une propriété de ressource ou changer une association entre les ressources.
- **GET** - permet de récupérer les informations d’une ressource de mise en service.
- **Supprimer** - permet de supprimer une ressource existante.

La demande et la réponse est conforme à un format de charge utile JSON. Pour plus d’informations, consultez [Azure des API de gestion de ressources](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="arm-template-language"></a>Langue du modèle ARM
Outre la gestion des ressources de manière impérative (via API ou SDK), vous pouvez également utiliser un style de programmation déclaratif pour générer et gérer les ressources réseau en utilisant le langage du modèle ARM.

Une représentation de l’exemple d’un modèle est fournie ci-dessous :

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

Le modèle est essentiellement une description JSON des ressources et les valeurs d’instance injectées via des paramètres. L’exemple ci-dessous peut être utilisé pour créer un réseau virtuel avec 2 sous-réseaux.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Vous avez la possibilité de fournir les valeurs de paramètre de manuellement lors de l’utilisation d’un modèle, ou vous pouvez utiliser un fichier de paramètres. L’exemple ci-dessous illustre un jeu possible de valeurs de paramètre à utiliser avec le modèle ci-dessus :

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


Les principaux avantages de l’utilisation de modèles sont :

- Vous pouvez créer une infrastructure complexe dans un groupe de ressources dans un style déclaratif. L’orchestration de la création de ressources, notamment la gestion des dépendances, est gérée par ARM.
- L’infrastructure peut être créé de façon reproductible sur différentes régions et au sein d’une région en modifiant simplement les paramètres.
- Le style déclaratif conduit au plus court délai dans la création de modèles et de déploiement de l’infrastructure.

Pour des exemples de modèles, voir [modèles d’Azure quickstart](https://github.com/Azure/azure-quickstart-templates).

Pour plus d’informations sur la langue du modèle ARM, reportez-vous à la section [Langue du modèle de gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

Le modèle de l’exemple ci-dessus utilise le réseau virtuel et les ressources du sous-réseau. Il existe autres ressources réseau que vous pouvez utiliser comme indiqué ci-dessous :

### <a name="using-a-template"></a>À l’aide d’un modèle

Vous pouvez déployer des services Azure à partir d’un modèle à l’aide de PowerShell, AzureCLI, ou en effectuant un clic pour déployer à partir de GitHub. Pour déployer les services à partir d’un modèle de GitHub, exécutez les étapes suivantes :

1. Ouvrez le fichier template3 à partir de GitHub. Par exemple, ouvrir le [réseau virtuel avec deux sous-réseaux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Cliquez sur **déployer vers Azure**et puis connectez-vous sur le portail Azure avec vos informations d’identification.
3. Vérifiez que le modèle, puis cliquez sur **Enregistrer**.
4. Cliquez sur **Modifier les paramètres de** , sélectionnez un emplacement, tel *États-Unis Ouest*, pour les vnet et les sous-réseaux.
5. Si nécessaire, modifiez les paramètres **ADDRESSPREFIX** et **SUBNETPREFIX** , puis cliquez sur **OK**.
6. Cliquez sur **Sélectionner un groupe de ressources** et puis cliquez sur le groupe de ressources que vous souhaitez ajouter les sous-réseaux à vnet. Vous pouvez également créer un nouveau groupe de ressources en cliquant sur **ou créer de nouveaux**.
3. Cliquez sur **créer**. Notez la mosaïque affichage de **déploiement de modèle de mise en service**. Une fois que le déploiement est terminé, vous verrez un écran similaire à celle ci-dessous.

![Exemple de déploiement de modèle](./media/resource-groups-networking/Figure6.png)


## <a name="next-steps"></a>Étapes suivantes

[Langue du modèle de gestionnaire de ressources Azure](../resource-group-authoring-templates.md)

[Mise en réseau Azure – des modèles couramment utilisés](https://github.com/Azure/azure-quickstart-templates)

[Fournisseur de ressources de calcul](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

[Vue d’ensemble de Gestionnaire des ressources Azure](../azure-resource-manager/resource-group-overview.md)
