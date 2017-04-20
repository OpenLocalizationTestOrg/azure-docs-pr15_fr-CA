<properties 
    pageTitle="Vue d’ensemble de Live transmission en continu à l’aide d’Azure Media Services | Microsoft Azure" 
    description="Cette rubrique fournit une vue d’ensemble de la transmission en continu live à l’aide d’Azure Media Services." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="juliako"/>

#<a name="overview-of-live-steaming-using-azure-media-services"></a>Vue d’ensemble de Live transmission en continu à l’aide d’Azure Media Services

##<a name="overview"></a>Vue d’ensemble

Lors de la remise des événements de diffusion en continu live avec Azure Media Services impliqués généralement les éléments suivants :

- Un appareil photo qui permet de diffuser un événement.
- Un codeur vidéo live qui convertit les signaux de la caméra dans des flux qui sont envoyés à un service de diffusion en continu live.

    Le cas échéant, plusieurs codeurs direct lors de la synchronisation. Les événements qu’une disponibilité très élevée à la demande et la qualité de l’expérience, il est recommandé d’employer l’actif des codeurs redondants avec la synchronisation de l’heure pour atteindre un basculement transparent sans perte de données en direct de certaines critiques.
- Service de diffusion en continu live qui vous permet d’effectuer les opérations suivantes :
    
    - acquisition de contenu en direct à l’aide de différents protocoles de diffusion en continu live (par exemple RTMP ou diffusion en continu lisse),
    - (le cas échéant) coder votre flux dans flux de transmission adaptive
    - afficher un aperçu de votre flux en direct,
    - Enregistrer et stocker le contenu ingéré pour être transmises à une version ultérieure (vidéo à la demande)
    - fournir le contenu par l’intermédiaire des protocoles de diffusion courants (par exemple, MPEG tiret, lisse, TLS, HDS) directement à vos clients, ou à un réseau de livraison de contenu (CDN) distribution se poursuive.


**Microsoft Azure Media Services** (AMS) offre la possibilité d’acquisition, à coder, afficher, stocker et livrer votre contenu de diffusion en continu live.

Lors de la remise de votre contenu aux clients votre objectif est de fournir une vidéo de haute qualité à divers périphériques dans des conditions de réseau différents. Pour ce faire, utiliser codeurs live pour coder votre flux de données dans un flux de vidéo multi-vitesse de transmission (débit adaptive).  Pour prendre en charge des flux de données sur différents périphériques, permet de Media Services [packaging dynamique](media-services-dynamic-packaging-overview.md) pour dynamiquement nouveau compresser votre flux de différents protocoles. Media Services prend en charge la remise de la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement).

Dans Azure Media Services, les **canaux**, les **programmes**et les **StreamingEndpoints** gèrent toutes les fonctionnalités en continu live, y compris d’acquisition, de mise en forme, DVR, de sécurité, d’évolutivité et de redondance.

Un **canal** représente un pipeline de traitement de contenu en continu en direct. Un canal peut recevoir un live d’entrée de flux de données dans l’une des manières suivantes :

- Sur les sites live envoie de codeur multi-bitrate **RTMP** ou **Diffusion en continu lisse** (MP4 fragmentés) pour le canal est configuré pour la remise des **pass-through** . La livraison **directe** est lorsque les flux ingérés traversent le **canal**s sans traitement supplémentaire. Vous pouvez utiliser les encodeurs direct suivants que la diffusion en continu lisse de multi-débit de sortie : élémentaire, Envivio, Cisco.  Les encodeurs direct suivants de sortie RTMP : transcodeurs Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast et Tricaster.  Un codeur live peut également envoyer un flux de vitesse de transmission unique à un canal qui n’est pas activé pour l ' encodage live, mais qui n’est pas recommandée. Lors d’une demande, les Services de support offre un flux.

    >[AZURE.NOTE] À l’aide d’une méthode directe est le moyen le plus économique de live en continu lorsque vous effectuez plusieurs événements sur une longue période de temps, et que vous avez déjà investi dans les codeurs sur place. Consultez les détails de [tarification](/pricing/details/media-services/) .
    
    
