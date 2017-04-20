<properties 
   pageTitle="Conception et planification de la passerelle VPN | Microsoft Azure"
   description="En savoir plus sur la planification de la passerelle VPN et la conception de coexistence, hybride et les connexions de VNet-à-VNet"
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
   ms.date="10/18/2016"
   ms.author="cherylmc"/>

# <a name="planning-and-design-for-vpn-gateway"></a>Planification et conception de passerelle VPN

Planification et la conception de votre coexistence et les configurations de VNet-à-VNet peuvent être simple ou complexe, en fonction de vos besoins réseau. Cet article vous guide tout au long des considérations de planification et de conception de base.

## <a name="planning"></a>Planification


### <a name="compare"></a>Options de connectivité de coexistence

Si vous souhaitez vous connecter vos sites locaux en toute sécurité un réseau virtuel, vous disposez de trois façons différentes d’effectuer cette opération : Site à Site, Site-à-Point et ExpressRoute. Comparer les connexions entre différents sites disponibles. L’option que vous choisissez peut dépendre de plusieurs considérations, telles que :


- Le type de débit de votre solution nécessite-t-elle ?
- Vous souhaitez communiquer sur l’Internet public via VPN sécurisé, ou via une connexion privée ?
- Disposez-vous d’une adresse IP publique est disponible à utiliser ?
- Vous envisagez d’utiliser un périphérique VPN ? Dans l’affirmative, est-il compatible ?
- Vous connectez quelques ordinateurs ou si vous souhaitez une connexion permanente pour votre site ?
- Quel type de passerelle VPN est requis pour la solution que vous voulez créer ?
- Quelle passerelle SKU devez-vous utiliser ?


Le tableau suivant peut vous aider à décider de la meilleure option de connectivité pour votre solution.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>Exigences de passerelle par type de VPN et de référence

