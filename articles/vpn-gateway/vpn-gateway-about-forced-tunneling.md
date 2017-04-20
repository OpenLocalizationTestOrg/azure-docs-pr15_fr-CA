<properties 
   pageTitle="Configurer le tunneling forcée pour les connexions de Site à Site en utilisant le modèle de déploiement classique | Microsoft Azure"
   description="Comment rediriger ou « force » du trafic Internet lié à votre magasin local."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurer un tunneling forcé en utilisant le modèle de déploiement classique

> [AZURE.SELECTOR]
- [PowerShell - classique](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Gestionnaire de ressources](vpn-gateway-forced-tunneling-rm.md)

Tunneling forcée vous permet de rediriger ou « force » tout trafic Internet lié aux revenir à votre emplacement sur site via un tunnel VPN de Site à Site d’inspection et d’audit. Il s’agit d’une exigence de sécurité critiques pour la plupart des entreprises informatiques les stratégies. 

Sans le tunneling forcé, trafic Internet lié à partir de vos ordinateurs virtuels dans Azure sera toujours parcourir à partir de l’infrastructure de réseau Azure directement à Internet, sans l’option vous permet d’inspecter ou de vérifier le trafic. Accès Internet non autorisés susceptibles d’entraîner à la divulgation d’informations ou d’autres types de violations de la sécurité.

Cet article vous guidera dans la configuration forcée de tunneling pour réseaux virtuels créés à l’aide du modèle de déploiement classique. 

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modèles de déploiement et des outils pour le tunneling de force**

Une connexion forcée de tunnel peut être configurée pour le modèle de déploiement classique et le modèle de déploiement du Gestionnaire de ressources. Reportez-vous au tableau suivant pour plus d’informations. Nous mettons à jour cette table comme de nouveaux articles, les nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de la table.

[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="requirements-and-considerations"></a>Configuration requise et remarques

Le tunneling forcée dans Azure est configuré via les itinéraires définis par l’utilisateur (UDR) du réseau virtuel. Rediriger le trafic d’un site local est exprimée sous la forme d’un itinéraire par défaut vers la passerelle VPN d’Azure. La section suivante répertorie la limite actuelle de la table de routage et itinéraires pour un réseau virtuel Azure :


-  Chaque sous-réseau du réseau virtuel a une table de routage du système intégré. La table de routage système comporte trois groupes d’itinéraires suivants :

    - **Route de VNet local :** Directement à la destination de machines virtuelles sur le même réseau virtuel
    
    - **Sur les itinéraires locaux :** À la passerelle VPN d’Azure
    
    - **Gamme par défaut :** Directement à Internet. Paquets destinés à des adresses IP privées non couverts par les deux itinéraires précédentes seront supprimées.


-  Avec la publication des itinéraires de défini par l’utilisateur, vous pouvez créer une table de routage pour ajouter un itinéraire par défaut et ensuite associer la table de routage à votre sous-réseau de VNet pour permettre le tunneling forcé sur ces sous-réseaux.

- Vous devez définir un « site par défaut « parmi les sites locaux de coexistence connecté au réseau virtuel.

- Tunneling forcé doit être associé à un VNet qui a une passerelle VPN routage dynamique (pas de passerelle statique).
 
- ExpressRoute forcé tunneling via ce mécanisme n’est pas configurée, mais au lieu de cela, est activé par un itinéraire par défaut via les sessions d’homologation ExpressRoute BGP de publicité. Consultez la [Documentation de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) pour plus d’informations.



## <a name="configuration-overview"></a>Vue d’ensemble de la configuration

Dans l’exemple suivant, la partie frontale sous-réseau n’est pas forcé par tunnel. Les charges de travail dans le sous-réseau frontal peuvent continuer à accepter et à répondre directement aux demandes des clients à partir d’Internet. Les sous-réseaux de milieu de gamme et back-end sont forcés par tunnel. Toutes les connexions sortantes à partir de ces deux sous-réseaux à Internet seront forcées ou redirigées vers un site local via parmi les tunnels VPN de S2S.

Cela vous permet de restreindre et contrôler l’accès à Internet à partir de vos machines virtuelles ou cloud services dans Azure, tout en continuant à activer votre architecture à plusieurs niveaux service requis. Vous pouvez également appliquer tunneling forcée pour les réseaux virtuels tout s’il n’y aucune des charges de travail avec accès Internet de vos réseaux virtuels.


![Forcé de Tunneling](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments suivants avant de commencer la configuration.

- Un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Un réseau virtuel configuré. 

- La version la plus récente des applets de commande PowerShell d’Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .


## <a name="configure-forced-tunneling"></a>Configurer le tunneling de force

La procédure suivante vous permet de spécifier le tunneling de force pour un réseau virtuel. Les étapes de configuration correspondent au fichier de configuration réseau VNet.



    <VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>

Dans cet exemple, le réseau virtuel « Multiniveau-VNet » a trois sous-réseaux : sous-réseaux *Frontend*et *Backend* *milieu de gamme*, avec des connexions de quatre locaux croisée : *DefaultSiteHQ*et trois *Branches*. 

Les étapes seront l' *DefaultSiteHQ* en tant que la connexion de site par défaut pour forcé de tunneling et configurer milieu de gamme et les sous-réseaux back-end à utiliser forcé de tunneling.


1. Créer une table de routage. L’applet de commande suivante permet de créer votre table de routage.

        New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

2. Ajouter un itinéraire par défaut à la table de routage. 

    L’exemple suivant ajoute un itinéraire par défaut à la table de routage créée à l’étape 1. Notez que l’itinéraire uniquement pris en charge est le préfixe de la destination de « 0.0.0.0/0 » pour le saut suivant « VPNGateway ».
 
        Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

3. Associer la table de routage pour les sous-réseaux. 

    Après une table de routage est créée et un itinéraire ajouté, utilisez l’exemple suivant permet d’ajouter ou d’associer la table de routage pour un sous-réseau VNet. L’exemple ajoute « MyRouteTable » de la table d’itinéraires vers les sous-réseaux du milieu de gamme et back-end de VNet multiniveau-VNet.

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

4. Affecter un site par défaut pour forcer le tunneling. 

    Dans l’étape précédente, les exemples de scripts d’applet de commande créé la table de routage et associé à la table d’itinéraires à deux sous-réseaux VNet. L’étape restante est pour sélectionner un site local entre les connexions de plusieurs sites du réseau virtuel sous le site par défaut ou un tunnel.

        $DefaultSite = @("DefaultSiteHQ")
        Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## <a name="additional-powershell-cmdlets"></a>Autres applets de commande PowerShell

### <a name="to-delete-a-route-table"></a>Pour supprimer une table de routage

    Remove-AzureRouteTable -Name <routeTableName>

### <a name="to-list-a-route-table"></a>Pour afficher une table de routage

    Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

### <a name="to-delete-a-route-from-a-route-table"></a>Pour supprimer un itinéraire à partir d’une table de routage

    Remove-AzureRouteTable –Name <routeTableName>

### <a name="to-remove-a-route-from-a-subnet"></a>Pour supprimer un itinéraire à partir d’un sous-réseau

    Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Pour répertorier la table de routage associée à un sous-réseau.
    
    Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Pour supprimer un site par défaut à partir de la passerelle VPN de VNet

    Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>






