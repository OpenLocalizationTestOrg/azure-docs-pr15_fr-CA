<properties
    pageTitle="L’Interface de ligne de commande multiplateforme (CLI) permet de créer des alertes pour les services Azure | Microsoft Azure"
    description="Utilisez l’interface de ligne de commande pour créer des alertes Azure, qui peuvent déclencher des notifications ou automation lorsque les conditions spécifiées sont remplies."
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
    ms.date="10/24/2016"
    ms.author="robb"/>

# <a name="use-the-cross-platform-command-line-interface-cli-to-create-alerts-for-azure-services"></a>L’Interface de ligne de commande multiplateforme (CLI) permet de créer des alertes pour les services Azure

> [AZURE.SELECTOR]
- [Portail](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Vue d’ensemble

Cet article vous explique comment configurer les alertes Azure à l’aide de l’Interface de ligne de commande (CLI).

>[AZURE.NOTE] Moniteur Azure est le nouveau nom de ce qui fut appelé « Perspectives Azure » jusqu'à ce que le 25 septembre 2016. Cependant, les espaces de noms et, par conséquent, les commandes ci-dessous contiennent encore les « perspectives ».

Vous pouvez recevoir une alerte basée sur des mesures de surveillance pour ou d’événements sur vos services Azure.

- **Valeurs métriques** - l’alert se déclenche lorsque la valeur d’une mesure spécifiée dépasse un seuil que vous affectez dans les deux sens. En d’autres termes, il déclenche à la fois lorsque la condition est remplie tout d’abord, et ensuite par la suite que lorsque la condition est n’est plus remplie.    
- **Événements de journal d’activité** - une alerte peut déclencher sur *chaque* événement, et uniquement lorsqu’un certain nombre d’événements se produire.

Vous pouvez configurer une alerte pour effectuer les opérations suivantes lorsqu’il déclenche :

- envoyer des notifications par courrier électronique à l’administrateur de service et des administrateurs
- envoyer les courriels supplémentaires que vous spécifiez.
- appeler un webhook
- lancer l’exécution d’une procédure Azure opérationnelle (uniquement à partir du portail Azure pour l’instant)

Vous pouvez configurer et obtenir des informations sur les règles d’alerte à l’aide de

- [Azure portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de ligne de commande (CLI)](insights-alerts-command-line-interface.md)
- [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Vous pouvez toujours recevoir aide pour les commandes en tapant une commande et la mise - aide à la fin. Par exemple :

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Créer des règles d’alerte à l’aide de l’interface CLI

1. Exécuter les composants requis et les connecter à Azure. Consultez les [exemples de la CLI de moniteur Azure](insights-cli-samples.md). En résumé, installez l’interface CLI et exécuter ces commandes. Ils vous aider connecté, afficher les abonnements que vous utilisez et vous préparez à exécuter des commandes du moniteur d’Azure.


    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2.  Pour répertorier les règles sur un groupe de ressources existants, utilisez formulaire suivant **azure perspectives sur la liste de règles alertes** *[options] &lt;resourceGroup&gt; *

    ```console
    azure insights alerts rule list myresourcegroupname

    ```
3. Pour créer une règle, vous devez avoir plusieurs informations importants tout d’abord.
    - L' **ID de ressource** pour la ressource que vous souhaitez définir une alerte pour
    - Les **définitions de mesure** disponibles pour cette ressource

    La première pour obtenir l’ID de ressource consiste à utiliser le portail Azure. En supposant que la ressource est déjà créée, sélectionnez-le dans le portail. Puis, dans la lame suivante, sélectionnez *Propriétés* dans la section *paramètres* . L' *ID de ressource* est un champ de la lame suivant. Une autre méthode consiste à utiliser l' [Explorateur de ressources Azure](https://resources.azure.com/).

    Un id de ressource d’exemple pour une application web est

    ```console
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Pour obtenir une liste des unités statistiques disponibles pour ces mesures pour l’exemple précédent de la ressource, utilisez la commande CLI suivante :  

    ```console
    azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
    ```

    _PT1M_ est la granularité de la mesure disponible (intervalles de 1 minute). À l’aide de plusieurs granularités vous offre différentes options métriques.


4. Pour créer une règle d’alerte basée sur une mesure, utilisez une commande sous la forme suivante :

    **métrique de règle insights Azure alertes définies** *[options] &lt;NomRègle&gt; &lt;emplacement&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;opérateur&gt; &lt;seuil&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt; *

    L’exemple suivant configure une alerte sur une ressource de site web. Les déclencheurs d’alerte chaque fois qu’il reçoit constamment le trafic de 5 minutes, et à nouveau lorsqu’il ne reçoit aucun trafic pendant 5 minutes.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```

5. Pour créer des webhook ou envoyer de courrier électronique lorsqu’une alerte se déclenche, créez d’abord le courrier électronique et/ou un webhooks. Puis créez la règle immédiatement par la suite. Vous ne pouvez pas associer webhook ou des courriers électroniques avec déjà créé des règles à l’aide de l’interface CLI.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```


6. Pour créer une alerte qui se déclenche à une condition spécifique dans le journal d’activité, utilisez le formulaire :

    **règle d’alertes Insights connecter ensemble** *[options] &lt;NomRègle&gt; &lt;emplacement&gt; &lt;resourceGroup&gt; &lt;NomOpération&gt; *

    Par exemple

    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```

    La NomOpération correspond à un type d’événement pour une entrée dans le journal d’activité. *Microsoft.Compute/virtualMachines/delete* et *microsoft.insights/diagnosticSettings/write*sont des exemples.

    Vous pouvez utiliser la commande PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) pour obtenir une liste des operationNames possible. Alternativement, vous pouvez utiliser le portail Azure et spécifiques au-delà des opérations que vous souhaitez créer une alerte pour interroger le journal d’activité. Les opérations apparaissent dans la vue graphique journal des noms conviviaux. Recherchez dans le JSON pour l’entrée et extraire la valeur NomOpération.   

7. Vous pouvez vérifier que vos alertes ont été créées correctement en consultant une règle individuelle.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```

8. Pour supprimer des règles, utilisez une commande de l’écran :

    **Supprimer de la règle d’alertes de perspectives** [options] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Ces commandes suppriment les règles créées précédemment dans cet article.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```



## <a name="next-steps"></a>Étapes suivantes

* [Obtenir une vue d’ensemble de la surveillance Azure](monitoring-overview.md) y compris les types d’informations, vous pouvez collecter et analyser.
* Pour en savoir plus sur la [configuration de webhooks dans les alertes](insights-webhooks-alerts.md).
* Pour en savoir plus sur les [Procédures opérationnelles de Automation Azure](..\automation\automation-starting-a-runbook.md).
* Obtenir une [vue d’ensemble de la collecte des journaux de diagnostics](monitoring-overview-of-diagnostic-logs.md) pour collecter les mesures détaillées de haute fréquence sur votre service.
* Obtenez une [vue d’ensemble de la collection de mesures](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et la réactivité.