[AZURE.INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

Pour plus d’informations sur les références SKU de passerelle, reportez-vous à la section [paramètres de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

#### <a name="aggregate-throughput-by-sku-and-vpn-type"></a>Débit total par type VPN et de référence

Le tableau suivant montre les types de la passerelle et le débit total estimé. Le débit total estimé peut être un facteur décisif pour votre conception.
Tarification est différent entre les références SKU de passerelle. Pour plus d’informations sur la tarification, consultez [Tarification de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Ce tableau s’applique aux modèles de déploiement classiques et le Gestionnaire de ressources.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

#### <a name="supported-configurations-by-sku-and-vpn-type"></a>Configurations prises en charge par le type référence et VPN

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 

### <a name="wf"></a>Flux de travail

La liste suivante présente le flux de travail courant pour la connectivité du nuage :

1.  Concevoir et planifier votre topologie de la connectivité et répertorier les espaces d’adressage pour tous les réseaux que vous souhaitez vous connecter.
2.  Créer un réseau virtuel Azure. 
3.  Créer une passerelle VPN pour le réseau virtuel.
4.  Créer et configurer des connexions à des réseaux locaux ou d’autres réseaux virtuels (le cas échéant).
5.  Créez et configurez une connexion Point-à-Site de la passerelle VPN d’Azure (le cas échéant).
 

## <a name="design"></a>Conception

### <a name="topologies"></a>Topologies de connexions

Commencez par examiner les schémas dans l’article [Sur la passerelle de VPN](vpn-gateway-about-vpngateways.md) . Cet article contient des diagrammes simples, les modèles de déploiement pour chaque topologie (Gestionnaire de ressources ou standard) et le déploiement des outils vous pouvez utiliser pour déployer votre configuration.   

### <a name="designbasics"></a>Concepts de base

Les sections suivantes présentent les bases de la passerelle VPN. Envisagez également [les restrictions de services réseau](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>À propos de sous-réseaux

Lorsque vous créez des connexions, vous devez prendre en compte vos plages de sous-réseau. Vous ne peut pas avoir de chevauchement de plages d’adresses de sous-réseau. Un sous-réseau qui se chevauchent est lorsqu’un réseau virtuel ou les emplacement local contient le même espace d’adressage qui contient de l’autre emplacement. Cela signifie que vous devez vos ingénieurs réseau pour vos réseaux locaux local à imaginer une plage, vous pouvez utiliser pour votre IP Azure/sous-réseaux de l’espace d’adressage. Vous avez besoin d’espace d’adressage qui n’est pas utilisé sur le réseau local sur site. 

Éviter le chevauchement des sous-réseaux est également important lorsque vous travaillez avec des connexions VNet-à-VNet. Si vos sous-réseaux se chevauchent et une adresse IP existe dans à la fois l’envoi et la destination, VNets, VNet de VNet connexions échouent. Azure ne peut pas acheminer les données vers l’autre VNet étant donné que l’adresse de destination fait partie de l’envoi du VNet. 

Les passerelles VPN requiert un sous-réseau spécifique appelé un sous-réseau de la passerelle. Tous les sous-réseaux de passerelle doivent être nommés GatewaySubnet pour fonctionner correctement. Veillez ne pas à un autre nom de votre sous-réseau de passerelle et ne déploient pas les ordinateurs virtuels ou tout autre élément dans le sous-réseau de passerelle. Consultez [les sous-réseaux de passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Sur les passerelles de réseau local

La passerelle du réseau local se réfère généralement à l’emplacement local. Dans le modèle de déploiement classique, la passerelle du réseau local est appelée à un Site de réseau Local. Lorsque vous configurez une passerelle de réseau local, vous lui donnez un nom, indiquez l’adresse IP publique du périphérique VPN sur site et spécifiez les préfixes d’adresse qui se trouvent dans l’emplacement local. Azure examine les préfixes d’adresse de destination pour le trafic réseau, consulte la configuration que vous avez spécifiée pour la passerelle du réseau local et achemine les paquets en conséquence. Vous pouvez modifier ces préfixes d’adresse si nécessaire. Pour plus d’informations, consultez [les passerelles de réseau Local](vpn-gateway-about-vpn-gateway-settings.md#lng).


#### <a name="gwtype"></a>À propos des types de passerelle

Il est essentiel de sélectionner le type de passerelle correct pour votre topologie. Si vous sélectionnez le type de problème, votre passerelle ne fonctionne pas correctement. Le type de passerelle qui spécifie comment la passerelle elle-même se connecte et est un paramètre de configuration requises pour le modèle de déploiement du Gestionnaire de ressources.

Les types de passerelle sont les suivantes :

- VPN
- ExpressRoute

#### <a name="connectiontype"></a>À propos des types de connexion

Chaque configuration nécessite un type de connexion spécifique. Les types de connexion sont les suivantes :

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>À propos des types VPN

Chaque configuration nécessite un type VPN spécifique. Si vous combinez deux configurations, telles que la création d’une connexion de Site à Site et une connexion Point-à-Site pour la même VNet, vous devez utiliser un type VPN qui satisfait les exigences de connexion.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)] 

Les tableaux suivants indiquent le type VPN, car il correspond à la configuration de chaque connexion. Assurez-vous que le type VPN pour votre passerelle correspond à la configuration que vous souhaitez créer. 


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)] 

### <a name="devices"></a>Périphériques VPN pour les connexions de Site à Site

Pour configurer une connexion de Site à Site, quel que soit le modèle de déploiement, vous avez besoin des éléments suivants :

- Un périphérique VPN qui est compatible avec les passerelles VPN d’Azure
- Une adresse IP IPv4 orienté public qui n’est pas derrière un NAT

Vous devez configurer l’appareil VPN ou une personne qui peut configurer le périphérique pour vous. Pour plus d’informations sur les périphériques VPN, voir [périphériques sur VPN](vpn-gateway-about-vpn-devices.md). L’article de périphériques VPN contient des informations sur les périphériques validés, les exigences pour les périphériques qui n’ont pas été validées et des liens vers des documents de configuration de périphérique s’il est disponible.

### <a name="forcedtunnel"></a>Envisagez de routage de tunnel forcée

Pour la plupart des configurations, vous pouvez configurer le tunneling forcée. Tunneling forcée vous permet de rediriger ou « force » tout trafic Internet lié aux revenir à votre emplacement sur site via un tunnel VPN de Site à Site d’inspection et d’audit. Il s’agit d’une exigence de sécurité critiques pour la plupart des entreprises informatiques les stratégies. 

Sans le tunneling forcé, trafic Internet lié à partir de vos ordinateurs virtuels dans Azure sera toujours parcourir à partir de l’infrastructure de réseau Azure directement à Internet, sans l’option vous permet d’inspecter ou de vérifier le trafic. Accès Internet non autorisés susceptibles d’entraîner à la divulgation d’informations ou d’autres types de violations de la sécurité.

**Diagramme de tunnel de forcé**

![Connexion de forcé de Tunneling] (./media/vpn-gateway-plan-design/forced-tunnel.png "forcé de tunneling")

Une connexion forcée de tunnel peut être configurée dans les deux modèles de déploiement et à l’aide de différents outils. Reportez-vous au tableau suivant pour plus d’informations. Nous mettons à jour cette table comme de nouveaux articles, les nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de la table.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 



## <a name="next-steps"></a>Étapes suivantes

Consultez les articles de [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md) et [Sur la passerelle de VPN](vpn-gateway-about-vpngateways.md) pour plus d’informations pour vous aider dans votre conception.

Pour plus d’informations sur les paramètres de la passerelle spécifique, reportez-vous à la section [Sur les paramètres de passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md).




