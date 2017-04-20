<properties
   pageTitle="À l’aide de SAP sur les machines virtuelles Windows | Microsoft Azure"
   description="Effacer sur l’utilisation de SAP sur Windows, machines virtuelles (VM) de Microsoft Azure"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/04/2016"
   ms.author="sedusch"/>

# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>À l’aide de SAP sur les ordinateurs virtuels Windows Azure

Le Cloud Computing est un terme largement utilisé, qui est de plus en plus et plus d’importance au sein du secteur informatique, des petites entreprises à des entreprises multinationales et les grands. Microsoft Azure est la plateforme de Services de Cloud de Microsoft, qui offre un large éventail de nouvelles possibilités. Désormais, les clients sont en mesure de fournir rapidement et de mettre hors service les applications comme des Services en nuage, afin qu’ils ne sont pas limités à des restrictions techniques ou de budgétisation. Au lieu d’investir du temps et budget dans l’infrastructure matérielle, sociétés peuvent se concentrer sur l’application, des processus métiers et ses avantages pour les clients et les utilisateurs.

Avec les ordinateurs virtuels de Microsoft Azure, Microsoft propose une Infrastructure complète en tant que Service (IaaS) plate-forme. Les applications SAP NetWeaver basé sont prises en charge sur les ordinateurs virtuels Azure (IaaS). Les livres blancs ci-dessous décrivent comment planifier et implémenter les applications SAP NetWeaver basé sur les ordinateurs virtuels Windows Azure. Vous pouvez également implémenter les applications SAP NetWeaver basé sur les [machines virtuelles de Linux](virtual-machines-linux-classic-sap-get-started.md).

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver sur Azure - HA

Titre : SAP NetWeaver sur Azure - Clustering SAP ASC/SCS Instances à l’aide de Cluster de basculement Windows Server sur Azure avec SIÔS DataKeeper

Résumé : « ce document décrit comment utiliser SIÔS DataKeeper pour définir une configuration de SAP ASC/SCS hautement disponible sur Azure. SAP protège leur seul point des composants d’échec comme SAP ASC/SCS ou les Services de réplication de file d’attente avec des configurations de Cluster de basculement Windows Server qui nécessitent des disques partagés. Ces composants SAP sont essentielles pour les fonctionnalités d’un système SAP. Fonctionnalités de haute disponibilité doivent par conséquent être mis en place afin de vous assurer que les composants peuvent supporter une défaillance d’un serveur ou un ordinateur virtuel comme avec les configurations de Cluster de Windows sans système d’exploitation et les environnements Hyper-V. Depuis août 2015 Azure sur lui-même ne peut pas fournir les disques partagés qui seraient nécessaires pour les fenêtres en fonction des configurations hautement disponibles requises pour ces composants SAP critiques. Toutefois à l’aide du produit DataKeeper en SIÔS, les configurations de Cluster de basculement Windows Server en fonction des besoins pour SAP ASC/SCS peuvent être générées sur la plateforme Azure IaaS. Ce document décrit dans une approche étape par étape procédure d’installation d’une configuration de Cluster de basculement Windows Server avec disque partagé fourni par Datakeeper SIÔS dans Azure. Le livre explique détails dans des configurations sur le côté d’Azure, Windows et SAP qui rendent la configuration haute disponibilité fonctionne de manière optimale. Le livre vient compléter la Documentation d’Installation de SAP et les Notes SAP qui représentent les principales ressources des installations et des déploiements de logiciels SAP sur compte tenu des plates-formes.

Mise à jour : Août 2015

[Téléchargez ce guide maintenant](http://go.microsoft.com/fwlink/?LinkId=613056)
