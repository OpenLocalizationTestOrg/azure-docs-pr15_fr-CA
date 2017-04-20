<properties
   pageTitle="API d’alerte reste journal Analytique"
   description="Le journal Analytique alerte reste API vous permet de créer et de gérer les alertes dans Operations Management Suite (OMS).  Cet article fournit des détails sur l’API et plusieurs exemples permettant d’effectuer différentes opérations."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="log-analytics-alert-rest-api"></a>Analytique de journal d’alerte API REST

Le journal Analytique alerte reste API vous permet de créer et de gérer les alertes dans Operations Management Suite (OMS).  Cet article fournit des détails sur l’API et plusieurs exemples permettant d’effectuer différentes opérations.

L’API REST de journal Analytique recherche est RESTful et sont accessibles via le REST API de gestionnaire de ressources Azure. Dans ce document vous trouverez des exemples où l’API est accessible à partir d’une ligne de commande PowerShell à l’aide [ARMClient](https://github.com/projectkudu/ARMClient), un outil de ligne de commande open source qui simplifie l’appel de l’API du Gestionnaire de ressources Azure. L’utilisation de ARMClient et de PowerShell est une des nombreuses options pour accéder à l’API de recherche de journal Analytique. Grâce à ces outils, vous pouvez utiliser l’API du Gestionnaire de ressources Azure RESTful pour effectuer des appels vers des espaces de travail OMS et exécuter des commandes de recherche qu’ils contiennent. L’API aura pour résultat les résultats de recherche pour vous au format JSON, ce qui vous permet d’utiliser les résultats de la recherche de différentes façons par programme.

## <a name="prerequisites"></a>Conditions préalables
Actuellement, les alertes ne peuvent être créés qu’avec une recherche enregistrée dans le journal Analytique.  Vous pouvez faire référence à l' [API REST de recherche journal](log-analytics-log-search-api.md) pour plus d’informations.

## <a name="schedules"></a>Planifications
Une recherche enregistrée peut avoir une ou plusieurs planifications. La planification définit la fréquence à laquelle la recherche est exécutée et que l’intervalle de temps pendant laquelle les critères est identifié.
Planifications ont les propriétés dans le tableau suivant.

| Propriété  | Description |
|:--|:--|
| Intervalle | La fréquence à laquelle la recherche est exécutée. Mesuré en minutes. |
| QueryTimeSpan | L’intervalle de temps pendant laquelle les critères est évalué. Doit être égale ou supérieure à l’intervalle. Mesuré en minutes. |
| Version | La version de l’API utilisée.  Actuellement, il doit toujours être défini sur 1. |

Par exemple, considérez une requête d’événement avec un intervalle de 15 minutes et un Timespan de 30 minutes. Dans ce cas, la requête est exécutée toutes les 15 minutes, et une alerte doit être déclenchée si les critères ont continué à résoudre sur true un intervalle de 30 minutes.

### <a name="retrieving-schedules"></a>Lors de la récupération des planifications
Utilisez la méthode Get pour extraire toutes les planifications pour une recherche enregistrée.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Utilisez la méthode Get avec un code de grille pour récupérer un planning spécifique pour une recherche enregistrée.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Voici un exemple de réponse d’une grille.

    {
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
        "Interval": 15,
        "QueryTimeSpan": 15
    }

### <a name="creating-a-schedule"></a>Création d’une planification
Utilisez la méthode Put avec un code de grille unique pour créer une nouvelle planification.  Notez que deux planifications ne peut pas avoir le même ID de même s’ils sont associés avec différentes recherches enregistrées.  Lorsque vous créez une planification dans la console de l’OMS, un GUID est créé pour le code de la grille.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Modification d’une planification
Utilisez la méthode Put avec un ID de planification existant pour la même recherche enregistrée à modifier cette planification.  Le corps de la demande doit inclure l’etag de la planification.

    $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Suppression de planifications
Pour supprimer une planification, utilisez la méthode Delete avec un code de grille.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Actions
Une planification peut avoir plusieurs actions. Une action peut définir un ou plusieurs processus à effectuer telles que l’envoi d’un courrier électronique ou le démarrage d’une procédure opérationnelle, ou il peut définir un seuil qui détermine si les résultats d’une recherche correspondent à certains critères.  Certaines actions définira les deux afin que les processus sont exécutés lorsque le seuil est atteint.

Toutes les actions ont les propriétés dans le tableau suivant.  Différents types d’alertes ont différentes propriétés supplémentaires qui sont décrites ci-dessous.

| Propriété | Description |
|:--|:--|
| Type de | Type de l’action.  Actuellement, les valeurs possibles sont alerte et Webhook. |
| Nom | Nom complet de l’alerte. |
| Version | La version de l’API utilisée.  Actuellement, il doit toujours être défini sur 1. |

### <a name="retrieving-actions"></a>Actions lors de la récupération
Utilisez la méthode Get pour extraire toutes les actions d’une grille.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Pour récupérer une action particulière pour un programme, utilisez la méthode Get avec l’ID de l’action.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Création ou modification d’actions
Utilisez la méthode Put avec un ID d’action qui est unique à la planification pour créer une nouvelle action.  Lorsque vous créez une action de la console de l’OMS, un GUID est pour l’ID de l’action.

Utilisez la méthode Put avec un code d’action existant pour la même recherche enregistrée à modifier cette planification.  Le corps de la demande doit inclure l’etag de la planification.

Le format de la demande de création d’une nouvelle action varie en fonction du type d’action afin que ces exemples sont fournis dans les sections ci-dessous.

### <a name="deleting-actions"></a>Suppression d’actions
Pour supprimer une action, utilisez la méthode Delete avec l’ID de l’action.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Actions d’alerte
Une planification doit avoir qu’une seule action d’alerte.  Les actions d’alerte ont un ou plusieurs des sections dans le tableau suivant.  Chacune est décrite plus en détail ci-dessous.

| Section | Description |
|:--|:--|
| Seuil | Critères de lorsque l’action est exécutée. |  
| EmailNotification | Envoyer des messages à plusieurs destinataires. |
| Mise à jour | Démarrer une procédure opérationnelle dans Automation Azure pour tenter de corriger le problème identifié. |

#### <a name="thresholds"></a>Seuils
Une action d’alerte doit avoir un seul et unique de seuil.  Lorsque les résultats d’une recherche enregistrée correspondent au seuil à une action associée à cette recherche, tous les autres processus dans cette action sont exécutés.  Une action peut également contenir uniquement un seuil afin qu’il peut être utilisé avec les actions des autres types ne contenant pas de seuils.

Seuils ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Opérateur | Opérateur pour la comparaison de seuil. <br> gt = supérieur à <br> lt = inférieur à |
| Valeur | Valeur du seuil. |

Par exemple, considérez une requête d’événement avec un intervalle de 15 minutes, un Timespan de 30 minutes et un seuil supérieur à 10. Dans ce cas, la requête est exécutée toutes les 15 minutes et une alerte doit être déclenchée si elle a retourné 10 événements qui ont été créés sur une durée de 30 minutes.

Voici un exemple de réponse à une action avec uniquement un seuil.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Pour créer une nouvelle action de seuil pour un programme, utilisez la méthode Put avec un ID d’action unique.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Pour modifier une action de seuil pour une planification, utilisez la méthode Put avec un code d’action existant.  Le corps de la demande doit inclure l’etag de l’action.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Notification par courrier électronique
Notifications par courrier électronique envoyer du courrier à un ou plusieurs destinataires.  Ils incluent les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Destinataires | Liste des adresses de messagerie. |
| Objet | L’objet du message. |
| Pièce jointe | Les pièces jointes ne sont pas actuellement pris en charge, cela aura toujours la valeur « None ». |

Voici un exemple de réponse pour une action de notification de courrier électronique avec un seuil.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Pour créer une nouvelle action de messagerie pour un programme, utilisez la méthode Put avec un ID d’action unique.  L’exemple suivant crée une notification par e-mail avec un seuil afin que le courrier est envoyé lorsque les résultats de la recherche enregistrée dépassent le seuil.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

Pour modifier une action de message électronique pour une planification, utilisez la méthode Put avec un code d’action existant.  Le corps de la demande doit inclure l’etag de l’action.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### <a name="remediation-actions"></a>Actions correctives
Correctives démarrer une procédure opérationnelle dans Azure Automation qui tente de corriger le problème identifié par l’alerte.  Vous devez créer un webhook pour la procédure opérationnelle utilisée dans une action de conversion et puis spécifiez l’URI dans la propriété WebhookUri.  Lorsque vous créez cette action à l’aide de la console de l’OMS, un nouveau webhook est automatiquement créé pour la procédure opérationnelle.

Correctives incluent les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| RunbookName | Nom de la procédure opérationnelle. Cela doit correspondre à une procédure publiée opérationnelle dans le compte de l’automation configuré dans la Solution d’Automation dans votre espace de travail de l’OMS. |
| WebhookUri | URI de la webhook.
| Expiration | La date d’expiration et l’heure de le webhook.  Si le webhook n’est pas une date d’expiration, cela peut être n’importe quelle date ultérieure valide. |

Voici un exemple de réponse pour une action de mise à jour avec un seuil.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Pour créer une nouvelle action de mise à jour pour un programme, utilisez la méthode Put avec un ID d’action unique.  L’exemple suivant crée une mise à jour avec un seuil pour la procédure opérationnelle est démarré lorsque les résultats de la recherche enregistrée dépassent le seuil.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Pour modifier une action de conversion pour une planification, utilisez la méthode Put avec un code d’action existant.  Le corps de la demande doit inclure l’etag de l’action.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Exemple
Voici un exemple complet pour créer un nouveau message d’alerte.  Cela crée une planification et une action contenant un seuil et un courrier électronique.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Actions de Webhook
Actions de Webhook démarrer un processus en appelant une URL et éventuellement fournir une charge utile à envoyer.  Ils sont similaires aux actions de mise à jour, mais elles sont destinées à webhooks qui peut appeler des processus autres que les procédures opérationnelles d’Automation d’Azure.  Elles fournissent également l’option supplémentaire de fournir une charge utile à livrer au processus distant.

Actions de Webhook n’ont pas un seuil mais plutôt qu’il doivent être ajoutées à un plan qui contient une action avec un seuil d’alerte.  Vous pouvez ajouter plusieurs actions Webhook tous à exécuter lorsque le seuil est atteint.

Actions de Webhook contiennent les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| WebhookUri | L’objet du message. |
| CustomPayload | Charge utile de personnalisé à envoyer à la webhook.  Le format dépend de ce que le webhook est attendu. |

Voici un exemple de réponse pour l’action de webhook et d’une action d’alerte associée avec un seuil.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Créer ou modifier une action webhook
Pour créer une nouvelle action de webhook pour un programme, utilisez la méthode Put avec un ID d’action unique.  L’exemple suivant crée une action de Webhook et d’une action d’alerte avec un seuil afin que le webhook sera déclenchée lorsque les résultats de la recherche enregistrée dépassent le seuil.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Pour modifier une action webhook pour une planification, utilisez la méthode Put avec un code d’action existant.  Le corps de la demande doit inclure l’etag de l’action.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Étapes suivantes

- Utilisez l' [API REST pour effectuer des recherches de journal](log-analytics-log-search-api.md) dans le journal Analytique.
