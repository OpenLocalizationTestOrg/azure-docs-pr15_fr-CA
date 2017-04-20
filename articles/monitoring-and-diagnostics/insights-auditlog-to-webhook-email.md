<properties
    pageTitle="Configurer un webhook sur les alertes de journal d’activité Azure | Microsoft Azure"
    description="Apprendre à utiliser les alertes de journal d’activité pour appeler webhooks. "
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
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-activity-log-alerts"></a>Configurer un webhook sur un alertes Azure journal d’activité

Webhooks vous permettent d’acheminer une notification d’alerte Azure à d’autres systèmes pour les actions de post-traitement ou personnalisées. Vous pouvez utiliser un webhook sur une alerte auquel elle est transmise aux services d’envoyer des SMS, enregistrer des bogues, notifient une équipe via les services de chat/messagerie ou effectuer diverses autres actions. Cet article décrit comment définir un webhook sur une alerte Azure le journal d’activité et ce à quoi ressemble la charge utile pour la publication HTTP à un webhook. Pour plus d’informations sur la configuration et le schéma d’une alerte de métriques Azure, [consultez cette page à la place](insights-webhooks-alerts.md). Vous pouvez également définir une mise en garde le journal d’activité pour envoyer du courrier électronique lors de l’activation.

>[AZURE.NOTE] Cette fonctionnalité est actuellement en mode Aperçu, attendez-vous de performances et qualité variable.

Vous pouvez configurer une alerte de journal d’activité utilisant les [Applets de commande PowerShell Azure](insights-powershell-samples.md#create-alert-rules), [Multiplates-formes CLI](insights-cli-samples.md#work-with-alerts)ou [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>L’authentification de la webhook
Webhook d’estimée peut s’authentifier à l’aide d’une des méthodes suivantes :

1. **Autorisation basée sur un jeton** - l’URI de webhook est enregistré avec un ID de jeton, par exemple. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **D’autorisation de base** - l’URI de webhook est enregistré avec un nom d’utilisateur et le mot de passe, par exemple. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schéma de charge utile
L’opération de publication contient la charge utile suivante de JSON et le schéma pour toutes les alertes basées sur le journal d’activité. Ce schéma est semblable à celui utilisé par les alertes basées sur la mesure.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Nom de l’élément       |Description|
|---                |---|
|état             |Utilisé pour les alertes métriques. Toujours la valeur « activé » pour les alertes de journal d’activité.|
|contexte            |Contexte de l’événement.|
|resourceProviderName|Le fournisseur de ressources de la ressource d’impactés.|
|conditionType      |Toujours « événement ».|
|nom               |Nom de la règle d’alerte.|
|ID                 |ID de ressource de l’alerte.|
|Description        |Description de l’alerte tel que défini lors de la création de l’alerte.|
|subscriptionId     |ID d’abonnement Azure.|
|horodatage          |Heure à laquelle l’événement a été généré par le service Azure qui a traité la demande.|
|resourceId         |ID de ressource de la ressource d’impactés.|
|resourceGroupName  |Nom du groupe de ressource pour la ressource d’impactés|
|propriétés         |Jeu de `<Key, Value>` paires (par exemple, `Dictionary<String, String>`) qui contient des détails sur l’événement.|
|événement              |Élément qui contient des métadonnées relatives à l’événement.|
|autorisation      |Les propriétés RBAC de l’événement. Il s’agit généralement « action », « rôle » et le « étendue ».|
|catégorie           |Catégorie de l’événement. Incluent les valeurs prises en charge : administration, d’alerte, de sécurité, de ServiceHealth, de recommandation.|
|appelant             |Adresse e-mail de l’utilisateur qui a exécuté l’opération, la revendication de nom UPN ou la revendication de nom principal de service basée sur la disponibilité. Peut être null pour certains appels système.|
|ID de corrélation      |Généralement, un GUID sous forme de chaîne. Les événements avec les ID de corrélation appartiennent à la même action de plus grande et partagent en général un ID de corrélation.|
|eventDescription   |Description de texte statique de l’événement.|
|eventDataId        |Identificateur unique de l’événement.|
|source d’événement        |Nom du service Azure ou infrastructure ayant généré l’événement.|
|httpRequest        |Inclut généralement les « clientRequestId », le « clientIpAddress » et la « méthode » (méthode HTTP PUT, par exemple).|
|niveau              |Une des valeurs suivantes : « Critique », « Erreur », « Warning » et « Informationnels » et « Verbose ».|
|operationId        |Généralement un GUID partagé entre les événements correspondant à une opération unique.|
|operationName      |Nom de l’opération.|
|propriétés         |Propriétés de l’événement.|
|état             |Chaîne. État de l’opération. Les valeurs courantes sont : « Démarré », « En cours », « Réussi », « Échec », « Active », « Résolu ».|
|Sous-état          |Inclut généralement le code d’état HTTP de l’appel de repos correspondant. Il peut également inclure d’autres chaînes décrivant un sous-état. Sous-état des valeurs courantes sont : OK (Code d’état HTTP : 200), créé (Code d’état HTTP : 201), accepté (Code d’état HTTP : 202), le contenu ne (Code d’état HTTP : 204), demande incorrecte (Code d’état HTTP : 400), introuvable (Code d’état HTTP : 404), conflit (Code d’état HTTP : 409), erreur de serveur interne (Code d’état HTTP : 500), Service non disponible (Code d’état HTTP : 503), délai de la passerelle (Code d’état HTTP : 504)|

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur le journal d’activité](monitoring-overview-activity-logs.md)
- [Exécuter des scripts d’automatisation de Azure (procédures opérationnelles) sur alertes Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Application de logique d’utiliser pour envoyer un SMS via Twilio à partir d’une alerte d’Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Cet exemple est pour les alertes métriques, mais peut être modifié pour travailler avec une alerte du journal d’activité.
- [Application de logique utilisé pour envoyer un message de marge d’une alerte d’Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Cet exemple est pour les alertes métriques, mais peut être modifié pour travailler avec une alerte du journal d’activité.
- [Application de logique d’utiliser pour envoyer un message à une file d’attente Azure à partir d’une alerte d’Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Cet exemple est pour les alertes métriques, mais peut être modifié pour travailler avec une alerte du journal d’activité.
