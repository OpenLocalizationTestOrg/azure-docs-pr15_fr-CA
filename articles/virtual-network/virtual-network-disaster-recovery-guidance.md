<properties
    pageTitle="Que faire en cas d’une interruption de service Azure impact sur les réseaux virtuels Azure | Microsoft Azure"
    description="Apprenez quoi faire en cas d’une interruption de service Azure impact sur les réseaux virtuels Azure."
    services="virtual-network"
    documentationCenter=""
    authors="NarayanAnnamalai"
    manager="jefco"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="virtual-network"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="narayan;aglick"/>

#<a name="virtual-network--business-continuity"></a>Réseau virtuel – la continuité d’activité

##<a name="overview"></a>Vue d’ensemble

Un réseau virtuel (VNet) est une représentation logique de votre réseau dans le nuage. Il vous permet de définir votre propre espace d’adressage IP privé et de segmenter le réseau en sous-réseaux. VNets constitue une limite de confiance pour héberger vos ressources de calcul des ordinateurs virtuels Azure Cloud Services (rôles web/travailleur). Un VNet permet une communication IP privée directe entre ressources hébergées dans il. Un réseau virtuel peut également être lié à un réseau local via l’une des options hybride comme une passerelle VPN ou un ExpressRoute.
 
Un VNet est créé dans le cadre d’une région. Vous pouvez créer des VNets avec le même espace d’adressage dans deux régions différentes (c'est-à-dire nous East et West nous mais Impossible de se connecter les uns aux autres directement). 

##<a name="business-continuity"></a>Continuité d’activité

Il est possible que votre application risque d’être bouleversée de différentes façons. Une région donnée peut être coupée complètement en raison d’une catastrophe naturelle ou d’une catastrophe partielle dû à une panne de plusieurs périphériques et services. L’impact sur le service VNet est différent dans chacune de ces situations.

**Q : que faire en cas de panne d’une région entière ? par exemple, si une région est complètement arrêtée en raison d’une catastrophe naturelle ? Que se passe-t-il pour les réseaux virtuels hébergés dans la région ?**

R : le réseau virtuel et les ressources de la région touchée reste inaccessible pendant la durée de l’interruption de service.

![Diagramme de réseau virtuel simple](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Q : que faire pour faire de recréer le même réseau virtuel dans une région différente ?**

A: réseau virtuel (VNet) est une ressource relativement légère. Vous pouvez appeler des API Azure pour créer un VNet avec le même espace d’adressage dans une région différente. Pour recréer le même environnement que celui qui était présent dans la zone concernée, vous devez effectuer des appels d’API de redéployer vos Services en nuage (rôles web/travailleur) et les ordinateurs virtuels que vous avez. Vous devrez également tourner une passerelle VPN et de se connecter à votre réseau local, si vous aviez connectivité locale (par exemple dans un déploiement hybride).

Les instructions pour la création d’un VNet sont trouvent [ici](./virtual-networks-create-vnet-arm-pportal.md). 

**Q un réplica d’une VNet dans une région donnée peut être recréé dans une autre région à l’avance ?**

R : Oui, vous pouvez créer deux VNets utilisant le même espace d’adressage IP privé et les ressources dans deux régions différentes à l’avance. Si un client a été d’hébergement internet vis-à-vis des services dans le VNet, qu’ils peuvent configurer le trafic Manager à géo-acheminer le trafic vers la région active. Cependant, un client ne peut pas se connecter deux VNets avec le même espace d’adressage à leur réseau local sous peine de causer des problèmes de routage. Au moment d’un incident et la perte d’une VNet dans une région, un client peut se connecter l’autre VNet dans la zone disponible correspondant d’espace d’adressage à leur réseau local.

Les instructions pour la création d’un VNet sont trouvent [ici](./virtual-networks-create-vnet-arm-pportal.md).
