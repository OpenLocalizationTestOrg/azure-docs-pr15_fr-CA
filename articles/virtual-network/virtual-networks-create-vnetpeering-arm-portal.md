<properties
   pageTitle="Créer VNet Peering via le portail Azure | Microsoft Azure"
   description="Apprenez à créer un réseau virtuel à l’aide du portail Azure dans le Gestionnaire de ressources."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Créer un réseau virtuel peering via le portail Azure

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer un VNet homologation selon le scénario ci-dessus, à l’aide du portail Azure, suivez les étapes ci-dessous.

1. À partir d’un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous à votre compte Azure.
2. Pour établir des VNET d’homologation, vous devez créer deux liens, une pour chaque direction, entre deux VNets. Vous pouvez d’abord créer lien d’homologation VNET de VNET1 à VNET2. Dans le portail, cliquez sur **Parcourir** > **Choisissez les réseaux virtuels**

    ![Créer VNet homologation dans Azure portal](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)

3. De lames de réseaux virtuels, choisissez VNET1, cliquez sur Peerings, puis cliquez sur Ajouter

    ![Choisissez l’homologation](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)

4. Dans la lame ajouter d’homologation, donnez un nom de lien homologation LinkToVnet2, choisissez l’abonnement et l’homologue VNET2 de réseau virtuel, cliquez sur OK.

    ![Lien vers VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)

5. Une fois ce lien d’homologation VNET est créé. Vous pouvez voir l’état de liaison comme suit :

    ![État de la liaison](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)

6. Ensuite, créez le lien d’homologation VNET de VNET2 à VNET1. De lames de réseaux virtuels, choisissez VNET2, cliquez sur Peerings, puis cliquez sur Ajouter

    ![Homologue à partir d’autre VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)

7. Dans la lame ajouter d’homologation, donnez un nom de lien homologation LinkToVnet1, cliquez sur l’abonnement et l’homologue réseau virtuel, cliquez sur le OK.

    ![Création de mosaïque du réseau virtuel](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)

8. Une fois ce lien d’homologation VNET est créé. Vous pouvez voir l’état de liaison comme suit :

    ![État de la liaison finale](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)

9. Vérifier l’état de LinkToVnet2 et il devient alors connecté ainsi.  

    ![État de la liaison finale 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [AZURE.NOTE] Homologation de VNET est établie uniquement si les deux liaisons sont connectés.

Il existe quelques propriétés configurables pour chaque lien :

|Option|Description|Par défaut|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Si l’espace de VNet égal à être inclus dans le cadre de la balise Virtual_network d’adressage|Oui|
|AllowForwardedTraffic|Si le trafic ne proviennent ne pas d’une VNet de ressources est accepté ou rejeté|N°|
|AllowGatewayTransit|Permet à l’hôte à utiliser la passerelle VNet VNet|N°|
|UseRemoteGateways|Utiliser passerelle de VNet de votre collègue. L’homologue VNet doit avoir une configuration de passerelle et AllowGatewayTransit est sélectionnée. Vous ne pouvez pas utiliser cette option si vous disposez d’une configuration de passerelle|N°|

Chaque lien de l’homologation de VNet a un jeu de propriétés au-dessus de. À partir du portail, vous pouvez cliquer sur le lien d’homologation VNet et changer les options disponibles, cliquez sur Enregistrer pour que l’effet de la modification.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. À partir d’un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous à votre compte Azure.
2. Dans cet exemple nous utiliserons deux abonnements A et B et les deux utilisateurs, utilisateur a et utilisateur b avec des privilèges dans les abonnements respectivement
3. Sur le portail, cliquez sur Parcourir, choisissez les réseaux virtuels. Cliquez sur le VNET et cliquez sur Ajouter.

    ![Parcourir du scénario 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)

4. Sur la blade d’accès ajouter, cliquez sur Sélectionnez un rôle et choisir les collaborateurs du réseau et cliquez sur Ajouter des utilisateurs, tapez le signe de l’UtilisateurB dans la zone Nom, cliquez sur OK.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

    Ce n’est pas une exigence, homologation peut être établie même si les utilisateurs déclenchent individuellement des demandes d’homologation pour leurs Vnets respectifs tant que les demandes correspondant à. Ajout d’un utilisateur privilégié de l’autre VNet en tant qu’utilisateurs dans le VNet local facilite l’effectuer l’installation dans le portail.

5. Puis connectez-vous Azure portal à UtilisateurB, qui est l’utilisateur des privilèges de SubscriptionB. Suivez les étapes pour ajouter l’UtilisateurA en tant que collaborateur de réseau ci-dessus.

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [AZURE.NOTE] Vous pouvez fermer la session et ouvrez une session sur les deux sessions utilisateur dans un navigateur pour vous assurer de que l’autorisation est activée avec succès.

6. Connexion au portail comme UtilisateurA, accédez à la lame de VNET3, cliquez sur Peering, vérifier ' je sais que mon ID de ressource « ID de case à cocher et le type de la ressource pour VNET5 dans au-dessous de format.

    / subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}

    ![ID de ressource](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)

