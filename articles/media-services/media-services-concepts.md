<properties 
    pageTitle="Concepts de Media Services Azure | Microsoft Azure" 
    description="Cette rubrique donne une vue d’ensemble des Concepts de Azure Media Services" 
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

#<a name="azure-media-services-concepts"></a>Concepts de Media Services Azure 

Cette rubrique fournit une vue d’ensemble des concepts de Media Services plus importants.

##<a id="assets"></a>Ressources et du stockage

###<a name="assets"></a>Actifs

Un [actif](https://msdn.microsoft.com/library/azure/hh974277.aspx) contient des fichiers numériques (y compris la vidéo, fichiers audio, images, collections de miniatures, légendes et sous-titres) et les métadonnées relatives à ces fichiers. Une fois les fichiers numériques sont téléchargés dans une immobilisation, elles pourraient servir dans les Services de support, de codage et diffusion en continu des flux de travail.

Un actif est mappé à un conteneur d’objet blob dans le compte de stockage Azure et les fichiers de l’actif sont stockés sous forme de BLOB dans ce conteneur.

Lorsque vous décidez quel contenu multimédia à télécharger et le stocker dans une immobilisation, les considérations suivantes s’appliquent :

- Un actif doit contenir uniquement une instance unique, unique de contenu multimédia. Par exemple, une seule modification de TV épisode, film ou de publication.
- Une immobilisation ne doit pas contenir plusieurs formats associés ou des modifications d’un fichier audiovisuel. Un exemple d’une utilisation incorrecte d’une immobilisation essaient de stocker plus d’un épisode de TV, de publicité ou de plusieurs angles de la caméra à partir d’une seule production à l’intérieur d’un actif. Stocker plusieurs formats associés ou des modifications d’un fichier audiovisuel dans un actif peut entraîner des difficultés de soumettre des tâches de codage, de diffusion en continu et la sécurisation de la livraison du bien plus loin dans le flux de travail.  

###<a name="asset-file"></a>Fichier d’actif 
Un [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) représente un fichier audio ou vidéo qui est stocké dans un conteneur d’objet blob. Un fichier de ressources est toujours associé à une immobilisation et une immobilisation peut contenir un ou plusieurs fichiers. La tâche de Media Services Encoder échoue si un objet de fichier de ressources n’est pas associé à un fichier numérique dans un conteneur d’objet blob.

L’instance de **AssetFile** et le fichier multimédia réelle sont deux objets distincts. L’instance de AssetFile contient des métadonnées sur le fichier multimédia, tandis que le fichier multimédia contient le contenu du média réel.

Vous ne devriez pas modifier le contenu des conteneurs blob qui ont été générés par les Services de support sans utiliser les API de Service de support.

###<a name="asset-encryption-options"></a>Options de chiffrement actif

Selon le type de contenu que vous souhaitez télécharger, stocker et fournir, Media Services offre différentes options de cryptage que vous pouvez choisir.

**Aucun** Aucun chiffrement n’est utilisé. Il s’agit de la valeur par défaut. Notez que lors de l’utilisation de cette option votre contenu n’est pas protégé en transit ou au repos dans le stockage.

Si vous souhaitez fournir un MP4 utilisant le téléchargement progressif, utilisez cette option pour télécharger votre contenu.

**StorageEncrypted** – Utilisez cette option pour chiffrer votre contenu clair localement en utilisant un cryptage AES 256 bits et ensuite de le télécharger vers le stockage Azure où il est stocké est crypté au repos. Les ressources protégées par un cryptage de stockage sont automatiquement et placés dans un système de fichiers cryptés avant le codage et non éventuellement cryptés avant de le télécharger comme une nouvelle immobilisation de sortie. Cas d’usage principal pour le cryptage du stockage est lorsque vous souhaitez sécuriser vos fichiers de média d’entrée de haute qualité avec le cryptage fort au repos sur le disque. 

Afin d’offrir une gestion du stockage crypté, vous devez configurer la stratégie de livraison de l’actif afin que les Services de support sache comment vous souhaitez livrer votre contenu. Avant votre actif peut être diffusé en continu, le serveur de diffusion supprime le cryptage de stockage et flux de votre contenu à l’aide de la stratégie de remise spécifié (par exemple, AES, PlayReady ou pas de cryptage). 

**CommonEncryptionProtected** - Utilisez cette option si vous souhaitez crypter (ou télécharger déjà chiffré) contenu avec cryptage commun ou DRM PlayReady (par exemple, diffusion en continu lisse protégé par PlayReady DRM).

**EnvelopeEncryptionProtected** – Utilisez cette option si vous souhaitez protéger (ou télécharger déjà protégé) HTTP Live en continu (TLS) cryptées avec le cryptage AES (Advanced Standard). Notez que si vous téléchargez TLS déjà cryptée avec AES, il doit avoir été crypté par le Gestionnaire de transformation.

###<a name="access-policy"></a>Stratégie d’accès 

Un [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) définit les autorisations (telles que lecture, écriture et liste) et la durée d’accès à une ressource. Vous transmettez généralement un objet AccessPolicy à un localisateur qui peut ensuite être utilisé pour accéder aux fichiers contenus dans un élément.


###<a name="blob-container"></a>Conteneur de BLOB

Un conteneur blob fournit un regroupement d’un ensemble d’objets BLOB. Conteneurs BLOB sont utilisés dans les Services de support en tant que point de franchissement de contrôle d’accès et localisateurs de Signature de l’accès partagé (SAS) sur les immobilisations. Un compte de stockage Azure peut contenir un nombre illimité de conteneurs blob. Un conteneur peut stocker un nombre illimité d’objets BLOB.

>[AZURE.NOTE]Vous ne devriez pas modifier le contenu des conteneurs blob qui ont été générés par les Services de support sans utiliser les API de Service de support.

###<a id="locators"></a>Localisateurs

[Localisateur de](https://msdn.microsoft.com/library/azure/hh974308.aspx)s fournissent un point d’entrée pour accéder aux fichiers contenus dans un élément. Une stratégie d’accès est utilisée pour définir les autorisations et la durée qu’un client a accès à une ressource donnée. Localisateurs peuvent ont une relation plusieurs à un avec une stratégie d’accès, telle que les localisateurs différents peuvent fournir différentes heures de démarrage et les types de connexions à différents clients tout en utilisant toutes la même autorisation et les paramètres de durée ; Toutefois, en raison d’une restriction de stratégie d’accès partagé par les services de stockage Azure, ne peut pas avoir plus de cinq localisateurs uniques associés à un actif à un moment donné. 

Media Services prend en charge deux types de localisateurs : localisateurs de OnDemandOrigin, utilisés pour diffuser des médias (par exemple, MPEG tiret, TLS ou diffusion en continu lisse) ou télécharger progressivement les media et localisateurs de SAS URL, utilisée pour charger ou télécharger le signalement de to\from les fichiers médias stockage Azure. 

Notez que l’autorisation de la liste (AccessPermissions.List) ne doit pas être utilisée lors de la création d’un localisateur de OrDemandOrigin. 

###<a name="storage-account"></a>Compte de stockage

Tous les accès au stockage Azure s’effectue via un compte de stockage. Un compte de Service de support peut associer un ou plusieurs comptes de stockage. Un compte peut contenir un nombre illimité de conteneurs, tant que leur taille totale est inférieure à 500 To par compte de stockage.  Media Services fournit des outils de niveau SDK pour vous permettre de gérer plusieurs comptes de stockage et d’équilibrer la répartition de vos actifs pendant le téléchargement de ces comptes en fonction de mesures ou de distribution aléatoire. Pour plus d’informations, consultez Utilisation des [Stockage Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

##<a name="jobs-and-tasks"></a>Projets et des tâches

Une [tâche](https://msdn.microsoft.com/library/azure/hh974289.aspx) est généralement utilisé pour le processus (par exemple, l’index ou la coder) une présentation audio/vidéo. Si vous traitez plusieurs vidéos, créer une tâche pour chaque vidéo à encoder.

Un projet contient des métadonnées sur le traitement à effectuer. Chaque projet contient un ou plusieurs s [tâches](https://msdn.microsoft.com/library/azure/hh974286.aspx)qui spécifient une tâche de traitement atomique, ses actifs d’entrée, de sortie actifs, un processeur multimédia et ses paramètres associés. Les tâches d’un projet peuvent être enchaînés, où l’actif de la sortie d’une tâche est donné sous l’élément d’entrée à l’étape suivante. De cette façon, une tâche peut contenir tout le traitement nécessaire pour une présentation multimédia.

##<a id="encoding"></a>Codage

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

Pour plus d’informations sur les encodeurs pris en charge, consultez les [codeurs](media-services-encode-asset.md).


##<a name="live-streaming"></a>Live en continu

Dans Azure Media Services, un canal représente un pipeline de traitement de contenu en continu en direct. Un canal reçoit un flux d’entrée direct de deux manières :

- Un encodeur live local envoie multi-bitrate RTMP ou diffusion en continu lisse (MP4 fragmenté) pour le canal. Vous pouvez utiliser les encodeurs direct suivants que la diffusion en continu lisse de multi-débit de sortie : élémentaire, Envivio, Cisco. Les encodeurs direct suivants de sortie RTMP : transcodeurs Adobe Flash Live Telestream Wirecast et Tricaster. Les flux ingérés passent par l’intermédiaire de canaux sans traitement supplémentaire. Lors d’une demande, les Services de support offre un flux.

- Vitesse de transmission unique d’un flux de données (dans un des formats suivants : RTP (MPEG-TS)), RTMP, ou la diffusion en continu lisse (MP4 fragmenté)) est envoyé sur le canal qui est activé pour effectuer l ' encodage live avec Media Services. Le canal a effectue ensuite l ' encodage live d’entrants seule vitesse de transmission de flux de données à un flux vidéo multi-vitesse de transmission (adaptative). Lors d’une demande, les Services de support offre un flux.

###<a name="channel"></a>Canal

Dans les Services de support, [canal](https://msdn.microsoft.com/library/azure/dn783458.aspx)sont responsables du traitement du contenu de diffusion en continu live. Un canal fournit un point de terminaison d’entrée (acquisition URL) que vous fournissez ensuite à un TRANSCODEUR live. Le canal reçoit des flux d’entrée direct de la TRANSCODEUR direct et le rend disponible pour la diffusion en continu par le biais de StreamingEndpoints un ou plusieurs. Les canaux fournissent également un point de terminaison d’aperçu (preview URL) vous permet d’afficher un aperçu et valider votre flux de données avant le traitement et la livraison.

Vous pouvez obtenir l’URL de l’acquisition et l’aperçu lorsque vous créez le canal. Pour obtenir ces URL, le canal n’a pas à être à l’état démarré. Lorsque vous êtes prêt à commencer l’envoi de données à partir d’un live TRANSCODEUR dans le canal, le canal doit être démarré. Une fois le TRANSCODEUR live commence à traiter les données, vous pouvez prévisualiser votre flux de données.

Chaque compte de Media Services peut contenir plusieurs canaux, plusieurs programmes et plusieurs StreamingEndpoints. Selon les besoins de bande passante et de la sécurité, les services de StreamingEndpoint peuvent être dédiés à un ou plusieurs canaux. Les StreamingEndpoint peut extraire à partir de n’importe quel canal.


###<a name="program"></a>Programme

Un [programme](https://msdn.microsoft.com/library/azure/dn783463.aspx) vous permet de contrôler la publication et le stockage de segments d’un flux en direct. Gérer les canaux de programmes. La relation de canal et le programme est très similaire sur des supports traditionnels, où un canal est un flux constant de contenu et un programme est associé à un événement chronométré sur ce canal.
Vous pouvez spécifier le nombre d’heures que vous souhaitez conserver le contenu enregistré pour le programme en définissant la propriété **ArchiveWindowLength** . Cette valeur peut être définie à partir d’un minimum de 5 minutes à un maximum de 25 heures.

ArchiveWindowLength détermine également que le montant maximal de fois que les clients pouvez effectuer une recherche en arrière à partir de la position actuelle de direct. Programmes peuvent s’exécuter sur le laps de temps, mais le contenu qui se situe derrière la longueur de la fenêtre est en permanence supprimé. La valeur de cette propriété détermine également la durée pendant laquelle les manifestes du client peuvent augmenter.

Chaque programme est associé à une immobilisation. Pour publier le programme, vous devez créer un localisateur de l’actif associé. Ayant ce repère vous permettra de générer une URL de flux que vous fournissez à vos clients.

Un canal prend en charge les programmes en cours d’exécution simultanément jusqu'à trois afin de pouvoir créer plusieurs archives du même flux de données entrante. Cela vous permet de publier et archiver des différentes parties d’un événement selon les besoins. Par exemple, vos besoins est d’archiver 6 heures d’un programme, mais à la dernières seulement 10 minutes de diffusion. Pour ce faire, vous devez créer deux programmes s’exécutant simultanément. Un programme est défini pour l’archivage de 6 heures de l’événement, mais le programme n’est pas publié. L’autre programme est défini pour l’archivage pendant 10 minutes et ce programme est publié.


Pour plus d’informations, voir :

- [Utilisation de canaux qui sont activés pour effectuer l ' encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Utilisation de canaux qui reçoivent Multi-vitesse de transmission de flux de codeurs de locaux](media-services-live-streaming-with-onprem-encoders.md)
- [Quotas et des limitations](media-services-quotas-and-limitations.md).

##<a name="protecting-content"></a>Protection du contenu

###<a name="dynamic-encryption"></a>Cryptage dynamique

Azure Media Services vous permet de sécuriser vos médias à partir de la que laisse votre ordinateur par le biais de stockage, de traitement et de distribution. Media Services vous permet de livrer votre contenu crypté dynamiquement avec la norme AES (Advanced Encryption) (à l’aide de clés de cryptage à 128 bits) et cryptage commun (CENC) à l’aide de PlayReady et/ou Widevine DRM. Media Services fournit également un service de distribution de clés AES et les licences PlayReady aux clients autorisés.

Actuellement, vous pouvez crypter les formats de diffusion en continu suivants : TLS, MPEG tiret et diffusion en continu lisse. Vous ne pouvez pas crypter HDS format de flux, ou des téléchargements progressifs.

Si vous souhaitez que pour les Services de support crypter une immobilisation, vous devez associer une clé de cryptage (CommonEncryption ou EnvelopeEncryption) à votre actif et également de configurer les stratégies d’autorisation pour la clé.

Si vous souhaitez diffuser la gestion du stockage crypté, vous devez configurer la stratégie de livraison de l’actif afin de spécifier la manière dont vous souhaitez livrer votre actif.

Lorsqu’un flux est demandé par un lecteur, Media Services utilise la clé spécifiée pour dynamiquement chiffrer votre contenu à l’aide d’un cryptage d’enveloppe (avec AES) ou commun (avec PlayReady ou Widevine). Pour déchiffrer le flux de données, le lecteur demande la clé à partir du service de distribution de clés. Pour décider ou non de l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiée pour la clé.


###<a name="token-restriction"></a>Restriction de jeton

La stratégie d’autorisation de clés contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvert, le jeton de restriction ou de restriction IP. La stratégie de jeton restreint doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans les formats de jetons de Web Simple (SWT) et JSON Web jeton (JWT). Media Services ne fournit pas de Services de jeton sécurisée. Vous pouvez créer un STS personnalisé ou exploiter Microsoft Azure ACS aux jetons de problème. Le SJS doit être configuré pour créer un jeton de signature avec les revendications spécifiées de clé et le problème que vous avez spécifié dans la configuration du jeton de restriction. Les services de livraison clé Media renvoie la clé demandée (ou licence) pour le client si le jeton est valide et les revendications dans la jeton correspondance ceux configurés pour la clé (ou licence).

Lors de la configuration du jeton restreint la stratégie, vous devez spécifier la clé de vérification principal, un émetteur et un paramètres d’audience. La clé primaire de vérification contient le jeton a été signé avec la clé, l’émetteur est le service de jeton de sécurité qui émet le jeton. L’audience (parfois appelée étendue) décrit l’objectif du jeton ou la ressource autorise l’accès au jeton. Les services de livraison clé Media valide que ces valeurs dans le jeton correspondent aux valeurs dans le modèle.

Pour plus d’informations, consultez les articles suivants :

[Protéger la présentation de contenu](media-services-content-protection-overview.md)
[protéger avec AES-128](media-services-protect-with-aes128.md)
[protéger avec DRM](media-services-protect-with-drm.md)

##<a name="delivering"></a>Remise

###<a id="dynamic_packaging"></a>Emballage dynamique

Lorsque vous travaillez avec Media Services, il est recommandé de coder vos fichiers mezzanine à une vitesse de transmission adaptive MP4 définie et convertir le jeu de la mise en forme souhaitée à l’aide de l' [Emballage dynamique](media-services-dynamic-packaging-overview.md).


###<a name="streaming-endpoint"></a>Point de terminaison de la diffusion en continu

Un StreamingEndpoint représente un service de diffusion en continu qui peut diffuser du contenu directement dans une application de lecteur client ou à un réseau de livraison de contenu (CDN) distribution se poursuive (Azure Media Services offre désormais l’intégration Azure CDN). Le flux de données sortant d’un service de StreamingEndpoint peut être un flux en direct, ou une vidéo à la demande actifs sur votre compte de Services de support. En outre, vous pouvez contrôler la capacité du service StreamingEndpoint pour gérer les besoins croissants de la bande passante en ajustant l’échelle unités (également connu sous le nom de diffusion en continu). Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Unités de l’échelle vous permet de fournir avec capacité de sortie dédié qui peut être achetée par incréments de 200 Mbits/s et des fonctionnalités supplémentaires qui inclut actuellement emballage dynamique d’utilisation.

Il est recommandé d’utiliser le cryptage dynamique et/ou de packaging dynamique. Pour utiliser ces fonctionnalités, vous devez disposer d’au moins une unité de transmission en continu pour le point de terminaison à partir duquel vous souhaitez diffuser. Pour plus d’informations, reportez-vous à la section [mise à l’échelle transmission en continu des unités](media-services-portal-manage-streaming-endpoints.md).

Par défaut, vous pouvez avoir jusqu'à 2 points de terminaison de votre compte de Services de média de diffusion en continu. Pour demander une limite plus élevée, consultez [des Quotas et des limitations](media-services-quotas-and-limitations.md).

Facturation uniquement lorsque votre StreamingEndpoint est en état de marche.

###<a name="asset-delivery-policy"></a>Stratégie de livraison des actifs

L’une des étapes du workflow de distribution de contenu Media Services consiste à configurer [des stratégies de distribution pour les immobilisations ](https://msdn.microsoft.com/library/azure/dn799055.aspx)que vous souhaitez transmettre en continu. La stratégie de livraison actif indique aux Services de support comment vous souhaitez pour votre capital à livrer : dans les flux de protocole doit vos ressources dynamiquement conditionné (par exemple, MPEG tiret, TLS, diffusion en continu lisse ou tous), vous souhaitez dynamiquement crypter votre actif ou non et comment (cryptage commun ou enveloppe).

Si vous avez une gestion du stockage crypté, avant votre actif peut être diffusé en continu, le serveur de diffusion supprime le cryptage de stockage et flux de votre contenu à l’aide de la stratégie de livraison spécifiée. Par exemple, pour remettre vos ressources cryptées avec la clé de chiffrement Advanced Encryption Standard (AES), définissez le type de stratégie à DynamicEnvelopeEncryption. Pour supprimer le cryptage de stockage et flux de l’actif en clair, définissez le type de stratégie à NoDynamicEncryption.

###<a name="progressive-download"></a>Téléchargement progressif

Le téléchargement progressif vous permet de commencer la lecture des médias avant que l’intégralité du fichier a été téléchargé. Vous pouvez télécharger uniquement progressivement d’un fichier MP4.

Notez que vous devez décrypter actifs cryptés si vous le souhaitez pour les rendre disponibles pour le téléchargement progressif.

Pour fournir aux utilisateurs des URL de téléchargement progressif, vous devez d’abord créer un localisateur de OnDemandOrigin. Création du localisateur, vous donne le chemin d’accès de base à l’actif. Vous devez ensuite ajouter le nom de fichier MP4. Par exemple :

http://amstest1.Streaming.mediaservices.Windows.NET/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###<a name="streaming-urls"></a>URL de la diffusion en continu

Diffusion en continu de votre contenu aux clients. Pour fournir aux utilisateurs les URL de diffusion en continu, vous devez d’abord créer un localisateur de OnDemandOrigin. Création du localisateur, vous donne le chemin d’accès de base de la ressource qui contient le contenu que vous souhaitez diffuser. Toutefois, pour pouvoir lire ce contenu, vous devez modifier ce chemin d’accès supplémentaire. Pour construire une URL complète vers le fichier de manifeste en continu, vous devez concaténer le valeur de chemin d’accès de l’URL et le manifeste (filename.ism) nom de fichier. Ajoutez ensuite /Manifest et un format approprié (si nécessaire) dans le chemin de recherche.

Vous pouvez aussi diffuser votre contenu sur une connexion SSL. Pour ce faire, assurez-vous que votre URL en continu commencent par HTTPS.

Notez que vous pouvez diffuser uniquement sur SSL si le point de terminaison en continu à partir de laquelle vous livrez votre contenu a été créé après le 10 septembre 2014. Si votre URL de diffusion en continu est basés sur les points de terminaison en continu créés après le 10 septembre, l’URL contient « streaming.mediaservices.windows.net » (le nouveau format). En continu les URL qui contiennent « origin.mediaservices.windows.net » (l’ancien format) ne supportent pas SSL. Si votre URL est dans l’ancien format et que vous souhaitez être en mesure de diffuser en continu via le protocole SSL, créez un nouveau point de terminaison en continu. URL créés à partir du nouveau point de terminaison en continu permet de diffuser votre contenu sur SSL.

La liste suivante décrit les différents formats de transmission en continu et donne des exemples :

- Diffusion en continu lisse

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest


- TIRET DE MPEG

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=MPD-Time-CSF)



- Apple HTTP Live en continu (TLS) V4

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-AAPL)



- Apple HTTP Live en continu (TLS) V3

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-AAPL-v3)

- HDS (pour Adobe Enforcer/accès titulaires de licence uniquement)

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=f4m-f4f)


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
