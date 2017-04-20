<properties 
    pageTitle="Les codecs et les formats de média codeur Premium Workflow | Microsoft Azure" 
    description="Cette rubrique donne une vue d’ensemble des codecs et les formats de média codeur Premium du flux de travail" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erik43" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"    
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-premium-workflow-formats-and-codecs"></a>Les codecs et les formats de média codeur Premium Workflow


>[AZURE.NOTE]Pour les questions de codeur premium, messagerie mepd sur le site Microsoft.com.
>
>Processeur de média Media Encoder Premium Workflow présenté dans cette rubrique n’est pas disponible en Chine. 

Ce document contient une liste de codecs pris en charge par la version de présentation publique de l’encodeur **Media Encoder Premium Workflow** et d’entrée et formats de fichier de sortie.

[Les Codecs et les Formats de Worflow entrée Media Encoder Premium](#input_formats)

[Les Codecs et les Formats de sortie de Worflow Media Encoder Premium](#output_formats)

**Workflow de prime Media Encoder** prend en charge le sous-titrage décrites dans [cette](#closed_captioning) section. 


##<a id="input_formats"></a>Les Codecs et les Formats d’entrée de Media Encoder Premium Workflow

La section suivante répertorie les codecs et formats de fichier qui prend en charge le processeur de ce média comme entrée.

###<a name="input-containerfile-formats"></a>Entrée des Formats de conteneur et de fichiers

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- Flux MPEG-2
- Flux programme MPEG-2
- MPEG-4/MP4
- Windows Media/ASF
- AVI (non compressé 8 bits/10 bits)

###<a name="input-video-codecs"></a>Codecs vidéo d’entrée

- AVC 8/10 bits bits, jusqu'à 4:2:2, y compris les AVCIntra
- Avid DNxHD (dans le format MXF)
- DVCPro/DVCProHD (dans le format MXF)
- JPEG2000
- MPEG-2 (profil 422 et de haut niveau, y compris les variantes de XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)
- MPEG-1
- Windows Media Video/VC-1

###<a name="input-audio-codecs"></a>Codecs Audio d’entrée

- AES (SMPTE 331 M et 302 M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, HE-AAC et AAC-HEv2 ; jusqu'à 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Audio Windows Media
- WAV/PCM
 
##<a id="output_format"></a>Les Codecs et les Formats de sortie de flux de travail Media Codeur Premium

La section suivante répertorie les codecs et formats de fichier pris en charge en tant que sortie de ce processeur de média.

###<a name="output-containerfile-formats"></a>Formats de conteneur et de fichiers de sortie

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM et AS02)
- DPP (y compris AS11)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (non compressé 8 bits/10 bits)
- Format de fichier (PIFF 1.3) diffusion en continu lisse
- MPEG-TS 


###<a name="output-video-codecs"></a>Codecs vidéo de sortie

- AVC (H.264 ; 8 bits ; à Elevé, niveau 5.2 ; 4 K Ultra HD ; Intra de AVC)
- Avid DNxHD (dans le format MXF)
- DVCPro/DVCProHD (dans le format MXF)
- MPEG-2 (profil 422 et de haut niveau, y compris les variantes de XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)
- MPEG-1
- Windows Media Video/VC-1
- Création d’une miniature JPEG

###<a name="output-audio-codecs"></a>Codecs Audio de sortie

- AES (SMPTE 331 M et 302 M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) à 7.1
- AAC (AAC-LC, HE-AAC et AAC-HEv2 ; jusqu'à 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Audio Windows Media

##<a id="closed_captioning"></a>Prise en charge de la labellisation fermée

D’acquisition, le **Workflow de prime Media Encoder** prend en charge :

1. Fichiers de contrôle de code source
1. Fichiers de SMPTE-TT
1. CEA-608/CEA-708 – exécutés en tant que données de l’utilisateur (messages SEI de flux élémentaires H.264, ATSC/53, SCTE20) ou exécutée en tant que données auxiliaires dans des fichiers MXF/GXF
1. Fichiers de sous-titre STL

À la sortie, les options suivantes sont disponibles :

1. 608-CEA à la traduction des CEA-708
1. CEA-608/CEA-708 traverser (incorporés dans des messages SEI de flux élémentaires de H.264, ou exécutée comme auxiliaires de données dans des fichiers MXF)
1. CONTRÔLE DE CODE SOURCE
1. SMPTE Timed Text (à partir de source 608-CEA par la SMPTE RP2052, y compris la création d’un fichier DFXP)
1. Fichier de SRT sous-titre
1. Flux de sous-titres DVB

Remarque : tous les formats de sortie ci-dessus sont pris en charge pour la remise par diffusion en continu dans Azure Media Services.

##<a name="known-issues"></a>Problèmes connus

Si votre vidéo d’entrée ne contient-elle pas de sous-titrage, la sortie actif contient toujours un fichier TTML vide. 


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
