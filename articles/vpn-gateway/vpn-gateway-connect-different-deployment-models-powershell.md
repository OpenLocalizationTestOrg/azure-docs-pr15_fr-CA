<properties 
   pageTitle="Comment connecter des réseaux virtuels classiques aux réseaux virtuels de gestionnaire de ressources à l’aide de PowerShell | Microsoft Azure"
   description="Apprenez à créer une connexion VPN entre classique des VNets et des VNets Gestionnaire de ressources à l’aide de la passerelle VPN et PowerShell"
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
   ms.date="09/23/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Connecter des réseaux virtuels à partir de modèles de déploiement différentes à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Portail](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure dispose actuellement de deux modèles de gestion : classique et Gestionnaire de ressources (RM). Si vous avez utilisé Azure pendant un certain temps, vous avez probablement Azure VM et les rôles d’instance en cours d’exécution dans un VNet classique. Vos machines virtuelles plus récentes et les instances de rôle peuvent être exécuté dans un VNet créé dans le Gestionnaire de ressources. Cet article vous guide dans le processus de connexion classique VNets à VNets le Gestionnaire de ressources pour permettre les ressources situées dans les modèles de déploiement séparé pour communiquer entre eux sur une connexion de passerelle.

Vous pouvez créer une connexion entre les VNets qui se trouvent dans les différentes souscriptions et dans différentes régions. Vous pouvez également connecter VNets disposant déjà de connexions à des réseaux locaux, tant qu’ils ont été configurés avec la passerelle est dynamique ou basée sur un itinéraire. Pour plus d’informations sur les connexions de VNet-à-VNet, consultez le [Forum aux questions sur VNet-à-VNet](#faq) à la fin de cet article.

### <a name="deployment-models-and-methods-for-connecting-vnets-in-different-deployment-models"></a>Méthodes de connexion VNets dans les modèles de déploiement différentes et des modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Nous mettons à jour le tableau suivant en tant qu’articles nouveaux et des outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de la table.<br><br>

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>Homologation de VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="before-beginning"></a>Avant de commencer

Les étapes suivantes vous guident tout au long des paramètres nécessaires pour configurer une passerelle dynamique ou basée sur un itinéraire pour chaque VNet et créer une connexion VPN entre les passerelles. Cette configuration ne gère pas les passerelles statiques ou basée sur des règles.

### <a name="prerequisites"></a>Conditions préalables

 - Les deux VNets ont déjà été créées.
 - Les plages d’adresses pour le VNets ne se chevauchent mutuellement, ou se chevauchent avec une des plages pour les autres connexions qui les passerelles peuvent être connectés à.
 - Vous avez installé les dernières applets de commande PowerShell (1.0.2 ou version ultérieure). Pour plus d’informations, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) . Assurez-vous que vous installez à la fois les services Management (SM) et les applets de commande du Gestionnaire de ressources (RM). 

### <a name="exampleref"></a>Exemples de paramètres

Vous pouvez utiliser les paramètres de l’exemple comme référence.

**Paramètres de VNet classiques**

Nom de VNet = ClassicVNet <br>
Emplacement = ouest des États-Unis <br>
Espaces d’adressage réseau virtuel = 10.0.0.0/24 <br>
Sous-réseau-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nom du réseau local = RMVNetLocal <br>
GatewayType = DynamicRouting

**Paramètres du Gestionnaire de ressources VNet**

Nom de VNet = RMVNet <br>
Groupe de ressource = RG1 <br>
Espaces d’adressage IP réseau virtuel = 192.168.0.0/16 <br>
Sous-réseau-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Emplacement = US orientale <br>
Nom IP public de passerelle = gwpip <br>
Passerelle réseau local = ClassicVNetLocal <br>
Nom de passerelle réseau virtuel = RMGateway <br>
Configuration de l’adressage IP Gateway = gwipconfig


## <a name="createsmgw"></a>Section 1 - configurer le VNet classique

### <a name="part-1---download-your-network-configuration-file"></a>Partie 1 - Télécharger le fichier de configuration de votre réseau

1. Connectez-vous à votre compte Azure dans la console PowerShell avec des droits élevés. L’applet de commande suivant vous demande les informations d’identification de connexion pour votre compte Azure. Après la connexion, il télécharge les paramètres de votre compte afin qu’ils soient disponibles pour Azure PowerShell. Vous utiliserez les applets de commande PowerShell de SM dans cette partie de la configuration.

        Add-AzureAccount

2. Exportation de votre fichier de configuration de réseau Azure en exécutant la commande suivante. Vous pouvez modifier l’emplacement du fichier à exporter vers un emplacement différent, si nécessaire. Vous modifiez le fichier et puis l’importer dans Azure.

        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

