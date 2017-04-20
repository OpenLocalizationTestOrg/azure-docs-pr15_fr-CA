<properties
    pageTitle="Enregistrement et gestion des erreurs dans la logique d’applications | Microsoft Azure"
    description="Permet d’afficher un cas d’utilisation de la vie réelle d’erreur avancé, gestion et journalisation avec des applications de la logique"
    keywords=""
    services="logic-apps"
    authors="hedidin"
    manager="anneta"
    editor=""
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="b-hoedid"/>

# <a name="logging-and-error-handling-in-logic-apps"></a>Enregistrement et gestion des erreurs dans la logique d’applications

Cet article décrit comment vous pouvez étendre une application logique pour mieux prendre en charge la gestion des exceptions. Il s’agit d’un cas d’utilisation de la vie réelle et notre réponse à la question de la « Logique Apps prend-il en charge exception et la gestion des erreurs ? »

>[AZURE.NOTE]La version actuelle de la fonctionnalité d’applications de la logique du Service d’application Microsoft Azure fournit un modèle standard pour les réponses de l’action.
>Cela inclut la validation interne et réponses d’erreur retournés à partir d’une application API.

## <a name="overview-of-the-use-case-and-scenario"></a>Vue d’ensemble du scénario et cas d’usage

L’article suivant est le cas d’utilisation de cet article.
Un établissement de santé connu engagé pour développer une solution Azure susceptibles de créer un portail d’un patient à l’aide de Microsoft Dynamics CRM Online. Il faut envoyer des enregistrements de rendez-vous entre le portail de patient Dynamics CRM Online et de la force de vente.  Nous avons demandé à la norme [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) pour tous les dossiers des patients.

Le projet avait deux exigences principales :  

 -  Une méthode pour enregistrer les enregistrements envoyés à partir du portail de Dynamics CRM Online
 -  Permet d’afficher toutes les erreurs qui se sont produites dans le flux de travail


## <a name="how-we-solved-the-problem"></a>Comment nous a résolu le problème

