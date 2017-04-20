<properties 
    pageTitle="Spécification d’acquisition Azure Media Services fragmentés MP4 live | Microsoft Azure" 
    description="Cette spécification décrit le protocole et le format pour l’ingestion de diffusion en continu live MP4 fragmenté en fonction de Microsoft Azure Media Services. Microsoft Azure Media Services fournit le service de diffusion en continu live qui permet aux clients de diffuser des événements en direct et de diffuser le contenu en temps réel à l’aide de Microsoft Azure comme la plateforme en nuage. Ce document explique également les meilleures pratiques de construction hautement redondant et robuste de live mécanismes d’acquisition." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"     
    ms.author="cenkdin;juliako"/>

#<a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Spécification d’acquisition Azure Media Services fragmentés MP4 live

Cette spécification décrit le protocole et le format pour l’ingestion de diffusion en continu live MP4 fragmenté en fonction de Microsoft Azure Media Services. Microsoft Azure Media Services fournit le service de diffusion en continu live qui permet aux clients de diffuser des événements en direct et de diffuser le contenu en temps réel à l’aide de Microsoft Azure comme la plateforme en nuage. Ce document explique également les meilleures pratiques de construction hautement redondant et robuste de live mécanismes d’acquisition.


##<a name="1-conformance-notation"></a>1. Notation de conformité

Les mots clés « doit », « Ne doit pas », « Requis », « Devra », « Ne devra pas », « Devrait », « Ne devrait pas », « Recommandé », « Mai » et « Facultatif » dans ce document doivent être interprétés comme décrit dans la RFC 2119.

##<a name="2-service-diagram"></a>2. diagramme du Service 

Le diagramme ci-dessous présente l’architecture de haut niveau du service de diffusion en continu live de Microsoft Azure Media Services :

1.  Codeur Live exécute un push de flux live en couches qui sont créés et mis en service via le SDK Microsoft Azure Media Services.
2.  Canaux, de programmes et de diffusion en continu de point de terminaison du handle Microsoft Azure Media Services toutes les fonctionnalités en continu live, y compris d’acquisition, de mise en forme, le cloud DVR, de sécurité, d’évolutivité et de redondance.
3.  Clients peuvent également déployer une couche CDN entre le point de terminaison de diffusion en continu et les points de terminaison de client.
4.  Flux de points de terminaison de client à partir de l’extrémité de la transmission en continu à l’aide des protocoles HTTP diffusion adaptative en continu (par exemple, diffusion en continu lisse, tiret, HDS ou TLS).

![Image1][image1]


##<a name="3-bit-stream-format--iso-14496-12-fragmented-mp4"></a>3. flux de bits, Format : ISO 14496-12 fragmenté MP4

Le format de transmission de diffusion en continu live d’acquisition dont il est question dans ce document repose sur [ISO-14496-12]. Reportez-vous au [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx) pour une explication détaillée du MP4 de fragmentation du format et des extensions pour les deux fichiers de vidéo à la demande et live de réception de diffusion en continu.

###<a name="live-ingest-format-definitions"></a>Transfert direct des définitions de format de 

Vous trouverez ci-dessous une liste de définitions qui s’appliquent pour live d’acquisition dans Microsoft Azure Media Services de format spécial :

1. Les 'ftyp', LiveServerManifestBox et boîte 'moov' doivent être envoyés avec chaque requête (HTTP POST).  Il doit être envoyé au début du flux de données et chaque fois que le codeur doit se reconnecter pour reprendre le flux de transfert.  Pour plus de détails, reportez-vous à la Section 6 dans [1].
2. Le point 3.3.2 dans [1] définit une zone facultative appelée StreamManifestBox pour live d’acquisition. En raison de la logique de routage de l’équilibrage de la charge de Microsoft Azure, l’utilisation de cette zone est déconseillée et ne devrait ne pas être présent lors de la réception dans les services de support Microsoft Azure. Si cette case n’est présente, Azure Media Services ignore silencieusement.
3. Le TrackFragmentExtendedHeaderBox défini dans 3.2.3.2 dans [1] doit être présent pour chaque fragment.
4. La version 2 de la TrackFragmentExtendedHeaderBox doit être utilisé pour générer des segments de médias avec des URL identiques dans plusieurs centres de données. Le champ d’index de fragment est requis pour basculement de centre de données entre des flux de données basé sur un index formats Apple HTTP Live en continu (TLS) et MPEG-tiret basée sur l’index.  Pour permettre le basculement de cross-centre de données, l’index du fragment doit être synchronisé sur plusieurs codeurs et augmenter par 1 pour chaque fragment media successifs, même sur les défaillances ou les redémarrages du codeur.
5. [1], section 3.3.6 définit la zone appelée MovieFragmentRandomAccessBox ('mfra') qui peut être envoyé à la fin de l’ingestion live pour indiquer EOS (fin de flux) pour le canal. En raison de la logique d’acquisition Azure Media Services, l’utilisation de la propriété EOS (fin de flux) est déconseillée et la zone « mfra » pour l’ingestion live ne doivent ne pas être envoyés. Si envoyé, Azure Media Services ignore silencieusement. Il est recommandé d’utiliser le [Canal réinitialiser](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels) pour réinitialiser l’état du point de réception et également il est recommandé d’utiliser le [Programme arrêter](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) pour mettre fin à une présentation et un flux de données.
6. La durée de fragment MP4 doit être constante, afin de réduire la taille des manifestes client et améliorer les heuristiques de téléchargement client à l’aide de balises de répétition.  La durée peut fluctuer afin de compenser la fréquence d’images de type non entier.
7. La durée de fragment MP4 doit être comprise entre environ 2 et 6 secondes.
8. MP4 fragment horodatages et index (temps d’absolute_ de fragment_ TrackFragmentExtendedHeaderBox et fragment_index) devraient vous parvenir dans l’ordre croissant.  Bien que Azure Media Services résiste aux fragments en double, elle a très limité pour réorganiser les fragments en fonction de la chronologie du média.

##<a name="4-protocol-format--http"></a>4. protocole Format – HTTP

ISO fragmenté MP4 basé direct d’acquisition pour les utilisations de Microsoft Azure Media Services standard HTTP POST à exécution longue demande pour transmettre les données du média encodé empaquetées dans un format MP4 de fragmentation au service. Chaque publication HTTP envoie une complète (« Stream ») MP4 de fragmentation des flux de bits à partir à partir de zones d’en-tête (zone 'moov', 'ftyp' et « Live de la zone serveur manifeste ») et en continuant avec une séquence de fragments (zones de 'moof' et « mdat »). Reportez-vous à la section 9.2 dans [1] pour la syntaxe de l’URL de requête HTTP POST. Est un exemple de l’URL de publication : 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

###<a name="requirements"></a>Configuration requise

Voici les spécifications détaillées :

