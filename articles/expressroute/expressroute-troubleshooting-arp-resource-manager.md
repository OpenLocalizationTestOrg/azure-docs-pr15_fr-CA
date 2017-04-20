<properties 
   pageTitle="Guide de dépannage ExpressRoute - obtention des tables ARP | Microsoft Azure"
   description="Cette page fournit des instructions sur l’obtention des tables du protocole ARP pour un circuit de ExpressRoute"
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

#<a name="expressroute-troubleshooting-guide---getting-arp-tables-in-the-resource-manager-deployment-model"></a>Guide de dépannage de l’ExpressRoute - tables ARP de mise en route dans le modèle de déploiement du Gestionnaire de ressources

> [AZURE.SELECTOR]
[PowerShell - Gestionnaire de ressources](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - classique](expressroute-troubleshooting-arp-classic.md)

Cet article vous guide à travers les étapes à apprendre les tables de correspondance ARP pour votre circuit ExpressRoute. 

>[AZURE.IMPORTANT] Ce document est destiné à vous aider à diagnostiquer et à résoudre des problèmes simples. Il n’est pas destiné à être un remplacement pour la prise en charge de Microsoft. Si vous ne parvenez pas à résoudre le problème en suivant les conseils ci-dessous, vous devez ouvrir un ticket de support avec [prise en charge par Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adresse des tables ARP et le protocole ARP (Resolution)
Protocole de résolution d’adresse (ARP) est un protocole de couche 2 défini dans la [RFC 826](https://tools.ietf.org/html/rfc826). ARP est utilisé pour mapper l’adresse Ethernet (adresse MAC) avec une adresse ip.

La table ARP fournit un mappage de l’adresse ipv4 et une adresse MAC pour une homologation particulière. La table ARP d’une homologation de circuit ExpressRoute fournit les informations suivantes pour chaque interface (principal et secondaire)

1. Mappage de l’adresse ip interface du routeur sur site à l’adresse MAC
2. Mappage de l’adresse ip interface du routeur ExpressRoute à l’adresse MAC
3. Âge du mappage

Tables ARP permet de valider la configuration de la couche 2 et résolution des problèmes de base de la couche 2 problèmes de connectivité. 

Exemple de table ARP : 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


La section suivante fournit des informations sur la manière d’afficher les tables de correspondance ARP vus par les routeurs de bordure de ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Conditions préalables pour l’apprentissage des tables ARP

Assurez-vous d’avoir les éléments suivants avant de vous la progression plus

 - Un circuit de ExpressRoute valide configuré avec une homologation au moins un. Le circuit doit être entièrement configuré par le fournisseur de connectivité. Vous (ou votre fournisseur de connectivité) doit avoir configuré au moins de la peerings (public de privé, Azure Azure et Microsoft) sur ce circuit.
 - Plages d’adresses IP utilisés pour la configuration de la peerings (public de privé, Azure Azure et Microsoft). Examinez les exemples d’affectation d’adresse ip dans la [page Configuration requise de la gamme ExpressRoute](expressroute-routing.md) pour comprendre comment les adresses ip sont mappés aux interfaces de votre côté et sur le côté ExpressRoute. Vous pouvez obtenir des informations sur la configuration d’homologation en consultant la [page de configuration homologation ExpressRoute](expressroute-howto-routing-arm.md).
 - Les informations de votre équipe de mise en réseau / fournisseur de connectivité sur les adresses MAC des interfaces utilisées avec ces adresses IP.
 - Vous devez disposer du dernier module PowerShell pour Azure (version 1.50 ou plus récente).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obtention des tables ARP pour votre circuit ExpressRoute
Cette section fournit des instructions sur la manière d’afficher les tables de correspondance ARP par homologation à l’aide de PowerShell. Vous ou votre fournisseur de connectivité doit avoir configuré l’homologation avant de poursuivre. Chaque circuit a deux chemins d’accès (principales et secondaires). Vous pouvez vérifier la table ARP pour chaque chemin de façon indépendante.

### <a name="arp-tables-for-azure-private-peering"></a>Tables de correspondance ARP pour homologation privé Azure
L’applet de commande suivant fournit le protocole ARP tables pour homologation privé Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary
        
        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Sortie de l’exemple est illustrée ci-dessous pour un des chemins

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tables de correspondance ARP pour homologation public Azure
L’applet de commande suivant fournit le protocole ARP tables pour homologation public Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary
        
        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Sortie de l’exemple est illustrée ci-dessous pour un des chemins

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tables de correspondance ARP pour l’homologation de Microsoft
L’applet de commande suivant fournit le protocole ARP tables pour l’homologation de Microsoft

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary
        
        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Sortie de l’exemple est illustrée ci-dessous pour un des chemins

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>L’utilisation de ces informations
La table ARP d’une homologation peut être utilisée pour déterminer valider la connectivité et configuration de la couche 2. Cette section fournit une vue d’ensemble de l’apparence des tables ARP sous différents scénarios.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Table ARP lorsqu’un circuit est en état de fonctionnement (état attendu)

 - La table ARP aura une entrée pour le côté local avec une adresse IP et adresse MAC et une entrée similaire pour le côté Microsoft. 
 - Le dernier octet de l’adresse ip locale sera toujours un nombre impair.
 - Le dernier octet de l’adresse ip de Microsoft sera toujours un nombre pair.
 - La même adresse MAC s’affiche sur le côté de Microsoft pour toutes les 3 peerings (principales / secondaires). 


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP lorsque la table locale côté fournisseur de connectivité a des problèmes

 - Une seule entrée s’affiche dans la table ARP. Le mappage entre l’adresse MAC et l’adresse IP dans la partie de Microsoft s’affiche. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Ouvrez une demande de support auprès de votre fournisseur de connectivité pour déboguer ces problèmes. 


### <a name="arp-table-when-microsoft-side-has-problems"></a>Table ARP lorsque Microsoft side rencontre des problèmes

 - Vous ne verrez pas un tableau ARP pour une homologation s’il existe des problèmes chez Microsoft. 
 -  Ouvrez un ticket de support avec [prise en charge par Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Spécifier que vous avez un problème avec la connectivité de la couche 2. 

## <a name="next-steps"></a>Étapes suivantes

 - Valider les configurations de couche 3 pour votre circuit ExpressRoute
     - Obtenir itinéraire résumé pour déterminer l’état des sessions de protocole BGP 
     - Obtenir la table de routage afin de déterminer les préfixes sont annoncés sur ExpressRoute
 - Valider le transfert de données en passant en revue les octets en entrée / sortie
 - Ouvrez un ticket de support avec [la prise en charge de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si vous rencontrez toujours des problèmes.
