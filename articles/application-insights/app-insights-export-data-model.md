<properties 
    pageTitle="Modèle de données d’informations d’application" 
    description="Décrit les propriétés exportées à partir de l’exportation en continu au format JSON et utilisé en tant que filtres." 
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
    ms.date="03/21/2016" 
    ms.author="awills"/>

# <a name="application-insights-export-data-model"></a>Modèle de données d’application Insights exportation

Ce tableau répertorie les propriétés de télémétrie envoyé dans les kits de développement [d’Idées d’Application](app-insights-overview.md) sur le portail. Vous verrez ces propriétés dans la sortie de données à partir [d’Exporter Continuous](app-insights-export-telemetry.md).
Ils apparaissent également dans les filtres de propriétés dans [l’Explorateur de mesure](app-insights-metrics-explorer.md) et de la [Recherche de Diagnostic](app-insights-diagnostic-search.md).

Points à noter :

* `[0]`dans ces tableaux désigne un point dans le chemin d’accès où vous devez insérer un index ; mais il n’est pas toujours 0.
* Durées de temps sont dans des dizaines de microsecondes, donc 10000000 == 1 seconde.
* Dates et heures sont UTC et figurent dans le format ISO`yyyy-MM-DDThh:mm:ss.sssZ`

Il existe plusieurs [exemples](app-insights-export-telemetry.md#code-samples) qui illustrent comment les utiliser.



## <a name="example"></a>Exemple

    // A server report about an HTTP request
    {
    "request": [ 
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": "" 
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events 
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0", 
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0, 
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }




## <a name="context"></a>Contexte

Tous les types de télémétrie sont accompagnés d’une section de contexte. Tous ces champs sont transmises à chaque point de données.



|Chemin d’accès|Type de|Notes|
|---|---|---|
| Context.Custom.dimensions [0]  | [] d’objet  | Paires de chaînes clé-valeur définies par le paramètre de propriétés personnalisées. Longueur de clé maximale 100, les valeurs de longueur max 1024. Plus de 100 valeurs uniques, la propriété peut être l’objet d’une recherche, mais ne peut pas être utilisée pour la segmentation. Clés de max 200 par ikey.  |
| Context.Custom.Metrics [0]  | [] d’objet  | Les paires clé-valeur définie par le paramètre de mesures personnalisées et TrackMetrics. Longueur max. 100, les valeurs peuvent être numériques. |
| context.data.eventTime | chaîne | HEURE UTC |
| context.data.isSynthetic | valeur booléenne | Demande semble provenir d’un test web ou des robots. |
| context.data.samplingRate | numéro de | Pourcentage de télémétrie généré par le Kit de développement qui est envoyé au portail. Plage de 0,0-100.0.|
| Context.Device | objet | DISPOSITIF client |
| Context.Device.Browser | chaîne | Internet Explorer, Chrome... |
| context.device.browserVersion | chaîne | Chrome 48.0... |
| context.device.deviceModel | chaîne | |
| context.device.deviceName | chaîne | |
| Context.Device.ID | chaîne | |
| Context.Device.locale | chaîne | en-GB, de-DE... |
| Context.Device.Network | chaîne | |
| context.device.oemName | chaîne | |
| context.device.osVersion | chaîne | Système d’exploitation hôte |
| context.device.roleInstance | chaîne | ID de l’hôte du serveur |
| context.device.roleName | chaîne | |
| Context.Device.type | chaîne | PC, navigateur... |
| Context.Location | objet | Dérivé de clientip. |
| Context.Location.City | chaîne | Dérivé de clientip, si connu  |
| Context.Location.ClientIP | chaîne | Dernier octogone est rendu anonyme à 0. |
| Context.Location.continent | chaîne | |
| Context.Location.Country | chaîne | |
| Context.Location.province | chaîne | État ou province |
| Context.Operation.ID | chaîne | Les éléments qui ont le même id d’opération sont affichés sous la forme d’éléments associés dans le portail. Généralement l’id de la demande. |
| Context.Operation.Name | chaîne | nom d’URL ou de la demande |
| context.operation.parentId | chaîne | Permet les éléments imbriqués associés. |
| Context.session.ID | chaîne | ID d’un groupe d’opérations à partir de la même source. Une période de 30 minutes sans opération signale la fin d’une session. |
| context.session.isFirst | valeur booléenne | |
| context.user.accountAcquisitionDate | chaîne | |
| context.user.anonAcquisitionDate | chaîne | |
| context.user.anonId | chaîne | |
| context.user.authAcquisitionDate | chaîne | [Utilisateur authentifié](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated | valeur booléenne | |
| internal.data.documentVersion | chaîne | |
| Internal.Data.ID | chaîne | |



## <a name="events"></a>Événements

Événements personnalisés générés par [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event). 


|Chemin d’accès|Type de|Notes|
|---|---|---|
| nombre d’événements [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple 4 =&gt; 25 %. |
| nom de l’événement [0] | chaîne | Nom de l’événement.  Longueur maximale de 250. |
| url d’événement [0] | chaîne | |
| urlData.base d’événement [0] | chaîne | |
| urlData.host d’événement [0] | chaîne | |

## <a name="exceptions"></a>Exceptions

Rapports des [exceptions](app-insights-asp-net-exceptions.md) sur le serveur et dans le navigateur. 


|Chemin d’accès|Type de|Notes|
|---|---|---|
| assemblage de basicException [0] | chaîne | |
| nombre de basicException [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple 4 =&gt; 25 %. |
| exceptionGroup de basicException [0] | chaîne | |
| exceptionType de basicException [0] | chaîne | |chaîne | |
| failedUserCodeMethod de basicException [0] | chaîne | |
| failedUserCodeAssembly de basicException [0] | chaîne | |
| handledAt de basicException [0] | chaîne | |
| hasFullStack de basicException [0] | valeur booléenne | |
| id de basicException [0] | chaîne | |
| méthode de basicException [0] | chaîne | |
| message de basicException [0] | chaîne | Message d’exception. Longueur maximale de 10k.|
| outerExceptionMessage de basicException [0] | chaîne | |
| outerExceptionThrownAtAssembly de basicException [0] | chaîne | |
| outerExceptionThrownAtMethod de basicException [0] | chaîne | |
| outerExceptionType de basicException [0] | chaîne | |
| outerId de basicException [0] | chaîne | |
| assemblage de parsedStack [0] basicException [0] | chaîne | |
| nom de fichier basicException [0] parsedStack [0] | chaîne | |
| niveau de parsedStack [0] basicException [0] | nombre entier | |
| ligne de parsedStack [0] basicException [0] | nombre entier | |
| méthode de parsedStack [0] basicException [0] | chaîne | |
| pile de basicException [0] | chaîne | Longueur maximale de 10k|
| typeName de basicException [0] | chaîne | |



## <a name="trace-messages"></a>Messages de trace

Envoyé par [TrackTrace](app-insights-api-custom-events-metrics.md#track-trace)et par l' [enregistrement des cartes](app-insights-asp-net-trace-logs.md).


|Chemin d’accès|Type de|Notes|
|---|---|---|
| loggerName de message [0] | chaîne ||
| paramètres de message [0] | chaîne ||
| message [0] brut | chaîne | Le message du journal, longueur max. 10k. |
| severityLevel de message [0] | chaîne | |



## <a name="remote-dependency"></a>Dépendance à distance

Envoyé par TrackDependency. Utilisé pour les performances des rapports et l’utilisation d' [appels de dépendances](app-insights-asp-net-dependencies.md) dans le serveur, et AJAX appelle dans le navigateur.

|Chemin d’accès|Type de|Notes|
|---|---|---|
| remoteDependency [0] async | valeur booléenne | |
| nom de base de remoteDependency [0] | chaîne |  |
| remoteDependency [0] commandName | chaîne | Par exemple, « accueil/index » |
| nombre de remoteDependency [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple 4 =&gt; 25 %. |
| dependencyTypeName de remoteDependency [0] | chaîne | HTTP, SQL... |
| durationMetric.value de remoteDependency [0] | numéro de | Heure à partir de l’appel à la fin de la réponse à la dépendance |
| id de remoteDependency [0] | chaîne | |
| nom de remoteDependency [0] | chaîne | URL. Longueur maximale de 250.|
| remoteDependency [0] resultCode | chaîne | à partir de la dépendance HTTP |
| réussite de la remoteDependency [0] | valeur booléenne | |
| type de remoteDependency [0] | chaîne | HTTP, Sql... |
| url de la remoteDependency [0] | chaîne |  Longueur maximale de 2000 |
| urlData.base de remoteDependency [0] | chaîne | Longueur maximale de 2000 |
| urlData.hashTag de remoteDependency [0] | chaîne | |
| urlData.host de remoteDependency [0] | chaîne | Longueur maximale de 200 |


## <a name="requests"></a>Demandes

Envoyé par [TrackRequest](app-insights-api-custom-events-metrics.md#track-request). Les modules standard permet de temps de réponse du serveur de rapports, mesurée sur le serveur. 


|Chemin d’accès|Type de|Notes|
|---|---|---|
| nombre de demande [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple : 4 =&gt; 25 %. |
| durationMetric.value de demande [0] | numéro de | Heure de la demande qui arrivent à la réponse. 1e7 == 1 s |
| id de la demande [0] | chaîne | Id d’opération |
| nom de la demande [0] | chaîne | GET/POST + url de base.  Longueur maximale de 250 |
| demande responseCode [0] | nombre entier | Réponse HTTP envoyé au client |
| succès de la demande [0] | valeur booléenne | Par défaut == (responseCode &lt; 400) |
| url de la demande [0] | chaîne | Ne pas y compris hôte |
| urlData.base de demande [0] | chaîne | |
| urlData.hashTag de demande [0] | chaîne |  |
| urlData.host de demande [0] | chaîne | |


## <a name="page-view-performance"></a>Performances de l’affichage de la page

Envoyé par le navigateur. Mesure la durée de traitement d’une page, à partir de l’utilisateur à l’origine de la demande d’affichage complet (à l’exclusion des appels d’AJAX asynchrone).

Affichent les valeurs de contexte client du système d’exploitation et la version du navigateur. 


|Chemin d’accès|Type de|Notes|
|---|---|---|
| clientProcess.value de clientPerformance [0] | nombre entier | Heure de fin de réception le code HTML pour l’affichage de la page. |
| nom de clientPerformance [0] | chaîne | |
| networkConnection.value de clientPerformance [0] | nombre entier | Temps nécessaire pour établir une connexion réseau. |
| receiveRequest.value de clientPerformance [0] | nombre entier | Heure de fin de l’envoi de la demande à la réception le code HTML dans la réponse. |
| sendRequest.value de clientPerformance [0] | nombre entier | À partir du temps nécessaire pour envoyer la demande HTTP. |
| total.value de clientPerformance [0] | nombre entier | Heure de début envoyer la demande à l’affichage de la page. |
| url de la clientPerformance [0] | chaîne | URL de cette demande. |
| urlData.base de clientPerformance [0] | chaîne | |
| urlData.hashTag de clientPerformance [0] | chaîne | |
| urlData.host de clientPerformance [0] | chaîne | |
| urlData.protocol de clientPerformance [0] | chaîne | |

## <a name="page-views"></a>Affichages de page

Envoyé par trackPageView() ou [stopTrackPage](app-insights-api-custom-events-metrics.md#page-view)

|Chemin d’accès|Type de|Notes|
|---|---|---|
| nombre de vues [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple 4 =&gt; 25 %. |
| Afficher durationMetric.value [0] | nombre entier | Valeur éventuellement définie dans trackPageView() ou en startTrackPage() - stopTrackPage(). Pas la même que les valeurs de clientPerformance. |
| nom de la vue [0] | chaîne | Titre de la page.  Longueur maximale de 250 |
| url d’affichage [0] | chaîne | |
| Afficher urlData.base [0] | chaîne | |
| Afficher urlData.hashTag [0] | chaîne | |
| Afficher urlData.host [0] | chaîne | |



## <a name="availability"></a>Disponibilité

Rapports de [disponibilité des tests web](app-insights-monitor-web-app-availability.md).

|Chemin d’accès|Type de|Notes|
|---|---|---|
| disponibilité [0] availabilityMetric.name | chaîne | disponibilité |
| disponibilité [0] availabilityMetric.value | numéro de |1.0 ou 0.0 |
| nombre de disponibilité [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple 4 =&gt; 25 %. |
| disponibilité [0] dataSizeMetric.name | chaîne | |
| disponibilité [0] dataSizeMetric.value | nombre entier | |
| disponibilité [0] durationMetric.name | chaîne | |
| disponibilité [0] durationMetric.value | numéro de | Durée du test. 1e7 == 1 s |
| message de disponibilité [0] | chaîne | Échec de diagnostic |
| résultat de disponibilité [0] | chaîne | Réussite/échec |
| disponibilité [0] runLocation | chaîne | Geo source http req |
| nomtest de disponibilité [0] | chaîne | |
| disponibilité [0] testRunId | chaîne | |
| disponibilité [0] testTimestamp | chaîne | |




## <a name="metrics"></a>Mesures

Généré par TrackMetric().

La valeur métrique est trouvée dans context.custom.metrics[0]

Par exemple :

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>À propos des valeurs métriques

Les valeurs métriques, à la fois dans les rapports de métriques et ailleurs, sont déclarés avec une structure d’objet standard. Par exemple :

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Actuellement : bien que cela peut changer dans le futur - toutes les valeurs signalées par les modules de kit de développement logiciel standard, `count==1` et seulement la `name` et `value` les champs sont utiles. Le seul cas où ils seraient différentes serait si vous écrivez vos propres appels de TrackMetric dans lequel vous définissez les autres paramètres. 

L’objectif des autres champs est afin que les mesures à agréger dans le Kit de développement pour réduire le trafic sur le portail. Par exemple, vous pourriez moyenne plusieurs lectures successives avant d’envoyer chaque rapport métrique. Puis calculer la min, max, écart type et valeur d’agrégation (somme ou moyenne) et définir le nombre et le nombre de lectures, représenté par le rapport. 

Dans les tableaux ci-dessus, nous avons omis le rarement des champs count, min, max, stdDev et la sampledValue.

Au lieu d’agrégation des mesures, vous pouvez utiliser [l’échantillonnage](app-insights-sampling.md) si vous avez besoin réduire le volume de télémétrie.


### <a name="durations"></a>Durées

Sauf mention contraire, les durées sont exprimées en dizaines de microsecondes, afin que les 10 000 000,0 signifie 1 seconde.



## <a name="see-also"></a>Voir aussi

* [Aperçu de l’application](app-insights-overview.md) 
* [Exportation en continu](app-insights-export-telemetry.md)
* [Exemples de code](app-insights-export-telemetry.md#code-samples)


