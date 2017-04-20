<properties
pageTitle="SendGrid | Microsoft Azure"
description="Permet de créer des applications de logique avec le service d’application d’Azure. Fournisseur de SendGrid de connexion vous permet d’envoyer du courrier électronique et de gérer des listes de destinataires."
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

# <a name="get-started-with-the-sendgrid-connector"></a>Mise en route avec le connecteur de SendGrid

Fournisseur de SendGrid de connexion vous permet d’envoyer du courrier électronique et de gérer des listes de destinataires.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2015-08-01-aperçu. 

Vous pouvez commencer par la création d’une application logique maintenant, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur de SendGrid peut être utilisé en tant qu’action ; Il a l’ou les déclencheurs. Tous les connecteurs prennent en charge les données au format JSON et XML. 

 Le connecteur de SendGrid a les actions suivantes disponibles. Il n’y a pas de déclencheurs.

### <a name="sendgrid-actions"></a>Actions de SendGrid
Vous pouvez effectuer ces actions :

|Action|Description|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|Envoie un e-mail à l’aide de l’API SendGrid (limité à 10 000 destinataires)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Ajouter un destinataire à une liste de destinataires|


## <a name="create-a-connection-to-sendgrid"></a>Créer une connexion à SendGrid
Pour créer des applications de logique avec SendGrid, vous devez d’abord créer une **connexion** puis fournir les détails pour les propriétés suivantes : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|ApiKey|Oui|Fournir votre clé d’Api SendGrid|
 

>[AZURE.INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications de logique.

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et d’écouter pour les déclencheurs décrites dans cet article.

## <a name="reference-for-sendgrid"></a>Référence SendGrid
S’applique à la version : 1.0

## <a name="sendemail"></a>SendEmail
Envoyer un e-mail : envoie un message électronique à l’aide de l’API SendGrid (limité à 10 000 destinataires) 

```POST: /api/mail.send.json``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|demande| |Oui|corps|Aucun|Message à envoyer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|429|Trop de demande|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="addrecipienttolist"></a>AddRecipientToList
Ajouter le destinataire à la liste : ajouter un destinataire à une liste de destinataires 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|listId|chaîne|Oui|chemin d’accès|Aucun|Id unique de la liste de destinataires|
|recipientId|chaîne|Oui|chemin d’accès|Aucun|Id unique du destinataire|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="object-definitions"></a>Définitions d’objets 

### <a name="emailrequest"></a>EmailRequest


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|De|chaîne|Oui |
|FromName|chaîne|N° |
|À|chaîne|Oui |
|nomà|chaîne|N° |
|Objet|chaîne|Oui |
|corps|chaîne|Oui |
|ishtml|valeur booléenne|N° |
|cc|chaîne|N° |
|ccname|chaîne|N° |
|Cci|chaîne|N° |
|bccname|chaîne|N° |
|ReplyTo|chaîne|N° |
|date|chaîne|N° |
|en-têtes|chaîne|N° |
|fichiers|tableau|N° |
|noms de fichiers|tableau|N° |



### <a name="emailresponse"></a>EmailResponse


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Message|chaîne|N° |



### <a name="recipientlists"></a>RecipientLists


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|listes|tableau|N° |



### <a name="recipientlist"></a>RecipientList


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|nom|chaîne|N° |
|recipient_count|nombre entier|N° |



### <a name="recipients"></a>Destinataires


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|destinataires|tableau|N° |



### <a name="recipient"></a>Destinataire


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Messagerie|chaîne|N° |
|last_name|chaîne|N° |
|Prénom|chaîne|N° |
|ID|chaîne|N° |


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)