<properties 
    pageTitle="Les filtres et les manifestes dynamiques | Microsoft Azure" 
    description="Cette rubrique décrit comment créer des filtres pour votre client peut utiliser les sections spécifiques de flux d’un flux. Media Services crée les manifestes dynamiques pour effectuer l’archivage de cette diffusion sélective." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="cenkdin;juliako"/>

# <a name="filters-and-dynamic-manifests"></a>Les filtres et les manifestes dynamiques

À partir de la version 2.11, Media Services vous permet de définir des filtres pour vos ressources. Ces filtres sont des règles côté serveur qui permettront à vos clients de faire des choses comme : lecture uniquement une section d’une vidéo (au lieu de la vidéo entière), ou de spécifier uniquement un sous-ensemble de formats audio et vidéo capable de gérer les périphériques de votre client (au lieu de tous les formats associés qui sont associés à l’actif). Ce filtrage de vos ressources est archivé via s **Manifeste dynamiques**qui sont créés à la demande de votre client pour diffuser une vidéo en fonction des filtres spécifiés.

Traite des scénarios courants dans lesquels les rubriques à l’aide de filtres serait très utile à vos clients et les liens vers des rubriques qui montrent comment créer par programme des filtres (actuellement, vous pouvez créer les filtres avec les API reste uniquement).

##<a name="overview"></a>Vue d’ensemble

Lors de la remise de votre contenu aux clients (diffusion en continu d’événements en direct ou vidéo à la demande) votre objectif est de fournir une vidéo de haute qualité à divers périphériques dans des conditions de réseau différents. Pour atteindre ce objectif suivantes :

