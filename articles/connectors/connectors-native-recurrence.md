<properties
    pageTitle="Ajouter le déclencheur de périodicité dans les applications de logique | Microsoft Azure"
    description="Vue d’ensemble du déclencheur récurrence et comment l’utiliser avec une application Azure logique."
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-recurrence-trigger"></a>Mise en route avec le déclencheur de la périodicité

À l’aide du déclencheur de la périodicité, vous pouvez créer des workflows puissants dans le nuage.

Par exemple, vous pouvez :

- Planifiez un flux de travail pour exécuter une procédure SQL stockée chaque jour.
- E-mail un récapitulatif de tous les tweet la semaine dernière sur un certain hashtag.

Pour commencer à utiliser le déclencheur de la récurrence dans une logique d’application, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-a-recurrence-trigger"></a>Utiliser un déclencheur de périodicité

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail est défini dans une logique d’application. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de la configuration d’un déclencheur de périodicité dans une logique d’application :

1. Ajouter le déclencheur de la **périodicité** de la première étape dans une logique d’application.
2. Renseignez les paramètres pour l’intervalle de récurrence.

L’application logique permet désormais de lancer une exécution après chaque intervalle de temps.

![Déclencheur HTTP](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>Détails du déclencheur

Le déclencheur de périodicité a les propriétés suivantes que vous pouvez configurer.

Il déclenche à une application de logique après un intervalle de temps spécifié.
A * signifie qu’il est un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Fréquence *|fréquence|The unit of time: `Second`, `Minute`, `Hour`, `Day`, or `Year`.|
|Intervalle *|intervalle|L’intervalle de la fréquence donnée de la périodicité.|
|Fuseau horaire|fuseau horaire|Si l’heure de début est fournie sans un offset UTC, serviront ce fuseau horaire.|
|Heure de début|heure de début|L’heure de début dans le [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).|
<br>


## <a name="next-steps"></a>Étapes suivantes

Maintenant, essayez la plate-forme et de [créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications de la logique en consultant notre [liste d’API](apis-list.md).
