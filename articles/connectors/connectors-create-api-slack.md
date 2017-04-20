<properties
pageTitle=" Utiliser le connecteur de marge dans vos applications de logique | Microsoft Azure"
description="Commencer à utiliser le connecteur de la marge dans vos applications Microsoft Azure Application Service logique"
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

# <a name="get-started-with-the-slack-connector"></a>Mise en route avec le connecteur de marge

La marge est un outil de communication d’équipe, qui réunit toutes les communications de votre équipe dans un placent instantanément consultable et disponible partout où vous allez.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2015-08-01-aperçu.

Avec le connecteur de marge, vous pouvez :

* Il permet de créer des applications de logique

Pour ajouter une opération dans les applications de la logique, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Parlons de déclencheurs et actions

Le connecteur de marge peut être utilisé en tant qu’action ; Il n’y a pas de déclencheurs. Tous les connecteurs prennent en charge les données au format JSON et XML. 

 Le connecteur de marge a les actions et/ou les déclencheurs disponibles suivants :

### <a name="slack-actions"></a>Actions de marge
Vous pouvez effectuer ces actions :

|Action|Description|
|--- | ---|
|PostMessage|Envoyer un Message à une couche spécifique.|
## <a name="create-a-connection-to-slack"></a>Créer une connexion à la marge
Pour utiliser le connecteur de marge, vous créez d’abord une **connexion** puis fournissez les détails de ces propriétés : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|Jeton|Oui|Fournir des informations d’identification de marges|

Suivez ces étapes pour vous connecter à la marge et terminer la configuration de la **connexion** de marge dans votre logique d’application :

1. Sélectionnez la **périodicité**
2. Sélectionnez une **fréquence** , puis entrez un **intervalle**
3. Sélectionnez **Ajouter une action**  
![Configurer la marge][1]  
4. Permet d’entrer la marge dans la zone Rechercher et attendez que la recherche de renvoyer toutes les entrées avec une marge dans le nom
5. Sélectionnez **marge - publier le message**
6. Sélectionnez **se connecter à la marge**:  
![Configurer la marge][2]
7. Fournir vos informations d’identification de marges pour vous connecter à autoriser l’application    
![Configurer la marge][3]  
8. Vous serez redirigé vers la page d’accès de votre organisation. **Autoriser** Marge d’interagir avec votre application logique :      
![Configurer la marge][5] 
9. À l’issue de l’autorisation, vous serez redirigé vers votre application logique pour la compléter en configurant la section **marge - obtenir tous les messages** . Ajouter d’autres déclencheurs et les actions dont vous avez besoin.  
![Configurer la marge][6]
10. Enregistrez votre travail en sélectionnant **Enregistrer** dans la barre de menu ci-dessus.


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications de logique.

## <a name="slack-rest-api-reference"></a>Référence des API du reste de marge
#### <a name="this-documentation-is-for-version-10"></a>Cette documentation est de version : 1.0


### <a name="post-a-message-to-a-specified-channel"></a>Envoyer un Message à une couche spécifique.
**```POST: /chat.postMessage```** 



| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|canal|chaîne|Oui|requête|Aucun|Canal, groupe privé ou canal de messagerie instantanée pour envoyer le message. Peut être un nom (ex : #general) ou un ID de codé.|
|texte|chaîne|Oui|requête|Aucun|Texte du message à envoyer. Pour plus d’options de mise en forme, consultez https://api.slack.com/docs/formatting.|
|nom d’utilisateur|chaîne|aucune|requête|Aucun|Nom du bot|
|as_user|valeur booléenne|aucune|requête|Aucun|Transmettre true pour publier le message que l’utilisateur authentifié, au lieu d’en robot|
|analyser|chaîne|aucune|requête|Aucun|Modifier la façon dont sont traités les messages. Pour plus d’informations, consultez https://api.slack.com/docs/formatting.|
|link_names|nombre entier|aucune|requête|Aucun|Rechercher et lier les noms de canaux et de noms d’utilisateurs.|
|unfurl_links|valeur booléenne|aucune|requête|Aucun|Transmettre true pour activer ferlage de contenu de type texte principalement.|
|unfurl_media|valeur booléenne|aucune|requête|Aucun|Transmettez false pour désactiver ferlage du contenu multimédia.|
|icon_url|chaîne|aucune|requête|Aucun|URL vers une image à utiliser en tant qu’icône pour ce message|
|icon_emoji|chaîne|aucune|requête|Aucun|Emoji à utiliser sous forme d’icône pour ce message|


### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|408|Délai d’expiration de la demande|
|429|Trop de demandes|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|503|Marge de Service non disponible|
|504|Délai de la passerelle|
|par défaut|Échoué de l’opération.|
------



## <a name="object-definitions"></a>Définitions de l’objet : 

 **Message**: Message de Yammer

Propriétés requises pour le Message :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|ID|nombre entier|
|content_excerpt|chaîne|
|sender_id|nombre entier|
|replied_to_id|nombre entier|
|created_at|chaîne|
|network_id|nombre entier|
|message_type|chaîne|
|sender_type|chaîne|
|URL|chaîne|
|web_url|chaîne|
|group_id|nombre entier|
|corps|non défini|
|thread_id|nombre entier|
|direct_message|valeur booléenne|
|client_type|chaîne|
|client_url|chaîne|
|langue|chaîne|
|notified_user_ids|tableau|
|confidentialité|chaîne|
|liked_by|non défini|
|system_message|valeur booléenne|



 **PostOperationRequest**: représente une demande post pour connecteur de Yammer publier sur yammer

Propriétés requises pour PostOperationRequest :

corps

**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|corps|chaîne|
|group_id|nombre entier|
|replied_to_id|nombre entier|
|direct_to_id|nombre entier|
|diffusion|valeur booléenne|
|Topic1|chaîne|
|Topic2|chaîne|
|topic3|chaîne|
|topic4|chaîne|
|topic5|chaîne|
|topic6|chaîne|
|topic7|chaîne|
|topic8|chaîne|
|topic9|chaîne|
|rubrique10|chaîne|
|rubrique11|chaîne|
|topic12|chaîne|
|topic13|chaîne|
|topic14|chaîne|
|topic15|chaîne|
|topic16|chaîne|
|rubrique17|chaîne|
|rubrique18|chaîne|
|rubrique19|chaîne|
|rubrique20|chaîne|



 **MessageList**: la liste des messages

Propriétés requises pour MessageList :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|messages|tableau|



 **L’élément MessageBody**: corps du Message

Propriétés requises pour l’élément MessageBody :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|analysé|chaîne|
|brut|chaîne|
|enrichi|chaîne|



 **LikedBy**: aimé par

Propriétés requises pour LikedBy :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|nombre|nombre entier|
|noms|tableau|



 **YammmerEntity**: aimé par

Propriétés requises pour YammmerEntity :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|type de|chaîne|
|ID|nombre entier|
|full_name|chaîne|


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
## <a name="object-definitions"></a>Définitions de l’objet : 

 **WebResultModel**: les résultats de recherche Bing

Propriétés requises pour WebResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Titre|chaîne|
|Description|chaîne|
|DisplayUrl|chaîne|
|ID|chaîne|
|FullUrl|chaîne|



 **VideoResultModel**: résultats de la recherche vidéo Bing

Propriétés requises pour VideoResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Titre|chaîne|
|DisplayUrl|chaîne|
|ID|chaîne|
|MediaUrl|chaîne|
|Moment de l’exécution|nombre entier|
|Miniature|non défini|



 **ThumbnailModel**: les propriétés de miniature de l’élément multimédia

Propriétés requises pour ThumbnailModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|MediaUrl|chaîne|
|ContentType|chaîne|
|Largeur|nombre entier|
|Hauteur|nombre entier|
|Taille du fichier|nombre entier|



 **ImageResultModel**: résultats de la recherche Bing image

Propriétés requises pour ImageResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Titre|chaîne|
|DisplayUrl|chaîne|
|ID|chaîne|
|MediaUrl|chaîne|
|SourceUrl|chaîne|
|Miniature|non défini|



 **NewsResultModel**: résultats de la recherche Bing news

Propriétés requises pour NewsResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Titre|chaîne|
|Description|chaîne|
|DisplayUrl|chaîne|
|ID|chaîne|
|Source|chaîne|
|Date|chaîne|



 **SpellResultModel**: résultats de suggestions d’orthographe Bing

Propriétés requises pour SpellResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|ID|chaîne|
|Valeur|chaîne|



 **RelatedSearchResultModel**: Bing concernant les résultats de la recherche

Propriétés requises pour RelatedSearchResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Titre|chaîne|
|ID|chaîne|
|BingUrl|chaîne|



 **CompositeSearchResultModel**: résultats de la recherche composite Bing

Propriétés requises pour CompositeSearchResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|WebResultsTotal|nombre entier|
|ImageResultsTotal|nombre entier|
|VideoResultsTotal|nombre entier|
|NewsResultsTotal|nombre entier|
|SpellSuggestionsTotal|nombre entier|
|WebResults|tableau|
|ImageResults|tableau|
|VideoResults|tableau|
|NewsResults|tableau|
|SpellSuggestionResults|tableau|
|RelatedSearchResults|tableau|


## <a name="object-definitions"></a>Définitions de l’objet : 

 **PostOperationResponse**: représente la réponse de l’opération de post du connecteur de la marge pour la validation de la marge

Propriétés requises pour PostOperationResponse :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Bien|valeur booléenne|
|canal|chaîne|
|TS|chaîne|
|Message|non défini|
|erreur|chaîne|



 **MessageItem ne**: un message de canal.

Propriétés requises pour MessageItem ne :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|texte|chaîne|
|ID|chaîne|
|utilisateur|chaîne|
|créé|nombre entier|
|is_user-supprimé|valeur booléenne|


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