3. Ouvrez le fichier .xml que vous avez téléchargé pour le modifier. Pour obtenir un exemple du fichier de configuration réseau, consultez le [Schéma de Configuration du réseau](https://msdn.microsoft.com/library/jj157100.aspx).
 

### <a name="part-2--verify-the-gateway-subnet"></a>Partie 2 - Vérifiez le sous-réseau de passerelle

Dans l’élément **VirtualNetworkSites** , ajouter un sous-réseau passerelle à votre VNet si aucune n’a pas déjà été créée. Lorsque vous travaillez avec le fichier de configuration de réseau, le sous-réseau de passerelle doit être nommé « GatewaySubnet » ou Azure ne peut pas reconnaître et l’utiliser comme un sous-réseau de la passerelle.

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exemple :**
    
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### <a name="part-3---add-the-local-network-site"></a>Partie 3 - ajouter le site de réseau local

Le site de réseau local que vous ajoutez représente le VNet RM auquel vous souhaitez vous connecter. Il se peut que vous deviez ajouter un élément **LocalNetworkSites** dans le fichier s’il n’existe pas déjà. À ce stade dans la configuration, le VPNGatewayAddress peut être une adresse IP publique valide, car nous n’avons pas encore créé la passerelle pour le Gestionnaire de ressources VNet. Une fois que nous avons créé la passerelle, nous remplacer cette adresse d’espace réservé avec l’adresse IP publique correcte qui a été affectée à la passerelle de RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="part-4---associate-the-vnet-with-the-local-network-site"></a>Partie 4 : associer la VNet avec le site de réseau local

Dans cette section, nous indiquer le site de réseau local que vous souhaitez connecter le VNet à. Dans ce cas, il est VNet le Gestionnaire de ressources que vous avez référencé plus haut. Assurez-vous que les noms correspondent. Cette étape ne crée pas une passerelle. Il indique que la passerelle doit se connecter au réseau local.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="part-5---save-the-file-and-upload"></a>Partie 5 : enregistrer le fichier et le téléchargement

Enregistrez le fichier, puis l’importer dans Azure en exécutant la commande suivante. Assurez-vous de que modifier le chemin d’accès de fichier en fonction de votre environnement.

        Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

Vous devriez voir quelque chose de similaire à ce résultat en indiquant que l’importation a réussi.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="part-6---create-the-gateway"></a>Partie 6 - créer la passerelle 

Vous pouvez créer la passerelle VNet de via le portail classique, soit à l’aide de PowerShell.

L’exemple suivant permet de créer une passerelle routage dynamique :

    New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting

Vous pouvez vérifier l’état de la passerelle en utilisant la `Get-AzureVNetGateway` applet de commande.


## <a name="creatermgw"></a>Section 2 : Configurer la passerelle RM VNet

Pour créer une passerelle VPN pour le VNet de RM, suivez les instructions ci-dessous. Ne démarrent pas les étapes qu’après avoir récupéré l’adresse IP publique pour passerelle de la VNet classique. 

1. **Se connecter à votre compte Azure** dans la console PowerShell. L’applet de commande suivant vous demande les informations d’identification de connexion pour votre compte Azure. Après la connexion, les paramètres de votre compte sont téléchargés afin qu’ils soient disponibles pour Azure PowerShell.

        Login-AzureRmAccount 

    Obtenir la liste de vos abonnements Azure si vous avez plus d’un abonnement.

        Get-AzureRmSubscription

    Spécifier l’abonnement que vous souhaitez utiliser. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Créer une passerelle du réseau local**. Dans un réseau virtuel, la passerelle du réseau local fait généralement référence à l’emplacement local. Dans ce cas, la passerelle du réseau local fait référence à votre VNet classique. Lui donner un nom par lequel Azure peut faire et également spécifier le préfixe de l’espace adresse. Azure utilise le préfixe d’adresse IP spécifié pour identifier le trafic à envoyer à votre magasin local. Si vous devez ajuster les ici les informations ultérieurement, avant de créer votre passerelle, vous pouvez modifier les valeurs et exécutez de nouveau l’exemple.<br><br>
    - `-Name`est le nom que vous souhaitez attribuer pour faire référence à la passerelle du réseau local.<br>
    - `-AddressPrefix`est l’espace d’adressage pour votre VNet classique.<br>
    - `-GatewayIpAddress`est l’adresse IP publique de passerelle de la VNet classique. Veillez à modifier l’exemple suivant pour refléter l’adresse IP correcte.
    
            New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
            -Location "West US" -AddressPrefix "10.0.0.0/24" `
            -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Demander une adresse IP publique** à allouer à la passerelle de réseau virtuel pour le Gestionnaire de ressources VNet. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. L’adresse IP est affectée dynamiquement à la passerelle de réseau virtuel. Toutefois, cela ne signifie pas que l’adresse IP change. Le seul moment où les modifications d’adresses IP passerelle réseau virtuel est lorsque la passerelle est supprimée et recréée. Il ne modifie pas sur le redimensionnement, la réinitialisation ou autres maintenance/mises à niveau internes de la passerelle.<br>Dans cette étape, nous définissons également une variable qui est utilisée dans une étape ultérieure.


        $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
        -ResourceGroupName RG1 -Location 'EastUS' `
        -AllocationMethod Dynamic

4. **Vérifiez que votre réseau virtuel dispose d’un sous-réseau de la passerelle**. Si aucun sous-réseau passerelle n’existe, ajoutez-en une. Assurez-vous que le sous-réseau de passerelle est nommé *GatewaySubnet*.

5. **Récupérer le sous-réseau** utilisé pour la passerelle en exécutant la commande suivante. Dans cette étape, nous avons également définir de variable à utiliser dans l’étape suivante.
    - `-Name`est le nom de votre gestionnaire de ressources de VNet.
    - `-ResourceGroupName`est le groupe de ressources qui est associé à le VNet. Le sous-réseau de la passerelle doit déjà exister pour ce VNet et doit être nommé *GatewaySubnet* pour fonctionner correctement.


            $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
            -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **Créer la configuration d’adressage IP gateway**. La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. L’exemple suivant permet de créer la configuration de votre passerelle.<br>Dans cette étape, la `-SubnetId` et `-PublicIpAddressId` paramètres doivent être transmis la propriété id de sous-réseau, les objets d’adresse IP, respectivement. Vous ne pouvez pas utiliser une simple chaîne. Ces variables sont définies dans l’étape de demander une adresse IP publique et l’étape pour récupérer le sous-réseau.

        $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
        -Name gwipconfig -SubnetId $subnet.id `
        -PublicIpAddressId $ipaddress.id
    
7. **Créer la passerelle de réseau virtuel du Gestionnaire de ressources** en exécutant la commande suivante. Le `-VpnType` doit être *RouteBased*. Il peut prendre au moins 45 minutes pour cette opération.

        New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
        -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
        -IpConfigurations $gwipconfig `
        -EnableBgp $false -VpnType RouteBased

8. **Copier l’adresse IP publique** qu’une seule fois la passerelle VPN a été créée. Utilisez-la lorsque vous configurez les paramètres de réseau local de votre VNet classique. Vous pouvez utiliser l’applet de commande suivante pour récupérer l’adresse IP publique. En tant *qu’adresse IP*, l’adresse IP publique est répertorié dans le retour.

        Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Section 3 : Modifier le réseau local pour la VNet classique

Vous pouvez effectuer cette étape en exportant le fichier de configuration de réseau, modification, l’enregistrement et l’importation du fichier sauvegarder sur Azure. Ou bien, vous pouvez modifier ce paramètre dans le portail classique. 

### <a name="to-modify-in-the-portal"></a>Pour modifier le portail

1. Ouvrez le [portail classique](https://manage.windowsazure.com).

2. Dans le portail classique, faites défiler la liste vers le bas sur le côté gauche, puis cliquez sur **réseaux**. Dans la page **réseaux** , cliquez sur les **Réseaux locaux** en haut de la page. 

3. Dans la page **Réseaux locaux** , cliquez sur pour sélectionner le réseau local que vous configuré dans la partie 1, puis accédez au bas de la page et cliquez sur **Modifier**.

4. Dans la page **spécifier les détails de votre réseau local** , remplacez l’adresse de l’espace réservé avec l’adresse IP publique de la passerelle du Gestionnaire de ressources que vous avez créé dans la section précédente. Cliquez sur la flèche pour passer à la section suivante. Vérifiez que l' **Espace d’adressage** est correct, puis cliquez sur la case à cocher pour accepter les modifications.

### <a name="to-modify-using-the-network-configuration-file"></a>Pour modifier le fichier de configuration du réseau à l’aide de

1. Exporter le fichier de configuration réseau.
2. Dans un éditeur de texte, modifiez l’adresse IP de la passerelle VPN.

        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Enregistrez vos modifications et réimporter le fichier modifié dans Azure.


## <a name="connect"></a>Section 4 : Créer une connexion entre les passerelles

Création d’une connexion entre les passerelles nécessite PowerShell. Vous devrez peut-être d’ajouter votre compte Azure pour utiliser les applets de commande PowerShell classique. Pour ce faire, vous pouvez utiliser l’applet de commande suivante : 
        
    Add-AzureAccount

1. **Définir votre clé partagée** en exécutant la commande suivante. Dans cet exemple, `-VNetName` est le nom de le VNet classique et `-LocalNetworkSiteName` est le nom que vous avez spécifié pour le réseau local lorsque vous l’avez configurée dans le portail classique. Le `-SharedKey` est une valeur que vous pouvez générer et spécifier. La valeur que vous spécifiez ici doit être la même valeur que vous spécifiez à l’étape suivante lors de la création de votre connexion. Le retour de cet exemple doit afficher **état : réussi**.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. Créer la connexion VPN en exécutant les commandes suivantes.

    **Définir les variables**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Créer la connexion**<br> Notez que la `-ConnectionType` IPSec, pas Vnet2Vnet.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. Vous pouvez afficher votre connexion dans un portail, ou à l’aide de la `Get-AzureRmVirtualNetworkGatewayConnection` applet de commande.

        Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>VNet à VNet FAQ

Afficher les détails du Forum aux questions pour plus d’informations sur les connexions de VNet-à-VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


