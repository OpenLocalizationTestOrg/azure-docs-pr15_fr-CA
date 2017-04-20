<properties
    pageTitle="Journal de collecteur de données HTTP Analytique API | Microsoft Azure"
    description="Vous pouvez utiliser l’API de collecteur de données de journal Analytique HTTP pour ajouter des données POST JSON au référentiel Analytique de journal à partir de n’importe quel client qui peut appeler l’API REST. Cet article explique comment utiliser l’API et présente des exemples de la façon de publier des données à l’aide de différents langages de programmation."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="bwren"/>


# <a name="log-analytics-http-data-collector-api"></a>Collecteur de données HTTP Analytique journal API

Lorsque vous utilisez l’API du collecteur de données Azure journal Analytique HTTP, vous pouvez ajouter les données POST Notation JSON (JavaScript Object) dans le référentiel d’Analytique de journal à partir de n’importe quel client qui peut appeler l’API REST. À l’aide de cette méthode, vous pouvez envoyer des données à partir d’applications tierces ou à partir de scripts, comme à partir d’une procédure opérationnelle dans Azure Automation.  

## <a name="create-a-request"></a>Création d’une demande

Les deux tables suivantes répertorient les attributs qui sont requis pour chaque demande adressée à l’API de collecteur de données de journal Analytique HTTP. Nous décrire chaque attribut plus en détail plus loin dans l’article.

### <a name="request-uri"></a>URI de la demande

| Attribut | Propriété |
|:--|:--|
| Méthode | Publier |
| URI | https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Type de contenu | application/json. |

### <a name="request-uri-parameters"></a>Paramètres de l’URI de la demande
| Paramètre | Description |
|:--|:--|
| Code client  | Identificateur unique pour l’espace de travail Microsoft Operations Management Suite. |
| Ressources    | Le nom de ressource API : / api/journaux. |
| Version de l’API | La version de l’API à utiliser avec cette demande. Actuellement, il s’agit de 2016-04-01. |

### <a name="request-headers"></a>En-têtes de demande
| En-tête | Description |
|:--|:--|
| Autorisation | La signature de l’autorisation. Plus loin dans cet article, vous pouvez lire sur la création d’un en-tête de HMAC-SHA256. |
| Type de journal | Spécifiez le type d’enregistrement des données qui sont envoyées. Actuellement, le type de journal prend en charge uniquement les caractères alpha. Il ne gère pas les valeurs numériques ou caractères spéciaux. |
| x-ms-date | La date à laquelle la demande a été traitée, au format de la RFC 1123. |
| champ heure générée | Le nom d’un champ de données qui contient l’horodatage de l’élément de données. Si vous spécifiez un champ son contenu est utilisé pour **TimeGenerated**. Si ce champ n’est pas spécifié, la valeur par défaut de **TimeGenerated** est l’heure à laquelle le message est intégré. La valeur de ce champ de message doit suivre le format ISO 8601 aaaa-MM-JJThh. |


## <a name="authorization"></a>Autorisation

Toute demande de l’API de collecteur de données de journal Analytique HTTP doit inclure un en-tête d’autorisation. Pour authentifier une demande, vous devez signer la demande avec le serveur principal ou la clé secondaire pour l’espace de travail qui effectue la demande. Ensuite, passez cette signature dans le cadre de la demande.   

