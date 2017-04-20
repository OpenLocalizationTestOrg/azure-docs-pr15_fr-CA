<properties
    pageTitle="Ajouter le connecteur Office 365 utilisateurs dans les applications de logique | Microsoft Azure"
    description="Vue d’ensemble du connecteur Office 365 utilisateurs avec les paramètres de l’API REST"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office-365-users-connector"></a>Mise en route avec le connecteur utilisateurs Office 365.

Se connecter à des utilisateurs d’Office 365 à obtenir les profils et utilisateurs de MSN search. 

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2015-08-01-aperçu.

Avec les utilisateurs d’Office 365, vous pouvez :

- Créer votre flux d’activité basé sur les données que vous obtenez à partir des utilisateurs d’Office 365. 
- Actions utilisation qui obtiennent le rapports directs, obtenir le profil d’utilisateur du responsable et bien plus encore. Ces actions Obtient une réponse et vérifiez la sortie disponible pour d’autres actions. Par exemple, obtenir des rapports directs d’une personne et puis prendre ces informations et mettre à jour une base de données SQL Azure. 

Pour ajouter une opération dans les applications de la logique, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur d’utilisateurs d’Office 365 a les actions suivantes disponibles. Il n’y a pas de déclencheurs.

| Déclencheurs | Actions|
| --- | --- |
|Aucun | <ul><li>Obtenez le Gestionnaire</li><li>Obtenir mon profil</li><li>Obtenir les rapports directs</li><li>Obtenir le profil de l’utilisateur</li><li>Rechercher des utilisateurs</li></ul>|

Tous les connecteurs prennent en charge les données au format JSON et XML. 


## <a name="create-a-connection-to-office-365-users"></a>Créer une connexion à des utilisateurs d’Office 365.

Lorsque vous ajoutez ce connecteur à vos applications de logique, vous devez reconnectez-vous à votre compte d’utilisateurs d’Office 365 et autoriser les applications logique pour se connecter à votre compte.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]

Après avoir créé la connexion, vous entrez les propriétés des utilisateurs d’Office 365, comme l’ID utilisateur. La **référence de l’API du reste** de cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion d’utilisateurs d’Office 365 dans d’autres applications de logique.


## <a name="office-365-users-rest-api-reference"></a>Référence des API d’autres utilisateurs Office 365
S’applique à la version : 1.0.

### <a name="get-my-profile"></a>Obtenir mon profil 
Extrait le profil de l’utilisateur actuel.  
```GET: /users/me``` 

Il n’y a pas de paramètres pour cet appel.

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


### <a name="get-user-profile"></a>Obtenir le profil de l’utilisateur 
Récupère un profil utilisateur spécifique.  
```GET: /users/{userId}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID utilisateur|chaîne|Oui|chemin d’accès|Aucun|Id utilisateur nom ou courriel principal|

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


### <a name="get-manager"></a>Obtenez le Gestionnaire 
Récupère le profil utilisateur pour le Gestionnaire de l’utilisateur spécifié.  
```GET: /users/{userId}/manager``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID utilisateur|chaîne|Oui|chemin d’accès|Aucun|Id utilisateur nom ou courriel principal|

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



### <a name="get-direct-reports"></a>Obtenir les rapports directs 
Obtenir les rapports directs.  
```GET: /users/{userId}/directReports``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID utilisateur|chaîne|Oui|chemin d’accès|Aucun|Id utilisateur nom ou courriel principal|

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



### <a name="search-for-users"></a>Rechercher des utilisateurs 
Récupère les résultats de profils utilisateur.  
```GET: /users``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|searchTerm|chaîne|aucune|requête|Aucun|Chaîne de recherche (s’applique à : nom complet, prénom, nom, courrier, messagerie surnom et principal nom d’utilisateur)|

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



## <a name="object-definitions"></a>Définitions d’objets

#### <a name="user-user-model-class"></a>Utilisateur : Classe de modèle d’utilisateur

|Nom de la propriété | Type de données |Obligatoire
|---|---|---|
|DisplayName|chaîne|aucune|
|GivenName|chaîne|aucune|
|Nom de famille|chaîne|aucune|
|Courrier|chaîne|aucune|
|MailNickname|chaîne|aucune|
|Numéro de téléphone|chaîne|aucune|
|AccountEnabled|valeur booléenne|aucune|
|ID|chaîne|Oui
|UserPrincipalName|chaîne|aucune|
|Département|chaîne|aucune|
|JobTitle|chaîne|aucune|
|mobilePhone|chaîne|aucune|


## <a name="next-steps"></a>Étapes suivantes

[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenir à la [liste des API](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG
