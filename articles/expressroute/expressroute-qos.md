<properties
   pageTitle="Exigences QoS pour ExpressRoute | Microsoft Azure"
   description="Cette page fournit des spécifications détaillées pour la configuration et la gestion de QoS pour des circuits de ExpressRoute."
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

# <a name="expressroute-qos-requirements"></a>Exigences QoS de ExpressRoute

Skype pour entreprise a différentes charges de travail nécessitant un traitement différencié de QoS. Si vous envisagez de consommer des services vocaux via ExpressRoute, vous devez respecter les exigences décrites ci-dessous.

![](./media/expressroute-qos/expressroute-qos.png)

>[AZURE.NOTE] Exigences de qualité de service s’appliquent à Microsoft homologation uniquement. Les valeurs DSCP dans votre trafic reçu sur l’homologation public Azure et homologation privé Azure seront remis à 0. 

Le tableau suivant fournit une liste des marquages DSCP utilisé par Skype pour les entreprises. Pour plus d’informations, consultez [Gestion de la QoS pour Skype pour les entreprises](https://technet.microsoft.com/library/gg405409.aspx) .

| **Classe de trafic** | **Traitement (marquage DSCP)** | **Skype pour les charges de travail métier** |
|---|---|---|
| **Voix** | EF (46) | Skype / voix de Lync |
| **Interactive** | AF41 (34) | Vidéo |
|   | AF21 (18) | Partage de l’application | 
| **Par défaut** | AF11 (10) | Transfert de fichiers|
|   | CS0 (0) | Autre chose| 


- Vous devez classer les charges de travail et marquez les valeurs DSCP. Suivez les instructions fournies [ici](https://technet.microsoft.com/library/gg405409.aspx) sur la définition des marquages DSCP dans votre réseau.

- Vous devez configurer et prendre en charge plusieurs files d’attente de qualité de service au sein de votre réseau. Voice doit être une classe autonome et recevoir le traitement EF spécifié dans RFC 3246. 

- Vous pouvez choisir le mécanisme de files d’attente, stratégie de détection de congestion et allocation de bande passante par classe de trafic. Toutefois, le marquage DSCP pour Skype pour les charges de l’entreprise doit être conservé. Si vous utilisez des marquages DSCP non répertoriés ci-dessus, par exemple AF31 (26), vous devez le réécrire cette valeur DSCP à 0 avant de l’envoyer à Microsoft. Microsoft envoie uniquement les paquets marquées avec la valeur DSCP indiquée dans le tableau ci-dessus. 

## <a name="next-steps"></a>Étapes suivantes

- Reportez-vous à la configuration requise pour le [routage](expressroute-routing.md) et [NAT](expressroute-nat.md).
- Consultez les liens suivants pour configurer votre connexion ExpressRoute.

    - [Créer un circuit de ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurer le routage](expressroute-howto-routing-classic.md)
    - [Lier un VNet d’un circuit de ExpressRoute](expressroute-howto-linkvnet-classic.md)
