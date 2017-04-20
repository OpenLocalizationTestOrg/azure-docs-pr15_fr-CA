<properties
   pageTitle="Déploiement des ressources avec Azure modèles du Gestionnaire de ressources de calcul | Microsoft Azure"
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

# <a name="application-architecture-with-azure-resource-manager-templates"></a>Architecture de l’application avec le Gestionnaire de ressources Azure modèles

Lors du développement d’un déploiement d’Azure le Gestionnaire de ressources, les exigences de calcul doivent être mappées aux services et aux ressources Azure. Si une application se compose de plusieurs points de terminaison http, une base de données et un service de mise en cache de données, les ressources Azure qui hébergent ces composants doit être de version rationalisé. Par exemple, l’exemple d’application magasin de musique comprend une application web qui est hébergée sur un ordinateur virtuel et une base de données SQL, qui est hébergé dans la base de données de SQL d’Azure. 

Ce document décrit en détail comment les ressources de calcul magasin de musique sont configurés dans l’exemple de modèle de gestionnaire de ressources Azure. Toutes les dépendances et les configurations uniques sont mis en surbrillance. Pour optimiser les performances, avant de déployer une instance de la solution à votre abonnement Azure et le travail avec le modèle de gestionnaire de ressources Azure. Le modèle terminé se trouve ici – [Déploiement du magasin de musique sur Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="virtual-machine"></a>Machine virtuelle

L’application de magasin de musique inclut une application web où les clients peuvent naviguer et acheter de la musique. Bien qu’il existe plusieurs services Azure qui peuvent héberger des applications web, pour cet exemple, un ordinateur virtuel est utilisé. À l’aide de l’exemple de modèle de magasin de musique, un ordinateur virtuel est déployé, installation d’un serveur web et le site Web du magasin de musique installés et configurés. Pour des raisons de cet article, seulement le déploiement de la machine virtuelle est détaillé. La configuration du serveur web et l’application est détaillée dans un article ultérieurement.

Un ordinateur virtuel peuvent être ajouté à un modèle à l’aide de l’Assistant Ajouter un nouvelle ressource de Visual Studio, ou en insérant un JSON valide dans le modèle de déploiement. Lorsque vous déployez un ordinateur virtuel, plusieurs ressources connexes sont également nécessaires. Si vous utilisez Visual Studio pour créer le modèle, ces ressources sont créées pour vous. Si vous construisez manuellement le modèle, ces ressources doivent être insérés et configurés.

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [JSON de la Machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L295).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
      ........<truncated>  
    }
```

Une fois déployé, les propriétés de la machine virtuelle est visible dans le portail Azure.

![Machine virtuelle](./media/virtual-machines-linux-dotnet-core/vm.png)

## <a name="storage-account"></a>Compte de stockage

Comptes de stockage disposent de nombreuses options de stockage et les fonctionnalités. Pour le contexte de machines virtuelles d’Azure, un compte de stockage conserve des disques durs virtuels de la machine virtuelle et les disques de données supplémentaires. L’exemple de magasin de musique inclut un compte de stockage pour stocker le disque dur virtuel de chaque machine virtuelle dans le déploiement. 

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Compte de stockage](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L109).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

Un compte de stockage est associée à un ordinateur virtuel à l’intérieur de la déclaration de modèle de gestionnaire de ressources de l’ordinateur virtuel. 

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [association de Machine virtuelle et le compte de stockage](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L341).

```none
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

Après le déploiement, du compte de stockage peut être affiché dans le portail Azure.

![Compte de stockage](./media/virtual-machines-linux-dotnet-core/storacct.png)

En cliquant sur dans le conteneur de blob de compte de stockage, le fichier de disque dur virtuel pour chaque ordinateur virtuel déployé avec le modèle peut être affichée.

![Disques durs virtuels](./media/virtual-machines-linux-dotnet-core/vhd.png)

Pour plus d’informations sur le stockage Azure, consultez la [documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/).

## <a name="virtual-network"></a>Réseau virtuel

Si un ordinateur virtuel requiert un réseau interne, telles que la capacité de communiquer avec d’autres machines virtuelles et les ressources d’Azure, un réseau virtuel Azure est requis.  Un réseau virtuel ne rend pas la machine virtuelle accessible sur internet. La connectivité nécessite une adresse IP publique qui est détaillée plus loin dans cette série.

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [réseau virtuel et les sous-réseaux](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L136).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
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
    ]
  }
}
```

À partir du portail Azure, le réseau virtuel ressemble à l’image suivante. Notez que tous les ordinateurs virtuels déployés avec le modèle sont connectés au réseau virtuel.

![Réseau virtuel](./media/virtual-machines-linux-dotnet-core/vnet.png)

## <a name="network-interface"></a>Interface réseau

 Une interface réseau connecte à un ordinateur virtuel à un réseau virtuel, et plus spécifiquement à un sous-réseau qui a été défini dans le réseau virtuel. 
 
 Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources : [Interface réseau](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L166).
 
```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceNamePrefix'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'SSH-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/SSH-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Chaque ressource d’ordinateur virtuel comprend un profil de réseau. L’interface réseau est associé à la machine virtuelle dans ce profil.  

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – le [Profil de réseau de Machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L350).


```none
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceNamePrefix'), copyindex()))]"
    }
  ]
}
```

À partir du portail Azure, l’interface réseau ressemble à l’image suivante. L’adresse IP interne et l’association de la machine virtuelle peuvent être vu dans la ressource d’interface réseau.

![Interface réseau](./media/virtual-machines-linux-dotnet-core/nic.png)

Pour plus d’informations sur les réseaux virtuels Azure, consultez la [documentation réseau virtuel d’Azure](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="azure-sql-database"></a>Base de données SQL Azure

En plus d’une machine virtuelle qui héberge le site Web du magasin de musique, une base de données de SQL Azure est déployé pour héberger la base de données de magasin de musique. L’avantage de l’utilisation de base de données de SQL Azure ici est qu’un second jeu d’ordinateurs virtuels n’est pas nécessaire et évolutivité et disponibilité est généré dans le service.

Une base de données Azure SQL peut être ajouté à l’aide de la Visual Studio ajouter une nouvelle ressource, Assistant, ou en insérant des JSON valide dans un modèle. La ressource de SQL Server inclut un nom d’utilisateur et le mot de passe disposant de droits d’administration sur l’instance SQL. En outre, une ressource de pare-feu SQL est ajoutée. Par défaut, les applications hébergées dans Azure sont en mesure de se connecter à l’instance SQL. Un studio de gestion de SQL Server pour se connecter à l’instance SQL, le pare-feu doit être configuré pour autoriser une application externe. Pour la démonstration de magasin de musique, la configuration par défaut fonctionne correctement. 

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Base de données SQL Azure](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L401).


```none
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicStoreSqlName')]",
  "location": "[resourceGroup().location]",

  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('sqlAdminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicStoreSqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Affichage du serveur SQL et de MusicStore de base de données comme indiqué dans le portail Azure.

![SQL Server](./media/virtual-machines-linux-dotnet-core/sql.png)

Pour plus d’informations sur le déploiement de base de données de SQL Azure, consultez la [documentation de la base de données de SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/).

## <a name="next-step"></a>Étape suivante

<hr>

[Étape 2 : accès et sécurité dans Azure modèles du Gestionnaire de ressources](./virtual-machines-linux-dotnet-core-3-access-security.md)
