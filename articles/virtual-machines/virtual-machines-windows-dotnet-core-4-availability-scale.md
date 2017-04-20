<properties
   pageTitle="Disponibilité et évolutivité dans Azure modèles du Gestionnaire de ressources | Microsoft Azure"
   description="Didacticiel de DotNet Core Azure VM"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>Disponibilité et meilleure évolutivité dans les modèles du Gestionnaire de ressources Azure

Disponibilité et meilleure évolutivité font référence à la disponibilité et la capacité à satisfaire la demande. Si une application doit être jusqu'à 99,9 % du temps, il doit avoir une architecture qui permet aux ressources de calcul simultanées multiples. Par exemple, au lieu d’avoir un site Web, une configuration à un niveau plus élevé de disponibilité comprend plusieurs instances du même site, avec la technologie en face d’équilibrage. Dans cette configuration, une seule instance de l’application peut être récupérée de maintenance, tandis que les autres continuent à fonctionner. D’autre part, échelle fait référence à une capacité d’applications pour répondre à la demande. Avec une charge une application équilibrée, ajoutant ou supprimant des instances dans le pool permet à une application à l’échelle pour répondre à la demande.

Ce document décrit en détail comment le déploiement exemple de magasin de musique est configuré pour la disponibilité et l’échelle. Toutes les dépendances et les configurations uniques sont mis en surbrillance. Pour optimiser les performances, avant de déployer une instance de la solution à votre abonnement Azure et le travail avec le modèle de gestionnaire de ressources Azure. Le modèle terminé se trouve ici – [Déploiement du magasin de musique sous Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="availability-set"></a>Ensemble de disponibilité

Un ensemble de disponibilité englobe logiquement ordinateurs virtuels Azure sur les hôtes physiques et les autres composants d’infrastructures, tels que les blocs d’alimentation et de matériel de réseau physique. Jeux de disponibilité Assurez-vous que lors de la maintenance, de défaillance du périphérique ou autres temps d’arrêt, pas de toutes les machines virtuelles sont effectuées. Un ensemble de disponibilité peuvent être ajouté à un modèle d’Azure le Gestionnaire de ressources à l’aide de la Visual Studio Assistant Ajout de nouvelles ressources ou en insérant un JSON valide dans un modèle.

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Disponibilité définie](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L368).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "availability-set"
  },
  "properties": {
  }
}
```

Un ensemble de disponibilité est déclaré en tant que propriété d’une ressource d’ordinateur virtuel. 

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [disponibilité définir une association avec la Machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L302).

```none
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
La disponibilité est définie comme le portail Azure. Chaque machine virtuelle et les détails sur la configuration sont détaillées ici.

![Ensemble de disponibilité](./media/virtual-machines-windows-dotnet-core/ase-win.png)

Pour obtenir des informations détaillées sur les jeux de disponibilité, consultez [disponibilité de gérer des ordinateurs virtuels](./virtual-machines-windows-manage-availability.md). 

## <a name="network-load-balancer"></a>Équilibrage de la charge réseau

Considérant qu’un ensemble de disponibilité fournit la tolérance de panne, un équilibreur de charge rend de nombreuses instances de l’application disponible sur une adresse réseau unique. Plusieurs instances d’une application peuvent être hébergés sur de nombreuses machines virtuelles, chacune d’elles connecté à un équilibreur de charge. Comme l’accès à l’application, l’équilibreur de charge achemine la demande entrante entre les membres attachés. Un équilibreur de charge peuvent être ajouté à l’aide de la Visual Studio Assistant Ajout de nouvelles ressources, ou en insérant correctement mis en forme de ressource JSON dans le modèle de gestionnaire de ressources Azure.

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Équilibrage de la charge réseau](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L198).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer"
  },
  ........<truncated>
}
```

Étant donné que l’exemple d’application est exposé à internet avec une adresse IP publique, cette adresse est associée à l’équilibrage de charge. 

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources : [association d’équilibrage de la charge réseau avec l’adresse IP publique](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

À partir du portail Azure, la vue d’ensemble de programme d’équilibrage de la charge réseau affiche l’association de l’adresse IP publique.

![Équilibrage de la charge réseau](./media/virtual-machines-windows-dotnet-core/nlb-win.png)

## <a name="load-balancer-rule"></a>Règle d’équilibrage de la charge

Lorsque vous utilisez un équilibreur de charge, les règles sont configurées qui contrôle comment le trafic est équitablement entre les ressources prévues. Avec l’exemple d’application magasin de musique, le trafic arrive sur le port 80 de l’adresse IP publique et est distribué sur toutes les machines virtuelles, le port 80. 

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Règle d’équilibrage de la charge](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L226).


```none
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Affichage de la règle d’équilibrage de la charge réseau à partir du portail.

![Règle d’équilibrage de la charge réseau](./media/virtual-machines-windows-dotnet-core/lbrule-win.png)

## <a name="load-balancer-probe"></a>Sonde d’équilibrage de la charge

