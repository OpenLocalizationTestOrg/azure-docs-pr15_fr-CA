<properties 
   pageTitle="Accélérer la mise en réseau pour un ordinateur virtuel - portail | Microsoft Azure"
   description="Découvrez comment configurer accéléré la mise en réseau pour un ordinateur virtuel Azure via le portail d’Azure."
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
   ms.date="09/26/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Accélérer la mise en réseau pour une machine virtuelle

> [AZURE.SELECTOR]
- [Azure Portal](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Mise en réseau rapide permet de virtualiser d’e/s unique racine (SR-IOV) à une machine virtuelle (VM), améliore considérablement les performances du réseau. Ce chemin d’accès de haute performance contourne l’hôte à partir de la datapath réduire la latence, l’instabilité et l’utilisation du processeur pour une utilisation avec les charges de travail les plus exigeantes réseau sur les types de machine virtuelle pris en charge. Cet article explique comment utiliser le portail Azure pour configurer accéléré la mise en réseau dans le modèle de déploiement du Gestionnaire de ressources Azure. Vous pouvez également créer un ordinateur virtuel avec accélération de réseau à l’aide de PowerShell d’Azure. Pour savoir comment procéder, cliquez sur la zone de PowerShell en haut de cet article.

L’illustration suivante montre la communication entre les deux machines virtuelles (VM) avec et sans accélération de réseau :

![Comparaison](./media/virtual-network-accelerated-networking-portal/image1.png)

Sans réseau accéléré, tout le trafic réseau entrant et sortant de la machine virtuelle doit parcourir l’hôte et la commutation virtuelle. Le commutateur virtuel fournit toutes les stratégies, tels que les groupes de sécurité réseau, listes de contrôle d’accès, d’isolation et autres services de réseau virtualisé pour le trafic réseau. Pour en savoir plus, lisez l’article de [la virtualisation du réseau Hyper-V et Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) .

Avec l’accélération de réseau, le trafic réseau arrive à la carte réseau (NIC) et est ensuite transmis à la machine virtuelle. Toutes les stratégies réseau qui le commutateur virtuel s’applique sans accélération réseau sont déchargées et appliqués dans le matériel. Application de la stratégie au niveau du matériel permet à la carte réseau pour transférer le trafic de réseau directement à la machine virtuelle, en ignorant l’hôte et la commutation virtuelle, tout en conservant toutes les stratégies qu’il est appliqué dans l’hôte.

Les avantages de l’accélération de réseau ne s’appliquer qu’il est activé sur la machine virtuelle. Pour de meilleurs résultats, il est idéal activer cette fonctionnalité sur au moins deux des ordinateurs virtuels connectés à la même VNet. Lors de la communication au sein de VNets ou de connexion sur site, cette fonction a un impact minimal sur la latence globale.

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

1. Inscrivez-vous pour l’aperçu en envoyant un e-mail à [Accélérer les abonnements de mise en réseau](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) avec votre ID d’abonnement et l’utilisation prévue. N’effectuez pas les étapes restantes jusqu'à ce que, après avoir reçu un courrier électronique vous informant que vous avez été accepté dans l’aperçu.
2. Connexion au portail Azure à http://portal.azure.com.
3. Créer un ordinateur virtuel en effectuant les étapes décrites dans l’article [créer un ordinateur virtuel de Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) en sélectionnant les options suivantes :
    - Sélectionnez un système d’exploitation répertorié dans la section Limitations de cet article.
    - Sélectionnez un emplacement (région) répertorié dans la section Limitations de cet article.
    - Sélectionnez une taille de mémoire virtuelle répertoriée dans la section Limitations de cet article. Si un des formats pris en charge n’est pas répertorié, cliquez sur **Afficher tout** dans la lame de **Choisir une taille** pour sélectionner une taille à partir d’une liste de développé.
    - Dans la lame de **paramètres** , cliquez sur *activé* pour **les réseaux accéléré**, comme illustré dans l’image suivante :

        ![Paramètres](./media/virtual-network-accelerated-networking-portal/image3.png)

    >[AZURE.NOTE] L’accélération de réseau ne sera visible que si vous avez :
    >
    >- Été acceptée dans l’aperçu
    >- Sélectionner les systèmes d’exploitation pris en charge, l’emplacement et taille de machine virtuelle mentionné dans la section Limitations de cet article.

5. Une fois que la machine virtuelle est créée, téléchargez le [pilote d’accélérer la mise en réseau](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), vous connecter et connectez-vous à la machine virtuelle et exécuter le programme d’installation de pilote à l’intérieur de la machine virtuelle.
6. Cliquez sur le bouton de Windows, puis cliquez sur **Gestionnaire de périphériques**. Vérifiez que la **Carte Ethernet fonction virtuelle de ConnectX de Mellanox-3** apparaît sous l’option **réseau** lorsque celui-ci est développé, comme illustré dans l’image suivante :

    ![Gestionnaire de périphériques](./media/virtual-network-accelerated-networking-portal/image2.png)