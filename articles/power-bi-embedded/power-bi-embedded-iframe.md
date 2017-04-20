<properties
   pageTitle="Comment utiliser incorporées de BI alimentation reste | Microsoft Azure"
   description="Découvrez comment utiliser alimentation BI incorporé avec le reste "
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="how-to-use-power-bi-embedded-with-rest"></a>L’utilisation de puissance BI incorporé avec le reste


## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Alimentation incorporées de BI : Ce qu’il est et qu’elle est pour
Une vue d’ensemble de puissance BI incorporé est décrite dans le officiel de [site incorporées de BI d’alimentation](https://azure.microsoft.com/services/power-bi-embedded/), mais voyons rapide avant d’entrer dans les détails relatifs à son utilisation avec les autres.

Il est très simple, vraiment. Un ISV souhaite souvent utiliser les visualisations de données dynamique de [Puissance BI](https://powerbi.microsoft.com) dans leur propre application en tant que blocs de construction de l’interface utilisateur.

Mais, vous savez, l’incorporation des rapports BI de puissance ou les morceaux dans votre page web est déjà possible sans le service Azure incorporées de BI d’alimentation, à l’aide de l' **API de BI d’alimentation**. Lorsque vous souhaitez partager vos rapports de votre organisation même, vous pouvez incorporer les rapports avec l’authentification Active Directory Azure. L’utilisateur qui consulte le rapports doit à l’aide de leur propre compte Azure AD. Lorsque vous souhaitez partager vos rapports pour tous les utilisateurs (y compris les utilisateurs externes), vous pouvez simplement incorporer avec l’accès anonyme.

Mais vous voyez, d’incorporer cette simple solution ne répond pas aux besoins d’une application d’ISV.
La plupart des applications ISV ont besoin pour fournir les données de leurs propres clients, pas nécessairement les utilisateurs de leur propre organisation. Par exemple, si vous êtes offrant un service pour la société A et société B, les utilisateurs dans la société A doivent uniquement afficher données pour leur propre société A. Autrement dit, l’architecture mutualisée est nécessaire pour la livraison.

L’application ISV peut également être offre ses propres méthodes d’authentification telles que l’authentification par formulaires, authentification de base, etc... Ensuite, la solution de l’incorporation doit collaborer en toute sécurité avec ces méthodes d’authentification existantes. Il est également nécessaire pour les utilisateurs d’être en mesure d’utiliser ces applications ISV sans l’achat supplémentaire ou de licence d’un abonnement de puissance BI.

 **Alimentation BI incorporé** est conçu pour précisément ces types de scénarios d’ISV. Donc maintenant que nous avons cette introduction rapide de, nous allons apprendre à certains détails

Vous pouvez utiliser .NET \(C#) ou SDK Node.js, permet de générer facilement votre application avec alimentation BI incorporé. Mais, dans cet article, nous expliquerons sur les flux HTTP \(incluse seul) de BI de puissance sans les kits de développement logiciel. Comprendre ce flux, vous pouvez créer votre application **avec n’importe quel langage de programmation**, et vous pouvez comprendre profondément l’essence de puissance BI incorporé.

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>Collection d’espace de travail de créer la BI de puissance et la clé d’accès get \(Provisioning)
Alimentation BI incorporé est un des services Azure. Seulement l’éditeur de logiciels qui utilise Azure Portal est facturé pour les frais d’utilisation de \(par session d’utilisateur toutes les heures), et l’utilisateur qui consulte le rapport n’est pas facturé ou même exiger un abonnement Azure.
Avant de commencer le développement de notre application, nous devons créer la **collection d’espace de travail de puissance BI** à l’aide du portail Azure.

Chaque espace de travail de puissance BI Embedded est l’espace de travail de chaque client (clients), et nous pouvons ajouter de nombreux espaces de travail dans chaque collection de l’espace de travail. La même touche d’accès rapide est utilisée dans chaque collection de l’espace de travail. En effet, la collection de l’espace de travail est la limite de sécurité pour alimentation BI incorporé.

![](media\power-bi-embedded-iframe\create-workspace.png)

Lorsque nous avons terminé de créer la collection de l’espace de travail, copiez la clé d’accès portail Azure.

![](media\power-bi-embedded-iframe\copy-access-key.png)

> [AZURE.NOTE] Nous pouvons également configurer la collection de l’espace de travail et obtenir la touche d’accès rapide via l’API REST. Pour plus d’informations, voir [API de fournisseur de ressource d’alimentation BI](https://msdn.microsoft.com/library/azure/mt712306.aspx).

## <a name="create-pbix-file-with-power-bi-desktop"></a>Créer un fichier de .pbix avec alimentation BI bureau
Ensuite, nous devons créer la connexion de données et les rapports à incorporer.
Pour cette tâche, il n’existe aucune programmation ou code. Nous utilisons simplement d’alimentation BI bureau.
Dans cet article, nous n’allons pas étudier les détails sur l’utilisation d’énergie BI bureau. Si vous avez besoin d’aide ici, reportez-vous à la section [mise en route avec le bureau de BI d’alimentation](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). Dans notre exemple, nous utiliserons uniquement l' [Exemple d’analyse de vente au détail](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\power-bi-embedded-iframe\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Créer un espace de travail de puissance BI

Maintenant que la mise en service se fait, allons-y création d’espace de travail d’un client dans la collection de l’espace de travail via les API du reste. Le suivant HTTP POST demande (reste) consiste à créer le nouvel espace de travail de notre collection d’espace de travail existant. Dans notre exemple, le nom de collection d’espace de travail est **mypbiapp**.
Nous venons de définir la touche d’accès rapide, qui nous copiés précédemment, comme **AppKey**. Il est très simple authentification !

**Requête HTTP**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**Réponse HTTP**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

Le retour de **workspaceId** est utilisé pour les appels d’API suivantes suivantes. Notre application doit conserver cette valeur.

## <a name="import-pbix-file-into-the-workspace"></a>Importer le fichier .pbix dans l’espace de travail
Chaque espace de travail peut héberger un seul fichier de bureau BI de puissance avec un groupe de données \(y compris les paramètres de source de données) et les rapports. Nous pouvons importer notre fichier .pbix à l’espace de travail comme indiqué dans le code ci-dessous. Comme vous pouvez le voir, nous pouvons télécharger le fichier binaire du fichier .pbix à l’aide de MIME multipart dans http.

Le de fragment uri **32960a09-6366-4208-a8bb-9e0678cdbb9d** est la workspaceId et de paramètre de requête **datasetDisplayName** est le nom du groupe de données à créer. Le groupe de données créé conserve toutes les données liées artefacts dans .pbix de fichiers telles que les données importées, le pointeur vers la source de données, etc...

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

Cette tâche d’importation peut s’exécuter pendant un certain temps. Lorsque vous avez terminé, notre application peut demander à l’état de la tâche à l’aide des id d’importation. Dans notre exemple, le code d’importation est **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

Demandant état à l’aide de ce code d’importation suivantes :

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Si la tâche n’est pas terminée, la réponse HTTP peut être comme suit :

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Si la tâche est terminée, la réponse HTTP peut être plus comme suit :

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>Connexion de source de données \(et l’architecture mutualisée de données)
Bien que presque tous les artefacts de .pbix fichier sont importés dans notre espace de travail, les informations d’identification pour les sources de données ne sont pas. Par conséquent, lorsque vous utilisez le **mode de DirectQuery**, le rapport incorporé ne peut pas s’afficher correctement. Toutefois, lorsque vous utilisez le **mode d’importation**, nous pouvons afficher le rapport en utilisant les données importées. Dans ce cas, nous devons définir les informations d’identification en suivant ces étapes via des appels de reste.

Tout d’abord, nous devons obtenir la source de données de passerelle. Nous savons que **l’id** du groupe de données est l’id retourné précédemment.

**Requête HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**Réponse HTTP**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

À l’aide de l’id de passerelle retourné et l’id de source de données \(consultez le précédent **gatewayId** et l' **id** du résultat renvoyé), nous pouvons modifier les informations d’identification de cette source de données comme suit :

**Requête HTTP**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**Réponse HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

Dans la production, nous pouvons également définir la chaîne de connexion différente pour chaque espace de travail à l’aide des API REST. \(Autrement dit, nous pouvons séparer la base de données pour chaque client.)

Les éléments suivants sont la modification de la chaîne de connexion de source de données via le reste.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

Ou, nous pouvons utiliser la sécurité au niveau ligne de puissance BI Embedded et nous pouvons séparer les données de tous les utilisateurs dans un rapport. As a result, nous pouvons attribuer chaque rapport client avec la même .pbix \(l’interface utilisateur, etc..) sources de données différentes.

> [AZURE.NOTE] Si vous utilisez le **mode d’importation** et non **en mode DirectQuery**, il n’existe aucun moyen pour actualiser les modèles via l’API. Et bien, sources de données sur site via la passerelle de puissance BI n’est pas encore prise en charge de puissance BI Embedded. Toutefois, vous voudrez vraiment garder un œil sur le [blog de BI de puissance](https://powerbi.microsoft.com/blog/) pour ce qui est nouveau et nouveautés dans les futures mises à jour.

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>L’authentification et des rapports (incorporation) dans notre page web d’hébergement

Dans l’API REST précédente, nous pouvons utiliser la touche d’accès rapide **AppKey** lui-même en tant que l’en-tête d’autorisation. Étant donné que ces appels peuvent être gérés sur le serveur principal, il est sûr.

Mais, lorsque nous incorporer le rapport dans notre page web, ce type d’informations de sécurité serait géré à l’aide de JavaScript \(frontal). Puis la valeur de l’en-tête d’autorisation doit être sécurisée. Si notre touche d’accès rapide est découverte par un utilisateur malveillant ou du code malveillant, elles peuvent appeler des opérations à l’aide de cette clé.

Lorsque nous incorporer le rapport dans notre page web, nous devons utiliser le jeton calculé au lieu de la touche d’accès rapide **AppKey**. Notre application doit créer le jeton Web de Json OAuth \(JWT) qui se compose de créances et de la signature numérique calculée. Comme illustré ci-dessous, ce JWT OAuth est de jetons de chaîne codée délimitée par des points.

![](media\power-bi-embedded-iframe\oauth-jwt.png)

Tout d’abord, nous devons préparer la valeur d’entrée, qui est signée ultérieurement. Cette valeur est la chaîne d’url codée (rfc4648) base64 de la json suivant, et elles sont délimitées par le point \(.) caractère. Par la suite, nous vous expliquerons comment obtenir le code de rapport.

> [AZURE.NOTE] Si nous souhaitons utiliser la sécurité de niveau ligne (RLS) avec alimentation BI incorporé, nous devons également spécifier **nom d’utilisateur** et des **rôles** dans les déclarations.

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Ensuite, nous devons créer la chaîne codée en base64 de HMAC \(la signature) avec l’algorithme SHA256. Cette valeur d’entrée signée est la chaîne précédente.

Enfin, nous devons combiner la chaîne de valeur et de la signature d’entrée à l’aide de la période de \(.) caractère. La chaîne complète est le jeton de l’application pour l’incorporation de l’état. Même si le jeton app est découverte par un utilisateur malveillant, ils ne peuvent pas obtenir la clé d’accès d’origine. Ce jeton d’application va expirer rapidement.

Voici un exemple PHP pour ces étapes :

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>Enfin, intégrez le rapport dans la page web

Pour l’incorporation de notre rapport, nous devons obtenir l’url de l’incorporer et **id** à l’aide de l’API REST suivant de l’état.

**Requête HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**Réponse HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

Nous pouvons intégrer le rapport dans notre application web en utilisant le jeton d’application précédente.
Si nous examinons l’exemple de code suivant, la première partie est la même que dans l’exemple précédent. Dans la dernière partie, cet exemple montre **embedUrl** \(voir le résultat précédent) dans l’iframe et comptabilise le jeton app dans l’iframe.

> [AZURE.NOTE] Vous devez modifier la valeur d’id état à celle de votre choix. En outre, en raison d’un bogue dans notre système de gestion de contenu, la balise iframe dans l’exemple de code est lu littéralement. Supprimer le texte limité à partir de la balise, si vous copiez et collez le code suivant.

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

Et Voici notre résultat :

![](media\power-bi-embedded-iframe\view-report.png)

À ce stade, incorporées de BI d’alimentation affiche uniquement le rapport dans l’iframe. Mais, gardez un œil sur le [Blog de BI d’alimentation](). Améliorations futures pourraient utiliser le côté client de nouvelles API qui va nous envoyer des informations dans l’iframe ainsi que fournir des informations. Choses intéressantes !


## <a name="see-also"></a>Voir aussi
- [Authentification et autorisation dans incorporées de BI d’alimentation](power-bi-embedded-app-token-flow.md)
