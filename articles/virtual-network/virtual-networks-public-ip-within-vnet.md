<properties 
   pageTitle="Comment faire pour utiliser des adresses IP publiques sur un réseau virtuel"
   description="Apprenez à configurer un réseau virtuel pour utiliser des adresses IP publiques"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="public-ip-address-space-in-a-virtual-network-vnet"></a>Espace d’adressage IP public dans un réseau virtuel (VNet)

Les réseaux virtuels (VNets) peuvent contenir à la fois publics et privés des espaces d’adressage (blocs d’adresses RFC 1918) IP. Lorsque vous ajoutez une plage d’adresses IP publique, il est considéré comme faisant partie de l’espace d’adressage VNet IP privé qui est uniquement accessible dans la VNet, VNets interconnectés et à partir de votre emplacement local.

L’illustration ci-dessous montre un VNet qui comprend des espaces d’adresse IP publiques et privées.

![IP publique conceptuel](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## <a name="how-do-i-add-a-public-ip-address-range"></a>Comment pour ajouter une plage d’adresses IP publique ?

Vous ajoutez une plage d’adresses IP publique de la même manière que vous ajouteriez une plage d’adresses IP privées ; en utilisant un fichier *netcfg* , ou par l’ajout de la configuration du [portail Azure](http://portal.azure.com). Vous pouvez ajouter une plage d’adresses IP publique lorsque vous créez votre VNet ou vous pouvez revenir en arrière et l’ajouter par la suite. L’exemple ci-dessous montre à la fois publics et privés espaces d’adressage IP configurés dans le même VNet.

![Adresse IP publique de portail](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## <a name="are-there-any-limitations"></a>Y a-t-il des restrictions ?

Il existe plusieurs plages d’adresses IP qui ne sont pas autorisés :

- 224.0.0.0/4 (multidiffusion)

- 255.255.255.255/32 (diffusion)

- 127.0.0.0/8 (en boucle)

- 169.254.0.0/16 (lien-local)

- 168.63.129.16/32 (DNS interne)

## <a name="next-steps"></a>Étapes suivantes

[Comment faire pour gérer des serveurs DNS utilisés par un VNet](virtual-networks-manage-dns-in-vnet.md)