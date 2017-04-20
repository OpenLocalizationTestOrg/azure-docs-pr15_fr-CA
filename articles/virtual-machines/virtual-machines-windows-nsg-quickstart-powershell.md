<properties
   pageTitle="Ouvrir des ports pour un ordinateur virtuel à l’aide de PowerShell | Microsoft Azure"
   description="Découvrez comment ouvrir un port / créer un point de terminaison de votre machine virtuelle de Windows à l’aide du mode de déploiement de gestionnaire de ressources Azure et Azure PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Ouverture des ports et des points de terminaison à une machine virtuelle dans Azure à l’aide de PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Commandes rapides
Pour créer un groupe de sécurité réseau et les ACL des règles, vous avez besoin de [la version la plus récente de PowerShell Azure installé](../powershell-install-configure.md). Vous pouvez également [effectuer ces étapes en utilisant le portail Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Ouvrez une session votre compte Azure :

```powershell
Login-AzureRmAccount
```

Dans les exemples suivants, remplacer les noms de paramètre d’exemple par vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `myNetworkSecurityGroup`, et `myVnet`.

Créer une règle. L’exemple suivant crée une règle nommée `myNetworkSecurityGroupRule` pour autoriser le trafic TCP sur le port 80 :

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Ensuite, créez votre groupe de sécurité réseau et attribuer la règle HTTP que vous venez de créer comme suit. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

Maintenant nous allons affecter votre groupe de sécurité réseau à un sous-réseau. L’exemple suivant assigne un réseau virtuel existant nommé `myVnet` à la variable `$vnet`:

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associer le groupe de sécurité réseau à votre sous-réseau. L’exemple suivant associe le sous-réseau nommé `mySubnet` avec votre groupe de sécurité réseau :

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Enfin, mise à jour de votre réseau virtuel dans l’ordre pour que vos modifications soient prises en compte :

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Plus d’informations sur les groupes de sécurité de réseau
Les commandes rapides ici permettent d’obtenir en cours d’exécution avec le trafic de votre machine virtuelle. Les groupes de sécurité de réseau fournissent de nombreuses fonctionnalités et la granularité pour contrôler l’accès à vos ressources. Vous pouvez en savoir plus sur la [Création d’un groupe de sécurité du réseau et des règles d’ACL ici](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Vous pouvez définir des groupes de sécurité réseau et les règles de la liste ACL dans le cadre des modèles du Gestionnaire de ressources Azure. Pour en savoir plus sur la [Création de groupes de sécurité de réseau avec les modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser réacheminement de port pour mapper un unique port externe à un port interne sur votre ordinateur virtuel, utilisez un équilibreur de charge et des règles de traduction d’adresses réseau (NAT). Par exemple, vous souhaitez exposer le port TCP 8080 en externe et que le trafic dirigé vers le port TCP 80 sur une machine virtuelle. Vous pouvez en savoir plus sur la [Création d’un équilibreur de charge pour Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous pouvez trouver des informations sur la création d’environnements plus détaillées dans les articles suivants :

- [Vue d’ensemble de Gestionnaire des ressources Azure](../azure-resource-manager/resource-group-overview.md)
- [En quoi consiste un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)
- [Vue d’ensemble de Gestionnaire des ressources Azure pour les équilibreurs de charge](../load-balancer/load-balancer-arm.md)