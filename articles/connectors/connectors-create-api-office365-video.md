<properties
pageTitle="Utiliser le connecteur Office 365 vidéo dans vos applications de logique | Microsoft Azure"
description="Commencer à utiliser le connecteur Office 365 vidéo dans vos applications de logique de service Microsoft Azure App"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office365-video-connector"></a>Mise en route avec le connecteur vidéo d’Office 365
Se connecter à Office 365 vidéo pour obtenir des informations sur un Office 365 vidéo, obtenir la liste des vidéos et bien plus encore. Le connecteur de Office 365 vidéo peut être utilisé à partir de :

- Applications de logique 

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2015-08-01-aperçu. Ce connecteur n’est pas pris en charge sur les versions de schéma précédent.

Avec Office 365 la vidéo, vous pouvez :

- Créer votre flux d’activité basé sur les données que vous obtenez à partir de Office 365 vidéo. 
- Utiliser des actions pour vérifier l’état de portail vidéo, d’obtenir une liste de tous les vidéo dans un canal et bien plus encore. Ces actions Obtient une réponse et vérifiez la sortie disponible pour d’autres actions. Par exemple, vous pouvez utiliser le connecteur de recherche Bing pour rechercher des vidéos d’Office 365 et puis utiliser le connecteur vidéo de Office 365 pour obtenir des informations relatives à cette vidéo. Si la vidéo répond à vos besoins, vous pouvez valider cette vidéo sur Facebook. 

Pour ajouter une opération dans les applications de la logique, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur Office 365 vidéo a les actions suivantes disponibles. Il n’y a pas de déclencheurs.

| Déclencheurs | Actions|
| --- | --- |
| Aucun | <ul><li>Vérifie l’état de portail vidéo</li><li>Obtenir tous les canaux visibles</li><li>Obtenir l’url de la lecture du manifeste Azure Media Services pour une vidéo</li><li>Obtenir le jeton de support pour accéder à décrypter la vidéo</li><li>Obtient des informations sur un Office 365 particulier vidéo</li><li>Répertorie toutes les vidéos d’Office 365 dans un canal</li></ul>

Tous les connecteurs prennent en charge les données au format JSON et XML. 

## <a name="create-a-connection-to-office365-video-connector"></a>Créer une connexion au connecteur vidéo d’Office 365
Lorsque vous ajoutez ce connecteur à vos applications de logique, vous devez ouvrez une session dans votre compte Office 365 vidéo et autoriser les applications logique pour se connecter à votre compte.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Après avoir créé la connexion, vous saisissez les propriétés vidéo Office 365, telles que le nom du locataire ou d’ID du canal La **référence de l’API du reste** de cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion Office 365 vidéo dans les autres applications de logique.

## <a name="swagger-rest-api-reference"></a>Swagger de référence de l’API REST
S’applique à la version : 1.0.

### <a name="checks-video-portal-status"></a>Vérifie l’état de portail vidéo 
Vérifie l’état de portail vidéo pour voir si des services vidéo sont activées.  
```GET: /{tenant}/IsEnabled``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|clients|chaîne|Oui|chemin d’accès|Aucun|Le nom du locataire pour le répertoire de l’utilisateur fait partie de|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Non trouvé|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|



### <a name="get-all-viewable-channels"></a>Obtenir tous les canaux visibles 
Obtient tous les canaux, à que l’utilisateur a affichage accès.  
```GET: /{tenant}/Channels``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|clients|chaîne|Oui|chemin d’accès|Aucun|Le nom du locataire pour le répertoire de l’utilisateur fait partie de|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Non trouvé|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|




### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Répertorie toutes les vidéos d’Office 365 dans un canal 
Répertorie toutes les vidéos d’Office 365 dans un canal.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|clients|chaîne|Oui|chemin d’accès|Aucun|Le nom du locataire pour le répertoire de l’utilisateur fait partie de|
|channelId|chaîne|Oui|chemin d’accès|Aucun|L’id de canal à partir de laquelle vidéos doivent être extraites|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Non trouvé|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|




### <a name="gets-information-about-a-particular-office365-video"></a>Obtient des informations sur un Office 365 particulier vidéo 
Obtient des informations sur un Office 365 particulier vidéo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|clients|chaîne|Oui|chemin d’accès|Aucun|Le nom du locataire pour le répertoire de l’utilisateur fait partie de|
|channelId|chaîne|Oui|chemin d’accès|Aucun|L’id du canal|
|videoId|chaîne|Oui|chemin d’accès|Aucun|L’id de vidéo|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Non trouvé|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|




### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Obtenir l’url de la lecture du manifeste Azure Media Services pour une vidéo 
Obtenir l’url de lecture du manifeste Azure Media Services pour une vidéo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|clients|chaîne|Oui|chemin d’accès|Aucun|Le nom du locataire pour le répertoire de l’utilisateur fait partie de|
|channelId|chaîne|Oui|chemin d’accès|Aucun|L’id du canal|
|videoId|chaîne|Oui|chemin d’accès|Aucun|L’id de vidéo|
|streamingFormatType|chaîne|Oui|requête|Aucun|Type de format de transmission en continu. 1 - smooth Streaming ou MPEG-tiret. 0 - TLS en continu|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Non trouvé|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|




### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Obtenir le jeton de support pour accéder à décrypter la vidéo 
Obtenir le jeton de support pour accéder à décrypter la vidéo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|clients|chaîne|Oui|chemin d’accès|Aucun|Le nom du locataire pour le répertoire de l’utilisateur fait partie de|
|channelId|chaîne|Oui|chemin d’accès|Aucun|L’id du canal|
|videoId|chaîne|Oui|chemin d’accès|Aucun|L’id de vidéo|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Non trouvé|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="object-definitions"></a>Définitions d’objets

#### <a name="channel-channel-class"></a>Canal : Classe de canal

| Nom | Type de données | Obligatoire|
|---|---|---|
|ID|chaîne|aucune|
|Titre|chaîne|aucune|
|Description|chaîne|aucune|


#### <a name="video"></a>Vidéo 

| Nom | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|aucune|
|Titre|chaîne|aucune|
|Description|chaîne|aucune|
|CreationDate|chaîne|aucune|
|Propriétaire|chaîne|aucune|
|ThumbnailUrl|chaîne|aucune|
|VideoUrl|chaîne|aucune|
|VideoDuration|nombre entier|aucune|
|VideoProcessingStatus|nombre entier|aucune|
|ViewCount|nombre entier|aucune|


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).
