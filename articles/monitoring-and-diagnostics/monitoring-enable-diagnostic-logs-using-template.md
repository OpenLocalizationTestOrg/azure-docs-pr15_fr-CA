<properties
    pageTitle="Activer automatiquement les paramètres de Diagnostic à l’aide d’un modèle de gestionnaire de ressources | Microsoft Azure"
    description="Apprenez à utiliser un modèle de gestionnaire de ressources pour créer les paramètres de diagnostic qui vous permet de diffuser vos journaux de diagnostic à des concentrateurs d’événement ou de les stocker dans un compte de stockage."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Activer automatiquement les paramètres de Diagnostic lors de la création de ressources à l’aide d’un modèle de gestionnaire de ressources
Dans cet article, nous montrons comment vous pouvez utiliser un [modèle de gestionnaire de ressources Azure](../resource-group-authoring-templates.md) pour configurer les paramètres de Diagnostic sur une ressource lorsqu’elle est créée. Cela vous permet de démarrer automatiquement la diffusion en continu de vos journaux de Diagnostic et les mesures à des concentrateurs d’événement, les archiver dans un compte de stockage, ou de les envoyer au journal Analytique lorsqu’une ressource est créée.

La méthode d’activation des journaux de Diagnostic à l’aide d’un modèle de gestionnaire de ressources dépend du type de ressource.

- **Compute non** ressources (par exemple, les groupes de sécurité réseau, applications de logique, Automation) utilisent [les paramètres de Diagnostic décrits dans cet article](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
- **Calculer** Ressources de (WAD/SCÉNARISTE basée) utilisent le [fichier de configuration WAD/SCÉNARISTE décrit dans cet article](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Dans cet article, nous décrivons comment configurer des diagnostics à l’aide de des deux méthodes.

Les étapes de base sont les suivantes :

1. Créer un modèle sous la forme d’un fichier JSON qui décrit comment créer la ressource et activer des tests de diagnostic.
2. [Déployer le modèle à l’aide de n’importe quelle méthode de déploiement](../resource-group-template-deploy.md).

Ci-dessous, nous donner un exemple du fichier modèle JSON que vous devez générer sans calcul des ressources de calcul.

## <a name="non-compute-resource-template"></a>Modèle de calcul non ressource
Pour les ressources sans calcul, vous devez faire deux choses :

1. Ajouter des paramètres à l’objet blob de paramètres pour le nom du compte de stockage, ID de règle de bus de service et/ou des ID d’espace de travail OMS journal Analytique (activation archivage des journaux de diagnostics dans un compte de stockage, en flux continu de journaux d’événements concentrateurs, et/ou l’envoi de journaux pour le journal Analytique).

    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Dans le tableau ressources de la ressource pour laquelle vous souhaitez activer les journaux de Diagnostic, ajouter une ressource de type `[resource namespace]/providers/diagnosticSettings`.

    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

Le blob de propriétés pour le paramètre Diagnostic suit [le format décrit dans cet article](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Voici un exemple complet qui crée un groupe de sécurité réseau et Active la lecture en continu vers les concentrateurs d’événement et de stockage dans un compte de stockage.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Calculer le modèle de ressource
Pour activer les tests de diagnostic sur une ressource de calcul, par exemple un ordinateur virtuel ou le Service Fabric du cluster, vous devez :

1. Ajouter l’extension de Diagnostics d’Azure à la définition de ressource d’ordinateur virtuel.
2. Spécifiez un concentrateur de stockage compte et/ou des événements en tant que paramètre.
3. Ajouter le contenu de votre fichier XML de WADCfg dans la propriété XMLCfg, la séquence d’échappement tous les caractères XML correctement.

> [AZURE.WARNING] Cette dernière étape peut être délicate. [Consultez cet article](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) pour obtenir un exemple qui fractionne le schéma de Configuration de Diagnostics dans des variables qui sont une séquence d’échappement et correctement mis en forme.

L’ensemble du processus, notamment les exemples, est décrite [dans ce document](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).


## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les journaux de Diagnostic Azure](./monitoring-overview-of-diagnostic-logs.md)
- [Flux de données des journaux de Diagnostic Azure à des concentrateurs d’événement](./monitoring-stream-diagnostic-logs-to-event-hubs.md)
