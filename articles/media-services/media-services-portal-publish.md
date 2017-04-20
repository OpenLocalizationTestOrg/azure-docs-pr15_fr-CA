<properties
    pageTitle="  Publier du contenu avec le portail Azure | Microsoft Azure"
    description="Ce didacticiel vous guide tout au long de la procédure de publication de votre contenu avec le portail Azure."
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
    ms.date="10/24/2016"
    ms.author="juliako"/>

# <a name="publish-content-with-the-azure-portal"></a>Publier du contenu avec le portail Azure

> [AZURE.SELECTOR]
- [Portail](media-services-portal-publish.md)
- [.NET](media-services-deliver-streaming-content.md)
- [RESTE](media-services-rest-deliver-streaming-content.md)

## <a name="overview"></a>Vue d’ensemble

> [AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/). 

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

Pour plus d’informations, consultez [vue d’ensemble du contenu de remise](media-services-deliver-content-overview.md).

>[AZURE.NOTE] Si vous avez utilisé le portail pour créer des repères avant mars 2015, les localisateurs avec une date d’expiration de deux ans ont été créés.  

Pour mettre à jour une date d’expiration sur un repère, utilisez [repos](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou à l’API [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Notez que lorsque vous mettez à jour la date d’expiration d’un localisateur SAS, l’URL change.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Pour utiliser le portail pour publier une immobilisation

Pour utiliser le portail pour publier une immobilisation, effectuez les opérations suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
1. Sélectionnez **paramètres** > **actifs**.
1. Sélectionnez l’élément que vous souhaitez publier.
1. Cliquez sur le bouton **Publier** .
1. Sélectionnez le type de localisateur.
2. Appuyez sur **Ajouter**.

    ![Publier](./media/media-services-portal-vod-get-started/media-services-publish1.png)

L’URL sera ajouté à la liste des **URL de publication**.

## <a name="play-content-from-the-portal"></a>Lire le contenu à partir du portail

Le portail Azure fournit un lecteur de contenu que vous pouvez utiliser pour tester votre vidéo.

Cliquez sur la vidéo souhaitée et puis cliquez sur le bouton **lecture** .

![Publier](./media/media-services-portal-vod-get-started/media-services-play.png)

Certaines considérations s’appliquent :

- Vérifiez que la vidéo a été publiée.
- Ce **lecteur multimédia** est lu à partir de la valeur par défaut de diffusion en continu de point de terminaison. Si vous souhaitez lire à partir d’un point de terminaison de diffusion par défaut, cliquez sur pour copier l’URL et d’utiliser un autre lecteur. Par exemple, [Le lecteur Media Services Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
- Le point de terminaison en continu à partir de laquelle vous transmettez en continu doit être exécuté.  
- Pour les flux de données à partir d’un point de terminaison en continu, vous devez ajouter au moins une unité de transmission en continu. Pour plus d’informations, consultez [cette](media-services-portal-scale-streaming-endpoints.md) rubrique.   

##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


