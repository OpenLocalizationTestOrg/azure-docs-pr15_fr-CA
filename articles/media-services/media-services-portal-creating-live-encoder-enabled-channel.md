<properties 
    pageTitle="Comment faire pour effectuer une diffusion en continu live à l’aide d’Azure Media Services pour créer des flux de multi-bitrate avec le portail Azure | Microsoft Azure" 
    description="Ce didacticiel vous guide tout au long des étapes de création d’un canal qui reçoit un flux en direct unique-bitrate et l’encode à flux multiples à vitesse de transmission via le portail Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Comment faire pour effectuer une diffusion en continu live à l’aide d’Azure Media Services pour créer des flux de multi-bitrate avec le portail Azure

> [AZURE.SELECTOR]
- [Portail](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

Ce didacticiel vous guide tout au long des étapes de création d’un **canal** qui reçoit un flux en direct unique-bitrate et l’encode pour multi-vitesse de transmission de flux de données.

>[AZURE.NOTE]Pour plus d’informations conceptuelles relatives aux canaux qui sont activés pour l ' encodage live, consultez [Live en continu à l’aide d’Azure Media Services pour créer des flux de données multi-bitrate](media-services-manage-live-encoder-enabled-channels.md).

##<a name="common-live-streaming-scenario"></a>Scénario classique de diffusion en continu Live

Voici les principales étapes de la création d’applications courantes de diffusion en continu live.

>[AZURE.NOTE] Actuellement, la durée maximale recommandée pour un événement en direct est de 8 heures. Si vous avez besoin exécuter un canal pour de longues périodes, contactez amslived sur le site Microsoft.com.

1. Connectez une caméra vidéo à un ordinateur. Lancer et configurer un encodeur live local qui peut afficher un flux de vitesse de transmission unique dans un des protocoles suivants : RTMP, diffusion en continu lisse ou RTP (MPEG-TS). Pour plus d’informations, consultez [prise en charge du protocole RTMP Azure Media Services et les codeurs de Live](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Cette étape peut également être effectuée après avoir créé votre canal.

1. Créer et démarrer un canal. 

1. Récupérer la chaîne URL d’acquisition. 

    L’URL d’acquisition est utilisé par l’encodeur live pour envoyer le flux au canal.
1. Extraire l’URL de prévisualisation de canal. 

    Cette URL permet de vérifier que votre canal reçoit correctement le flux en direct.

3. Créer un événement/programme (ce qui créera également un actif). 
1. Publier l’événement (qui va créer un localisateur à la demande de l’actif associé).  

    Assurez-vous d’avoir au moins une unité réservée sur le point de terminaison en continu à partir de laquelle vous souhaitez transmettre du contenu de diffusion en continu.
1. Démarrer l’événement lorsque vous êtes prêt à commencer la diffusion et l’archivage.
2. Le cas échéant, l’encodeur live peut être signalé pour lancer une publication. La publicité est insérée dans le flux de sortie.
1. Arrêter l’événement chaque fois que vous souhaitez arrêter la diffusion et l’archivage de l’événement.
1. Suppression de l’événement (et éventuellement supprimer l’actif).   

##<a name="in-this-tutorial"></a>Dans ce didacticiel

Dans ce didacticiel, le portail Azure est utilisé pour effectuer les tâches suivantes : 

2.  Configurez des points de diffusion en continu.
3.  Créer un canal qui est activé pour effectuer l ' encodage live.
1.  Obtenir l’URL d’acquisition afin de lui fournir pour encoder de live. L’encodeur live utilisera cette URL pour recevoir le flux de données dans le canal. .
1.  Créez un programme d’événement / (et un actif)
1.  Publier l’actif et obtenir l’URL de la diffusion en continu  
1.  Lire votre contenu 
2.  Nettoyage des

##<a name="prerequisites"></a>Conditions préalables

La configuration suivante est requise pour ce didacticiel.

- Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/).
- Un compte de Services de support. Pour créer un compte de Services de support, consultez [Créer un compte](media-services-portal-create-account.md).
- Une webcam et un encodeur qui peut envoyer un flux direct de vitesse de transmission unique.

##<a name="configure-streaming-endpoints"></a>Configurer des points de terminaison en continu 

Media Services fournit l’emballage dynamique qui vous permet de remettre votre MP4s multi-bitrate dans les formats de diffusion en continu suivants : MPEG tiret, TLS, diffusion en continu lisse ou HDS, sans avoir à ré-package dans ces formats de transmission en continu. Avec emballage dynamique vous suffit de stocker et de payer les fichiers au format de stockage unique et Media Services pour la génération et fournir la réponse appropriée en fonction de la demande du client.

Pour tirer parti de l’emballage dynamique, vous devez obtenir au moins une unité de transmission en continu pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu.  

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, effectuez les opérations suivantes :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/) et sélectionnez votre compte de l’AMS.
1. Dans la fenêtre **paramètres** , cliquez sur **les points de terminaison en flux continu**. 

