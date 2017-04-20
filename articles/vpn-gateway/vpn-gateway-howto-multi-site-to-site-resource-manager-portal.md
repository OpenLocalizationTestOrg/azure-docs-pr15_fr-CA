<properties
   pageTitle="L’ajout de plusieurs connexions de passerelle de Site à Site VPN à un réseau virtuel pour le modèle de déploiement Gestionnaire de ressources à l’aide du portail Azure | Microsoft Azure"
   description="Ajouter des connexions de S2S sites multiples à une passerelle VPN qui dispose d’une connexion existante"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>



# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Ajouter une connexion de Site à Site à une VNet avec une connexion de passerelle VPN existante

> [AZURE.SELECTOR]
- [Responsable de ressources - portail](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Standard - PowerShell](vpn-gateway-multi-site.md)

Cet article vous guide tout au long de l’aide du portail Azure pour ajouter des connexions de Site à Site (S2S) à une passerelle VPN qui dispose d’une connexion existante. Ce type de connexion est souvent appelé une configuration « plusieurs sites ». 

Vous pouvez utiliser cet article pour ajouter une connexion de S2S à un VNet qui a déjà une S2S connexion, connexion Point-à-Site ou connexion de VNet-à-VNet. Il existe certaines limitations lors de l’ajout de connexions. Vérifiez la section [avant de commencer](#before) , dans cet article pour vérifier avant de commencer votre configuration. 

Cet article s’applique aux VNets créé à l’aide du modèle de déploiement du Gestionnaire de ressources qui ont une passerelle VPN de RouteBased. Ces étapes ne s’appliquent pas aux configurations de connexion coexistence ExpressRoute/Site-à-Site. Pour plus d’informations sur les connexions de coexistence, voir [connexions de coexistence de ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) .

### <a name="deployment-models-and-methods"></a>Méthodes et modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Mise à jour de cette table en tant que de nouveaux articles et des outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 


## <a name="before"></a>Avant de commencer

Vérifiez les éléments suivants :

- Vous ne créez pas d’une connexion de coexistence de ExpressRoute/S2S.
- Vous avez un réseau virtuel qui a été créé à l’aide du modèle de déploiement du Gestionnaire de ressources avec une connexion existante.
- La passerelle de réseau virtuel pour votre VNet est RouteBased. Si vous avez une passerelle VPN de PolicyBased, vous devez supprimer la passerelle réseau virtuel et la création d’une passerelle VPN en tant que RoutBased.
- Aucun des plages d’adresses se chevauchent pour tous le VNets auquel se connecte ce VNet.
- Vous avez un appareil VPN compatible et une personne qui est en mesure de le configurer. [Sur les périphériques VPN](vpn-gateway-about-vpn-devices.md), reportez-vous à la section. Si vous n’êtes pas familiarisé avec la configuration de votre périphérique VPN, ou que vous ne connaissez pas l’adresse IP des plages qui se trouvent dans vos locaux sur configuration du réseau, vous devez coordonner avec une personne qui pourra vous fournir ces informations.
- Vous avez une adresse IP de public tourné vers l’extérieur pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.


## <a name="part1"></a>Partie 1 - configurer une connexion

1. À partir d’un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous à votre compte Azure.
2. Cliquez sur **toutes les ressources** et recherchez votre **passerelle réseau virtuel** à partir de la liste des ressources, puis cliquez dessus.
3. Sur la lame de **passerelle de réseau virtuel** , cliquez sur **connexions**.

    ![Lame de connexions](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Connections blade")<br>

4. Sur la lame de **connexions** , cliquez sur **+ Ajouter**.

    ![Ajouter le bouton de connexion](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Add connection button")<br>

5. Sur la blade **d’Ajouter une connexion** , remplissez les champs suivants :
    - **Nom :** Le nom que vous souhaitez attribuer le site que vous créez la connexion à.
    - **Type de connexion :** Sélectionnez le **Site à site (IPsec)**.

    ![Ajouter des lames de connexion](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection blade")<br>

## <a name="part2"></a>Partie 2 - Ajout d’une passerelle de réseau local

1. Cliquez sur **passerelle du réseau Local** , ***Choisir une passerelle du réseau local***. La lame de la **passerelle du réseau local choisir** s’ouvre.

    ![Choisissez la passerelle du réseau local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choose local network gateway")<br>
2. Cliquez sur **créer** pour ouvrir la lame de la **passerelle du réseau local créer** .

    ![Créer des lames de passerelle réseau local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>

3. Sur la lame de la **passerelle du réseau local créer** , renseignez les champs suivants :
    - **Nom :** Le nom que vous souhaitez donner à la ressource de passerelle du réseau local.
    - **Adresse IP :** L’adresse IP publique du périphérique sur le site que vous souhaitez connecter au VPN.
    - **L’espace d’adressage :** L’espace d’adressage que vous souhaitez être routés vers le nouveau site de réseau local.
4. Sur la lame de **passerelle du réseau local créer** pour enregistrer les modifications, cliquez sur **OK** .

## <a name="part3"></a>Partie 3 - ajouter la clé partagée et créer la connexion

1. Sur la blade **d’Ajouter une connexion** , ajoutez la clé partagée que vous souhaitez utiliser pour créer votre connexion. Vous pouvez obtenir la clé partagée à partir de votre périphérique VPN, ou effectuer une ici et puis configurer votre périphérique VPN pour utiliser la même clé partagée. Il est important que les clés sont exactement les mêmes.

    ![Clé partagée](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Shared key")<br>
2. En bas de la lame, cliquez sur **OK** pour créer la connexion.

## <a name="part4"></a>Partie 4 - vérification de la connexion VPN

Vous pouvez vérifier votre connexion VPN dans le portail, soit à l’aide de PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## <a name="next-steps"></a>Étapes suivantes

- Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à des réseaux virtuels. Voir les ordinateurs virtuels [cursus](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) pour plus d’informations.
