<properties 
    pageTitle="Développer des applications de lecteur vidéo" 
    description="La rubrique fournit des liens vers les infrastructures de lecteur et les plug-ins que vous pouvez utiliser pour développer vos propres applications clientes qui peuvent utiliser des médias en flux continu à partir de Media Services." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="develop-video-player-applications"></a>Développer des applications de lecteur vidéo

##<a name="overview"></a>Vue d’ensemble

Azure Media Services fournit les outils nécessaires pour créer des applications de lecteur client riches et dynamiques pour la plupart des plates-formes, y compris : e/s de périphériques, des appareils Android, Windows, Windows Phone, Xbox et décodeurs boîtes. Cette rubrique fournit également des liens vers les kits de développement logiciel et les infrastructures de lecteur que vous pouvez utiliser pour développer vos propres applications clientes qui peuvent utiliser des médias en flux continu à partir d’Azure Media Services.

##<a name="azure-media-player"></a>Lecteur Media Azure

[Azure Media Player](http://aka.ms/ampinfo) est un lecteur vidéo web conçu pour lire un contenu multimédia à partir de Microsoft Azure Media Services sur une grande variété de navigateurs et de périphériques. Azure Media Player utilise les normes industrielles, telles que HTML5 Media sources Extensions (MSE) et Extensions de médias crypté (EME) pour fournir une expérience de diffusion adaptative enrichie. Lorsque ces normes ne sont pas disponibles sur un périphérique ou dans un navigateur, Azure Media Player utilise Flash et Silverlight en tant que technologie de secours. Quelle que soit la technologie de lecture utilisée, les développeurs auront une interface unifiée de JavaScript pour accéder aux API. Ainsi, pour le contenu offert par Azure Media Services pour être lus sur un large éventail de périphériques et de navigateurs sans aucun effort supplémentaire.

Microsoft Azure Media Services permet d’être pris en charge avec tiret, diffusion en continu lisse et TLS, formats de transmission en continu du contenu à lire le contenu. Azure Media Player prend en compte ces différents formats et lit automatiquement le lien meilleur selon les fonctionnalités du navigateur/platform. Microsoft Azure Media Services permet également de cryptage dynamique des actifs avec chiffrement PlayReady ou cryptage d’enveloppe AES-128 bits. Azure Media Player permet le décryptage de PlayReady et AES-128 bits contenu chiffré lorsqu’il est correctement configuré. 

Pour plus d’informations :

- [Lecteur Media Azure](http://aka.ms/ampinfo)
- [Documentation du lecteur Media Azure](http://aka.ms/ampdocs) 
- [Azure Media Player l’obtention lancé Blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
- [Inscrivez-vous pour rester à jour avec les toutes dernières d’Azure Media Player](http://aka.ms/ampsignup)
- [Ajouter de nouvelles requêtes de fonctionnalités, des idées et des commentaires](http://aka.ms/ampuservoice ) 


##<a name="other-tools-for-creating-player-applications"></a>Autres outils de création d’Applications de lecteur

Vous pouvez également utiliser les kits de développement logiciel suivants :

- [Lisser les flux Client SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Application du Windows Store en continu lisse](media-services-build-smooth-streaming-apps.md)
- [Plate-forme de support de Microsoft : Framework de lecteur](http://playerframework.codeplex.com/) 
- [HTML5 Documentation du Framework lecteur](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Smooth Streaming plug-in pour OSMF est Microsoft](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Licence Microsoft® lisse en continu Client Kit de portage](http://aka.ms/sspk) 
- [Développement d’Application vidéo XBOX](http://xbox.create.msdn.com/) 
 

##<a name="advertising"></a>Publicité

Azure Media Services fournit la prise en charge pour l’insertion de publicité par l’intermédiaire de la plate-forme Windows Media : infrastructures de lecteur. Infrastructures de lecteur avec prise en charge d’Active Directory sont disponibles pour les périphériques Windows 8, Silverlight, Windows Phone 8 et e/s. Chaque structure de lecteur contient un exemple de code qui montre comment implémenter une application de lecteur. Il existe trois différents types de publicités que vous pouvez insérer dans vos médias :

Linéaire – annonces plein cadre suspendre la vidéo principale

Non linéaire : annonces de superposition sont affichées, comme la lecture de la vidéo principale, généralement un logo ou une autre image statique placé dans le lecteur

Guide – les annonces qui s’affichent en dehors du lecteur

Les annonces peuvent être placés à n’importe quel point dans la chronologie de la vidéo principale. Vous devez indiquer le lecteur quand jouer à la publicité et les publicités à lire. Pour cela, à l’aide d’un ensemble de fichiers XML standard : modèle de Service publicité vidéo (VAST), Digital Video plusieurs annonces sélection (VMAP), modèle de séquençage abstraite Media (mat) et Digital Video Player Ad Interface définition (VPAID). Fichiers de grandes spécifient les annonces à afficher. Fichiers VMAP spécifier à quel moment jouer différentes annonces et contient du code XML de grande. Les fichiers de mât sont un autre moyen d’annonces de séquence peut comporter elle aussi vaste XML. Les fichiers VPAID définissent une interface entre le lecteur vidéo et l’annonce ou le serveur Active Directory. Pour plus d’informations, consultez [Insertion de publicités](https://msdn.microsoft.com/library/dn387398.aspx).

Pour plus d’informations sur le support de sous-titrage et les annonces fermé dans Live en continu de vidéos, consultez [prise en charge de sous-titrage et de normes d’Insertion de publicité](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi

[L’incorporation d’une vidéo de diffusion adaptative MPEG-tiret dans une Application HTML5 avec DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Référentiel de dash.js de GitHub](https://github.com/Dash-Industry-Forum/dash.js)
 
