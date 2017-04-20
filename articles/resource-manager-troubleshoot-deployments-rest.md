<properties
   pageTitle="Afficher les opérations de déploiement avec l’API REST | Microsoft Azure"
   description="Décrit comment utiliser le REST API de gestionnaire de ressources Azure pour détecter les problèmes de déploiement du Gestionnaire de ressources."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-resource-manager-rest-api"></a>Afficher les opérations de déploiement avec le REST API de gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Si vous avez reçu une erreur lors du déploiement des ressources dans Azure, vous souhaiterez afficher plus de détails sur les opérations de déploiement qui ont été exécutées. L’API REST fournit des opérations qui vous permettent de rechercher les erreurs et de déterminer les éventuels correctifs.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Vous pouvez éviter certaines erreurs en validant votre modèle et votre infrastructure avant le déploiement. Vous pouvez également vous connecter demande supplémentaire et des informations de réponse au cours du déploiement qui peut être utile ultérieurement pour la résolution des problèmes. Pour en savoir plus sur la validation et les informations de consignation des demandes et de réponses, voir [déploiement d’un groupe de ressources avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy-rest.md).

## <a name="troubleshoot-with-rest-api"></a>Résoudre les problèmes avec l’API REST

1. Déployer vos ressources avec l’opération de [Création d’un déploiement du modèle](https://msdn.microsoft.com/library/azure/dn790564.aspx) . Pour conserver les informations qui peuvent être utiles pour le débogage, définir la propriété **debugSetting** de demande JSON à **requestContent** ou **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    Par défaut, la valeur **debugSetting** est définie sur **Aucun**. Lorsque vous spécifiez la valeur **debugSetting** , avec soin le type d’informations que vous transmettez dans pendant le déploiement. Par l’enregistrement d’informations sur la demande ou la réponse, vous pouvez potentiellement exposer des données sensibles sont récupérées par les opérations de déploiement. 

2. Obtenir des informations sur un déploiement à l’opération [d’obtenir des informations sur le déploiement d’un modèle](https://msdn.microsoft.com/library/azure/dn790565.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    Dans la réponse, notez en particulier les éléments **provisioningState** , **ID de corrélation** et **d’erreur** . L' **ID de corrélation** est utilisé pour effectuer le suivi des événements associés et peut s’avérer utile lorsque vous travaillez avec le support technique pour résoudre un problème.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
            }  
          }
        }

3. Obtenir des informations sur les opérations de déploiement avec le fonctionnement de [toutes les opérations de déploiement de modèle de liste](https://msdn.microsoft.com/library/azure/dn790518.aspx) . 

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    La réponse inclut des informations de demande ou de réponse en fonction de ce que vous spécifié dans la propriété **debugSetting** pendant le déploiement.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Obtenir des événements à partir des journaux d’audit pour le déploiement avec l’opération [répertorie les événements de gestion dans un abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## <a name="next-steps"></a>Étapes suivantes

- Pour l’aide sur la résolution des erreurs de déploiement particulier, consultez [résoudre les erreurs courantes lors du déploiement de ressources vers Azure avec le Gestionnaire de ressources Azure](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, voir [Auditer les opérations effectuées avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant de l’exécuter, voir [déploiement d’un groupe de ressources avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy.md).
