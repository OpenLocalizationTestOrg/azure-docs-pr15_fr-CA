<properties 
    pageTitle="Surveillance de vos applications de logique dans le Service d’application Azure | Microsoft Azure" 
    description="Comment savoir ce qu’ont fait vos applications logique" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="monitor-your-logic-apps"></a>Surveillance de vos applications de logique

Après avoir [créer une application de logique](app-service-logic-create-a-logic-app.md), vous pouvez voir l’historique complet de son exécution dans le portail Azure.  Vous pouvez également définir des services, tels que des Diagnostics d’Azure et d’Azure alertes pour surveiller les événements en temps réel et alertes pour les événements souhaitée « en cas d’échouent des séries de plus de 5 en une heure ».

## <a name="monitor-in-the-azure-portal"></a>Moniteur dans le portail Azure

Pour afficher l’historique, sélectionnez **Parcourir**et sélectionnez **Les applications logique**. Une liste de toutes les applications de logique dans votre abonnement s’affiche.  Sélectionnez l’application logique que vous souhaitez surveiller.  Vous verrez une liste de toutes les actions et les déclencheurs qui ont eu lieu pour cette application logique.

![Vue d’ensemble](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Il existe quelques sections sur cette blade utiles :

- **Résumé** répertorie **tous s’exécute** et l' **Historique de déclencheur**
    - Liste de **tous les exécute** la dernière application logique s’exécute.  Vous pouvez cliquez sur n’importe quelle ligne pour plus d’informations sur l’exécution, ou sur la mosaïque pour répertorier plusieurs exécutions.
    - **Déclencheur historique** répertorie toutes les activités de déclencheur pour cette application logique.  Activité du déclencheur peut être un contrôle « Ignoré » pour les nouvelles données (par exemple, à la recherche pour savoir si un nouveau fichier a été ajouté pour FTP), « Réussi » ce qui signifie que de données a été renvoyées à une application de logique, ou « Échec » correspondant d’une erreur de configuration.
- **Diagnostics** vous permet d’afficher les événements et les détails de l’exécution, et s’abonner aux [Alertes d’Azure](#adding-azure-alerts)

>[AZURE.NOTE] Tous les événements et les détails de l’exécution sont cryptées au reste dans le service d’application de la logique. Ils sont décryptées uniquement lors d’une demande d’affichage d’un utilisateur. Accès à ces événements peut également être contrôlé par le contrôle d’accès d’Azure Role-Based (RBAC).

### <a name="view-the-run-details"></a>Afficher les détails d’exécution

Cette liste de séries d’affiche le **statut**, l' **Heure de début**et la **durée** de la notamment exécution. Sélectionnez une ligne pour afficher des détails sur cette exécution.

La surveillance affiche que chaque étape de l’exécution, les entrées et les sorties et les messages d’erreur susceptibles d’être occurre.

![Exécuter et Actions](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

Si vous avez besoin des détails supplémentaires, tels que l' **ID de corrélation** exécution (ce qui peut être utilisé pour l’API REST), vous pouvez cliquer sur le bouton **Détails de l’exécution** .  Cela inclut toutes les étapes, l’état et les entrées/sorties de l’exécution.

## <a name="azure-diagnostics-and-alerts"></a>Diagnostics de Windows Azure et alertes

Outre les informations fournies par le portail Azure et l’API REST ci-dessus, vous pouvez configurer votre application de logique pour utiliser les Diagnostics Azure pour plus de détails enrichis et le débogage.

1. Cliquez sur la section des **Diagnostics** de la lame d’application logique
1. Cliquez ici pour configurer les **Paramètres de Diagnostic**
1. Configurer un concentrateur d’événements ou un compte de stockage pour l’émission de données

    ![Paramètres de diagnostic Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### <a name="adding-azure-alerts"></a>Ajout d’alertes Azure

Une fois les tests de diagnostic sont configurés, vous pouvez ajouter des alertes de Azure se déclenche lorsque certains seuils sont dépassés.  Dans la lame de **Diagnostics** , sélectionnez les **alertes** mosaïque et **Ajouter une alerte**.  Cela vous aidera à la configuration d’une alerte basée sur un certain nombre de mesures et les seuils.

![Mesures d’alerte Azure](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

Comme vous le souhaitez, vous pouvez configurer la **Condition**, le **seuil**et la **période** .  Enfin, vous pouvez configurer une adresse de messagerie pour envoyer une notification pour, ou configurer un webhook.  Vous pouvez utiliser le [déclencheur de demande](../connectors/connectors-native-reqres.md) dans une logique d’application pour s’exécuter sur une alerte (pour faire des choses comme le [valider dans la marge](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app), [Envoyer un texte](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)ou [Ajouter un message à une file d’attente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)).

### <a name="azure-diagnostics-settings"></a>Paramètres de Diagnostics de Windows Azure

Chacun de ces événements contient plus d’informations sur l’application de la logique et l’événement comme état.  Voici un exemple d’un événement *ActionCompleted* :

```javascript
{
            "time": "2016-07-09T17:09:54.4773148Z",
            "workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
            "resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-06-01",
                "startTime": "2016-07-09T17:09:53.4336305Z",
                "endTime": "2016-07-09T17:09:53.5430281Z",
                "status": "Succeeded",
                "code": "OK",
                "resource": {
                    "subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
                    "resourceGroupName": "MyResourceGroup",
                    "workflowId": "cff00d5458f944d5a766f2f9ad142553",
                    "workflowName": "MyLogicApp",
                    "runId": "08587361146922712057",
                    "location": "eastus",
                    "actionName": "Http"
                },
                "correlation": {
                    "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
                    "clientTrackingId": "my-custom-tracking-id"
                },
                "trackedProperties": {
                    "myProperty": "<value>"
                }
            }
        }
```

Les deux propriétés sont particulièrement utiles pour le suivi et la surveillance sont *clientTrackingId* et *trackedProperties*.  

#### <a name="client-tracking-id"></a>ID de suivi client

Le suivi des ID de client est une valeur qui sera de corréler les événements sur une application de logique fonctionnent, y compris tous les workflows imbriquées appelées dans le cadre d’une application de logique.  Ce code est généré automatiquement si n’est pas fourni, mais vous pouvez spécifier manuellement le client ID de suivi à partir d’un déclencheur en passant un `x-ms-client-tracking-id` en-tête avec la valeur de l’ID de la demande de déclencheur (trigger de la demande, un déclencheur HTTP ou webhook déclencheur).

#### <a name="tracked-properties"></a>Propriétés de suivi

Les propriétés peuvent être ajoutées sur actions dans la définition de flux de travail pour effectuer le suivi des entrées ou des sorties de données de diagnostic.  Cela peut être utile si vous souhaitez effectuer le suivi des données comme un « ID d’ordre » dans votre télémétrie.  Pour ajouter une propriété de suivi, incluez les `trackedProperties` propriété sur une action.  Propriétés de suivi peut uniquement le suivi un entrées des actions simples et les sorties, mais vous pouvez utiliser la `correlation` propriétés des événements permettant de corréler entre actions lors de l’exécution.

```javascript
{
    "myAction": {
        "type": "http",
        "inputs": {
            "uri": "http://uri",
            "headers": {
                "Content-Type": "application/json"
            },
            "body": "@triggerBody()"
        },
        "trackedProperties":{
            "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
            "myActionHTTPValue": "@action()['outputs']['body']['foo']",
            "transactionId": "@action()['inputs']['body']['bar']"
        }
    }
}
```

### <a name="extending-your-solutions"></a>Extension de vos solutions

Vous pouvez exploiter cette télémétrie à partir du concentrateur de l’événement ou le stockage à d’autres services comme [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/)et [Alimentation BI](https://powerbi.com) pour que la surveillance en temps réel de votre flux de travail d’intégration.

## <a name="next-steps"></a>Étapes suivantes
- [Commune des exemples et des scénarios pour les applications de logique](app-service-logic-examples-and-scenarios.md)
- [Création d’un modèle de déploiement d’application logique](app-service-logic-create-deploy-template.md)
- [Fonctionnalités d’intégration entreprise](app-service-logic-enterprise-integration-overview.md)
