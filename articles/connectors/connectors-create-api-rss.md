<properties
pageTitle="RSS | Microsoft Azure"
description="Permet de créer des applications de logique avec le service d’application d’Azure. Connecteur RSS permet aux utilisateurs de publier et de récupérer des éléments de flux. Il permet également aux utilisateurs de le déclencher lorsqu’un nouvel élément est publié sur le flux des opérations."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-rss-connector"></a>Mise en route avec le connecteur RSS
RSS est un format de syndication web les plus courants utilisé pour publier du contenu fréquemment mis à jour – comme les entrées de blog et les titres des actualités.  De nombreux éditeurs de contenu fournissent un flux RSS pour permettre aux utilisateurs d’y souscrire.  Utilisez le lien RSS pour récupérer des flux d’informations et alimentation lorsque de nouveaux éléments sont publiées dans un flux RSS.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2015-08-01-aperçu. 

Vous pouvez commencer par la création d’une application logique maintenant, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur RSS peut être utilisé en tant qu’action ; Il a l’ou les déclencheurs. Tous les connecteurs prennent en charge les données au format JSON et XML. 

 Le lien RSS a les actions et/ou les déclencheurs disponibles suivants :

### <a name="rss-actions"></a>Actions de RSS
Vous pouvez effectuer ces actions :

|Action|Description|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|Obtenir tous les flux RSS éléments.|
### <a name="rss-triggers"></a>Déclencheurs RSS
Vous pouvez écouter ces événements :

|Déclencheur | Description|
|--- | ---|
|Lorsqu’un nouvel élément d’alimentation publié|Déclenche un flux de travail lors de la publication d’une nouvelle alimentation|


## <a name="create-a-connection-to-rss"></a>Créer une connexion à RSS

>[AZURE.INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications de logique.

## <a name="reference-for-rss"></a>Référence de RSS
S’applique à la version : 1.0

## <a name="onnewfeed"></a>OnNewFeed
Lorsqu’un nouvel élément d’alimentation publié : déclenche un flux de travail lors de la publication d’une nouvelle alimentation 

```GET: /OnNewFeed``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|feedUrl|chaîne|Oui|requête|Aucun|Url du flux|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|202|Accepté|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="listfeeditems"></a>ListFeedItems
Liste de tous les flux RSS éléments. : obtenir tous les flux RSS éléments. 

```GET: /ListFeedItems``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|feedUrl|chaîne|Oui|requête|Aucun|Url du flux|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|202|Accepté|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="object-definitions"></a>Définitions d’objets 

### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse [FeedItem]


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="feeditem"></a>FeedItem


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|chaîne|Oui |
|titre|chaîne|Oui |
|contenu|chaîne|Oui |
|liens|tableau|N° |
|updatedOn|chaîne|N° |


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)