<properties
   pageTitle="Accès et sécurité dans modèles de gestionnaire de ressources Azure | Microsoft Azure" 
   description="Didacticiel de DotNet Core Azure VM"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="access-and-security-in-azure-resource-manager-templates"></a>Accès et la sécurité dans les modèles du Gestionnaire de ressources Azure

Applications hébergées dans Azure susceptible doivent être d’accès via internet ou un réseau privé virtuel / connexion Express itinéraire avec Azure. Avec l’exemple d’application magasin de musique, le site web est rendue disponible sur internet avec une adresse IP publique. Avec l’accès établi, les connexions à l’application et l’accès aux ressources machine virtuelle doivent être sécurisées. Cette sécurité d’accès est fournie avec un groupe de sécurité du réseau. 

Ce document décrit en détail comment l’application magasin de musique est sécurisée dans l’exemple de modèle de gestionnaire de ressources Azure. Toutes les dépendances et les configurations uniques sont mis en surbrillance. Pour optimiser les performances, avant de déployer une instance de la solution à votre abonnement Azure et le travail avec le modèle de gestionnaire de ressources Azure. Le modèle terminé se trouve ici – [Déploiement du magasin de musique sur Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


## <a name="public-ip-address"></a>Adresse IP publique

Pour fournir un accès public à une ressource d’Azure, une ressource d’adresse IP publique peut être utilisée. Adresse IP publique peut être configuré avec une adresse IP statique ou dynamique. Si une adresse dynamique est utilisée, et l’ordinateur virtuel est arrêté et libéré, les adresses est supprimé. Au prochain démarrage de l’ordinateur, il peut être affecté à une autre adresse IP publique. Pour empêcher la modification d’une adresse IP, une adresse IP réservée peut être utilisée. 

Une adresse IP publique peuvent être ajoutée à un modèle d’Azure le Gestionnaire de ressources à l’aide de la Visual Studio Assistant Ajout de nouvelles ressources, ou en insérant un JSON valide dans un modèle. 

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Adresse IP publique](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L121).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicipaddressName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "public-ip-front"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Une adresse IP publique peut être associée à une carte réseau virtuelle ou un équilibreur de charge. Dans cet exemple, le site Web du magasin de musique étant réparties sur plusieurs machines virtuelles, l’adresse IP publique est joint à l’équilibreur de charge.

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [association adresse IP publique avec équilibreur de charge](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

L’adresse IP publique vu à partir du portail Azure. Notez que l’adresse IP publique est associé à un équilibreur de charge et pas une machine virtuelle. Programmes d’équilibrage de charge réseau sont détaillées dans le document suivant de cette série.

![Adresse IP publique](./media/virtual-machines-linux-dotnet-core/pubip.png)

Pour plus d’informations sur les adresses IP publiques Azure, consultez [les adresses IP dans Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Groupe de sécurité réseau

Une fois que l’accès a été établie pour les ressources d’Azure, cet accès doit être limité. Pour les ordinateurs virtuels Azure, sécuriser l’accès s’effectue à l’aide d’un groupe de sécurité du réseau. L’exemple d’application magasin de musique, tous les accès à l’ordinateur virtuel sont restreint à l’exception de sur le port 80 pour l’accès http et le port 22 pour accès SSH. Un groupe de sécurité de réseau peuvent être ajouté à un modèle d’Azure le Gestionnaire de ressources à l’aide de la Visual Studio Assistant Ajout de nouvelles ressources, ou en insérant un JSON valide dans un modèle.

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – le [Groupe de sécurité réseau](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L68).

```none
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('nsgfront')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "nsg-front"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
}
```

Dans cet exemple, le groupe de sécurité du réseau est associée à l’objet de sous-réseau déclaré dans la ressource du réseau virtuel. 

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [association du groupe de sécurité de réseau avec le réseau virtuel](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L158).


```none
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
```

Voici à quoi ressemble le groupe de sécurité du réseau à partir du portail Azure. Notez qu’un NSG peut être associer à une interface sous-réseau et / ou de réseau. Dans ce cas, le NSG est associé à un sous-réseau. Dans cette configuration, les règles de trafic entrant s’appliquent à tous les ordinateurs virtuels sont connectés au sous-réseau.

![Groupe de sécurité réseau](./media/virtual-machines-linux-dotnet-core/nsg.png)

Pour obtenir des informations détaillées sur les groupes de sécurité réseau, reportez-vous à la section [qu’est un groupe de sécurité du réseau]( https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Étape suivante

<hr>

[Étape 3 : disponibilité et évolutivité dans Azure modèles du Gestionnaire de ressources](./virtual-machines-linux-dotnet-core-4-availability-scale.md)
