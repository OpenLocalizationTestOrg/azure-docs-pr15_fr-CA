<properties
   pageTitle="Guide de dépannage ExpressRoute : obtention des tables ARP | Microsoft Azure"
   description="Cette page fournit des instructions pour l’obtention des tables du protocole ARP pour un circuit de ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="expressroute-troubleshooting-guide-getting-arp-tables-in-the-classic-deployment-model"></a>Guide de dépannage ExpressRoute : obtention des tables ARP dans le modèle de déploiement classique

> [AZURE.SELECTOR]
[PowerShell - Gestionnaire de ressources](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - classique](expressroute-troubleshooting-arp-classic.md)

Cet article vous guide tout au long de la procédure pour obtenir les tables du protocole ARP (Address Resolution) pour votre circuit Azure ExpressRoute.

>[AZURE.IMPORTANT] Ce document est destiné à vous aider à diagnostiquer et à résoudre des problèmes simples. Il n’est pas destiné à être un remplacement pour la prise en charge de Microsoft. Si vous ne pouvez pas résoudre le problème en suivant les indications suivantes, ouvrez une demande de prise en charge avec [Microsoft Azure aide + prise en charge](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adresse des tables ARP et le protocole ARP (Resolution)
ARP est un protocole de couche 2 qui est défini dans la [RFC 826](https://tools.ietf.org/html/rfc826). ARP est utilisé pour mapper une adresse Ethernet (adresse MAC) à une adresse IP.

Une table ARP fournit un mappage de l’adresse IPv4 et une adresse MAC pour une homologation particulière. La table ARP d’une homologation de circuit ExpressRoute fournit les informations suivantes pour chaque interface (principal et secondaire) :

1. Mappage d’une adresse IP locale d’interface routeur à une adresse MAC
2. Mappage d’une adresse IP de ExpressRoute routeur interface à une adresse MAC
3. L’âge du mappage

Avec la validation de la configuration de la couche 2 et sur la résolution des problèmes de connectivité de base Layer 2 peuvent aider aux tables ARP.

Voici un exemple d’une table ARP :

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


La section suivante fournit des informations sur la façon d’afficher les tables de correspondance ARP sont visibles par les routeurs de bordure de ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Conditions préalables à l’aide de ARP tables

Assurez-vous d’avoir les éléments suivants avant de continuer :

 - Un circuit de ExpressRoute valide qui est configuré avec une homologation au moins un. Le circuit doit être entièrement configuré par le fournisseur de connectivité. Vous (ou votre fournisseur de connectivité) devez configurer au moins un de la peerings (public Azure de privé, Azure, ou Microsoft) sur ce circuit.

 - Plages d’adresses IP qui sont utilisées pour la configuration de la peerings (public de privé, Azure Azure et Microsoft). Examinez les exemples d’affectation d’adresse IP dans la [page Configuration requise de la gamme ExpressRoute](expressroute-routing.md) pour comprendre comment les adresses IP sont mappés aux interfaces à votre aise et sur le côté ExpressRoute. Vous pouvez obtenir des informations sur la configuration d’homologation en consultant la [page de configuration homologation ExpressRoute](expressroute-howto-routing-classic.md).

 - Informations auprès de votre fournisseur d’équipe ou de connectivité réseau sur les adresses MAC des interfaces qui sont utilisées avec ces adresses IP.

 - Le dernier module de Windows PowerShell pour Azure (version 1.50 ou ultérieure).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tables de correspondance ARP pour votre circuit ExpressRoute
Cette section fournit des instructions sur la façon d’afficher les tables de correspondance ARP pour chaque type d’homologation, à l’aide de PowerShell. Avant de continuer, vous ou votre fournisseur de connectivité doit configurer l’homologation. Chaque circuit a deux chemins d’accès (principales et secondaires). Vous pouvez vérifier la table ARP pour chaque chemin de façon indépendante.

### <a name="arp-tables-for-azure-private-peering"></a>Tables de correspondance ARP pour homologation privé Azure
L’applet de commande suivant fournit le protocole ARP tables pour homologation privé Azure :

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Voici un exemple de sortie d’un des chemins d’accès :

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tables de correspondance ARP pour homologation public Azure :
L’applet de commande suivant fournit le protocole ARP tables pour homologation public Azure :

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Voici un exemple de sortie d’un des chemins d’accès :

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Voici un exemple de sortie d’un des chemins d’accès :

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tables de correspondance ARP pour l’homologation de Microsoft
L’applet de commande suivant fournit le protocole ARP tables pour l’homologation de Microsoft :

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Exemple de sortie ci-dessous pour l’un des chemins d’accès :

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>L’utilisation de ces informations
La table ARP d’une homologation peut être utilisée pour valider la connectivité et la configuration de la couche 2. Cette section fournit une vue d’ensemble de l’apparence des tables ARP dans différents scénarios.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Table ARP lorsqu’un circuit est dans un état de fonctionnement (attendu)

 - La table ARP possède une entrée pour le côté local avec une adresse IP et MAC et une entrée similaire pour le côté Microsoft.
 - Le dernier octet de l’adresse IP locale est toujours un nombre impair.
 - Le dernier octet de l’adresse IP de Microsoft est toujours un nombre pair.
 - La même adresse MAC s’affiche sur le côté de Microsoft pour tous les peerings de trois (principal et secondaire).


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Table ARP lorsqu’elle est local ou lorsque le côté de connectivité-fournisseur rencontre des problèmes

 Il apparaît qu’une seule entrée dans la table ARP. Il illustre le mappage entre l’adresse MAC et l’adresse IP qui est utilisé sur le côté de Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Si vous rencontrez ce problème, ouvrez une demande de support auprès de votre fournisseur de connectivité pour le résoudre.


### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Table ARP lorsque du côté Microsoft rencontre des problèmes

 - Vous ne verrez pas un tableau ARP pour une homologation s’il existe des problèmes chez Microsoft.
 -  Ouvrez une demande de prise en charge avec [Microsoft Azure aide + prise en charge](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Spécifier que vous avez un problème avec la connectivité de la couche 2.

## <a name="next-steps"></a>Étapes suivantes

 - Valider les configurations de couche 3 pour votre circuit ExpressRoute :
     - Obtenir un itinéraire résumé pour déterminer l’état des sessions BGP.
     - Obtenir une table de routage afin de déterminer les préfixes sont annoncés sur ExpressRoute.
 - Valider le transfert de données en passant en revue les octets d’entrée et de sortie.
 - Ouvrez une demande de support avec [Microsoft Azure aide + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si vous rencontrez toujours des problèmes.
