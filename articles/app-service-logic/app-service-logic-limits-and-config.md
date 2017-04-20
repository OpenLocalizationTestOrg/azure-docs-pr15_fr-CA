<properties
    pageTitle="Limites d’application logique et configuration | Microsoft Azure"
    description="Vue d’ensemble des limites de service et des valeurs de configuration disponibles pour les applications de la logique."
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="logic-app-limits-and-configuration"></a>Limites d’application logique et de la configuration

Vous trouverez ci-dessous des informations sur les limites actuelles et les détails de configuration pour les applications de logique d’Azure.

## <a name="limits"></a>Limites

### <a name="http-request-limits"></a>Limites des demandes HTTP

Il s’agit de limites pour un seul appel connecteur et/ou la demande HTTP

#### <a name="timeout"></a>Délai d’attente

|Nom|Limite de|Notes|
|----|----|----|
|Délai d’expiration de la demande|1 minute|Un [modèle asynchrone](app-service-logic-create-api-app.md) ou [jusqu'à ce que la boucle](app-service-logic-loops-and-scopes.md) peut compenser en fonction des besoins|

#### <a name="message-size"></a>Taille des messages

|Nom|Limite de|Notes|
|----|----|----|
|Taille des messages|50 MO|Certains liens et une API peut prend pas en charge les 50 Mo.  Déclencheur de demande prend en charge jusqu'à 25 Mo|
|Limite de d’évaluation d’expression|131 072 caractères|`@concat()`, `@base64()`, `string` ne peut pas être plus longue|

#### <a name="retry-policy"></a>Nouvelle tentative de stratégie

|Nom|Limite de|Notes|
|----|----|----|
|Nouvelles tentatives|4|Pouvez configurer avec la [nouvelle tentative du paramètre de stratégie](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Délai maximal de tentatives|1 heure|Pouvez configurer avec la [nouvelle tentative du paramètre de stratégie](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Min délai entre reprises|20 min|Pouvez configurer avec la [nouvelle tentative du paramètre de stratégie](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Rétention et la durée d’exécution

Voici les limites d’une application logique unique exécuté.

|Nom|Limite de|Notes|
|----|----|----|
|Durée d’exécution|90 jours||
|Rétention du stockage|90 jours|Il s’agit de l’heure de début d’exécution|
|Intervalle de périodicité min|s 15||
|Intervalle de périodicité max|500 jours||


### <a name="looping-and-debatching-limits"></a>Bouclage et limites de debatching

Il s’agit des limites pour une application de logique unique fonctionnent.

|Nom|Limite de|Notes|
|----|----|----|
|Éléments de ForEach|5 000|Vous pouvez utiliser l' [action de requête](../connectors/connectors-native-query.md) pour filtrer des baies plus grandes selon les besoins|
|Jusqu'à ce que les itérations|10 000||
|Éléments de SplitOn|5 000||
|Parallélisme de ForEach|20|Vous pouvez définir pour une instruction foreach séquentielle en ajoutant `"operationOptions": "Sequential"` à la `foreach` action|


### <a name="throughput-limits"></a>Limites du débit

Il s’agit des limites pour une instance d’application logique unique. 

|Nom|Limite de|Notes|
|----|----|----|
|Déclencheurs par seconde|100|Pouvez distribuer les flux de travail entre plusieurs applications en fonction des besoins|

### <a name="definition-limits"></a>Limites de définition

Il s’agit des limites pour une définition d’application logique unique.

|Nom|Limite de|Notes|
|----|----|----|
|Actions de ForEach|1|Vous pouvez ajouter des workflows imbriquées pour développer si nécessaire|
|Actions par le flux de travail|60|Vous pouvez ajouter des workflows imbriquées pour développer si nécessaire|
|Profondeur d’imbrication d’action d’autorisé|5|Vous pouvez ajouter des workflows imbriquées pour développer si nécessaire|
|Flux par région par abonnement|1000||
|Déclencheurs par flux de travail|10||
|Caractères max par expression|8 192||
|Max `trackedProperties` taille de caractères|16 000|
|`action`/`trigger`limite de nom|80||
|`description`limite de longueur|256||
|`parameters`limite de|50||
|`outputs`limite de|10||

## <a name="configuration"></a>Configuration de

### <a name="ip-address"></a>Adresse IP

Appels effectués à partir d’un [connecteur](../connectors/apis-list.md) proviendra de l’adresse IP spécifiée ci-dessous.

Appels effectués à partir d’une application de logique directement (c'est-à-dire, via [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) peuvent provenir d’un des [Plages d’IP Azure Datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

|Région d’application logique|IP sortants|
|-----|----|
|Est de l’Australie|40.126.251.213|
|Sud-est de l’Australie|40.127.80.34|
|Sud du Brésil|191.232.38.129|
|Central de l’Inde|104.211.98.164|
|États-Unis centre|40.122.49.51|
|Asie de l’est|23.99.116.181|
|Les États-Unis|191.237.41.52|
|Les États-Unis 2|104.208.233.100|
|Nord-est du Japon|40.115.186.96|
|Ouest du Japon|40.74.130.77|
|États-Unis centre nord|65.52.218.230|
|Europe du Nord|104.45.93.9|
|États-Unis centre sud|104.214.70.191|
|Asie du Sud-est|13.76.231.68|
|Sud-est de l’Inde|104.211.227.225|
|Europe de l’ouest|40.115.50.13|
|Ouest de l’Inde|104.211.161.203|
|États-Unis Ouest|104.40.51.248|


## <a name="next-steps"></a>Étapes suivantes  

- Pour vous familiariser avec les applications de logique, suivez le didacticiel de [créer une application de logique](app-service-logic-create-a-logic-app.md) .  
- [Afficher des exemples et des scénarios courants](app-service-logic-examples-and-scenarios.md)
- [Vous pouvez automatiser des processus d’entreprise avec les applications de logique](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Apprenez à intégrer vos systèmes avec les applications de logique](http://channel9.msdn.com/Events/Build/2016/P462)