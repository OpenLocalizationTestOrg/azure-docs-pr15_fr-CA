<properties 
    pageTitle="Licence Microsoft® lisse en continu Client Kit de portage" 
    description="Obtenir des informations sur le Kit Microsoft® lisse en continu Client portage de licence." 
    services="media-services" 
    documentationCenter="" 
    authors="xpouyat,vsood" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016"  
    ms.author="xpouyat"/>

#<a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licence Microsoft® lisse en continu Client Kit de portage

##<a name="overview"></a>Vue d’ensemble

Lisse en continu Client portage Kit de Microsoft (**SSPK** en abrégé) est une implémentation de client de diffusion en continu lisse qui est optimisée pour aider les fabricants de périphériques intégrés, câble opérateurs de téléphonie mobile, fournisseurs de services de contenu, fabricants de combiné, éditeurs de logiciels indépendants (ISV) et des fournisseurs de solutions pour créer des produits et services pour la diffusion en continu de contenu adaptatif dans le format de diffusion en continu lisse. SSPK est un périphérique et une plate-forme indépendante mise en œuvre du client de diffusion en continu lisse qui peut être porté par le Licencié à n’importe quel périphérique et une plate-forme. 

Inclus ci-dessous est une architecture de haut niveau et sans heurts de diffusion en continu de portage Kit de IIS est l’implémentation Client de diffusion en continu lisse fournie par Microsoft et inclut toute la logique de base pour la lecture de contenu de diffusion en continu lisse. Ceci est ensuite porté par les partenaires pour un périphérique spécifique ou d’une plate-forme en implémentant les interfaces appropriées. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

##<a name="description"></a>Description

SSPK est concédé sous licence à des conditions qui offrent une excellente valeur. Licence SSPK offre au secteur de :

- Source de Kit de portage de diffusion en continu lisse en C++ 
  - implémente les fonctionnalités de Client de diffusion en continu lisse
  - Ajoute un format lors de l’analyse, analyse heuristique, mise en mémoire tampon à logique, etc..
- API d’application de lecteur 
  - interfaces de programmation pour l’interaction avec une application de lecteur multimédia
- Interface (PAL) de couche d’Abstraction de plate-forme 
  - interfaces de programmation pour l’interaction avec le système d’exploitation (threads, sockets)
- Interface (HAL) de couche d’Abstraction de matériel 
  - interfaces de programmation permettant l’interaction avec le matériel A / décodeurs V (décodage, rendu)
- Digital Rights Management (DRM) Interface 
  - interfaces de programmation pour la gestion des DRM par le biais de la couche de Abstraction DRM (DAL)
  - Kit de portage de Microsoft PlayReady est fourni séparément, mais intègre via cette interface. Pour plus d’informations sur les licences de Microsoft PlayReady Device, cliquez [ici](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
- Exemples d’implémentation 
  - exemple d’implémentation PAL pour Linux
  - exemple d’implémentation de HAL pour GStreamer

##<a name="licensing-options"></a>Options de licence

Microsoft lisse de diffusion en continu de portage Kit Client est mis à disposition licenciés sous deux accords de licence distincts : un pour le développement Client de diffusion en continu lisse intérimaire des produits et une autre pour distribution de fluide en continu Client Final produits aux utilisateurs finaux.
 
- Pour les fabricants de chipset, intégrateurs de systèmes ou les éditeurs de logiciels indépendants (ISV) qui ont besoin d’un kit pour développer des produits de version préliminaire de portage de code de source, une lisse en continu Client portage Kit Microsoft **Intérimaire produit licence** doit être exécutée.
- Pour les fabricants ou les éditeurs de logiciels qui ont besoin de droits de distribution pour lisse en continu Client Final des produits aux utilisateurs finaux, le Microsoft lisse en continu Client portage du Kit **Licence de produit Final** doit être exécutée.

###<a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Smooth Streaming Client portage intérimaire de Kit produit licence est Microsoft

Sous cette licence, Microsoft offre un bon flux portage Kit Client et les droits de propriété intellectuelle nécessaire pour développer et distribuer le Client de diffusion en continu lisse intérimaire des produits à d’autres licenciés de périphérique lisse de diffusion en continu de portage Kit Client qui distribuent lisse en continu Client produits finaux.

####<a name="fee-structure"></a>Structure de frais

Une redevance de licence de 50 000 dollars US fournit l’accès à la lisse de diffusion en continu de portage Kit Client. 

###<a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Smooth Streaming Client portage de licence du produit Final du Kit est Microsoft

Sous cette licence, Microsoft vous propose tous les droits de propriété intellectuelle nécessaire pour recevoir le Client de diffusion en continu lisse intérimaire des produits à partir d’autres licenciés sans heurts de diffusion en continu de portage Kit Client et distribuer marque lisse en continu Client Final produits aux utilisateurs finaux.

####<a name="fee-structure"></a>Structure de frais

Le bon produit Final Client de diffusion en continu est proposé sous un modèle soumis à redevance que sous :

- 0,10 $ par la mise en oeuvre du dispositif livré
- La redevance est plafonnée à 50 000 dollars chaque année
- Aucune redevance pour les 10 000 premières implémentations de périphérique chaque année 

##<a name="licensing-procedure-and-sspk-access"></a>Procédure de licences et de SSPK d’accès

Veuillez envoyer un e-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) pour toutes les requêtes de licences.

