<properties
    pageTitle="Utilisation de PowerShell pour créer des alertes pour des services Azure | Microsoft Azure"
    description="Utilisation de PowerShell pour créer des alertes Azure, qui peuvent déclencher des notifications ou automation lorsque les conditions spécifiées sont remplies."
    authors="rboucher"
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
    ms.author="robb"/>

# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Utilisation de PowerShell pour créer des alertes pour des services Azure

> [AZURE.SELECTOR]
- [Portail](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Vue d’ensemble

Cet article vous explique comment configurer les alertes Azure à l’aide de PowerShell.  

Vous pouvez recevoir une alerte basée sur des mesures de surveillance pour ou d’événements sur vos services Azure.

- **Valeurs métriques** - l’alert se déclenche lorsque la valeur d’une mesure spécifiée dépasse un seuil que vous affectez dans les deux sens. En d’autres termes, il déclenche à la fois lorsque la condition est remplie tout d’abord, et ensuite par la suite que lorsque la condition est n’est plus remplie.    
- **Événements de journal d’activité** - une alerte peut déclencher sur *chaque* événement, et uniquement lorsqu’un certain nombre d’événements se produire.

Vous pouvez configurer une alerte pour effectuer les opérations suivantes lorsqu’il déclenche :

- envoyer des notifications par courrier électronique à l’administrateur de service et des administrateurs
- envoyer les courriels supplémentaires que vous spécifiez.
- appeler un webhook
- lancer l’exécution d’une procédure Azure opérationnelle (uniquement à partir du portail Azure)

Vous pouvez configurer et obtenir des informations sur les règles d’alerte à l’aide de

- [Azure portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de ligne de commande (CLI)](insights-alerts-command-line-interface.md)
- [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Pour plus d’informations, vous pouvez toujours taper ```get-help``` et puis la commande PowerShell vous voulez de l’aide.

## <a name="create-alert-rules-in-powershell"></a>Créer des règles d’alerte dans PowerShell

1. Ouvrez une session Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```

2. Obtenir la liste des abonnements dont vous disposez. Vérifiez que vous travaillez avec l’abonnement. Dans le cas contraire, la valeur à la droite à l’aide de la sortie de `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

3.  Pour répertorier les règles sur un groupe de ressources existants, utilisez la commande suivante :

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

4. Pour créer une règle, vous devez avoir plusieurs informations importants tout d’abord. 
    - L' **ID de ressource** pour la ressource que vous souhaitez définir une alerte pour
    - Les **définitions de mesure** disponibles pour cette ressource

    La première pour obtenir l’ID de ressource consiste à utiliser le portail Azure. En supposant que la ressource est déjà créée, sélectionnez-le dans le portail. Puis, dans la lame suivante, sélectionnez *Propriétés* dans la section *paramètres* . L’ID de ressource est un champ de la lame suivant. Une autre méthode consiste à utiliser l' [Explorateur de ressources Azure](https://resources.azure.com/).

    Un id de ressource d’exemple pour une application web est

    ```
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Vous pouvez utiliser `Get-AzureRmMetricDefinition` pour afficher la liste de toutes les définitions de mesure d’une ressource spécifique.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id>
    ```

    L’exemple suivant génère une table avec la nom de la mesure et l’unité de cette mesure.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

    ```
    Une liste complète des options disponibles pour Get-AzureRmMetricDefinition est disponible en exécutant Get-MetricDefinitions.


5. L’exemple suivant configure une alerte sur une ressource de site web. Les déclencheurs d’alerte chaque fois qu’il reçoit constamment le trafic de 5 minutes, et à nouveau lorsqu’il ne reçoit aucun trafic pendant 5 minutes.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```

6. Pour créer des webhook ou envoyer de courrier électronique lorsqu’une alerte se déclenche, créez d’abord le courrier électronique et/ou un webhooks. Puis créer immédiatement la règle par la suite avec-balise Actions, comme illustré dans l’exemple suivant. Vous ne pouvez pas associer webhook ou des courriers électroniques avec déjà créé les règles via PowerShell.


    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


7. Pour créer une alerte qui se déclenche sur une condition spécifique dans le journal d’activité, utilisez les commandes sous la forme suivante

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```

    -OperationName correspond à un type d’événement dans le journal d’activité. *Microsoft.Compute/virtualMachines/delete* et *microsoft.insights/diagnosticSettings/write*sont des exemples.

    Vous pouvez utiliser la commande PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) pour obtenir une liste des operationNames possible. Alternativement, vous pouvez utiliser le portail Azure et spécifiques au-delà des opérations que vous souhaitez créer une alerte pour interroger le journal d’activité. Les opérations apparaissent dans la vue graphique journal des noms conviviaux. Recherchez dans le JSON pour l’entrée et extraire la valeur NomOpération.   

8. Vérifiez que vos alertes ont été créées correctement en consultant les règles individuelles.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

9. Supprimer vos alertes. Ces commandes suppriment les règles créées précédemment dans cet article.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir une vue d’ensemble de la surveillance Azure](monitoring-overview.md) y compris les types d’informations, vous pouvez collecter et analyser.
* Pour en savoir plus sur la [configuration de webhooks dans les alertes](insights-webhooks-alerts.md).
* Pour en savoir plus sur les [Procédures opérationnelles de Automation Azure](..\automation\automation-starting-a-runbook.md).
* Obtenir une [vue d’ensemble de la collecte des journaux de diagnostics](monitoring-overview-of-diagnostic-logs.md) pour collecter les mesures détaillées de haute fréquence sur votre service.
* Obtenez une [vue d’ensemble de la collection de mesures](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et la réactivité.
