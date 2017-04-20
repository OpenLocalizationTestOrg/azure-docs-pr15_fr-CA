<properties 
    pageTitle="Vue d’ensemble et comparaison d’Azure sur codeurs de media à la demande | Microsoft Azure" 
    description="Cette rubrique fournit une vue d’ensemble et la comparaison de Azure sur codeurs de media à la demande." 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>

#<a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Vue d’ensemble et comparaison d’Azure sur codeurs de media à la demande

##<a name="encoding-overview"></a>Vue d’ensemble du codage

Azure Media Services fournit plusieurs options pour le codage de média dans le nuage.

Lors du démarrage d’avec les Services de support, il est important de comprendre la différence entre les formats de fichier et de codecs.
Les codecs sont des logiciels qui implémente les algorithmes de compression/décompression que les formats de fichier sont des conteneurs qui renferment les données vidéo compressées.

Media Services fournit l’emballage dynamique qui vous permet de remettre votre contenu de vitesse de transmission adaptive MP4 ou diffusion en continu lisse codé en continu des formats pris en charge par les Services de support (MPEG tiret, TLS, diffusion en continu lisse, HDS) sans avoir à ré-package dans ces formats de transmission en continu.

Pour tirer parti de [l’emballage dynamique](media-services-dynamic-packaging-overview.md), vous devez effectuer les opérations suivantes :

- Coder votre fichier mezzanine (source) dans un ensemble de fichiers de vitesse de transmission adaptive MP4 ou les fichiers de diffusion en continu lisse adaptive de vitesse de transmission (les étapes de codage sont expliqués plus loin dans ce didacticiel).
- Obtenir au moins une unité de transmission en continu à la demande pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu. Pour plus d’informations, consultez [comment à la demande de diffusion en continu réservé unités de l’échelle](media-services-portal-manage-streaming-endpoints.md).

Media Services prend en charge les éléments suivants sur les encodeurs à la demande qui sont décrits dans cet article :

