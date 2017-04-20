<properties
    pageTitle="Déclencheur de minuterie de fonctions Azure | Microsoft Azure"
    description="Comprendre comment utiliser des déclencheurs de minuteur dans les fonctions d’Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="fonctions Azure, fonctions, traitement de l’événement, calcul dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-timer-trigger"></a>Déclencheur de minuterie de fonctions Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer des déclencheurs de minuteur dans les fonctions d’Azure. Minuterie déclenche les fonctions d’appel basées sur une planification, une seule fois ou de façon régulière.  

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-timer-trigger"></a>Function.JSON de déclenchement de la minuterie

Le fichier *function.json* fournit une expression de planification. Par exemple, la planification suivante exécute la fonction minute :

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Le déclencheur de minuteur gère automatiquement la multi-instance horizontale : une seule instance d’une fonction particulière de minuterie exécutera sur toutes les instances.

## <a name="format-of-schedule-expression"></a>Format d’expression de planification

L’expression de planification est une [expression CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) qui inclut 6 champs : `{second} {minute} {hour} {day} {month} {day of the week}`. 

Notez que la plupart des expressions cron que vous trouvez omettre le champ {deuxième}, afin que si vous copiez à partir d’un de ceux vous devrez ajuster pour le champ supplémentaire. 

Voici un autre calendrier exemples d’expressions :

Pour déclencher une fois toutes les 5 minutes :

```json
"schedule": "0 */5 * * * *"
```

Pour déclencher une fois en haut de toutes les heures :

```json
"schedule": "0 0 * * * *",
```

Pour déclencher une fois toutes les deux heures :

```json
"schedule": "0 0 */2 * * *",
```

Pour déclencher une fois toutes les heures de 9 h à 17 h 00 :

```json
"schedule": "0 0 9-17 * * *",
```

Pour déclencher à 9 h 30 tous les jours :

```json
"schedule": "0 30 9 * * *",
```

Pour déclencher à 9 h 30 chaque jour de la semaine :

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="timer-trigger-c-code-example"></a>Exemple de code Timer trigger C#

Cet exemple de code C# écrit un seul journal à chaque fois que la fonction est déclenchée.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
