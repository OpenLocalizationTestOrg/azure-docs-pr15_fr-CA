<properties
   pageTitle="Créer l’homologation de VNet à l’aide du Gestionnaire de ressources de modèles | Microsoft Azure"
   description="Apprenez à créer un réseau virtuel peering à l’aide des modèles dans le Gestionnaire de ressources."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-vnet-peering-using-resource-manager-templates"></a>Créer l’homologation de VNet à l’aide des modèles du Gestionnaire de ressources

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer un VNet d’homologation, à l’aide des modèles du Gestionnaire de ressources, veuillez suivre les étapes ci-dessous :

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Comment faire pour installer et configurer le PowerShell Azure](../powershell-install-configure.md) et suivez les instructions jusqu'à la fin pour signer dans Azure et sélectionnez votre abonnement.

    > [AZURE.NOTE] L’applet de commande PowerShell de gestion VNet homologation est livré avec [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Le texte ci-dessous montre la définition d’un lien d’homologation VNet de VNet1 à VNet2, en fonction du scénario ci-dessus. Copiez le contenu suivant et enregistrez-le dans un fichier nommé VNetPeeringVNet1.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }

3. La section ci-dessous illustre la définition d’un lien d’homologation VNet de VNet2 à VNet1, en fonction du scénario ci-dessus.  Copiez le contenu suivant et enregistrez-le dans un fichier nommé VNetPeeringVNet2.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

    Comme dans le modèle ci-dessus, il existe quelques propriétés configurables pour l’homologation de VNet :

  	|Option|Description|Par défaut|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Si l’espace d’adressage d’un homologue VNet est inclus dans le cadre de la balise virtual_network.|Oui|
  	|AllowForwardedTraffic|Si le trafic ne proviennent ne pas d’une VNet de ressources est accepté ou rejeté.|N°|
  	|AllowGatewayTransit|Permet à l’hôte à utiliser la passerelle VNet VNet.|N°|
  	|UseRemoteGateways|Utiliser passerelle de VNet de votre collègue. L’homologue VNet doit avoir une configuration de passerelle et AllowGatewayTransit est sélectionnée. Vous ne pouvez pas utiliser cette option si vous disposez d’une configuration de passerelle.|N°|

    Chaque lien de l’homologation de VNet a l’ensemble des propriétés ci-dessus. Par exemple, vous pouvez définir AllowVirtualNetworkAccess sur True pour un lien d’homologation VNet VNet1 à VNet2 et affectez-lui la valeur False pour le lien d’homologation VNet dans l’autre direction.


4. Pour déployer le fichier de modèle, vous pouvez exécuter l’applet de commande New-AzureRmResourceGroupDeployment pour créer ou mettre à jour le déploiement. Pour plus d’informations sur l’utilisation des modèles du Gestionnaire de ressources, reportez-vous à cet [article](../resource-group-template-deploy.md).

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

    > [AZURE.NOTE] Remplacez le groupe nom et modèle de fichier de ressources comme il convient.

    Ci-dessous est un exemple en fonction du scénario ci-dessus :

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

    Sortie indique :

        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

    Sortie indique :

        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Une fois le déploiement terminé, vous pouvez exécuter l’applet de commande ci-dessous pour afficher l’état d’homologation :

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    Sortie indique :

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Une fois que l’homologation établie dans ce scénario, vous devez être en mesure d’initier les connexions à partir de n’importe quelle machine virtuelle sur n’importe quelle machine virtuelle dans les deux VNets. Par défaut, AllowVirtualNetworkAccess a la valeur True et VNet peering met en service les ACL appropriées pour permettre la communication entre VNets. Vous pouvez toujours appliquer les règles de groupe (NSG) de sécurité de réseau pour bloquer la connectivité entre des sous-réseaux ou des machines virtuelles à prendre le contrôle précis d’accès entre deux réseaux virtuels.  Pour plus d’informations de création de règles NSG, reportez-vous à cet [article](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Pour créer un VNet d’homologation sur les abonnements, veuillez suivre les étapes ci-dessous :

1. Connectez-vous à Azure avec privilèges utilisateur-A de compte dans l’abonnement-A, exécutez l’applet de commande suivante :

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

    Ce n’est pas une exigence, homologation peut être établie même si les utilisateurs déclenchent individuellement des demandes d’homologation pour leurs Vnets respectifs tant que les demandes correspondant à. Ajout d’un utilisateur privilégié de l’autre VNet en tant qu’utilisateurs dans le VNet local rend plus facile d’effectuer la configuration.

2. Connectez-vous à Azure avec compte de privilèges à l’utilisateur B b-abonnement et exécuter l’applet de commande suivante :

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. Dans-un utilisateur de la session de connexion, exécutée la cmdlet suivante :

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Voici comment le fichier JSON est défini.  

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }

