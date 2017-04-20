<properties
    pageTitle="Vue d’ensemble de l’empaquetage dynamique | Microsoft Azure"
    description="La rubrique donne et une vue d’ensemble de l’emballage dynamique."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


# <a name="dynamic-packaging"></a>Emballage dynamique

##<a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services peut être utilisés pour offrir de nombreux médias source fichier formats, formats de diffusion multimédia en continu et de protection de contenu met en forme une variété de technologies client (par exemple, iOS, XBOX, Silverlight, Windows 8). Ces clients comprennent les différents protocoles, par exemple iOS requiert un format V4 de diffusion en continu Live HTTP (TLS) et Silverlight et Xbox nécessitent de diffusion en continu lisse. Si vous avez un ensemble de débit adaptatif (multi-débit) MP4 fichiers (multimédias de Base ISO 14496-12) ou un ensemble de fichiers de diffusion en continu lisse adaptive de vitesse de transmission que vous souhaitez servir aux clients qui comprennent MPEG tiret, TLS ou diffusion en continu lisse, vous devez tirer parti de l’emballage dynamique de Media Services.

Avec emballage dynamique vous suffit pour créer une immobilisation qui contient un ensemble de fichiers de vitesse de transmission adaptive MP4 ou les fichiers de diffusion en continu lisse de débit adaptatif. Puis, selon le format spécifié dans la demande de manifeste ou d’un fragment, la diffusion en continu de la demande serveur garantissent que le flux du protocole que vous avez choisi. Par conséquent, il vous suffit de stocker et de payer pour les fichiers au format de stockage unique et service Media Services pour la génération et fournir la réponse appropriée en fonction de la demande du client.

Le diagramme suivant illustre le codage traditionnel et le workflow d’emballage statique.

![Codage statique](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Le diagramme suivant illustre le flux de travail d’emballage dynamique.

![Codage dynamique](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Pour tirer parti de l’emballage dynamique, vous devez d’abord obtenir au moins une unité de transmission en continu à la demande pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu. Pour plus d’informations, consultez [comment les Services de support d’échelle](media-services-portal-manage-streaming-endpoints.md).

##<a name="common-scenario"></a>Scénario courant

1. Télécharger un fichier d’entrée (appelé fichier mezzanine). Par exemple, H.264, MP4 ou WMV (pour la liste des [Formats pris en charge par la norme Media Encoder](media-services-media-encoder-standard-formats.md), consultez formats pris en charge.

1. Coder votre fichier mezzanine pour ensembles de vitesse de transmission adaptive H.264 MP4.

1. Publier la ressource qui contient la vitesse de transmission adaptive MP4 définie en créant le localisateur à la demande.

1. Générer des URL pour accéder et votre contenu de flux en continu.


##<a name="preparing-assets-for-dynamic-streaming"></a>Préparation pour le flux dynamique des ressources

Pour préparer votre actif pour le flux dynamique, vous avez deux options :

1. [Télécharger un fichier maître](media-services-dotnet-upload-files.md).
2. [Utilisez le codeur Media Encoder Standard pour produire des ensembles de vitesse de transmission adaptive H.264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Le contenu de flux](media-services-deliver-content-overview.md).


##<a id="unsupported_formats"></a>Formats qui ne sont pas pris en charge par emballage dynamique

Les formats de fichier source suivants ne sont pas pris en charge par emballage dynamique.

- Fichiers mp4 numérique Dolby.
- Dolby digital lisse les fichiers.

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
