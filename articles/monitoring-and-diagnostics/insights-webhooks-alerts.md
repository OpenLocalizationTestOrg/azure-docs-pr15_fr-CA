<properties
    pageTitle="Configurer webhooks sur des alertes métriques Azure | Microsoft Azure"
    description="Rediriger des alertes Azure à d’autres systèmes non-Azure."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configurer un webhook sur une alerte métrique Azure

Webhooks vous permettent d’acheminer une notification d’alerte Azure à d’autres systèmes pour les actions de post-traitement ou personnalisées. Vous pouvez utiliser un webhook sur une alerte auquel elle est transmise aux services d’envoyer des SMS, enregistrer des bogues, notifient une équipe via les services de chat/messagerie ou effectuer diverses autres actions. Cet article décrit comment définir un webhook sur une alerte métrique Azure et ce à quoi ressemble la charge utile pour la publication HTTP à un webhook. Pour plus d’informations sur la configuration et le schéma d’un journal d’activité Azure alert (alerte sur les événements), [consultez la page suivante à la place](./insights-auditlog-to-webhook-email.md).

Alertes Azure HTTP POST le contenu de l’alerte dans JSON format, schéma défini ci-dessous, pour un webhook URI que vous fournissez lors de la création de l’alerte. Cet URI doit être un point de terminaison HTTP ou HTTPS valide. Azure publie une entrée par demande lorsqu’une alerte est activée.

## <a name="configuring-webhooks-via-the-portal"></a>Configuration webhooks de via le portail

Vous pouvez ajouter ou mettre à jour de l’URI de webhook dans l’écran alertes de créer/mettre à jour dans le [portail](https://portal.azure.com/).

![Ajouter une règle d’alerte](./media/insights-webhooks-alerts/Alertwebhook.png)

Vous pouvez également configurer une alerte pour publier sur un URI en utilisant les [Applets de commande PowerShell Azure](./insights-powershell-samples.md#create-alert-rules), [Multiplates-formes CLI](./insights-cli-samples.md#work-with-alerts)ou [Azure moniteur reste API](https://msdn.microsoft.com/library/azure/dn933805.aspx)de webhook.

## <a name="authenticating-the-webhook"></a>L’authentification de la webhook

Le webhook peut s’authentifier à l’aide d’une des méthodes suivantes :

1. **Autorisation basée sur un jeton** - l’URI de webhook est enregistré avec un ID de jeton, par exemple. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **D’autorisation de base** - l’URI de webhook est enregistré avec un nom d’utilisateur et le mot de passe, par exemple. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schéma de charge utile

L’opération de publication contient la charge utile suivante de JSON et le schéma pour toutes les alertes basées sur la mesure.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Champ | Obligatoire | Ensemble fixe de valeurs | Notes |
| :-------------| :-------------   | :-------------   | :-------------   |
|état|Y|« Activé », « Résolu »|Statut de l’alerte en fonction des conditions que vous avez défini.|
|contexte| Y | | Le contexte de l’alerte.|
|horodatage| Y | | Heure à laquelle l’alerte a été déclenchée.|
|ID | Y | | Chaque règle d’alerte possède un id unique.|
|nom               |Y                  |                   | Le nom de l’alerte.|
|Description        |Y                  |                           |Description de l’alerte.|
|conditionType      |Y                  |« Mesure », « Événement »          |Deux types d’alertes sont pris en charge. Une basée sur une condition de métriques et l’autre basent sur un événement dans le journal d’activité. Utilisez cette valeur pour vérifier si l’alerte est basée sur mesure ou un événement.|
|condition          |Y                  |                           | Les champs spécifiques à vérifier en fonction de la conditionType.|
|metricName         |pour les alertes métriques  |                           |Le nom de la mesure qui définit ce que la règle surveille.|
|metricUnit         |pour les alertes métriques  |« Octets », « BytesPerSecond », « Count », « CountPerSecond », « Pourcentage », « Secondes »|     L’unité est autorisée dans la mesure. Les [valeurs autorisées sont répertoriés ici](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx).|
|metricValue        |pour les alertes métriques  |                           |La valeur réelle de la mesure qui a provoqué l’alerte.|
|seuil          |pour les alertes métriques  |                           |La valeur seuil à laquelle l’alerte est activée.|
|windowSize         |pour les alertes métriques  |                           |La période de temps qui est utilisée pour surveiller l’activité des alerte basée sur le seuil. Il faut entre 5 minutes et 1 jour. Format de durée ISO 8601.|
|timeAggregation    |pour les alertes métriques  |« Moyen », « Last », « Maximum », « Minimum », « None », « Total » | Comment les données sont collectées doivent être combinées dans le temps. La valeur par défaut est moyenne. Les [valeurs autorisées sont répertoriés ici](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx).|
|opérateur           |pour les alertes métriques  |                           |Opérateur utilisé pour comparer les données de mesure en cours pour le seuil défini.|
|subscriptionId     |Y                  |                           |ID d’abonnement Azure.|
|resourceGroupName  |Y                  |                           |Nom du groupe de ressource pour la ressource d’impactés.|
|nom_ressource       |Y                  |                           |Nom de la ressource de la ressource d’impactés.|
|type de ressource       |Y                  |                           |Type de ressource de la ressource d’impactés.|
|resourceId         |Y                  |                           |ID de ressource de la ressource d’impactés.|
|resourceRegion     |Y                  |                           |Région ou l’emplacement de la ressource d’impactés.|
|portalLink         |Y                  |                           |Lien direct vers la page de résumé du portail de ressources.|
|propriétés         |N                  |Facultatif                   |Jeu de `<Key, Value>` paires (par exemple, `Dictionary<String, String>`) qui contient des détails sur l’événement. Le champ de propriétés est facultatif. Dans une logique ou l’interface utilisateur basée sur l’application flux de travail personnalisé, les utilisateurs peuvent entrer de clé/valeur qui peut être passé par la charge utile. L’autre façon de passer des propriétés personnalisées à la webhook est via l’uri du webhook (en tant que paramètres de la requête)|


>[AZURE.NOTE] Le champ de propriétés peut uniquement être défini à l’aide de l' [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les alertes Azure et webhooks dans vidéo [Intégrer les alertes Azure avec PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
- [Exécuter des scripts d’automatisation de Azure (procédures opérationnelles) sur alertes Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Logique d’application permet d’envoyer un SMS via Twilio à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [Logique d’application permet d’envoyer un message de marge d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [Utiliser la logique d’application pour envoyer un message à une file d’attente Azure à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
