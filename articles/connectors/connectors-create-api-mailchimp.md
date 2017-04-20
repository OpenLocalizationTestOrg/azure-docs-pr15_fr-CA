<properties
pageTitle="MailChimp | Microsoft Azure"
description="Permet de créer des applications de logique avec le service d’application d’Azure. MailChimp est un service SaaS permet aux entreprises de gérer et d’automatiser les activités de marketing électronique, notamment l’envoi de courriels marketing, messages automatisés et des campagnes ciblées."
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

# <a name="get-started-with-the-mailchimp-connector"></a>Mise en route avec le connecteur de MailChimp

MailChimp est un service SaaS permet aux entreprises de gérer et d’automatiser les activités de marketing électronique, notamment l’envoi de courriels marketing, messages automatisés et des campagnes ciblées.


>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2015-08-01-aperçu.

Vous pouvez commencer par la création d’une application logique maintenant, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur de MailChimp peut être utilisé en tant qu’action ; Il a l’ou les déclencheurs. Tous les connecteurs prennent en charge les données au format JSON et XML.

 Le connecteur de MailChimp a l’ou des actions et/ou les déclencheurs disponibles suivants :

### <a name="mailchimp-actions"></a>Actions de MailChimp
Vous pouvez effectuer ces actions :

|Action|Description|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|Créer une nouvelle campagne basée sur un Type de campagne, la liste de destinataires et les paramètres de la campagne (ligne d’objet, titre, from_name et reply_to)|
|[NewList](connectors-create-api-mailchimp.md#newlist)|Créer une nouvelle liste dans votre compte MailChimp|
|[AddMember](connectors-create-api-mailchimp.md#addmember)|Ajouter ou mettre à jour un membre de la liste|
|[RemoveMember](connectors-create-api-mailchimp.md#removemember)|Supprimer un membre d’une liste.|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|Mettre à jour les informations d’un membre de liste spécifique|
### <a name="mailchimp-triggers"></a>Déclencheurs de MailChimp
Vous pouvez écouter ces événements :

|Déclencheur | Description|
|--- | ---|
|Lorsqu’un membre a été ajouté à une liste|Déclenche un flux de travail lorsqu’un nouveau membre a été ajouté à une liste|
|Lors de la création d’une nouvelle liste|Déclenche un flux de travail lors de la création d’une nouvelle liste|


## <a name="create-a-connection-to-mailchimp"></a>Créer une connexion à MailChimp
Pour créer des applications de logique avec MailChimp, vous devez d’abord créer une **connexion** puis fournir les détails pour les propriétés suivantes :

|Propriété| Obligatoire|Description|
| ---|---|---|
|Jeton|Oui|Fournir des informations d’identification de MailChimp|

>[AZURE.INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications de logique.

## <a name="reference-for-mailchimp"></a>Référence MailChimp
S’applique à la version : 1.0

## <a name="newcampaign"></a>newcampaign
Nouvelle campagne marketing : Créer une nouvelle campagne basée sur un Type de campagne, la liste de destinataires et les paramètres de la campagne (ligne d’objet, titre, from_name et reply_to)

```POST: /campaigns```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|newCampaignRequest| |Oui|corps|Aucun|Objet JSON à envoyer dans le corps avec les nouveaux paramètres de requête de campagne|

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


## <a name="newlist"></a>NewList
Nouvelle liste : Créer une nouvelle liste dans votre compte MailChimp

```POST: /lists```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|newListRequest| |Oui|corps|Aucun|Objet JSON à envoyer dans le corps avec les nouveaux paramètres de requête de campagne|

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


## <a name="addmember"></a>AddMember
Ajouter des membres à la liste : ajouter ou mettre à jour un membre de la liste

```POST: /lists/{list_id}/members```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|chaîne|Oui|chemin d’accès|Aucun|Id unique de la liste|
|newMemberInList| |Oui|corps|Aucun|Objet JSON à envoyer dans le corps avec les nouvelles informations de membre|

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


## <a name="removemember"></a>RemoveMember
Supprimer les membres de liste : supprimer un membre d’une liste.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|chaîne|Oui|chemin d’accès|Aucun|Id unique de la liste|
|member_email|chaîne|Oui|chemin d’accès|Aucun|L’adresse e-mail du membre à supprimer|

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


## <a name="updatemember"></a>updatemember
Mettre à jour les informations de membre : mettre à jour les informations d’un membre de liste spécifique

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|chaîne|Oui|chemin d’accès|Aucun|Id unique de la liste|
|member_email|chaîne|Oui|chemin d’accès|Aucun|L’adresse de messagerie unique du membre à mettre à jour|
|updateMemberInListRequest| |Oui|corps|Aucun|Objet JSON à envoyer dans le corps avec les informations de mise à jour des membres|

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


## <a name="onmembersubscribed"></a>OnMemberSubscribed
Lorsqu’un membre a été ajouté à une liste : déclenche un flux de travail lorsqu’un nouveau membre a été ajouté à une liste

```GET: /trigger/lists/{list_id}/members```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|chaîne|Oui|chemin d’accès|Aucun|Id unique de la liste|

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


## <a name="oncreatelist"></a>OnCreateList
Lors de la création d’une nouvelle liste : déclenche un flux de travail lors de la création d’une nouvelle liste

```GET: /trigger/lists```

Aucun paramètre pour cet appel
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

### <a name="newcampaignrequest"></a>NewCampaignRequest


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|type de|chaîne|Oui |
|destinataires|non défini|Oui |
|Paramètres|non défini|Oui |
|variate_settings|non défini|N° |
|suivi|non défini|N° |
|rss_opts|non défini|N° |
|social_card|non défini|N° |



### <a name="recipient"></a>Destinataire


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|list_id|chaîne|Oui |
|segment_opts|non défini|N° |



### <a name="settings"></a>Paramètres


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ligne_objet|chaîne|Oui |
|titre|chaîne|N° |
|from_name|chaîne|Oui |
|reply_to|chaîne|Oui |
|use_conversation|valeur booléenne|N° |
|to_name|chaîne|N° |
|folder_id|nombre entier|N° |
|s’authentifier|valeur booléenne|N° |
|auto_footer|valeur booléenne|N° |
|inline_css|valeur booléenne|N° |
|auto_tweet|valeur booléenne|N° |
|auto_fb_post|tableau|N° |
|fb_comments|valeur booléenne|N° |



### <a name="variatesettings"></a>Variate_Settings


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|winner_criteria|chaîne|N° |
|temps_attente|nombre entier|N° |
|test_size|nombre entier|N° |
|subject_lines|tableau|N° |
|send_times|tableau|N° |
|from_names|tableau|N° |
|reply_to_addresses|tableau|N° |



### <a name="tracking"></a>Suivi


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|s’ouvre|valeur booléenne|N° |
|html_clicks|valeur booléenne|N° |
|text_clicks|valeur booléenne|N° |
|goal_tracking|valeur booléenne|N° |
|ecomm360|valeur booléenne|N° |
|google_analytics|chaîne|N° |
|clicktale|chaîne|N° |
|force de vente|non défini|N° |
|highrise|non défini|N° |
|Capsule|non défini|N° |



### <a name="rssopts"></a>RSS_Opts


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|feed_url|chaîne|N° |
|fréquence|chaîne|N° |
|constrain_rss_img|chaîne|N° |
|planification|non défini|N° |



### <a name="socialcard"></a>Social_Card


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|image_url|chaîne|N° |
|Description|chaîne|N° |
|titre|chaîne|N° |



### <a name="segmentopts"></a>Segment_Opts


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|saved_segment_id|nombre entier|N° |
|correspond à|chaîne|N° |



### <a name="salesforce"></a>Force de vente


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|campagne|valeur booléenne|N° |
|Notes|valeur booléenne|N° |



### <a name="highrise"></a>Highrise


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|campagne|valeur booléenne|N° |
|Notes|valeur booléenne|N° |



### <a name="capsule"></a>Capsule


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Notes|valeur booléenne|N° |



### <a name="schedule"></a>Planification


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|heure|nombre entier|N° |
|daily_send|non défini|N° |
|weekly_send_day|chaîne|N° |
|monthly_send_date|numéro de|N° |



### <a name="dailysend"></a>Daily_Send


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|dimanche|valeur booléenne|N° |
|lundi|valeur booléenne|N° |
|Mardi|valeur booléenne|N° |
|Mercredi|valeur booléenne|N° |
|Jeudi|valeur booléenne|N° |
|vendredi|valeur booléenne|N° |
|samedi|valeur booléenne|N° |



### <a name="campaignresponsemodel"></a>CampaignResponseModel


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|chaîne|N° |
|type de|chaîne|N° |
|create_time|chaîne|N° |
|archive_url|chaîne|N° |
|état|chaîne|N° |
|emails_sent|nombre entier|N° |
|send_time|chaîne|N° |
|content_type|chaîne|N° |
|destinataire|tableau|N° |
|Paramètres|non défini|N° |
|variate_settings|non défini|N° |
|suivi|non défini|N° |
|rss_opts|non défini|N° |
|ab_split_opts|non défini|N° |
|social_card|non défini|N° |
|report_summary|non défini|N° |
|delivery_status|non défini|N° |
|_links|tableau|N° |



### <a name="absplitopts"></a>AB_Split_Opts


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|split_test|chaîne|N° |
|pick_winner|chaîne|N° |
|wait_units|chaîne|N° |
|temps_attente|nombre entier|N° |
|split_size|nombre entier|N° |
|from_name_a|chaîne|N° |
|from_name_b|chaîne|N° |
|reply_email_a|chaîne|N° |
|reply_email_b|chaîne|N° |
|subject_a|chaîne|N° |
|subject_b|chaîne|N° |
|send_time_a|chaîne|N° |
|send_time_b|chaîne|N° |
|send_time_winner|chaîne|N° |



### <a name="reportsummary"></a>Report_Summary


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|s’ouvre|nombre entier|N° |
|unique_opens|nombre entier|N° |
|open_rate|numéro de|N° |
|clique sur|nombre entier|N° |
|subscriber_clicks|numéro de|N° |
|click_rate|numéro de|N° |



### <a name="deliverystatus"></a>Delivery_Status


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|activé|valeur booléenne|N° |
|can_cancel|valeur booléenne|N° |
|état|chaîne|N° |
|emails_sent|nombre entier|N° |
|emails_canceled|nombre entier|N° |



### <a name="link"></a>Lien


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|rel|chaîne|N° |
|href|chaîne|N° |
|méthode|chaîne|N° |
|targetSchema|chaîne|N° |
|schéma|chaîne|N° |



### <a name="newlistrequest"></a>NewListRequest


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|nom|chaîne|Oui |
|contact|non défini|Oui |
|permission_reminder|chaîne|Oui |
|use_archive_bar|valeur booléenne|N° |
|campaign_defaults|non défini|Oui |
|notify_on_subscribe|chaîne|N° |
|notify_on_unsubscribe|chaîne|N° |
|email_type_option|valeur booléenne|Oui |
|visibilité|chaîne|N° |



### <a name="contact"></a>Contact


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|société|chaîne|Oui |
|adresse 1|chaîne|Oui |
|adresse 2|chaîne|N° |
|Ville|chaîne|Oui |
|état|chaîne|Oui |
|ZIP|chaîne|Oui |
|pays|chaîne|Oui |
|téléphone|chaîne|Oui |



### <a name="campaigndefaults"></a>Campaign_Defaults


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|from_name|chaîne|Oui |
|from_email|chaîne|Oui |
|Objet|chaîne|N° |
|langue|chaîne|Oui |



### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|chaîne|Oui |
|nom|chaîne|Oui |
|contact|non défini|Oui |
|permission_reminder|chaîne|Oui |
|use_archive_bar|valeur booléenne|N° |
|campaign_defaults|non défini|Oui |
|notify_on_subscribe|chaîne|N° |
|notify_on_unsubscribe|chaîne|N° |
|date_de_création|chaîne|N° |
|list_rating|nombre entier|N° |
|email_type_option|valeur booléenne|Oui |
|subscribe_url_short|chaîne|N° |
|subscribe_url_long|chaîne|N° |
|beamer_address|chaîne|N° |
|visibilité|chaîne|N° |
|modules|tableau|N° |
|statistiques|non défini|N° |
|_links|tableau|N° |



### <a name="stats"></a>Statistiques


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|member_count|nombre entier|N° |
|unsubscribe_count|nombre entier|N° |
|cleaned_count|nombre entier|N° |
|member_count_since_send|nombre entier|N° |
|unsubscribe_count_since_send|nombre entier|N° |
|cleaned_count_since_send|nombre entier|N° |
|campaign_count|nombre entier|N° |
|campaign_last_sent|nombre entier|N° |
|merge_field_count|nombre entier|N° |
|avg_sub_rate|numéro de|N° |
|avg_unsub_rate|numéro de|N° |
|target_sub_rate|numéro de|N° |
|open_rate|numéro de|N° |
|click_rate|numéro de|N° |
|last_sub_date|chaîne|N° |
|last_unsub_date|chaîne|N° |



### <a name="getlistsresponsemodel"></a>GetListsResponseModel


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|listes|tableau|N° |
|total_items|nombre entier|N° |



### <a name="newmemberinlistrequest"></a>NewMemberInListRequest


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|email_type|chaîne|N° |
|état|chaîne|Oui |
|merge_fields|non défini|N° |
|centres d’intérêt|chaîne|N° |
|langue|chaîne|N° |
|VIP|valeur booléenne|N° |
|emplacement|non défini|N° |
|argument adresse_électronique|chaîne|Oui |



### <a name="firstandlastname"></a>FirstAndLastName


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|FNAME|chaîne|N° |
|LNAME|chaîne|N° |



### <a name="location"></a>Emplacement


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Latitude|numéro de|N° |
|longitude|numéro de|N° |



### <a name="memberresponsemodel"></a>MemberResponseModel


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|chaîne|N° |
|argument adresse_électronique|chaîne|N° |
|unique_email_id|chaîne|N° |
|email_type|chaîne|N° |
|état|chaîne|N° |
|merge_fields|non défini|N° |
|centres d’intérêt|chaîne|N° |
|statistiques|non défini|N° |
|ip_signup|chaîne|N° |
|timestamp_signup|chaîne|N° |
|ip_opt|chaîne|N° |
|timestamp_opt|chaîne|N° |
|member_rating|nombre entier|N° |
|last_changed|chaîne|N° |
|langue|chaîne|N° |
|VIP|valeur booléenne|N° |
|email_client|chaîne|N° |
|emplacement|non défini|N° |
|last_note|non défini|N° |
|list_id|chaîne|N° |
|_links|tableau|N° |



### <a name="lastnote"></a>Last_Note


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|note_id|nombre entier|N° |
|created_at|chaîne|N° |
|created_by|chaîne|N° |
|Remarque|chaîne|N° |



### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|membres|tableau|N° |
|list_id|chaîne|N° |
|total_items|nombre entier|N° |



### <a name="object"></a>Objet






### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|argument adresse_électronique|chaîne|N° |
|email_type|chaîne|N° |
|état|chaîne|Oui |
|merge_fields|non défini|N° |
|centres d’intérêt|chaîne|N° |
|langue|chaîne|N° |
|VIP|valeur booléenne|N° |
|emplacement|non défini|N° |



### <a name="getmembersresponsemodel"></a>GetMembersResponseModel


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|membres|tableau|N° |
|list_id|chaîne|N° |
|total_items|nombre entier|N° |



### <a name="adduserresponsemodel"></a>AddUserResponseModel


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|chaîne|Oui |
|argument adresse_électronique|chaîne|Oui |
|unique_email_id|chaîne|N° |
|email_type|chaîne|N° |
|état|chaîne|N° |
|merge_fields|non défini|Oui |
|centres d’intérêt|chaîne|N° |
|statistiques|non défini|N° |
|ip_signup|chaîne|N° |
|timestamp_signup|chaîne|N° |
|ip_opt|chaîne|N° |
|timestamp_opt|chaîne|N° |
|member_rating|nombre entier|N° |
|last_changed|chaîne|N° |
|langue|chaîne|N° |
|VIP|valeur booléenne|N° |
|email_client|chaîne|N° |
|emplacement|non défini|N° |
|last_note|non défini|N° |
|list_id|chaîne|N° |
|_links|tableau|N° |


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
