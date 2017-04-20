<properties
pageTitle="Outlook.com | Microsoft Azure"
description="Permet de créer des applications de logique avec le service d’application d’Azure. Connecteur de Outlook.com vous permet de gérer votre courrier électronique, les calendriers et les contacts. Vous pouvez effectuer différentes actions comme envoyer un courrier, planifier des réunions, ajouter des contacts, etc.."
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

# <a name="get-started-with-the-outlookcom-connector"></a>Mise en route avec le connecteur de Outlook.com

Connecteur de Outlook.com vous permet de gérer votre courrier électronique, les calendriers et les contacts. Vous pouvez effectuer différentes actions comme envoyer un courrier, planifier des réunions, ajouter des contacts, etc..

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2015-08-01-aperçu. 

Vous pouvez commencer par la création d’une application logique maintenant, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur de Outlook.com peut être utilisé en tant qu’action ; Il a l’ou les déclencheurs. Tous les connecteurs prennent en charge les données au format JSON et XML. 

 Le connecteur de Outlook.com a l’ou des actions et/ou les déclencheurs disponibles suivants :

### <a name="outlookcom-actions"></a>Actions de Outlook.com
Vous pouvez effectuer ces actions :

|Action|Description|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|Récupère des messages électroniques à partir d’un dossier|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|Envoie un message électronique|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|Supprime un e-mail par id|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|Marque un e-mail comme ayant été lus.|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|Réponses à un message électronique|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|Récupère pièce jointe par id|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|Envoyer un courrier électronique avec plusieurs options et attendez que le destinataire de répondre avec une des options|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|Envoyer un e-mail d’approbation et d’attendre une réponse du destinataire|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|Récupère les calendriers|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|Extrait les éléments d’un calendrier|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|Crée un nouvel événement|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|Extrait un élément spécifique à partir d’un calendrier|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|Supprime un élément de calendrier|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|Partiellement mises à jour d’un élément de calendrier|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|Récupère les dossiers de contacts|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|Extrait des contacts à partir d’un dossier de contacts|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|Crée un nouveau contact|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|Récupère un contact spécifique à partir d’un dossier de contacts|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|Supprime un contact|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|Partiellement mises à jour d’un contact|
### <a name="outlookcom-triggers"></a>Déclencheurs de Outlook.com
Vous pouvez écouter ces événements :

|Déclencheur | Description|
|--- | ---|
|Sur les événements point de commencer|Déclenche un flux lors du démarrage d’un événement de calendrier à venir|
|Sur la nouvelle adresse e-mail|Déclenche un flux lors de l’arrivée d’un e-mail|
|Sur les nouveaux éléments|Déclenché lors de la création d’un nouvel élément de calendrier|
|Sur les éléments mis à jour|Déclenché lorsqu’un élément de calendrier est modifié.|


## <a name="create-a-connection-to-outlookcom"></a>Créer une connexion à un Outlook.com
Pour créer des applications de logique avec Outlook.com, vous devez d’abord créer une **connexion** puis fournir les détails pour les propriétés suivantes : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|Jeton|Oui|Fournir des informations d’identification de Outlook.com|
Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et d’écouter pour les déclencheurs décrites dans cet article.

>[AZURE.INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)] 

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications de logique.  

## <a name="reference-for-outlookcom"></a>Référence Outlook.com
S’applique à la version : 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
Sur événement commencer : déclenche un flux lors du démarrage d’un événement de calendrier à venir 

```GET: /Events/OnUpcomingEvents``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|requête|Aucun|Identificateur unique du calendrier|
|lookAheadTimeInMinutes|nombre entier|aucune|requête|15|Temps (en minutes) pour rechercher directement des événements à venir|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|202|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="getemails"></a>GetEmails
Obtenir les e-mails : récupère des messages électroniques à partir d’un dossier 

```GET: /Mail``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|chaîne|aucune|requête|Boîte de réception|Chemin d’accès du dossier pour récupérer des messages électroniques (par défaut : « Boîte de réception »)|
|Retour au début|nombre entier|aucune|requête|10|Nombre d’e-mails à extraire (par défaut : 10)|
|fetchOnlyUnread|valeur booléenne|aucune|requête|valeur True|Récupérer uniquement les courriers électroniques non lus ?|
|includeAttachments|valeur booléenne|aucune|requête|False|Si défini sur true, jointes est également récupérée avec le message|
|searchQuery|chaîne|aucune|requête|Aucun|Requête de recherche pour filtrer les e-mails|
|Ignorer|nombre entier|aucune|requête|0|Nombre d’e-mails à ignorer (par défaut : 0)|
|skipToken|chaîne|aucune|requête|Aucun|Ignorer le jeton pour la nouvelle page d’extraction|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="sendemail"></a>SendEmail
Envoyer un e-mail : envoie un message électronique 

