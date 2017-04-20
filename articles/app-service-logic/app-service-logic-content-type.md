<properties
   pageTitle="Gestion du type de contenu à logique applications | Microsoft Azure"
   description="Comprendre la logique d’applications traite les types de contenu au moment de l’exécution et de conception"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-content-type-handling"></a>Gestion du Type de contenu des applications logique

Il existe de nombreux types de contenu qui peuvent être transmis via une application de logique - y compris les données binaires, fichiers plats, JSON et XML.  Alors que tous les types de contenu sont pris en charge, certaines sont comprises en mode natif par le moteur d’applications logique, et d’autres peuvent nécessiter de cast ou conversion en fonction des besoins.  L’article suivant décrit comment le moteur gère les différents types de contenu, et comment ils peuvent être correctement traitées en fonction des besoins.

## <a name="content-type-header"></a>En-tête Content-Type

Pour démarrer simple, examinons les deux `Content-Types` qui ne nécessitent pas une conversion d’utilisation au sein d’une application logique - `application/json` et `text/plain`.

### <a name="applicationjson"></a>Application/json.

Le moteur de flux de travail repose sur le `Content-Type` appelle d’en-tête HTTP pour déterminer le traitement approprié.  Toute demande avec le type de contenu `application/json` seront stockées et gérées en tant qu’objet JSON.  En outre, le contenu JSON peut être analysée par défaut sans avoir besoin de conversion.  Par conséquent, une demande qui a l’en-tête content-type `application/json ` comme suit :

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

peut être analysée dans un flux de travail avec une expression comme `@body('myAction')['foo'][0]` pour obtenir une valeur (dans ce cas, `bar`).  Aucune conversion supplémentaire n’est nécessaire.  Si vous travaillez avec des données sont JSON, mais n’a pas un en-tête spécifié, vous pouvez le convertir manuellement à l’aide de JSON le `@json()` fonction (par exemple : `@json(triggerBody())['foo']`).

### <a name="textplain"></a>Texte/brut

Semblable à `application/json`, les messages HTTP envoyés avec la `Content-Type` en-tête de `text/plain` seront stockées dans sa forme brute.  En outre, si inclus dans des actions suivantes sans conversion la demande passera avec un `Content-Type`: `text/plain` en-tête.  Par exemple, si vous travaillez avec un fichier plat, vous pouvez recevoir le contenu HTTP suivant :

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

as `text/plain`.  Si dans l’action suivante vous l’avez envoyée dans le corps d’une autre demande (`@body('flatfile')`), la requête aurait un `text/plain` en-tête Content-Type.  Si vous travaillez avec des données sont le texte brut, mais n’a pas un en-tête spécifié, vous pouvez le convertir manuellement à l’aide de texte le `@string()` fonction (par exemple : `@string(triggerBody())`)

### <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml et Application/octet-stream et fonctions du convertisseur

Le moteur d’application logique conserve toujours la `Content-Type` qui a été reçu sur la demande HTTP ou la réponse.  Cela signifie si un contenu est reçu avec `Content-Type` de `application/octet-stream`, y compris celui de des actions suivantes avec aucun cast entraînera une requête sortante avec `Content-Type`: `application/octet-stream`.  De cette manière, le moteur peuvent guaruntee données ne soient pas perdues lorsqu’il se déplace dans le flux de travail.  Toutefois, l’état de l’action (entrées et sorties) sont stockées dans un objet JSON comme il circule dans le flux de travail.  Cela signifie que pour préserver certains types de données, le moteur convertit le contenu d’une chaîne binary base64 encodée avec les métadonnées appropriées qui conserve les deux `$content` et `$content-type` -qui sera automatiquement converti.  Vous pouvez convertir également manuellement entre les types de contenu à l’aide d’intégrées dans les fonctions de conversion :

* `@json()`-effectue un cast des données`application/json`
* `@xml()`-effectue un cast des données`application/xml`
* `@binary()`-effectue un cast des données`application/octet-stream`
* `@string()`-effectue un cast des données`text/plain`
* `@base64()`-Convertit le contenu d’une chaîne en base 64
* `@base64toString()`-Convertit une chaîne codée en base64`text/plain`
* `@base64toBinary()`-Convertit une chaîne codée en base64`application/octet-stream`
* `@encodeDataUri()`-Encode une chaîne sous la forme d’un tableau d’octets dataUri
* `@decodeDataUri()`-Décode un dataUri dans un tableau d’octets

Par exemple, si vous avez reçu une demande HTTP avec `Content-Type`: `application/xml` de :

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Je pouvais effectuer un cast et utiliser ultérieurement avec quelque chose comme `@xml(triggerBody())`, ou dans une fonction comme `@xpath(xml(triggerBody()), '/CustomerName')`.

### <a name="other-content-types"></a>Types de contenu-autres

Autres types de contenu sont pris en charge et fonctionne avec une application de logique, mais vous devrez manuellement extraire le corps du message par le décodage du `$content`.  Par exemple, si j’avais déclenchement hors d’une `application/x-www-url-formencoded` demande qui ressemble à ce qui suit :

```
CustomerName=Frank&Address=123+Avenue
```

Étant donné que cela un pas en texte brut ou le JSON, il sera stocké dans l’action en tant que :

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Où `$content` est codée sous la forme d’une chaîne en base 64 pour conserver toutes les données de charge utile.  Dans la mesure où il n’existe actuellement pas d’une fonction native pour les données du formulaire, je pourrais toujours utiliser ces données au sein d’un flux de travail par manuellement l’accès à des données avec une fonction comme `@string(body('formdataAction'))`.  Si je voulais ma demande sortante ait également la `application/x-www-url-formencoded` en-tête content-type, je pourrais simplement l’ajouter dans le corps de l’action sans conversion comme `@body('formdataAction')`.  Toutefois, cela ne fonctionne que si le corps est le seul paramètre dans le `body` d’entrée.  Si vous essayez d’effectuer `@body('formdataAction')` à l’intérieur d’une `application/json` demande vous obtiendrez une erreur d’exécution qu’il enverra le corps codé.
