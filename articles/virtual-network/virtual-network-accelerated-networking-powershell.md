<properties 
   pageTitle="Accélérer la mise en réseau pour une machine virtuelle - PowerShell | Microsoft Azure"
   description="Découvrez comment configurer accéléré la mise en réseau pour un ordinateur virtuel Azure à l’aide de PowerShell."
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

# <a name="accelerated-networking-for-a-virtual-machine"></a>Accélérer la mise en réseau pour une machine virtuelle

> [AZURE.SELECTOR]
- [Azure Portal](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Mise en réseau rapide permet de virtualiser d’e/s unique racine (SR-IOV) à une machine virtuelle (VM), améliore considérablement les performances du réseau. Ce chemin d’accès de haute performance contourne l’hôte à partir de la datapath réduire la latence, l’instabilité et l’utilisation du processeur pour une utilisation avec les charges de travail les plus exigeantes réseau sur les types de machine virtuelle pris en charge. Cet article explique comment utiliser Azure PowerShell pour configurer accéléré la mise en réseau dans le modèle de déploiement du Gestionnaire de ressources Azure. Vous pouvez également créer un ordinateur virtuel avec accélération réseau via le portail d’Azure. Pour savoir comment procéder, cliquez sur la zone de portail Azure en haut de cet article.

L’illustration suivante montre la communication entre les deux machines virtuelles (VM) avec et sans accélération de réseau :

![Comparaison](./media/virtual-network-accelerated-networking-powershell/image1.png)

Sans réseau accéléré, tout le trafic réseau entrant et sortant de la machine virtuelle doit parcourir l’hôte et la commutation virtuelle. Le commutateur virtuel fournit toutes les stratégies, tels que les groupes de sécurité réseau, listes de contrôle d’accès, d’isolation et autres services de réseau virtualisé pour le trafic réseau. Pour en savoir plus, lisez l’article de [la virtualisation du réseau Hyper-V et Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) .

Avec l’accélération de réseau, le trafic réseau arrive à la carte réseau (NIC) et est ensuite transmis à la machine virtuelle. Toutes les stratégies réseau qui le commutateur virtuel s’applique sans accélération réseau sont déchargées et appliqués dans le matériel. Application de la stratégie au niveau du matériel permet à la carte réseau pour transférer le trafic de réseau directement à la machine virtuelle, en ignorant l’hôte et la commutation virtuelle, tout en conservant toutes les stratégies qu’il est appliqué dans l’hôte.

Les avantages de l’accélération de réseau ne s’appliquer qu’il est activé sur la machine virtuelle. Pour de meilleurs résultats, il est idéal activer cette fonctionnalité sur au moins deux des ordinateurs virtuels connectés à la même VNet.  Lors de la communication au sein de VNets ou de connexion sur site, cette fonction a un impact minimal sur la latence globale.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Avantages

- **Une latence inférieure / supérieure de paquets par seconde (pps) :** Supprimer le commutateur virtuel le datapath supprime le temps de paquets de l’hôte pour le traitement de la stratégie et augmente le nombre de paquets pouvant être traitées à l’intérieur de la machine virtuelle.
- **Réduction de gigue :** Traitement de commutateur virtuel dépend de la stratégie doit être appliquée et la charge de travail du processeur qui effectue le traitement. L’application de la stratégie pour le matériel de déchargement supprime cette variabilité par remettre des paquets directement à la machine virtuelle, la suppression de l’hôte pour la communication de la machine virtuelle et toutes les interruptions de logiciel et les changements de contexte.
- **a diminué de l’UC :** En contournant la commutation virtuelle dans l’hôte entraîne moins l’utilisation du processeur pour le traitement du trafic réseau.

## <a name="limitations"></a>Limitations

Les restrictions suivantes existent lors de l’utilisation de cette fonctionnalité :
 
- **Création de l’interface de réseau :** Mise en réseau rapide ne peut être activée que pour une nouvelle interface réseau.  Il ne peut pas être activé sur une interface réseau existante.
- **Création de machine virtuelle :** Une interface de réseau avec un réseau accéléré activé ne peut être attachée à une machine virtuelle lors de la création de la machine virtuelle. L’interface réseau ne peut être attaché à une machine virtuelle existante.
- **Régions :** Proposé dans les régions ouest États-Unis et Azure d’Europe occidentale. L’ensemble des régions se développera à l’avenir.
- **Système d’exploitation pris en charge :** Microsoft Windows Server 2012 R2 et Windows Server 2016 Aperçu technique 5. Prise en charge de Linux et Windows Server 2012 est bientôt ajouté.
- **Taille de mémoire virtuelle :** Standard_D15_v2 et Standard_DS15_v2 sont que la seule prise en charge des tailles d’instance de machine virtuelle. Pour plus d’informations, consultez l’article de la [taille de la mémoire virtuelle de Windows](../virtual-machines/virtual-machines-windows-sizes.md) . L’ensemble des tailles d’instance VM pris en charge se développera à l’avenir.

Les modifications apportées à ces limites seront annoncées par le biais de la page [mises à jour de la mise en réseau virtuel d’Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Créer un ordinateur virtuel de Windows avec un réseau accéléré

1. Ouvrez une invite de commande PowerShell et terminer les étapes restantes de cette section au sein d’une seule session de PowerShell. Si vous n’avez pas installé et configuré de PowerShell, suivez les étapes de l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .
2. Pour obtenir l’aperçu, envoyez un e-mail à [Accélérer les abonnements de mise en réseau](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) avec votre ID d’abonnement et de la destination. N’effectuez pas les étapes restantes jusqu'à ce que, après avoir reçu un courrier électronique vous informant que vous avez été accepté dans l’aperçu.
3. Enregistrer la capacité de votre abonnement en entrant les commandes suivantes :

        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

4. Remplacez *westcentralus* par le nom d’un autre emplacement pris en charge par cette fonctionnalité répertoriée dans la section [Limitations](#limitations) de cet article (si vous le souhaitez). Entrez la commande suivante pour définir une variable pour l’emplacement :

        $locName = "westcentralus"

5. Remplacez *RG1* avec un nom pour le groupe de ressources qui contiendra la nouvelle interface réseau et entrez les commandes suivantes pour la créer :

        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

6. Modifier *NIC1 VM1* à ce que vous voulez nommer l’interface réseau et puis entrez la commande suivante :

        $NICName = "VM1-NIC1"

7. L’interface réseau doit être connecté à un sous-réseau au sein d’un réseau virtuel existant Azure (VNet) dans le même emplacement et [abonnement](../azure-glossary-cloud-terminology.md#subscription) comme l’interface réseau. Plus d’informations sur VNets en lisant l’article de [Présentation de réseau virtuel](virtual-networks-overview.md) si vous n’êtes pas familier avec eux. Pour créer un VNet, suivez les étapes décrites dans l’article [créer un VNet](virtual-networks-create-vnet-arm-ps.md) . Modifier les « valeurs » de la $Variables suivant au nom de la VNet et le sous-réseau que vous souhaitez vous connecter à l’interface réseau.

        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"

    Si vous ne connaissez pas le nom d’un VNet existant dans l’emplacement que vous avez choisi à l’étape 3, entrez les commandes suivantes :
        
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
        
    Si la liste retournée est vide, vous devez créer un VNet dans l’emplacement. Pour créer un VNet, suivez les étapes décrites dans l’article [créer un réseau virtuel](virtual-networks-create-vnet-arm-ps.md) .

    Pour obtenir le nom des sous-réseaux au sein de la VNet, tapez les commandes suivantes et remplacez *Subnet1* ci-dessus par le nom d’un sous-réseau :
        
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

8. Entrez les commandes suivantes pour récupérer le VNet et le sous-réseau et les affecter à des variables.

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

9. Identifier une existante ressource d’adresse IP publique qui peut être associée à l’interface réseau pour pouvoir vous connecter à celui-ci via Internet. Si vous ne souhaitez pas accéder à la machine virtuelle avec l’interface réseau sur Internet, vous pouvez ignorer cette étape. Sans une adresse IP publique, vous devez vous connecter à la machine virtuelle à partir d’une autre VM connectée à la même VNet. 

    Remplacez *PIP1* par le nom de ressource adresse IP existant public qui existe à l’emplacement que vous créez dans l’interface du réseau et qui n’est pas associé à une autre interface de réseau. Si nécessaire, modifiez $rgName pour le nom du groupe de ressources, la ressource d’adresse IP publique existe dans et entrez la commande suivante :

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName

    Entrez les commandes suivantes si vous ne connaissez pas le nom d’une ressource d’adresse IP publique existante :

        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration

    Si la colonne **IPFichier** a aucune valeur dans le résultat renvoyé, la ressource d’adresse IP publique n’est pas associée à une interface de réseau existante et peut être utilisée. Si la liste est vide ou si aucune ressource d’adresse IP publique disponible, vous pouvez en créer un à l’aide de la commande New-AzureRmPublicIPAddress.

    >[AZURE.NOTE] Les adresses IP publiques ont une somme insignifiante. Pour en savoir plus sur la tarification en lisant la page [tarification d’adresse IP](https://azure.microsoft.com/pricing/details/ip-addresses) .
10. Si vous avez choisi ne pas à ajouter une ressource d’adresse IP publique de l’interface, supprimez *- PublicIPAddress PIP1 $* à la fin de la commande qui suit. Créer l’interface de réseau avec un réseau accéléré en entrant la commande suivante :

        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 

11. Affecter l’interface de réseau à une machine virtuelle lors de la création de la machine virtuelle en suivant les instructions des étapes 3 et 6 de l’article [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-ps-create.md) . À l’étape 6-2, remplacez *Standard_A1* par une des tailles de mémoire virtuelle répertoriés dans la section [Limitations](#limitations) de cet article.

    >[AZURE.NOTE] Si vous avez modifié le *nom* de la $locName, $rgName ou $nic des variables dans cet article, l’étape 6 de la créer un article de la machine virtuelle échouera. Cependant, vous pouvez modifier les *valeurs* des variables.

12. Une fois que la machine virtuelle est créée, télécharger le [pilote d’accélérer la mise en réseau](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)de se connecter à la machine virtuelle et exécuter le programme d’installation de pilote à l’intérieur de la machine virtuelle.

13. Cliquez sur le bouton de Windows, puis cliquez sur **Gestionnaire de périphériques**. Vérifiez que la **Carte Ethernet fonction virtuelle de ConnectX de Mellanox-3** apparaît sous l’option **réseau** lorsque celui-ci est développé, comme illustré dans l’image suivante :

    ![Gestionnaire de périphériques](./media/virtual-network-accelerated-networking-powershell/image2.png)