2. Cliquez sur la diffusion en continu de point de terminaison par défaut. 

    La fenêtre **Détails de point de terminaison de diffusion en continu par défaut** s’affiche.

3. Pour spécifier le nombre d’unités de transmission en continu, faites glisser le curseur de **l’unité de transmission en continu** .

    ![Unités de transmission en continu](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-streaming-units.png)

4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

    >[AZURE.NOTE]L’allocation de toutes nouvelles unités peut prendre jusqu'à 20 minutes.

##<a name="create-a-channel"></a>Création d’un canal

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez les Services de support et puis cliquez sur le nom de votre compte de Services de support.
2. Sélectionnez **Live en continu**.
3. Sélectionnez **créer des personnalisées**. Cette option vous permet de créer un canal qui est activé pour l ' encodage live.

    ![Création d’un canal](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
    
4. Cliquez sur **paramètres**.
    
    1.  Choisissez le type de canal **D ' encodage Live** . Ce type indique que vous voulez créer un canal qui est activé pour l ' encodage live. Cela signifie que l’entrée seule vitesse de transmission de flux de données est envoyé au canal et codée en un flux multi-vitesse de transmission à l’aide des paramètres d’encodeur live. Pour plus d’informations, consultez [Live en continu à l’aide d’Azure Media Services pour créer des flux de données multi-bitrate](media-services-manage-live-encoder-enabled-channels.md). Cliquez sur OK.
    2. Spécifiez le nom d’un canal.
    3. Cliquez sur OK au bas de l’écran.
    
5. Sélectionnez l’onglet **réception** .

    1. Sur cette page, vous pouvez sélectionner un protocole de diffusion en continu. Pour le type de canal **D ' encodage Live** , les options de protocole valides sont les suivantes :
        
        - Une seule vitesse de transmission Fragmented MP4 (diffusion en continu lisse)
        - Vitesse de transmission unique RTMP
        - RTP (MPEG-TS) : Les flux MPEG-2 Transport via RTP.
        
        Pour obtenir une explication détaillée sur chaque protocole, reportez-vous à la section [Live en continu à l’aide d’Azure Media Services pour créer des flux de données multi-bitrate](media-services-manage-live-encoder-enabled-channels.md).
    
        Vous ne pouvez pas modifier l’option de protocole lors du canal ou ses événements/programmes associés sont en cours d’exécution. Si vous avez besoin de différents protocoles, vous devez créer des canaux séparés pour chaque protocole de diffusion en continu.  

    2. Vous pouvez appliquer la restriction IP sur l’acquisition. 
    
        Vous pouvez définir les adresses IP qui sont autorisés à l’acquisition d’une vidéo à ce canal. Autorisé les adresses IP peuvent être spécifiées sous la forme d’une seule adresse IP (par exemple, « 10.0.0.1 »), une plage IP à l’aide d’une adresse IP et un masque de sous-réseau CIDR (par exemple, ' 10.0.0.1/22') ou une plage IP à l’aide d’une adresse IP et un masque de sous-réseau décimal séparé (par exemple, ' 10.0.0.1(255.255.252.0)').

        Si aucune adresse IP n’est spécifiée et s’il n’y a aucune définition de règle puis aucune adresse IP ne pourra. Pour permettre à n’importe quelle adresse IP, créez une règle et la valeur 0.0.0.0/0.

6. Sous l’onglet **Aperçu** , appliquer la restriction IP sur l’aperçu.
7. Sur l’onglet **codage** , spécifiez le paramètre prédéfini de codage. 

    Actuellement, le seul système prédéfinis vous pouvez sélectionner est **par défaut 720p**. Pour spécifier un paramètre prédéfini personnalisé, ouvrez un ticket de support technique de Microsoft. Puis, entrez le nom de la présélection créé pour vous. 

>[AZURE.NOTE] Actuellement, le début de canal peut prendre jusqu'à 30 minutes. Réinitialisation du canal peut prendre jusqu'à 5 minutes.

Une fois que vous avez créé le canal, vous pouvez cliquez sur le canal et sélectionnez **paramètres** dans lequel vous pouvez afficher vos configurations de canaux. 

Pour plus d’informations, consultez [Live en continu à l’aide d’Azure Media Services pour créer des flux de données multi-bitrate](media-services-manage-live-encoder-enabled-channels.md).


##<a name="get-ingest-urls"></a>URL d’acquisition Get

Une fois que le canal est créé, vous pouvez obtenir les URL que vous souhaitez offrir à l’encodeur direct d’acquisition. Le codeur utilise ces URL à l’entrée d’un flux en direct.

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##<a name="create-and-manage-events"></a>Créer et gérer des événements

###<a name="overview"></a>Vue d’ensemble

Un canal est associé à des événements/programmes qui vous permettent de contrôler la publication et le stockage de segments d’un flux en direct. Canaux de gérer les programmes d’événements. La relation de canal et le programme est très similaire sur des supports traditionnels, où un canal est un flux constant de contenu et un programme est associé à un événement chronométré sur ce canal.

Vous pouvez spécifier le nombre d’heures que vous souhaitez conserver le contenu enregistré pour l’événement en définissant la longueur de la **Fenêtre d’Archive** . Cette valeur peut être définie à partir d’un minimum de 5 minutes à un maximum de 25 heures. Longueur de la fenêtre archives détermine également qu'autant de fois que les clients peut rechercher en arrière à partir de la position actuelle de live. Événements peuvent s’exécuter sur le laps de temps, mais contenu qui se situe derrière la longueur de la fenêtre est ignoré en permanence. La valeur de cette propriété détermine également la durée pendant laquelle les manifestes du client peuvent augmenter.

Chaque événement est associé à une immobilisation. Pour publier l’événement, vous devez créer un localisateur à la demande pour l’élément associé. Ayant ce repère vous permettra de générer une URL de flux que vous fournissez à vos clients.

Un canal prend en charge les trois événements s’exécutant simultanément afin de pouvoir créer plusieurs archives du même flux de données entrante. Cela vous permet de publier et archiver des différentes parties d’un événement selon les besoins. Par exemple, vos besoins est d’archiver 6 heures d’un événement, mais à la dernières seulement 10 minutes de diffusion. Pour ce faire, vous devez créer deux qui s’exécutent simultanément événement. Un événement est défini pour l’archivage de 6 heures de l’événement, mais le programme n’est pas publié. L’autre événement est défini pour l’archivage pendant 10 minutes et ce programme est publié.

Vous ne devez pas réutiliser les programmes existants de nouveaux événements. À la place, créer et démarrer un nouveau programme pour chaque événement.

Démarrer un programme d’événement/lorsque vous êtes prêt à commencer la diffusion et l’archivage. Arrêter l’événement chaque fois que vous souhaitez arrêter la diffusion et l’archivage de l’événement. 

Pour supprimer le contenu archivé, arrêter et supprimer l’événement puis supprimer l’actif associé. Une immobilisation ne peut pas être supprimée s’il est utilisé par l’événement ; l’événement doit être d’abord supprimé. 

Même après l’arrêt et suppression de l’événement, les utilisateurs pourront diffuser vos contenus archivés sous la forme d’une vidéo à la demande, pour tant que vous ne supprimez pas la ressource.

Si vous souhaitez conserver le contenu archivé, mais n’est pas disponible pour la diffusion en continu, supprimer le localisateur de transmission en continu.

###<a name="createstartstop-events"></a>Événements créer/démarrage/arrêt

Une fois que le flux de données transitent par le canal, vous pouvez commencer l’événement en flux continu par la création d’une immobilisation, le programme et le localisateur de diffusion en continu. Cela archiver le flux et rendre disponible aux utilisateurs via le point de terminaison pour la diffusion en continu. 

Il existe deux méthodes pour démarrer des événements : 

1. À partir de la page de la **chaîne** , appuyez sur Ajouter un nouvel événement de l' **Événement Live** .

    Spécifiez : nom de l’événement, nom de la ressource, fenêtre Archiver et option de chiffrement.
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Si vous avez laissé **Publier maintenant cet événement en direct** est activée, l’événement de l’URL de publication sera créée.
    
    Vous pouvez appuyer sur **Démarrer**, chaque fois que vous êtes prêt à diffuser l’événement.

    Une fois que vous démarrez l’événement, vous pouvez appuyer sur **Espion** pour commencer à lire le contenu.

2. Sinon, vous pouvez utiliser un raccourci et appuyez sur bouton de **Mise en service** de la page de la **chaîne** . Cela créera un par défaut actif, programme et repère de diffusion en continu.

    L’événement est appelé **par défaut** et la fenêtre d’archive est définie à 8 heures.

Vous pouvez regarder l’événement publié à partir de la page **Live événement** . 

Si vous cliquez sur **Off Air**, il s’arrêtera tous les événements en direct. 


##<a name="watch-the-event"></a>Regarder l’événement

Pour suivre l’événement, cliquez sur **Espion** dans le portail Azure ou copiez l’URL du flux et utilisez un lecteur de votre choix. 
 
![Créé](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Événement en direct convertit automatiquement des événements de contenu à la demande lors de l’arrêt.

##<a name="clean-up"></a>Nettoyer

Si vous sont effectuées en continu des événements et à nettoyer les ressources configurés précédemment, suivez la procédure suivante.

- Arrêtez de pousser le flux à partir du codeur.
- Arrêter le canal. Une fois que le canal est arrêté, il ne pas encourir des frais. Lorsque vous devez le démarrer à nouveau, il aura le même URL d’acquisition afin que vous n’aurez pas besoin de reconfigurer votre codeur.
- Vous pouvez arrêter votre point de terminaison de diffusion en continu, sauf si vous souhaitez continuer à fournir l’archive de votre événement en direct comme un flux à la demande. Si le canal est dans un état arrêté, il ne pas encourir des frais.
  
##<a name="view-archived-content"></a>Permet d’afficher le contenu archivé

Même après l’arrêt et suppression de l’événement, les utilisateurs pourront diffuser vos contenus archivés sous la forme d’une vidéo à la demande, pour tant que vous ne supprimez pas la ressource. Une immobilisation ne peut pas être supprimée s’il est utilisé par un événement ; l’événement doit être d’abord supprimé. 

Pour gérer vos actifs, sélectionnez le **paramètre** , puis cliquez sur **ressources**.

![Actifs](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

##<a name="considerations"></a>Considérations relatives à la

- Actuellement, la durée maximale recommandée pour un événement en direct est de 8 heures. Si vous avez besoin exécuter un canal pour de longues périodes, contactez amslived sur le site Microsoft.com.
- Assurez-vous d’avoir au moins une unité réservée sur le point de terminaison en continu à partir de laquelle vous souhaitez transmettre du contenu de diffusion en continu.


##<a name="next-step"></a>Étape suivante

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