7. Connexion au portail UtilisateurB et suivez ci-dessus étape pour créer le lien d’homologation de VNET5 à VNet3.

    ![ID de ressource 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)

8. Homologation est établie et un ordinateur virtuel de VNet3 doit être en mesure de communiquer avec un ordinateur virtuel de VNet5

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Dans un premier temps, VNET des liens d’homologation de HubVnet à VNET1. Notez qu’option d’autoriser le trafic transmis n’est pas activée pour le lien.

    ![Homologation de base](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)

2. Comme une étape suivante, les liens d’homologation de VNET1 à HubVnet peuvent être créés. Notez que l’option de trafic autoriser transmis est sélectionnée.

    ![Homologation de base](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)

3. Après que homologation est établie, vous pouvez reportez-vous à cet [article](virtual-network-create-udr-arm-ps.md) et définir le Route(UDR) de défini par l’utilisateur afin de rediriger le trafic de VNet1 grâce à une appliance virtuelle pour utiliser ses fonctionnalités. Lorsque vous spécifiez l’adresse de tronçon suivant dans l’itinéraire, vous pouvez la définir à l’adresse IP de l’appliance virtuelle dans homologue VNet HubVNet


[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]



1. À partir d’un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous à votre compte Azure.

2. Pour établir des VNET homologation dans ce scénario, vous devez ne créer qu’un seul lien, à partir du réseau virtuel dans le Gestionnaire de ressources Azure à celui d’un classique. Autrement dit, à partir de **VNET1** à **VNET2**. Dans le portail, cliquez sur **Parcourir** > choisissez des **Réseaux virtuels**

3. Dans la lame de réseaux virtuels, choisissez **VNET1**. Cliquez sur **Peerings**, puis cliquez sur **Ajouter**.

4. De la lame ajouter d’homologation, nom de votre lien. Ici, il est appelé **LinkToVNet2**. Sous Détails du poste, sélectionnez **classique**.

5. Choisissez ensuite l’abonnement et l’homologue réseau virtuel **VNET2**. Puis cliquez sur OK.

    ![Liaison de Vnet1 à Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)

6. Une fois ce lien d’homologation VNet est créé, les deux réseaux virtuels sont ressources et vous ne pourrez pas voir les éléments suivants :

    ![Vérification de la connexion d’homologation](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)


## <a name="remove-vnet-peering"></a>Supprimer l’homologation de VNet

1.  À partir d’un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous à votre compte Azure.
2.  Accédez à lame de réseau virtuel, cliquez sur Peerings, cliquez sur le lien que vous souhaitez supprimer, cliquez sur le bouton Supprimer.

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)

3. Après la suppression d’un lien dans l’homologation de VNET, l’état des liaisons homologue passera à déconnecté.

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)

4. Dans cet état, vous ne pouvez pas recréer le lien jusqu'à ce que l’état du lien homologue devient initié. Nous vous recommandons de que vous supprimez à la fois les liens avant de recréer l’homologation VNET.
