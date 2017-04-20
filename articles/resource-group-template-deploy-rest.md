<properties
   pageTitle="Déployer des ressources avec l’API REST et modèle | Microsoft Azure"
   description="Utilisez le Gestionnaire de ressources Azure et REST API de gestionnaire de ressources déployer une ressources vers Azure. Les ressources sont définies dans un modèle de gestionnaire de ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Déployer les ressources avec le Gestionnaire de ressources de modèles et le REST API de gestionnaire de ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cet article explique comment utiliser le REST API de gestionnaire de ressources avec les modèles du Gestionnaire de ressources pour déployer vos ressources vers Azure.  

> [AZURE.TIP] Pour vous aider à déboguer une erreur pendant le déploiement, voir :
>
> - [Afficher les opérations de déploiement avec l’API REST](resource-manager-troubleshoot-deployments-rest.md) pour en savoir plus sur l’obtention des informations qui vous aident à résoudre les problèmes de votre erreur.
> - [Résoudre les erreurs courantes lors du déploiement de ressources vers Azure avec le Gestionnaire de ressources Azure](resource-manager-common-deployment-errors.md) pour savoir comment résoudre les erreurs de déploiement courantes

Votre modèle peut être un fichier local ou un fichier externe qui est disponible via un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature (SAS) d’un accès partagé au cours du déploiement.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Déployer avec l’API REST
1. Définir [des en-têtes et des paramètres courants](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), y compris les jetons d’authentification.
2. Si vous ne disposez pas d’un groupe de ressources existant, créez un groupe de ressources. Fournir votre id d’abonnement, le nom du nouveau groupe de ressources et emplacement dont vous avez besoin pour votre solution. Pour plus d’informations, voir [Création d’un groupe de ressources](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Validez votre déploiement avant de l’exécuter en exécutant l’opération [valider un déploiement du modèle](https://msdn.microsoft.com/library/azure/dn790547.aspx) . Lorsque vous testez le déploiement, fournir des paramètres exactement comme vous le feriez lors de l’exécution du déploiement (illustré à l’étape suivante).

3. Créer un déploiement. Fournir votre id d’abonnement, le nom du groupe de ressources à déployer, le nom du déploiement et un lien pour votre modèle. Pour plus d’informations sur le fichier de modèle, consultez le [fichier de paramètres](#parameter-file). Pour plus d’informations sur l’API REST pour créer un groupe de ressources, consultez [créer un modèle de déploiement](https://msdn.microsoft.com/library/azure/dn790564.aspx). Notez que le **mode** a la valeur **incrémentielle**. Pour exécuter un déploiement complet, définissez le **mode** sur **terminé**. Soyez prudent lorsque vous utilisez le mode complet que vous pouvez supprimer par inadvertance de ressources qui ne sont pas dans votre modèle.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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
              }
            }
          }
   
      Si vous souhaitez enregistrer le contenu de la réponse, contenu de la demande, ou les deux, sont **debugSetting** dans la demande.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      Vous pouvez configurer votre compte de stockage pour utiliser un jeton de signature (SAS) d’un accès partagé. Pour plus d’informations, consultez [Délégation de l’accès avec une Signature d’accès partagé](https://msdn.microsoft.com/library/ee395415.aspx).

4. Obtenir l’état du déploiement du modèle. Pour plus d’informations, consultez [obtenir les informations sur le déploiement d’un modèle](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, reportez-vous à la section [ressources de déploiement à l’aide de bibliothèques .NET et un modèle](virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour définir les paramètres de modèle, consultez [modèles de création](resource-group-authoring-templates.md#parameters).
- Pour obtenir des instructions sur le déploiement de votre solution aux différents environnements, consultez [développement et des environnements de test de Microsoft Azure](solution-dev-test-environments.md).
- Pour plus d’informations sur l’utilisation d’une référence KeyVault pour passer des valeurs sécurisées, voir [passer des valeurs sécurisées pendant le déploiement](resource-manager-keyvault-parameter.md).
