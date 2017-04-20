<properties
    pageTitle="Ajouter le connecteur du stockage blob Azure dans vos applications de logique | Microsoft Azure"
    description="Vue d’ensemble du stockage de blob Azure connecteur avec des paramètres de l’API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="anneta"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-azure-blob-storage-connector"></a>Mise en route avec le connecteur de stockage blob Azure
Stockage de Blob Azure est un service permettant de stocker de grandes quantités de données non structurées. Effectuer différentes actions comme le chargement, mettre à jour, obtenir et supprimer des objets BLOB dans le stockage blob Azure. 

Avec le stockage blob Azure, vous :

- Créer votre flux de travail en téléchargeant de nouveaux projets ou l’obtention de fichiers qui ont été récemment mis à jour.
- Utiliser des actions pour obtenir les métadonnées d’un fichier, supprimer un fichier, copier les fichiers et bien plus encore. Par exemple, lorsqu’un outil est mis à jour dans un site web Azure (un déclencheur), puis mettre à jour un fichier dans le stockage blob (action). 

Cette rubrique vous indique comment utiliser le connecteur du stockage blob dans une logique d’application et répertorie également les actions.

>[AZURE.NOTE] Cette version de l’article s’applique à la disponibilité des applications de logique (GA). 

Pour plus d’informations sur les applications de la logique, voir [Quelles sont les applications de logique](../app-service-logic/app-service-logic-what-are-logic-apps.md) et [créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-azure-blob-storage"></a>Se connecter au stockage du blob Azure

Avant que votre application logique peut accéder à n’importe quel service, vous créez tout d’abord une *connexion* au service. Une connexion fournit une connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à un compte de stockage, vous d’abord créez une *connexion*du stockage blob. Pour créer une connexion, entrez les informations d’identification que vous utilisez normalement pour accéder au service que vous vous connectez à. Ainsi, avec le stockage Azure, entrez les informations d’identification à votre compte de stockage pour créer la connexion. 

#### <a name="create-the-connection"></a>Créer la connexion

>[AZURE.INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]
 
## <a name="use-a-trigger"></a>Utilisation d’un déclencheur

Ce connecteur n’a pas de déclencheurs. Autres déclencheurs permet de démarrer l’application logique, comme un déclencheur de récurrence, un déclencheur Webhook de HTTP, déclencheurs disponibles avec les autres connecteurs et bien plus encore. [Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md) fournit un exemple.

## <a name="use-an-action"></a>Utilisez une action
    
Une action est une opération effectuée par le flux de travail défini dans une logique d’application.

1. Sélectionnez le signe plus. Vous consultez plusieurs choix : **Ajouter une action**, **Ajouter une condition**ou **plus** d’options.

    ![](./media/connectors-create-api-azureblobstorage/add-action.png)

2. Cliquez sur **Ajouter une action**.

3. Dans la zone de texte, tapez « blob » pour obtenir une liste de toutes les actions disponibles.

    ![](./media/connectors-create-api-azureblobstorage/actions.png) 

4. Dans notre exemple, choisissez **AzureBlob - obtenir les métadonnées d’un fichier à l’aide du chemin d’accès**. Si une connexion existe déjà, sélectionnez le **...** Bouton (Afficher sélecteur) pour sélectionner un fichier.

    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)

    Si vous êtes invité à entrer les informations de connexion, puis entrez les détails pour créer la connexion. [Créer la connexion](connectors-create-api-azureblobstorage.md#create-the-connection) de cette rubrique décrit ces propriétés. 

    > [AZURE.NOTE] Dans cet exemple, nous obtenons les métadonnées d’un fichier. Pour voir les métadonnées, ajoutez une autre action qui crée un nouveau fichier à l’aide d’un autre connecteur. Par exemple, ajouter une action OneDrive qui crée un nouveau fichier « test » basé sur les métadonnées. 

5. **Enregistrez** vos modifications (situé dans l’angle supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.

> [AZURE.TIP] [Explorateur de stockage](http://storageexplorer.com/) est un outil formidable pour gérer plusieurs comptes de stockage.

## <a name="technical-details"></a>Détails techniques

## <a name="storage-blob-actions"></a>Actions de Blob de stockage

|Action|Description|
|--- | ---|
|[Obtenir les métadonnées de fichiers](connectors-create-api-azureblobstorage.md#get-file-metadata)|Cette opération Obtient les métadonnées de fichier à l’aide d’id de fichier.|
|[Fichier de mise à jour](connectors-create-api-azureblobstorage.md#update-file)|Cette opération met à jour un fichier.|
|[Supprimer fichier](connectors-create-api-azureblobstorage.md#delete-file)|Cette opération supprime un fichier.|
|[Obtenir les métadonnées à l’aide du chemin d’accès d’un fichier](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path)|Cette opération Obtient les métadonnées de fichier en utilisant le chemin d’accès.|
|[Obtenir le contenu d’un fichier à l’aide du chemin d’accès](connectors-create-api-azureblobstorage.md#get-file-content-using-path)|Cette opération Obtient le contenu du fichier en utilisant le chemin d’accès.|
|[Obtenir le contenu d’un fichier](connectors-create-api-azureblobstorage.md#get-file-content)|Cette opération Obtient le contenu du fichier à l’aide de code.|
|[Créer fichier](connectors-create-api-azureblobstorage.md#create-file)|Cette opération transfère un fichier.|
|[Copiez le fichier](connectors-create-api-azureblobstorage.md#copy-file)|Cette opération copie un fichier dans le stockage Blob Azure.|
|[Extraire l’archive dans le dossier](connectors-create-api-azureblobstorage.md#extract-archive-to-folder)|Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip).|

### <a name="action-details"></a>Détails de l’action

Dans cette section, voir les détails relatifs à chaque action, y compris les propriétés d’entrée requises ou facultatives et toute associés au connecteur de sortie correspondante.

#### <a name="get-file-metadata"></a>Obtenir les métadonnées de fichiers
Cette opération Obtient les métadonnées de fichier à l’aide d’id de fichier.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|ID *|Fichier|Sélectionnez un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
BlobMetadata

| Nom de la propriété | Type de données |
|---|---|
|ID|chaîne|
|Nom|chaîne|
|DisplayName|chaîne|
|Chemin d’accès|chaîne|
|LastModified|chaîne|
|Taille|nombre entier|
|MediaType|chaîne|
|IsFolder|valeur booléenne|
|ETag|chaîne|
|FileLocator|chaîne|


#### <a name="update-file"></a>Fichier de mise à jour
Cette opération met à jour un fichier.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|ID *|Fichier|Sélectionnez un fichier|
|corps *|Contenu du fichier|Contenu du fichier à mettre à jour|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
BlobMetadata

| Nom de la propriété | Type de données |
|---|---|
|ID|chaîne|
|Nom|chaîne|
|DisplayName|chaîne|
|Chemin d’accès|chaîne|
|LastModified|chaîne|
|Taille|nombre entier|
|MediaType|chaîne|
|IsFolder|valeur booléenne|
|ETag|chaîne|
|FileLocator|chaîne|


#### <a name="delete-file"></a>Supprimer fichier
Cette opération supprime un fichier.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|ID *|Fichier|Sélectionnez un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Aucun.


#### <a name="get-file-metadata-using-path"></a>Obtenir les métadonnées à l’aide du chemin d’accès d’un fichier
Cette opération Obtient les métadonnées de fichier en utilisant le chemin d’accès.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|chemin d’accès *|Chemin d’accès du fichier|Sélectionnez un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
BlobMetadata

| Nom de la propriété | Type de données |
|---|---|
|ID|chaîne|
|Nom|chaîne|
|DisplayName|chaîne|
|Chemin d’accès|chaîne|
|LastModified|chaîne|
|Taille|nombre entier|
|MediaType|chaîne|
|IsFolder|valeur booléenne|
|ETag|chaîne|
|FileLocator|chaîne|


#### <a name="get-file-content-using-path"></a>Obtenir le contenu d’un fichier à l’aide du chemin d’accès
Cette opération Obtient le contenu du fichier en utilisant le chemin d’accès.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|chemin d’accès *|Chemin d’accès du fichier|Sélectionnez un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Aucun.


#### <a name="get-file-content"></a>Obtenir le contenu d’un fichier
Cette opération Obtient le contenu du fichier à l’aide de code.  

|Nom de la propriété| Type de données|Description|
| ---|---|---|
|ID *|chaîne|Sélectionnez un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Aucun.


#### <a name="create-file"></a>Créer fichier
Cette opération transfère un fichier.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|folderPath *|Chemin d’accès du dossier|Sélectionnez un dossier|
|nom *|Nom de fichier|Nom du fichier à télécharger|
|corps *|Contenu du fichier|Contenu du fichier à télécharger|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
BlobMetadata

| Nom de la propriété | Type de données | 
|---|---|
|ID|chaîne|
|Nom|chaîne|
|DisplayName|chaîne|
|Chemin d’accès|chaîne|
|LastModified|chaîne|
|Taille|nombre entier|
|MediaType|chaîne|
|IsFolder|valeur booléenne|
|ETag|chaîne|
|FileLocator|chaîne|


#### <a name="copy-file"></a>Copiez le fichier
Cette opération copie un fichier dans le stockage Blob Azure.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|source *|Url de la source|Spécifier l’Url vers le fichier source|
|destination *|Chemin d’accès du fichier de destination|Spécifiez le chemin du fichier de destination, y compris le nom de fichier cible|
|remplacer|Le remplacer ?|Doit un fichier de destination existant est remplacé (true/false) ?  |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
BlobMetadata

| Nom de la propriété | Type de données |
|---|---|
|ID|chaîne|
|Nom|chaîne|
|DisplayName|chaîne|
|Chemin d’accès|chaîne|
|LastModified|chaîne|
|Taille|nombre entier|
|MediaType|chaîne|
|IsFolder|valeur booléenne|
|ETag|chaîne|
|FileLocator|chaîne|

#### <a name="extract-archive-to-folder"></a>Extraire l’archive dans le dossier
Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip).  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|source *|Chemin d’accès du fichier source archive|Sélectionnez un fichier d’archive|
|destination *|Chemin d’accès du dossier de destination|Sélectionnez le contenu à extraire|
|remplacer|Le remplacer ?|Doit un fichier de destination existant est remplacé (true/false) ?|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
BlobMetadata

| Nom de la propriété | Type de données |
|---|---|
|ID|chaîne|
|Nom|chaîne|
|DisplayName|chaîne|
|Chemin d’accès|chaîne|
|LastModified|chaîne|
|Taille|nombre entier|
|MediaType|chaîne|
|IsFolder|valeur booléenne|
|ETag|chaîne|
|FileLocator|chaîne|


## <a name="http-responses"></a>Réponses HTTP

Lorsque vous appelez les différentes actions, vous pouvez obtenir certaines réponses. Le tableau suivant présente les réponses et leurs descriptions :  

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

## <a name="next-steps"></a>Étapes suivantes

[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Explorez les autres connecteurs disponibles dans les applications de logique à notre [liste d’API](apis-list.md).



