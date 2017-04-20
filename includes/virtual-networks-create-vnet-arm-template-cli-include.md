## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Déployer le modèle ARM à l’aide de la CLI d’Azure

Pour déployer le modèle ARM que vous avez téléchargé à l’aide de CLI d’Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé CLI d’Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.
2. Exécuter le **`azure config mode`** commande pour passer en mode de gestionnaire de ressources, comme indiqué ci-dessous.

        azure config mode arm

    Voici la sortie attendue de la commande ci-dessus :

        info:    New mode is arm

3. Si nécessaire, exécutez les **`azure group create`** pour créer un nouveau groupe de ressources, comme indiqué ci-dessous. Notez la sortie de la commande. La liste est indiquée après que la sortie explique les paramètres utilisés. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](../articles/resource-group-overview.md).

        azure group create -n TestRG -l centralus

    Voici la sortie attendue de la commande ci-dessus :

        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

    - **n-(ou--nom)**. Nom du nouveau groupe de ressources. Pour notre scénario, il s’agit de *TestRG*.
    - **-l (ou--emplacement)**. Région Azure où sera créé le nouveau groupe de ressources. Pour notre scénario, il s’agit de *centralus*.

4. Exécuter le **`azure group deployment create`** applet de commande pour déployer la nouvelle VNet à l’aide du modèle et le paramètre de fichiers téléchargés et de modifier les propriétés. La liste est indiquée après que la sortie explique les paramètres utilisés.

        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

    Voici la sortie attendue de la commande ci-dessus :

        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK

    - **-g (ou des groupes de ressources)**. Nom du groupe de ressources le nouveau VNet sera créé dans.
    - **-f (ou--fichier de modèle)**. Chemin d’accès à votre fichier de modèle ARM.
    - **-e (ou--paramètres-fichier)**. Chemin d’accès à votre fichier de paramètres ARM.

5. Exécuter le **`azure network vnet show`** commande pour afficher les propriétés de la nouvelle vnet, comme illustré ci-dessous.

        azure network vnet show -g TestRG -n TestVNet

    Voici la sortie attendue de la commande ci-dessus :

        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK
