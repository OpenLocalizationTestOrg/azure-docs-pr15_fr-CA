<properties
   pageTitle="Créer une VM Linux avec plusieurs cartes réseau | Microsoft Azure"
   description="Apprenez à créer une VM Linux avec plusieurs cartes réseau liée à l’aide de modèles Azure CLI ou le Gestionnaire de ressources."
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
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-linux-vm-with-multiple-nics"></a>Créer une VM Linux avec plusieurs cartes réseau
Vous pouvez créer une machine virtuelle (VM) dans Azure qui possède plusieurs interfaces de réseau virtuel (NIC) attachés. Un scénario courant se serait d’avoir de sous-réseaux différents d’une connectivité front-end et back-end, ou d’un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article fournit des commandes rapides pour créer une machine virtuelle avec plusieurs cartes réseau attaché. Pour plus d’informations, y compris comment créer plusieurs cartes réseau dans vos propres scripts Bash, en savoir plus sur le [déploiement des ordinateurs virtuels de plusieurs cartes](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Différentes [tailles de machine virtuelle](virtual-machines-linux-sizes.md) prise en charge d’un nombre variable de cartes réseau, si la taille votre machine virtuelle en conséquence.

>[AZURE.WARNING] Vous devez associer plusieurs cartes réseau lorsque vous créez un ordinateur virtuel, vous ne pouvez pas ajouter des cartes réseau à un ordinateur virtuel existant. Vous pouvez [créer un ordinateur virtuel basé sur les disques virtuels d’origine](virtual-machines-linux-copy-vm.md) et créer plusieurs cartes réseau lorsque vous déployez la machine virtuelle.

## <a name="quick-commands"></a>Commandes rapides
Assurez-vous que vous disposez de la [CLI d’Azure](../xplat-cli-install.md) connecté et en utilisant le mode du Gestionnaire de ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacer les noms de paramètre d’exemple par vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `mystorageaccount`, et `myVM`.

Tout d’abord, créez un groupe de ressources. L’exemple suivant crée un groupe de ressources appelé `myResourceGroup` dans les `WestUS` emplacement :

```bash
azure group create myResourceGroup -l WestUS
```

Créer un compte de stockage pour stocker vos ordinateurs virtuels. L’exemple suivant crée un compte de stockage nommé `mystorageaccount`:

```bash
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Créer un réseau virtuel pour connecter vos ordinateurs virtuels vers. L’exemple suivant crée un réseau virtuel nommé `myVnet` avec un préfixe d’adresse de `192.168.0.0/16`:

```bash
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Créer deux sous-réseaux du réseau virtuel : un pour le trafic frontal et une pour le trafic de back-end. L’exemple suivant crée deux sous-réseaux, nommés `mySubnetFrontEnd` et `mySubnetBackEnd`:

```bash
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

Créer deux cartes d’interface réseau, attacher une carte réseau sur le sous-réseau frontal et une carte réseau sur le sous-réseau du back-end. L’exemple suivant crée deux cartes d’interface réseau, nommées `myNic1` et `myNic2`et les associe à vos sous-réseaux :

```bash
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic1 -m myVnet -k mySubnetFrontEnd
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic2 -m myVnet -k mySubnetBackEnd
```

Enfin, créez votre machine virtuelle, association de deux cartes réseau que vous avez créé précédemment. L’exemple suivant crée un ordinateur virtuel nommé `myVM`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-azure-cli"></a>Création de plusieurs cartes réseau à l’aide de la CLI d’Azure
Si vous avez précédemment créé un ordinateur virtuel à l’aide de la CLI d’Azure, les commandes rapides doivent être familiers. Le processus est le même pour créer une carte d’interface réseau ou plusieurs cartes réseau. Vous trouverez plus de détails sur le [déploiement de plusieurs cartes réseau à l’aide de la CLI d’Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), y compris les scripts pour le processus de boucle pour créer toutes les cartes réseau.

L’exemple suivant crée deux cartes d’interface réseau, nommées `myNic1` et `myNic2`, avec une carte réseau, connectez chaque sous-réseau :

```bash
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

En général vous créez également un [Groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) ou un [équilibreur de charge](../load-balancer/load-balancer-overview.md) pour vous aider à gérer et répartir le trafic entre vos ordinateurs virtuels. Encore une fois, les commandes sont les mêmes lorsque vous travaillez avec plusieurs cartes réseau. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Lier vos cartes réseau au groupe de sécurité réseau à l’aide de `azure network nic set`. L’exemple suivant lie `myNic1` et `myNic2` avec `myNetworkSecurityGroup`:

```bashazure 
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

Lors de la création de la machine virtuelle, vous spécifiez maintenant plusieurs cartes réseau. Au lieu de cela à l’aide de `--nic-name` pour fournir une seule carte réseau, vous utilisez plutôt `--nic-names` et fournir une liste séparée par des virgules de cartes réseau. Vous devez également prendre en charge lorsque vous sélectionnez la taille de la mémoire virtuelle. Il existe des limites pour le nombre total de cartes réseau que vous pouvez ajouter à une machine virtuelle. Apprenez-en plus sur les [tailles de la machine virtuelle de Linux](virtual-machines-linux-sizes.md). L’exemple suivant montre comment spécifier plusieurs cartes réseau, puis sur une taille de mémoire virtuelle qui prend en charge l’utilisation de plusieurs cartes réseau (`Standard_DS2_v2`) :

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Création de plusieurs cartes réseau à l’aide des modèles du Gestionnaire de ressources
Azure modèles du Gestionnaire de ressources permet de définir votre environnement de fichiers JSON déclaratives. Vous pouvez lire une [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md). Modèles du Gestionnaire de ressources permettent de créer plusieurs instances d’une ressource au cours du déploiement, comme la création de plusieurs cartes réseau. *Copie* vous permet de spécifier le nombre d’instances à créer :

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Plus d’informations sur la [Création de plusieurs instances à l’aide de la *copie*](../resource-group-create-multiple.md). 

Vous pouvez également utiliser un `copyIndex()` puis ajouter un nombre à un nom de ressource, ce qui vous permet de créer `myNic1`, `myNic2`, etc.. Vous trouverez ci-dessous un exemple d’ajout de la valeur d’index :

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Vous trouverez un exemple complet de [Création de plusieurs cartes réseau à l’aide des modèles du Gestionnaire de ressources](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Étapes suivantes
Assurez-vous de consulter [les tailles de Virtual Media Linux](virtual-machines-linux-sizes.md) lors de la création d’une machine virtuelle avec plusieurs cartes réseau. Soyez attentif au nombre maximal de cartes d’interface réseau prend en charge la taille de chaque machine virtuelle. 

Gardez à l’esprit que vous ne pouvez pas ajouter des cartes réseau à un ordinateur virtuel existant, vous devez créer toutes les cartes réseau lorsque vous déployez la machine virtuelle. Prenez soin lors de la planification de votre déploiement pour vous assurer que vous disposez tous la connectivité réseau requise dès le début.