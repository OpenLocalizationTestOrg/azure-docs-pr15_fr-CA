<properties
pageTitle="Apprenez à utiliser le connecteur de Bus des services Azure dans vos applications de logique | Microsoft Azure"
description="Permet de créer des applications de logique avec le service d’application d’Azure. Se connecter au Bus des services Azure pour envoyer et recevoir des messages. Vous pouvez effectuer des actions telles qu’envoyer à la file d’attente, envoyer à la rubrique, recevoir à partir de la file d’attente et recevoir de l’abonnement."
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
ms.date="08/02/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-azure-service-bus-connector"></a>Mise en route avec le connecteur de Bus des services Azure

Se connecter au Bus des services Azure pour envoyer et recevoir des messages. Vous pouvez effectuer des actions telles qu’envoyer à la file d’attente, envoyer à la rubrique, recevoir à partir de la file d’attente et recevoir de l’abonnement.

Pour utiliser [un connecteur](./apis-list.md), vous devez d’abord créer une application de logique. Vous pouvez commencer par [créer une application de logique maintenant](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Se connecter à un Bus de Service

Avant que votre application logique peut accéder à n’importe quel service, vous devez d’abord créer une connexion au service. Une [connexion](./connectors-overview.md) fournit une connectivité entre une application logique et un autre service.  

>[AZURE.INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]

## <a name="use-a-service-bus-trigger"></a>Utiliser un déclencheur de Bus des services

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail défini dans une logique d’application. [En savoir plus sur les déclencheurs](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

>[AZURE.INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]  

## <a name="use-a-service-bus-action"></a>Utiliser une action de Bus des services

Une action est une opération effectuée par le flux de travail défini dans une logique d’application. [En savoir plus sur les actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]  

## <a name="technical-details"></a>Détails techniques

Voici les détails sur les déclencheurs, les actions et les réponses qui prend en charge de cette connexion.

### <a name="service-bus-triggers"></a>Déclencheurs de Bus des services

Bus de service a les déclencheurs suivants :  

|Déclencheur | Description|
|--- | ---|
|[Lorsqu’un message est reçu dans une file d’attente](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|Cette opération déclenche un flux lors de la réception d’un message dans une file d’attente.|
|[Lorsqu’un message est reçu dans un abonnement de rubrique](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|Cette opération déclenche un flux lorsqu’un message est reçu dans un abonnement de rubrique.|


### <a name="service-bus-actions"></a>Actions de Bus des services

Bus de service comprend les actions suivantes :


|Action|Description|
|--- | ---|
|[Envoyer le message](connectors-create-api-servicebus.md#send-message)|Cette opération envoie un message à une file d’attente ou une rubrique.|
### <a name="action-and-trigger-details"></a>Détails de l’action et de déclencheur

Voici les détails des actions et des déclencheurs pour ce connecteur, ainsi que leurs réponses.



#### <a name="send-message"></a>Envoyer le message

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|ContentData *|Contenu|Contenu du message.|
|ContentType|Type de contenu|Type de contenu du contenu du message.|
|Propriétés|Propriétés|Demandé de paires clé-valeur pour chaque propriété.|
|l’objet entityName *|Nom de la file d’attente/rubrique.|Nom de la file d’attente ou une rubrique.|

Ces paramètres avancés sont également disponibles :

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|MessageId|Id de message|Une valeur définie par l’utilisateur que le Bus de Service peut utiliser pour identifier les doublons de messages, si activé.|
|À|À|Adresse de destination.|
|ReplyTo|Répondre à|Adresse de la file d’attente de réponse à.|
|ReplyToSessionId|Réponse à l’Id de Session|Identificateur de la session pour y répondre.|
|Étiquette|Étiquette|Étiquette de spécifique à l’application.|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|Date et heure en UTC, lorsque le message est ajouté à la file d’attente.|
|ID de session|Id de session|Identificateur de la session.|
|ID de corrélation|Id de corrélation|Identificateur de la corrélation.|
|Propriété TimeToLive|Durée de vie|La durée, en graduations, qu’un message est valide. La durée commence lorsque le message est envoyé à un Bus de Service.|



Un * indique qu’une propriété est requise.


#### <a name="when-a-message-is-received-in-a-queue"></a>Lorsqu’un message est reçu dans une file d’attente

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|queueName *|Nom de la file d’attente|Nom de la file d’attente.|


Un * indique qu’une propriété est requise.


##### <a name="output-details"></a>Détails de sortie

ServiceBusMessage : Cet objet a le contenu et les propriétés d’un message de Service Bus.


| Nom de la propriété | Type de données | Description |
|---|---|---|
|ContentData|chaîne|Contenu du message.|
|ContentType|chaîne|Type de contenu du contenu du message.|
|Propriétés|objet|Demandé de paires clé-valeur pour chaque propriété.|
|MessageId|chaîne|Une valeur définie par l’utilisateur que le Bus de Service peut utiliser pour identifier les doublons de messages, si activé.|
|À|chaîne|Envoyer à l’adresse.|
|ReplyTo|chaîne|Adresse de la file d’attente de réponse à.|
|ReplyToSessionId|chaîne|Identificateur de la session pour y répondre.|
|Étiquette|chaîne|Étiquette de spécifique à l’application.|
|ScheduledEnqueueTimeUtc|chaîne|Date et heure en UTC, lorsque le message est ajouté à la file d’attente.|
|ID de session|chaîne|Identificateur de la session.|
|ID de corrélation|chaîne|Identificateur de la corrélation.|
|Propriété TimeToLive|chaîne|La durée, en graduations, qu’un message est valide. La durée commence lorsque le message est envoyé à un Bus de Service.|




#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>Lorsqu’un message est reçu dans un abonnement de rubrique

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|topicName *|Nom de la rubrique|Nom de la rubrique.|
|subscriptionName *|Nom de rubrique d’abonnement|Nom de l’abonnement de la rubrique.|


Un * indique qu’une propriété est requise.


##### <a name="output-details"></a>Détails de sortie

ServiceBusMessage : Cet objet a le contenu et les propriétés d’un message de Service Bus.


| Nom de la propriété | Type de données | Description |
|---|---|---|
|ContentData|chaîne|Contenu du message.|
|ContentType|chaîne|Type de contenu du contenu du message.|
|Propriétés|objet|Demandé de paires clé-valeur pour chaque propriété.|
|MessageId|chaîne|Une valeur définie par l’utilisateur que le Bus de Service peut utiliser pour identifier les doublons de messages, si activé.|
|À|chaîne|Envoyer à l’adresse.|
|ReplyTo|chaîne|Adresse de la file d’attente de réponse à.|
|ReplyToSessionId|chaîne|Identificateur de la session pour y répondre.|
|Étiquette|chaîne|Étiquette de spécifique à l’application.|
|ScheduledEnqueueTimeUtc|chaîne|Date et heure en UTC, lorsque le message est ajouté à la file d’attente.|
|ID de session|chaîne|Identificateur de la session.|
|ID de corrélation|chaîne|Identificateur de la corrélation.|
|Propriété TimeToLive|chaîne|La durée, en graduations, qu’un message est valide. La durée commence lorsque le message est envoyé à un Bus de Service.|



### <a name="http-responses"></a>Réponses HTTP

Les actions et les déclencheurs précédent peuvent renvoyer un ou plusieurs des codes d’état HTTP suivants :

|Nom|Description|
|---|---|
|200|Bien|
|202|Accepté|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite.|
|par défaut|Échoué de l’opération.|

## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).
