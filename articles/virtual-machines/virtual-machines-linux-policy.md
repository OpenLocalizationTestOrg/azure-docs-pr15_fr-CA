<properties
    pageTitle="Appliquer des stratégies pour Azure le Gestionnaire de ressources Virtual Machines | Microsoft Azure"
    description="Comment appliquer une stratégie à une Machine virtuelle Azure le Gestionnaire de ressources Linux"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/13/2016"
    ms.author="singhkay"/>

# <a name="apply-policies-to-azure-resource-manager-virtual-machines"></a>Appliquer des stratégies pour Azure le Gestionnaire de ressources Virtual Machines

En utilisant des stratégies, une organisation peut appliquer différentes conventions et règles de l’entreprise. Application du comportement souhaité peut aider à atténuer les risques tout en contribuant à la réussite de l’organisation. Dans cet article, nous décrirons comment vous pouvez utiliser des stratégies de gestionnaire de ressources Azure pour définir le comportement de votre choix pour les ordinateurs virtuels de votre organisation.

Le plan de la procédure pour cela est comme ci-dessous

1. Stratégie de gestionnaire de ressources Azure 101
2. Définir une stratégie pour votre Machine virtuelle
3. Créez la stratégie
4. Appliquer la stratégie

## <a name="azure-resource-manager-policy-101"></a>Stratégie de gestionnaire de ressources Azure 101

Pour la mise en route de stratégies du Gestionnaire de ressources Azure, nous vous recommandons la lecture de l’article ci-dessous puis poursuivre les étapes de cet article. L’article ci-dessous décrit la définition de base et la structure d’une stratégie, comment les stratégies évaluée et fournit divers exemples de définitions de stratégie.

* [Utiliser la stratégie pour la gestion des ressources et de contrôler l’accès](../resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Définir une stratégie pour votre Machine virtuelle

L’un des scénarios courants pour une entreprise peut être pour autoriser uniquement les utilisateurs à créer des ordinateurs virtuels à partir des systèmes d’exploitation spécifiques qui ont été testés pour être compatible avec une application métier. À l’aide d’une stratégie du Gestionnaire de ressources Azure cette tâche peut être accomplie en quelques étapes. Dans cet exemple de stratégie, nous allons permettre uniquement Ubuntu 14.04.2-LTS des ordinateurs virtuels à créer. La définition de stratégie se présente comme ci-après

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "Canonical"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "UbuntuServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "14.04.2-LTS"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

La stratégie ci-dessus peut facilement être modifiée pour un scénario dans lequel vous pouvez souhaiter autoriser n’importe quelle image d’Ubuntu LTS à utiliser pour un déploiement d’ordinateur virtuel avec le dessous de modification

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

#### <a name="virtual-machine-property-fields"></a>Champs de propriétés de Machine virtuelle

Le tableau ci-dessous décrit les propriétés de l’ordinateur virtuel qui peuvent être utilisées en tant que champs dans la définition de la stratégie. Pour plus d’informations sur les champs de stratégie, consultez l’article ci-dessous :

* [Champs et Sources](../resource-manager-policy.md#fields-and-sources)


| Nom de champ     | Description                                        |
|----------------|----------------------------------------------------|
| imagePublisher | Spécifie l’éditeur de l’image               |
| imageOffer     | Spécifie l’offre pour l’éditeur d’image sélectionné |
| imageSku       | Spécifie la référence SKU de l’offre choisie             |
| imageVersion   | Spécifie la version de l’image de référence sélectionné     |

## <a name="create-the-policy"></a>Créez la stratégie

Une stratégie peut facilement être créée à l’aide de l’API REST directement ou les applets de commande PowerShell. Pour créer la stratégie, consultez l’article ci-dessous :

* [Création d’une stratégie](../resource-manager-policy.md#creating-a-policy)


## <a name="apply-the-policy"></a>Appliquer la stratégie

Après avoir créé la stratégie, vous devrez l’appliquer sur une étendue définie. La portée peut être un abonnement, groupe de ressources ou même la ressource. Pour l’application de la stratégie, consultez l’article ci-dessous :

* [Création d’une stratégie](../resource-manager-policy.md#applying-a-policy)
