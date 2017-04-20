<properties
   pageTitle="Conditions requises pour l’adoption de ExpressRoute | Microsoft Azure"
   description="Cette page fournit une liste des exigences à respecter avant de commander un circuit de ExpressRoute d’Azure."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>


# <a name="expressroute-prerequisites--checklist"></a>Liste de vérification et les conditions préalables de ExpressRoute  

Pour vous connecter aux services de cloud de Microsoft à l’aide de ExpressRoute, vous devez vérifier que les conditions suivantes, répertoriées dans les sections ci-dessous sont remplies.

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Compte Azure

- Un compte Microsoft Azure valide et actif. Ceci est nécessaire pour le circuit de ExpressRoute l’installation. Circuits de ExpressRoute sont des ressources de l’abonnement Azure. Un abonnement Azure est une exigence, même si la connectivité est limitée à des services en nuage non Microsoft Azure, tels que les services Office 365 et CRM en ligne.
- Un abonnement à Office 365 actif (si vous utilisez des services Office 365). Consultez la section [Configuration requise spécifique à Office 365](#office-365-specific-requirements) de cet article pour plus d’informations.

## <a name="connectivity-provider"></a>Fournisseur de connectivité
- Vous pouvez travailler avec un [partenaire de connectivité ExpressRoute](expressroute-locations.md#partners) pour se connecter au nuage Microsoft. Vous pouvez configurer une connexion entre votre réseau de locaux et de Microsoft de [trois façons](expressroute-introduction.md#howtoconnect). 
- Si votre fournisseur n’est pas un partenaire de connectivité des ExpressRoute, vous pouvez toujours vous connecter dans le nuage de Microsoft via un [fournisseur de nuage exchange](expressroute-locations.md#nonpartners).

## <a name="network-requirements"></a>Configuration réseau requise
- **Connectivité redondante**: il est inutile de redondance sur la connectivité physique entre vous et votre fournisseur. Microsoft n’exige pas des sessions BGP redondantes à définir entre les routeurs de Microsoft et les routeurs d’homologation, même lorsque vous avez simplement [une connexion physique à un échange de nuage](expressroute-faqs.md#onep2plink). 
- **Routage**: en fonction de comment vous connectez au Cloud Microsoft, vous ou votre fournisseur est nécessaire configurer et gérer les sessions BGP pour [les domaines de routage](expressroute-circuit-peerings.md). Certains fournisseur de connectivité Ethernet ou d’un fournisseur de nuage exchange peut proposer la gestion BGP sous la forme d’un service à valeur ajoutée.
- **NAT**: Microsoft n’accepte que les adresses IP publiques via l’homologation de Microsoft. Si vous utilisez des adresses IP privées sur votre réseau local, vous ou votre fournisseur doivent traduire les adresses IP privées à l’adresse IP publique répond [à l’aide de NAT](expressroute-nat.md).
- **QoS**: Skype pour entreprise possède des services différents (par exemple, vidéo, texte) qui nécessitent le traitement de QoS différencié. Vous et votre fournisseur doivent suivre les [impératifs de qualité de service](expressroute-qos.md).
- **Sécurité réseau**: vous devez considérer la [sécurité de réseau](../best-practices-network-security.md) lorsque vous vous connectez au Cloud Microsoft via ExpressRoute.
 
## <a name="office-365"></a>Office 365

Si vous envisagez d’activer Office 365 sur ExpressRoute, veuillez consulter les documents suivants pour plus d’informations sur la configuration requise d’Office 365.


- [Vue d’ensemble de ExpressRoute pour Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Routage avec ExpressRoute pour Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [Plages d’adresses IP et des URL d’Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Planification de réseau et de réglage des performances pour Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Outils et calculateurs de la bande passante réseau](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Intégration à Office 365 avec les environnements sur site](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## <a name="crm-online"></a>CRM en ligne 
Si vous envisagez d’activer CRM en ligne sur ExpressRoute, veuillez consulter les documents suivants pour plus d’informations à propos de CRM en ligne

- [URL en ligne de CRM](https://support.microsoft.com/kb/2655102) et les [plages d’adresses IP](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur ExpressRoute, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).
- Trouver un fournisseur de connectivité ExpressRoute. Consultez [ExpressRoute partenaires et emplacements d’homologation](expressroute-locations.md).
- Voir la rubrique Configuration requise pour le [routage](expressroute-routing.md), [NAT](expressroute-nat.md) et [QoS](expressroute-qos.md).
- Configurez votre connexion ExpressRoute.
    - [Créer un circuit de ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurer le routage](expressroute-howto-routing-classic.md)
    - [Lier un VNet d’un circuit de ExpressRoute](expressroute-howto-linkvnet-classic.md)

