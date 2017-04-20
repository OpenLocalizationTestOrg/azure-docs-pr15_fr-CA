<properties
    pageTitle="Utilisez des actions de demande et de réponse | Microsoft Azure"
    description="Vue d’ensemble de la demande et réponse de déclencheur et l’action dans une application Azure logique"
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
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-request-and-response-components"></a>Mise en route avec les composants de requête et de réponse

Avec les composants de requête et de réponse dans une logique d’application, vous pouvez répondre à des événements en temps réel.

Par exemple, vous pouvez :

- Répondre à une demande HTTP avec des données à partir d’une base de données locale à une application de logique.
- Déclencher une application logique d’un événement externe webhook.
- Appeler une application logique avec une action de demande et de réponse à partir de dans une autre application de logique.

Pour commencer à l’aide d’une demande et la réponse dans une logique d’application, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-request-trigger"></a>Utiliser le déclencheur de la demande HTTP

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail est défini dans une logique d’application. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de la configuration d’une requête HTTP dans le Concepteur d’application logique.

1. Ajouter le déclencheur **- requête HTTP d’un lors de la demande** dans votre logique d’application. Vous pouvez éventuellement fournir un schéma JSON (en utilisant un outil tel que [JSONSchema.net](http://jsonschema.net)) pour le corps de la demande. Ainsi, le concepteur générer des jetons pour les propriétés de la requête HTTP.
2. Ajouter une autre action afin que vous puissiez enregistrer l’application logique.
3. Après avoir enregistré l’application logique, vous pouvez obtenir l’URL de requête HTTP à partir de la fiche de la demande.
4. Une publication HTTP (vous pouvez utiliser un outil tel que [Postman](https://www.getpostman.com/)) à l’adresse URL déclenche l’application de la logique.

>[AZURE.NOTE] Si vous ne définissez pas une action de réponse, une `202 ACCEPTED` réponse est immédiatement renvoyé à l’appelant. Vous pouvez utiliser l’action de réponse pour personnaliser une réponse.

![Déclencheur de réponse](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Utilisez l’action de réponse HTTP

L’action de réponse HTTP n’est valide que lorsque vous l’utilisez dans un workflow qui est déclenché par une requête HTTP. Si vous ne définissez pas une action de réponse, une `202 ACCEPTED` réponse est immédiatement renvoyé à l’appelant.  Vous pouvez ajouter une action de réponse à une étape dans le flux de travail. L’application logique conserve uniquement la demande entrante ouverte pendant une minute pour une réponse.  Après une minute, si aucune réponse n’a été envoyé à partir du flux de travail (et une action de réponse existe dans la définition), un `504 GATEWAY TIMEOUT` est retourné à l’appelant.

Voici comment procéder ajouter une action de réponse HTTP :

1. Cliquez sur le bouton **Nouvelle étape** .
2. Cliquez sur **Ajouter une action**.
3. Dans la zone de recherche action, tapez la **réponse** pour l’action de réponse de la liste.

    ![Sélectionnez l’action de réponse](./media/connectors-native-reqres/using-action-1.png)

4. Ajoutez tous les paramètres qui sont requis pour le message de réponse HTTP.

    ![Effectuer l’action de réponse](./media/connectors-native-reqres/using-action-2.png)

5. Cliquez sur le coin supérieur gauche de la barre d’outils pour enregistrer et votre application logique est à la fois enregistrer et publier (activer).

## <a name="request-trigger"></a>Déclencheur de demande

Voici les détails pour le déclencheur qui prend en charge de ce connecteur. Il existe un déclencheur de demande unique.

|Déclencheur|Description|
|---|---|
|Demande|Se produit lors de la réception d’une demande HTTP|

## <a name="response-action"></a>Action de réponse

Voici les détails de l’action qui prend en charge de ce connecteur. Il existe une action de réponse unique qui peut être utilisée uniquement lorsqu’il est accompagné d’un déclencheur de la demande.

|Action|Description|
|---|---|
|Réponse|Retourne une réponse à la demande HTTP en corrélation|

### <a name="trigger-and-action-details"></a>Détails de déclencheur et action

Les tableaux suivants décrivent les champs d’entrée pour le déclencheur et l’action, et les détails de sortie correspondant.

#### <a name="request-trigger"></a>Déclencheur de demande
Voici un champ d’entrée pour le déclencheur à partir d’une demande HTTP entrante.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Schéma JSON|schéma|Le schéma JSON du corps de la demande HTTP|
<br>

**Détails de sortie**

Vous trouverez ci-dessous des informations de sortie pour la demande.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|En-têtes|objet|En-têtes de demande|
|Corps|objet|Objet de la demande|

#### <a name="response-action"></a>Action de réponse

Les éléments suivants sont les champs d’entrée de l’action de réponse HTTP. A * signifie qu’il est un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Code d’état *|statusCode|Le code d’état HTTP|
|En-têtes|en-têtes|Un objet JSON des en-têtes de réponse à inclure|
|Corps|corps|Le corps de réponse|

## <a name="next-steps"></a>Étapes suivantes

Maintenant, essayez la plate-forme et de [créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications de la logique en consultant notre [liste d’API](apis-list.md).
