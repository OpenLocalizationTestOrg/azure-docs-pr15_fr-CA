<properties
    pageTitle="Fourniture de contenu aux clients | Microsoft Azure"
    description="Cette rubrique fournit une vue d’ensemble de ce qui est impliqué dans la livraison de votre contenu avec Azure Media Services."
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
    ms.date="09/19/2016"
    ms.author="juliako"/>


# <a name="deliver-content-to-customers"></a>Livrer du contenu aux clients
Lorsque vous vous livrez votre contenu de diffusion en continu ou des vidéos à la demande aux clients, votre objectif est de fournir des vidéo de haute qualité à divers périphériques dans des conditions de réseau différents.

Pour atteindre cet objectif, vous pouvez :

- Coder votre flux de données dans un flux vidéo multi-vitesse de transmission (débit adaptive). Il se charge des conditions de qualité et réseau.
- Microsoft Azure Media Services [packaging dynamique](media-services-dynamic-packaging-overview.md) permet de dynamiquement package nouveau votre flux de données dans les différents protocoles. Il se charge de diffusion en continu sur des périphériques différents. Media Services prend en charge la remise de la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG-tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement).

Cet article donne une vue d’ensemble des concepts de livraison de contenu important.

Pour vérifier les problèmes connus, reportez-vous à la section [problèmes connus](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Emballage dynamique

Avec l’emballage dynamique qui fournit des Services de support, vous pouvez livrer votre contenu de vitesse de transmission adaptive MP4 ou diffusion en continu lisse codé en continu des formats pris en charge par les Services de support (MPEG-tiret, TLS, diffusion en continu lisse, HDS) sans avoir à ré-package dans ces formats de transmission en continu. Nous vous recommandons de diffusion de vos contenus avec emballage dynamique.

Pour tirer parti de l’emballage dynamique, vous devez effectuer les opérations suivantes :

- Coder votre fichier mezzanine (source) dans un ensemble de fichiers MP4 de vitesse de transmission adaptive ou les fichiers de diffusion en continu lisse de débit adaptatif.
- Obtenir au moins une unité de transmission en continu à la demande pour le point de terminaison en continu à partir de laquelle vous souhaitez livrer votre contenu. Pour plus d’informations, consultez [comment mettre à l’échelle d’unités réservées de diffusion en continu à la demande](media-services-portal-manage-streaming-endpoints.md).

Avec emballage dynamique, vous stockez et payez les fichiers au format de stockage unique. Media Services pour la génération et fournir la réponse appropriée en fonction de vos demandes.

En plus de fournir l’accès aux fonctions de packaging dynamique, unités réservées de diffusion en continu à la demande vous fournit capacité dédié de sortie qui peut être achetée par incréments de 200 Mbits/s. Par défaut, la diffusion en continu à la demande est configurée dans un modèle d’instance partagée pour le serveur (par exemple, la capacité compute ou sortie) de ressources sont partagés avec tous les autres utilisateurs. Vous pouvez améliorer un débit de transmission en continu à la demande par unités de réservé en continu à la demande d’achat.

Pour plus d’informations, consultez [l’emballage dynamique](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Les filtres et les manifestes dynamiques

Vous pouvez définir des filtres pour vos ressources avec Media Services. Ces filtres sont des règles côté serveur qui aideront vos clients à faire lire une section spécifique d’une vidéo ou spécifier un sous-ensemble de formats audio et vidéo capable de gérer les périphériques de votre client (au lieu de tous les formats associés qui sont associés à l’actif). Ce filtrage par le biais *des manifestes dynamiques* qui sont créés lorsque votre client souhaite pour diffuser une vidéo basée sur un ou plusieurs filtres spécifiés.

Pour plus d’informations, consultez [filtres et manifestes dynamiques](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a>Localisateurs

Pour fournir aux utilisateurs une URL qui peut être utilisée pour transmettre en continu ou télécharger votre contenu, vous devez d’abord publier votre actif en créant un localisateur. Un localisateur fournit un point d’entrée pour accéder aux fichiers contenus dans un élément. Media Services prend en charge deux types de localisateurs :

- Repères de OnDemandOrigin. Ceux-ci sont utilisés pour diffuser les médias (par exemple, MPEG-tiret, TLS ou diffusion en continu lisse) ou téléchargement progressivement les fichiers.
-  Localisateurs de URL de signature (SAS) un accès partagé. Ceux-ci sont utilisés pour télécharger des fichiers multimédias sur votre ordinateur local.

Une *stratégie d’accès* est utilisée pour définir les autorisations (par exemple, lecture, écriture et liste) et la durée pour laquelle un client a accès à une ressource particulière. Notez que l’autorisation de la liste (AccessPermissions.List) ne doit pas être utilisée dans la création d’un localisateur de OrDemandOrigin.

Localisateurs ont des dates d’expiration. Le portail Azure définit une date d’expiration dans le futur de 100 ans pour les repères.

>[AZURE.NOTE] Si vous avez utilisé le portail Azure pour créer des repères avant mars 2015, les localisateurs ont été configurés pour expirer après deux ans.

Pour mettre à jour une date d’expiration sur un repère, utilisez [repos](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou à l’API [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Notez que lorsque vous mettez à jour la date d’expiration d’un localisateur SAS, l’URL change.

Repères ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Vous pouvez attribuer différents droits d’accès à des utilisateurs individuels à l’aide de solutions de gestion des droits numériques (DRM). Pour plus d’informations, consultez [Sécurisation des supports](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Lorsque vous créez un repère, il peut être un délai de 30 secondes à cause de stockage requis et le processus de propagation dans le stockage Azure.


## <a name="adaptive-streaming"></a>Diffusion adaptative

Vitesse de transmission Adaptive technologies permettent aux applications de lecteur vidéo déterminer les conditions de réseau et de sélectionner plusieurs vitesses de transmission. Lors de la communication réseau se dégrade, le client peut sélectionner une vitesse de transmission inférieure pour lecture puisse continuer avec une qualité de vidéo. Comme l’amélioration des conditions réseau, le client peut passer à un débit plus élevé avec amélioration de la qualité vidéo. Azure Media Services prend en charge les technologies de transmission adaptive suivantes : diffusion en continu Live HTTP (TLS), la diffusion en continu lisse, MPEG-tiret et HDS.

Pour fournir aux utilisateurs les URL de diffusion en continu, vous devez d’abord créer un localisateur de OnDemandOrigin. Création du localisateur vous donne le chemin d’accès de base de la ressource qui contient le contenu que vous souhaitez diffuser. Toutefois, pour pouvoir lire ce contenu, vous devez modifier ce chemin d’accès supplémentaire. Pour construire une URL complète vers le fichier de manifeste en continu, vous devez concaténer le valeur de chemin d’accès de l’URL et le manifeste (filename.ism) nom de fichier. Puis ajouter **de manifeste** et un format approprié (si nécessaire) dans le chemin de recherche.

>[AZURE.NOTE]Vous pouvez aussi diffuser votre contenu sur une connexion SSL. Pour ce faire, assurez-vous que votre URL en continu commencent par HTTPS.

Vous pouvez uniquement diffuser sur SSL si le point de terminaison en continu à partir de laquelle vous livrez votre contenu a été créé après le 10 septembre 2014. Si votre URL de diffusion en continu est basés sur les points de terminaison en continu créés après le 10 septembre 2014, l’URL contient « streaming.mediaservices.windows.net ». En continu les URL qui contiennent « origin.mediaservices.windows.net » (l’ancien format) ne supportent pas SSL. Si votre URL est dans l’ancien format et que vous souhaitez être en mesure de diffuser en continu via le protocole SSL, créez un nouveau point de terminaison en continu. URL basés sur le nouveau point de terminaison en continu permet de diffuser votre contenu sur SSL.


## <a name="streaming-url-formats"></a>Diffusion en continu des formats d’URL

### <a name="mpeg-dash-format"></a>Format de MPEG-tiret

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=MPD-Time-CSF)



### <a name="apple-http-live-streaming-hls-v4-format"></a>Format V4 de Apple HTTP Live en continu (TLS)

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-AAPL)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Format de V3 Apple HTTP Live en continu (TLS)

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-AAPL-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Format Apple HTTP Live en continu (TLS) avec filtre audio uniquement

Par défaut, les pistes audio sont inclus dans le TLS manifeste. Ceci est nécessaire pour la certification de Store d’Apple pour réseaux cellulaires. Dans ce cas, si un client ne possède pas suffisamment de bande passante ou est connecté via une connexion de 2G, la lecture passe audio uniquement. Cela permet de conserver le contenu de la diffusion en continu sans mise en mémoire tampon, mais il n’y a aucun signal vidéo. Dans certains scénarios, mise en mémoire tampon de lecteur a priorité sur l’audio uniquement. Si vous souhaitez supprimer la piste audio uniquement, ajoutez **audio uniquement = false** à l’URL.

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-AAPL-v3,audio-Only=false)

Pour plus d’informations, consultez [prise en charge de la Composition dynamique de manifeste et TLS sortie des fonctionnalités supplémentaires](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).


### <a name="smooth-streaming-format"></a>Format de transmission en continu lisse

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Exemple :

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a id="fmp4_v20"></a>Manifeste de 2.0 de diffusion en continu lisse (manifeste ancien)

Par défaut, le format manifeste de diffusion en continu lisse contient la balise répétition (r-balise). Toutefois, certains lecteurs ne supportent pas la balise-r. Clients avec ces lecteurs peuvent utiliser un format qui désactive la balise-r :

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

### <a name="hds-for-adobe-primetimeaccess-licensees-only"></a>HDS (pour Adobe Enforcer/accès titulaires de licence uniquement)

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

## <a name="progressive-download"></a>Téléchargement progressif

Avec un téléchargement progressif, vous pouvez démarrer la lecture de médias avant que l’intégralité du fichier a été téléchargé. Impossible de télécharger progressivement (.ism * ismv, isma, ismt ou ismc) des fichiers.

Pour télécharger progressivement du contenu, utilisez le type de OnDemandOrigin de localisateur. L’exemple suivant affiche l’URL qui est basé sur le type de OnDemandOrigin de localisateur :

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Vous devez décrypter des actifs de stockage crypté que vous voulez transmettre en continu à partir du service d’origine pour un téléchargement progressif.

## <a name="download"></a>Télécharger

Pour télécharger votre contenu sur un périphérique client, vous devez créer un localisateur de SAS. Le localisateur d’associations de sécurité vous permet d’accéder au conteneur de stockage Azure où se trouve votre fichier. Pour générer l’URL de téléchargement, vous devez incorporer le nom du fichier entre l’hôte et de la signature des associations de sécurité.

L’exemple suivant affiche l’URL qui est basé sur le repère SAS :

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Les considérations suivantes s’appliquent :

- Vous devez décrypter des actifs de stockage crypté que vous voulez transmettre en continu à partir du service d’origine pour un téléchargement progressif.
- Un téléchargement qui n’est pas terminée dans les 12 heures échouera.

## <a name="streaming-endpoints"></a>Les points de terminaison en continu

Diffusion en continu d’un point de terminaison représente un service de transmission en continu qui peut diffuser du contenu directement à une application de lecteur client ou à un réseau de diffusion de contenu (CDN) pour la distribution se poursuive. Le flux de sortie à partir d’un service en continu de point de terminaison peut être un flux en direct ou un actif de vidéo à la demande dans votre compte de Services de support. Vous pouvez également contrôler la capacité du service de point de terminaison en continu pour gérer les besoins croissants de la bande passante en réglant les unités réservées. Vous devez allouer au moins une unité réservée pour les applications dans un environnement de production. Pour plus d’informations, consultez [Comment faire évoluer un service de support](media-services-portal-manage-streaming-endpoints.md).

## <a name="known-issues"></a>Problèmes connus

### <a name="changes-to-smooth-streaming-manifest-version"></a>Version du manifeste de modifications apportées à la diffusion en continu lisse

Avant la version de service juillet 2016--lorsque les actifs produits par Media Encoder Standard, Media Encoder Premium Workflow, ou l’encodeur de Media Azure antérieures ont été transmis à l’aide d’emballage dynamique--la diffusion en continu lisse manifeste retourné serait conforme à la version 2.0. Dans la version 2.0, les durées de fragment n’utilisent pas les balises de répéter ce que l'on appelle (« r »). Par exemple :

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Dans la version de service juillet 2016, le manifeste généré de diffusion en continu lisse est conforme à la version 2.2, avec des durées de fragment à l’aide de balises de répétition. Par exemple :

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Parmi les clients hérités de diffusion en continu lisse ne peut pas prendre en charge les balises de répétition et ne pourra pas charger le manifeste. Pour atténuer ce problème, vous pouvez utiliser le paramètre de format de manifeste hérité **(format = v20-fmp4)** ou mettre à jour votre client vers la version la plus récente, qui prend en charge les balises de répétition. Pour plus d’informations, reportez-vous à la section [2.0 de diffusion en continu lisse](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Rubriques connexes

[Mettre à jour les localisateurs de Media Services après laminage clés de stockage](media-services-roll-storage-access-keys.md)
