<properties 
    pageTitle="Mise en route de fourniture de contenu à la demande de l’aide reste | Microsoft Azure" 
    description="Ce didacticiel vous guide à travers les étapes de l’implémentation d’une application de diffusion de contenu sur demande avec Azure Media Services à l’aide des API REST." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="juliako"/>

#<a name="get-started-with-delivering-content-on-demand-using-rest"></a>Mise en route de fourniture de contenu à la demande à l’aide d’autres 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 


Ce démarrage rapide vous guide à travers les étapes de l’implémentation d’une application de diffusion de contenu vidéo à la demande (VoD) à l’aide des API de reste Azure Media Services (AMS). 

Le didacticiel présente le flux de travail des Services de support et des objets de programmation courants plus les tâches requises pour le développement de Services de support. À la fin de ce didacticiel, vous serez en mesure de flux ou de téléchargement progressif d’un exemple de fichier multimédia que vous avez téléchargé, codés, téléchargé.  

## <a name="prerequisites"></a>Conditions préalables
Les conditions préalables suivantes sont requises pour commencer à développer avec les Services de support avec les API de reste.

- Présentation du développement avec l’API REST de Services multimédia. Pour plus d’informations, consultez [media-services-reste-vue d’ensemble](http://msdn.microsoft.com/library/azure/hh973616.aspx).
- L’application de votre choix qui peut envoyer des demandes et réponses HTTP. Ce didacticiel utilise [Fiddler](http://www.telerik.com/download/fiddler). 

Les tâches suivantes sont affichées dans ce démarrage rapide.

1.  Créez un compte de Services de support (en utilisant le portail Azure).
2.  Configurer le point de terminaison en continu (en utilisant le portail Azure).
1.  Se connecter sur le compte de Services de support avec l’API REST.
1.  Créer une nouvelle immobilisation et télécharger un fichier vidéo avec l’API REST.
1.  Configurer les unités de transmission en continu avec les API REST.
2.  Encoder le fichier source dans un ensemble de fichiers MP4 de débit adaptatif avec l’API REST.
1.  Publier l’actif et le get de diffusion en continu et l’URL de téléchargement progressif avec l’API REST. 
1.  Lire votre contenu. 

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Créer un compte Azure Media Services via le portail Azure

Les étapes de cette section montrent comment créer un compte de l’AMS.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ nouvelle** > **Media + CDN** > **Media Services**.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Dans **Créer un compte de SERVICES de support** , entrez les valeurs requises.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Dans la zone **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte de Services de support est de tous les nombres en minuscules ou lettres sans espace et 3 à 24 caractères.
    2. Abonnement, sélectionnez parmi les différents abonnements Azure auxquelles vous avez accès à.
    
    2. Dans le **Groupe de ressources**, sélectionnez la ressource nouvelle ou existante.  Un groupe de ressources est un ensemble de ressources qui partagent le cycle de vie, les autorisations et les stratégies. Pour en savoir plus [ici](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Dans **emplacement**, sélectionnez la région géographique est utilisé pour stocker les enregistrements des supports et des métadonnées pour votre compte de Services de support. Cette zone est utilisée pour traiter et de diffuser vos médias. Seules les régions de Media Services disponibles s’affichent dans la zone de liste déroulante. 
    
    3. Dans le **Compte de stockage**, sélectionnez un compte de stockage pour fournir le stockage blob du contenu multimédia à partir de votre compte de Services de support. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique, comme votre compte de Services de support, ou vous pouvez créer un compte de stockage. Un nouveau compte de stockage est créé dans la même région. Les règles pour les noms de compte de stockage sont les mêmes que pour les comptes de Services de support.

        En savoir plus sur le stockage [ici](storage-introduction.md).

    4. Sélectionnez **Ajouter au tableau de bord** pour voir la progression du déploiement compte.
    
7. Cliquez sur **créer** en bas de l’écran.

    Une fois que le compte est créé avec succès, le statut devient **en cours d’exécution**. 

    ![Paramètres des Services de support](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Pour gérer votre compte AMS (par exemple, télécharger des vidéos, coder actifs, surveiller la progression de la tâche) utilisez la fenêtre **paramètres** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurer des points de terminaison en continu via le portail Azure

Lorsque vous travaillez avec Azure Media Services est un des scénarios plus courants est offrant des performances vidéo via adaptive vitesse de transmission en continu à vos clients. Media Services prend en charge la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement).

Media Services fournit un emballage dynamique, ce qui vous permet de remettre votre contenu MP4 encodé en continu des formats pris en charge par les Services de support (MPEG tiret, TLS, diffusion en continu lisse, HDS) juste-à-temps, sans avoir à stocker des versions intégrées de chacun de ces formats de transmission en continu de vitesse de transmission adaptive.

Pour tirer parti de l’emballage dynamique, vous devez effectuer les opérations suivantes :

- Coder votre fichier mezzanine (source) dans un ensemble de fichiers MP4 de débit adaptatif (les étapes de codage sont expliqués plus loin dans ce didacticiel).  
- Créer au moins une unité de transmission en continu pour le *point de terminaison de la diffusion en continu* à partir de laquelle vous envisagez de livraison votre contenu. Les étapes ci-dessous montrent comment modifier le nombre d’unités de transmission en continu.

Avec emballage dynamique, il vous suffit de stocker et de payer pour les fichiers au format de stockage unique et Media Services génère et remplit la réponse appropriée en fonction de la demande du client.

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, effectuez les opérations suivantes :


1. Dans la fenêtre **paramètres** , cliquez sur **les points de terminaison en flux continu**. 

2. Cliquez sur la valeur par défaut de diffusion en continu de point de terminaison. 

    La fenêtre **Détails de point de terminaison de diffusion en continu par défaut** s’affiche.

3. Pour spécifier le nombre d’unités de transmission en continu, faites glisser le curseur de **l’unité de transmission en continu** .

    ![Unités de transmission en continu](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

    >[AZURE.NOTE]L’allocation de toutes nouvelles unités peut prendre jusqu'à 20 minutes.

## <a id="connect"></a>Se connecter sur le compte de Services de support avec l’API REST

Deux choses sont requis lors de l’accès aux Services de support Azure : un jeton d’accès fourni par Azure Access Control Services (ACS) et l’URI de Services média lui-même. Vous pouvez utiliser tout moyen souhaité lors de la création de ces requêtes tant que vous spécifiez les valeurs d’en-tête correct et passez le jeton d’accès correctement lors de l’appel dans les Services de support.

Les étapes suivantes décrivent le flux de travail plus courante lors de l’utilisation de l’API REST de Services média pour vous connecter à des Services multimédias :

1. Obtention d’un jeton d’accès. 
2. Connexion aux Services de support URI.  

    N’oubliez pas qu’après avoir été connecté à https://media.windows.net, vous recevez un réacheminement 301 spécifiant un autre URI de Services multimédia. Vous devez faire d’autres appels à l’URI de nouveau. Vous pouvez également recevoir une réponse HTTP/1.1 200 qui contient la description des métadonnées ODATA API.
3. Validation de vos appels d’API suivants vers la nouvelle URL. 
    
    Par exemple, si, après avoir essayé de vous connecter, vous avez les éléments suivants :
        
        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Vous devez enregistrer vos appels d’API suivants à https://wamsbayclus001rest-hs.cloudapp.net/api/.

###<a name="getting-an-access-token"></a>L’obtention d’un jeton d’accès

Pour accéder aux Services de support directement par le biais de l’API REST, ils récupèrent un jeton d’accès ACS et l’utiliser lors de chaque demande HTTP effectuée dans le service. Vous n’avez pas besoin de toutes les autres conditions préalables avant de se connecter directement à des Services multimédias.

L’exemple suivant affiche l’en-tête de demande HTTP et le corps permet de récupérer un jeton.

**En-tête**:

    POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Host: wamsprodglobal001acs.accesscontrol.windows.net
    Content-Length: 120
    Expect: 100-continue
    Connection: Keep-Alive
    Accept: application/json

    
**Corps**:

Vous devez prouvé les valeurs identifiant_client et client_secret dans le corps de cette demande ; identifiant_client et client_secret correspondent aux valeurs NomCompte et AccountKey, respectivement. Ces valeurs sont fournies par les Services de support pour vous lorsque vous configurez votre compte. 

La AccountKey de votre compte de Services de support doit être codé en URL lorsque vous l’utilisez comme valeur de client_secret dans votre demande de jeton d’accès.

    grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Par exemple : 

    grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


L’exemple suivant montre la réponse HTTP qui contient de l’accès au jeton dans le corps de réponse.

    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    request-id: a65150f5-2784-4a01-a4b7-33456187ad83
    X-Content-Type-Options: nosniff
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 15 Jan 2015 08:07:20 GMT
    Content-Length: 670
    
    {  
       "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
Il est recommandé de mettre en cache les valeurs « access_token » et « expires_in » à un système de stockage externe. Les données de jetons peuvent ultérieurement être récupérées à partir du stockage et réutilisées dans vos appels de l’API REST de Services média. Ceci est particulièrement utile pour les scénarios où le jeton peut être partagé en toute sécurité entre plusieurs processus ou des ordinateurs.

Veillez à contrôler la valeur de « expires_in » du jeton d’accès et les appels de l’API REST de mettre à jour avec nouveaux jetons en fonction des besoins.

###<a name="connecting-to-the-media-services-uri"></a>Connexion aux Services de support URI

La racine URI de Media Services est https://media.windows.net/. Vous devez vous connecter initialement à cet URI, et si vous obtenez un réacheminement 301 dans la réponse, vous devez les appels ultérieurs à l’URI de nouveau. En outre, n’utilisez pas de logique de redirection automatique/suivre dans vos requêtes. Verbes HTTP et le corps de la demande ne seront pas transférés au nouvel URI.

La racine URI pour le téléchargement des fichiers de ressource est https://yourstorageaccount.blob.core.windows.net/ où le nom de compte de stockage est le même que celui utilisé lors de la configuration de votre compte de Services de support.

L’exemple suivant montre la demande HTTP à l’URI (https://media.windows.net/) de la racine des Services de support. La demande Obtient un réacheminement 301 dans la réponse. La requête suivante utilise le nouvel URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Requête HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: media.windows.net


**Réponse HTTP**:
    
    HTTP/1.1 301 Moved Permanently
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
    Server: Microsoft-IIS/8.5
    request-id: 987d7652-497a-44e5-b815-f492e02aef97
    x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:53 GMT
    Content-Length: 164
    
    <html><head><title>Object moved</title></head><body>
    <h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
    </body></html>


**Requête HTTP** (à l’aide du nouvel URI) :
            
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: wamsbayclus001rest-hs.cloudapp.net


**Réponse HTTP**:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1250
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
     


>[AZURE.NOTE] Désormais le nouvel URI est utilisé dans ce didacticiel.

## <a id="upload"></a>Créer une nouvelle immobilisation et le téléchargement d’un fichier vidéo avec l’API REST

Dans les Services de support, vous téléchargez vos fichiers numériques dans une immobilisation. L’entité **Asset** peut contenir, vidéo, audio, images, collections de miniatures, texte pistes et sous-titres fichiers (et les métadonnées relatives à ces fichiers.)  Une fois que les fichiers sont téléchargés dans l’actif, votre contenu est stocké en toute sécurité dans le nuage pour la poursuite du traitement et la diffusion en continu. 

Une des valeurs que vous devez fournir lors de la création d’une immobilisation est les options de création de ressources. La propriété **Options** est une valeur d’énumération qui décrit une immobilisation peut être créée avec les options de cryptage. Une valeur valide est une des valeurs de la liste ci-dessous, pas une combinaison de valeurs à partir de cette liste :

 
- **Aucun** = **0** - aucun chiffrement n’est utilisé. Lors de l’utilisation de cette option votre contenu n’est pas protégé en transit ou au repos dans le stockage.
    Si vous souhaitez fournir un MP4 utilisant le téléchargement progressif, utilisez cette option. 
- **StorageEncrypted** = **1** - chiffre votre contenu clair localement en utilisant un cryptage AES-256 bits, puis la télécharge vers le stockage Azure où il est stocké est crypté au repos. Les ressources protégées par un cryptage de stockage sont automatiquement et placés dans un système de fichiers cryptés avant le codage et non éventuellement cryptés avant de le télécharger comme une nouvelle immobilisation de sortie. Cas d’usage principal pour le cryptage du stockage est lorsque vous souhaitez sécuriser vos fichiers de média d’entrée de haute qualité avec le cryptage fort au repos sur le disque.
- **CommonEncryptionProtected** = **2** - Utilisez cette option si vous téléchargez le contenu qui a été déjà chiffré et protégé par DRM PlayReady (par exemple, diffusion en continu lisse protégé avec PlayReady DRM) ou de cryptage commun.
- **EnvelopeEncryptionProtected** = **4** – Utilisez cette option si vous téléchargez TLS cryptée avec AES. Les fichiers doivent codés et chiffrés par Gestionnaire de transformation.

### <a name="create-an-asset"></a>Créer un actif

Un actif est un conteneur pour plusieurs types ou des ensembles d’objets dans les Services multimédias, y compris la vidéo, audio, images, collections de miniatures, suivis de texte et des fichiers de sous-titrage. Dans l’API REST, la création d’une ressource nécessite envoi requête POST pour les Services de support et de placer les informations de propriété sur vos ressources dans le corps de la demande.

L’exemple suivant montre comment créer une immobilisation.

**Requête HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45
    
    {"Name":"BigBuckBunny.mp4", "Options":"0"}
    

**Réponse HTTP**

Si l’opération réussit, le suivant est renvoyé :
    
    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
### <a name="create-an-assetfile"></a>Créer un AssetFile

L’entité [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) représente un fichier audio ou vidéo qui est stocké dans un conteneur d’objet blob. Un fichier de ressources est toujours associé à une immobilisation et une immobilisation peut contenir un ou plusieurs des AssetFiles. La tâche de Media Services Encoder échoue si un objet de fichier de ressources n’est pas associé à un fichier numérique dans un conteneur d’objet blob.

Après avoir téléchargé vos fichiers multimédias numériques dans un conteneur blob, la demande HTTP **de fusion** vous permet de mettre à jour la AssetFile avec les informations à propos de votre fichier multimédia (comme indiqué plus loin dans la rubrique). 

**Requête HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Réponse HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Création de la AccessPolicy avec autorisation d’écriture. 

Avant de télécharger des fichiers dans le stockage blob, définir l’accès à des droits de stratégie d’écriture d’une ressource. Pour ce faire, VALIDEZ une demande HTTP pour le jeu d’entités AccessPolicies. Définir une valeur de DurationInMinutes lors de la création ou vous recevez un message d’erreur de serveur interne 500 en réponse. Pour plus d’informations sur AccessPolicies, reportez-vous à la section [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

L’exemple suivant montre comment créer un AccessPolicy :
        
**Requête HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Réponse HTTP**

    If successful, the following response is returned:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Obtenir l’URL de téléchargement

Pour obtenir l’URL de téléchargement réel, créer un localisateur de SAS. Localisateurs de définissent l’heure de début et le type de point de terminaison de connexion pour les clients qui souhaitent accéder à des fichiers d’un actif. Vous pouvez créer plusieurs entités de localisateur pour deux AccessPolicy et actifs gérer les différentes demandes des clients et des besoins. Chacun de ces repères utilise la valeur de l’heure de début ainsi que la valeur DurationInMinutes de la AccessPolicy pour déterminer la durée pendant laquelle qu'une URL peut être utilisée. Pour plus d’informations, consultez le [localisateur](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Une URL de SAS a le format suivant :

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Certaines considérations s’appliquent :

- Vous ne pouvez pas avoir plus de cinq localisateurs uniques associés à un actif à un moment donné. Pour plus d’informations, consultez le localisateur.
- Si vous avez besoin télécharger vos fichiers immédiatement, vous devez définir votre valeur StartTime à cinq minutes avant l’heure actuelle. C’est parce qu’il peut y avoir horloge inclinaison entre votre ordinateur client et les Services de support. Également, votre valeur StartTime doit être dans le format DateTime suivant : AAAA-MM-JJThh (par exemple, « 2014-05-23T17:53:50Z »).   
- Il peut y avoir de 30 à 40 secondes délai après la création d’un localisateur pour lorsqu’il est disponible pour utilisation. Ce problème s’applique aux URL des associations de sécurité et de repères d’origine.

L’exemple suivant montre comment créer un localisateur URL SAS, tel que défini par la propriété de Type dans le corps de la demande (« 1 » pour un localisateur SAS) et « 2 » pour un localisateur d’origine de la demande. La propriété de **chemin d’accès** retournée contient l’URL qui vous permet de charger votre fichier.
    
**Requête HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178
    
    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Réponse HTTP**

Si l’opération réussit, la réponse suivante est retournée :
        
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Télécharger un fichier dans un conteneur de stockage des blob
    
Une fois le AccessPolicy et Locator définie, le fichier est téléchargé à un conteneur de stockage des objets Blob Azure à l’aide de l’API reste du stockage Azure. Vous pouvez télécharger dans la page ou bloquer des objets BLOB. 

>[AZURE.NOTE] Vous devez ajouter le nom de fichier pour le fichier que vous souhaitez télécharger dans la valeur de localisateur **chemin** reçue dans la section précédente. Par exemple, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Pour plus d’informations sur l’utilisation des objets BLOB de stockage Azure, voir [API de Blob Service REST](http://msdn.microsoft.com/library/azure/dd135733.aspx).


### <a name="update-the-assetfile"></a>Mise à jour de la AssetFile 

Maintenant que vous avez téléchargé le fichier, mettre à jour les informations de FileAsset taille (et autres). Par exemple :
    
    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Réponse HTTP**

Si réussie, le suivant est retourné : HTTP/1.1 204 sans contenu

## <a name="delete-the-locator-and-accesspolicy"></a>Supprimer le repère et AccessPolicy 

**Requête HTTP**


    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

    
**Réponse HTTP**

Si l’opération réussit, le suivant est renvoyé :

    HTTP/1.1 204 No Content 
    ...

**Requête HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Réponse HTTP**

Si l’opération réussit, le suivant est renvoyé :

    HTTP/1.1 204 No Content 
    ...

 
## <a id="configure_streaming_units"></a>Configurer des unités de transmission en continu avec les API REST

Lorsque vous travaillez avec Azure Media Services est un des scénarios plus courants remet adaptive vitesse de transmission en continu à vos clients. Avec la vitesse de transmission adaptative en continu, le client peut passer en un flux de taux d’échantillonnage supérieur ou inférieur la vidéo s’affiche en fonction de la bande passante réseau, l’utilisation du processeur et d’autres facteurs. Media Services prend en charge la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement). 

Media Services fournit un emballage dynamique, ce qui vous permet de fournir le contenu de votre vitesse de transmission adaptive MP4 ou diffusion en continu lisse codé en continu des formats pris en charge par les Services de support (MPEG tiret, TLS, diffusion en continu lisse, HDS) sans avoir à recompresser dans ces formats de transmission en continu. 

Pour tirer parti de l’emballage dynamique, vous devez effectuer les opérations suivantes :

- obtenir au moins une unité de transmission en continu pour le **point de terminaison de la diffusion en continu **à partir de laquelle vous souhaitez livrer votre contenu (décrite dans cette section).
- coder ou transcoder des fichiers de votre mezzanine (source) dans un ensemble de fichiers de vitesse de transmission adaptive MP4 ou de vitesse de transmission adaptive Smooth Streaming fichiers (les étapes de codage sont expliqués plus loin dans ce didacticiel),  

Avec emballage dynamique, il vous suffit de stocker et de payer pour les fichiers au format de stockage unique et Media Services génère et remplit la réponse appropriée en fonction de la demande du client. 


>[AZURE.NOTE] Pour plus d’informations sur les détails de la tarification, voir les [Détails de la tarification de Services média](http://go.microsoft.com/fwlink/?LinkId=275107).

Pour modifier le nombre d’unités réservées de diffusion en continu, effectuez les opérations suivantes :
    
### <a name="get-the-streaming-endpoint-you-want-to-update"></a>Obtenir le point de terminaison en continu que vous souhaitez mettre à jour

Par exemple, nous allons apprendre le premier point de terminaison en continu dans votre compte (vous pouvez avoir jusqu'à deux points de terminaison en continu en état de marche en même temps.)

**Requête HTTP**:

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Réponse HTTP**
    
Si l’opération réussit, le suivant est renvoyé :
    
    HTTP/1.1 200 OK
    . . . 
    
### <a name="scale-the-streaming-endpoint"></a>Mettre à l’échelle le point de terminaison en continu
 
**Requête HTTP**:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
    Content-Type: application/json;odata=verbose
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {"scaleUnits":1}

**Réponse HTTP**

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:16:43 GMT
    Content-Length: 0

    
### <a id="long_running_op_status"></a>Vérifier l’état d’une opération longue

L’allocation de toutes nouvelles unités prend environ 20 minutes. Pour vérifier l’état de l’opération, utilisez la méthode **d’opérations** et de spécifier l’Id de l’opération. L’opération N° a retourné dans la réponse à la demande de **l’échelle** .

    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**Requête HTTP**:
    
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
**Réponse HTTP**
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 515
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
    request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:57:39 GMT
    
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
          },
          "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
          "State":"Succeeded",
          "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
          "ErrorCode":null,
          "ErrorMessage":null
       }
    }


## <a id="encode"></a>Coder le fichier source dans un ensemble de fichiers MP4 de vitesse de transmission adaptive

Avant après ingestion Qu'actifs dans Media Services, les supports peuvent être codés, transmuxed, filigrane, etc., il est remis aux clients. Ces activités sont planifiées et exécutées par rapport à plusieurs instances de rôle en arrière-plan pour assurer la disponibilité et des performances élevées. Ces activités sont appelées tâches, et chaque [tâche](http://msdn.microsoft.com/library/azure/hh974289.aspx) est composé de tâches atomiques qui effectuent le travail réel sur le fichier actif. 

Comme mentionné précédemment, lorsque vous utilisez un des scénarios plus courants remet adaptive vitesse de transmission en continu à vos clients des Services Azure Media. Media Services peut empaqueter dynamiquement d’un ensemble de fichiers de la vitesse de transmission adaptive MP4 dans un des formats suivants : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement). 

Pour tirer parti de l’emballage dynamique, vous devez effectuer les opérations suivantes :

- coder ou transcoder des fichiers votre mezzanine (source) dans un ensemble de fichiers de vitesse de transmission adaptive MP4 ou les fichiers de diffusion en continu lisse adaptive de vitesse de transmission,  
- obtenir au moins une unité de transmission en continu pour le point de terminaison en continu à partir de laquelle vous souhaitez livrer votre contenu. 

La section suivante montre comment créer un projet qui contient une tâche de codage. La tâche spécifie le fichier de la mezzanine à transcoder dans un ensemble de transmission adaptive MP4s à l’aide de **Media Encoder Standard**. La section indique également comment faire pour surveiller la progression de traitement d’une tâche. Lorsque la tâche est terminée, vous serait en mesure de créer des repères qui sont nécessaires pour accéder à vos ressources. 

### <a name="get-a-media-processor"></a>Obtenir un processeur média

Dans les Services de support, un processeur de média est un composant qui gère une tâche de traitement spécifiques, telles que le codage, la conversion de format, de cryptage ou de décryptage le contenu multimédia. Pour la tâche de codage indiquée dans ce didacticiel, nous allons utiliser la norme de codage Media.

Le code suivant demande les id de l’encodeur. 

**Requête HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Réponse HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Créer une tâche

Chaque tâche peut avoir une ou plusieurs tâches en fonction du type de traitement que vous souhaitez accomplir. Par le biais de l’API REST, vous pouvez créer des tâches et leurs tâches associées de deux manières : des tâches peuvent être défini en ligne par le biais de la propriété de navigation de tâches sur les entités de la tâche, ou par le biais de traitement par lots de OData. Le Kit de développement de Services de support utilise le traitement par lots. Toutefois, pour la lisibilité des exemples de code dans cette rubrique, les tâches sont définies inline. Pour plus d’informations sur le traitement par lots, voir la rubrique [Traitement des lots d’Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

L’exemple suivant vous montre comment créer et publier un projet avec une que tâche définie pour encoder une vidéo à une résolution spécifique et de la qualité. La section suivante de la documentation contient la liste de tous les [paramètres prédéfinis de tâches](http://msdn.microsoft.com/library/mt269960) pris en charge par le processeur de Media Encoder Standard.  

**Requête HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482
    
    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Réponse HTTP**

Si l’opération réussit, la réponse suivante est retournée :

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  
    
             ]
          }
       }
    }


Il existe quelques points importants à noter dans toute demande de tâche :

- TaskBody propriétés doivent utiliser le littéral XML pour définir le nombre d’entrées ou sorties d’actifs qui sont utilisés par la tâche. La rubrique de la tâche contient la définition de schéma XML pour le fichier XML.
- Dans la définition de TaskBody, chaque interne de valeur pour <inputAsset> et <outputAsset> doit être défini comme JobInputAsset(value) ou JobOutputAsset(value).
- Une tâche peut avoir plusieurs capitaux de sortie. Un JobOutputAsset(x) utilisable uniquement une fois sous la forme d’une sortie d’une tâche dans un projet.
- Vous pouvez spécifier JobInputAsset ou JobOutputAsset comme un actif d’entrée d’une tâche.
- Tâches ne doivent pas former un cycle.
- Le paramètre de la valeur que vous passez à JobInputAsset ou JobOutputAsset représente la valeur d’index pour une immobilisation. Les ressources réelles sont définis dans les propriétés de navigation InputMediaAssets et OutputMediaAssets sur la définition de l’entité tâche. 

>[AZURE.NOTE] Media Services s’appuie sur OData v3, les immobilisations individuelles dans les ensembles de propriétés de navigation InputMediaAssets et OutputMediaAssets sont référencées par un « __metadata : uri « paire nom-valeur. 

- InputMediaAssets correspond à un ou plusieurs actifs que vous avez créé dans les Services de support. OutputMediaAssets sont créés par le système. Ils ne font pas référencent à une immobilisation existante.
- OutputMediaAssets peut être nommé à l’aide de l’attribut assetName. Si cet attribut n’est pas présent, alors que le nom de la OutputMediaAsset est quelle que soit la valeur de texte interne de la <outputAsset> élément est le suffixe de la valeur du nom de la tâche, soit de la valeur de l’Id de la tâche (dans le cas où la propriété Name n’est pas définie). Par exemple, si vous définissez une valeur pour assetName de « Sample », la propriété Name de OutputMediaAsset être définie à « Sample ». Toutefois, si vous n’avez pas défini de valeur pour assetName, mais n’avez défini le nom du travail à « NewJob », alors le nom OutputMediaAsset est « _NewJob JobOutputAsset (valeur) ». 

    L’exemple suivant montre comment définir l’attribut assetName :
    
        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"


- Pour activer le chaînage de la tâche :

    - Un projet doit avoir au moins deux tâches
    - Il doit y avoir au moins une tâche dont l’entrée est la sortie d’une autre tâche dans le projet.

Pour plus d’informations, consultez [Création d’un travail de codage avec l’API REST de Services multimédia](http://msdn.microsoft.com/library/azure/jj129574.aspx).

### <a name="monitor-processing-progress"></a>Moniteur de traitement de progression

Vous pouvez récupérer l’état du travail à l’aide de la propriété de l’état, comme illustré dans l’exemple suivant. 

**Requête HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Réponse HTTP**

Si l’opération réussit, la réponse suivante est retournée :

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT
    
    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Annulation d’une tâche

Media Services vous permet d’annuler des tâches en cours d’exécution par le biais de la fonction CancelJob. Cet appel renvoie un code d’erreur 400 si vous tentez d’annuler un travail lorsque son état est annulé, l’annulation, l’erreur ou terminé.

L’exemple suivant montre comment appeler CancelJob.


**Requête HTTP**


    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
    Host: wamsbayclus001rest-hs.net


Si l’opération réussit, un code de 204 réponse est retourné sans corps de message.

>[AZURE.NOTE] Vous devez coder en URL l’id de travail (normalement nb:jid:UUID : somevalue) lors de son passage dans en tant que paramètre à CancelJob.


### <a name="get-the-output-asset"></a>Obtenir de l’actif de sortie 

Le code suivant montre comment demander l’immobilisation de sortie ID. 


**Requête HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Réponse HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }



## <a id="publish_get_urls"></a>Publier les actifs et get de diffusion en continu et les URL de téléchargement progressif avec l’API REST

Pour transmettre en continu ou télécharger un actif, vous devez d’abord « publier » en créant un localisateur. Localisateurs de fournissent l’accès aux fichiers contenus dans l’immobilisation. Media Services prend en charge deux types de localisateurs : localisateurs de OnDemandOrigin, utilisés pour diffuser des médias (par exemple, MPEG tiret, TLS ou diffusion en continu lisse) et localisateurs de Signature accès (SAS), utilisé pour télécharger des fichiers multimédias.

Après avoir créé les repères, vous pouvez créer les URL qui sont utilisées pour transmettre en continu ou télécharger vos fichiers. 


Une URL de transmission en continu pour la diffusion en continu lisse a le format suivant :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Une URL de transmission en continu pour TLS a le format suivant :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Une URL de transmission en continu pour le MPEG tiret a le format suivant :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Une URL de SAS permet de télécharger des fichiers a le format suivant :

    {blob container name}/{asset name}/{file name}/{SAS signature}

Cette section montre comment effectuer les tâches suivantes nécessaires pour « publier » des actifs.  

- Création de la AccessPolicy avec l’autorisation de lecture 
- Création d’une URL de SAS pour le téléchargement de contenu 
- Création d’une URL d’origine pour la diffusion de contenu 

###<a name="creating-the-accesspolicy-with-read-permission"></a>Création de la AccessPolicy avec l’autorisation de lecture

Avant de télécharger ou de tout contenu média de diffusion en continu, tout d’abord définir un AccessPolicy avec les autorisations de lecture et de créer l’entité appropriée de localisateur qui spécifie le type de mécanisme de livraison que vous souhaitez activer pour vos clients. Pour plus d’informations sur les propriétés disponibles, consultez [Les propriétés d’entité AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties).

L’exemple suivant montre comment spécifier le AccessPolicy pour les autorisations de lecture pour un élément donné.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue
    
    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Si l’opération réussit, un code de 201 réussite est retourné décrivant l’entité AccessPolicy que vous avez créé. Vous utilisez l’AccessPolicy Id avec le code d’actif de l’actif qui contient le fichier que vous souhaitez livrer (par exemple, un actif de sortie) pour créer l’entité Locator.

>[AZURE.NOTE]
Ce flux de travail est le même que le téléchargement d’un fichier lors de l’ingestion d’une immobilisation (comme indiqué plus haut dans cette rubrique). Également, comme le téléchargement de fichiers, si vous (ou vos clients) doivent accéder immédiatement à vos fichiers, définir votre valeur StartTime à cinq minutes avant l’heure actuelle. Cette action n’est nécessaire, car il peut y avoir horloge maximal autorisé entre le client et les Services de support. La valeur de l’heure de début doit être dans le format DateTime suivant : AAAA-MM-JJThh (par exemple, « 2014-05-23T17:53:50Z »).


###<a name="creating-a-sas-url-for-downloading-content"></a>Création d’une URL de SAS pour le téléchargement de contenu 

Le code suivant montre comment obtenir une URL qui peut être utilisée pour télécharger un fichier multimédia créé et téléchargé précédemment. Le AccessPolicy a lu le jeu d’autorisations et le chemin d’accès de recherche fait référence à une URL de téléchargement d’associations de sécurité.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Si l’opération réussit, la réponse suivante est retournée :

    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }


La propriété de **chemin d’accès** retournée contient l’URL de SAS.

>[AZURE.NOTE]
Si vous téléchargez le contenu du stockage crypté, vous devez décrypter avant de le rendre manuellement ou utiliser le MediaProcessor de décryptage de stockage dans une tâche de traitement des fichiers traités en clair pour un OutputAsset de sortie et de télécharger à partir de cet actif. Pour plus d’informations sur le traitement, consultez Création d’un travail de codage avec l’API REST de Services multimédia. Également, SAS URL localisateurs ne peuvent pas être mis à jour après que qu’ils ont été créés. Par exemple, vous ne pouvez pas réutiliser la même recherche avec une valeur StartTime mise à jour. C’est en raison de la façon dont les URL de SAS sont créées. Si vous souhaitez accéder à une ressource pour un téléchargement après qu’un localisateur a expiré, vous devez créer un nouveau avec une nouvelle heure de début.

###<a name="download-files"></a>Télécharger des fichiers

Une fois le AccessPolicy et Locator définie, vous pouvez télécharger des fichiers à l’aide de l’API reste du stockage Azure.  

>[AZURE.NOTE] Vous devez ajouter le nom de fichier pour le fichier que vous souhaitez télécharger la valeur Locator **chemin** reçu dans la section précédente. Par exemple, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Pour plus d’informations sur l’utilisation des objets BLOB de stockage Azure, voir [API de Blob Service REST](http://msdn.microsoft.com/library/azure/dd135733.aspx).

À la suite de la tâche d ' encodage que vous avez effectuée antérieures (codage en jeu de MP4 Adaptive), vous avez plusieurs fichiers MP4 que vous pouvez télécharger progressivement. Par exemple :    
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


### <a name="creating-a-streaming-url-for-streaming-content"></a>Création d’une URL de transmission en continu de contenu de diffusion en continu


Le code suivant montre comment créer un localisateur URL en continu :

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Si l’opération réussit, la réponse suivante est retournée :

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Pour diffuser une URL d’origine de diffusion en continu lisse dans un lecteur multimédia en continu, vous devez ajouter le chemin d’accès de propriété avec le nom de la diffusion en continu lisse manifeste du fichier, suivi de « / manifeste ».

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Pour diffuser le TLS, ajouter (format = m3u8-aapl) après le « / manifeste ».

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Pour flux MPEG tiret, ajouter (format = mpd-heure-csf) après le « / manifeste ».

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Lire votre contenu  

Pour diffuser une vidéo vous, utilisez [Le lecteur Media Services Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Pour tester le téléchargement progressif, collez une adresse URL dans un navigateur (par exemple, Internet Explorer, Chrome, Safari).


##<a name="next-steps-media-services-learning-paths"></a>Les étapes suivantes : Media Services, voies de formation

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="looking-for-something-else"></a>Vous recherchez quelque chose d’autre ?

Si cette rubrique ne contenait pas ce que vous attendiez, il manque quelque chose, ou dans une autre façon n’a pas répondre à vos besoins, veuillez nous fournir vos commentaires à l’aide du thread de Disqus ci-dessous.



