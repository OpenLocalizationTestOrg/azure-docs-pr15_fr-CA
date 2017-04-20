<properties
    pageTitle="Concepts de l’Engagement de Mobile | Microsoft Azure"
    description="Concepts de Mobile Engagement Azure"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-concepts"></a>Concepts de Mobile Engagement Azure

Engagement de Mobile définit quelques concepts communs à toutes les plates-formes prises en charge. Cet article décrit brièvement ces concepts.

Cet article est un bon point de départ si vous débutez dans l’Engagement de Mobile. Veillez également à lire la documentation spécifique à la plate-forme que vous utilisez, comme il affiner les concepts décrits dans cet article avec plusieurs détails et exemples ainsi que des limitations possibles.

## <a name="devices-and-users"></a>Les utilisateurs et les périphériques
Engagement de Mobile identifie les utilisateurs en générant un identificateur unique pour chaque périphérique. Cet identificateur est appelé l’identificateur de périphérique (ou `deviceid`). Il est généré de manière à ce que toutes les applications s’exécuter le même périphérique partagent le même identificateur de périphérique.

Implicitement, il signifie que Mobile Engagement considère qu’un périphérique appartenant à un seul utilisateur, et par conséquent, les utilisateurs et périphériques sont des concepts équivalents.

## <a name="sessions-and-activities"></a>Des activités et sessions
Une session est l’une des utilisations de l’application exécutée par un utilisateur, entre le moment où l’utilisateur commence à l’utiliser jusqu'à ce que l’utilisateur arrête.

Une activité est une utilisation d’une partie de sous-système donnée de l’application exécutée par un utilisateur (il s’agit généralement d’un écran, mais il peut s’agir d’approprié à l’application).

Un utilisateur ne pouvez effectuer qu’une seule activité à la fois.

Une activité est identifiée par un nom (limité à 64 caractères) et pouvez incorporer éventuellement quelques données supplémentaires (dans la limite de 1 024 octets).

Les sessions sont calculées automatiquement à partir de la séquence d’activités effectuées par les utilisateurs. Une session démarre lorsque l’utilisateur démarre son activité première et s’arrête lorsqu’il a terminé sa dernière activité. Cela signifie qu’une session ne doive pas être démarré ou arrêté explicitement. Au lieu de cela, les activités sont explicitement démarrées ou arrêtées. Si aucune activité n’est signalée, aucune session n’est signalée.

## <a name="events"></a>Événements
Les événements sont utilisés pour signaler des actions instantanées (comme bouton enfoncé ou articles lus par les utilisateurs).

Un événement peut être associé à la session en cours, à une tâche en cours d’exécution, ou il peut être un événement autonome.

Un événement est identifié par un nom (limité à 64 caractères) et pouvez incorporer éventuellement quelques données supplémentaires (dans la limite de 1 024 octets).

## <a name="error"></a>Erreur
Les erreurs sont utilisés pour signaler des problèmes détectés correctement par l’application (telles que les actions de l’utilisateur incorrecte ou échecs des appels API).

Une erreur peut être liée à la session en cours, à une tâche en cours d’exécution, ou il peut être une erreur autonome.

Une erreur est identifiée par un nom (limité à 64 caractères) et pouvez incorporer éventuellement quelques données supplémentaires (dans la limite de 1 024 octets).

## <a name="job"></a>Travail
Les travaux sont utilisés pour signaler des actions ayant une durée (comme la durée des appels de l’API, afficher les temps des annonces, des tâches d’arrière-plan ou durée des actions de l’utilisateur).

Un travail n’est pas associé à une session, car une tâche peut être réalisée en arrière-plan, sans intervention de la part de l’utilisateur.

Un travail est identifié par un nom (limité à 64 caractères) et pouvez incorporer éventuellement quelques données supplémentaires (dans la limite de 1 024 octets).

## <a name="crash"></a>Se bloquer
Incidents sont émis automatiquement par le Kit de développement Mobile Engagement à signaler des échecs d’application si l’application ne détectés ne pas les problèmes rendent incident.

## <a name="application-information"></a>Informations sur l’application
Informations sur l’application (ou informations d’application) sont utilisée pour baliser les utilisateurs, c'est-à-dire, d’associer des données aux utilisateurs d’une application (cela est semblable aux cookies de web, sauf que les informations d’application sont stockée sur le serveur sur la plateforme Azure Mobile Engagement).

Info de l’application peut être enregistrée à l’aide de l’API de kit de développement logiciel de Mobile Engagement ou à l’aide de la plate-forme Mobile Engagement API du périphérique.

Info de l’application est une paire clé/valeur associée à un périphérique. La clé est le nom de l’information sur l’application (limité à 64 ASCII des lettres [a-zA-Z], des nombres [0-9] et des traits de soulignement [_]). La valeur (limitée à 1024 caractères) peut être toute chaîne, entier, date (yyyy-MM-JJ) ou type Boolean (true ou false).

N’importe quel nombre d’informations de l’application peut être associé à un périphérique, dans les limites définies par les conditions de tarification Mobile Engagement. Pour une clé donnée, Engagement de Mobile n’effectue le suivi de la dernière valeur définie (aucun historique). Définition ou modification de la valeur des informations d’application force l’Engagement Mobile pour réévaluer les critères définis sur cette information de l’application (le cas échéant), ce qui signifie que des infos de cette application peut être utilisée pour déclencher les push d’en temps réel de public.

## <a name="extra-data"></a>Données supplémentaires
Données supplémentaires (ou extras) sont certaines données arbitraires qui peuvent être liées à des événements, des erreurs, des activités et des tâches.

Extras sont structurés de la même façon pour les objets JSON : ils sont constitués d’une arborescence de paires clé/valeur. Les clés sont limitées à 64 les lettres ASCII [a-zA-Z], des nombres [0-9] et des traits de soulignement [_]) et la taille totale des bonus est limitée à 1024 caractères (une fois codées au format JSON par le Kit de développement logiciel de Mobile Engagement).

L’intégralité de l’arborescence de paires clé/valeur est stockée sous la forme d’un objet JSON. Néanmoins, seul le premier niveau de clés/valeurs est décomposé soit directement accessible à certaines fonctions avancées comme les Segments (par exemple, vous pouvez facilement définir un segment appelé « SciFi ventilateurs » qui est constituée de tous les utilisateurs ayant envoyé au moins 10 fois l’événement nommé « content_viewed » avec le content_type clé supplémentaire de « » de la valeur « scifi « le mois dernier). Il est donc fortement recommandé pour envoyer des extras uniquement constitués de simples listes de paires clé/valeur à l’aide des valeurs scalaires (par exemple, chaînes, dates, nombres entiers ou booléen).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du Kit de développement logiciel Windows universel pour Azure Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md)
- [Vue d’ensemble de Silverlight de Windows Phone SDK pour Azure Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS SDK pour Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md)
- [Android SDK pour Azure Engagement Mobile](mobile-engagement-android-sdk-overview.md)