```POST: /Mail``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|emailMessage| |Oui|corps|Aucun|Messagerie|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="deleteemail"></a>DeleteEmail
Supprimer les e-mails : supprime un e-mail par id 

```DELETE: /Mail/{messageId}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|chaîne|Oui|chemin d’accès|Aucun|ID de l’e-mail à supprimer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="markasread"></a>MarkAsRead
Marquer comme lu : marque un e-mail comme ayant été lus. 

```POST: /Mail/MarkAsRead/{messageId}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|chaîne|Oui|chemin d’accès|Aucun|ID du message à marquer comme lu|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="replyto"></a>ReplyTo
Réponse aux e-mails : les réponses à un message électronique 

```POST: /Mail/ReplyTo/{messageId}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|chaîne|Oui|chemin d’accès|Aucun|ID de l’e-mail de réponse à|
|commentaire|chaîne|Oui|requête|Aucun|Commentaire de suivi|
|replyAll|valeur booléenne|aucune|requête|False|Répondre à tous les destinataires|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="getattachment"></a>GetAttachment
Obtenir la pièce jointe : récupère pièce jointe par id 

```GET: /Mail/{messageId}/Attachments/{attachmentId}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|chaîne|Oui|chemin d’accès|Aucun|ID de l’e-mail|
|attachmentId|chaîne|Oui|chemin d’accès|Aucun|ID de la pièce jointe à télécharger|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="onnewemail"></a>OnNewEmail
Sur la nouvelle adresse e-mail : déclenche un flux lors de l’arrivée d’un e-mail 

```GET: /Mail/OnNewEmail``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|chaîne|aucune|requête|Boîte de réception|Dossier de messagerie à récupérer (par défaut : boîte de réception)|
|À|chaîne|aucune|requête|Aucun|Adresses e-mail du destinataire|
|De|chaîne|aucune|requête|Aucun|À partir de l’adresse|
|importance|chaîne|aucune|requête|Normal|Importance du message (haute, normale, basse) (par défaut : Normal)|
|fetchOnlyWithAttachment|valeur booléenne|aucune|requête|False|Extraire uniquement les e-mails contenant une pièce jointe|
|includeAttachments|valeur booléenne|aucune|requête|False|Inclure des pièces jointes|
|subjectFilter|chaîne|aucune|requête|Aucun|Chaîne à rechercher dans l’objet|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|202|Accepté|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="sendmailwithoptions"></a>SendMailWithOptions
Envoyer un e-mail avec des options : envoyer un courrier électronique avec plusieurs options et attendez que le destinataire de répondre avec une des options 

```POST: /mailwithoptions/$subscriptions``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |Oui|corps|Aucun|Demande d’abonnement pour le courrier électronique d’options|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|201|Abonnement créé|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="sendapprovalmail"></a>SendApprovalMail
Envoyer un e-mail d’approbation : envoyer un courrier électronique d’approbation et d’attendre une réponse du destinataire 

```POST: /approvalmail/$subscriptions``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |Oui|corps|Aucun|Demande d’abonnement pour approbation par e-mail|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|201|Abonnement créé|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="calendargettables"></a>CalendarGetTables
Obtenir des calendriers : récupère des calendriers 

```GET: /datasets/calendars/tables``` 

Aucun paramètre pour cet appel
#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="calendargetitems"></a>CalendarGetItems
Obtenir des événements : extrait les éléments d’un calendrier 

```GET: /datasets/calendars/tables/{table}/items``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique du calendrier à récupérer|
|$filter|chaîne|aucune|requête|Aucun|Une requête de filtre ODATA pour limiter le nombre d’entrées|
|$orderby|chaîne|aucune|requête|Aucun|Une requête d’orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|nombre entier|aucune|requête|Aucun|Nombre d’entrées à ignorer (par défaut = 0)|
|$top|nombre entier|aucune|requête|Aucun|Nombre maximal d’entrées à récupérer (par défaut = 256)|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="calendarpostitem"></a>CalendarPostItem
Créer un événement : crée un nouvel événement 

```POST: /datasets/calendars/tables/{table}/items``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un calendrier|
|élément| |Oui|corps|Aucun|Élément de calendrier à créer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="calendargetitem"></a>CalendarGetItem
Obtenir l’événement : extrait un élément spécifique à partir d’un calendrier 

