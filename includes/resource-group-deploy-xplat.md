## <a name="how-to-deploy-with-azure-cli"></a>Comment déployer avec l’interface CLI d’Azure

1. Connectez-vous à votre compte Azure.

        azure login

  Après avoir fourni vos informations d’identification, la commande renvoie le résultat de votre connexion.

        ...
        info:    login command OK

2. Si vous disposez de plusieurs abonnements, fournissez l’id d’abonnement que vous souhaitez utiliser pour le déploiement.

        azure account set <YourSubscriptionNameOrId>

3. Basculez vers le module Gestionnaire de ressource Azure

        azure config mode arm

   Vous recevrez une confirmation du nouveau mode.

        info:     New mode is arm

4. Si vous ne disposez pas d’un groupe de ressources existant, créez un nouveau groupe de ressources. Indiquez le nom du groupe de ressources et emplacement dont vous avez besoin pour votre solution.

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

5. Pour créer un nouveau déploiement de votre groupe de ressources, exécutez la commande suivante et fournir les paramètres nécessaires. Les paramètres inclut un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL pour le modèle que vous avez créé et tous les autres paramètres requis pour votre scénario.

   Vous disposez des options suivantes pour fournir les valeurs de paramètre :

   - Utilisez les paramètres inline et un modèle local.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Utilisez les paramètres inline et un lien vers un modèle.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Utiliser un fichier de paramètres.

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  Lorsque le groupe de ressources a été déployé, vous allez voir un récapitulatif du déploiement.

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. Pour obtenir des informations sur votre dernier déploiement.

         azure group log show -l ExampleResourceGroup

7. Pour obtenir des informations détaillées sur les échecs de déploiement.

         azure group log show -l -v ExampleResourceGroup
