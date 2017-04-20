<properties 
    pageTitle="L’utilisation de castLabs pour remettre les licences de Widevine pour Azure Media Services | Microsoft Azure" 
    description="Cet article décrit comment vous pouvez utiliser Azure Media Services (AMS) pour fournir un flux qui est chiffré dynamiquement par AMS PlayReady et Widevine DRMs. La licence PlayReady provient de serveur de licences PlayReady des Services de support et Widevine licence est remis par le serveur de licences de castLabs." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="Mingfeiy;willzhan;Juliako"/>


#<a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>L’utilisation de castLabs pour remettre les licences de Widevine pour Azure Media Services

> [AZURE.SELECTOR]
- [Axinom](media-services-axinom-integration.md)
- [castLabs](media-services-castlabs-integration.md)

##<a name="overview"></a>Vue d’ensemble

Cet article décrit comment vous pouvez utiliser Azure Media Services (AMS) pour fournir un flux qui est chiffré dynamiquement par AMS PlayReady et Widevine DRMs. La licence PlayReady provient de serveur de licences PlayReady des Services de support et Widevine licence est remis par le serveur de licences de **castLabs** .

Pour la lecture en continu de contenu protégé par CENC (PlayReady et/ou Widevine), vous pouvez utiliser [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Pour plus d’informations, reportez-vous à la section [document de l’AMP](http://amp.azure.net/libs/amp/latest/docs/) .

Le diagramme suivant illustre un haut niveau Azure Media Services et l’architecture d’intégration castLabs.

![intégration](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##<a name="typical-system-set-up"></a>Configuration système par défaut

- Contenu multimédia est stocké dans l’AMS.
- ID de clé des clés de contenu sont stockées dans castLabs et AMS.
- castLabs et AMS sont jeton pour l’authentification intégré. Les sections suivantes traitent des jetons d’authentification. 
- Lorsque les demandes des clients vers le flux de la vidéo, le contenu est crypté dynamiquement avec **Cryptage commun** (CENC) et de dynamiquement AMS pour la diffusion en continu lisse et le tiret. Nous fournissons également le cryptage par flux élémentaire PlayReady M2TS protocole de diffusion en continu de TLS.
- Licences PlayReady est récupéré à partir du serveur de licences AMS et Widevine licence est récupéré à partir du serveur de licences de castLabs. 
- Media Player détermine automatiquement quelle licence pour extraire en fonction de la capacité de la plateforme client. 

##<a name="authentication-token-generation-for-getting-a-license"></a>Génération de jeton d’authentification pour l’obtention d’une licence

À la fois castLabs et AMS prend en charge le format du jeton JWT (JSON Web Token) utilisée pour autoriser une licence. 

###<a name="jwt-token-in-ams"></a>Jeton JWT dans l’AMS 

Le tableau suivant décrit le jeton JWT dans l’AMS. 

Émetteur|Chaîne de l’émetteur dans le choisi Service (jeton de sécurité)
---|---
Public|Chaîne d’audience à partir de l’enquête STS utilisé
Créances|Un ensemble de revendications
NotBefore|Démarrer la validité du jeton
Expire|Validité de fin du jeton
Valeur de SigningCredentials|La clé qui est partagée entre le serveur de licences PlayReady, castLabs License Server et SharePoint Team Services, il peut être symétrique ou asymétrique clé.

###<a name="jwt-token-in-castlabs"></a>Jeton JWT dans castLabs

Le tableau suivant décrit le jeton JWT dans castLabs. 

Nom|Description
---|---
optData|Une chaîne JSON contenant des informations sur vous. 
CRT|Une chaîne JSON contenant des informations sur l’actif, ses droits de lecture et les informations de licence.
IAT|Date et heure en cours dans l’époque.
JTI|Un identificateur unique sur ce jeton (chaque jeton peut être utilisé uniquement une fois dans le système de castLabs).

##<a name="sample-solution-set-up"></a>Exemple de solution configuré 

La [solution de l’échantillon](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) est constitué de deux projets :

-   Une application de console qui peut être utilisée pour définir des restrictions de DRM sur une immobilisation déjà ingérée, PlayReady et Widevine.
-   Une Application Web qui attribue des jetons, qui peuvent être considérées comme une version simplifiée très d’un STS.


Pour utiliser l’application de console :

1.  Modifier le fichier app.config pour configurer les informations d’identification de l’AMS, informations d’identification de castLabs, configuration de SharePoint Team Services et la clé partagée.
2.  Télécharger un bien dans l’AMS.
3.  Obtenir l’UUID à partir de la ressource téléchargée et modifiez les 32 de ligne dans le fichier Program.cs :

         var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.  Utilisez un AssetId pour nommer l’actif dans le système de castLabs (44 de ligne dans le fichier Program.cs).

    Vous devez définir AssetId pour **castLabs**; Il doit être une chaîne alphanumérique unique.

5.  Exécutez le programme.


Pour utiliser l’Application Web (STS) :

1.  Modifier le fichier web.config au commerçant de castlabs le programme d’installation ID, de la configuration de SharePoint Team Services et de la clé partagée.
2.  Déploiement de sites Web Azure.
3.  Accédez au site Web.

##<a name="playing-back-a-video"></a>Lecture d’une vidéo

Pour lire une vidéo cryptée avec le cryptage commun (PlayReady et/ou Widevine), vous pouvez utiliser le [Lecteur de Media Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Lorsque vous exécutez l’application console, l’ID de la clé de contenu et l’URL du manifeste sont répercutées.

1.  Ouvrir un nouvel onglet et lancer votre service SJS : http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.  Accédez au [lecteur Media Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.  Collez l’URL en continu.
4.  Cliquez sur la case à cocher **Options avancées** .
5.  Dans la liste déroulante **Protection** , sélectionnez PlayReady et/ou Widevine.
6.  Coller le jeton que vous avez obtenu à partir de celle présentée dans la zone de texte de jeton. 
    
    Le serveur de licences castLab n’a pas besoin du « porteur = » préfixe devant le jeton. Par conséquent, veuillez supprimer avant d’envoyer le jeton.
7.  Mettre à jour le lecteur.
8.  La vidéo doit jouer.


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
