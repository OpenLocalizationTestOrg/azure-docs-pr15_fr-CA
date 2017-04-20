
<properties
    pageTitle="Pour ajouter le HTTP + Swagger action dans les applications de logique | Microsoft Azure"
    description="Vue d’ensemble du HTTP + Swagger action et opérations"
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

# <a name="get-started-with-the-http--swagger-action"></a>Mise en route avec le HTTP + Swagger action

Avec le HTTP + Swagger action, vous pouvez créer un connecteur de première classe à un point de terminaison reste via un [Swagger le document](https://swagger.io). Vous pouvez également étendre une application logique pour appeler n’importe quel point de terminaison reste une expérience de concepteur d’application logique de première classe.

Mise en route du HTTP + Swagger action dans une logique d’application, voir [Création d’une nouvelle application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Utiliser HTTP + Swagger sous la forme d’un déclencheur ou d’une action

Le HTTP + Swagger déclencheur et action fonctionnent de la même manière que l' [action HTTP](connectors-native-http.md) mais offrent une meilleure expérience de design en montrant la forme des API et des sorties dans le Concepteur de la [Swagger des métadonnées](https://swagger.io). En outre, vous pouvez utiliser HTTP + Swagger comme un déclencheur. Si vous souhaitez implémenter un déclencheur d’interrogation, il doit suivre le modèle d’interrogation qui est décrite dans [Création d’une API personnalisée à utiliser avec des applications de logique](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).

[Pour en savoir plus sur les actions et les déclencheurs d’application logique.](connectors-overview.md)

Voici un exemple d’utilisation du protocole HTTP + opération Swagger sous la forme d’une action dans un flux de travail dans une logique d’application.

1. Cliquez sur le bouton **Nouvelle étape** .
2. Sélectionnez **Ajouter une action**.
3. Dans la zone de recherche action, tapez **swagger** à la liste HTTP + Swagger action.

    ![Sélectionnez HTTP + Swagger action](./media/connectors-native-http-swagger/using-action-1.png)

4. Tapez l’URL d’un document Swagger :
    - Pour travailler dans le Concepteur d’application logique, l’URL doit être un point de terminaison HTTPS et avez activé les CORS.
    - Si le document Swagger ne répond pas à cette exigence, vous pouvez utiliser [Le stockage Azure avec CORS activé](#hosting-swagger-from-storage) pour stocker le document.
5. Cliquez sur **suivant** pour la lecture et le rendu du document Swagger.
6. Ajoutez tous les paramètres qui sont requis pour l’appel HTTP.

    ![Terminer action HTTP](./media/connectors-native-http-swagger/using-action-2.png)

1. Cliquez sur **Enregistrer** dans le coin supérieur gauche de la barre d’outils et votre logique application à la fois enregistrer et publier (activer).

### <a name="host-swagger-from-azure-storage"></a>Swagger hôte du stockage Azure

Vous pouvez souhaiter faire référence à un document de Swagger qui n’est pas hébergé, ou qui ne répond pas aux exigences de sécurité et origine croisée pour le concepteur. Pour résoudre ce problème, vous pouvez stocker le document Swagger dans le stockage Azure et activer les CORS référencer le document.  

Voici les étapes permettant de créer, de configurer et de stocker des documents de Swagger dans le stockage Azure :

1. [Création d’un compte de stockage Azure avec stockage des objets Blob Azure](../storage/storage-create-storage-account.md). (Pour ce faire, définir des autorisations pour **l’accès Public**).
2. Activer les CORS sur le blob. Vous pouvez utiliser [ce script PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) pour configurer ce paramètre automatiquement.
3. Téléchargez le fichier Swagger dans le blob. Vous pouvez pour cela à partir du [portail Azure](https://portal.azure.com) , ou à partir d’un outil comme [l’Explorateur de stockage Azure](http://storageexplorer.com/).
1. Faire référence à un lien HTTPS au document dans le stockage des objets Blob Azure. (Le lien suit le format `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`.)



## <a name="technical-details"></a>Détails techniques

Voici les détails pour les déclencheurs et les actions que cette HTTP + le Swagger prend en charge de connecteur.

## <a name="http--swagger-triggers"></a>HTTP + déclencheurs de Swagger

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail est défini dans une logique d’application. [Pour en savoir plus sur les déclencheurs.](connectors-overview.md) Le HTTP + Swagger connecteur possède un déclencheur.

|Déclencheur|Description|
|---|---|
|HTTP + Swagger|Effectuer un appel HTTP et de retourner le contenu de la réponse|

## <a name="http--swagger-actions"></a>HTTP + actions de Swagger

Une action est une opération qui est réalisée par le flux de travail est défini dans une logique d’application. [Pour en savoir plus sur les actions.](connectors-overview.md) Le HTTP + Swagger connecteur a une action possible.

|Action|Description|
|---|---|
|HTTP + Swagger|Effectuer un appel HTTP et de retourner le contenu de la réponse|

### <a name="action-details"></a>Détails de l’action

Le HTTP + Swagger connecteur est fourni avec une action possible. Voici des informations sur chacune des actions, les champs d’entrée requis et facultatifs et les détails de sortie correspondants qui sont associés à leur utilisation.

#### <a name="http--swagger"></a>HTTP + Swagger

Faire de demande HTTP sortante avec l’aide des métadonnées de Swagger.
Un astérisque (*) indique un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Méthode *|méthode|Verbe HTTP à utiliser.|
|URI *|URI|URI de la demande HTTP.|
|En-têtes|en-têtes|Un objet JSON d’en-têtes HTTP à inclure.|
|Corps|corps|Le corps de la demande HTTP.|
|Authentification|authentification|Authentification à utiliser pour la demande. [Pour plus d’informations, consultez HTTP](./connectors-native-http.md#authentication).|

**Détails de sortie**

Réponse HTTP

|Nom de la propriété|Type de données|Description|
|---|---|---|
|En-têtes|objet|En-têtes de réponse|
|Corps|objet|Objet de réponse|
|Code d’état|int|Code d’état HTTP|

### <a name="http-responses"></a>Réponses HTTP

Lors d’appels à diverses actions, vous pouvez obtenir certaines réponses. Voici un tableau qui souligne les descriptions et les réponses correspondantes.

|Nom|Description|
|---|---|
|200|Bien|
|202|Accepté|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite.|

---

## <a name="next-steps"></a>Étapes suivantes

Essayez dès maintenant à la plate-forme et de [créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md) . Vous pouvez explorer les autres connecteurs disponibles dans les applications de la logique en consultant notre [liste d’API](apis-list.md).
