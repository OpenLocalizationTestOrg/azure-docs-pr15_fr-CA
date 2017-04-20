<properties
   pageTitle="Se connecter VNets en utilisant le modèle de déploiement du Gestionnaire de ressources et le portail Azure | Microsoft Azure"
   description="Créer une connexion de passerelle VPN entre VNets en utilisant le Gestionnaire de ressources et le portail Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Configurer une connexion de VNet à VNet l’utilisation du portail Azure

> [AZURE.SELECTOR]
- [Responsable de ressources - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Standard - portail classique](virtual-networks-configure-vnet-to-vnet-connection.md)


Cet article vous explique comment procéder pour créer une connexion entre les VNets dans le modèle de déploiement Gestionnaire de ressources à l’aide de la passerelle VPN et le portail Azure.

Lorsque vous utilisez le portail Azure pour connecter des réseaux virtuels, la VNets doit être dans le même abonnement. Si vos réseaux virtuels sont dans les différentes souscriptions, vous pouvez toujours se connecter les à l’aide de la procédure de [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) .

![diagramme de V2V](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modèles de déploiement et les méthodes pour les connexions de VNet-à-VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant montre les modèles de déploiement actuellement disponibles et les méthodes pour les configurations VNet-à-VNet. Lorsqu’un article avec des étapes de configuration n’est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>Homologation de VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>À propos des connexions de VNet-à-VNet

Connexion d’un réseau virtuel à un autre réseau virtuel (VNet à VNet) est similaire à la connexion d’un VNet à un emplacement de site local. Les deux types de connectivité permet de fournir un tunnel sécurisé à l’aide de IPsec/IKE une passerelle VPN d’Azure. Le VNets que vous vous connectez peut être dans différentes régions ou dans différents abonnements.

Vous pouvez même combiner communication VNet-à-VNet avec des configurations sur plusieurs sites. Cela vous permet de vous établissez une coexistence des topologies de réseau qui combinent dotée de la connectivité réseau entre virtuels, comme illustré dans le diagramme suivant :

![À propos des connexions] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "À propos des connexions")

### <a name="why-connect-virtual-networks"></a>Pourquoi connecter des réseaux virtuels ?

Voulez-vous vous connecter des réseaux virtuels pour les raisons suivantes :

- **Redondance entre région géographique et géo-présence**
    - Vous pouvez configurer votre propre géo-réplication ou de synchronisation avec une connectivité sécurisée sans passer par les points de terminaison faisant face à Internet.
    - Vous pouvez définir avec Azure Traffic Manager et équilibrage de la charge, la charge de travail hautement disponible avec redondance géographique dans plusieurs régions Azure. Un exemple consiste à paramétrer SQL toujours sur des groupes de disponibilité répartissant sur plusieurs régions Azure.

- **Applications n-tier régionale avec isolation ou limite d’administration**
    - Dans la même région, vous pouvez configurer des applications multicouches avec plusieurs réseaux virtuels reliés entre eux en raison d’isolement ou les besoins administratifs.