```GET: /datasets/calendars/tables/{table}/items/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un calendrier|
|ID|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un élément de calendrier à récupérer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="calendardeleteitem"></a>CalendarDeleteItem
Supprimer l’événement : supprime un élément de calendrier 

```DELETE: /datasets/calendars/tables/{table}/items/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un calendrier|
|ID|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique de l’élément de calendrier à supprimer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="calendarpatchitem"></a>CalendarPatchItem
Les événements de mise à jour : partiellement mises à jour d’un élément de calendrier 

```PATCH: /datasets/calendars/tables/{table}/items/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un calendrier|
|ID|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique de l’élément de calendrier mise à jour|
|élément| |Oui|corps|Aucun|Élément de calendrier mise à jour|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
Sur les nouveaux éléments : déclenché lors de la création d’un nouvel élément de calendrier 

```GET: /datasets/calendars/tables/{table}/onnewitems``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un calendrier|
|$filter|chaîne|aucune|requête|Aucun|Une requête de filtre ODATA pour limiter le nombre d’entrées|
|$orderby|chaîne|aucune|requête|Aucun|Une requête d’orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|nombre entier|aucune|requête|Aucun|Nombre d’entrées à ignorer (par défaut = 0)|
|$top|nombre entier|aucune|requête|Aucun|Nombre maximal d’entrées à récupérer (par défaut = 256)|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
Sur les articles mis à jour : déclenché lors de la modification d’un élément de calendrier 

```GET: /datasets/calendars/tables/{table}/onupdateditems``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un calendrier|
|$filter|chaîne|aucune|requête|Aucun|Une requête de filtre ODATA pour limiter le nombre d’entrées|
|$orderby|chaîne|aucune|requête|Aucun|Une requête d’orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|nombre entier|aucune|requête|Aucun|Nombre d’entrées à ignorer (par défaut = 0)|
|$top|nombre entier|aucune|requête|Aucun|Nombre maximal d’entrées à récupérer (par défaut = 256)|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="contactgettables"></a>ContactGetTables
Obtenir les dossiers de contacts : récupère des dossiers de contacts 

```GET: /datasets/contacts/tables``` 

Aucun paramètre pour cet appel
#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="contactgetitems"></a>ContactGetItems
Obtenir des contacts : extrait des contacts à partir d’un dossier de contacts 

```GET: /datasets/contacts/tables/{table}/items``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique du dossier contacts à récupérer|
|$filter|chaîne|aucune|requête|Aucun|Une requête de filtre ODATA pour limiter le nombre d’entrées|
|$orderby|chaîne|aucune|requête|Aucun|Une requête d’orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|nombre entier|aucune|requête|Aucun|Nombre d’entrées à ignorer (par défaut = 0)|
|$top|nombre entier|aucune|requête|Aucun|Nombre maximal d’entrées à récupérer (par défaut = 256)|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="contactpostitem"></a>ContactPostItem
Créer un contact : crée un nouveau contact 

```POST: /datasets/contacts/tables/{table}/items``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un dossier de contacts|
|élément| |Oui|corps|Aucun|Contact à créer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="contactgetitem"></a>ContactGetItem
Obtenir de contact : récupère un contact spécifique à partir d’un dossier de contacts 

```GET: /datasets/contacts/tables/{table}/items/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un dossier de contacts|
|ID|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique du contact à récupérer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="contactdeleteitem"></a>ContactDeleteItem
Supprimer contact : supprime un contact 

```DELETE: /datasets/contacts/tables/{table}/items/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un dossier de contacts|
|ID|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique du contact à supprimer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="contactpatchitem"></a>ContactPatchItem
Mettre à jour le contact : partiellement mises à jour d’un contact 

```PATCH: /datasets/contacts/tables/{table}/items/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique d’un dossier de contacts|
|ID|chaîne|Oui|chemin d’accès|Aucun|Identificateur unique du contact pour mettre à jour|
|élément| |Oui|corps|Aucun|Élément de contact à jour|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="object-definitions"></a>Définitions d’objets 

### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse [IDictionary [String, objet]]


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="object"></a>Objet


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|



