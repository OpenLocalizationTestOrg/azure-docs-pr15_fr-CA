<properties
   pageTitle="Ouvrir des ports pour une machine virtuelle via le portail Azure | Microsoft Azure"
   description="Découvrez comment ouvrir un port / créer un point de terminaison de votre machine virtuelle de Windows en utilisant le modèle de déploiement de gestionnaire de ressources dans le portail Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>Ouverture de ports à une machine virtuelle dans Azure via le portail Azure
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Commandes rapides
Vous pouvez également [effectuer ces étapes à l’aide de PowerShell d’Azure](virtual-machines-windows-nsg-quickstart-powershell.md).

Commencez par créer le groupe de sécurité du réseau. Sélectionnez un groupe de ressources dans le portail, cliquez sur **Ajouter**, puis recherchez et sélectionnez « Groupe de sécurité du réseau » :

![Ajouter un groupe de sécurité du réseau](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Entrez un nom pour votre groupe de sécurité réseau ou créer un groupe de ressources et sélectionnez un emplacement. Cliquez sur **Create** lorsque vous avez terminé :

![Créer un groupe de sécurité réseau](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Sélectionnez un nouveau groupe de sécurité de réseau. Sélectionnez les règles de sécurité entrante, puis cliquez sur le bouton **Ajouter** pour créer une règle :

![Ajouter une règle de trafic entrant](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Fournissez un nom pour votre nouvelle règle. Le port 80 est déjà entré par défaut. Cette blade est où vous modifieriez la source, le protocole et la destination lors de l’ajout de règles supplémentaires à votre groupe de sécurité du réseau. Cliquez sur **OK** pour créer la règle :

![Créer une règle de trafic entrant](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

L’étape finale consiste à associer un sous-réseau ou une interface réseau spécifique dans votre groupe de sécurité du réseau. Nous allons associer le groupe de sécurité réseau à un sous-réseau. Sélectionnez « Sous-réseaux », puis cliquez sur **associer**:

![Associer un groupe de sécurité de réseau d’un sous-réseau](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Sélectionnez votre réseau virtuel et sélectionnez le sous-réseau approprié :

![Association d’un groupe de sécurité de réseau avec un réseau virtuel](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Vous avez maintenant créé un groupe de sécurité réseau, créé une règle de trafic entrant qui autorise le trafic sur le port 80 et associé à un sous-réseau. Les ordinateurs virtuels que vous vous connectez à ce sous-réseau sont accessibles sur le port 80.


## <a name="more-information-on-network-security-groups"></a>Plus d’informations sur les groupes de sécurité de réseau
Les commandes rapides ici permettent d’obtenir en cours d’exécution avec le trafic de votre machine virtuelle. Les groupes de sécurité de réseau fournissent de nombreuses fonctionnalités et la granularité pour contrôler l’accès à vos ressources. Vous pouvez en savoir plus sur la [Création d’un groupe de sécurité du réseau et des règles d’ACL ici](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Vous pouvez définir des groupes de sécurité réseau et les règles de la liste ACL dans le cadre des modèles du Gestionnaire de ressources Azure. Pour en savoir plus sur la [Création de groupes de sécurité de réseau avec les modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser réacheminement de port pour mapper un unique port externe à un port interne sur votre ordinateur virtuel, utilisez un équilibreur de charge et des règles de traduction d’adresses réseau (NAT). Par exemple, vous souhaitez exposer le port TCP 8080 en externe et que le trafic dirigé vers le port TCP 80 sur une machine virtuelle. Vous pouvez en savoir plus sur la [Création d’un équilibreur de charge pour Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous pouvez trouver des informations sur la création d’environnements plus détaillées dans les articles suivants :

- [Vue d’ensemble de Gestionnaire des ressources Azure](../azure-resource-manager/resource-group-overview.md)
- [En quoi consiste un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)
- [Vue d’ensemble de Gestionnaire des ressources Azure pour les équilibreurs de charge](../load-balancer/load-balancer-arm.md)
