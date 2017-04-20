<properties
   pageTitle="Déployer des ressources avec PowerShell et modèle | Microsoft Azure"
   description="Utiliser le Gestionnaire de ressources Azure et d’Azure PowerShell pour déployer une ressources Azure. Les ressources sont définies dans un modèle de gestionnaire de ressources."
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

# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Déployer les ressources avec le Gestionnaire de ressources modèles et Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cette rubrique explique comment utiliser PowerShell d’Azure avec les modèles du Gestionnaire de ressources pour déployer vos ressources vers Azure.  

> [AZURE.TIP] Pour vous aider à déboguer une erreur pendant le déploiement, voir :
>
> - [Afficher les opérations de déploiement avec Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) pour en savoir plus sur l’obtention des informations qui vous aident à résoudre les problèmes de votre erreur.
> - [Résoudre les erreurs courantes lors du déploiement de ressources vers Azure avec le Gestionnaire de ressources Azure](resource-manager-common-deployment-errors.md) pour savoir comment résoudre les erreurs de déploiement courantes

Votre modèle peut être un fichier local ou un fichier externe qui est disponible via un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature (SAS) d’un accès partagé au cours du déploiement.

## <a name="quick-steps-to-deployment"></a>Étapes à suivre pour le déploiement

Cet article décrit toutes les différentes options disponibles au cours du déploiement. Toutefois, souvent vous devez uniquement deux commandes. Pour vous familiariser rapidement avec le déploiement, utilisez les commandes suivantes :

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Pour en savoir plus sur les options de déploiement qui peuvent être mieux adaptés à votre scénario, poursuivre la lecture de cet article.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-powershell"></a>Déployer avec PowerShell