Le [portail de SSPK Distribution](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) est accessible à des titulaires de licence provisoire.

Licenciés intermédiaire et Final SSPK peuvent soumettre vos questions techniques à [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

##<a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Smooth Streaming détenteurs de licences accord Client produit intermédiaire est Microsoft

- Solutions d’entreprise adroit, Inc
- Association de sécurité de diffusion numérique avancé
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Alticast Corporation
- Amazon Digital Services, Inc.
- AVC multimédia Software Co., Ltd.
- Cavium, Inc.
- Société d’achat de EchoStar
- Enseo, Inc.
- Fluendo Amérique du Sud
- HANDAN BroadInfoCom Co., Ltd.
- Infomir GMBH
- Irdeto USA Inc.
- Liberty Global Services BV
- Inc. de MediaTek
- MStar co., Ltd
- Nintendo Co., Ltd.
- OpenTV, Inc.
- Safran numérique limitée
- Électrique de Changhong du Sichuan Co., Ltd
- SoftAtHome
- Sony Corporation
- Tatung Technology Inc.
- TCL Technoly Electronics (Huizhou) Co., Ltd.
- Vestel Elektronik Sanayi ve Ticaret A.S.
- VisualOn, Inc.
- ZTE Corporation

##<a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Smooth Streaming détenteurs de licences accord Client produit Final est Microsoft

- Association de sécurité de diffusion numérique avancé
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Amazon Digital Services, Inc.
- AmTRAN technologies Co., Ltd.
- Arcadyan Technology Corporation
- SAN ELEKTRONİK DE ATMACA. VE, TİC. A.Ş
- British Sky diffusion limitée
- CastPal Technology Inc., Shenzhen
- Intégrer Compal Electronics, Inc.
- Dongguan numérique AV technologie corp., Ltd.
- Société d’achat de EchoStar
- Enseo, Inc.
- Filmflex films limitée
- Fluendo Amérique du Sud
- Gibson Innovations limitée
- Haier informations Applicantion S.R.L
- HANDAN BroadInfoCom Co., Ltd.
- Homecast Co., Ltd
- HON Hai Precision industrie Co., Ltd.
- Infomir GMBH
- Kaonmedia Co., Ltd.
- KDDI Corporation
- Nintendo Co., Ltd.
- Association de sécurité orange
- Safran numérique limitée
- Sagemcom large bande SAS
- Shenzhen Coship Electronics CO., LTD
- Shenzhen Jiuzhou électrique Co., Ltd
- La technologie numérique Shenzhen Skyworth Co., Ltd
- Du Sichuan Changhong électrique Co., Ltd.
- Skardin, Industrial corp.
- Ciel Deutschland Fernsehen GmbH & Co. KG
- SmarDTV Amérique du Sud
- SoftAtHome
- Sony Corporation
- Marketing d’outre-mer TCL (Macao Commercial Offshore) limités
- Technologies de livraison technicolor, SAS
- Ltd. de Global Tongfang
- Style de vie Toshiba produits & Services Corporation
- Universal Media Corporation /Slovakia/ s.r.o.
- VIZIO, Inc.
- Wistron Corporation
- ZTE Corporation

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
