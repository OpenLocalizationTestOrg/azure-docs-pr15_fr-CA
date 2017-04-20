<properties
    pageTitle="À l’aide de balises pour organiser vos ressources Azure | Microsoft Azure"
    description="Montre comment appliquer des balises pour organiser les ressources de facturation et de gestion."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="AzurePortal"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="tomfitz"/>


# <a name="using-tags-to-organize-your-azure-resources"></a>À l’aide de balises pour organiser vos ressources Azure

Le Gestionnaire de ressources vous permet d’organiser logiquement les ressources en appliquant des balises. Les balises sont constituées de paires clé/valeur qui identifient les ressources avec les propriétés que vous définissez. Pour marquer des ressources comme appartenant à la même catégorie, appliquer la même balise à ces ressources.

Lorsque vous affichez les ressources avec une balise particulière, vous consultez les ressources de tous les groupes de ressources. Vous n’êtes pas limité aux seules ressources dans le même groupe de ressources, ce qui vous permet d’organiser vos ressources de manière indépendante les relations de déploiement. Balises peuvent être utiles lorsque vous devez organiser les ressources pour la facturation ou de gestion.

Chaque balise que vous ajoutez à une ressource ou un groupe de ressources est automatiquement ajouté à la taxonomie à l’échelle de l’abonnement. Vous pouvez également préremplir la taxonomie pour votre abonnement avec les noms de balises et de valeurs que vous souhaitez utiliser comme ressources sont balisés à l’avenir.

Chaque ressource ou un groupe de ressources peut avoir un maximum de 15 balises. Le nom de balise est limité à 512 caractères, et la valeur de la balise est limitée à 256 caractères.

> [AZURE.NOTE] Vous ne pouvez appliquer des balises à des ressources qui prennent en charge les opérations du Gestionnaire de ressources. Si vous avez créé un ordinateur virtuel, un réseau virtuel ou un stockage via le modèle de déploiement classique (par exemple via le portail classique), vous ne pouvez pas appliquer une balise à cette ressource. Pour prendre en charge le marquage, redéployer ces ressources par le biais du Gestionnaire de ressources. Toutes les autres ressources prend en charge le marquage.

## <a name="templates"></a>Modèles

Pour baliser une ressource au cours du déploiement, ajoutez l’élément de **balises** à la ressource que vous déployez et fournir le nom de la balise et la valeur. Le nom de la balise et la valeur n’avez pas besoin d’exister préalablement dans votre abonnement. Vous pouvez fournir jusqu'à 15 mots-clés pour chaque ressource.

L’exemple suivant montre un compte de stockage avec une balise.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Actuellement, le Gestionnaire de ressources ne gère pas un objet pour les noms de balises et les valeurs de traitement. Au lieu de cela, passez un objet pour les valeurs de la balise, mais vous devez toujours spécifier les noms de balises, comme illustré dans l’exemple suivant.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## <a name="portal"></a>Portail

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>CLI Azure

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>API REST

Le portail et le PowerShell utilisent l' [REST API de gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn848368.aspx) en tâche de fond. Si vous avez besoin intégrer le balisage dans un autre environnement, vous pouvez obtenir des balises avec une opération d’obtention de l’id de ressource et mettre à jour le jeu de balises avec un appel de correctif.


## <a name="tags-and-billing"></a>Balises et facturation

Pour connaître les services pris en charge, vous pouvez utiliser des balises pour regrouper les données de facturation. Par exemple, [Machines virtuelles intégrées avec le Gestionnaire de ressources Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) permettent de définir et d’appliquer des balises pour organiser l’utilisation de la facturation pour les machines virtuelles. Si vous exécutez plusieurs ordinateurs virtuels pour différentes organisations, vous pouvez utiliser les balises à l’utilisation des groupes par centre de coût.  
Vous pouvez également utiliser des balises pour classer les coûts par l’environnement d’exécution ; par exemple, l’utilisation de la facturation pour les machines virtuelles en cours d’exécution dans l’environnement de production.

Vous pouvez récupérer des informations sur les balises par le biais de l' [utilisation des ressources Azure et API de RateCard](billing-usage-rate-card-overview.md) ou de l’utilisation de fichier de valeurs séparées par des virgules (CSV). Vous téléchargez le fichier d’utilisation à partir du [portail de comptes Azure](https://account.windowsazure.com/) ou un [portail EA](https://ea.azure.com). Pour plus d’informations sur l’accès par programme aux informations de facturation, voir [intégrer votre consommation de ressources de Microsoft Azure](billing-usage-rate-card-overview.md). Pour les opérations de l’API REST, consultez [Référence des API reste facturation Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Lorsque vous téléchargez l’utilisation CSV pour les services qui prennent en charge les balises avec la facturation, les balises apparaissent dans la colonne de **balises** . Pour plus d’informations, voir [comprendre votre facture Microsoft Azure](billing/billing-understand-your-bill.md).

![Reportez-vous à la section balises dans facturation](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez appliquer des conventions et des restrictions sur votre abonnement avec des stratégies personnalisées. La stratégie que vous définissez peut nécessiter que toutes les ressources ont une valeur pour une balise particulière. Pour plus d’informations, voir [Utiliser une stratégie pour la gestion des ressources et de contrôler l’accès](resource-manager-policy.md).
- Pour une introduction à l’utilisation de PowerShell d’Azure lors du déploiement de ressources, reportez-vous [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](./powershell-azure-resource-manager.md).
- Pour une présentation à l’aide de la CLI de Azure lors du déploiement de ressources, reportez-vous [à l’aide de la CLI Azure pour Mac, Linux et Windows Azure Gestion des ressources](./xplat-cli-azure-resource-manager.md).
- Pour une introduction à l’utilisation du portail, consultez [l’aide du portail Azure pour gérer vos ressources Azure](./azure-portal/resource-group-portal.md)  