- Un encodeur live local envoie un flux unique à vitesse de transmission à la chaîne qui est activée pour effectuer l ' encodage live avec les Services de support dans un des formats suivants : RTMP ou diffusion en continu lisse (MP4 fragmentés). RTP (MPEG-TS) est également pris en charge, autant que vous possédiez une connexion dédiée au centre de données Azure. Les encodeurs direct suivants avec sortie RTMP connus pour fonctionner avec des canaux de ce type : Telestream Wirecast, FMLE. Le canal a effectue ensuite l ' encodage live d’entrants seule vitesse de transmission de flux de données à un flux vidéo multi-vitesse de transmission (adaptative). Lors d’une demande, les Services de support offre un flux.


À partir de la version de Media Services 2.10, lorsque vous créez un canal, vous pouvez spécifier la façon dont vous souhaitez pour votre canal recevoir le flux d’entrée et que vous souhaitez ou non pour le canal effectuer l ' encodage live de votre flux de. Vous disposez de deux options :

- **Aucun** (pass-through) : spécifiez cette valeur si vous envisagez d’utiliser un encodeur live sur site qui aura pour résultat multi-vitesse de transmission de flux de données (un flux pass-through). Dans ce cas, le flux entrant passé par le biais à la sortie sans aucun codage. C’est le comportement d’un canal avant la version 2.10.  

- **Standard** – choisissez cette valeur si vous envisagez d’utiliser les Services de support pour encoder le flux direct de votre vitesse de transmission unique pour multi-vitesse de transmission de flux de données. Cette méthode est la plus économique pour la mise à l’échelle rapidement pour des événements peu fréquents. Sachez qu’il existe un impact facturation pour l ' encodage live et que vous devez vous rappeler que quitter un canal direct de codage dans l’état « En cours d’exécution » implique des frais de facturation.  Il est recommandé d’arrêter immédiatement vos canaux en cours d’exécution après votre événement de diffusion en continu live pour éviter des frais supplémentaires toutes les heures. 

##<a name="comparison-of-channel-types"></a>Comparaison des Types de canal

Tableau suivant fournit un guide pour comparer les deux types de canaux pris en charge dans les Services de support

Fonctionnalité|Canal de transfert|Les canaux standard
---|---|---
Vitesse de transmission unique entrée est codée dans plusieurs vitesses de transmission dans le nuage|N°|Oui
Résolution maximale, le nombre de calques|1080p, 8 couches, 60 images par seconde|720p, 6 couches, 30 i/s
Protocoles d’entrée|RTMP, Smooth Streaming|RTMP, diffusion en continu lisse et RTP
Prix|Consultez la [page de tarification](/pricing/details/media-services/) et cliquez sur l’onglet « Vidéo Live »|Consultez la [page de tarification](/pricing/details/media-services/) 
Durée maximale d’exécution|24 x 7|8 heures
Prise en charge pour l’insertion de tablettes|N°|Oui
Prise en charge de la signalisation d’annonce|N°|Oui
Légendes des CEA 608/708 pass-through|Oui|Oui
Possibilité de récupérer à partir de brèves places dans la contribution d’alimentation|Oui|Non (canal commence slating après 6 + secondes sans les données d’entrée)
Prise en charge des groupes d’images d’entrée non uniforme|Oui|Non – entrée doit être fixée s 2 groupes d’images
Prise en charge pour l’entrée des taux de trame variable|Oui|Non – entrée doit être fixée de la fréquence d’images.<br/>Des variations mineures sont tolérées, par exemple, pendant les scènes à haut débit. Mais le codeur ne peut pas déplacer de 10 images/s.
Distributeur à arrêt automatique des canaux d’entrée lorsque l’alimentation est perdue|N°|Après 12 heures, si il n’y a aucun programme en cours d’exécution 

##<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Utilisation de canaux qui reçoivent multi-vitesse de transmission de flux de codeurs de locaux (pass-through)

Le diagramme suivant illustre les principales parties de la plate-forme AMS qui sont impliqués dans le processus de **transfert** .

