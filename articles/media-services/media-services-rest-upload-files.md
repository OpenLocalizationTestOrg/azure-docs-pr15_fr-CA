<properties 
    pageTitle="Télécharger des fichiers dans un compte de Services de support à l’aide de reste | Microsoft Azure" 
    description="Découvrez comment obtenir du contenu multimédia dans les Services de support en créant et en chargeant les actifs." 
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
    ms.date="09/19/2016"
    ms.author="juliako"/>


# <a name="upload-files-into-a-media-services-account-using-rest"></a>Télécharger des fichiers dans un compte de Services de support à l’aide d’autres

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [RESTE](media-services-rest-upload-files.md)
 - [Portail](media-services-portal-upload-files.md)

Dans les Services de support, vous téléchargez vos fichiers numériques dans une immobilisation. L’entité [Asset](https://msdn.microsoft.com/library/azure/hh974277.aspx) peut contenir, vidéo, audio, images, collections de miniatures, texte pistes et sous-titres fichiers (et les métadonnées relatives à ces fichiers.)  Une fois que les fichiers sont téléchargés dans l’actif, votre contenu est stocké en toute sécurité dans le nuage pour la poursuite du traitement et la diffusion en continu. 

>[AZURE.NOTE]Les considérations suivantes s’appliquent lors du choix d’un nom de fichier de ressources :
>
>- Les Services de support utilise la valeur de la propriété IAssetFile.Name lors de la génération d’URL pour le contenu de transmission en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Pour cette raison, codage de pourcentage n’est pas autorisée. La valeur de la propriété **Name** ne peut comporter aucun des [caractères %-codage-réservés](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)suivants : !*'();:@&=+$,/?%#[]". En outre, il ne peut exister un '.' pour l’extension de nom de fichier.
>
>- La longueur du nom ne doit pas être supérieure à 260 caractères.

Le flux de travail pour le transfert des actifs est répartie dans les sections suivantes :

- Créer un actif
- Crypter un actif (facultatif)
- Télécharger un fichier dans le stockage blob

AMS vous permet également de télécharger des éléments en vrac. Pour plus d’informations, consultez [cette](media-services-rest-upload-files.md#upload_in_bulk) section.

##<a name="upload-assets"></a>Télécharger des ressources

###<a name="create-an-asset"></a>Créer un actif

>[AZURE.NOTE] Lorsque vous travaillez avec l’API REST de Services multimédias, les considérations suivantes s’appliquent :
>
>Lorsque vous accédez à des entités dans les Services de support, vous devez définir les valeurs et les champs d’en-tête spécifiques dans vos requêtes HTTP. Pour plus d’informations, consultez [le programme d’installation pour le développement d’API Media Services reste](media-services-rest-how-to-use.md).

>Après vous être connecté avec succès à la https://media.windows.net, vous recevrez un réacheminement 301 spécifiant un autre URI de Services multimédia. Vous devez faire d’autres appels à l’URI de nouveau comme décrit dans la [connexion à des Services de support à l’aide des API REST](media-services-rest-connect-programmatically.md). 
 
Un actif est un conteneur pour plusieurs types ou des ensembles d’objets dans les Services multimédias, y compris la vidéo, audio, images, collections de miniatures, suivis de texte et des fichiers de sous-titrage. Dans l’API REST, la création d’une ressource nécessite envoi requête POST pour les Services de support et de placer les informations de propriété sur vos ressources dans le corps de la demande.

L’une des propriétés que vous pouvez spécifier lors de la création d’une immobilisation est **Options**. **Options** est une valeur d’énumération qui décrit une immobilisation peut être créée avec les options de cryptage. Une valeur valide est une des valeurs de la liste ci-dessous, pas une combinaison de valeurs. 

- **Aucun** = **0**: aucun chiffrement ne sera utilisé. Il s’agit de la valeur par défaut. Notez que lors de l’utilisation de cette option votre contenu n’est pas protégé en transit ou au repos dans le stockage.
    Si vous souhaitez fournir un MP4 utilisant le téléchargement progressif, utilisez cette option. 

- **StorageEncrypted** = **1**: spécifiez si vous souhaitez pour vos fichiers à être chiffrés avec le cryptage AES-256 bits pour le téléchargement et le stockage.

    Si votre actif est le stockage chiffré, vous devez configurer la stratégie de livraison d’actif. Pour plus d’informations, consultez [stratégie de livraison de configuration actif](media-services-rest-configure-asset-delivery-policy.md).

- **CommonEncryptionProtected** = **2**: spécifiez si vous téléchargez des fichiers protégés par une méthode de cryptage commune (par exemple, PlayReady). 

- **EnvelopeEncryptionProtected** = **4**: spécifiez si vous téléchargez TLS cryptée avec les fichiers AES. Notez que les fichiers doivent ont été codés et chiffrés par Gestionnaire de transformation.

>[AZURE.NOTE]Si vos ressources utilisent le cryptage, vous devez créer un **ContentKey** et liez-le à votre actif, comme décrit dans la rubrique suivante :[comment créer un ContentKey](media-services-rest-create-contentkey.md). Notez qu’après avoir téléchargé les fichiers dans des immobilisations, vous devez mettre à jour les propriétés de cryptage sur l’entité **AssetFile** avec les valeurs que vous avez obtenu lors du cryptage de **l’immobilisation** . Le faire à l’aide de la requête HTTP **de fusion** . 


L’exemple suivant montre comment créer une immobilisation.

**Requête HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"BigBuckBunny.mp4"}
    

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
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
###<a name="create-an-assetfile"></a>Créer un AssetFile

L’entité [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) représente un fichier audio ou vidéo qui est stocké dans un conteneur d’objet blob. Un fichier de ressources est toujours associé à une immobilisation et une immobilisation peut contenir un ou plusieurs fichiers de ressources. La tâche de Media Services Encoder échoue si un objet de fichier de ressources n’est pas associé à un fichier numérique dans un conteneur d’objet blob.

Notez que l’instance de **AssetFile** et le fichier multimédia réelle sont deux objets distincts. L’instance de AssetFile contient des métadonnées sur le fichier multimédia, tandis que le fichier multimédia contient le contenu du média réel.

Après avoir téléchargé vos fichiers multimédias numériques dans un conteneur blob, vous utiliserez la requête HTTP **de fusion** pour mettre à jour la AssetFile avec les informations à propos de votre fichier multimédia (comme indiqué plus loin dans la rubrique). 

**Requête HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
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

Avant de télécharger des fichiers dans le stockage blob, définir l’accès à des droits de stratégie d’écriture d’une ressource. Pour ce faire, VALIDEZ une demande HTTP pour le jeu d’entités AccessPolicies. Définir une valeur de DurationInMinutes lors de la création ou vous recevrez un message d’erreur de serveur interne 500 en réponse. Pour plus d’informations sur AccessPolicies, reportez-vous à la section [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

L’exemple suivant montre comment créer un AccessPolicy :
        
**Requête HTTP**

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Requête HTTP**

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

###<a name="get-the-upload-url"></a>Obtenir l’URL de téléchargement

Pour obtenir l’URL de téléchargement réel, créer un localisateur de SAS. Localisateurs de définissent l’heure de début et le type de point de terminaison de connexion pour les clients qui souhaitent accéder à des fichiers d’un actif. Vous pouvez créer plusieurs entités de localisateur pour deux AccessPolicy et actifs gérer les différentes demandes des clients et des besoins. Chacun de ces repères utilisent la valeur de l’heure de début ainsi que la valeur de DurationInMinutes de la AccessPolicy pour déterminer la durée pendant laquelle une URL peut être utilisée. Pour plus d’informations, consultez le [localisateur](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Une URL de SAS a le format suivant :

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Certaines considérations s’appliquent :

- Vous ne pouvez pas avoir plus de cinq localisateurs uniques associés à un actif à un moment donné. Pour plus d’informations, consultez le localisateur.
- Si vous avez besoin télécharger vos fichiers immédiatement, vous devez définir votre valeur StartTime à cinq minutes avant l’heure actuelle. C’est parce qu’il peut y avoir horloge inclinaison entre votre ordinateur client et les Services de support. Également, votre valeur StartTime doit être dans le format DateTime suivant : AAAA-MM-JJThh (par exemple, « 2014-05-23T17:53:50Z »).   
- Il peut y avoir de 30 à 40 secondes délai après la création d’un localisateur pour lorsqu’il est disponible pour utilisation. Ce problème s’applique aux URL des associations de sécurité et de repères d’origine.

L’exemple suivant montre comment créer un localisateur URL SAS, tel que défini par la propriété de Type dans le corps de la demande (« 1 » pour un localisateur SAS) et « 2 » pour un localisateur d’origine de la demande. La propriété de **chemin d’accès** retournée contient l’URL qui vous permet de charger votre fichier.
    
**Requête HTTP**
    
    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
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
    
    MERGE https://media.windows.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Réponse HTTP**

Si réussie, le suivant est retourné : HTTP/1.1 204 sans contenu

### <a name="delete-the-locator-and-accesspolicy"></a>Supprimer le repère et AccessPolicy 

**Requête HTTP**


    DELETE https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net

    
**Réponse HTTP**

Si l’opération réussit, le suivant est renvoyé :

    HTTP/1.1 204 No Content 
    ...

**Requête HTTP**

    DELETE https://media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net

**Réponse HTTP**

Si l’opération réussit, le suivant est renvoyé :

    HTTP/1.1 204 No Content 
    ...

##<a id="upload_in_bulk"></a>Télécharger les éléments en vrac

###<a name="create-the-ingestmanifest"></a>Créer la IngestManifest

Le IngestManifest est un conteneur pour un ensemble de ressources, des fichiers et les informations statistiques qui peuvent être utilisées pour déterminer la progression de réception pour l’ensemble des blocs de.


**Requête HTTP**

    POST https:// media.windows.net/API/IngestManifests HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 36
    Expect: 100-continue
    
    { "Name" : "ExampleManifestREST" }

###<a name="create-assets"></a>Créer des immobilisations

Avant de créer la IngestManifestAsset, vous devez créer l’immobilisation sera terminée à l’aide de bulk absorber. Un actif est un conteneur pour plusieurs types ou des ensembles d’objets dans les Services multimédias, y compris la vidéo, audio, images, collections de miniatures, suivis de texte et des fichiers de sous-titrage. Dans l’API REST, la création d’un actif nécessite l’envoi d’une demande HTTP POST à Microsoft Azure Media Services et placer les informations de propriété sur vos ressources dans le corps de la demande. Dans cet exemple, l’immobilisation est créée à l’aide de l’option StorageEncrption(1) incluse dans le corps de la demande.

**Réponse HTTP**

    POST https://media.windows.net/API/Assets HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 55
    Expect: 100-continue
    
    { "Name" : "ExampleManifestREST_Asset", "Options" : 1 }

###<a name="create-the-ingestmanifestassets"></a>Créer la IngestManifestAssets

IngestManifestAssets représentent les éléments dans un IngestManifest qui sont utilisés avec l’ingestion de bloc. Le fondamentalement lier l’immobilisation au manifeste. Azure Media Services assure le suivi en interne pour le téléchargement de fichiers basé sur la collection IngestManifestFiles associée à l’IngestManifestAsset. Une fois que ces fichiers sont téléchargés, l’immobilisation est terminée. Vous pouvez créer un nouveau IngestManifestAsset avec une demande HTTP POST. Dans le corps de la demande, inclure l’Id de l’IngestManifest et le code d’actif que le IngestManifestAsset doit relier en bloc cassettes.

**Réponse HTTP**

    POST https://media.windows.net/API/IngestManifestAssets HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 152
    Expect: 100-continue
    { "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "Asset" : { "Id" : "nb:cid:UUID:b757929a-5a57-430b-b33e-c05c6cbef02e"}}


###<a name="create-the-ingestmanifestfiles-for-each-asset"></a>Créer la IngestManifestFiles pour chaque immobilisation

Un IngestManifestFile représente un objet blob réel de vidéo ou audio qui est téléchargé dans le cadre d’absorber de bloc pour un actif. Cryptage associées de propriétés ne sont pas obligatoires sauf si l’actif est à l’aide d’une option de cryptage. L’exemple utilisé dans cette section illustre la création d’un IngestManifestFile utilisant des StorageEncryption de l’immobilisation précédemment créée.


**Réponse HTTP**

    POST https://media.windows.net/API/IngestManifestFiles HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 367
    Expect: 100-continue
    
    { "Name" : "REST_Example_File.wmv", "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "ParentIngestManifestAssetId" : "nb:maid:UUID:beed8531-9a03-9043-b1d8-6a6d1044cdda", "IsEncrypted" : "true", "EncryptionScheme" : "StorageEncryption", "EncryptionVersion" : "1.0", "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510" }
    
###<a name="upload-the-files-to-blob-storage"></a>Téléchargez les fichiers dans le stockage Blob

Vous pouvez utiliser n’importe quelle application de client à grande vitesse capable de télécharger les fichiers de composants dans le conteneur de stockage blob Uri fourni par la propriété BlobStorageUriForUpload de l’IngestManifest. Un service de téléchargement de notable à grande vitesse est [Aspera à la demande pour une Application Azure](http://go.microsoft.com/fwlink/?LinkId=272001).

###<a name="monitor-bulk-ingest-progress"></a>Cours d’acquisition en bloc du moniteur

Vous pouvez surveiller la progression de bloc absorber des opérations pour un IngestManifest en interrogeant la propriété statistiques de la IngestManifest. Cette propriété est un type complexe, [IngestManifestStatistics](https://msdn.microsoft.com/library/azure/jj853027.aspx). Pour interroger la propriété statistiques, soumettre une demande HTTP GET, en passant le IngestManifest Id.
 

##<a name="create-contentkeys-used-for-encryption"></a>Créer ContentKeys utilisée pour le chiffrement

Si vos ressources utilisent le cryptage, vous devez créer le ContentKey à utiliser pour le chiffrement avant de créer les fichiers de composants. Pour le cryptage de stockage, les propriétés suivantes doivent figurer dans le corps de la demande.
 
Propriété de corps de requête   | Description
---|---
ID | L’Id de ContentKey nous-mêmes, nous générer au format suivant, « nb:kid:UUID :<NEW GUID>».
ContentKeyType | C’est le type de clé de contenu comme un entier de cette clé de contenu. Nous transmettons la valeur 1 pour le cryptage du stockage.
EncryptedContentKey | Nous créons une nouvelle valeur de clé contenue qui est une valeur de 256 bits (32 octets). La clé est cryptée à l’aide du certificat X.509 de cryptage de stockage qui nous récupérer auprès des Services de support Microsoft Azure par l’exécution d’une requête HTTP GET pour les méthodes de GetProtectionKey et le GetProtectionKeyId.
ProtectionKeyId | Il s’agit de l’identificateur de clé de protection pour le certificat X.509 de cryptage stockage qui a été utilisé pour chiffrer la clé de notre contenue.
ProtectionKeyType | C’est le type de cryptage de la clé de protection qui a été utilisé pour chiffrer la clé de contenu. Cette valeur est StorageEncryption(1) pour notre exemple.
Somme de contrôle |Le checksum calculé MD5 pour la clé de contenu. Elle est calculée par le chiffrement de l’Id de contenu avec la clé de contenu. L’exemple de code montre comment calculer la somme de contrôle.


**Réponse HTTP**
    
    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 572
    Expect: 100-continue
    
    {"Id" : "nb:kid:UUID:316d14d4-b603-4d90-b8db-0fede8aa48f8", "ContentKeyType" : 1, "EncryptedContentKey" : "Y4NPej7heOFa2vsd8ZEOcjjpu/qOq3RJ6GRfxa8CCwtAM83d6J2mKOeQFUmMyVXUSsBCCOdufmieTKi+hOUtNAbyNM4lY4AXI537b9GaY8oSeje0NGU8+QCOuf7jGdRac5B9uIk7WwD76RAJnqyep6U/OdvQV4RLvvZ9w7nO4bY8RHaUaLxC2u4aIRRaZtLu5rm8GKBPy87OzQVXNgnLM01I8s3Z4wJ3i7jXqkknDy4VkIyLBSQvIvUzxYHeNdMVWDmS+jPN9ScVmolUwGzH1A23td8UWFHOjTjXHLjNm5Yq+7MIOoaxeMlKPYXRFKofRY8Qh5o5tqvycSAJ9KUqfg==", "ProtectionKeyId" : "7D9BB04D9D0A4A24800CADBFEF232689E048F69C", "ProtectionKeyType" : 1, "Checksum" : "TfXtjCIlq1Y=" }

### <a name="link-the-contentkey-to-the-asset"></a>Lier la ContentKey à l’actif

Le ContentKey est associé à un ou plusieurs actifs en envoyant une demande HTTP POST. La requête suivante est un exemple de lier l’exemple ContentKey à l’actif de l’exemple par code.

**Réponse HTTP**
    
    POST https://media.windows.net/API/Assets('nb:cid:UUID:b3023475-09b4-4647-9d6d-6fc242822e68')/$links/ContentKeys HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 113
    Expect: 100-continue
    
    { "uri": "https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A32e6efaf-5fba-4538-b115-9d1cefe43510')"}

**Réponse HTTP**

    GET https://media.windows.net/API/IngestManifests('nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net



##<a name="next-step"></a>Étape suivante

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



 
[How to Get a Media Processor]: media-services-get-media-processor.md
 