1. Connectez-vous à votre compte Azure.

        Add-AzureRmAccount

     Un récapitulatif de votre compte est renvoyé.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Si vous disposez de plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement à l’aide de la commande **Set-AzureRmContext** . 

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. En général, lors du déploiement d’un nouveau modèle, vous souhaitez créer un groupe de ressources contenant les ressources. Si vous avez un groupe de ressources existant que vous souhaitez déployer, vous pouvez ignorer cette étape et utilisez ce groupe de ressources. 

     Pour créer un groupe de ressources, fournissez un nom et un emplacement pour votre groupe de ressources. Vous devez fournir un emplacement pour le groupe de ressources, car le groupe de ressources stocke des métadonnées concernant les ressources. Pour des raisons de conformité, vous souhaiterez spécifier où les métadonnées sont stockées. En général, nous recommandons que vous spécifiez un emplacement dans lequel se trouve la plupart de vos ressources. À partir du même emplacement permet de simplifier votre modèle.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Un résumé du nouveau groupe de ressources est retourné.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Avant d’exécuter votre déploiement, vous pouvez valider vos paramètres de déploiement. L’applet de commande **Test-AzureRmResourceGroupDeployment** vous permet de rechercher les problèmes avant de créer les ressources réelles. L’exemple suivant montre comment valider un déploiement.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Pour déployer des ressources à votre groupe de ressources, exécutez la commande **New-AzureRmResourceGroupDeployment** et fournir les paramètres nécessaires. Les paramètres incluent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL pour le modèle que vous avez créé et tous les autres paramètres requis pour votre scénario. Si le paramètre **Mode** n’est pas spécifié, la valeur par défaut de **incrémentielle** est utilisée. Pour exécuter un déploiement complet, définissez le **Mode** sur **terminé**. Soyez prudent lorsque vous utilisez le mode complet que vous pouvez supprimer par inadvertance de ressources qui ne sont pas dans votre modèle.

     Pour déployer un modèle local, utilisez le paramètre **TemplateFile** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Pour déployer un modèle externe, utilisez le paramètre de **TemplateUri** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Vous disposez des options suivantes pour fournir les valeurs de paramètre : 
   
     1. Utilisez les paramètres en ligne.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Utiliser un objet paramètre.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Utiliser un fichier de paramètre local. Pour plus d’informations sur le fichier de modèle, consultez le [fichier de paramètres](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Utiliser un fichier de paramètres externe. Pour plus d’informations sur le fichier de modèle, consultez le [fichier de paramètres](#parameter-file). 

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        Lorsque vous utilisez un fichier de paramètres externe, vous ne pouvez pas passer autres valeurs inline ou à partir d’un fichier local. Pour plus d’informations, consultez [priorité des paramètres](#parameter-precendence).

     Une fois que les ressources ont été déployées, vous allez voir un récapitulatif du déploiement.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Si votre modèle comprend un paramètre avec le même nom que l’un des paramètres de la commande PowerShell, vous êtes invité à fournir une valeur pour ce paramètre. Le paramètre à partir de votre modèle inclut le suffixe de **modèle**. Par exemple, un paramètre nommé **ResourceGroupName** dans votre modèle est en conflit avec le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) . Vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En général, vous devez éviter cette confusion par ne pas nommer les paramètres avec le même nom que les paramètres utilisés pour les opérations de déploiement.

6. Si vous souhaitez enregistrer des informations supplémentaires sur le déploiement qui peut-être vous aider à résoudre les problèmes liés à des erreurs de déploiement, utilisez le paramètre **DeploymentDebugLogLevel** . Vous pouvez spécifier que le contenu de la demande, le contenu de la réponse ou les deux être enregistrés avec l’opération de déploiement.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Pour plus d’informations sur l’utilisation de ce contenu de débogage pour résoudre les problèmes de déploiement, consultez [déploiements de groupe de ressources de résolution des problèmes avec Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## <a name="deploy-template-from-storage-with-sas-token"></a>Déployer le modèle de stockage avec jeton SAS

Vous pouvez ajouter vos modèles à un compte de stockage et d’un lien pendant le déploiement avec un jeton d’associations de sécurité.

> [AZURE.IMPORTANT] En suivant les étapes ci-dessous, le blob qui contient le modèle est accessible uniquement au propriétaire du compte. Toutefois, lorsque vous créez un jeton SAS pour le blob, le blob est accessible à toute personne disposant de cet URI. Si un autre utilisateur intercepte l’URI, cet utilisateur peut accéder au modèle. À l’aide d’un jeton SAS est un bon moyen de limiter l’accès à vos modèles, mais vous ne devez pas inclure les données sensibles telles que des mots de passe directement dans le modèle.

### <a name="add-private-template-to-storage-account"></a>Ajouter un modèle privé à un compte de stockage

Les étapes suivantes configurent un compte de stockage pour les modèles :

1. Créer un groupe de ressources.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Créer un compte de stockage. Le nom de compte de stockage doit être unique dans Azure, donc fournir votre propre nom pour le compte.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Définir le compte en cours de stockage.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Créer un conteneur. L’autorisation est définie à **Off** qui signifie que le conteneur est uniquement accessible au propriétaire de la.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Ajoutez votre modèle pour le conteneur.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Fournir un jeton SAS pendant le déploiement

Pour déployer un modèle privé dans un compte de stockage, récupérer un jeton SAS et l’inclure dans l’URI pour le modèle.

1. Si vous avez modifié le compte de stockage actuel, la valeur du compte en cours de stockage qui contient vos modèles.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Créer un jeton d’associations de sécurité avec les autorisations de lecture et d’une heure d’expiration pour limiter l’accès. Récupérer l’URI complet du modèle, y compris le jeton SAS.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Déployer le modèle en fournissant l’URI qui inclut le jeton SAS.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Pour obtenir un exemple de l’utilisation d’un jeton SAS avec modèles liés, consultez [l’aide de modèles liés avec le Gestionnaire de ressources Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="parameter-precedence"></a>Priorité de paramètre

Vous pouvez utiliser les paramètres inline et un fichier de paramètre local dans la même opération de déploiement. Par exemple, vous pouvez spécifier des valeurs dans le fichier de paramètre local et ajouter d’autre en ligne des valeurs pendant le déploiement. Si vous fournissez des valeurs pour un paramètre dans le fichier de paramètre local et en ligne, la valeur d’inline est prioritaire.

Toutefois, vous ne pouvez pas utiliser les paramètres inline avec un fichier de paramètres externe. Lorsque vous spécifiez un fichier de paramètres dans le paramètre **TemplateParameterUri** , tous les paramètres inline sont ignorées. Vous devez fournir toutes les valeurs de paramètre dans le fichier externe. Si votre modèle comprend une valeur sensible que vous ne pouvez pas inclure dans le fichier de paramètres, ajouter cette valeur dans un coffre-fort clé et référencer le coffre-fort clé dans votre fichier de paramètres externe ou fournir en liaison dynamique en ligne toutes des valeurs de paramètre.

Pour plus d’informations sur l’utilisation d’une référence KeyVault pour passer des valeurs sécurisées, voir [passer des valeurs sécurisées pendant le déploiement](resource-manager-keyvault-parameter.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, reportez-vous à la section [ressources de déploiement à l’aide de bibliothèques .NET et un modèle](virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour définir les paramètres de modèle, consultez [modèles de création](resource-group-authoring-templates.md#parameters).
- Pour obtenir des instructions sur le déploiement de votre solution aux différents environnements, consultez [développement et des environnements de test de Microsoft Azure](solution-dev-test-environments.md).

