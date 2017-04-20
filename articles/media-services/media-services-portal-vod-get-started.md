<properties
    pageTitle=" Mise en route de fourniture de contenu à la demande via le portail Azure | Microsoft Azure"
    description="Ce didacticiel vous guide à travers les étapes de l’implémentation d’un service de diffusion de contenu vidéo à la demande (VoD) base avec application Azure Media Services (AMS) via le portail Azure."
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
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Mise en route de fourniture de contenu à la demande via le portail Azure

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Ce didacticiel vous guide à travers les étapes de l’implémentation d’un service de diffusion de contenu vidéo à la demande (VoD) base avec application Azure Media Services (AMS) via le portail Azure.

> [AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/). 

Ce didacticiel inclut les tâches suivantes :

1.  Créez un compte Azure Media Services.
2.  Configurer le point de terminaison en continu.
1.  Télécharger un fichier vidéo.
1.  Encoder le fichier source dans un ensemble de fichiers MP4 de débit adaptatif.
1.  Publier l’actif et le get de diffusion en continu et l’URL de téléchargement progressif.  
1.  Lire votre contenu.


## <a name="create-an-azure-media-services-account"></a>Créer un compte Azure Media Services

Les étapes de cette section montrent comment créer un compte de l’AMS.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ nouvelle** > **Web + Mobile** > **Media Services**.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Dans **Créer un compte de SERVICES de support** , entrez les valeurs requises.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Dans la zone **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte de Services de support est de tous les nombres en minuscules ou lettres sans espace et 3 à 24 caractères.
    2. Abonnement, sélectionnez parmi les différents abonnements Azure auxquelles vous avez accès à.
    
    2. Dans le **Groupe de ressources**, sélectionnez la ressource nouvelle ou existante.  Un groupe de ressources est un ensemble de ressources qui partagent le cycle de vie, les autorisations et les stratégies. Pour en savoir plus [ici](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Dans **emplacement**, sélectionnez la région géographique est utilisé pour stocker les enregistrements des supports et des métadonnées pour votre compte de Services de support. Cette zone est utilisée pour traiter et de diffuser vos médias. Seules les régions de Media Services disponibles s’affichent dans la zone de liste déroulante. 
    
    3. Dans le **Compte de stockage**, sélectionnez un compte de stockage pour fournir le stockage blob du contenu multimédia à partir de votre compte de Services de support. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique, comme votre compte de Services de support, ou vous pouvez créer un compte de stockage. Un nouveau compte de stockage est créé dans la même région. Les règles pour les noms de compte de stockage sont les mêmes que pour les comptes de Services de support.

        En savoir plus sur le stockage [ici](storage-introduction.md).

    4. Sélectionnez **Ajouter au tableau de bord** pour voir la progression du déploiement compte.
    
7. Cliquez sur **créer** en bas de l’écran.

    Une fois que le compte est créé avec succès, le statut devient **en cours d’exécution**. 

    ![Paramètres des Services de support](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Pour gérer votre compte AMS (par exemple, télécharger des vidéos, coder actifs, surveiller la progression de la tâche) utilisez la fenêtre **paramètres** .

## <a name="manage-keys"></a>La gestion des clés

Vous devez le nom de compte et les informations de clé primaire pour accéder par programme le compte des Services de support.

1. Dans le portail Azure, sélectionnez votre compte. 

    La fenêtre **paramètres** s’affiche sur la droite. 

2. Dans la fenêtre **paramètres** , sélectionnez **clés**. 

    **Gérer les clés** windows affiche le nom de compte et les clés primaires et secondaires s’affiche. 
3. Appuyez sur le bouton Copier pour copier les valeurs.
    
    ![Les clés des Services multimédia](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="configure-streaming-endpoints"></a>Configurer des points de terminaison en continu

Lorsque vous travaillez avec Azure Media Services est un des scénarios plus courants est offrant des performances vidéo via adaptive vitesse de transmission en continu à vos clients. Media Services prend en charge la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement).

Media Services fournit un emballage dynamique, ce qui vous permet de remettre votre contenu MP4 encodé en continu des formats pris en charge par les Services de support (MPEG tiret, TLS, diffusion en continu lisse, HDS) juste-à-temps, sans avoir à stocker des versions intégrées de chacun de ces formats de transmission en continu de vitesse de transmission adaptive.

Pour tirer parti de l’emballage dynamique, vous devez effectuer les opérations suivantes :

- Coder votre fichier mezzanine (source) dans un ensemble de fichiers MP4 de débit adaptatif (les étapes de codage sont expliqués plus loin dans ce didacticiel).  
- Créer au moins une unité de transmission en continu pour le *point de terminaison de la diffusion en continu* à partir de laquelle vous envisagez de livraison votre contenu. Les étapes ci-dessous montrent comment modifier le nombre d’unités de transmission en continu.

Avec emballage dynamique, il vous suffit de stocker et de payer pour les fichiers au format de stockage unique et Media Services génère et remplit la réponse appropriée en fonction de la demande du client.

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, effectuez les opérations suivantes :


1. Dans la fenêtre **paramètres** , cliquez sur **les points de terminaison en flux continu**. 

2. Cliquez sur la valeur par défaut de diffusion en continu de point de terminaison. 

    La fenêtre **Détails de point de terminaison de diffusion en continu par défaut** s’affiche.

3. Pour spécifier le nombre d’unités de transmission en continu, faites glisser le curseur de **l’unité de transmission en continu** .

    ![Unités de transmission en continu](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

    >[AZURE.NOTE]L’allocation de toutes nouvelles unités peut prendre jusqu'à 20 minutes.

## <a name="upload-files"></a>Télécharger des fichiers

Pour les flux vidéos à l’aide de Azure Media Services, vous devez télécharger les vidéos source, de les coder dans plusieurs vitesses de transmission et de publier le résultat. La première étape est décrite dans cette section. 

1. Dans la fenêtre **configuration** , cliquez sur **ressources**.

    ![Télécharger des fichiers](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Cliquez sur le bouton **Télécharger** .

    La fenêtre **télécharger un élément vidéo** s’affiche.

    >[AZURE.NOTE] Il n’y a aucune limitation de taille de fichier.
    
4. Accédez à la vidéo de votre choix sur votre ordinateur, sélectionnez-le et appuyez sur OK.  

    Le téléchargement démarre et vous pouvez voir la progression sous le nom de fichier.  

Une fois le téléchargement terminé, vous voyez la nouvelle immobilisation répertoriée dans la fenêtre **d’actifs** . 

## <a name="encode-assets"></a>Coder des actifs

Lorsque vous travaillez avec Azure Media Services est un des scénarios plus courants remet adaptive vitesse de transmission en continu à vos clients. Media Services prend en charge la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement). Pour préparer vos vidéos pour la diffusion en continu de la vitesse de transmission adaptative, vous devez encoder votre vidéo source dans des fichiers multi-bitrate. Vous devez utiliser l’encodeur **Standard de Media Encoder** pour encoder votre vidéo.  

Media Services fournit également un emballage dynamique, ce qui vous permet de remettre votre MP4s multi-bitrate dans les formats de diffusion en continu suivants : MPEG tiret, TLS, diffusion en continu lisse ou HDS, sans avoir à recompresser dans ces formats de transmission en continu. Avec emballage dynamique, il vous suffit de stocker et de payer pour les fichiers au format de stockage unique et Media Services génère et remplit la réponse appropriée en fonction de la demande du client.

Pour tirer parti de l’emballage dynamique, vous devez effectuer les opérations suivantes :

- Coder votre fichier source dans un ensemble de fichiers MP4 de multi-bitrate (les étapes de codage sont expliqués plus loin dans cette section).
- Obtenir au moins une unité de transmission en continu pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu. Pour plus d’informations, consultez [configuration de points de terminaison en continu](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### <a name="to-use-the-portal-to-encode"></a>Pour utiliser le portail pour coder

Cette section décrit les étapes à que suivre pour coder votre contenu avec Media Encoder Standard.

1.  Dans la fenêtre **paramètres** , sélectionnez **ressources**.  
2.  Dans la fenêtre **d’actifs** , sélectionnez l’élément que vous voulez encoder.
3.  Appuyez sur le bouton **Encoder** .
4.  Dans la fenêtre de **coder un actif** , sélectionnez le processeur « Media Encoder Standard » et un paramètre prédéfini. Par exemple, si vous connaissez votre vidéo d’entrée a une résolution de 1920 x 1080 pixels, vous pouvez utiliser le « H264 débit plusieurs 1080p » prédéfini. Pour plus d’informations sur les présélections, voir [cet](https://msdn.microsoft.com/library/azure/mt269960.aspx) article – il est important de sélectionner le paramètre prédéfini qui convient le mieux pour votre vidéo d’entrée. Si vous avez une vidéo de basse résolution (640 x 360), puis vous ne devez pas utiliser la valeur par défaut « H264 débit plusieurs 1080p » prédéfini.
    
    Pour faciliter la gestion, vous pouvez modifier le nom de l’élément de sortie et le nom de la tâche.
        
    ![Coder des actifs](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Appuyez sur **créer**.

### <a name="monitor-encoding-job-progress"></a>Surveiller la progression de tâche codage

Pour surveiller la progression de la tâche de codage, cliquez sur **paramètres** (en haut de la page) et cliquez sur **travaux**.

![Travaux](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publier du contenu

Pour fournir aux utilisateurs une URL qui peut être utilisée pour transmettre en continu ou télécharger votre contenu, vous devez d’abord « publier » sur vos ressources en créant un localisateur. Localisateurs de fournissent l’accès aux fichiers contenus dans l’immobilisation. Media Services prend en charge deux types de localisateurs : 

- Diffusion en continu de localisateurs (OnDemandOrigin), utilisés pour la diffusion adaptative en continu (par exemple, à des flux MPEG tiret, TLS ou diffusion en continu lisse). Pour créer un localisateur de transmission en continu de votre actif doit contenir un fichier .ism. 
- Progressifs locators (SAS), utilisés pour la remise de video via téléchargement progressif.


Une URL de transmission en continu a le format suivant, et vous pouvez l’utiliser pour lire les ressources de diffusion en continu lisse.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Pour générer une URL de transmission en continu de TLS, ajouter (format = m3u8-aapl) à l’adresse URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Pour générer un tiret MPEG URL de diffusion en continu, vous devez ajouter (format = mpd-heure-csf) à l’adresse URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Une URL de SAS a le format suivant.

    {blob container name}/{asset name}/{file name}/{SAS signature}

>[AZURE.NOTE] Si vous avez utilisé le portail pour créer des repères avant mars 2015, les localisateurs avec une date d’expiration de deux ans ont été créés.  

Pour mettre à jour une date d’expiration sur un repère, utilisez [repos](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou à l’API [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Lorsque vous mettez à jour la date d’expiration d’un localisateur SAS, les modifications de l’URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Pour utiliser le portail pour publier une immobilisation

Pour utiliser le portail pour publier une immobilisation, effectuez les opérations suivantes :

1. Sélectionnez **paramètres** > **actifs**.
1. Sélectionnez l’élément que vous souhaitez publier.
1. Cliquez sur le bouton **Publier** .
1. Sélectionnez le type de localisateur.
2. Appuyez sur **Ajouter**.

    ![Publier](./media/media-services-portal-vod-get-started/media-services-publish1.png)

L’URL est ajoutée à la liste des **URL de publication**.

## <a name="play-content-from-the-portal"></a>Lire le contenu à partir du portail

Le portail Azure fournit un lecteur de contenu que vous pouvez utiliser pour tester votre vidéo.

Cliquez sur la vidéo souhaitée et puis cliquez sur le bouton **lecture** .

![Publier](./media/media-services-portal-vod-get-started/media-services-play.png)

Certaines considérations s’appliquent :

- Vérifiez que la vidéo a été publiée.
- Ce **lecteur multimédia** est lu à partir de la valeur par défaut de diffusion en continu de point de terminaison. Si vous souhaitez lire à partir d’un point de terminaison de diffusion par défaut, cliquez sur pour copier l’URL et d’utiliser un autre lecteur. Par exemple, [Le lecteur Media Services Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


