
<properties
   pageTitle="Créer un environnement Linux complet à l’aide de la CLI Azure | Microsoft Azure"
   description="Créer le stockage, une VM Linux, un réseau virtuel et sous-réseau, un équilibreur de charge, une carte réseau, une adresse IP publique et un groupe de sécurité réseau, tous entièrement à l’aide de la CLI d’Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/24/2016"
   ms.author="iainfou"/>

# <a name="create-a-complete-linux-environment-by-using-the-azure-cli"></a>Créer un environnement Linux complet à l’aide de la CLI d’Azure

Dans cet article, nous créons un réseau simple avec un équilibreur de charge et une paire d’ordinateurs virtuels qui sont utiles pour le développement et l’informatique simple. Nous vous guidons à travers le processus de commande par commande, jusqu'à ce que vous ayez deux VM Linux fonctionne, sécurisé auquel vous pouvez vous connecter à partir de n’importe où sur Internet. Puis vous pouvez vous déplacer à des réseaux et des environnements plus complexes.

Tout au long du processus, vous découvrez la hiérarchie des dépendances que le modèle de déploiement du Gestionnaire de ressources offre, et sur la quantité d’alimentation il. Après avoir vu la manière dont le système est généré, vous pouvez la recréer plus rapidement à l’aide des [modèles du Gestionnaire de ressources Azure](../resource-group-authoring-templates.md). En outre, une fois que vous découvrez comment les parties de votre environnement s’imbriquent, création de modèles pour automatiser les devient plus facile.

L’environnement contient :

- Deux des ordinateurs virtuels à l’intérieur d’un jeu de disponibilité.
- Un équilibreur de charge avec une règle d’équilibrage de la charge sur le port 80.
- Règles du groupe (NSG) de sécurité réseau pour protéger votre ordinateur virtuel à partir du trafic indésirable.

