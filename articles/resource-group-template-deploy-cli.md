<properties
   pageTitle="Déployer des ressources avec la CLI d’Azure et modèle | Microsoft Azure"
   description="Utiliser le Gestionnaire de ressources Azure et d’Azure CLI pour déployer une ressources Azure. Les ressources sont définies dans un modèle de gestionnaire de ressources."
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Déployer des ressources à l’aide des modèles du Gestionnaire de ressources et de la CLI d’Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cette rubrique explique comment utiliser Azure CLI avec les modèles du Gestionnaire de ressources pour déployer vos ressources vers Azure.  

> [AZURE.TIP] Pour vous aider à déboguer une erreur pendant le déploiement, voir :
>
> - [Afficher les opérations de déploiement avec Azure CLI](resource-manager-troubleshoot-deployments-cli.md) pour en savoir plus sur l’obtention des informations qui vous aident à résoudre les problèmes de votre erreur.
> - [Résoudre les erreurs courantes lors du déploiement de ressources vers Azure avec le Gestionnaire de ressources Azure](resource-manager-common-deployment-errors.md) pour savoir comment résoudre les erreurs de déploiement courantes

Votre modèle peut être un fichier local ou un fichier externe qui est disponible via un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature (SAS) d’un accès partagé au cours du déploiement.

## <a name="quick-steps-to-deployment"></a>Étapes à suivre pour le déploiement

Cet article décrit toutes les différentes options disponibles au cours du déploiement. Toutefois, souvent vous devez uniquement deux commandes. Pour vous familiariser rapidement avec le déploiement, utilisez les commandes suivantes :

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

Pour en savoir plus sur les options de déploiement qui peuvent être mieux adaptés à votre scénario, poursuivre la lecture de cet article.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-azure-cli"></a>Déployer avec l’interface CLI Azure

Si vous n'avez pas déjà utilisé Azure CLI avec le Gestionnaire de ressources, reportez-vous [à l’aide de la CLI Azure pour Mac, Linux et Windows Azure Gestion des ressources](xplat-cli-azure-resource-manager.md).

1. Connectez-vous à votre compte Azure. Après avoir fourni vos informations d’identification, la commande renvoie le résultat de votre connexion.

        azure login
  
        ...
        info:    login command OK

2. Si vous disposez de plusieurs abonnements, fournissez l’id d’abonnement que vous souhaitez utiliser pour le déploiement.

        azure account set <YourSubscriptionNameOrId>

3. Basculez vers le module Gestionnaire de ressource Azure. Vous recevez la confirmation du nouveau mode.

        azure config mode arm
   
        info:     New mode is arm

4. Si vous ne disposez pas d’un groupe de ressources existant, créez un groupe de ressources. Indiquez le nom du groupe de ressources et emplacement dont vous avez besoin pour votre solution. Vous devez fournir un emplacement pour le groupe de ressources, car le groupe de ressources stocke des métadonnées concernant les ressources. Pour des raisons de conformité, vous souhaiterez spécifier où les métadonnées sont stockées. En général, nous recommandons que vous spécifiez un emplacement dans lequel se trouve la plupart de vos ressources. À partir du même emplacement permet de simplifier votre modèle.

        azure group create -n ExampleResourceGroup -l "West US"

     Un résumé du nouveau groupe de ressources est retourné.
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Validez votre déploiement avant de l’exécuter en exécutant la commande **validation du modèle de groupe azure** . Lorsque vous testez le déploiement, fournir des paramètres exactement comme vous le feriez lors de l’exécution du déploiement (illustré à l’étape suivante).

        azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup

5. Pour déployer des ressources à votre groupe de ressources, exécutez la commande suivante et fournir les paramètres nécessaires. Les paramètres incluent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL pour le modèle et d’autres paramètres nécessaires pour votre scénario. 
   
     Vous avez trois options pour fournir des valeurs de paramètre suivantes : 

     1. Utilisez les paramètres inline et un modèle local. Chaque paramètre est au format : `"ParameterName": { "value": "ParameterValue" }`. L’exemple suivant affiche les paramètres avec des caractères d’échappement.

            azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Utilisez les paramètres inline et un lien vers un modèle.

            azure group deployment create --template-uri <LinkToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Utiliser un fichier de paramètres. Pour plus d’informations sur le fichier de modèle, consultez le [fichier de paramètres](#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Une fois que les ressources ont été déployés via l’une des trois méthodes ci-dessus, vous allez voir un récapitulatif du déploiement.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Pour exécuter un déploiement complet, définissez le **mode** sur **terminé**. Soyez prudent lorsque vous utilisez ce mode que vous pouvez supprimer par inadvertance de ressources qui ne sont pas dans votre modèle.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Si vous souhaitez enregistrer des informations supplémentaires sur le déploiement qui peut-être vous aider à résoudre les erreurs de déploiement, utilisez le paramètre de **débogage** . Vous pouvez spécifier que le contenu de la demande, le contenu de la réponse ou les deux être enregistrés avec l’opération de déploiement.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## <a name="deploy-template-from-storage-with-sas-token"></a>Déployer le modèle de stockage avec jeton SAS

Vous pouvez ajouter vos modèles à un compte de stockage et d’un lien pendant le déploiement avec un jeton d’associations de sécurité.

> [AZURE.IMPORTANT] En suivant les étapes ci-dessous, le blob qui contient le modèle est accessible uniquement au propriétaire du compte. Toutefois, lorsque vous créez un jeton SAS pour le blob, le blob est accessible à toute personne disposant de cet URI. Si un autre utilisateur intercepte l’URI, cet utilisateur peut accéder au modèle. À l’aide d’un jeton SAS est un bon moyen de limiter l’accès à vos modèles, mais vous ne devez pas inclure les données sensibles telles que des mots de passe directement dans le modèle.

### <a name="add-private-template-to-storage-account"></a>Ajouter un modèle privé à un compte de stockage

Les étapes suivantes configurent un compte de stockage pour les modèles :

1. Créer un groupe de ressources.

        azure group create -n "ManageGroup" -l "westus"

2. Créer un compte de stockage. Le nom de compte de stockage doit être unique dans Azure, donc fournir votre propre nom pour le compte.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. Permet de définir des variables pour le compte de stockage et de la clé.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. Créer un conteneur. L’autorisation est définie à **Off** qui signifie que le conteneur est uniquement accessible au propriétaire de la.

        azure storage container create --container templates -p Off 
        
4. Ajoutez votre modèle pour le conteneur.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Fournir un jeton SAS pendant le déploiement

Pour déployer un modèle privé dans un compte de stockage, récupérer un jeton SAS et l’inclure dans l’URI pour le modèle.

1. Créer un jeton d’associations de sécurité avec les autorisations de lecture et d’une heure d’expiration pour limiter l’accès. Définir le délai d’expiration pour permettre suffisamment de temps pour terminer le déploiement. Récupérer l’URI complet du modèle, y compris le jeton SAS.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. Déployer le modèle en fournissant l’URI qui inclut le jeton SAS.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

Pour obtenir un exemple de l’utilisation d’un jeton SAS avec modèles liés, consultez [l’aide de modèles liés avec le Gestionnaire de ressources Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, reportez-vous à la section [ressources de déploiement à l’aide de bibliothèques .NET et un modèle](virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour définir les paramètres de modèle, consultez [modèles de création](resource-group-authoring-templates.md#parameters).
- Pour obtenir des instructions sur le déploiement de votre solution aux différents environnements, consultez [développement et des environnements de test de Microsoft Azure](solution-dev-test-environments.md).
- Pour plus d’informations sur l’utilisation d’une référence KeyVault pour passer des valeurs sécurisées, voir [passer des valeurs sécurisées pendant le déploiement](resource-manager-keyvault-parameter.md).