L’équilibreur de charge doit également surveiller chaque ordinateur virtuel afin que les demandes sont pris en charge uniquement pour les systèmes en cours d’exécution. Cette analyse intervient par la constante de détection d’un port prédéfini. Le déploiement de magasin de musique est configuré pour détecter le port 80 sur tous les ordinateurs virtuels inclus. 

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Sonde d’équilibrage de la charge](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L247).


```none
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

La sonde d’équilibreur de charge visible à partir du portail Azure.

![Sonde d’équilibrage de charge réseau](./media/virtual-machines-windows-dotnet-core/lbprobe-win.png)

## <a name="inbound-nat-rules"></a>NAT règles de trafic entrant

Lorsque vous utilisez un équilibreur de charge, les règles doivent être mis en place pour fournir un accès équilibré de non-charge à chaque Machine virtuelle. Par exemple, lorsque vous créez une connexion RDP avec chaque machine virtuelle, ce trafic ne doit pas être équilibrées, au lieu de cela un chemin d’accès prédéfini doit être configuré. Les chemins d’accès prédéterminés sont configurés à l’aide d’une ressource règle de NAT entrant. À l’aide de cette ressource, communication entrante peut être mappée à des Machines virtuelles individuelles. 

L’application magasin de musique, un port commençant à 5000 est mappé vers le port 3389 sur chaque ordinateur virtuel pour l’accès RDP. Le `copyindex()` fonction est utilisée pour incrémenter le port entrant, telles que le deuxième ordinateur virtuel reçoit un port entrant de 5001, la troisième 5002 et ainsi de suite.

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Entrant les règles NAT](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L260). 

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'RDP-VM', copyIndex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 3389,
    "enableFloatingIP": false
  }
}
```

Exemple d’une règle de trafic entrant NAT comme dans le portail Azure. Création d’une règle de protocole RDP NAT pour chaque ordinateur virtuel dans le déploiement.

![NAT règle de trafic entrant](./media/virtual-machines-windows-dotnet-core/natrule-win.png)

Pour plus d’informations sur l’équilibrage de charge réseau Azure, voir [équilibrage de la charge pour les services d’infrastructure Azure](./virtual-machines-windows-load-balance.md).

## <a name="deploy-multiple-vms"></a>Déployer plusieurs ordinateurs virtuels

Enfin, pour une disponibilité ensemble ou équilibreur de charge fonctionner efficacement, plusieurs machines virtuelles sont nécessaires. Plusieurs ordinateurs virtuels peuvent être déployés à l’aide de la fonction de copie de modèle Azure le Gestionnaire de ressources. À l’aide de la fonction de copie, il n’est pas nécessaire de définir un nombre fini de Machines virtuelles, plutôt cette valeur peut être fournie dynamiquement au moment du déploiement. La fonction de copie utilise le nombre d’instances à créer et les poignées de déployer le nombre approprié d’ordinateurs virtuels et les ressources associées.

Dans le modèle exemple de magasin de musique, un paramètre est défini dans un nombre d’instances. Ce numéro est utilisé dans le modèle lors de la création d’ordinateurs virtuels et les ressources associées.

```none
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 2,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
},
```

Sur la ressource d’ordinateur virtuel, la boucle de copie est donnée un nom et le numéro du paramètre d’instances permettent de contrôler le nombre de copies qui en résulte.

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Fonction de copie de Machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L290). 


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  }
```

L’itération actuelle de la fonction de copie est accessible avec la `copyIndex()` fonction. La valeur de la fonction index copie peut être utilisée pour nommer les ordinateurs virtuels et autres ressources. Par exemple, si deux instances d’un ordinateur virtuel sont déployées, ils besoin des noms différents. Le `copyIndex()` fonction peut être utilisée en tant que partie d’un nom de machine virtuelle pour créer un nom unique. Un exemple de la `copyindex()` utilisée pour nommer les fins de fonction est visible dans la ressource d’ordinateur virtuel. Ici, le nom d’ordinateur est la concaténation de la `vmName` paramètre et le `copyIndex()` fonction. 

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Fonction Index de copie](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L309). 


```none
"osProfile": {
  "computerName": "[concat(variables('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "adminPassword": "[parameters('adminPassword')]"
}
```

Le `copyIndex` fonction est utilisée plusieurs fois dans le modèle d’exemple de magasin de musique. Ressources et utilisation des fonctions `copyIndex` contenir tous les éléments spécifiques à une instance unique de l’ordinateur virtuel tel et l’interface de réseau, les règles d’équilibrage de la charge, et tout dépend des fonctions. 

Pour plus d’informations sur la fonction de copie, reportez-vous à la section [créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure](../resource-group-create-multiple.md).

## <a name="next-step"></a>Étape suivante

<hr>

[Étape 4 - déploiement d’applications avec modèles de gestionnaire de ressources Azure](./virtual-machines-windows-dotnet-core-5-app-deployment.md)