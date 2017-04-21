## <a name="how-to-deploy-with-powershell"></a>Comment déployer avec PowerShell

1. Connectez-vous à votre compte Azure.

          Add-AzureAccount

   Après avoir fourni vos informations d’identification, la commande renvoie des informations sur votre compte.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Si vous disposez de plusieurs abonnements, fournissez l’id d’abonnement que vous souhaitez utiliser pour le déploiement. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Basculez vers le module Gestionnaire de ressources Azure.

          Switch-AzureMode AzureResourceManager

4. Si vous ne disposez pas d’un groupe de ressources existant, créez un nouveau groupe de ressources. Indiquez le nom du groupe de ressources et emplacement dont vous avez besoin pour votre solution.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

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

5. Pour créer un nouveau déploiement de votre groupe de ressources, exécutez la commande **New-AzureResourceGroupDeployment** et fournir les paramètres nécessaires. Les paramètres inclut un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL pour le modèle que vous avez créé et tous les autres paramètres requis pour votre scénario. 
   
   Vous disposez des options suivantes pour fournir les valeurs de paramètre : 
   
   - Utilisez les paramètres en ligne.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Utiliser un objet paramètre.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - À l’aide d’un fichier de paramètres.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  Lorsque le groupe de ressources a été déployé, vous allez voir un récapitulatif du déploiement.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. Pour obtenir des informations sur les échecs de déploiement.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Pour obtenir des informations détaillées sur les échecs de déploiement.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