1. Codeur doit démarrer la diffusion en envoyant une demande HTTP POST avec un vide « corps » (longueur de contenu nulle) à l’aide de la même URL de réception. Cela peut aider à détecter rapidement si le point de terminaison de réception live est valide et s’il existe d’authentification ou d’autres conditions requises. Par le protocole HTTP, le serveur ne sera en mesure d’envoyer des réponse HTTP arrière jusqu'à ce que la totalité de la demande, y compris le corps de publication est reçue. Étant donné la nature de longue durée de l’événement en direct, sans cette étape, l’encodeur peut-être pas en mesure de détecter toute erreur jusqu'à ce qu’il a terminé d’envoyer toutes les données.
2. Codeur doit gérer les erreurs ou les demandes d’authentification à la suite (1). Si (1) réussit avec une réponse HTTP 200, continuer.
3. Codeur doit démarrer une nouvelle demande HTTP POST avec le flux MP4 fragmenté.  La charge utile doit commencer par les zones d’en-tête suivies de fragments.  Remarque la boîte 'ftyp' et « moov » « Live Server manifeste Box » (dans cet ordre) doit être envoyé avec chaque demande, même si le codeur doit se reconnecter car la demande précédente a été interrompue avant la fin du flux. 
4. Encodeur doit utiliser l’encodage de transfert mémorisé en bloc pour le transfert car il est impossible de prédire la longueur du contenu entier de l’événement en direct.
5. Lorsque l’événement est terminé, après l’envoi du dernier fragment, le codeur doit se terminer normalement la séquence des messages encodage de transfert mémorisé en bloc (la plupart des piles de client HTTP gérer automatiquement). Codeur doit attendre le service retourne le code de réponse finale et puis mettre fin à la connexion. 
6. Le codeur ne doit pas utiliser le nom de Events() comme décrit dans 9.2 [1] pour l’ingestion direct dans Microsoft Azure Media Services.
7. Si la requête HTTP POST se termine ou arrive à expiration avant la fin du flux de données avec une erreur TCP, le codeur doit émettre une nouvelle demande de publication à l’aide d’une nouvelle connexion et respecter les spécifications ci-dessus avec l’exigence supplémentaire que le codeur doit renvoyer les deux fragments de MP4 précédentes pour chaque piste dans le flux de données et reprendre sans introduire des discontinuités dans la chronologie du média.  Renvoyer les dernière deux fragments de MP4 pour chaque piste garantit qu’il n’existe aucune perte de données.  En d’autres termes, si un flux contient une piste audio et vidéo, et la demande POST en cours échoue, le codeur doit se reconnecter et renvoyer les deux derniers fragments pour la piste audio, qui ont déjà été envoyés, et les deux derniers fragments de la piste vidéo, qui ont déjà été envoyés, afin de garantir qu’il n’existe aucune perte de données.  Le codeur doit conserver un tampon « forward » de fragments de média, dont il envoie à nouveau lors de la reconnexion.

##<a name="5-timescale"></a>5. l’échelle de temps 

