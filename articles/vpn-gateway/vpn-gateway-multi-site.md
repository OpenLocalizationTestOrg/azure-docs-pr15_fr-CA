<properties 
   pageTitle="Connectez un réseau virtuel à plusieurs sites à l’aide de la passerelle VPN et PowerShell | Microsoft Azure"
   description="Cet article vous guidera dans la connexion de plusieurs sites sur site local à un réseau virtuel à l’aide d’une passerelle VPN pour le modèle de déploiement classique."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />

# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Ajouter une connexion de Site à Site à une VNet avec une connexion de passerelle VPN existante

> [AZURE.SELECTOR]
- [Responsable de ressources - portail](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Standard - PowerShell](vpn-gateway-multi-site.md)

Cet article vous guide tout au long de l’utilisation de PowerShell pour ajouter des connexions de Site à Site (S2S) à une passerelle VPN qui dispose d’une connexion existante. Ce type de connexion est souvent appelé une configuration « plusieurs sites ». 

Cet article s’applique aux réseaux virtuels créés à l’aide du modèle de déploiement classique (également appelé gestion des services). Ces étapes ne s’appliquent pas aux configurations de connexion coexistence ExpressRoute/Site-à-Site. Pour plus d’informations sur les connexions de coexistence, voir [connexions de coexistence de ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-classic.md) .

### <a name="deployment-models-and-methods"></a>Méthodes et modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Mise à jour de cette table en tant que de nouveaux articles et des outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 



## <a name="about-connecting"></a>À propos de la connexion

Vous pouvez vous connecter à plusieurs sites locaux à un seul réseau virtuel. Ceci est particulièrement intéressant pour créer des solutions de nuage hybride. Création d’une connexion sur plusieurs sites à votre passerelle Azure réseau virtuel est très similaire à celle d’autres connexions de Site à Site. En fait, vous pouvez utiliser une passerelle VPN d’Azure existante, dans la mesure où la passerelle est dynamique (basée sur un itinéraire).

Si vous disposez déjà d’une passerelle statique connectée à votre réseau virtuel, vous pouvez modifier le type de passerelle dynamique sans avoir à reconstruire le réseau virtuel afin de tenir compte de plusieurs sites. Avant de modifier le type de routage, assurez-vous que votre passerelle VPN local prend en charge les configurations VPN basée sur un itinéraire. 

![diagramme de plusieurs sites] (./media/vpn-gateway-multi-site/multisite.png "plusieurs sites")

## <a name="points-to-consider"></a>Points à prendre en compte

**Vous ne pourrez pas utiliser le portail classique Azure pour apporter des modifications à ce réseau virtuel.** Pour cette version, vous devez apporter des modifications au fichier de configuration de réseau au lieu d’utiliser le portail classique d’Azure. Si vous apportez des modifications dans le portail classique d’Azure, ils vous remplace vos paramètres de référence de plusieurs sites pour ce réseau virtuel. 

Vous pouvez très à l’aise avec le fichier de configuration du réseau au moment où que vous avez terminé la procédure de plusieurs sites. Toutefois, si vous possédez plusieurs personnes travaillant sur la configuration de votre réseau, vous devez vous assurer que tout le monde sait à propos de cette limitation. Cela ne signifie pas que vous ne pouvez pas utiliser le portail classique Azure tout. Vous pouvez l’utiliser pour tout le reste, à l’exception des modifications de configuration à ce réseau virtuel particulier.

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer la configuration, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Matériel VPN compatible pour chaque local emplacement. Vérifiez [Sur les périphériques VPN pour les connexions de réseau virtuel](vpn-gateway-about-vpn-devices.md) pour vérifier si le périphérique que vous souhaitez utiliser est quelque chose qui est connue pour être compatible.

- Adresse IP IPv4 publique tourné vers l’extérieur pour chaque périphérique VPN. L’adresse IP ne peut pas se trouver derrière un NAT. Cela est nécessaire.

- Vous devez installer la dernière version des applets de commande PowerShell d’Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .

- Une personne qui est expert dans la configuration de votre matériel VPN. Vous ne pourrez pas utiliser les scripts VPN généré automatiquement à partir du portail classique Azure pour configurer vos périphériques VPN. Cela signifie que vous devrez avoir une bonne maîtrise de la façon de configurer votre périphérique VPN ou travailler avec un utilisateur.

- Les plages d’adresses IP que vous souhaitez utiliser pour votre réseau virtuel (si vous ne l’avez pas déjà créé une). 

