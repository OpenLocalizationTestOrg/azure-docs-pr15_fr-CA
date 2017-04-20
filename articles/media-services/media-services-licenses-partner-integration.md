<properties 
    pageTitle="L’utilisation de partenaires pour fournir des licences de Widevine pour Azure Media Services | Microsoft Azure" 
    description="Cet article décrit comment vous pouvez utiliser Azure Media Services (AMS) pour fournir un flux qui est chiffré dynamiquement par AMS PlayReady et Widevine DRMs. La licence PlayReady provient de serveur de licences PlayReady des Services de support et Widevine licence est remis par le serveur de licences de castLabs." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>L’utilisation de partenaires pour fournir des licences de Widevine pour Azure Media Services

##<a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services vous permet de livrer MPEG-tiret protégé par DRM Widevine, qui est crypté par la spécification de chiffrement commune (CENC).

À partir de Media Services .NET SDK version 3.5.2, Media Services vous permet de configurer le modèle de licence de Widevine et d’obtenir des licences de Widevine. Vous pouvez également utiliser les partenaires AMS suivants pour vous aider à obtenir les licences de Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##<a name="castlabs"></a>castLabs

Vous pouvez utiliser [castLabs](http://castlabs.com/company/partners/azure/) pour fournir des licences de Widevine. Pour plus d’informations, reportez-vous à la section [castLabs utilisation de fournir des licences DRM pour Azure Media Services](media-services-castlabs-integration.md)

##<a name="axinom"></a>Axinom

Vous pouvez utiliser [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) pour fournir des licences de Widevine. Pour plus d’informations, consultez [L’aide de Axinom pour fournir des licences DRM pour Azure Media Services](media-services-axinom-integration.md)


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi

[En utilisant le cryptage commun dynamique PlayReady et/ou Widevine](media-services-protect-with-drm.md)

[Blog du Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