4. Dans la session de connexion de l’utilisateur-B, exécutez l’applet de commande suivante :

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all

    Voici comment le fichier JSON est défini :

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }

    Une fois que l’homologation établie dans ce scénario, vous devez être en mesure d’initier les connexions à partir de n’importe quelle machine virtuelle à toute machine virtuelle de deux VNets entre différents abonnements.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Dans ce scénario, vous pouvez déployer l’exemple de modèle ci-dessous pour établir l’homologation VNet.  Vous devez définir la propriété AllowForwardedTraffic à True, ce qui permet à l’appliance virtuelle du réseau dans le ressources VNet pour envoyer et recevoir le trafic.

    Voici le modèle pour la création d’un VNet d’homologation de HubVNet à VNet1. Notez que AllowForwardedTraffic est définie sur false.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. Voici le modèle pour la création d’un VNet d’homologation de VNet1 à HubVnet. Notez que AllowForwardedTraffic est défini sur true.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. Une fois que l’homologation est établie, vous pouvez faire référence à cet [article](virtual-network-create-udr-arm-ps.md) pour définir routes(UDR) de défini par l’utilisateur afin de rediriger le trafic de VNet1 grâce à une appliance virtuelle pour utiliser ses fonctionnalités. Lorsque vous spécifiez l’adresse du tronçon suivant dans l’itinéraire, vous pouvez le définir à l’adresse IP de l’appliance virtuelle de l’homologue VNet HubVNet.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Pour créer une homologation entre réseaux virtuels à partir de modèles de déploiement différentes, procédez comme suit :
1. Le texte ci-dessous montre la définition d’un lien d’homologation VNet de VNET1 à VNET2 dans ce scénario. Qu’un lien est requis pour un réseau virtuel classique à un réseau virtuel du Gestionnaire des ressources Azure de l’homologue.

    Veillez à placer dans votre ID d’abonnement pour où se trouve le classique réseau virtuel ou un VNET2 et modifier des MyResouceGroup au nom du groupe de ressources approprié.

    {« $schema » : « https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json# », « contentVersion » : « 1.0.0.0 », « paramètres » : {}, « variables » : {}, « ressources » : [{« apiVersion » : « 2016-06-01 », « type » : « Microsoft.Network/virtualNetworks/virtualNetworkPeerings », « nom » : « VNET1/LinkToVNET2 », « emplacement » : « [resourceGroup () .location] », « propriétés » : {« allowVirtualNetworkAccess » : true, « allowForwardedTraffic » : false, « allowGatewayTransit » : false, « useRemoteGateways » : false, « remoteVirtualNetwork » : {« id » : « [resourceId (' Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')] »}}}]}

2. Pour déployer le fichier de modèle, exécutez l’applet de commande suivante pour créer ou mettre à jour le déploiement.

        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all

        Output shows:

        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

3. Une fois le déploiement réussi, vous pouvez exécuter l’applet de commande suivante pour afficher l’état d’homologation :

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2

        Output shows:

        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Une fois que l’homologation est établie entre un VNet classique et un gestionnaire de ressources VNet, vous devez être capable d’initier des connexions à partir d’un ordinateur virtuel de VNET1 à n’importe quelle machine virtuelle dans VNET2 et vice versa.