Pour plus d’informations sur les connexions de VNet-à-VNet, consultez le [Forum aux questions sur VNet-à-VNet](#faq) à la fin de cet article.

### <a name="values"></a>Exemples de paramètres

Lors de l’utilisation de ces étapes en guise d’exercice, vous pouvez utiliser les exemples de valeurs de configuration. À titre d’exemple, nous utilisons plusieurs espaces d’adressage pour chaque VNet. Toutefois, plusieurs espaces d’adressage ne nécessitent pas les configurations VNet-à-VNet.

**Valeurs pour TestVNet1 :**

- VNet nom : TestVNet1
- Espace d’adressage : 10.11.0.0/16
    - Nom du sous-réseau : site Web frontal
    - Plage d’adresses de sous-réseau : 10.11.0.0/24
- Groupe de ressources : TestRG1
- Emplacement : Américain de l'
- Espace d’adressage : 10.12.0.0/16
    - Nom du sous-réseau : back-end
    - Plage d’adresses de sous-réseau : 10.12.0.0/24
- Nom du sous-réseau passerelle : GatewaySubnet (ce sera la recopie incrémentée dans le portail)
    - Plage d’adresses de sous-réseau de passerelle : 10.11.255.0/27
- Serveur DNS : Utilisez l’adresse IP de votre serveur DNS
- Nom de la passerelle réseau virtuel : TestVNet1GW
- Type de passerelle : VPN
- Type de VPN : basée sur un itinéraire
- Référence : Sélectionnez le SKU de passerelle que vous souhaitez utiliser
- Nom de l’adresse IP publique : TestVNet1GWIP
- Valeurs de connexion :
    - Nom : TestVNet1toTestVNet4
    - Clé partagée : vous pouvez créer la clé vous-même. Pour cet exemple, nous allons utiliser abc123. L’important est que, lorsque vous créez la connexion entre la VNets, la valeur doit correspondre.

**Valeurs pour TestVNet4 :**

- VNet nom : TestVNet4
- Espace d’adressage : 10.41.0.0/16
    - Nom du sous-réseau : site Web frontal
    - Plage d’adresses de sous-réseau : 10.41.0.0/24
- Groupe de ressources : TestRG1
- Emplacement : États-Unis Ouest
- Espace d’adressage : 10.42.0.0/16
    - Nom du sous-réseau : back-end
    - Plage d’adresses de sous-réseau : 10.42.0.0/24
- Nom de GatewaySubnet : GatewaySubnet (ce sera la recopie incrémentée dans le portail)
    - Plage d’adresses GatewaySubnet : 10.41.255.0/27
- Serveur DNS : Utilisez l’adresse IP de votre serveur DNS
- Nom de la passerelle réseau virtuel : TestVNet4GW
- Type de passerelle : VPN
- Type de VPN : basée sur un itinéraire
- Référence : Sélectionnez le SKU de passerelle que vous souhaitez utiliser
- Nom de l’adresse IP publique : TestVNet4GWIP
- Valeurs de connexion :
    - Nom : TestVNet4toTestVNet1
    - Clé partagée : vous pouvez créer la clé vous-même. Pour cet exemple, nous allons utiliser abc123. L’important est que, lorsque vous créez la connexion entre la VNets, la valeur doit correspondre.


## <a name="CreatVNet"></a>1. créer et configurer des TestVNet1

Si vous avez déjà un VNet, vérifiez que les paramètres sont compatibles avec votre conception de passerelle VPN. Une attention particulière pour les sous-réseaux peuvent se superposer avec d’autres réseaux. Si vous disposez de chevauchement des sous-réseaux, votre connexion ne fonctionne pas correctement. Si votre VNet est configuré avec les paramètres corrects, vous pouvez commencer la procédure décrite dans la section [spécifier un serveur DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Pour créer un réseau virtuel

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. ajouter de l’espace d’adresse supplémentaires et de créer des sous-réseaux

Vous pouvez ajouter de l’espace d’adresse supplémentaires et créer des sous-réseaux lorsque votre VNet a été créé.
[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. création d’un sous-réseau de passerelle

Avant de vous connecter à votre réseau virtuel à une passerelle, vous devez d’abord créer le sous-réseau de passerelle pour le réseau virtuel auquel vous voulez vous connecter. Dans la mesure du possible, il est préférable de créer un sous-réseau de passerelle à l’aide d’un bloc CIDR de /28 ou de /27 afin de fournir suffisamment d’adresses IP pour prendre en charge les besoins supplémentaires de configuration futures.

Si vous créez cette configuration en guise d’exercice, consultez ces [exemples de paramètres](#values) lors de la création de votre sous-réseau de passerelle.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau de passerelle

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. Spécifiez un serveur DNS (facultatif)

Si vous souhaitez que la résolution de noms pour les ordinateurs virtuels qui sont déployés dans votre VNets, vous devez spécifier un serveur DNS.

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]


## <a name="VNetGateway"></a>5. créer une passerelle réseau virtuel

Dans cette étape, vous créez la passerelle réseau virtuels pour votre VNet. Cette étape peut prendre jusqu'à 45 minutes. Si vous créez cette configuration en guise d’exercice, vous pouvez faire référence aux [paramètres de l’exemple](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle réseau virtuel

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


## <a name="CreateTestVNet4"></a>6. créer et configurer des TestVNet4

Une fois que vous avez configuré le TestVNet1, créez TestVNet4 en répétant les étapes précédentes, en remplaçant les valeurs avec celles de TestVNet4. Vous n’avez pas besoin d’attendre que la passerelle de réseau virtuel pour TestVNet1 a terminé la création avant de configurer les TestVNet4. Si vous utilisez vos propres valeurs, assurez-vous que les espaces d’adressage ne se chevauchent pas avec de la VNets vous souhaitez vous connecter.


## <a name="TestVNet1Connection"></a>7. configurer la connexion TestVNet1

Lorsque les passerelles de réseau virtuel pour à la fois TestVNet1 et TestVNet4 sont terminées, vous pouvez créer votre réseau virtuel connexions de passerelle. Dans cette section, vous allez créer une connexion à partir de VNet1 à VNet4.

1. Dans **toutes les ressources**, accédez à la passerelle de réseau virtuel pour votre VNet. Par exemple, **TestVNet1GW**. Cliquez sur **TestVNet1GW** pour ouvrir la lame de passerelle réseau virtuel.

    ![Lame de connexions] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Lame de connexions")

2. Cliquez sur **+ Ajouter** pour ouvrir la blade **d’Ajouter une connexion** .

3. Sur la blade **d’Ajouter une connexion** , dans le champ nom, tapez un nom pour votre connexion. Par exemple, **TestVNet1toTestVNet4**.

    ![Nom de la connexion] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nom de la connexion")

4. Pour le **type de connexion**. dans la liste déroulante, sélectionnez **VNet-à-VNet** .

5. La valeur du champ **première passerelle réseau virtuel** est renseignée automatiquement, car vous créez cette connexion à partir de la passerelle réseau virtuel spécifié.

6. Le champ de la **deuxième passerelle réseau virtuel** est le VNet que vous voulez créer une connexion à la passerelle réseau virtuel. Cliquez sur **Choisir une autre passerelle de réseau virtuel** pour ouvrir la lame **Choisissez passerelle réseau virtuel** .

    ![Ajouter une connexion] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Ajouter une connexion")

7. Permet d’afficher les passerelles de réseau virtuel qui figurent sur cette carte. Notez que seules les passerelles de réseau virtuel qui se trouvent dans votre abonnement sont répertoriés. Si vous souhaitez vous connecter à une passerelle de réseau virtuel qui n’est pas dans votre abonnement, veuillez utiliser l' [article de PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 

8. Cliquez sur la passerelle de réseau virtuel que vous souhaitez vous connecter.
 
9. Dans le champ de **clé partagée** , tapez une clé partagée pour votre connexion. Vous pouvez générer ou la créer vous-même. Dans une connexion de site à site, la clé que vous utilisez est exactement le même pour le périphérique local et votre connexion de passerelle réseau virtuel. Le concept est similaire, sauf que plutôt que de se connecter à un périphérique VPN, vous vous connectez à une autre passerelle de réseau virtuel.

    ![Clé partagée] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Clé partagée")

10. Cliquez sur **OK** au bas de la lame pour enregistrer vos modifications.

## <a name="TestVNet4Connection"></a>8. configurer la connexion TestVNet4

Ensuite, créez une connexion à partir de TestVNet4 à TestVNet1. Utilisez la même méthode que celle que vous avez utilisé pour créer la connexion à partir de TestVNet1 à TestVNet4. Assurez-vous que vous utilisez la même clé partagée.

## <a name="VerifyConnection"></a>9. vérifier votre connexion

Vérifiez la connexion. Pour chaque passerelle réseau virtuel, effectuez les opérations suivantes :

1. Recherchez la lame de la passerelle réseau virtuel. Par exemple, **TestVNet4GW**. 
2. Sur la lame de passerelle réseau virtuel, cliquez sur **connexions** pour afficher la lame de connexions de la passerelle réseau virtuel.

Afficher les connexions et vérifier l’état. Une fois la connexion créée, vous verrez **a réussi** et **connecté** en tant que les valeurs d’état.

![A réussi] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "A réussi")

Vous pouvez double-cliquer sur chaque connexion séparément pour afficher plus d’informations sur la connexion.

![Essentials] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="faq"></a>VNet à VNet FAQ

Afficher les détails du Forum aux questions pour plus d’informations sur les connexions de VNet-à-VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à des réseaux virtuels. Étapes, consultez [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
