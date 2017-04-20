<properties
    pageTitle="Comment planifier votre réseau virtuel pour une collection d’Azure RemoteApp | Microsoft Azure"
    description="Apprenez à planifier votre réseau virtuel pour une collection d’Azure RemoteApp."
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>Comment planifier votre réseau virtuel Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Ce document décrit comment configurer votre réseau virtuel Azure (VNET) et le sous-réseau pour Azure RemoteApp. Si vous ne connaissez pas les réseaux virtuels Azure, il s’agit d’une fonctionnalité qui vous permet de virtualiser votre infrastructure de réseau vers le nuage et pour créer des solutions de hybride avec Azure et vos ressources sur site. Vous pouvez en savoir plus à ce sujet [ici](../virtual-network/virtual-networks-overview.md).

Si vous souhaitez définir des stratégies de sécurité pour le trafic (entrant et sortant) de votre réseau virtuel où vous déployez RemoteApp d’Azure, nous vous recommandons vivement de créer un sous-réseau distinct pour Azure RemoteApp du reste de vos déploiements dans le réseau virtuel Azure. Pour plus d’informations sur la définition des stratégies de sécurité sur votre sous-réseau Azure réseau virtuel, veuillez consulter [ce qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md).

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Types de collections Azure RemoteApp Azure réseaux virtuels

Les graphiques suivants illustrent les deux options de collection différente lorsque vous voulez utiliser un réseau virtuel.

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>Collection de nuage RemoteApp Azure avec VNET

 ![RemoteApp Azure - collection de nuage avec un VNET](./media/remoteapp-planvpn/ra-cloudvpn.png)

Représente une collection d’Azure RemoteApp où la session RemoteApp hôtes ont besoin d’accéder à toutes les ressources sont déployés dans Azure. Ils peuvent être dans le même VNET que le RemoteApp VNET ou un VNET différent dans Azure.

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>Azure collection hybride de RemoteApp avec VNET

![RemoteApp Azure - collection hybride avec un VNET](./media/remoteapp-planvpn/ra-hybridvpn.png)

Représente une collection d’Azure RemoteApp où certaines ressources auxquels doivent accéder les hôtes de la session RemoteApp sont déployés sur site. Le VNET RemoteApp est lié au réseau local à l’aide de technologies hybrides Azure comme un VPN de site à site ou Express itinéraire.


## <a name="how-the-system-works"></a>Comment fonctionne le système

Dans les coulisses Azure RemoteApp déploie des machines virtuelles Azure (avec votre image téléchargée) pour le sous-réseau du réseau virtuel que vous avez choisi au cours de la mise en service. Si vous avez opté pour une collection hybride, essaie de résoudre le nom de domaine complet du contrôleur de domaine que vous avez entré dans la configuration du flux de travail avec le serveur DNS fourni dans le réseau virtuel.  
Si vous vous connectez à un réseau virtuel existant, veillez à exposer les ports nécessaires dans vos groupes de sécurité de réseau dans votre sous-réseau Azure RemoteApp. 

Nous vous recommandons de qu'utiliser un [sous-réseau suffisamment grand pour Azure RemoteApp](remoteapp-vnetsizing.md). La plus grande prise en charge par le réseau virtuel Azure est /8 (à l’aide de définitions de sous-réseau CIDR). Votre sous-réseau doit être assez grande pour contenir tous les Azure RemoteApp ordinateurs virtuels pendant l’échelle lorsque plusieurs utilisateurs accèdent aux applications. 

Voici les choses que vous devez activer sur votre sous-réseau de réseau virtuel : 

2.  Le trafic sortant depuis le sous-réseau devrait pouvoir sur la plage de ports 10101-10175 pour communiquer avec un des services Azure RemoteApp internes.
3.  Convient le trafic sortant de votre sous-réseau pour se connecter au stockage Azure sur le port 443
4.  Si vous avez Active Directory hébergé dans Azure, assurez-vous que tout ordinateur virtuel au sein du sous-réseau de réseau virtuel pour Azure RemoteApp est en mesure de se connecter à ce contrôleur de domaine. Le serveur DNS dans le réseau virtuel doit être en mesure de résoudre le nom de domaine complet de ce contrôleur de domaine.


## <a name="virtual-network-with-forced-tunneling"></a>Réseau virtuel avec le tunneling de force

[Forcé de tunneling](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) est maintenant pris en charge pour toutes les nouvelles collections Azure RemoteApp. Actuellement, nous ne gèrent pas la migration d’une collection existante pour prendre en charge le tunneling forcée.  Vous devez supprimer toutes les collections existantes à l’aide de le VNET que vous liez Azure RemoteApp et créez-en un nouveau pour obtenir forcé tunneling activé sur vos collections. 
