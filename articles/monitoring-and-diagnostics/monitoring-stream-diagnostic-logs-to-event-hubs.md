<properties
    pageTitle="Flux de données des journaux de Diagnostic Azure à des concentrateurs d’événement | Microsoft Azure"
    description="Découvrez comment diffuser des journaux de Diagnostic Azure à des concentrateurs de l’événement."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="johnkem"/>

# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Flux de données des journaux de Diagnostic Azure à des concentrateurs d’événement

**[Journaux de Diagnostic Azure](monitoring-overview-of-diagnostic-logs.md)** peuvent être diffusées dans quasiment en temps réel à n’importe quelle application à l’aide de l’option « Exporter à événement concentrateurs » intégrée dans le portail, ou en activant l’Id de règle de Bus de Service dans un Diagnostic via les applets de commande PowerShell Azure Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Ce que vous pouvez faire avec les journaux de diagnostic et les concentrateurs d’événement
Voici quelques méthodes que vous pouvez utiliser la fonctionnalité de diffusion en continu pour les journaux de Diagnostic :

- **Journaux de flux 3ème partie enregistrement et systèmes de télémétrie** – au fil du temps, les concentrateurs d’événements de diffusion en continu devient le mécanisme de vos journaux de Diagnostic de tuyau dans les SIEMs de tiers et des solutions d’analytique de journaux.

- **Fonctionnement du service Affichage par diffusion en continu de données « chemin réactif » à PowerBI** – à l’aide de concentrateurs d’événement, flux Analytique et PowerBI, vous pouvez facilement transformer vos données de tests de diagnostic dans près des analyses en temps réel sur vos services Azure. [Cet article de la documentation fournit une excellente présentation de la procédure pour définir un événement de concentrateurs, de traiter les données avec un flux de données Analytique et d’utiliser des PowerBI sous la forme d’une sortie](../stream-analytics/stream-analytics-power-bi-dashboard.md). Voici quelques conseils pour la mise en route définie avec les journaux de Diagnostic :
    - Les concentrateurs d’événement pour une catégorie de journaux de Diagnostic est créé automatiquement lorsque vous activez l’option dans le portail ou l’activer par le biais de PowerShell, afin que vous souhaitez sélectionner les concentrateurs d’événement dans l’espace de noms du Bus de Service dont le nom commence par « perspectives- »
    - Voici un exemple de requête Analytique de flux que vous permet d’analyser tout simplement toutes les données de journal dans une table PowerBI :

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

- **Créer une plate-forme de la journalisation et de télémétrie personnalisé** – si ou est déjà une plateforme de télémétrie personnalisés sont simplement penser à un projet, hautement évolutive publication-abonnement nature de concentrateurs d’événements vous permet d’acquisition flexible des journaux de diagnostic. [Voir Dan Rosanova du guide de l’utilisation de concentrateurs d’événement dans une plate-forme de télémétrie d’échelle mondiale ici](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Activer la diffusion en continu de journaux de Diagnostic
Vous pouvez activer la diffusion en continu de journaux de Diagnostic par programmation, via le portail, ou à l’aide de l' [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx). Quoi qu’il en soit, vous choisissez un Namespace de Bus de Service et un événement de concentrateurs est créé dans l’espace de noms pour chaque catégorie d’enregistrement que vous activez. Une **Catégorie de journal** de Diagnostic est un type de journal d’une ressource peut collecter. Vous pouvez sélectionner les catégories de journal que vous souhaitez collecter pour une ressource particulière dans le portail Azure sous la lame de Diagnostics.

![Catégories de journaux dans le portail](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [AZURE.WARNING] L’activation et la diffusion en continu de journaux de diagnostic à partir des ressources (par exemple, les ordinateurs virtuels ou Service Fabric) Compute [nécessite un ensemble différent de procédures](../event-hubs/event-hubs-streaming-azure-diags-data.md).

### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell
Pour activer la diffusion en continu via les [Applets de commande PowerShell Azure](insights-powershell-samples.md), vous pouvez utiliser la `Set-AzureRmDiagnosticSetting` applet de commande avec ces paramètres :

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

L’ID de règle de Bus de Service est une chaîne de ce format : `{service bus resource ID}/authorizationrules/{key name}`, par exemple, `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.


### <a name="via-azure-cli"></a>Via l’interface CLI Azure
Pour activer la diffusion en continu via la [CLI d’Azure](insights-cli-samples.md), vous pouvez utiliser la `insights diagnostic set` commande comme suit :

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Utilisez le même format pour l’ID de règle de Bus de Service comme expliqué pour la PowerShell Cmdlet.

###<a name="via-azure-portal"></a>Via le portail Azure
Pour activer la diffusion en continu via le portail d’Azure, accédez aux paramètres de tests de diagnostic d’une ressource et sélectionnez « Exportation à concentrateur d’événements ».

![Exporter vers les concentrateurs d’événement dans le portail](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Pour le configurer, sélectionnez un Namespace de Bus de Service existant. L’espace de noms sélectionné sera où les concentrateurs d’événements est créé (s’il s’agit de votre première fois en continu les journaux de diagnostic) ou diffusé sur (si il existe déjà les ressources qui sont en continu à cette catégorie de journal pour cet espace de noms), et la stratégie définit les autorisations le mécanisme de transmission en continu. Aujourd'hui, diffusion en continu à un événement de concentrateurs requiert des autorisations d’envoi, la lecture et la gestion. Vous pouvez créer ou modifier des stratégies d’accès Service Namespace de Bus partagé dans le portail classique sous l’onglet « Configuration » pour votre Namespace de Bus de Service. Pour mettre à jour un de ces paramètres de Diagnostic, le client doit avoir l’autorisation de ListKey sur la règle d’autorisation du Bus de Service.

##<a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Comment consommer les données du journal de concentrateurs d’événement ?
Voici un exemple de données de sortie de concentrateurs de l’événement :

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nom de l’élément | Description                                            |
|--------------|--------------------------------------------------------|
|enregistrements       | Un tableau de tous les événements de journal dans cette charge utile.            |
|heure          | Heure à laquelle l’événement s’est produit.                      |
|catégorie      | Catégorie de journal pour cet événement.                           |
|resourceId    | ID de ressource de la ressource qui a généré cet événement. |
|operationName | Nom de l’opération.                                 |
|niveau         | Facultatif. Indique le niveau d’événement du journal.               |
|propriétés    | Propriétés de l’événement.                               |


Vous pouvez afficher une liste de tous les fournisseurs de ressources qui prennent en charge la lecture en continu vers le concentrateur d’événements [ici](monitoring-overview-of-diagnostic-logs.md).

##<a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les journaux de Diagnostic Azure](monitoring-overview-of-diagnostic-logs.md)
- [Mise en route avec les concentrateurs d’événement](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
