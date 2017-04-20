<properties 
    pageTitle="Les codecs et les formats Standard de codeur multimédia" 
    description="Cette rubrique fournit une vue d’ensemble des codecs et les formats Standard de Media Encoder." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016"
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-standard-formats-and-codecs"></a>Les codecs et les Formats Standard du codeur multimédia


Ce document contient une liste des formats de fichier d’exportation que vous pouvez utiliser avec Media Encoder Standard plus commun d’importation.


##<a name="input-containerfile-formats"></a>Entrée des Formats de conteneur et de fichiers

Formats de fichier (extension)|Prise en charge
---|---|---|---
FLV (avec le codec H.264 et AAC) (.flv)          |Oui 
MXF (.mxf)                  |Oui 
GXF (.gxf)                  |Oui 
MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg)   |Oui 
Vidéo Windows Media (WMV) / ASF (.wmv, .asf) |Oui 
AVI (non compressé 8 bits/10 bits) (.avi)|Oui 
MP4 (.mp4, .m4a, .m4v) / ISMV (.isma, .ismv)|Oui 
[Recording(DVR-MS) vidéo de Microsoft Photo](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Oui 
Matroska/WebM (.mkv)        |Oui 
WAVE/WAV (.wav) |Oui 
QuickTime (.mov) |Oui

>[AZURE.NOTE] Ci-dessus est une liste des extensions de fichier plus couramment rencontrés. Media Encoder Standard prend en charge beaucoup d’autres (par exemple : .m2ts, .mpeg2video, .qt). Si vous essayez de coder un fichier et que vous obtenez un message d’erreur sur le format n’est ne pas pris en charge, veuillez fournir un commentaire [ici](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).

###<a name="audio-formats-in-input-containers"></a>Formats audio dans des conteneurs d’entrée 

Media Encoder Standard prend en charge transportant les formats audio suivants dans des conteneurs d’entrée :

- Qui ont des pistes audio avec entrelacement stéréo ou 5.1 exemples de fichiers MXF, GXF et QuickTime

ou

- Fichiers MXF, GXF et QuickTime où l’audio est exécutée en tant que pistes distinctes de PCM, mais le mappage de canal (à stéréo ou 5.1) peut être déduit à partir des métadonnées de fichiers

Remarque Cette prise en charge pour correspondance fournie par l’utilisateur/explicite de canal sera fournie dans un avenir proche.


##<a name="input-video-codecs"></a>Codecs vidéo d’entrée

Codecs vidéo d’entrée|Prise en charge
---|---|---|---
AVC 8/10 bits bits, jusqu'à 4:2:2, y compris les AVCIntra   |8 bits 4:2:0 et 4:2:2 
Avid DNxHD (dans le format MXF)                                 |Oui 
DVCPro/DVCProHD (dans le format MXF)                            |Oui 
Vidéo numérique (DV) (dans les fichiers AVI)                   |Oui
JPEG 2000                                           |Oui 
MPEG-2 (profil 422 et de haut niveau, y compris les variantes de XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|Profil jusqu'à 422 
MPEG-1                                              |Oui 
VC-1/WMV9                                           |Oui 
Canopus HQ/HQX                                      |N° 
MPEG-4 partie 2                                       |Oui 
[Theora](https://en.wikipedia.org/wiki/Theora)      |Oui 
YUV420 non compressé ou mezzanine                   |Oui
Apple ProRes 422                                    |Oui
Apple ProRes 422 LT |Oui
Apple ProRes 422 HQ |Oui
Apple ProRes Proxy|Oui
Apple ProRes 4444 |Oui
Apple ProRes 4444 XQ |Oui



##<a name="input-audio-codecs"></a>Codecs Audio d’entrée

Codecs Audio d’entrée|Prise en charge
---|---|---|---
AAC (AAC-LC, HE-AAC et AAC-HEv2 ; jusqu'à 5.1)|Oui 
MPEG Layer 2|Oui 
MP3 (MPEG-1 Audio Layer 3)|Oui 
Audio Windows Media|Oui 
WAV/PCM|Oui 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Oui 
[Opus](http://go.microsoft.com/fwlink/?LinkId=822667) |Oui 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Oui 
AMR (adaptive taux multiples)|Oui
AES (SMPTE 331 M et 302 M, AES3-2003)        |N° 
Dolby® E                                    |N° 
Dolby® Digital (AC3)                        |N° 
Dolby® numérique Plus (E-AC3)                 |N° 


##<a name="output-formats-and-codecs"></a>Les codecs et les Formats de sortie

Le tableau suivant répertorie les codecs et formats de fichier pris en charge pour l’exportation.


Format de fichier|Codec vidéo|Codec audio
---|---|---
MP4 <br/><br/>(y compris les conteneurs multi-bitrate MP4) |H.264 (haute, principal et les profils de base)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (haute, principal et les profils de base)|AAC-LC, HE-AAC v1, HE-AAC v2 



##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi

[Encodage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md)

[Comment coder avec Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)
