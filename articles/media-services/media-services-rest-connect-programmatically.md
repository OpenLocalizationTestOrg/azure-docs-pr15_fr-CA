<properties 
    pageTitle="Connexion à un compte de Services de support à l’aide des API REST | Microsoft Azure" 
    description="Cette rubrique montre comment se connecter à des Services de support en utilisant API REST." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-rest-api"></a>Connexion à un compte de Services de support à l’aide des API REST de Services multimédia

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect-programmatically.md)
- [RESTE](media-services-rest-connect-programmatically.md)

Cette rubrique décrit la façon d’obtenir une connexion par programme pour Microsoft Azure Media Services lors de la programmation avec l’API REST de Services multimédia.

Deux choses sont requis lors de l’accès aux Services de support Microsoft Azure : un jeton d’accès fourni par Azure Access Control Services (ACS) et l’URI de Services média lui-même. Vous pouvez utiliser tout moyen souhaité lors de la création de ces requêtes tant que vous spécifiez les valeurs d’en-tête correct et passez le jeton d’accès correctement lors de l’appel dans les Services de support.

Les étapes suivantes décrivent le flux de travail plus courante lors de l’utilisation de l’API REST de Services média pour vous connecter à des Services multimédias :

1. L’obtention d’un jeton d’accès 
2. Connexion aux Services de support URI 

    >[AZURE.NOTE] Après vous être connecté avec succès à la https://media.windows.net, vous recevrez un réacheminement 301 spécifiant un autre URI de Services multimédia. Vous devez faire d’autres appels à l’URI de nouveau.
Vous pouvez également recevoir une réponse HTTP/1.1 200 qui contient la description des métadonnées ODATA API.

3. Validez vos appels d’API suivants vers la nouvelle URL. 

    Par exemple, si, après avoir essayé de vous connecter, vous avez les éléments suivants :

        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Vous devez enregistrer vos appels d’API suivants à https://wamsbayclus001rest-hs.cloudapp.net/api/.

##<a name="access-control-address"></a>Adresse de contrôle d’accès

Adresse de contrôle d’accès au Media Services est https://wamsprodglobal001acs.accesscontrol.windows.net, à l’exception de la région en Chine du Nord, où il est https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn.

##<a name="getting-an-access-token"></a>L’obtention d’un jeton d’accès

Pour accéder aux Services de support directement par le biais de l’API REST, ils récupèrent un jeton d’accès ACS et l’utiliser lors de chaque demande HTTP effectuée dans le service. Ce jeton est similaire aux autres jetons fournies par l’ACS basée sur les revendications d’accès fournies dans l’en-tête d’une demande HTTP et utilisant le protocole de v2 OAuth. Vous n’avez pas besoin de toutes les autres conditions préalables avant de se connecter directement à des Services multimédias.

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

Vous devez prouver les valeurs identifiant_client et client_secret dans le corps de cette demande ; identifiant_client et client_secret correspondent aux valeurs NomCompte et AccountKey, respectivement. Ces valeurs sont fournies par les Services de support pour vous lorsque vous configurez votre compte. 

Notez que la AccountKey de votre compte de Services de support doit être codé en URL (voir [Codage de pour cent](http://tools.ietf.org/html/rfc3986#section-2.1) lorsque vous l’utilisez comme valeur de client_secret dans votre demande de jeton d’accès.

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
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
Il est recommandé de mettre en cache les valeurs « access_token » et « expires_in » à un système de stockage externe. Les données de jetons peuvent ultérieurement être récupérées à partir du stockage et réutilisées dans vos appels de l’API REST de Services média. Ceci est particulièrement utile pour les scénarios où le jeton peut être partagé en toute sécurité entre plusieurs processus ou des ordinateurs.

Veillez à contrôler la valeur de « expires_in » du jeton d’accès et les appels de l’API REST de mettre à jour avec nouveaux jetons en fonction des besoins.

###<a name="connecting-to-the-media-services-uri"></a>Connexion aux Services de support URI

La racine URI de Media Services est https://media.windows.net/. Vous devez vous connecter initialement à cet URI, et si vous obtenez un réacheminement 301 dans la réponse, vous devez les appels ultérieurs à l’URI de nouveau. En outre, n’utilisez pas de logique de redirection automatique/suivre dans vos requêtes. Verbes HTTP et le corps de la demande ne seront pas transférés au nouvel URI.

Notez que l’URI pour le téléchargement des fichiers de ressource racine est https://yourstorageaccount.blob.core.windows.net/, où le nom de compte de stockage est le même que celui utilisé lors de la configuration de votre compte de Services de support.

L’exemple suivant montre la demande HTTP à l’URI (https://media.windows.net/) de la racine des Services de support. La demande Obtient un réacheminement 301 dans la réponse. La requête suivante utilise le nouvel URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Requête HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
     


>[AZURE.NOTE] Une fois que vous obtenez du nouvel URI, qui est l’URI qui doit être utilisé pour communiquer avec les Services de support. 


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