[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) décrit l’utilisation de l’échelle de « temps » pour SmoothStreamingMedia (Section 2.2.2.1), StreamElement (Section 2.2.2.3), StreamFragmentElement(2.2.2.6) et LiveSMIL (Section 2.2.7.3.1). Si la valeur de l’échelle de temps n’est pas présente, la valeur par défaut est 10 000 000 (10 MHz). Bien que la spécification de Format de diffusion en continu lisse ne bloque pas l’utilisation d’autres valeurs de l’échelle de temps, la plupart des utilisations d’implémentations de codeur de cette valeur par défaut (10 MHz) pour générer la diffusion en continu lisse acquisition de données. En raison de la fonctionnalité [d’Emballage de Azure Media dynamique](media-services-dynamic-packaging-overview.md) , il est conseillé d’utiliser kHz 90 l’échelle de temps pour les flux vidéo et kHz 44,1 ou 48.1 pour les flux de données audio. Si l’échelle de temps de différentes valeurs sont utilisées pour les flux différents, l’échelle de temps de niveau de flux de données doit être envoyé. Reportez-vous à [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

##<a name="6-definition-of-stream"></a>6. définition de « Stream »  

« Stream » est l’unité élémentaire de l’opération de réception live pour composer la présentation en direct, la gestion du flux scénarios de basculement et la redondance. « Stream » est défini comme un unique MP4 de fragmentation des flux de bits qui peut contenir une piste unique ou plusieurs pistes. Une présentation en direct peut contenir un ou plusieurs flux selon la configuration de l’encoder(s) live. Les exemples ci-dessous illustrent diverses options d’utilisation autorisera à composer une présentation en direct.

**Exemple :** 

Il souhaite créer une présentation en continu en direct qui inclut les vitesses de transmission audio/vidéo suivants :

Vidéo – kb/s 3000, 1500kbps, 750 Kbits/s

Audio – 128 Kbits/s

###<a name="option-1-all-tracks-in-one-stream"></a>Option 1 : Toutes les pistes dans un flux de données

Dans cette option, un codeur unique génère toutes les pistes audio et vidéo et les regrouper dans un seul MP4 de fragmentation des flux de bits qui puis envoyé via une connexion HTTP POST. Dans cet exemple, il existe un seul flux pour cette présentation en direct :

![Image2][image2]

###<a name="option-2-each-track-in-a-separate-stream"></a>Option 2 : Chaque piste dans un flux distinct

Dans cette option, l’encoder(s) uniquement placer une trace dans le flux de bits de chaque Fragment MP4 et valide tous les flux sur plusieurs connexions HTTP distinctes. Cela peut être effectué avec des codeurs d’un ou plusieurs codeurs. Du point de vue de direct d’ingestion, cette présentation en direct est composée de quatre flux de données.

![sa stratégie partenaires3][image3]

###<a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Option 3 : Regrouper une piste audio à la piste vidéo la plus basse vitesse de transmission dans un flux de données

Dans cette option, le client choisit regrouper la piste audio de la piste vidéo la vitesse de transmission plus faible dans un flux de bits Fragment MP4 et laissez les autres pistes vidéo deux chaque son propre flux en cours. 


![image4][image4]


###<a name="summary"></a>Résumé

Ce qui est indiqué ci-dessus n’est pas une liste exhaustive de toutes les options de réception possibles pour cet exemple. En fait, tout groupement de pistes dans le flux de données prend en charge l’intégration de live. Clients et des fournisseurs de codeur peuvent choisir leurs propres implémentations en fonction de la complexité technique, capacité de codeur et redondance et basculement considérations. Il convient toutefois de noter que dans la plupart des cas il n'existe qu’une seule piste audio pour l’ensemble de la présentation live pourquoi il est important de vérifier la santé du flux d’acquisition qui contient de la piste audio. Cette considération souvent des résultats en plaçant la piste audio dans son propre flux de données (comme l’Option 2) ou il groupage avec la piste vidéo la vitesse de transmission le plus bas (comme dans l’Option 3). Également pour l’amélioration de la redondance et la tolérance de pannes, envoyer la même piste audio dans deux flux différents (Option 2 avec des pistes audio redondantes) ou regroupement de la piste audio au moins deux des pistes vidéo débit le plus faible (Option 3 avec audio fourni au moins deux flux de données vidéo) est recommandée pour live d'acquisition dans Microsoft Azure Media Services.

##<a name="7-service-failover"></a>7. basculement de Service 

Compte tenu de la nature de la diffusion en continu live, une bonne prise en charge est essentielle pour garantir la disponibilité du service. Microsoft Azure Media Services est conçu pour gérer les divers types de défaillances, notamment des erreurs de réseau, les erreurs de serveur, des problèmes de stockage, etc.. Lorsqu’elle est utilisée conjointement avec la logique de basculement sur incident du côté de l’encodeur direct, client peut obtenir un service de diffusion en continu live très fiable du nuage.

Dans cette section, nous aborderons les scénarios de basculement de service. Dans ce cas, l’échec se produit quelque part dans le service et se manifeste sous la forme d’une erreur réseau. Voici quelques recommandations pour la mise en oeuvre du codeur pour le basculement du service de gestion :


1. Utiliser un délai d’attente deuxième 10 pour l’établissement de la connexion TCP.  Si une tentative d’établissement de la connexion prend plue de 10 secondes, abandonner l’opération et essayez à nouveau. 
2. Utiliser un délai d’expiration court pour l’envoi des segments de message de la demande HTTP.  Si la durée de fragment cible MP4 est N secondes, utiliser un délai d’attente d’envoi entre N et 2N des secondes ; par exemple, utiliser un délai de 6 à 12 secondes si la durée de fragment MP4 est de 6 secondes.  Si un délai d’attente se produit, réinitialisation de la connexion, ouvrez une nouvelle connexion et reprendre le flux d’acquisition sur la nouvelle connexion. 
3. Tenir à jour une mémoire tampon de roulement contenant deux derniers fragments, pour chaque piste, qui ont été envoyés avec succès et complètement au service.  Si la requête HTTP POST pour un flux de données se termine ou arrive à expiration avant la fin du flux de données, ouvrir une nouvelle connexion et commencer une autre demande HTTP POST, renvoyer les en-têtes de flux, renvoyer les deux derniers fragments pour chaque piste et reprendre le flux sans introduire une discontinuité dans la chronologie de médias.  Cela réduira les risques de perte de données.
4. Il est recommandé que le codeur ne limite pas le nombre de tentatives pour établir une connexion ou de reprendre après une erreur TCP de diffusion en continu.
5. Après une erreur TCP :
    1. La connexion en cours doit être fermée, et une nouvelle connexion doit être créée pour une nouvelle demande HTTP POST.
    2. Le nouveau HTTP POST URL doit être le même que l’URL initiale de la publication.
    3. Le nouveau HTTP POST doit inclure les en-têtes de flux ('ftyp', « Live Server manifeste Box » et « moov » boîte) identiques aux en-têtes de flux de la comptabilisation initiale.
    4. Les deux derniers fragments envoyés pour chaque piste doivent être renvoyés, et sans introduire une discontinuité dans la chronologie de médias de diffusion en continu de reprendre.  Les horodatages de fragment MP4 doivent augmenter en permanence, même à travers les demandes HTTP POST.
6. Le codeur doit mettre fin à la requête HTTP POST si les données ne sont pas envoyées à un taux proportionnel à la durée de fragment MP4.  Une demande HTTP POST qui n’envoie pas de données peut empêcher Azure Media Services de se déconnecter rapidement à partir du codeur dans le cas d’une mise à jour de service.  Pour cette raison, la publication HTTP pour creux (signal ad) pistes doivent être courts vécu, fin dès que le fragment incomplet est envoyé.

##<a name="8-encoder-failover"></a>8. basculement de codeur

Basculement du codeur est le deuxième type de scénario de basculement sur incident doit être résolu pour la diffusion en continu de bout en bout pour live. Dans ce scénario, le message d’erreur s’est produite sur le côté du codeur. 

![image5][image5]


Voici les attentes du point de terminaison de réception en cas de basculement du codeur :

1. Une nouvelle instance de l’encodeur doit être créée afin de continuer la lecture en continu, comme illustré dans le diagramme ci-dessus (flux de vidéo avec une ligne en pointillés à 3000 k).
2. Nouvel encodeur doit utiliser la même URL pour les demandes HTTP POST comme l’instance non réussie.
3. Demande POST du nouvel encodeur doit inclure les zones d’en-tête MP4 fragmentés mêmes que l’instance a échoué.
4. Nouvel encodeur doit être correctement synchronisé avec tous les autres codeurs en cours d’exécution pour la même présentation en direct pour générer des échantillons audio/vidéo synchronisées avec les limites de fragment aligné.
5. Nouveau flux de données doit être sémantiquement équivalentes avec le flux de données précédent et interchangeables, au niveau de l’en-tête et le fragment.
6. Nouvel encodeur doit tenter de minimiser les pertes de données.  Les fragment_absolute_time et les fragment_index de fragments de media doivent augmenter à partir du point où le codeur s’était arrêté précédemment.  Le fragment_absolute_time et le fragment_index doivent augmenter de manière continue, mais il est possible d’introduire une discontinuité si nécessaire.  Azure Media Services ignorera les fragments qu’il a déjà reçu et traité, afin qu’il soit mieux vaut être prévoyant le renvoi de fragments que les discontinuités dans la chronologie de médias à l’usage. 

##<a name="9-encoder-redundancy"></a>9. redondance du codeur 

Événements que davantage la disponibilité à la demande et la qualité de l’expérience, il est recommandé d’employer actif des codeurs redondants pour assurer un basculement transparent sans perte de données en direct de certaines critiques.

![image6][image6]

Comme illustré dans le diagramme ci-dessus, il existe deux groupes de codeurs poussant simultanément les deux copies de chaque flux de données dans le service live. Cette configuration est prise en charge car Microsoft Azure Media Services a la possibilité de filtrer les fragments en double basés sur un timestamp ID et fragment de flux de données. Le flux résultant et l’archivage sera une seule copie de tous les flux qui constitue l’agrégation meilleure possible à partir des deux sources. Par exemple, dans un cas extrême hypothétique, tant qu’il existe un codeur (ne doit pas être le même que celui) en cours d’exécution à un moment donné dans le temps pour chaque flux de données, le flux direct résultant du service sera continu sans perte de données. 

L’exigence de ce scénario est presque identique à la configuration requise en cas de basculement du codeur à l’exception du second jeu de codeurs sont en cours d’exécution en même temps que les encodeurs principales.

##<a name="10-service-redundancy"></a>10. redondance des services  

Pour la distribution de globale très redondante, il parfois doit avoir sauvegarde cross-région pour traiter les sinistres distants. Développement sur la topologie de la « Redondance Encoder », les clients peuvent choisir d’avoir un déploiement de services redondants dans une région différente qui est connectée avec la 2e jeu des encodeurs. Les clients peuvent également fonctionner avec un fournisseur CDN pour déployer un lancement (Global Traffic Manager) devant les déploiements de deux service en toute transparence de router le trafic client. La configuration requise pour les codeurs est le même que le cas de « Redondance Encoder » avec la seule exception du second jeu de codeurs ont besoin de pointer vers un autre live acquisition de point de terminaison. Le diagramme ci-dessous illustre cette configuration :

![image7][image7]

##<a name="11-special-types-of-ingestion-formats"></a>11. spéciales Types de Formats d’acquisition 

Cette section traite d’un type spécial de formats d’ingestion vivants qui sont conçus pour gérer certains scénarios spécifiques.

###<a name="sparse-track"></a>Piste creux

Lors de la remise d’une présentation en continu en direct avec l’expérience client de qualité, il est souvent nécessaire de transmettre les événements synchronisés ou des signaux de bande avec les données du média principal. Un exemple est l’insertion de publicités de live dynamique. Ce type d’événement de signalisation diffère régulière audio/vidéo en flux continu en raison de sa nature incomplet. En d’autres termes, les données de signalisation généralement ne se produisent pas en permanence, et l’intervalle peut être difficile à prédire. Le concept de piste Sparse a été spécialement conçu pour l’acquisition et de diffusion des données de signalisation intrabande.

Vous trouverez ci-dessous une implémentation recommandée pour absorber la piste sparse :

1. Créer un distinct MP4 de fragmentation des flux de bits qui contient seulement des creux piste (s) sans les pistes audio et vidéo.
2. Dans la « Live Server zone manifeste » telle que définie à la Section 6 dans [1], utilisez paramètre de « parentTrackName » pour spécifier le nom de la piste de parent. Reportez-vous à la section 4.2.1.2.1.2 dans [1] pour plus de détails.
3. Dans la « Live Server zone manifeste », manifestOutput doit être défini sur « true ».
4. Étant donné la nature incomplet de l’événement de signalisation, il est recommandé que :
    1. Au début de l’événement en direct, codeur envoie les zones d’en-tête initiale au service qui permettrait au service d’enregistrer la piste creux dans le manifeste du client.
    2. Le codeur doit mettre fin à la requête HTTP POST lorsque les données ne sont pas envoyées.  Une publication HTTP de longue durée qui n’envoie pas de données peut empêcher Azure Media Services rapidement déconnecter à partir du codeur en cas de redémarrage du serveur ou de la mise à jour de service, comme le serveur de médias est bloqué temporairement dans une opération de réception sur le socket. 
    3. Durant le temps où les données de signalisation ne sont pas disponibles, le codeur devrait fermer la publication HTTP demande.  Lorsque la demande POST est active, le codeur doit envoyer les données 
    4. Lors de l’envoi de fragments éparpillés, codeur peut définir des en-tête Content-Length explicite s’il est disponible.
    5. Lors de l’envoi de fragment incomplet avec une nouvelle connexion, le codeur doit commencer à envoyer dans les zones d’en-tête suivis par les fragments de nouveau. Il s’agit pour gérer le cas où basculement a eu lieu entre les deux et la nouvelle connexion sparse est établie sur un nouveau serveur qui n’a pas vu la piste creux avant.
    6. Le fragment de suivi sparse seront disponible pour le client lorsque le parent correspondant suivi de fragment qui a égale ou plus grande valeur d’horodatage est mise à disposition du client. Par exemple, si le fragment incomplet a un horodatage de t = 1000, il est prévu une fois que le client voit la vidéo (en supposant que le nom de la piste de parent est une vidéo) fragment timestamp 1000 ou au-delà, il peut télécharger le fragment incomplet t = 1000. Veuillez noter que le signal réel peut très bien servir pour une position différente dans la chronologie de la présentation de son objectif désigné. Dans l’exemple ci-dessus, il est possible que le fragment incomplet de t = 1 000 a une charge XML qui est pour l’insertion d’une publicité dans une position de quelques secondes plus tard.
    7. La charge utile de fragment de suivi fragmenté peut être dans différents formats (par exemple XML ou texte ou binaire, etc.) en fonction de différents scénarios. 


###<a name="redundant-audio-track"></a>Redondant piste Audio

Dans un diffusion adaptative de HTTP scénario classique (diffusion en continu lisse ou tiret), il est souvent qu’une seule piste audio de la présentation. À la différence des pistes vidéo qui ont plusieurs niveaux de qualité pour le client à choisir dans les conditions d’erreur, la piste audio peut être un point de défaillance unique si la réception du flux de données qui contient la piste audio est interrompue. 

Pour résoudre ce problème, Microsoft Azure Media Services prend en charge la réception direct de pistes redondants. L’idée est que la même piste audio peut être envoyée plusieurs fois dans des flux différents. Pendant que le service n’enregistrera la piste audio une fois dans le manifeste du client, il est en mesure d’utiliser des pistes audio redondants comme des sauvegardes pour la récupération des fragments audio si la piste audio principale rencontre des problèmes de. Pour réceptionner les pistes audio redondants, le codeur doit :

1. Créez la même piste audio dans plusieurs flux de bits Fragment MP4. Les pistes audio redondantes doivent être sémantiquement équivalentes avec exactement les mêmes horodatages de fragment et interchangeables au niveau de l’en-tête et le fragment.
2. Assurez-vous que le « son » entrée dans le Live Server manifeste (Section 6 dans [1]) être le même pour toutes les pistes audio redondants.

Voici une implémentation recommandée pour les pistes audio redondants :

1. Envoyer chaque piste audio unique dans un flux de données en elle-même.  Également envoyer un flux de données redondante pour chacun de ces flux de piste audio, où le flux 2e diffère de la 1re uniquement par l’identificateur dans l’URL de publication HTTP : {protocole}  :// {adresse serveur} / {path}/Streams({identifier}) de point de publication.
2. Flux distincts permet d’envoyer les vitesses de transmission vidéo plus bas deux. Chacun de ces flux de données doit également contenir une copie de chaque piste audio unique.  Par exemple, lorsque plusieurs langues sont prises en charge, ces flux de données doit contenir des pistes audio pour chaque langue.
3. Les instances de serveur distinct (codeur) permet d’encoder et d’envoyer les flux de données redondantes mentionné dans (1) et (2). 



##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

 