![Flux de travail direct](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Pour plus d’informations, consultez [fonctionne avec des couches multiples de cette réception-vitesse de transmission des flux direct de codeurs de local](media-services-live-streaming-with-onprem-encoders.md).

##<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Utilisation des chaînes qui sont activés pour effectuer l ' encodage live avec Azure Media Services

Le diagramme suivant illustre les principales parties de la plate-forme AMS impliqués dans les flux de production Live en continu où un canal est activé pour effectuer l ' encodage live avec Media Services.

![Flux de travail direct](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Pour plus d’informations, consultez [utilisation des canaux qui sont activés pour effectuer l ' encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

##<a name="description-of-a-channel-and-its-related-components"></a>Description d’un canal et ses composants associés

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


##<a name="billing-implications"></a>Implications de la facturation

Un canal commence dès qu’il s’agit des transitions d’état pour « Exécution » via l’API de facturation.  

Le tableau suivant montre la correspondance entre les États de canal et les États de facturation dans le portail d’API et d’Azure. Notez que les États sont légèrement différentes entre les API UX de portail. Dès qu’une chaîne est dans l’état « En cours d’exécution » via l’API, ou à l’état « Prêt » ou « Diffusion » dans Azure portal, facturation sera active.

Pour arrêter le canal de facturation vous autres, vous devez arrêter le canal via l’API ou le portail Azure.
Vous êtes responsable de l’arrêt de vos canaux lorsque vous avez terminé avec le canal. Pour arrêter le canal provoque de facturation continue.

>[AZURE.NOTE]Lorsque vous travaillez avec des canaux Standard, AMS automatique fermeture n’importe quel canal est toujours dans un état « Running » 12 heures après que le flux d’entrée est perdu et aucun programme en cours d’exécution. Toutefois, vous serez toujours facturé pour le temps que le canal a été en état de « Exécution ».

###<a id="states"></a>États de canal et comment elles sont mappées vers le mode de facturation 

L’état actuel d’un canal. Les valeurs possibles sont les suivantes :

- **S’est arrêté**. C’est l’état initial du canal après sa création (sauf si le démarrage automatique a été sélectionnée dans le portail.) Aucune la facturation ne survient dans cet état. Dans cet état, les propriétés de canal peuvent être mis à jour mais de diffusion en continu n’est pas autorisée.
- **Démarrage**. Le canal est en cours de démarrage. Aucune la facturation ne survient dans cet état. Aucune mise à jour ou de la diffusion est autorisée au cours de cet état. Si une erreur se produit, le canal retourne à l’état arrêté.
- **En cours d’exécution**. Le canal est capable de traiter les flux en direct. Il est désormais de facturation l’utilisation. Vous devez arrêter le canal pour empêcher davantage de facturation. 
- **L’arrêt**. Le canal est en cours d’arrêt. Aucune la facturation ne survient dans cet état transitoire. Aucune mise à jour ou de la diffusion est autorisée au cours de cet état.
- **La suppression**. Le canal est en cours de suppression. Aucune la facturation ne survient dans cet état transitoire. Aucune mise à jour ou de la diffusion est autorisée au cours de cet état.

Le tableau suivant montre comment mappent des États de canal pour le mode de facturation. 
 
État du canal|Indicateurs de l’interface utilisateur de portail|S’agit-il de facturation ?
---|---|---
Démarrage|Démarrage|Aucun (état transitoire)
En cours d’exécution|Prêt (pas de programmes en cours d’exécution)<br/>ou<br/>Transmission en continu (au moins un programme en cours)|OUI
Arrêt|Arrêt|Aucun (état transitoire)
Arrêté|Arrêté|N°


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="related-topics"></a>Rubriques connexes

[Spécification d’acquisition Azure Media Services fragmentés MP4 Live](media-services-fmp4-live-ingest-overview.md)

[Utilisation de canaux qui sont activés pour effectuer l ' encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Utilisation de canaux qui reçoivent Multi-vitesse de transmission de flux de codeurs de locaux](media-services-live-streaming-with-onprem-encoders.md)

[Quotas et des limitations](media-services-quotas-and-limitations.md).  

[Concepts des Services multimédia](media-services-concepts.md)