Voici le format de l’en-tête d’autorisation :

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* est l’identificateur unique pour l’espace de travail Suite de gestion des opérations. *La signature* est un [Hash-based Message Authentication Code (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) qui est construit à partir de la demande et puis calculée à l’aide de l' [algorithme SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Puis, vous le codez en utilisant le codage Base64.

Ce format permet de coder la chaîne de signature de **SharedKey** :

```
StringToSign = VERB + "\n" +
               Content-Length + "\n" +
               Content-Type + "\n" +
               x-ms-date + "\n" +
               "/api/logs";
```

Voici un exemple d’une chaîne de signature :

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Une fois que la chaîne de signature, les encoder à l’aide de l’algorithme HMAC-SHA256 sur la chaîne codée UTF-8 et ensuite coder le résultat au format Base64. Utilisez ce format :

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Les exemples fournis dans les sections suivantes ont des exemples de code pour vous aider à créer un en-tête d’autorisation.

## <a name="request-body"></a>Corps de la demande

Le corps du message doit être au format JSON. Elle doit inclure un ou plusieurs enregistrements, avec les paires nom / valeur de propriété dans ce format :

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Vous pouvez par lots plusieurs enregistrements dans une requête unique en utilisant le format suivant. Tous les enregistrements doivent être du même type d’enregistrement.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Propriétés et le type d’enregistrement

Vous définissez un type d’enregistrement personnalisé lorsque vous envoyez des données via l’API de collecteur de données de journal Analytique HTTP. Actuellement, vous ne peut pas écrire des données sur les types d’enregistrement existants qui ont été créés par d’autres types de données et les solutions. Analytique de journal lit les données entrantes et crée ensuite des propriétés qui correspondent aux types de données des valeurs que vous entrez.

Chaque demande adressée à l’API d’Analytique de journal doit inclure un en-tête de **Journal de Type** avec le nom du type d’enregistrement. Le suffixe **_CL** est automatiquement ajouté au nom que vous entrez pour le distinguer des autres types de journal comme un journal personnalisé. Par exemple, si vous entrez le nom de **MyNewRecordType**, journal Analytique crée un enregistrement avec le type de **MyNewRecordType_CL**. Cela permet de garantir qu’il n’y a pas de conflits entre noms de types créée par l’utilisateur et ceux fournis dans les solutions Microsoft actuelles ou futures.

Pour identifier un type de données, journal Analytique ajoute un suffixe au nom de propriété. Si une propriété contient une valeur null, la propriété n’est pas incluse dans l’enregistrement. Ce tableau répertorie le type de données de propriété et le suffixe correspondant :

| Type de données de propriété | Suffixe |
|:--|:--|
| Chaîne    | _S |
| Valeur booléenne   | _b |
| Double    | _D |
| Date/heure | _T |
| GUID      | _g |


Le type de données Analytique de journal utilise pour chaque propriété dépend si le type d’enregistrement pour le nouvel enregistrement existe déjà.

- Si le type d’enregistrement n’existe pas, Analytique de journal crée un nouveau. Journal Analytique utilise l’inférence de type JSON pour déterminer le type de données pour chaque propriété d’un nouvel enregistrement.
- Si le type d’enregistrement existe, journal Analytique tente de créer un nouvel enregistrement en fonction des propriétés existantes. Si le type de données pour une propriété dans le nouvel enregistrement ne correspond pas et ne peut pas être convertie dans le type existant, ou si l’enregistrement inclut une propriété qui n’existe pas, le journal Analytique crée une propriété qui a le suffixe approprié.

Par exemple, cette entrée de soumission doit créer un enregistrement avec trois propriétés, **number_d**, **boolean_b**et **string_s**:

![Exemple d’enregistrement 1](media/log-analytics-data-collector-api/record-01.png)

Si vous avez envoyé ensuite cette entrée suivante, avec toutes les valeurs mises en forme sous forme de chaînes, les propriétés ne changerait pas. Ces valeurs peuvent être converties pour les types de données existants :

![Exemple d’enregistrement 2](media/log-analytics-data-collector-api/record-02.png)

Toutefois, si vous avez ensuite cette soumission suivante, journal Analytique crée les nouvelles propriétés **boolean_d** et **string_d**. Ces valeurs ne peuvent pas être convertis :

![Exemple d’enregistrement 3](media/log-analytics-data-collector-api/record-03.png)

Si vous avez envoyé puis l’entrée suivante, avant que le type d’enregistrement a été créé, Analytique de journal crée un enregistrement avec trois propriétés **nombre_succès**, **boolean_s**et **string_s**. Dans cette entrée, les valeurs initiales sont mis en forme comme une chaîne :

![Exemple d’enregistrement 4](media/log-analytics-data-collector-api/record-04.png)


## <a name="data-limits"></a>Limites des données
Il existe certaines contraintes autour des données enregistrées sur la collecte des données d’Analytique de journal API.

- Le nombre maximal de 30 Mo par post à l’API du collecteur de données journal Analytique. Il s’agit d’une limite de taille pour une seule publication. Si les données à partir d’une seule validation qui dépasse 30 Mo, vous devez fractionner les données à des segments de plus petite tailles et les envoyer simultanément. 
- Maximum de la limite de 32 Ko pour les valeurs de champ. Si la valeur du champ est supérieure à 32 Ko, les données seront tronquées. 
- Nombre maximal recommandé de champs d’un type donné est de 50. Il s’agit d’une limite pratique d’une facilité d’utilisation et de la perspective d’expérience de recherche.  


## <a name="return-codes"></a>Codes de retour

Le code d’état HTTP 202 signifie que la demande a été acceptée pour traitement, mais le traitement n’est pas encore terminée. Cela indique que l’opération a réussi.

Ce tableau répertorie l’ensemble des codes d’état renvoyées par le service :

| Code | État | Code d’erreur | Description |
|:--|:--|:--|:--|
| 202 | Accepté |  | La demande a été acceptée. |
| 400 | Demande incorrecte | InactiveCustomer | L’espace de travail a été fermé. |
| 400 | Demande incorrecte | InvalidApiVersion | La version de l’API que vous avez spécifié n’a pas été reconnue par le service. |
| 400 | Demande incorrecte | InvalidCustomerId | L’ID de l’espace de travail spécifié n’est pas valide. |
| 400 | Demande incorrecte | InvalidDataFormat | JSON non valide a été envoyé. Le corps de la réponse peut contenir plus d’informations sur la résolution de l’erreur. |
| 400 | Demande incorrecte | InvalidLogType | Le type de journal spécifié contient des caractères spéciaux ou des valeurs numériques. |
| 400 | Demande incorrecte | MissingApiVersion | La version de l’API n’a pas été spécifiée. |
| 400 | Demande incorrecte | MissingContentType | Le type de contenu n’a pas été spécifié. |
| 400 | Demande incorrecte | MissingLogType | Le type de journal de valeur requise n’a pas été spécifié. |
| 400 | Demande incorrecte | UnsupportedContentType | Le type de contenu n’a pas été défini sur **application/json**. |
| 403 | Interdit | InvalidAuthorization | Le service n’a pas pu authentifier la demande. Vérifiez que la clé de connexion et ID d’espace de travail sont valides. |
| 500 | Erreur interne du serveur | UnspecifiedError | Le service a rencontré une erreur interne. Réessayez la demande. |
| 503 | Service non disponible | ServiceUnavailable | Le service est actuellement indisponible pour recevoir des demandes. Veuillez réessayer votre demande. |

## <a name="query-data"></a>Données de requête

Pour obtenir des données fournies par l’API de collecteur de données de journal Analytique HTTP, rechercher des enregistrements dont le **Type** est égal à la valeur **LogType** que vous avez spécifié, suivi d' **_CL**. Par exemple, si vous avez utilisé **MyCustomLog**, vous devrez retourner tous les enregistrements avec **Type = MyCustomLog_CL**.


## <a name="sample-requests"></a>Demandes d’exemples

Dans les sections suivantes, vous trouverez des exemples de la façon d’envoyer des données à l’API de collecteur de données de journal Analytique HTTP à l’aide de différents langages de programmation.

Pour chaque échantillon, procédez comme suit pour définir les variables de l’en-tête d’autorisation :

1. Dans le portail de la Suite de gestion des opérations, sélectionnez la fenêtre **paramètres** et puis sélectionnez l’onglet **Sources connectées** .
2. À droite de **l’ID de l’espace de travail**, sélectionnez l’icône de copie, puis collez le code en tant que la valeur de la variable de **Code client** .
3. À droite de la **Clé primaire**, sélectionnez l’icône de copie, puis collez le code en tant que la valeur de la variable de la **Clé partagée** .

Vous pouvez également modifier les variables pour le type de journal et les données JSON.

### <a name="powershell-sample"></a>Exemple de PowerShell

```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-OMSData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-OMSData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Exemple C#

```
using System;
using System.Net;
using System.Security.Cryptography;

namespace OIAPIExample
{
    class ApiExample
    {
// An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField1"":""DemoValue3"",""DemoField2"":""DemoValue4""}]";

// Update customerId to your Operations Management Suite workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

// For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

// LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

// You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
// Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            string stringToHash = "POST\n" + json.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

// Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

// Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            string url = "https://"+ customerId +".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
            using (var client = new WebClient())
            {
                client.Headers.Add(HttpRequestHeader.ContentType, "application/json");
                client.Headers.Add("Log-Type", LogName);
                client.Headers.Add("Authorization", signature);
                client.Headers.Add("x-ms-date", date);
                client.Headers.Add("time-generated-field", TimeStampField);
                client.UploadString(new Uri(url), "POST", json);
            }
        }
    }
}
```

### <a name="python-sample"></a>Exemple Python

```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Operations Management Suite workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code == 202):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Étapes suivantes

- [Concepteur de vues](log-analytics-view-designer.md) permet de créer des vues personnalisées des données que vous envoyez.