- encoder votre flux dans flux de vidéo multi-vitesse ([vitesse de transmission adaptive](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (cela s’occupera de conditions de qualité et de réseau) et 
- Media Services [Packaging dynamique](media-services-dynamic-packaging-overview.md) permet de dynamiquement package nouveau votre flux de données dans les différents protocoles (cela s’occupera de diffusion en continu sur des périphériques différents). Media Services prend en charge la remise de la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement). 

###<a name="manifest-files"></a>Fichiers manifeste 

Lorsque vous codez une immobilisation pour la diffusion en continu de la vitesse de transmission adaptative, un fichier **manifeste** (sélection) est créé (le fichier est basé sur le XML ou texte). Le fichier de **manifeste** inclut la diffusion en continu des métadonnées telles que : suivre le type (audio, vidéo ou texte), suivi du nom, heure de début et de fin, la vitesse de transmission (qualités), langages de piste, la fenêtre de présentation (fenêtre coulissante des durée fixe), un codec vidéo (FourCC). Il indique également le lecteur pour récupérer le fragment suivant en fournissant des informations sur les fragments vidéo lisibles suivant disponibles et leur emplacement. Fragments (ou segments) sont les « segments » réelles d’un contenu vidéo.


Voici un exemple d’un fichier manifeste : 

    
    <?xml version="1.0" encoding="UTF-8"?>  
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">
    
    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />
    
    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    </SmoothStreamingMedia>
    
###<a name="dynamic-manifests"></a>Manifestes dynamiques

Il existe des [scénarios](media-services-dynamic-manifest-overview.md#scenarios) lorsque votre client a besoin de davantage de souplesse que ce qui est décrit dans le fichier de manifeste de l’actif par défaut. Par exemple :

- Spécifiques au périphérique : livrer uniquement les formats spécifié et/ou spécifiées des pistes de langue qui sont pris en charge par le périphérique qui est utilisé pour la lecture du contenu (« filtrage de format associé »). 
- Réduire le manifeste pour afficher un sous-clip d’un événement en direct (« filtrage de sous-clip »).
- Raccorder le début d’une vidéo (« ajustement d’une vidéo »).
- Ajuster la fenêtre de présentation (DVR) afin de fournir une longueur limitée de la fenêtre DVR dans le lecteur (« fenêtre Ajustement présentation »).
 
Pour atteindre cette flexibilité, les Services de support offre **Manifestes dynamique** basé sur des [filtres](media-services-dynamic-manifest-overview.md#filters)prédéfinis.  Lorsque vous définissez les filtres, vos clients peuvent les utiliser pour diffuser un format associé spécifique ou des sous-clips de la vidéo. Ils spécifiez filtre (s) dans l’URL en continu. Filtres peuvent être appliqués à adaptive vitesse de transmission en continu de protocoles pris en charge par [Emballage dynamique](media-services-dynamic-packaging-overview.md): TLS, MPEG-tiret, diffusion en continu lisse et HDS. Par exemple :

URL de tiret MPEG avec filtre

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL de diffusion en continu lisse avec filtre

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Pour plus d’informations sur comment livrer votre contenu et de générer l’URL de la diffusion en continu, consultez la [vue d’ensemble du contenu de remise](media-services-deliver-content-overview.md).


>[AZURE.NOTE]Notez que la dynamique de manifestes ne changent pas l’actif et le manifeste par défaut pour cet actif. Votre client peut choisir demander un flux avec ou sans filtres. 


###<a id="filters"></a>Filtres 

Il existe deux types de filtres d’immobilisation : 

- Filtres globaux (peut être appliqué à n’importe quel élément dans le compte Azure Media Services, ont une durée de vie du compte) et 
- Filtres locaux (ne peut être appliqué à une ressource à laquelle le filtre a été associé lors de sa création, ont une durée de vie de l’actif). 

Types de filtres globaux et locaux ont exactement les mêmes propriétés. La principale différence entre les deux est que pour les scénarios de type d’un serveur de fichiers est plus approprié. Filtres globaux conviennent généralement pour les profils de périphérique (filtrage de format associé) où les filtres locaux peuvent être utilisés pour supprimer une ressource spécifique.


##<a id="scenarios"></a>Scénarios courants 

Comme mentionné avant, lors de la remise de votre contenu aux clients (diffusion en continu d’événements en direct ou vidéo à la demande) votre vise à fournir une vidéo de haute qualité à divers périphériques dans des conditions de réseau différents. En outre, votre peut avoir d’autres impératifs qui impliquent le filtrage de vos ressources et de l’utilisation de s de **Manifeste dynamique**. Les sections suivantes fournissent une brève vue d’ensemble des différents scénarios de filtrage.

- Spécifiez uniquement un sous-ensemble des formats audio et vidéo capable de certains périphériques (au lieu de tous les formats associés qui sont associés à l’actif). 
- Lire uniquement une section d’une vidéo (au lieu de la vidéo entière).
- Ajuster la fenêtre de présentation DVR.

##<a name="rendition-filtering"></a>Filtrage de format associé 

Vous pouvez choisir de coder vos ressources à plusieurs profils de codage (base de H.264, H.264 haute, AACL, AACH, Dolby Digital Plus) et plusieurs vitesses de transmission de qualité. Toutefois, tous les périphériques client prendra en charge les profils et les vitesses de transmission de toutes vos ressources. Par exemple, les anciens périphériques Android prend uniquement en charge H.264 planifié + AACL. Envoi des débits supérieurs avec un dispositif qui ne peut pas obtenir les avantages, déchets de calcul de la bande passante et de périphérique. Ce dispositif doit décoder toutes les informations données, uniquement à l’échelle vers le bas pour l’affichage.

Avec un manifeste dynamique, vous pouvez créer des profils de périphérique comme mobile, de la console, HD/SD, etc. et inclure les pistes et les qualités que vous souhaitez faire partie de chaque profil.

 
![Exemple de filtrage associé][renditions2]

Dans l’exemple suivant, un encodeur a été utilisé pour encoder un actif de la mezzanine en sept formats de vidéo ISO MP4s (à partir de 180p 1080p). L’immobilisation codée peut être empaquetée dynamiquement dans n’importe lequel des protocoles de diffusion en continu suivants : TLS, lisse, MPEG tiret et HDS.  En haut du diagramme, le manifeste de TLS pour l’actif sans filtre s’affiche (elle contient tous les formats associés de sept).  Dans la partie inférieure gauche, le manifeste de TLS auxquels a été appliqué un filtre nommé « ott » s’affiche. Le filtre de « ott » spécifie la suppression de toutes les vitesses de transmission au-dessous de 1 Mb/s, ce qui a entraîné les niveaux de qualité deux bas retirées dans la réponse.  En bas à droite, le manifeste de TLS auxquels a été appliqué un filtre nommé « mobiles » s’affiche. Le filtre « mobile » spécifie pour supprimer des formats associés où la résolution est plus grande que 720p, ce qui a entraîné les deux formats 1080p qui est supprimés.

![Filtrage de format associé][renditions1]

##<a name="removing-language-tracks"></a>Suppression de pistes de langue

Vos actifs peuvent inclure plusieurs langues audio telles que l’anglais, espagnol, Français, etc.. En règle générale, les responsables du SDK du lecteur par défaut de sélection d’une piste audio et audio disponible effectue le suivi par la sélection de l’utilisateur. Il est difficile de développer ces kits de développement logiciel lecteur, il requiert que les implémentations différentes sur un lecteur spécifique au périphérique-infrastructures. En outre, sur certaines plates-formes, API de lecteur sont limitées et n’incluent pas la fonctionnalité de sélection audio où les utilisateurs ne peuvent pas sélectionner ou modifier la piste audio par défaut. Avec les filtres actifs, vous pouvez contrôler le comportement en créant des filtres qui incluent uniquement les langues audio souhaitées.

![Filtrage des pistes de langue][language_filter]


##<a name="trimming-start-of-an-asset"></a>Début d’ajustement d’un actif 

Dans la plupart des événements de diffusion en continu live, opérateurs exécuter certains tests avant l’événement. Par exemple, ils peuvent inclure une ardoise, comme suit, avant le début de l’événement : « Programme commencera momentanément ». Si le programme est l’archivage, le test et les données ardoise sont également archivées et seront incluses dans la présentation. Toutefois, ces informations ne doivent pas s’afficher pour les clients. Avec un manifeste dynamique, vous pouvez créer un filtre d’heure de début et supprimer les données indésirables dans le manifeste.

![Début de la fonction de suppression][trim_filter]

##<a name="creating-sub-clips-views-from-a-live-archive"></a>Création de sous-clips (vues) à partir d’une archive en ligne

De nombreux événements en direct sont longues et une archive en ligne peut inclure plusieurs événements. Après l’événement live extrémités diffuseurs souhaiterez décomposer l’archivage direct dans la logique de programme de démarrage et d’arrêt des séquences. Ensuite, publiez ces programmes virtuels séparément sans post traitement l’archivage direct et ne crée ne pas des actifs distincts (qui bénéficieront pas des fragments existants mis en cache dans les CDN). Parmi ces programmes virtuels (sous-clips) est les trimestres d’un football ou de basket, les tournées de baseball, ou des événements individuels d’une après-midi du programme des jeux Olympiques.

Avec un manifeste dynamique, vous pouvez créer des filtres à l’aide des heures de début/fin et créer des vues virtuels au-dessus d’une archive en ligne. 

![Filtre de sous-élément][subclip_filter]

Ressources filtrées :

![Ski][skiing]

##<a name="adjusting-presentation-window-dvr"></a>Réglage de la fenêtre de présentation (DVR)

Actuellement, Azure Media Services propose des archives circulaire où la durée peut être configurée entre 5 minutes - 25 heures. Manifeste de filtrage permet de créer une fenêtre DVR propagée sur la partie supérieure de l’archive, sans supprimer le média. Il existe de nombreux scénarios où diffuseurs pour fournir une fenêtre DVR limitée qui déplace le bord live et en même temps garder une plus grande fenêtre d’archivage. Un organisme de radiodiffusion télévisuelle peut utiliser les données hors de la fenêtre DVR pour mettre en surbrillance les éléments, ou he\she peut fournir différentes fenêtres DVR pour différents périphériques. Par exemple, la plupart des périphériques mobiles ne gère pas grandes fenêtres DVR (vous pouvez avoir une fenêtre DVR 2 minutes pour les périphériques mobiles et 1 heure pour les clients de bureau).

![Fenêtre DVR][dvr_filter]

##<a name="adjusting-livebackoff-live-position"></a>Réglage des LiveBackoff (position direct)

Manifeste de filtrage permet de supprimer plusieurs secondes à partir du bord direct d’un programme direct. Ainsi, les diffuseurs à lire la présentation sur le point de publication de visualisation et de créer des points d’insertion de publicité avant les afficheurs de recevoir le flux (généralement sauvegardées-off en 30 secondes). Diffuseurs peuvent ensuite exécuter un push ces dernières à leurs infrastructures de client dans le temps pour que les reçus et traiter les informations avant que l’opportunité de la publication.

En plus de la prise en charge de la publication, LiveBackoff peut être utilisé pour ajuster la position de téléchargement direct de client afin que lorsque des clients DERIVE et atteint le bord live toujours avoir fragments à partir du serveur au lieu de récupérer les erreurs HTTP 404 ou 412.

![livebackoff_filter][livebackoff_filter]


##<a name="combining-multiple-rules-in-a-single-filter"></a>Combinaison de plusieurs règles dans un même filtre.

Vous pouvez combiner plusieurs règles de filtrage dans un même filtre. Par exemple, vous pouvez définir une règle de plage pour supprimer ardoise à partir d’une archive en ligne et également filtrer les vitesses de transmission disponibles. Plusieurs règles de filtrage, le résultat final est la composition de ces règles (intersection uniquement).

![plusieurs des règles][multiple-rules]

##<a name="create-filters-programmatically"></a>Créer par programmation des filtres

La rubrique suivante décrit les entités de Media Services liés aux filtres. La rubrique indique également comment créer par programme des filtres.  

[Créer des filtres avec les API de reste](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Pour combiner plusieurs filtres (filtre composition)

Vous pouvez également combiner plusieurs filtres dans une URL. 

Le scénario suivant illustre pourquoi vous souhaiterez combiner des filtres :

1. Vous devez filtrer vos qualités de vidéo pour les périphériques mobiles comme Android ou iPAD (afin de limiter les qualités vidéo). Pour supprimer les qualités indésirables, vous devez créer un filtre global qui convient pour les profils de périphérique. Comme mentionné ci-dessus, les filtres globaux peuvent être utilisés pour tous vos actifs sous le même compte de services multimédias sans aucune autre association. 
2. Vous souhaitez également supprimer l’heure de début et de fin d’une immobilisation. Pour ce faire, vous créez un filtre local et définir l’heure de début et de fin. 
3. Vous souhaitez combiner les deux de ces filtres (sans combinaison que vous devez ajouter la qualité de filtrage pour le filtre de réduction qui rend l’utilisation du filtre difficile).

Pour combiner des filtres, vous devez définir les noms de filtre à la manifeste/sélection URL avec délimitée par des points-virgules. Supposons que vous avez un filtre nommé *MyMobileDevice* que les qualités de filtres et que vous avez nommé un autre *MyStartTime* pour définir une heure de début spécifique. Vous pouvez les combiner comme suit :

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Vous pouvez combiner jusqu'à 3 filtres. 

Pour plus d’informations, consultez [le](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.


##<a name="know-issues-and-limitations"></a>Problèmes connus et limitations

- Manifeste dynamique fonctionne dans un groupe d’images des limites (clé de trames), par conséquent, l’ajustement a la précision du groupe d’images. 
- Vous pouvez utiliser le même nom de filtre pour les filtres locaux et globaux. Notez ce filtre local sont prioritaires et remplacent les filtres globaux.
- Si vous mettez à jour un filtre, il peut prendre jusqu'à 2 minutes pour actualiser les règles du point de terminaison en continu. Si le contenu a été pris en charge l’utilisation de certains filtres (et mises en cache proxy et CDN caches), mise à jour de ces filtres peut entraîner des erreurs de lecteur. Il est conseillé d’effacer le cache après la mise à jour du filtre. Si cette option n’est pas possible, envisagez d’utiliser un autre filtre.


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Voir aussi

[Fourniture de contenu à la vue d’ensemble des clients](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
 