### <a name="sendmessage"></a>SendMessage


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Pièces jointes|tableau|N° |
|De|chaîne|N° |
|Cc|chaîne|N° |
|Cci|chaîne|N° |
|Objet|chaîne|Oui |
|Corps|chaîne|Oui |
|Importance|chaîne|N° |
|IsHtml|valeur booléenne|N° |
|À|chaîne|Oui |



### <a name="sendattachment"></a>SendAttachment


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|@odata.type|chaîne|N° |
|Nom|chaîne|Oui |
|ContentBytes|chaîne|Oui |



### <a name="receivemessage"></a>ReceiveMessage


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|chaîne|N° |
|Estlu|valeur booléenne|N° |
|HasAttachment|valeur booléenne|N° |
|DateTimeReceived|chaîne|N° |
|Pièces jointes|tableau|N° |
|De|chaîne|N° |
|Cc|chaîne|N° |
|Cci|chaîne|N° |
|Objet|chaîne|Oui |
|Corps|chaîne|Oui |
|Importance|chaîne|N° |
|IsHtml|valeur booléenne|N° |
|À|chaîne|Oui |



### <a name="receiveattachment"></a>ReceiveAttachment


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|chaîne|Oui |
|ContentType|chaîne|Oui |
|@odata.type|chaîne|N° |
|Nom|chaîne|Oui |
|ContentBytes|chaîne|Oui |



### <a name="digestmessage"></a>DigestMessage


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Objet|chaîne|Oui |
|Corps|chaîne|N° |
|Importance|chaîne|N° |
|Digest|tableau|Oui |
|Pièces jointes|tableau|N° |
|À|chaîne|Oui |



### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse [ReceiveMessage]


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="datasetsmetadata"></a>DataSetsMetadata


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|sous forme de tableau|non défini|N° |
|objet BLOB|non défini|N° |



### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|source|chaîne|N° |
|displayName|chaîne|N° |
|URL|chaîne|N° |
|tableDisplayName|chaîne|N° |
|tablePluralName|chaîne|N° |



### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|source|chaîne|N° |
|displayName|chaîne|N° |
|URL|chaîne|N° |



### <a name="tablemetadata"></a>TableMetadata


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|nom|chaîne|N° |
|titre|chaîne|N° |
|x-ms-autorisation|chaîne|N° |
|x-ms-fonctionnalités|non défini|N° |
|schéma|non défini|N° |



### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|sortRestrictions|non défini|N° |
|filterRestrictions|non défini|N° |
|filterFunctions|tableau|N° |



### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|peut être trié|valeur booléenne|N° |
|unsortableProperties|tableau|N° |
|ascendingOnlyProperties|tableau|N° |



### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|filtrables|valeur booléenne|N° |
|nonFilterableProperties|tableau|N° |
|requiredProperties|tableau|N° |



### <a name="optionsemailsubscription"></a>OptionsEmailSubscription


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|NotificationUrl|chaîne|N° |
|Message|non défini|N° |



### <a name="messagewithoptions"></a>MessageWithOptions


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Objet|chaîne|Oui |
|Options|chaîne|Oui |
|Corps|chaîne|N° |
|Importance|chaîne|N° |
|Pièces jointes|tableau|N° |
|À|chaîne|Oui |



### <a name="subscriptionresponse"></a>SubscriptionResponse


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|chaîne|N° |
|ressources|chaîne|N° |
|notificationType|chaîne|N° |
|notificationUrl|chaîne|N° |



### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|NotificationUrl|chaîne|N° |
|Message|non défini|N° |



### <a name="approvalmessage"></a>ApprovalMessage


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Objet|chaîne|Oui |
|Options|chaîne|Oui |
|Corps|chaîne|N° |
|Importance|chaîne|N° |
|Pièces jointes|tableau|N° |
|À|chaîne|Oui |



### <a name="approvalemailresponse"></a>ApprovalEmailResponse


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Selected|chaîne|N° |



### <a name="tableslist"></a>TablesList


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="table"></a>Table


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Nom|chaîne|N° |
|DisplayName|chaîne|N° |



### <a name="item"></a>Élément


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ItemInternalId|chaîne|N° |



### <a name="calendaritemslist"></a>CalendarItemsList


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="calendaritem"></a>CalendarItem


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ItemInternalId|chaîne|N° |



### <a name="contactitemslist"></a>ContactItemsList


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="contactitem"></a>Objet ContactItem


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ItemInternalId|chaîne|N° |



### <a name="datasetslist"></a>DataSetsList


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="dataset"></a>Groupe de données


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Nom|chaîne|N° |
|DisplayName|chaîne|N° |


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)