<properties
 pageTitle="Authentification sortante du planificateur"
 description="Authentification sortante du planificateur"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/15/2016"
 ms.author="deli"/>

# <a name="scheduler-outbound-authentication"></a>Authentification sortante du planificateur

Travaux du planificateur peut-être faire appel à des services qui requièrent une authentification. De cette manière, un service appelé peut déterminer si la tâche du planificateur peut accéder à ses ressources. Certains de ces services incluent d’autres services Azure, Salesforce.com, Facebook et des sites Web personnalisés sécurisés.

## <a name="adding-and-removing-authentication"></a>Ajout et suppression de l’authentification

Ajout d’une tâche du Planificateur de l’authentification est simple : ajouter un élément enfant JSON `authentication` à la `request` élément lors de la création ou la mise à jour d’une tâche. Secrets passé au service du planificateur dans une demande POST, PUT ou correctif – dans le cadre de la `authentication` objet – ne sont jamais renvoyées dans les réponses. Dans les réponses, les informations confidentielles sont définies sur une valeur null ou est peut-être un jeton public qui représente l’entité authentifiée.

Pour supprimer l’authentification, placer ou le travail des correctifs explicitement, la définition du `authentication` objet à null. Vous ne verrez pas toutes les propriétés d’authentification en réponse.

Actuellement, les types de prises en charge uniquement l’authentification sont les `ClientCertificate` modèle (pour à l’aide de certificats clients SSL/TLS), la `Basic` modèle (pour l’authentification de base) et le `ActiveDirectoryOAuth` modèle (pour l’authentification Active Directory OAuth.)

## <a name="request-body-for-clientcertificate-authentication"></a>Corps de requête pour l’authentification de ClientCertificate

Lors de l’ajout de l’authentification à l’aide de la `ClientCertificate` du modèle, spécifiez les éléments supplémentaires suivants dans le corps de la demande.  

|Élément|Description|
|:---|:---|
|_authentification (élément parent)_|Objet d’authentification à l’aide d’un certificat client SSL.|
|_type de_|Obligatoire. Type d’authentification. Pour les certificats de client SSL, la valeur doit être `ClientCertificate`.|
|_PFX_|Obligatoire. Codé en base 64 de contenu d’un fichier PFX.|
|_mot de passe_|Obligatoire. Mot de passe pour accéder au fichier PFX.|


## <a name="response-body-for-clientcertificate-authentication"></a>Corps de la réponse pour l’authentification de ClientCertificate

Lorsqu’une demande est envoyée avec les informations d’authentification, la réponse contient les éléments suivants liés à l’authentification.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d’authentification à l’aide d’un certificat client SSL.|
|_type de_ |Type d’authentification. Pour les certificats de client SSL, la valeur est `ClientCertificate`.|
|_certificateThumbprint_ |L’empreinte numérique du certificat.|
|_certificateSubjectName_ |Le nom unique du sujet du certificat.|
|_certificateExpiration_ |La date d’expiration du certificat.|

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Exemple de requête reste pour l’authentification de ClientCertificate

```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Exemple de réponse reste pour l’authentification de ClientCertificate

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>Corps de la demande pour l’authentification de base

Lors de l’ajout de l’authentification à l’aide de la `Basic` du modèle, spécifiez les éléments supplémentaires suivants dans le corps de la demande.

|Élément|Description|
|:--|:--|
|_authentification (élément parent)_ |Objet d’authentification pour l’authentification de base.|
|_type de_ |Obligatoire. Type d’authentification. L’authentification de base, la valeur doit être `Basic`.|
|_nom d’utilisateur_ |Obligatoire. Nom d’utilisateur à authentifier.|
|_mot de passe_ |Obligatoire. Mot de passe pour l’authentification.|

## <a name="response-body-for-basic-authentication"></a>Corps de la réponse pour l’authentification de base

Lorsqu’une demande est envoyée avec les informations d’authentification, la réponse contient les éléments suivants liés à l’authentification.

|Élément|Description|
|:--|:--|
|_authentification (élément parent)_ |Objet d’authentification pour l’authentification de base.|
|_type de_ |Type d’authentification. L’authentification de base, la valeur est `Basic`.|
|_nom d’utilisateur_ |Le nom d’utilisateur authentifié.|

## <a name="sample-rest-request-for-basic-authentication"></a>Exemple de requête reste pour l’authentification de base

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Exemple de réponse reste pour l’authentification de base

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Corps de requête pour l’authentification de ActiveDirectoryOAuth

Lors de l’ajout de l’authentification à l’aide de la `ActiveDirectoryOAuth` du modèle, spécifiez les éléments supplémentaires suivants dans le corps de la demande.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d’authentification pour l’authentification de ActiveDirectoryOAuth.|
|_type de_ |Obligatoire. Type d’authentification. Pour l’authentification de ActiveDirectoryOAuth, la valeur doit être `ActiveDirectoryOAuth`.|
|_clients_ |Obligatoire. L’identificateur de client pour les clients AD Azure.|
|_public_ |Obligatoire. Cette option est définie à https://management.core.windows.net/.|
|_clientId_ |Obligatoire. Fournir l’identificateur du client pour l’application Azure AD.|
|_secret_ |Obligatoire. Secret du client qui demande le jeton.|

### <a name="determining-your-tenant-identifier"></a>Détermination de votre identificateur de client

Vous pouvez trouver l’identificateur de client pour les clients AD Azure en exécutant `Get-AzureAccount` dans PowerShell d’Azure.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Corps de la réponse pour l’authentification de ActiveDirectoryOAuth

Lorsqu’une demande est envoyée avec les informations d’authentification, la réponse contient les éléments suivants liés à l’authentification.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d’authentification pour l’authentification de ActiveDirectoryOAuth.|
|_type de_ |Type d’authentification. Pour l’authentification de ActiveDirectoryOAuth, la valeur est `ActiveDirectoryOAuth`.|
|_clients_ |L’identificateur de client pour les clients AD Azure. |
|_public_ |Cette option est définie à https://management.core.windows.net/.|
|_clientId_ |L’identificateur client d’application Azure AD.|

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>Exemple de requête reste pour l’authentification de ActiveDirectoryOAuth

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>Exemple de réponse reste pour l’authentification de ActiveDirectoryOAuth

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Voir aussi


 [Quel est le planificateur ?](scheduler-intro.md)

 [Hiérarchie d’entités, terminologie et des concepts du planificateur Azure](scheduler-concepts-terms.md)

 [Mise en route à l’aide du planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Référence des API de reste de planificateur Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence d’applets de commande PowerShell de planificateur Azure](scheduler-powershell-reference.md)

 [Azure planificateur haute disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites de planificateur Azure, les valeurs par défaut et les codes d’erreur](scheduler-limits-defaults-errors.md)
