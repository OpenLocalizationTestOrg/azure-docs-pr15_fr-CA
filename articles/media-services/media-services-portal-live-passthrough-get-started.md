<properties 
    pageTitle="Comment faire pour effectuer une diffusion en continu live avec des codeurs sur site via le portail Azure | Microsoft Azure" 
    description="Ce didacticiel vous guide tout au long des étapes de création d’un canal qui est configuré pour une livraison directe." 
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
    ms.topic="get-started-article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


#<a name="how-to-perform-live-streaming-with-on-premise-encoders-using-the-azure-portal"></a>Comment faire pour effectuer une diffusion en continu live avec des codeurs sur site via le portail Azure

> [AZURE.SELECTOR]
- [Portail]( media-services-portal-live-passthrough-get-started.md)
- [.NET]( media-services-dotnet-live-encode-with-onpremises-encoders.md)
- [RESTE]( https://msdn.microsoft.com/library/azure/dn783458.aspx)

Ce didacticiel vous guide à travers les étapes de l’utilisation du portail Azure pour créer un **canal** qui est configuré pour une livraison directe. 

##<a name="prerequisites"></a>Conditions préalables

Les éléments suivants sont nécessaires pour terminer le didacticiel :

- Un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Un compte de Services de support. Pour créer un compte de Services de support, reportez-vous à la section [comment créer un compte de Services de support](media-services-portal-create-account.md).
- Une webcam. Par exemple, [le codeur Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm).

Il est vivement recommandé de consulter les articles suivants :

- [Services multimédias Azure RTMP prennent en charge et codeurs en direct](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Vue d’ensemble de Live transmission en continu à l’aide d’Azure Media Services](media-services-manage-channels-overview.md)
- [Live en continu avec des codeurs sur site qui créent des flux de multi-débit](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Scénario classique de diffusion en continu live

Les étapes suivantes décrivent les tâches impliquées dans la création de live en continu des applications courantes qui utilisent des canaux qui sont configurés pour une livraison directe. Ce didacticiel explique comment créer et gérer un canal direct et des événements en direct.

1. Connectez une caméra vidéo à un ordinateur. Lancer et configurer un encodeur live sur site qui exporte un flux RTMP ou MP4 de fragmentation de multi-vitesse de transmission. Pour plus d’informations, consultez [prise en charge du protocole RTMP Azure Media Services et les codeurs de Live](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Cette étape peut également être effectuée après avoir créé votre canal.

1. Créer et démarrer un canal direct.
1. Récupérer la chaîne URL d’acquisition. 

    L’URL d’acquisition est utilisé par l’encodeur live pour envoyer le flux au canal.
1. Extraire l’URL de prévisualisation de canal. 

    Cette URL permet de vérifier que votre canal reçoit correctement le flux en direct.

3. Créer un programme d’événement/live. 

    Lorsque vous utilisez le portail d’Azure, création d’un événement en direct crée également un actif. 
      
    >[AZURE.NOTE]Assurez-vous d’avoir au moins une unité réservée sur le point de terminaison en continu à partir de laquelle vous souhaitez transmettre du contenu de diffusion en continu.
1. Démarrez le programme / d’événement lorsque vous êtes prêt à commencer la diffusion et l’archivage.
2. Le cas échéant, l’encodeur live peut être signalé pour lancer une publication. La publicité est insérée dans le flux de sortie.
1. Arrêter le programme / d’événement chaque fois que vous souhaitez arrêter la diffusion et l’archivage de l’événement.
1. Supprimer l’événement/programme (et éventuellement supprimer l’actif).     

>[AZURE.IMPORTANT] Veuillez consulter [la diffusion en continu Live avec des codeurs sur site qui créent des flux de données multi-bitrate](media-services-live-streaming-with-onprem-encoders.md) pour en savoir plus sur les concepts et considérations liées à la diffusion en continu live avec les codeurs sur site et les canaux de transmission.

##<a name="to-view-notifications-and-errors"></a>Pour afficher les erreurs et notifications

Si vous souhaitez afficher les notifications et les erreurs produites par le portail Azure, cliquez sur l’icône de Notification.

![Notifications](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##<a name="configure-streaming-endpoints"></a>Configurer des points de terminaison en continu 

Media Services propose un emballage dynamique, ce qui vous permet de remettre votre MP4s multi-bitrate dans les formats de diffusion en continu suivants : MPEG tiret, TLS, diffusion en continu lisse ou HDS, sans avoir à recompresser dans ces formats de transmission en continu. Avec emballage dynamique vous suffit de stocker et de payer les fichiers au format de stockage unique et Media Services génère et remplit la réponse appropriée en fonction de la demande du client.

Pour tirer parti de l’emballage dynamique, vous devez obtenir au moins une unité de transmission en continu pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu.  

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, effectuez les opérations suivantes :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/).
1. Dans la fenêtre **paramètres** , cliquez sur **les points de terminaison en flux continu**. 

2. Cliquez sur la diffusion en continu de point de terminaison par défaut. 

    La fenêtre **Détails de point de terminaison de diffusion en continu par défaut** s’affiche.

3. Pour spécifier le nombre d’unités de transmission en continu, faites glisser le curseur de **l’unité de transmission en continu** .

    ![Unités de transmission en continu](./media/media-services-portal-passthrough-get-started/media-services-streaming-units.png)

4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

    >[AZURE.NOTE]L’allocation de toutes nouvelles unités peut prendre jusqu'à 20 minutes.
    
##<a name="create-and-start-pass-through-channels-and-events"></a>Créer et lancer des événements et des canaux de transmission

Un canal est associé à des événements/programmes qui vous permettent de contrôler la publication et le stockage de segments d’un flux en direct. Canaux de gérer les événements. 
    
Vous pouvez spécifier le nombre d’heures que vous souhaitez conserver le contenu enregistré pour le programme en définissant la longueur de la **Fenêtre d’Archive** . Cette valeur peut être définie à partir d’un minimum de 5 minutes à un maximum de 25 heures. Longueur de la fenêtre archives détermine également qu'autant de fois que les clients peut rechercher en arrière à partir de la position actuelle de live. Événements peuvent s’exécuter sur le laps de temps, mais contenu qui se situe derrière la longueur de la fenêtre est ignoré en permanence. La valeur de cette propriété détermine également la durée pendant laquelle les manifestes du client peuvent augmenter.

Chaque événement est associé à une immobilisation. Pour publier l’événement, vous devez créer un localisateur à la demande pour l’élément associé. Ayant ce repère vous permet de générer une URL de flux que vous fournissez à vos clients.

Un canal prend en charge les trois événements s’exécutant simultanément afin de pouvoir créer plusieurs archives du même flux de données entrante. Cela vous permet de publier et archiver des différentes parties d’un événement selon les besoins. Par exemple, vos besoins est d’archiver 6 heures d’un programme, mais à la dernières seulement 10 minutes de diffusion. Pour ce faire, vous devez créer deux programmes s’exécutant simultanément. Un programme est défini pour l’archivage de 6 heures de l’événement, mais le programme n’est pas publié. L’autre programme est défini pour l’archivage pendant 10 minutes et ce programme est publié.

Vous ne devez pas réutiliser les événements en direct existants. Au lieu de cela, de créer et de démarrer un nouvel événement pour chaque événement.

Démarrer l’événement lorsque vous êtes prêt à commencer la diffusion et l’archivage. Arrêter le programme chaque fois que vous souhaitez arrêter la diffusion et l’archivage de l’événement. 

Pour supprimer le contenu archivé, arrêter et supprimer l’événement puis supprimer l’actif associé. Une immobilisation ne peut pas être supprimée s’il est utilisé par un événement ; l’événement doit être d’abord supprimé. 

Même après l’arrêt et suppression de l’événement, les utilisateurs pourront diffuser vos contenus archivés sous la forme d’une vidéo à la demande, pour tant que vous ne supprimez pas la ressource.

Si vous souhaitez conserver le contenu archivé, mais n’est pas disponible pour la diffusion en continu, supprimer le localisateur de transmission en continu.

###<a name="to-use-the-portal-to-create-a-channel"></a>Pour utiliser le portail pour créer un canal 

Cette section indique comment utiliser l’option de **Création rapide** pour créer un canal direct.

Pour plus d’informations sur les canaux pass-through, consultez [Live en continu avec des codeurs sur site qui créent des flux de données multi-bitrate](media-services-live-streaming-with-onprem-encoders.md).

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **paramètres** , cliquez sur **Live en continu**. 

    ![Mise en route](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    La fenêtre de **diffusion en continu Live** s’affiche.

3. Cliquez sur **Création rapide** pour créer un canal direct avec le protocole RTMP protocole d’acquisition.

    La fenêtre **Créer un nouveau canal** apparaît.
4. Donnez un nom à la nouvelle chaîne, cliquez sur **créer**. 

    Cette opération crée un canal direct avec le protocole RTMP protocole d’acquisition.

##<a name="create-events"></a>Créer des événements

1. Sélectionner un canal auquel vous souhaitez ajouter un événement.
2. Appuyez sur le bouton **Événements de Live** .

![Événement](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)


##<a name="get-ingest-urls"></a>URL d’acquisition Get

Une fois que le canal est créé, vous pouvez obtenir les URL que vous souhaitez offrir à l’encodeur direct d’acquisition. Le codeur utilise ces URL à l’entrée d’un flux en direct.

![Créé](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##<a name="watch-the-event"></a>Regarder l’événement

Pour suivre l’événement, cliquez sur **Espion** dans le portail Azure ou copiez l’URL du flux et utilisez un lecteur de votre choix. 
 
![Créé](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Événement en direct sont automatiquement converties au contenu de la demande lors de l’arrêt.

##<a name="clean-up"></a>Nettoyer

Pour plus d’informations sur les canaux pass-through, consultez [Live en continu avec des codeurs sur site qui créent des flux de données multi-bitrate](media-services-live-streaming-with-onprem-encoders.md).

- Un canal peut être arrêté que lorsque tous les événements de programmes sur le canal ont été arrêtés.  Une fois que le canal est arrêté, il n’entraîne pas des frais. Lorsque vous devez le démarrer à nouveau, il aura le même URL d’acquisition afin que vous n’aurez pas besoin de reconfigurer votre codeur.
- Un canal peut être supprimé que lorsque tous les événements en direct sur le canal ont été supprimés.

##<a name="view-archived-content"></a>Permet d’afficher le contenu archivé

Même après l’arrêt et suppression de l’événement, les utilisateurs pourront diffuser vos contenus archivés sous la forme d’une vidéo à la demande, pour tant que vous ne supprimez pas la ressource. Une immobilisation ne peut pas être supprimée s’il est utilisé par un événement ; l’événement doit être d’abord supprimé. 

Pour gérer vos actifs, sélectionnez le **paramètre** , puis cliquez sur **ressources**.

![Actifs](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##<a name="next-step"></a>Étape suivante

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
