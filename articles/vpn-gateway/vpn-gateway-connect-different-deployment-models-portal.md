<properties 
   pageTitle="Comment connecter des réseaux virtuels classiques pour le Gestionnaire de ressources de réseaux virtuels dans le portail | Microsoft Azure"
   description="Apprenez à créer une connexion VPN entre classique des VNets et des VNets Gestionnaire de ressources à l’aide de la passerelle VPN et du portail"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>Connecter des réseaux virtuels à partir de modèles de déploiement différentes dans le portail

> [AZURE.SELECTOR]
- [Portail](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Azure dispose actuellement de deux modèles de gestion : classique et Gestionnaire de ressources (RM). Si vous avez utilisé Azure pendant un certain temps, vous avez probablement Azure VM et les rôles d’instance en cours d’exécution dans un VNet classique. Vos machines virtuelles plus récentes et les instances de rôle peuvent être exécuté dans un VNet créé dans le Gestionnaire de ressources. Cet article vous guide dans le processus de connexion classique VNets à VNets le Gestionnaire de ressources pour permettre les ressources situées dans les modèles de déploiement séparé pour communiquer entre eux sur une connexion de passerelle. 

Vous pouvez créer une connexion entre les VNets qui se trouvent dans les différentes souscriptions et dans différentes régions. Vous pouvez également connecter VNets disposant déjà de connexions à des réseaux locaux, tant qu’ils ont été configurés avec la passerelle est dynamique ou basée sur un itinéraire. Pour plus d’informations sur les connexions de VNet-à-VNet, consultez le [Forum aux questions sur VNet-à-VNet](#faq) à la fin de cet article.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modèles de déploiement et les méthodes pour les connexions de VNet-à-VNet

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Nous mettons à jour le tableau suivant en tant qu’articles nouveaux et des outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de la table.<br><br>

**VNet-à-VNet**

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>Homologation de VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>Avant de commencer

Les étapes suivantes vous guident tout au long des paramètres nécessaires pour configurer une passerelle dynamique ou basée sur un itinéraire pour chaque VNet et créer une connexion VPN entre les passerelles. Cette configuration ne gère pas les passerelles statiques ou basée sur des règles. 

Dans cet article, nous utilisons le portail classique, le portail Azure et PowerShell. Actuellement, il n’est pas possible de créer cette configuration en utilisant uniquement le portail Azure.

### <a name="prerequisites"></a>Conditions préalables

 - Les deux VNets ont déjà été créées.
 - Les plages d’adresses pour le VNets ne se chevauchent mutuellement, ou se chevauchent avec une des plages pour les autres connexions qui les passerelles peuvent être connectés à.
 - Vous avez installé les dernières applets de commande PowerShell (1.0.2 ou version ultérieure). Pour plus d’informations, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) . Assurez-vous que vous installez à la fois les services Management (SM) et les applets de commande du Gestionnaire de ressources (RM). 

### <a name="values"></a>Exemples de paramètres

Vous pouvez utiliser les paramètres de l’exemple comme référence.

**Paramètres de VNet classiques**

Nom de VNet = ClassicVNet <br>
Emplacement = ouest des États-Unis <br>
Espaces d’adressage réseau virtuel = 10.0.0.0/24 <br>
Sous-réseau-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nom du réseau local = RMVNetLocal <br>

**Paramètres du Gestionnaire de ressources VNet**

Nom de VNet = RMVNet <br>
Groupe de ressource = RG1 <br>
Espaces d’adressage IP réseau virtuel = 192.168.0.0/16 <br>
Sous-réseau-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Emplacement = US orientale <br>
Nom de la passerelle réseau virtuel = RMGateway <br>
Nom IP public de passerelle = gwpip <br>
Type de passerelle VPN de = <br>
Type de VPN = basée sur un itinéraire <br>
Passerelle réseau local = ClassicVNetLocal <br>

## <a name="createsmgw"></a>Section 1 : Configurer les paramètres VNet classiques

Dans cette section, nous créons le réseau local et la passerelle pour votre VNet classique. Les instructions de cette section utilisent le portail classique. Actuellement, le portail Azure n’offre pas tous les paramètres qui se rapportent à un VNet classique.

### <a name="part-1---create-a-new-local-network"></a>Partie 1 - Création d’un nouveau réseau local

Ouvrez le [portail classique](https://manage.windowsazure.com) et connectez-vous avec votre compte Azure.

1. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau** > **Services réseau** > **Réseau virtuel** > **réseau local d’ajouter**.

2. Dans la fenêtre **spécifier les détails de votre réseau local** , tapez un nom pour le VNet de RM vous souhaitez vous connecter. Dans la zone **adresse IP du périphérique VPN (facultatif)** , tapez une adresse IP publique valide. Ceci est uniquement un espace réservé temporaire. Vous modifiez cette adresse IP ultérieurement. Dans le coin inférieur droit de la fenêtre, cliquez sur le bouton de la flèche.
 
3. Sur la page **spécifier l’espace d’adressage** , dans la zone de texte **IP de début** , tapez le préfixe de réseau et d’un bloc CIDR pour le Gestionnaire de ressources VNet vous souhaitez vous connecter. Ce paramètre est utilisé pour spécifier l’espace d’adressage pour router vers les VNet de RM.

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>Partie 2 - associer le réseau local à votre VNet

1. Cliquez sur **Réseaux virtuels** en haut de la page pour basculer vers l’écran de réseaux virtuels, puis cliquez sur pour sélectionner votre VNet classique. Dans la page de votre VNet, cliquez sur **configurer** pour accéder à la page de configuration.

2. Sous la section connexion de **connectivité de site à site** , activez la case à cocher **se connecter au réseau local** . Sélectionnez le réseau local que vous avez créé. Si vous avez plusieurs réseaux locaux que vous avez créé, veillez à sélectionner celle que vous avez créée pour représenter votre VNet Gestionnaire de ressources dans la liste déroulante.

3. Cliquez sur **Enregistrer** en bas de la page.

### <a name="part-3---create-the-gateway"></a>Partie 3 - créer la passerelle

1. Après avoir enregistré les paramètres, cliquez sur le **tableau de bord** en haut de la page pour modifier la page de tableau de bord. En bas de la page de tableau de bord, cliquez sur **Créer une passerelle**, puis cliquez sur **Routage dynamique**. Cliquez sur **Oui** pour commencer la création de votre passerelle. Une passerelle de routage dynamique est nécessaire pour cette configuration.

2. Attente de la passerelle doit être créé. Cela peut parfois prendre 45 minutes ou plus.

### <a name="ip"></a>Partie 4 : affichage de l’adresse IP publique de passerelle

Après avoir créé la passerelle, vous pouvez afficher l’adresse IP de passerelle dans la page de **tableau de bord** . Il s’agit de l’adresse IP publique de votre passerelle. Notez ou copiez l’adresse IP publique. Vous l’utiliser dans les étapes suivantes lorsque vous créez le réseau local pour la configuration de votre gestionnaire de ressources VNet.


## <a name="creatermgw"></a>Section 2 : Configurer les paramètres du Gestionnaire de ressources VNet

Dans cette section, nous créons la passerelle réseau virtuel et le réseau local pour votre VNet Gestionnaire de ressources. Ne démarrent pas les étapes suivantes jusqu'à ce qu’une fois que vous avez extrait l’adresse IP publique pour passerelle de la VNet classique.

Les captures d’écran sont fournis comme exemples. Veillez à remplacer les valeurs de votre propre. Si vous créez cette configuration en guise d’exercice, reportez-vous à ces [valeurs](#values).


### <a name="part-1---create-a-gateway-subnet"></a>Partie 1 - Création d’un sous-réseau de passerelle

Avant de vous connecter à votre réseau virtuel à une passerelle, vous devez d’abord créer le sous-réseau de passerelle pour le réseau virtuel auquel vous voulez vous connecter. Créer un sous-réseau de passerelle avec nombre CIDR de /28 ou plus (/ 27/26, etc..)

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

À partir d’un navigateur, accédez au [portail Azure](http://portal.azure.com) et connectez-vous à votre compte Azure.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Partie 2 - permet de créer une passerelle réseau virtuel


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### <a name="part-3---create-a-local-network-gateway"></a>Partie 3 - créer une passerelle de réseau local

La passerelle « réseau local » fait généralement référence à l’emplacement local. Il indique à Azure plages d’adresse IP à router vers l’emplacement et l’adresse IP publique du périphérique pour cet emplacement. Toutefois, dans ce cas, il fait référence à la plage d’adresses et l’adresse IP publique, associé à votre VNet classique et la passerelle du réseau virtuel.

Nommez la passerelle du réseau local par lequel Azure peut faire. Vous pouvez créer votre passerelle réseau local lors de la création de votre passerelle de réseau virtuel. Pour cette configuration, vous utilisez l’adresse IP publique qui a été attribuée à votre passerelle VNet classique dans la [section précédente](#ip).

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### <a name="part-4---copy-the-public-ip-address"></a>Partie 4 : copie de l’adresse IP publique

Une fois la passerelle réseau virtuel création est terminée, copiez l’adresse IP publique qui est associé à la passerelle. Utilisez-la lorsque vous configurez les paramètres de réseau local de votre VNet classique. 


## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Section 3 : Modifier le réseau local pour la VNet classique

Ouvrez le [portail classique](https://manage.windowsazure.com).

2. Dans le portail classique, faites défiler la liste vers le bas sur le côté gauche, puis cliquez sur **réseaux**. Dans la page **réseaux** , cliquez sur les **Réseaux locaux** en haut de la page. 

3. Cliquez pour sélectionner le réseau local que vous avez configuré dans la partie 1. En bas de la page, cliquez sur **Modifier**.

4. Dans la page **spécifier les détails de votre réseau local** , remplacez l’adresse de l’espace réservé avec l’adresse IP publique de la passerelle du Gestionnaire de ressources que vous avez créé dans la section précédente. Cliquez sur la flèche pour passer à la section suivante. Vérifiez que l' **Espace d’adressage** est correct, puis cliquez sur la case à cocher pour accepter les modifications.

## <a name="connect"></a>Section 4 : Créer la connexion

Dans cette section, nous créons la connexion entre le VNets. La procédure pour cela nécessite PowerShell. Vous ne pouvez pas créer cette connexion des portails. Assurez-vous que vous avez téléchargé et installé le classique (SM) et les applets de commande PowerShell de Resource Manager (RM).


1. Connectez-vous à votre compte Azure dans la console PowerShell. L’applet de commande suivant vous demande les informations d’identification de connexion pour votre compte Azure. Après la connexion, les paramètres de votre compte sont téléchargés afin qu’ils soient disponibles pour Azure PowerShell.

        Login-AzureRmAccount 

    Obtenir la liste de vos abonnements Azure si vous avez plus d’un abonnement.

        Get-AzureRmSubscription

    Spécifier l’abonnement que vous souhaitez utiliser. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. Ajoutez votre compte Azure pour utiliser les applets de commande PowerShell classique. Pour ce faire, vous pouvez utiliser la commande suivante :

        Add-AzureAccount

3. Définissez votre clé partagée par l’exécution de l’exemple suivant. Dans cet exemple, `-VNetName` est le nom de le VNet classique et `-LocalNetworkSiteName` est le nom que vous avez spécifié pour le réseau local lorsque vous l’avez configurée dans le portail classique. Le `-SharedKey` est une valeur que vous pouvez générer et spécifier. La valeur que vous spécifiez ici doit être la même valeur que vous spécifiez à l’étape suivante lors de la création de votre connexion.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. Créer la connexion VPN en exécutant les commandes suivantes :
    
    **Définir les variables**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Créer la connexion**<br> Notez que les `-ConnectionType` « IPSec », pas « Vnet2Vnet ». Dans cet exemple, `-Name` est le nom que vous souhaitez appeler votre connexion. L’exemple suivant crée une connexion intitulée «*connexion rm-standard*».
        
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>Vérifier votre connexion

Vous pouvez vérifier votre connexion en utilisant le portail classique, le portail Azure ou PowerShell. Vous pouvez utiliser les étapes suivantes pour vérifier votre connexion. Remplacez les valeurs de votre propre.

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)] 

## <a name="faq"></a>VNet à VNet FAQ

Afficher les détails du Forum aux questions pour plus d’informations sur les connexions de VNet-à-VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


