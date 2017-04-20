<properties 
pageTitle="Déploiement de SAP IDES EHP7 SP3 pour SAP ERP 6.0 sur Microsoft Azure | Microsoft Azure" 
description="Déploiement de SAP IDES EHP7 SP3 pour SAP ERP 6.0 sur Microsoft Azure" 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/> 
<tags 
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/> 


# <a name="deploying-sap-ides-ehp7-sp3-for-sap-erp-60-on-microsoft-azure"></a>Déploiement de SAP IDES EHP7 SP3 pour SAP ERP 6.0 sur Microsoft Azure 

Cet article décrit comment déployer IDE SAP exécutant avec SQL Server et le système d’exploitation Windows sur Microsoft Azure via SAP Cloud Appliance Library 3.0. Les captures d’écran illustrent les processus étape par étape. Déploiement d’autres solutions dans la liste le fonctionnement même à partir d’un point de vue du processus. Un doit simplement sélectionner une autre solution.

Pour commencer avec SAP Cloud Appliance bibliothèque (licence d’accès client SAP) allez [ici](https://cal.sap.com/). Il existe un blog à partir de SAP sur les nouveaux [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


Les captures d’écran suivantes montrent étape par étape comment déployer IDE de SAP sur Microsoft Azure. Le processus fonctionne de la même façon pour d’autres solutions.


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

La première image indique toutes les solutions qui sont disponibles sur Microsoft Azure. La mise en surbrillance solution IDE de SAP basée sur Windows qui est uniquement disponible sur Azure a été choisie pour passer par le processus.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

Un nouveau compte de licence d’accès client SAP doit d’abord être créé. Il existe actuellement deux choix pour Azure - standard de Azure et d’Azure sur Chine continentale qui est géré par un partenaire 21Vianet.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

Puis, un doit entrer l’ID d’abonnement Azure que vous trouverez sur le portail Azure - également voir plus bas les moyens d’y parvenir. Par la suite un certificat de gestion Azure doit être téléchargé.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

Dans la nouvelle Azure un portail recherche l’élément « Abonnements » sur le côté gauche. Cliquez dessus pour afficher tous les abonnements actifs pour l’utilisateur.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

Sélectionnez un des abonnements, puis choisissez « Gestion de certificats » explique qu’il sont un nouveau concept à l’aide de « entités de service » pour le nouveau modèle du Gestionnaire de ressources Azure.
Licence d’accès client SAP n’est pas adapté encore de ce nouveau modèle et nécessite toujours le modèle « classique » et le portail Azure ancien pour travailler avec les certificats de gestion.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

Ici, une peut voir l’ancien portail Azure. Le téléchargement d’un certificat de gestion permet de licence d’accès client SAP les autorisations pour créer des machines virtuelles au sein d’un abonnement de client. Sous « Abonnements » onglet le peut rechercher l’ID d’abonnement qui doit être saisi dans le portail SAP licence d’accès client.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

Sous le second onglet, il est alors possible de télécharger le certificat de gestion qui a été téléchargé avant de la licence d’accès client SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

Une petite boîte de dialogue s’affiche pour sélectionner le fichier de certificat téléchargé.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

Une fois que le certificat a été téléchargé la connexion entre la licence d’accès client SAP et le client abonnement Azure peut être testée dans la licence d’accès client SAP. Un petit message qui indique que la connexion est valide doit apparaître.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

Après l’installation d’un compte, un doit sélectionner une solution qui doit être déployée et créer une instance.
Avec le mode « de base », il est vraiment simple. Entrez un nom d’instance, choisissez une région Azure et définir le mot de passe maître de la solution.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

Après un certain temps selon la taille et la complexité de la solution (une estimation est donnée par la licence d’accès client SAP), il est affiché comme « actif » et prêt à l’emploi. Il est très simple.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

En examinant certains détails de la solution peut voir quel type de machines virtuelles ont été déployés. Dans ce cas, il est un seul Azure VM de taille D12 qui a été créé par la licence d’accès client SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

Sur le portail Azure, l’ordinateur virtuel se trouve commençant avec le même nom d’instance qui a été accordé dans la licence d’accès client SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

Il est maintenant possible de se connecter à la solution via le bouton de connexion dans le portail SAP licence d’accès client. La petite boîte de dialogue contient un lien vers un guide de l’utilisateur qui décrit toutes les informations d’identification par défaut pour travailler avec la solution.
[Ici](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) est le lien vers le guide de la solution de l’IDE.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Une autre option consiste à se connecter à la machine virtuelle Windows et de commencer par exemple la GUI SAP préconfigurée.





