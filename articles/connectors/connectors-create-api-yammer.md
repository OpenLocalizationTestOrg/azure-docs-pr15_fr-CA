<properties
pageTitle="Ajouter le connecteur Yammer dans vos applications de logique | Microsoft Azure"
description="Vue d’ensemble du connecteur Yammer avec les paramètres de l’API REST"
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

# <a name="get-started-with-the-yammer-connector"></a>Mise en route avec le connecteur de Yammer

Se connecter à Yammer de conversations d’accès dans votre réseau d’entreprise.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2015-08-01-aperçu.

Avec Yammer, vous pouvez :

- Créer votre flux d’activité basé sur les données que vous obtenez à partir de Yammer. 
- Utilisation déclenche lorsqu’il y a un nouveau message dans un groupe ou un flux à votre suivant.
- Utiliser des actions pour publier un message, obtenir tous les messages et bien plus encore. Ces actions Obtient une réponse et vérifiez la sortie disponible pour d’autres actions. Par exemple, lorsqu’un nouveau message s’affiche, vous pouvez envoyer un courrier électronique à l’aide d’Office 365.

Pour ajouter une opération dans les applications de la logique, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Yammer inclut les déclencheurs et les actions suivantes. 

Déclencheur | Actions
--- | ---
<ul><li>Lorsqu’il existe un message dans un groupe</li><li>Lorsqu’il y a un nouveau message dans mon suivant d’alimentation</li></ul>| <ul><li>Obtenir tous les messages</li><li>Obtient les messages dans un groupe</li><li>Obtient que les messages de mon suivant d’alimentation</li><li>Publier le message</li><li>Lorsqu’il existe un message dans un groupe</li><li>Lorsqu’il y a un nouveau message dans mon suivant d’alimentation</li></ul>

Tous les connecteurs prennent en charge les données au format JSON et XML. 

## <a name="create-a-connection-to-yammer"></a>Créer une connexion à Yammer
Pour utiliser le connecteur de Yammer, vous créez d’abord une **connexion** puis fournissez les détails de ces propriétés : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|Jeton|Oui|Fournir des informations d’identification de Yammer|

>[AZURE.INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications de logique.

## <a name="yammer-rest-api-reference"></a>Référence de l’API REST de Yammer
Cette documentation est de version : 1.0


### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>Obtenir tous les messages publics dans le réseau Yammer de l’utilisateur connecté
Correspond à des conversations « Tout » dans l’interface web de Yammer.  
```GET: /messages.json```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|older_then|nombre entier|aucune|requête|Aucun|Retourne les messages vieux de plus de l’ID de message spécifié comme une chaîne numérique. Cela est utile pour paginer des messages. Par exemple, si vous affichez actuellement 20 messages et que le plus ancien est le numéro 2912, vous pourriez ajouter « ? older_than = 2912″ à votre demande d’obtention de 20 messages antérieurs à ceux que vous voyez.|
|newer_then|nombre entier|aucune|requête|Aucun|Renvoie des messages plus récente que celle de l’ID de message spécifié comme une chaîne numérique. Il doit être utilisé lors de l’interrogation de nouveaux messages. Si vous examinez les messages et le message le plus récent retourné est 3516, vous pouvez faire une demande avec le paramètre « ? newer_than = 3516″ pour vous assurer que vous n’obtenez pas les doublons de messages déjà sur votre page.|
|limite de|nombre entier|aucune|requête|Aucun|Renvoyer uniquement le nombre spécifié de messages.|
|page|nombre entier|aucune|requête|Aucun|Obtenir la page spécifiée. Si retourné de données sont supérieures à la limite, vous pouvez utiliser ce champ pour accéder aux pages suivantes|


### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|408|Délai d’expiration de la demande|
|429|Trop de demandes|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|503|Yammer Service non disponible|
|504|Délai de la passerelle|
|par défaut|Échoué de l’opération.|


### <a name="post-a-message-to-a-group-or-all-company-feed"></a>Envoyer un Message à un groupe ou société de tous les flux
Si l’ID de groupe est fourni, le message est publié au groupe spécifié autre qu'il sera validée dans la société de tous les flux.    
```POST: /messages.json``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|entrée| |Oui|corps|Aucun|Demande de Message post|


### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|201|Créé|



## <a name="object-definitions"></a>Définitions d’objets

#### <a name="message-yammer-message"></a>Message : Message de Yammer

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|ID|nombre entier|aucune|
|content_excerpt|chaîne|aucune|
|sender_id|nombre entier|aucune|
|replied_to_id|nombre entier|aucune|
|created_at|chaîne|aucune|
|network_id|nombre entier|aucune|
|message_type|chaîne|aucune|
|sender_type|chaîne|aucune|
|URL|chaîne|aucune|
|web_url|chaîne|aucune|
|group_id|nombre entier|aucune|
|corps|non défini|aucune|
|thread_id|nombre entier|aucune|
|direct_message|valeur booléenne|aucune|
|client_type|chaîne|aucune|
|client_url|chaîne|aucune|
|langue|chaîne|aucune|
|notified_user_ids|tableau|aucune|
|confidentialité|chaîne|aucune|
|liked_by|non défini|aucune|
|system_message|valeur booléenne|aucune|

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest : Représente une demande post pour connecteur de Yammer publier sur yammer

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|corps|chaîne|Oui|
|group_id|nombre entier|aucune|
|replied_to_id|nombre entier|aucune|
|direct_to_id|nombre entier|aucune|
|diffusion|valeur booléenne|aucune|
|Topic1|chaîne|aucune|
|Topic2|chaîne|aucune|
|topic3|chaîne|aucune|
|topic4|chaîne|aucune|
|topic5|chaîne|aucune|
|topic6|chaîne|aucune|
|topic7|chaîne|aucune|
|topic8|chaîne|aucune|
|topic9|chaîne|aucune|
|rubrique10|chaîne|aucune|
|rubrique11|chaîne|aucune|
|topic12|chaîne|aucune|
|topic13|chaîne|aucune|
|topic14|chaîne|aucune|
|topic15|chaîne|aucune|
|topic16|chaîne|aucune|
|rubrique17|chaîne|aucune|
|rubrique18|chaîne|aucune|
|rubrique19|chaîne|aucune|
|rubrique20|chaîne|aucune|

#### <a name="messagelist-list-of-messages"></a>MessageList : La liste des messages

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|messages|tableau|aucune|


#### <a name="messagebody-message-body"></a>L’élément MessageBody : Corps de Message

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|analysé|chaîne|aucune|
|brut|chaîne|aucune|
|enrichi|chaîne|aucune|

#### <a name="likedby-liked-by"></a>LikedBy : Aimé par

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|nombre|nombre entier|aucune|
|noms|tableau|aucune|

#### <a name="yammmerentity-liked-by"></a>YammmerEntity : Aimé par

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|type de|chaîne|aucune|
|ID|nombre entier|aucune|
|full_name|chaîne|aucune|


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png