- Les plages d’adresses IP pour chacun des sites que vous allez vous connecter au réseau local. Vous devez vous assurer que l’adresse IP varie pour chacun des sites de réseau local que vous voulez vous connecter ne se chevauchent ne pas. Dans le cas contraire, le portail classique Azure ou l’API REST rejette la configuration en cours de téléchargement. 

    Par exemple, si vous avez deux sites de réseau local que les deux contiennent l’IP adresse plage 10.2.3.0/24 et que vous disposez d’un package avec une adresse de destination 10.2.3.3, Azure ne sauriez le site auquel vous souhaitez envoyer le package car les plages d’adresses qui sont chevauchent. Pour éviter les problèmes de routage, Azure ne vous permet pas de télécharger un fichier de configuration qui a des plages qui se chevauchent.



## <a name="1-create-a-site-to-site-vpn"></a>1. création d’un VPN de Site à Site

Si vous avez déjà un réseau VPN de Site à Site avec une passerelle de routage dynamique, très bien ! Vous pouvez procéder à [Exporter les paramètres de configuration de réseau virtuel](#export). Si ce n’est pas le cas, effectuez les opérations suivantes :

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Si vous disposez déjà d’un réseau virtuel de Site à Site, mais il dispose d’une passerelle de routage statique (basée sur une stratégie) :

1. Modifier votre type de passerelle au routage dynamique. Un réseau privé virtuel sur plusieurs sites requiert une passerelle de routage dynamique (également appelé itinéraire basé). Pour modifier votre type de passerelle, vous devez d’abord supprimer la passerelle existante, puis créer un nouveau. Pour obtenir des instructions, reportez-vous à la section [comment modifier le type de routage de la passerelle VPN](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway).  

2. Configurez votre nouvelle passerelle et créer le tunnel VPN. Pour plus d’informations, consultez [configurer une passerelle VPN dans le portail classique d’Azure](vpn-gateway-configure-vpn-gateway-mp.md). Tout d’abord, modifiez votre type de passerelle au routage dynamique. 

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Si vous n’avez pas un réseau virtuel de Site à Site :

1. Créer votre réseau virtuel de Site à Site à l’aide de ces instructions : [créez un réseau virtuel avec une connexion VPN de Site à Site dans le portail classique d’Azure](vpn-gateway-site-to-site-create.md).  

2. Configurer une passerelle de routage dynamique à l’aide de ces instructions : [configurer une passerelle VPN](vpn-gateway-configure-vpn-gateway-mp.md). Veillez à sélectionner le **routage dynamique** pour votre type de passerelle.

## <a name="export"></a>2. l’exportation du fichier de configuration de réseau 

Exporter le fichier de configuration de votre réseau. Le fichier que vous exportez permet de configurer vos nouveaux paramètres sur plusieurs sites. Si vous avez besoin d’instructions sur la manière d’exporter un fichier, reportez-vous à la section de l’article : [comment créer un VNet à l’aide d’un fichier de configuration de réseau dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="3-open-the-network-configuration-file"></a>3. Ouvrez le fichier de configuration de réseau

Ouvrez le fichier de configuration de réseau que vous avez téléchargé dans la dernière étape. Utilisez un éditeur xml qui vous convient. Le fichier doit ressembler à ce qui suit :

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Ajoutez plusieurs références à des sites

Lorsque vous ajoutez ou supprimez des informations de référence de site, vous devez modifier la configuration de la ConnectionsToLocalNetwork/LocalNetworkSiteRef. Ajout d’un nouveau site local référence les déclencheurs Azure pour créer un tunnel de nouveau. Dans l’exemple ci-dessous, la configuration réseau est pour une connexion de site unique. Enregistrez le fichier une fois que vous avez terminé vos modifications.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5. importer le fichier de configuration de réseau

Importez le fichier de configuration réseau. Lorsque vous importez ce fichier avec les modifications, les tunnels de nouveau seront ajoutés. Les tunnels utilise la passerelle dynamique que vous avez créé précédemment. Si vous avez besoin d’instructions sur la façon d’importer le fichier, reportez-vous à la section de l’article : [comment créer un VNet à l’aide d’un fichier de configuration de réseau dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="6-download-keys"></a>6. Téléchargez des clés

Une fois votre nouveau tunnels ont été ajoutées, utilisez l’applet de commande PowerShell `Get-AzureVNetGatewayKey` pour obtenir l’IPsec/IKE des clés prépartagées pour chaque tunnel.

Par exemple :

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Si vous préférez, vous pouvez également utiliser l’API REST *Obtenir virtuel réseau passerelle clé partagée* pour obtenir les clés prépartagées.

## <a name="7-verify-your-connections"></a>7. Vérifiez vos connexions

Vérifiez l’état du tunnel de plusieurs sites. Après avoir téléchargé les clés pour chaque tunnel, vous voudrez sans doute vérifier les connexions. Utilisez `Get-AzureVnetConnection` pour obtenir la liste des tunnels de réseau virtuel, comme indiqué dans l’exemple ci-dessous. VNet1 est le nom de la VNet.

    Get-AzureVnetConnection -VNetName VNET1
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les passerelles VPN, reportez-vous à la section [Sur les passerelles VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

