<properties
    pageTitle="Ajoutez l’action HTTP dans les applications de logique | Microsoft Azure"
    description="Vue d’ensemble de l’action de HTTP avec des propriétés"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-http-action"></a>Mise en route de l’action de HTTP

Avec l’action de HTTP, vous pouvez étendre les flux de travail de votre organisation et communiquer à n’importe quel point de terminaison via HTTP.

Vous pouvez :

- Créer la logique des workflows app activent (déclencheur) lors de la défaillance d’un site Web que vous gérez.
- Communiquer à n’importe quel point de terminaison via HTTP pour étendre votre flux de travail à d’autres services.

Pour commencer à utiliser l’action HTTP dans une logique d’application, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-trigger"></a>Utiliser le déclencheur HTTP

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail est défini dans une logique d’application. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de paramétrage du déclencheur HTTP dans le Concepteur d’application logique.

1. Ajouter le déclencheur HTTP dans votre logique d’application.
2. Renseignez les paramètres pour le point de terminaison HTTP que vous souhaitez interroger.
3. Modifier l’intervalle de récurrence sur la fréquence à laquelle il doit interroger.
4. L’application logique se déclenche désormais dont le contenu est retourné au cours de chaque vérification.

![Déclencheur HTTP](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Comment le déclencheur HTTP fonctionne

Le déclencheur HTTP effectue un appel à un point de terminaison HTTP à un intervalle périodique. Par défaut, toutes les réponses HTTP de code moins de 300 résultats dans une logique d’application s’exécutent. Vous pouvez ajouter une condition en mode code qui permettra d’évaluer après l’appel HTTP pour déterminer si l’application de la logique doit se déclencher. Voici un exemple d’un déclencheur de HTTP qui se déclenche chaque fois que le code d’état retourné est supérieure ou égale à `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

En savoir plus sur les paramètres de déclencheur HTTP sont disponibles sur [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Utilisez l’action HTTP

Une action est une opération qui est réalisée par le flux de travail est défini dans une logique d’application. [En savoir plus sur les actions](connectors-overview.md).

1. Cliquez sur le bouton **Nouvelle étape** .
2. Cliquez sur **Ajouter une action**.
3. Dans la zone de recherche action, tapez **http** pour répertorier l’action HTTP.

    ![Sélectionnez l’action HTTP](./media/connectors-native-http/using-action-1.png)

4. Ajoutez tous les paramètres qui sont requis pour l’appel HTTP.

    ![Effectuer l’action HTTP](./media/connectors-native-http/using-action-2.png)

5. Cliquez sur le coin supérieur gauche de la barre d’outils pour enregistrer. Votre application logique est à la fois enregistrer et publier (activer).

## <a name="http-trigger"></a>Déclencheur HTTP

Voici les détails pour le déclencheur qui prend en charge de ce connecteur. Le connecteur HTTP a un déclencheur.

|Déclencheur|Description|
|---|---|
|HTTP|Effectue un appel HTTP et retourne le contenu de la réponse.|

## <a name="http-action"></a>Action de HTTP

Voici les détails de l’action qui prend en charge de ce connecteur. Le connecteur HTTP a une action possible.

|Action|Description|
|---|---|
|HTTP|Effectue un appel HTTP et retourne le contenu de la réponse.|

## <a name="http-details"></a>Détails sur HTTP

Les tableaux suivants décrivent les champs d’entrée requis et facultatifs pour l’action et les détails de sortie correspondants qui sont associés à l’aide de l’action.


#### <a name="http-request"></a>Requête HTTP
Les éléments suivants sont les champs d’entrée de l’action, ce qui rend les demandes HTTP sortantes.
A * signifie qu’il est un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Méthode *|méthode|Le verbe HTTP à utiliser|
|URI *|URI|L’URI de la demande HTTP|
|En-têtes|en-têtes|Un objet JSON d’en-têtes HTTP pour inclure|
|Corps|corps|Le corps de la demande HTTP|
|Authentification|authentification|Détails dans la section [authentification](#authentication)|
<br>

#### <a name="output-details"></a>Détails de sortie

Vous trouverez ci-dessous des informations de sortie pour la réponse HTTP.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|En-têtes|objet|En-têtes de réponse|
|Corps|objet|Objet de réponse|
|Code d’état|int|Code d’état HTTP|

## <a name="authentication"></a>Authentification

La fonctionnalité des applications de logique de Service d’application Azure vous permet d’utiliser différents types d’authentification par rapport aux points de terminaison HTTP. Vous pouvez utiliser cette authentification avec les connecteurs **HTTP**, **[HTTP + Swagger](./connectors-native-http-swagger.md)**et **[Webhook de HTTP](./connectors-native-webhook.md)** . Les types d’authentification suivants sont configurables :

* [Authentification de base](#basic-authentication)
* [Authentification par certificat client](#client-certificate-authentication)
* [Authentification Active Directory (AD Azure) OAuth Azure](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Authentification de base

L’objet suivant de l’authentification est nécessaire pour l’authentification de base.
A * signifie qu’il est un champ obligatoire.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Type *|type de|Type d’authentification (doit être `Basic` pour l’authentification de base)|
|Nom d’utilisateur *|nom d’utilisateur|Nom d’utilisateur pour l’authentification|
|Mot de passe *|mot de passe|Mot de passe pour l’authentification|

>[AZURE.TIP] Si vous souhaitez utiliser un mot de passe ne peuvent pas être récupérée à partir de la définition, utilisez une `securestring` paramètre et la `@parameters()` [fonction de définition de flux de travail](http://aka.ms/logicappdocs).

Par conséquent, vous devez créer un objet comme suit dans le champ authentification :

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Authentification par certificat client

L’objet suivant de l’authentification est nécessaire pour l’authentification de certificat client. A * signifie qu’il est un champ obligatoire.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Type *|type de|Le type d’authentification (doit être `ClientCertificate` pour les certificats clients SSL)|
|PFX *|PFX|Le contenu codé en base 64 du fichier d’échange dynamique de données (PFX, Personal Information Exchange)|
|Mot de passe *|mot de passe|Le mot de passe pour accéder au fichier PFX|

>[AZURE.TIP] Vous pouvez utiliser un `securestring` paramètre et le `@parameters()` pour utiliser un paramètre qui ne sont pas lisible dans la définition d’après l’enregistrement de l’application de la logique de la [fonction de définition de flux de travail](http://aka.ms/logicappdocs) .

Par exemple :

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Authentification AD OAuth Azure

L’objet suivant de l’authentification est nécessaire pour l’authentification d’Azure AD OAuth. A * signifie qu’il est un champ obligatoire.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Type *|type de|Le type d’authentification (doit être `ActiveDirectoryOAuth` pour Azure AD OAuth)|
|Clients *|clients|L’identificateur de client pour les clients AD Azure|
|Audience *|public|La valeur`https://management.core.windows.net/`|
|Client ID *|clientId|L’identificateur du client de l’application Azure AD|
|Secret *|secret|Le mot de passe du client qui demande le jeton|

>[AZURE.TIP] Vous pouvez utiliser un `securestring` paramètre et la `@parameters()` [fonction de définition de flux de travail](http://aka.ms/logicappdocs) pour utiliser un paramètre qui ne sont pas lisible dans la définition d’après l’enregistrement.

Par exemple :

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant, essayez la plate-forme et de [créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications de la logique en consultant notre [liste d’API](apis-list.md).
