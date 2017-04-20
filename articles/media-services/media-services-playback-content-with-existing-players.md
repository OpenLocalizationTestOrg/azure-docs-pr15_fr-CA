<properties 
    pageTitle="Lecture du contenu | Microsoft Azure" 
    description="Cette rubrique répertorie les lecteurs existants que vous pouvez utiliser pour la lecture de votre contenu." 
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
    ms.date="10/12/2016" 
    ms.author="juliako"/>


#<a name="playing-your-content-with-existing-players"></a>Lecture du contenu avec les lecteurs existants

Azure Media Services prend en charge de nombreux formats en continu populaires, telles que la diffusion en continu lisse HTTP Live en continu et MPEG-tiret. Cette rubrique renvoie à des lecteurs existants que vous pouvez utiliser pour tester votre flux de données.

>[AZURE.NOTE]Play emballés de manière dynamique ou contenu crypté de manière dynamique, veillez à obtenir au moins une unité de transmission en continu pour le point de terminaison en continu à partir de laquelle vous souhaitez livrer votre contenu. Pour plus d’informations sur la mise à l’échelle d’unités de transmission en continu, consultez : [comment mettre à l’échelle d’unités de transmission en continu](media-services-portal-manage-streaming-endpoints.md).

### <a name="the-azure-portal-media-services-content-player"></a>Le lecteur de contenu Media Services portail Azure

Le portail **Azure** fournit un lecteur de contenu que vous pouvez utiliser pour tester votre vidéo.

Cliquez sur la vidéo souhaitée (Vérifiez qu’il a été [publié](media-services-portal-publish.md)), puis cliquez sur le bouton **lecture** dans le bas du portail.

Certaines considérations s’appliquent :

- Le **Lecteur de contenu MEDIA SERVICES** est lu à partir de la valeur par défaut de diffusion en continu de point de terminaison. Si vous souhaitez lire à partir d’un point de terminaison de diffusion par défaut, utilisez un autre lecteur. Par exemple, [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]

###<a name="azure-media-player"></a>Lecteur Media Azure

Utilisez [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) pour la lecture (clair ou non) de votre contenu dans un des formats suivants :

- Diffusion en continu lisse
- TIRET DE MPEG
- TLS
- MP4 progressif


###<a name="flash-player"></a>Flash Player

####<a name="aes-encrypted-with-token"></a>Chiffrement AES avec jeton

[http://aestoken.azurewebsites.NET](http://aestoken.azurewebsites.net)

###<a name="silverlight-players"></a>Lecteurs Silverlight

####<a name="monitoring"></a>Surveillance

[http://SMF.cloudapp.NET/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####<a name="playready-with-token"></a>PlayReady avec jeton

[http://sltoken.azurewebsites.NET](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Lecteurs de tiret

[http://dashplayer.azurewebsites.NET](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###<a name="other"></a>Autres

Pour tester l’URL TLS vous permet également de :

- **Safari** sur un appareil iOS ou
- **le lecteur 3ivx TLS** sur Windows.

##<a name="developing-video-players"></a>Développement de lecteurs vidéo

Pour plus d’informations sur la façon de développer vos propres lecteurs, voir [développement des lecteurs vidéo](media-services-develop-video-players.md)




##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
