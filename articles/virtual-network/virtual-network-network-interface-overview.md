<properties 
   pageTitle="Interfaces réseau | Microsoft Azure"
   description="Obtenir des informations sur les interfaces réseau Azure dans le Gestionnaire de ressources Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# <a name="network-interfaces"></a>Interfaces réseau

Une interface réseau (NIC) est l’interconnexion entre une Machine virtuelle (VM) et le réseau de logiciel sous-jacent. Cet article explique une interface réseau est et comment il est utilisé dans le modèle de déploiement du Gestionnaire de ressources Azure.

Microsoft recommande de déployer de nouvelles ressources à l’aide du modèle de déploiement du Gestionnaire de ressources, mais vous pouvez également déployer des machines virtuelles grâce à la connectivité de réseau dans le modèle de déploiement [classique](virtual-network-ip-addresses-overview-classic.md) . Si vous êtes familiarisé avec le modèle classique, il existe des différences importantes dans la mise en réseau de la machine virtuelle dans le modèle de déploiement du Gestionnaire de ressources. Pour en savoir plus sur les différences, en lisant l’article de la [mise en réseau machine virtuelle - classique](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

Dans Azure, il s’agit d’une interface réseau :

1. Est une ressource qui peut être créée, supprimé, et dispose de ses propres paramètres configurables.
2. Doit être connecté à un sous-réseau dans un réseau virtuel Azure (VNet) lors de sa création. Si vous n’êtes pas familier avec VNets, en savoir plus à leur sujet en lisant l’article de [Présentation de réseau virtuel](virtual-networks-overview.md) . La carte réseau doit être connectée à un VNet qui existe dans le même Azure [emplacement](https://azure.microsoft.com/regions) et [abonnement](../azure-glossary-cloud-terminology.md#subscription) comme la carte réseau. Après la création d’une carte réseau, vous pouvez modifier le sous-réseau qu’il est connecté à, mais vous ne pouvez pas modifier le VNet qu’il est connecté à.
3. A un nom affecté qui ne peut pas être modifié après la création de la carte réseau. Le nom doit être unique au sein d’un [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups)d' Azure, mais ne doit pas être unique au sein de l’abonnement ou le VNet qu’il est connecté à l’emplacement Azure, qu'il est créé. Plusieurs cartes d’interface réseau sont généralement créés dans un abonnement Azure. Il est recommandé que vous concevoir une convention d’affectation de noms qui facilite la gestion de plusieurs cartes réseau est plus facile que l’utilisation de noms par défaut. Consultez l’article [recommandé des conventions d’attribution de noms pour les ressources Azure](../guidance/guidance-naming-conventions.md) pour obtenir des suggestions.
4. Peuvent être reliés à un ordinateur virtuel, mais peuvent uniquement être associés à une machine virtuelle unique qui existe dans le même emplacement que la carte réseau.
5. A une adresse MAC, qui est conservée avec la carte d’interface réseau tant qu’il reste attaché à une machine virtuelle. L’adresse MAC est conservé si la machine virtuelle est redémarrée (à partir du système d’exploitation) ou arrêté (désallouée) et commencé à utiliser le portail Azure, Azure PowerShell ou l’Interface de ligne de commande Azure. Si elle a détaché à partir d’un ordinateur virtuel et attaché à une VM différente, la carte réseau reçoit une adresse MAC différente. Si la carte réseau est supprimée, l’adresse MAC est attribuée aux autres cartes réseau.
6. Doit lui ont un principal **privé** adresse *IPv4* statique ou dynamique IP affectées.
8. Peut être une ressource d’adresse IP publique associés à lui.
9. Prend en charge l’accélération d’un réseau avec la virtualisation (SR-IOV) de racine unique d’e/s de tailles spécifiques de machine virtuelle exécutant des versions spécifiques du système d’exploitation Microsoft Windows Server. Pour en savoir plus sur cette fonctionnalité d’aperçu, lisez l’article de [accéléré la mise en réseau pour un ordinateur virtuel](virtual-network-accelerated-networking-powershell.md) .
10. Peuvent recevoir du trafic non destiné à des adresses IP privées affectés si le routage IP est activé pour la carte réseau. Si un ordinateur virtuel est en cours d’exécution un logiciel pare-feu par exemple, il achemine les paquets non destinés à ses propres adresses IP. La machine virtuelle doit toujours exécuter le logiciel capable de router ou de transfert du trafic, mais pour ce faire, le transfert IP doit être activé pour une carte réseau.
11. Est souvent créé dans le même groupe de ressources que l’ordinateur virtuel auquel il est attaché ou le même VNet auquel il est connecté, mais il n’est pas nécessaire d’être.

## <a name="vms-with-multiple-network-interfaces"></a>Machines virtuelles avec plusieurs interfaces réseau

Plusieurs cartes réseau peut être associés à une machine virtuelle, mais lorsque cela, gardez à l’esprit les éléments suivants :  

- La taille de la mémoire virtuelle doit prendre en charge plusieurs cartes réseau. Pour en savoir plus sur les tailles de machine virtuelle prend en charge plusieurs cartes réseau, lisez les articles de la [taille de la mémoire virtuelle de Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) ou [tailles de Virtual Media Linux](../virtual-machines/virtual-machines-linux-sizes.md) .   
- La machine virtuelle doit être créée avec au moins deux cartes réseau. Si la machine virtuelle est créée avec une seule carte réseau, même si la taille de la mémoire virtuelle prend en charge plus d’un, vous ne peut pas joindre des cartes réseau de la machine virtuelle après sa création. Tant que l’ordinateur virtuel a été créé avec au moins deux cartes réseau, vous pouvez joindre des cartes réseau de la machine virtuelle après sa création, tant que la taille de la mémoire virtuelle prend en charge plus de deux cartes réseau.  
- Vous pouvez détacher NIC secondaire (la carte réseau principale ne peut pas être détachée) à partir d’un ordinateur virtuel si l’ordinateur virtuel possède au moins trois cartes d’interface réseau attachés. Impossible de détacher des cartes réseau si la machine virtuelle a deux ou moins cartes réseau attaché.  
- L’ordre des cartes réseau à partir de l’intérieur de la machine virtuelle sera aléatoire et peut également changer entre les mises à jour de l’infrastructure Azure. Toutefois, les adresses IP et les correspondants ethernet MAC adresses, reste la même. Par exemple, supposons que le système d’exploitation identifie NIC1 Azure comme Eth1. Eth1 a l’adresse IP 10.1.0.100 et adresse MAC 00-0D-3A-B0-39-0D. Après une infrastructure Azure mise à jour et redémarrer, le système d’exploitation peut à présent identifier NIC1 Azure comme Eth2, mais les adresses IP et MAC sera le même telles qu’elles étaient lorsque le système d’exploitation identifié NIC1 Azure comme Eth1. Lorsqu’un redémarrage est lancée par le client, la commande de la carte d’interface réseau reste la même au sein du système d’exploitation.  
- Si la machine virtuelle est un membre d’un [jeu de disponibilité](../azure-glossary-cloud-terminology.md#availability-set), tous les ordinateurs virtuels dans le jeu de disponibilité doivent avoir une carte réseau unique ou plusieurs cartes réseau. Si les ordinateurs virtuels ont plusieurs cartes réseau, le nombre ils ont chacun n’est pas obligatoirement être identiques, tant que chaque machine virtuelle possède au moins deux cartes réseau.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à créer un ordinateur virtuel avec une seule carte réseau en consultant l’article [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
- Apprenez à créer un ordinateur virtuel avec plusieurs cartes réseau en consultant l’article de [déployer un ordinateur virtuel avec la carte d’interface réseau multiples](virtual-network-deploy-multinic-arm-ps.md) .
- Découvrez comment créer une carte d’interface réseau avec plusieurs configurations IP en lisant l’article [adresses IP multiples pour les machines virtuelles Azure](virtual-network-multiple-ip-addresses-powershell.md) .