- [Standard de codage de média](media-services-encode-asset.md#media-encoder-standard)
- [Flux de travail Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Cet article donne une vue d’ensemble de la demande codeurs media et fournit des liens vers des articles qui donnent des informations plus détaillées. La rubrique compare également les encodeurs.

Notez que par défaut chaque compte de Services de support peut avoir une tâche de codage active à la fois. Vous pouvez réserver des unités de codage qui vous permettent d’avoir plusieurs tâches de codage qui s’exécutent simultanément, un pour chaque unité réservée de codage que vous achetez. Pour plus d’informations, reportez-vous à la section [mise à l’échelle des unités de codage](media-services-scale-media-processing-overview.md).

##<a name="media-encoder-standard"></a>Standard de codage de média

###<a name="how-to-use"></a>Comment utiliser

[Comment coder avec Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

###<a name="formats"></a>Formats

[Les codecs et les formats](media-services-media-encoder-standard-formats.md)

###<a name="presets"></a>Paramètres prédéfinis

Media Encoder Standard est configuré à l’aide d’un de l’encodeur prédéfinis décrits [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Métadonnées d’entrée et de sortie

Les métadonnées d’entrée de codeurs sont décrite [ici](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Les métadonnées de sortie de codeurs sont décrite [ici](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###<a name="generate-thumbnails"></a>Générer des miniatures

Pour plus d’informations, consultez [comment générer des miniatures à l’aide de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###<a name="trim-videos-clipping"></a>Ajuster les vidéos (découpage)

Pour plus d’informations, consultez [comment supprimer des vidéos à l’aide de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###<a name="create-overlays"></a>Créer des calques

Pour plus d’informations, consultez [comment créer des calques à l’aide de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#overlay).

###<a name="see-also"></a>Voir aussi

[Le blog de Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##<a name="media-encoder-premium-workflow"></a>Flux de travail Media Encoder Premium

###<a name="overview"></a>Vue d’ensemble

[Présentation de prime codage dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###<a name="how-to-use"></a>Comment utiliser

Workflow de prime Media Encoder est configuré à l’aide de flux de travail complexes. Fichiers de flux de travail peuvent être créés et mis à jour avec l’outil [Concepteur de flux de travail](media-services-workflow-designer.md) .

[L’utilisation de prime codage dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###<a name="known-issues"></a>Problèmes connus

Si votre vidéo d’entrée ne contient-elle pas de sous-titrage, la sortie actif contient toujours un fichier TTML vide. 


##<a id="compare_encoders"></a>Comparer des codeurs

###<a id="billing"></a>Compteur de facturation utilisé par chaque codeur

Nom du processeur|La tarification applicable|Notes
---|---|---
**Standard de codage de média** |CODEUR|Tâches de codage sera débitée en fonction de la taille de la sortie actif, dans Go, à la vitesse spécifiée [ici][1], sous la colonne de l’encodeur.
**Flux de travail Media Encoder Premium** |CODEUR DE PRIME|Tâches de codage sera débitée en fonction de la taille de la sortie actif, dans Go, à la vitesse spécifiée [ici][1], sous la colonne de l’encodeur de la prime.


Cette section compare les fonctionnalités de codage de **Media Encoder Standard** et **Premium du flux de travail codeur multimédia**.


###<a name="input-containerfile-formats"></a>Entrée des Formats de conteneur et de fichiers

Entrée des Formats de conteneur et de fichiers|Standard de codage de média|Flux de travail Media Encoder Premium
---|---|---
Adobe® Flash® F4V           |Oui|Oui
MXF/SMPTE 377M              |Oui|Oui
GXF                         |Oui|Oui
Flux MPEG-2    |Oui|Oui
Flux programme MPEG-2      |Oui|Oui
MPEG-4/MP4                  |Oui|Oui
Windows Media/ASF           |Oui|Oui
AVI (non compressé 8 bits/10 bits)|Oui|Oui
3GPP/3GPP2                  |Oui|N°
Format de fichier (PIFF 1.3) diffusion en continu lisse|Oui|N°
[Recording(DVR-MS) vidéo de Microsoft Photo](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Oui|N°
Matroska/WebM               |Oui|N°
QuickTime (.mov) |Oui|N°

###<a name="input-video-codecs"></a>Codecs vidéo d’entrée

Codecs vidéo d’entrée|Standard de codage de média|Flux de travail Media Encoder Premium
---|---|---
AVC 8/10 bits bits, jusqu'à 4:2:2, y compris les AVCIntra   |8 bits 4:2:0 et 4:2:2|Oui
Avid DNxHD (dans le format MXF)                                 |Oui|Oui
DVCPro/DVCProHD (dans le format MXF)                            |Oui|Oui
JPEG2000                                            |Oui|Oui
MPEG-2 (profil 422 et de haut niveau, y compris les variantes de XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|Profil jusqu'à 422|Oui
MPEG-1                                              |Oui|Oui
Windows Media Video/VC-1                            |Oui|Oui
Canopus HQ/HQX                                      |N°|N°
MPEG-4 partie 2                                       |Oui|N°
[Theora](https://en.wikipedia.org/wiki/Theora)      |Oui|N°
Apple ProRes 422    |Oui|N°
Apple ProRes 422 LT |Oui|N°
Apple ProRes 422 HQ |Oui|N°
Apple ProRes Proxy|Oui|N°
Apple ProRes 4444 |Oui|N°
Apple ProRes 4444 XQ |Oui|N°

###<a name="input-audio-codecs"></a>Codecs Audio d’entrée

Codecs Audio d’entrée|Standard de codage de média|Flux de travail Media Encoder Premium
---|---|---
AES (SMPTE 331 M et 302 M, AES3-2003)        |N°|Oui
Dolby® E                                    |N°|Oui
Dolby® Digital (AC3)                        |N°|Oui
Dolby® numérique Plus (E-AC3)                 |N°|Oui
AAC (AAC-LC, HE-AAC et AAC-HEv2 ; jusqu'à 5.1)|Oui|Oui
MPEG Layer 2|Oui|Oui
MP3 (MPEG-1 Audio Layer 3)|Oui|Oui
Audio Windows Media|Oui|Oui
WAV/PCM|Oui|Oui
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Oui|N°
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format)) |Oui|N°
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Oui|N°


###<a name="output-containerfile-formats"></a>Formats de conteneur et de fichiers de sortie

Formats de conteneur et de fichiers de sortie|Standard de codage de média|Flux de travail Media Encoder Premium
---|---|---
Adobe® Flash® F4V|N°|Oui
MXF (OP1a, XDCAM et AS02)|N°|Oui
DPP (y compris AS11)|N°|Oui
GXF|N°|Oui
MPEG-4/MP4|Oui|Oui
MPEG-TS|Oui|Oui
Windows Media/ASF|N°|Oui
AVI (non compressé 8 bits/10 bits)|N°|Oui
Format de fichier (PIFF 1.3) diffusion en continu lisse|N°|Oui

###<a name="output-video-codecs"></a>Codecs vidéo de sortie

Codecs vidéo de sortie|Standard de codage de média|Flux de travail Media Encoder Premium
---|---|---
AVC (H.264 ; 8 bits ; à Elevé, niveau 5.2 ; 4 K Ultra HD ; Intra de AVC)|Seuls 8 bits 4:2:0|Oui
Avid DNxHD (dans le format MXF)|N°|Oui
MPEG-2 (profil 422 et de haut niveau, y compris les variantes de XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|N°|Oui
MPEG-1|N°|Oui
Windows Media Video/VC-1|N°|Oui
Création d’une miniature JPEG|N°|Oui

###<a name="output-audio-codecs"></a>Codecs Audio de sortie

Codecs Audio de sortie|Standard de codage de média|Flux de travail Media Encoder Premium
---|---|---
AES (SMPTE 331 M et 302 M, AES3-2003)|N°|Oui
Dolby® Digital (AC3)|N°|Oui
Dolby® Digital Plus (E-AC3) à 7.1|N°|Oui
AAC (AAC-LC, HE-AAC et AAC-HEv2 ; jusqu'à 5.1)|Oui|Oui
MPEG Layer 2|N°|Oui
MP3 (MPEG-1 Audio Layer 3)|N°|Oui
Audio Windows Media|N°|Oui


##<a name="error-codes"></a>Codes d’erreur  

Le tableau suivant répertorie les codes d’erreur qui peuvent être retournés dans le cas où une erreur est survenue lors de l’exécution de tâches de codage.  Pour obtenir les détails de l’erreur dans votre code .NET, utilisez la classe [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . Pour obtenir les détails de l’erreur dans votre code reste, utilisez l’API REST de [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) .

ErrorDetail.Code|Causes possibles de l’erreur
-----|-----------------------
Inconnu| Erreur inconnue lors de l’exécution de la tâche
ErrorDownloadingInputAssetMalformedContent|Catégorie d’erreurs qui couvre les erreurs dans le téléchargement actif d’entrée tels que les noms de fichier incorrect, aucun fichier de longueur incorrecte met en forme et ainsi de suite.
ErrorDownloadingInputAssetServiceFailure|Catégorie d’erreurs qui aborde les problèmes du côté service - pour les exemples d’erreurs réseau ou de stockage lors du téléchargement.
ErrorParsingConfiguration|Catégorie d’erreurs de la tâche où <see cref="MediaTask.PrivateData"/> (configuration) n’est pas valide, par exemple la configuration n’est pas un système valide prédéfini ou il contient du code XML non valide.
ErrorExecutingTaskMalformedContent|Catégorie d’erreurs lors de l’exécution de la tâche où les problèmes dans les fichiers de média d’entrée provoquent l’échec.
ErrorExecutingTaskUnsupportedFormat|Catégorie d’erreurs dans laquelle le processeur de média ne peut pas traiter les fichiers fournis - format de média non pris en charge ou ne correspond pas à la Configuration. Par exemple, essayez de générer une sortie audio uniquement d’une immobilisation qui possède une seule carte vidéo
ErrorProcessingTask|Catégorie d’autres erreurs que le processeur média rencontre pendant le traitement de la tâche qui ne sont pas liés au contenu.
ErrorUploadingOutputAsset|Catégorie d’erreurs lors du téléchargement de la ressource de sortie
ErrorCancelingTask|Catégorie d’erreurs pour couvrir les échecs lors de la tentative d’annulation de la tâche
TransientError|Catégorie d’erreurs pour couvrir des problèmes temporaires (par ex. problèmes réseau temporaires avec le stockage Azure)


Pour obtenir de l’aide auprès de l’équipe **Services de support** , ouvrir un [ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-articles"></a>Articles connexes

- [Exécuter des tâches de codage avancées en personnalisant les préconfigurations Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
- [Quotas et Limitations](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
