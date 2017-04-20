<properties
   pageTitle="Ouvrir les ports et les points de terminaison à une VM Linux | Microsoft Azure"
   description="Découvrez comment ouvrir un port / créer un point de terminaison de votre VM Linux en utilisant le modèle de déploiement d’Azure resource manager et la CLI Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>Ouverture des ports et des points de terminaison à une VM Linux dans Azure
Vous ouvrez un port, ou créez un point de terminaison, à une machine virtuelle (VM) dans Azure en créant un filtre de réseau sur un sous-réseau ou une interface de réseau de machine virtuelle. Vous placez ces filtres, qui contrôlent le trafic entrant et sortant, sur un réseau de groupe de sécurité associé à la ressource qui reçoit le trafic. Nous allons utiliser un exemple courant de trafic web sur le port 80.

## <a name="quick-commands"></a>Commandes rapides
Pour créer un groupe de sécurité du réseau et des règles, vous devez [La CLI Azure](../xplat-cli-install.md) installé et utilise le mode du Gestionnaire de ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacer les noms de paramètre d’exemple par vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `myNetworkSecurityGroup`, et `myVnet`.

Créez votre groupe de sécurité réseau, saisie de vos propres noms et l’emplacement approprié. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup` dans les `WestUS` emplacement :

```
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Ajouter une règle pour autoriser le trafic HTTP sur votre serveur Web (ou modifier pour votre propre scénario, par exemple de la connectivité de base de données ou l’accès SSH). L’exemple suivant crée une règle nommée `myNetworkSecurityGroupRule` pour autoriser le trafic TCP sur le port 80 :

```
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

Associer le groupe de sécurité réseau disposant d’interface réseau (NIC) de votre machine virtuelle. L’exemple suivant associe une carte existante nommée `myNic` avec le groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

Sinon, vous pouvez associer votre groupe de sécurité de réseau avec un sous-réseau de réseau virtuel et non pas uniquement l’interface réseau sur un ordinateur virtuel unique. L’exemple suivant associe un sous-réseau existant nommé `mySubnet` dans les `myVnet` réseau virtuel avec le groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Plus d’informations sur les groupes de sécurité de réseau
Les commandes rapides ici permettent d’obtenir en cours d’exécution avec le trafic de votre machine virtuelle. Les groupes de sécurité de réseau fournissent de nombreuses fonctionnalités et la granularité pour contrôler l’accès à vos ressources. Vous pouvez en savoir plus sur la [Création d’un groupe de sécurité du réseau et des règles d’ACL ici](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Vous pouvez définir des groupes de sécurité réseau et les règles de la liste ACL dans le cadre des modèles du Gestionnaire de ressources Azure. Pour en savoir plus sur la [Création de groupes de sécurité de réseau avec les modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser réacheminement de port pour mapper un unique port externe à un port interne sur votre ordinateur virtuel, utilisez un équilibreur de charge et des règles de traduction d’adresses réseau (NAT). Par exemple, vous souhaitez exposer le port TCP 8080 en externe et que le trafic dirigé vers le port TCP 80 sur une machine virtuelle. Vous pouvez en savoir plus sur la [Création d’un équilibreur de charge pour Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous pouvez trouver des informations sur la création d’environnements plus détaillées dans les articles suivants :

- [Vue d’ensemble de Gestionnaire des ressources Azure](../azure-resource-manager/resource-group-overview.md)
- [En quoi consiste un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)
- [Vue d’ensemble de Gestionnaire des ressources Azure pour les équilibreurs de charge](../load-balancer2    /load-balancer-arm.md)