>[AZURE.TIP] Vous pouvez afficher une vidéo de haut niveau du projet au niveau du [Groupe d’utilisateurs intégration](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Intégration de groupe d’utilisateurs").

Nous avons choisi [d’Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "DocumentDB d’Azure") comme un référentiel pour les enregistrements de journal et d’erreur (DocumentDB fait référence à des enregistrements en tant que documents). Parce que la logique d’applications possède un modèle standard pour toutes les réponses, nous n’aurait pas à créer un schéma personnalisé. Nous pouvons créer une application API **d’Insérer** et de **requête** pour les enregistrements d’erreur et de journal. Nous pouvons également définir un schéma pour chacun au sein de l’application API.  

Une autre exigence a été de purger les enregistrements après une certaine date. DocumentDB a une propriété appelée [durée de vie]de(https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "durée de vie") (TTL), qui nous a permis de définir une valeur de **durée de vie** pour chaque enregistrement ou de la collection. Cela vous évite de supprimer manuellement les enregistrements de DocumentDB.

### <a name="creation-of-the-logic-app"></a>Création de l’application logique

La première étape consiste à créer l’application logique et de le charger dans le concepteur. Dans cet exemple, nous utilisons les applications logique parent-enfant. Supposons que nous ont déjà créé des parent et que vous allez créer une application de logique d’enfants.

Étant donné que nous allons être enregistrement l’enregistrement provenant de Dynamics CRM Online, commençons par le haut. Nous devons utiliser un déclencheur de la demande, car l’application de la logique parent déclenche cet enfant.

> [AZURE.IMPORTANT] Pour terminer ce didacticiel, vous devez créer une base de données DocumentDB et deux collections (journalisation et erreurs).

### <a name="logic-app-trigger"></a>Déclencheur d’application logique

Nous utilisons un déclencheur de la demande, comme indiqué dans l’exemple suivant.

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### <a name="steps"></a>Étapes

Nous avons besoin pour vous connecter la source des enregistrements du patient (demande) à partir du portail de Dynamics CRM Online.

1. Nous avons besoin obtenir un nouvel enregistrement de rendez-vous à partir de Dynamics CRM Online.
    Le déclencheur en provenance de CRM nous fournit la **CRM PatentId**, **type d’enregistrement**, **Nouveau ou mis à jour l’enregistrement** (nouveau ou mettre à jour la valeur de type Boolean) et **SalesforceId**. La **SalesforceId** peut être null, car il est utilisé uniquement pour une mise à jour.
    Nous allons l’enregistrement CRM à l’aide de la **PatientID** de CRM et le **Type d’enregistrement**.
1. Ensuite, nous devons ajouter notre application API de DocumentDB **InsertLogEntry** opération, comme indiqué dans les figures suivantes.


#### <a name="insert-log-entry-designer-view"></a>Insérer la vue concepteur d’entrée de journal

![Insérer l’entrée du journal](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### <a name="insert-error-entry-designer-view"></a>Insérer la vue de concepteur erreur entrée
![Insérer l’entrée du journal](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### <a name="check-for-create-record-failure"></a>À cocher pour créer l’échec de l’enregistrement

![Condition](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## <a name="logic-app-source-code"></a>Code source logique app

>[AZURE.NOTE]  Voici des exemples uniquement. Dans la mesure où ce didacticiel est basé sur une implémentation actuellement en production, la valeur d’un **Nœud Source** n’affichera ne peut-être pas les propriétés liées à la planification d’un rendez-vous.

### <a name="logging"></a>Enregistrement dans le journal
L’exemple de code d’application logique suivant montre comment gérer l’enregistrement.

#### <a name="log-entry"></a>Entrée de journal
C’est le code de source d’application logique pour l’insertion d’une entrée de journal.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Demande de journal

Il s’agit du message de demande de journal validé dans l’application de l’API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}"
        }
    }

```


#### <a name="log-response"></a>Enregistrer la réponse

C’est le message de réponse de journal à partir de l’application API.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "\"0400fc2f-0000-0000-0000-575b3ff00000\"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Maintenant examinons la procédure de gestion d’erreur.


### <a name="error-handling"></a>Gestion des erreurs

L’exemple de code de logique applications suivant montre comment vous pouvez implémenter la gestion des erreurs.

#### <a name="create-error-record"></a>Créer un enregistrement d’erreur

Il s’agit du code source logique Apps pour la création d’un enregistrement d’erreur.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}          
```

#### <a name="insert-error-into-documentdb--request"></a>Erreur d’insertion dans la DocumentDB--demande

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MasterID_mp__c\":\"\",\"C_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"ONY_ID__c\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-documentdb--response"></a>Erreur dans DocumentDB : réponse d’insertion


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "\"0c00eaac-0000-0000-0000-575b3fdc0000\"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\":\"DO - Degree level is DO\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MterID_mp__c\":\"\",\"Medicis_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"XXXXXXX\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Réponse d’erreur force de vente

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="returning-the-response-back-to-the-parent-logic-app"></a>Renvoi de la réponse à l’application de la logique parent

Une fois que vous avez la réponse, vous pouvez le passer à l’application de la logique parent.

#### <a name="return-success-response-to-the-parent-logic-app"></a>Renvoyer la réponse de succès à l’application de la logique parent

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "   Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-the-parent-logic-app"></a>Retourner la réponse d’erreur à l’application de la logique parent

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="documentdb-repository-and-portal"></a>Portail et référentiel de DocumentDB

Notre solution d’ajouter des fonctionnalités supplémentaires avec [DocumentDB](https://azure.microsoft.com/services/documentdb).

### <a name="error-management-portal"></a>Portail de gestion d’erreur

Pour afficher les erreurs, vous pouvez créer une application web MVC pour afficher les enregistrements à partir de DocumentDB. **Liste**, **Détails**, **Modifier**et **Supprimer** des opérations sont incluses dans la version actuelle.

> [AZURE.NOTE]Modifier l’opération : DocumentDB est un remplacement de l’intégralité du document.
> Les enregistrements affichés dans la **liste** et les vues de **Détail** sont uniquement des exemples. Ils ne sont pas des enregistrements de rendez-vous de patient réel.

Voici des exemples de détails application MVC créés avec l’approche décrite précédemment.

#### <a name="error-management-list"></a>Liste de gestion des erreurs

![Liste d’erreurs](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Affichage détaillé de gestion des erreurs

![Détails de l’erreur](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portail de gestion de journal

Pour afficher les journaux, nous avons créé également une application web MVC.  Voici des exemples de détails application MVC créés avec l’approche décrite précédemment.

#### <a name="sample-log-detail-view"></a>Exemple de journal vue Détails

![Affichage des détails de journal](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Détails d’application API

#### <a name="logic-apps-exception-management-api"></a>Gestion des exceptions logique applications API

Notre application de API open source logique applications exception management fournit les fonctionnalités suivantes.

Il existe deux contrôleurs :

- **ErrorController** insère un enregistrement d’erreur (document) dans une collection de DocumentDB.
- **LogController** Insère un enregistrement de journal (document) dans une collection de DocumentDB.

> [AZURE.TIP] Les deux contrôleurs utilisent `async Task<dynamic>` opérations. Ainsi, les opérations à être résolu lors de l’exécution, nous pouvons créer le schéma DocumentDB dans le corps de l’opération.

Chaque document de DocumentDB doit avoir un ID unique. Nous utilisons `PatientId` et l’ajout de l’horodatage qui est converti en une valeur d’horodatage Unix (double). Nous tronqué pour supprimer la valeur fractionnaire.

Vous pouvez afficher le code source de notre contrôleur erreur API [à partir de GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Nous appeler l’API à partir d’une application de logique à l’aide de la syntaxe suivante.

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

L’expression de l’exemple de code précédent est vérification de l’état de *Create_NewPatientRecord* de **l’Échec**.

## <a name="summary"></a>Résumé

- Vous pouvez facilement implémenter la journalisation et gestion des erreurs dans une application de logique.
- Vous pouvez utiliser DocumentDB comme référentiel pour les enregistrements de journal et d’erreur (documents).
- Vous pouvez utiliser MVC pour créer un portail pour afficher les enregistrements de journal et d’erreur.

### <a name="source-code"></a>Code source
Le code source pour la gestion des exceptions logique applications application API est disponible dans ce [référentiel de GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de gestion des exceptions logique d’application").


## <a name="next-steps"></a>Étapes suivantes
- [Afficher des scénarios et des exemples d’applications de logique plus](app-service-logic-examples-and-scenarios.md)
- [En savoir plus sur les outils de surveillance des applications de logique](app-service-logic-monitor-your-logic-apps.md)
- [Créer un modèle de déploiement automatisé de logique App](app-service-logic-create-deploy-template.md)
