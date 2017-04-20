<properties
    pageTitle="Utiliser Powershell pour définir des alertes dans les conseils d’Application"
    description="Automatiser la configuration des perspectives d’Application pour obtenir des courriels sur les modifications de métriques."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/19/2016"
    ms.author="awills"/>

# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Utiliser PowerShell pour définir des alertes dans les conseils d’Application

Vous pouvez automatiser la configuration des [alertes](app-insights-alerts.md) dans les [Conseils d’Application Visual Studio](app-insights-overview.md).

En outre, vous pouvez [définir webhooks pour automatiser votre réponse à une alerte](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="one-time-setup"></a>Programme d’installation unique

Si vous n’avez pas utilisé de PowerShell avec votre abonnement Azure avant :

Installez le module Azure Powershell sur l’ordinateur où vous souhaitez exécuter des scripts.

 * Installation de [Microsoft Web Platform Installer (v5 ou ultérieure)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Utilisez-le pour installer Microsoft Azure Powershell


## <a name="connect-to-azure"></a>Se connecter à Azure

Démarrer PowerShell d’Azure et [se connecter à votre abonnement](../powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## <a name="get-alerts"></a>Recevoir des alertes

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Ajout d’alerte


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



## <a name="example-1"></a>Exemple 1

M’envoyer un courriel si la réponse du serveur pour les requêtes HTTP, plus de 5 minutes, la moyenne est inférieure à 1 seconde. Mes idées d’Application est appelée IceCreamWebApp et qu’il est dans le groupe de ressources Fabrikam. Je suis le propriétaire de l’abonnement Azure.

Le GUID est l’ID d’abonnement (pas la clé d’instrumentation de l’application).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Exemple 2

Je possède une application dans laquelle j’utilise [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) pour signaler une mesure nommée « salesPerHour ». Envoyer qu'un courrier électronique à Mes collègues si « salesPerHour » est inférieur à 100, la moyenne sur 24 heures.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

La même règle peut servir pour la mesure signalée en utilisant le [paramètre de mesure](app-insights-api-custom-events-metrics.md#properties) de suivi d’un autre appel de TrackEvent ou trackPageView.

## <a name="metric-names"></a>Noms de métriques

Nom de mesure | Nom de l’écran | Description
---|---|---
`basicExceptionBrowser.count`|Exceptions du navigateur|Nombre d’exceptions non interceptées levée dans le navigateur.
`basicExceptionServer.count`|Exceptions du serveur|Nombre d’exceptions non gérées levées par l’application
`clientPerformance.clientProcess.value`|Temps de traitement client|Temps écoulé entre la réception du dernier octet d’un document jusqu'à ce que le modèle DOM est chargé. Les demandes asynchrones peuvent être encore en cours.
`clientPerformance.networkConnection.value`|Temps de connexion de réseau de chargement de page| Durée du navigateur pour vous connecter au réseau. Est 0 si la mise en cache.
`clientPerformance.receiveRequest.value`|Réception des temps de réponse| Temps écoulé entre le navigateur envoyant la demande au début de la réception de réponse.
`clientPerformance.sendRequest.value`|Envoyer la durée de la demande| Durée par navigateur pour envoyer la demande.
`clientPerformance.total.value`|Temps de chargement de page de navigateur|Heure à partir de la demande de l’utilisateur jusqu'à ce que le DOM, les feuilles de style, les scripts et les images sont chargées.
`performanceCounter.available_bytes.value`|Mémoire disponible|Mémoire physique immédiatement disponible pour un processus ou pour une utilisation du système.
`performanceCounter.io_data_bytes_per_sec.value`|Débit d’e/s de processus|Nombre total d’octets par seconde de lecture et l’écriture de fichiers, réseau et périphériques.
`performanceCounter.number_of_exceps_thrown_per_sec`|taux d’exception|Exceptions levées par seconde.
`performanceCounter.percentage_processor_time.value`|Processeur de processus|Le pourcentage de temps écoulé de toutes les threads du processus utilisé par le processeur à exécuter les instructions pour le processus d’applications.
`performanceCounter.percentage_processor_total.value`|Temps processeur|Le pourcentage de temps que le processeur passe dans des threads non inactifs.
`performanceCounter.process_private_bytes.value`|Octets privés du processus|Mémoire exclusivement affectée au processus de l’application surveillée.
`performanceCounter.request_execution_time.value`|Temps d’exécution requête ASP.NET|Durée d’exécution de la requête la plus récente.
`performanceCounter.requests_in_application_queue.value`|Demandes ASP.NET dans la file d’attente de l’exécution|Longueur de la file d’attente de la demande application.
`performanceCounter.requests_per_sec`|Taux de demandes ASP.NET|Taux de toutes les demandes à l’application par seconde à partir de ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Échecs de la dépendance|Nombre d’appels ayant échouées effectuées par l’application serveur à des ressources externes.
`request.duration`|Temps de réponse de serveur|Délai entre la réception d’une demande HTTP et de fin d’envoi de la réponse.
`request.rate`|Taux de demandes|Taux de toutes les demandes à l’application par seconde.
`requestFailed.count`|Demandes ayant échoué|Demandes nombre de HTTP qui ont abouti à un code de réponse > = 400
`view.count`|Affichages de page|Nombre de demandes client de l’utilisateur pour une page web. Le trafic synthétique est filtré.
{votre nom métrique personnalisée}|{Votre nom métrique}|Votre valeur métrique signalée par [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) ou dans le [paramètre de mesures d’un appel de suivi](app-insights-api-custom-events-metrics.md#properties).

Les mesures sont envoyés par des modules de télémétrie différents :

Groupe de mesure | Module du collecteur
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>affichage | [JavaScript du navigateur](app-insights-javascript.md)
performanceCounter | [Performances](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dépendance](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
demande,<br/>requestFailed|[Demande de serveur](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)

## <a name="webhooks"></a>Webhooks

Vous pouvez [automatiser votre réponse à une alerte](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure appellera une adresse web de votre choix lorsqu’une alerte est déclenchée.

## <a name="see-also"></a>Voir aussi


* [Script pour configurer les informations d’Application](app-insights-powershell-script-create-resource.md)
* [Créer des perspectives d’Application et les ressources de test web à partir de modèles](app-insights-powershell.md)
* [Automatiser Microsoft Azure Diagnostics de couplage d’idées d’Application](app-insights-powershell-azure-diagnostics.md)
* [Automatiser votre réponse à une alerte](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