![Vue d’ensemble de l’environnement de base](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Pour créer cet environnement personnalisé, vous devez la dernière [Azure CLI](../xplat-cli-install.md) en mode de Gestionnaire des ressources (`azure config mode arm`). Vous devez également un outil d’analyse de JSON. Cet exemple utilise [jq](https://stedolan.github.io/jq/).

## <a name="quick-commands"></a>Commandes rapides
Si vous avez besoin effectuer rapidement les tâches, les détails suivants de la section de la base de commandes pour télécharger une machine virtuelle pour Azure. Plus d’informations et le contexte de chaque étape peut être trouvé dans le reste du document, départ [ici](#detailed-walkthrough).

Assurez-vous que vous disposez de [L’infrastructure du langage commun Azure](../xplat-cli-install.md) connecté et en utilisant le mode du Gestionnaire de ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacer les noms de paramètre d’exemple par vos propres valeurs. Incluent des exemples de noms de paramètre `myResourceGroup`, `mystorageaccount`, et `myVM`.

Créer le groupe de ressources. L’exemple suivant crée un groupe de ressources appelé `myResourceGroup` dans les `westeurope` emplacement :

```bash
azure group create -n myResourceGroup -l westeurope
```

Vérifiez que le groupe de ressources à l’aide de l’analyseur JSON :

```bash
azure group show myResourceGroup --json | jq '.'
```

Créer le compte de stockage. L’exemple suivant crée un compte de stockage nommé `mystorageaccount` (le nom du compte de stockage doit être unique, donc fournir votre propre nom unique) :

```bash
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Vérifiez que le compte de stockage à l’aide de l’analyseur JSON :

```bash
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Créez le réseau virtuel. L’exemple suivant crée un réseau virtuel nommé `myVnet`:

```bash
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Créer un sous-réseau. L’exemple suivant crée un sous-réseau nommé `mySubnet`"

```bash
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Vérifiez que le réseau virtuel et un sous-réseau à l’aide de l’analyseur JSON :


```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Créer une adresse IP publique. L’exemple suivant crée un IP publique nommée `myPublicIP` avec le nom DNS de `mypublicdns` (le nom DNS doit être unique, donc fournir votre propre nom unique) :

```bash
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Créer l’équilibreur de charge. L’exemple suivant crée un équilibreur de charge nommé `myLoadBalancer`:

```bash
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Créer un pool frontal d’IP pour l’équilibrage de charge et associer l’adresse IP publique. L’exemple suivant crée un pool frontal d’IP nommé `mySubnetPool`:

```bash
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool 
```

Créer le pool d’IP pour l’équilibrage de charge back-end. L’exemple suivant crée un pool d’IP principal nommé `myBackEndPool`:

```bash
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Créer des règles de la traduction d’adresse pour l’équilibrage de charge réseau entrant de SSH. L’exemple suivant crée deux règles d’équilibrage de la charge, `myLoadBalancerRuleSSH1` et `myLoadBalancerRuleSSH2`:

```bash
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Créer le site web les règles NAT pour l’équilibrage de la charge de trafic entrant. L’exemple suivant crée une règle d’équilibrage de la charge nommée`myLoadBalancerRuleWeb`

```bash
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Créer la sonde de santé d’équilibrage de la charge. L’exemple suivant crée une sonde de TCP appelée `myHealthProbe`:

```bash
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Vérifiez l’équilibreur de charge, des pools d’IP et les règles NAT à l’aide de l’analyseur JSON :

```bash
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Créer la première carte d’interface réseau (NIC). Remplacer le `#####-###-###` sections avec votre propre code d’abonnement Azure. Votre abonnement ID est indiquée dans la sortie de `jq` lors de l’examen les ressources que vous créez. Vous pouvez également afficher votre ID d’abonnement avec `azure account list`. 

L’exemple suivant crée une carte réseau nommée `myNic1`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Créez la seconde carte réseau. L’exemple suivant crée une carte réseau nommée `myNic2`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Vérifiez que les deux cartes d’interface réseau à l’aide de l’analyseur JSON :

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Créer le groupe de sécurité du réseau. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```bash
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Ajoutez deux règles de trafic entrant pour le groupe de sécurité du réseau. L’exemple suivant crée deux règles, `myNetworkSecurityGroupRuleSSH` et `myNetworkSecurityGroupRuleHTTP`:

```bash
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Vérifiez que le groupe de sécurité réseau et les règles de trafic entrant à l’aide de l’analyseur JSON :

```bash
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Lier le groupe de sécurité de réseau à deux cartes réseau :

```bash
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Créez le jeu de disponibilité. L’exemple suivant crée une jeu nommée de disponibilité `myAvailabilitySet`:

```bash
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Créer la première VM Linux. L’exemple suivant crée un ordinateur virtuel nommé `myVM1`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Créez la deuxième VM Linux. L’exemple suivant crée un ordinateur virtuel nommé `myVM2`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Utilisez l’analyseur JSON pour vérifier que tout ce qui a été créé :

```bash
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exporter votre nouvel environnement à un modèle pour recréer rapidement des nouvelles instances :

```bash
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé
Les étapes détaillées qui suivent expliquent ce que fait chaque commande lorsque vous constituez votre environnement. Ces concepts sont utiles lorsque vous créez vos propres environnements personnalisés pour le développement ou la production.

Assurez-vous que vous disposez de [L’infrastructure du langage commun Azure](../xplat-cli-install.md) connecté et en utilisant le mode du Gestionnaire de ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacer les noms de paramètre d’exemple par vos propres valeurs. Incluent des exemples de noms de paramètre `myResourceGroup`, `mystorageaccount`, et `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Créer des groupes de ressources et de choisir les emplacements de déploiement

Groupes de ressources Azure sont des entités de déploiement logique qui contiennent des informations de configuration et des métadonnées pour permettre la gestion logique des déploiements de la ressource. L’exemple suivant crée un groupe de ressources appelé `myResourceGroup` dans les `westeurope` emplacement :

```bash
azure group create --name myResourceGroup --location westeurope
```

Sortie :

```bash                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Créer un compte de stockage

Vous avez besoin de comptes de stockage pour les disques de votre machine virtuelle et pour tous les disques de données supplémentaires que vous souhaitez ajouter. Vous créez des comptes de stockage presque immédiatement après que vous créez des groupes de ressources.

Nous utilisons ici la `azure storage account create` de commande, en passant l’emplacement du compte, le groupe de ressources qui contrôle et le type de support de stockage que vous souhaitez. L’exemple suivant crée un compte de stockage nommé `mystorageaccount`:

```bash
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Sortie :

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Pour examiner notre groupe de ressources à l’aide de la `azure group show` de commande, nous allons utiliser l’outil [jq](https://stedolan.github.io/jq/) avec la `--json` option d’Azure CLI. (Vous pouvez utiliser **jsawk** ou n’importe quelle bibliothèque de langue que vous souhaitez analyser le JSON).

```bash
azure group show myResourceGroup --json | jq '.'
```

Sortie :

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Pour savoir si le compte de stockage à l’aide de l’interface CLI, vous devez d’abord définir les noms de compte et les clés. Avec un nom que vous choisissez, remplacez le nom du compte de stockage dans l’exemple suivant :

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Vous pouvez ensuite afficher facilement vos informations sur le stockage :

```bash
azure storage container list
```

Sortie :

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Créer un sous-réseau et un réseau virtuel

Ensuite, vous allez devoir créer un réseau virtuel exécute dans Azure et un sous-réseau dans lequel vous pouvez créer vos ordinateurs virtuels. L’exemple suivant crée un réseau virtuel nommé `myVnet` avec la `192.168.0.0/16` préfixe d’adresse :

```bash
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16 
```

Sortie :

```bash
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Là encore, nous utiliserons l’option--json de `azure group show` et **jq** pour voir la façon dont nous créons nos ressources. Nous disposons désormais d’une `storageAccounts` ressource et un `virtualNetworks` ressource.  

```bash
azure group show myResourceGroup --json | jq '.'
```

Sortie :

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Maintenant nous allons créer un sous-réseau dans le `myVnet` réseau virtuel dans lequel les ordinateurs virtuels sont déployés. Nous utilisons le `azure network vnet subnet create` commande, ainsi que les ressources que nous avons déjà créé : le `myResourceGroup` groupe de ressources et de la `myVnet` réseau virtuel. Dans l’exemple suivant, nous ajoutons le sous-réseau nommé `mySubnet` avec le préfixe d’adresse de sous-réseau `192.168.1.0/24`:

```bash
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Sortie :

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Étant donné que le sous-réseau est logiquement à l’intérieur du réseau virtuel, nous recherchez les informations de sous-réseau avec une commande légèrement différente. La commande que nous utilisons est `azure network vnet show`, mais nous continuons à examiner la sortie JSON à l’aide de **jq**.

```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Sortie :

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address-pip"></a>Créer une adresse IP publique (PIP)

Maintenant nous allons créer l’adresse IP publique (PIP) qui nous affecter à votre équilibreur de charge. Il permet de vous connecter à vos ordinateurs virtuels à partir d’Internet à l’aide de la `azure network public-ip create` commande. Car l’adresse par défaut est dynamique, nous créons une entrée nommée de DNS dans le domaine de la **cloudapp.azure.com** à l’aide de la `--domain-name-label` option. L’exemple suivant crée un IP publique nommée `myPublicIP` avec le nom DNS de `mypublicdns`. Le nom DNS doit être unique, fournir votre propre nom DNS unique :

```bash
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Sortie :

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

L’adresse IP publique est également une ressource de niveau supérieur, vous pouvez le voir avec `azure group show`.

```bash
azure group show myResourceGroup --json | jq '.'
```

Sortie :

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Vous pouvez rechercher plus de ressources, y compris le nom de domaine pleinement qualifié (FQDN) du sous-domaine, à l’aide de `azure network public-ip show` commande. La ressource d’adresse IP publique a été allouée de manière logique, mais une adresse spécifique n’a pas encore été affectée. Pour obtenir une adresse IP, vous aurez besoin d’un équilibreur de charge, nous n’avons pas encore créé.

```bash
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Sortie :

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Créer un équilibreur de charge et des pools d’IP
Lorsque vous créez un équilibreur de charge, il vous permet de répartir le trafic entre plusieurs ordinateurs virtuels. Il assure aussi la redondance à votre application en exécutant plusieurs ordinateurs virtuels qui répondent aux demandes des utilisateurs en cas de maintenance ou de charges lourdes. L’exemple suivant crée un équilibreur de charge nommé `myLoadBalancer`:

```bash
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Sortie :

```bash
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

L’équilibrage de la charge est relativement vide, nous allons créer des pools d’IP. Nous voulons créer deux pools d’IP pour notre équilibreur de charge - un pour le front-end et un pour le back-end. Le pool frontal IP est visible publiquement. Il est également l’emplacement auquel nous affecter le PIP que nous avons créées précédemment. Puis, nous utilisons le pool principal en tant qu’emplacement pour les nos machines virtuelles pour se connecter à. De cette manière, le trafic peut transiter par l’intermédiaire de l’équilibreur de charge pour les ordinateurs virtuels.

Tout d’abord, créons notre pool IP front-end. L’exemple suivant crée un pool frontal nommé `myFrontEndPool`:

```bash
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool 
```

Sortie :

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Notez comment nous avons utilisé le `--public-ip-name` commutateur pour passer dans le `myPublicIP` que nous avons créée précédemment. Affectation d’une adresse IP publique à l’équilibreur de charge vous permet d’atteindre vos ordinateurs virtuels via Internet.

Ensuite, nous allons créer notre deuxième pool IP, cette fois pour notre trafic back-end. L’exemple suivant crée un pool principal nommé `myBackEndPool`:

```bash
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Sortie :

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Nous pouvons voir le faire de l’équilibrage de la charge en recherchant avec `azure network lb show` et en examinant la sortie JSON :

```bash
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Sortie :

```bash
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Créer des règles NAT équilibreur de charge
Pour obtenir le trafic transitant par l’équilibrage de la charge, nous devons créer des règles de traduction (NAT) qui spécifient les actions entrantes ou sortantes adresse réseau. Vous pouvez spécifier le protocole à utiliser, puis mapper les ports externes pour les ports internes que vous le souhaitez. Pour notre environnement, nous allons créer des règles qui permettent de SSH par l’intermédiaire de notre équilibreur de charge pour nos ordinateurs virtuels. Nous allons configurer les ports TCP 4222 et 4223 de diriger vers le port TCP 22 sur nos machines virtuelles (qui sera créé plus tard). L’exemple suivant crée une règle nommée `myLoadBalancerRuleSSH1` pour mapper le port TCP 4222 à la voie 22 :

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Sortie :

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Répétez cette procédure pour votre deuxième règle NAT pour SSH. L’exemple suivant crée une règle nommée `myLoadBalancerRuleSSH2` pour mapper le port TCP 4223 à la voie 22 :

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Nous allons également continuer et créer une règle NAT pour le port TCP 80 pour le trafic web, raccordement de la règle à nos groupes IP. Si nous raccorder la règle de pool IP, au lieu de connecter la règle à nos ordinateurs virtuels individuellement, nous pouvons ajouter ou supprimer des ordinateurs virtuels dans le pool de l’IP. L’équilibreur de charge ajuste automatiquement le flux du trafic. L’exemple suivant crée une règle nommée `myLoadBalancerRuleWeb` pour mapper le port TCP 80 vers le port 80 :

```bash
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Sortie :

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Créer une sonde de santé d’équilibrage de la charge

Un état sonde régulièrement des vérifications sur les ordinateurs virtuels qui sont derrière notre équilibreur de charge afin de vous assurer que leur date d’exploitation et de répondre aux requêtes, tel que défini. Si ce n’est pas le cas, ils sont supprimés de l’opération pour garantir que les utilisateurs ne sont pas en cours dirigés vers les. Vous pouvez définir des contrôles personnalisés pour la sonde de santé, ainsi que les intervalles et les valeurs de délai d’attente. Pour plus d’informations sur les investigations de santé, voir [équilibrage de la charge des sondes](../load-balancer/load-balancer-custom-probe-overview.md). L’exemple suivant crée un TCP santé détectés nommé `myHealthProbe`:

```bash
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Sortie :

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Ici, nous avons spécifié un intervalle de 15 secondes pour les vérifications de la santé. Nous pouvons passer un maximum de quatre sondes (une minute) avant de l’équilibreur de charge estime que l’hôte ne fonctionne plus.

## <a name="verify-the-load-balancer"></a>Vérifiez l’équilibreur de charge
La configuration d’équilibrage de la charge est maintenant terminée. Voici les étapes que vous avez prises :

1. Tout d’abord, vous avez créé un équilibreur de charge.
2. Puis vous créé un pool IP front-end et affecté une adresse IP publique.
3. Ensuite, vous avez créé un pool back-end IP que les ordinateurs virtuels peuvent se connecter à.
4. Après cela, vous avez créé des règles NAT autorisent SSH pour les ordinateurs virtuels pour la gestion, ainsi que d’une règle qui autorise le port TCP 80 pour notre application web.
5. Enfin, vous avez ajouté une sonde de santé pour vérifier régulièrement les ordinateurs virtuels. Cette sonde de santé garantit que les utilisateurs n’essayent pas d’accéder à un ordinateur virtuel qui n’est plus fonctionne ni héberger un contenu.

Passons en revue ce que votre équilibreur de charge ressemble maintenant :

```bash
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Sortie :

```bash
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Créer une carte d’interface réseau à utiliser avec la VM Linux

Cartes réseau est disponibles par programme, parce que vous pouvez appliquer des règles à leur utilisation. Vous pouvez également avoir plusieurs. L’exemple suivant `azure network nic create` de commande, de vous connecter la carte réseau pour le pool d’IP de charge back-end et l’associez à la règle NAT pour autoriser le trafic SSH.
 
Remplacer le `#####-###-###` sections avec votre propre code d’abonnement Azure. Votre abonnement ID est indiquée dans la sortie de `jq` lors de l’examen les ressources que vous créez. Vous pouvez également afficher votre ID d’abonnement avec `azure account list`. 

L’exemple suivant crée une carte réseau nommée `myNic1`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Sortie :

```bash
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Vous pouvez afficher les détails en examinant la ressource directement. Vous examinez la ressource à l’aide de la `azure network nic show` commande :

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Sortie :

```bash
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Créons à présent la seconde carte réseau, raccordement à nouveau à notre pool IP back-end. Cette règle NAT la seconde autorise le trafic SSH. L’exemple suivant crée une carte réseau nommée `myNic2`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Créer un groupe de sécurité du réseau et des règles

Nous créons un groupe de sécurité réseau et les règles de trafic entrant qui régissent l’accès à la carte réseau. Un groupe de sécurité du réseau peut être appliqué à une carte réseau ou un sous-réseau. Vous définissez des règles pour contrôler le flux du trafic entrant et sortant de vos ordinateurs virtuels. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Nous allons ajouter la règle de trafic entrant pour le NSG à autoriser les connexions entrantes sur le port 22 (prise en charge SSH). L’exemple suivant crée une règle nommée `myNetworkSecurityGroupRuleSSH` pour autoriser le port TCP 22 :

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Maintenant nous allons ajouter la règle de trafic entrant pour le NSG à autoriser les connexions entrantes sur le port 80 (pour prendre en charge le trafic web). L’exemple suivant crée une règle nommée `myNetworkSecurityGroupRuleHTTP` pour autoriser le port TCP 80 :

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [AZURE.NOTE] La règle entrante est un filtre pour les connexions réseau entrantes. Dans cet exemple, nous lions la NSG à la carte virtual machines virtuelles, qui signifie que toute demande à la voie 22 est transmis à la carte réseau sur notre ordinateur virtuel. Cette règle de trafic entrant sur une connexion réseau, et pas sur un point de terminaison, qu’il serait sur dans les déploiements classiques. Pour ouvrir un port, vous devez laisser le `--source-port-range` la valeur «\*» (valeur par défaut) pour accepter les demandes entrantes à partir de **n’importe quel** port de demande. Les ports sont généralement dynamiques.

## <a name="bind-to-the-nic"></a>Lier à la carte réseau

Lier le NSG aux cartes réseau. Nous devons mettre nos cartes réseau avec notre groupe de sécurité du réseau. Exécutez les deux commandes, pour raccorder deux de nos cartes d’interface réseau :

```bash
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```bash
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Créer un jeu de disponibilité
Disponibilité définit propagation à l’aide de vos ordinateurs virtuels entre les domaines de la panne et mise à niveau. Nous allons créer une disponibilité pour vos ordinateurs virtuels. L’exemple suivant crée une jeu nommée de disponibilité `myAvailabilitySet`:

```bash
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domaines d’erreur définissent un regroupement d’ordinateurs virtuels qui partagent un courant d’alimentation source et le commutateur réseau. Par défaut, les ordinateurs virtuels qui sont configurés au sein de votre jeu de disponibilité sont séparés dans les trois domaines d’erreur. L’idée est qu’un problème matériel dans un de ces domaines d’erreur n’affecte pas chaque ordinateur virtuel qui exécute votre application. Azure distribue automatiquement les ordinateurs virtuels sur les domaines de pannes lorsque vous les placez dans un jeu de disponibilité.

Domaines de mise à niveau permet d’indiquer les groupes de machines virtuelles et le matériel physique sous-jacent qui peut être redémarré en même temps. L’ordre dans lequel la mise à niveau de domaines sont réamorcés peut ne pas être séquentielle pendant les maintenances planifiées, mais uniquement un mise à niveau est redémarré à la fois. Encore une fois, Azure distribue automatiquement vos ordinateurs virtuels sur les domaines de mise à niveau lorsque vous les placez dans un site de disponibilité.

Plus d’informations sur la [gestion de la disponibilité des ordinateurs virtuels](./virtual-machines-linux-manage-availability.md).

## <a name="create-the-linux-vms"></a>Créer des ordinateurs virtuels Linux

Vous avez créé les ressources de stockage et de réseau pour prendre en charge les ordinateurs virtuels accessibles sur Internet. Maintenant nous allons créer des ordinateurs virtuels et les sécuriser avec une clé SSH n’ayant pas un mot de passe. Dans ce cas, nous allons créer une machine virtuelle en fonction de la plus récente LTS d’Ubuntu. Nous trouver ces informations d’image à l’aide de `azure vm image list`, comme décrit dans la [recherche d’images Azure VM](virtual-machines-linux-cli-ps-findimage.md).

Nous avons sélectionné une image à l’aide de la commande `azure vm image list westeurope canonical | grep LTS`. Dans ce cas, nous utilisons `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Pour le dernier champ, nous passons `latest` afin que l’avenir nous toujours obtenir la version la plus récente. (La chaîne que nous utilisons est `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Cette étape est familier à quiconque a déjà créé un ssh les clés publique et privée rsa paire sous Linux ou Mac à l’aide de **ssh keygen-t rsa -b 2048**. Si vous n’avez pas de n’importe quelle paire de clés de certificat votre `~/.ssh` répertoire, vous pouvez les créer :

- Automatiquement, à l’aide de la `azure vm create --generate-ssh-keys` option.
- Manuellement, en utilisant [les instructions pour les créer vous-même](virtual-machines-linux-mac-create-ssh-keys.md).

Vous pouvez également utiliser le `--admin-password` méthode pour authentifier les connexions SSH après la création de la machine virtuelle. Cette méthode est généralement moins sécurisée.

Nous créons la machine virtuelle en mettant tous nos ressources et informations avec les `azure vm create` commande :

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Sortie :

```bash
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Vous pouvez vous connecter à votre machine virtuelle immédiatement à l’aide de vos clés SSH par défaut. Assurez-vous que vous spécifiez le port approprié dans la mesure où nous sommes en passant par l’intermédiaire de l’équilibreur de charge. (Pour notre premier VM, nous paramétrer la règle NAT pour transférer le port 4222 de notre machine virtuelle) :

```bash
 ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Sortie :

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Continuez et créer votre deuxième VM de la même manière :

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Vous pouvez maintenant utiliser le `azure vm show myResourceGroup myVM1` commande pour examiner ce que vous avez créé. À ce stade, vous exécutez vos ordinateurs virtuels d’Ubuntu derrière un équilibreur de charge dans Azure que vous pouvez vous connecter à uniquement avec votre paire de clés SSH (car les mots de passe sont désactivées). Vous pouvez installer nginx ou httpd, déployer une application web et voir le trafic de flux par l’intermédiaire de l’équilibreur de charge à la fois à des ordinateurs virtuels.

```bash
azure vm show --resource-group myResourceGroup --name myVM1
```

Sortie :

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exporter l’environnement en tant que modèle
Maintenant que vous avez créé à cet environnement, que se passe-t-il si vous souhaitez créer un environnement de développement supplémentaires avec les mêmes paramètres, ou un environnement de production qui met en correspondance ? Le Gestionnaire de ressources utilise des modèles JSON qui définissent tous les paramètres de votre environnement. Vous développez des environnements entiers par référence à ce modèle JSON. Vous pouvez [créer manuellement des modèles JSON](../resource-group-authoring-templates.md) ou exporter un environnement existant pour créer le modèle JSON pour vous :

```bash
azure group export --name myResourceGroup
```

Cette commande crée le `myResourceGroup.json` le fichier dans votre répertoire de travail actuel. Lorsque vous créez un environnement à partir de ce modèle, vous êtes invité tous les noms de ressources, y compris les noms de l’équilibreur de charge, interfaces réseau, les ordinateurs virtuels. Vous pouvez remplir ces noms dans votre fichier modèle en ajoutant le `-p` ou `--includeParameterDefaultValue` paramètre pour le `azure group export` commande présentée précédemment. Modifiez votre modèle JSON pour spécifier les noms de ressources, ou [créer un fichier parameters.json](../resource-group-authoring-templates.md#parameters) qui spécifie les noms des ressources.

Pour créer un environnement à partir de votre modèle :

```bash
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Vous pouvez souhaiter lire [plus d’informations sur le déploiement à partir de modèles](../resource-group-template-deploy-cli.md). Découvrez comment mettre à jour les environnements de façon incrémentielle, utilisez le fichier de paramètres et accéder aux modèles à partir d’un emplacement de stockage unique.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à commencer à travailler avec plusieurs composants réseau et des ordinateurs virtuels. Vous pouvez utiliser cet environnement d’exemple pour générer votre application en utilisant les composants de base présentés ici.
