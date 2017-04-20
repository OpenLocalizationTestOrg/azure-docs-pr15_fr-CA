<properties
pageTitle="SMTP | Microsoft Azure"
description="Permet de créer des applications de logique avec le service d’application d’Azure. Se connecter à SMTP pour l’envoi de courrier électronique."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-smtp-connector"></a>Mise en route avec le connecteur SMTP

Se connecter à SMTP pour l’envoi de courrier électronique.

Pour utiliser [un connecteur](./apis-list.md), vous devez d’abord créer une application de logique. Vous pouvez commencer par [créer une application de logique maintenant](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-smtp"></a>Se connecter au service SMTP

Avant que votre application logique peut accéder à n’importe quel service, vous devez d’abord créer une *connexion* au service. Une [connexion](./connectors-overview.md) fournit une connectivité entre une application logique et un autre service. Par exemple, pour vous connecter au service SMTP, vous devez *connexion*SMTP. Pour créer une connexion, vous devez fournir les informations d’identification que vous utilisez normalement pour accéder au service que vous souhaitez vous connecter. Ainsi, dans l’exemple SMTP, vous devez les informations d’identification à votre nom de connexion, l’adresse du serveur SMTP et les informations de connexion utilisateur pour créer la connexion SMTP. [En savoir plus sur les connexions]()  

### <a name="create-a-connection-to-smtp"></a>Créer une connexion au service SMTP

>[AZURE.INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]

## <a name="use-an-smtp-trigger"></a>Utilisez un déclencheur de SMTP

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail défini dans une logique d’application. [En savoir plus sur les déclencheurs](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Dans cet exemple, car SMTP n’est pas un déclencheur qui lui sont propres, nous allons utiliser le déclencheur de la **force de vente - lorsqu’un objet est créé** . Ce déclencheur active lorsqu’un nouvel objet est créé dans la force de vente. Dans notre exemple, nous allons créer il telle que chaque fois qu’un nouveau prospect est créé dans la force de vente, une action *Envoyer e-mail* se produit via le connecteur SMTP avec une notification du nouveau prospect en cours de création.

1. Entrez la *force de vente* dans la zone de recherche dans le Concepteur d’applications logique, puis sélectionnez le déclencheur de la **force de vente - lorsqu’un objet est créé** .  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  

2. Le contrôle **lorsqu’un objet est créé** est affiché.
 ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  

3. Sélectionnez le **Type d’objet** , puis sélectionnez le *prospect* à partir de la liste d’objets. Dans cette étape vous indiquez que vous créez un déclencheur qui avertit votre application logique lors de la création d’un nouveau prospect dans la force de vente.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  

4. Le déclencheur a été créé.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Utilisez une action de SMTP

Une action est une opération effectuée par le flux de travail défini dans une logique d’application. [En savoir plus sur les actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Maintenant que le déclencheur a été ajouté, procédez comme suit pour ajouter une action de SMTP qui se produisent lorsqu’un prospect est créé dans la force de vente.

1. Sélectionnez **+ nouvelle étape** pour ajouter l’action que vous souhaitez prendre lors de la création d’un nouveau prospect.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  

2. Sélectionnez **Ajouter une action**. Ouvre la boîte de recherche dans lequel vous pouvez rechercher n’importe quelle action vous souhaitez prendre.  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  

3. Entrez *smtp* pour effectuer une recherche pour des actions liées au protocole SMTP.  

4. Sélectionnez l’action à entreprendre lors de la création du nouveau prospect **SMTP - envoyer un E-mail** . Le bloc de contrôle d’action s’ouvre. Vous devez établir votre connexion smtp dans le bloc de concepteur si vous le n'avez pas fait précédemment.  
 ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    

5. Entrez vos informations de messagerie de votre choix dans le bloc de **SMTP - envoyer un E-mail** .  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  

6. Pour activer votre flux de travail, enregistrez votre travail.  

## <a name="technical-details"></a>Détails techniques

Voici les détails sur les déclencheurs, les actions et les réponses qui prend en charge de cette connexion :

## <a name="smtp-triggers"></a>Déclencheurs de SMTP

SMTP n’a pas de déclencheurs. 

## <a name="smtp-actions"></a>Actions SMTP

SMTP a l’action suivante :


|Action|Description|
|--- | ---|
|[Envoyer un E-mail](connectors-create-api-smtp.md#send-email)|Cette opération envoie un message électronique à un ou plusieurs destinataires.|

### <a name="action-details"></a>Détails de l’action

Voici les détails de l’action de ce connecteur, ainsi que ses réponses :


### <a name="send-email"></a>Envoyer un E-mail
Cette opération envoie un message électronique à un ou plusieurs destinataires. 


|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|À|À|Spécifier les adresses de messagerie séparées par des points-virgules commerecipient1@domain.com;recipient2@domain.com|
|CC|cc|Spécifier les adresses de messagerie séparées par des points-virgules commerecipient1@domain.com;recipient2@domain.com|
|Objet|Objet|Objet du courriel|
|Corps|Corps|Corps de l’e-mail|
|De|De|Adresse e-mail de l’expéditeur commesender@domain.com|
|IsHtml|Est au format Html|Envoyer l’e-mail au format HTML (true/false)|
|Cci|Cci|Spécifier les adresses de messagerie séparées par des points-virgules commerecipient1@domain.com;recipient2@domain.com|
|Importance|Importance|Importance du message (haute, normale ou basse)|
|ContentData|Données de contenu des pièces jointes|Données de contenu (pour les flux de données et comme en Base64-est pour la chaîne)|
|ContentType|Type de contenu des pièces jointes|Type de contenu|
|ContentTransferEncoding|Codage de transfert de contenu des pièces jointes|Transférer le codage du contenu (base64 ou aucun)|
|Nom de fichier|Nom de fichier des pièces jointes|Nom de fichier|
|ContentId|ID de contenu des pièces jointes|Id de contenu|

Un * indique qu’une propriété est obligatoire


## <a name="http-responses"></a>Réponses HTTP

Les actions et les déclencheurs ci-dessus peuvent retourner un ou plusieurs des codes d’état HTTP suivants : 

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
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)