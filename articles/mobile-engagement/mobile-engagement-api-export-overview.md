<properties
    pageTitle="Vue d’ensemble des API d’exportation Engagement mobile"
    description="Découvrez les concepts de base sur l’exportation de vos données brutes générées par les périphériques de l’utilisateur à les exploiter dans vos propres outils"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="kpiteira"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile"
    ms.date="04/26/2016"
    ms.author="kpiteira"/>

# <a name="mobile-engagement-export-api-overview"></a>Vue d’ensemble des API d’exportation Engagement mobile

## <a name="introduction"></a>Introduction

Dans ce document, vous apprendrez les notions de base sur l’exportation de vos données brutes générées par les périphériques de l’utilisateur à les exploiter dans vos propres outils.

## <a name="pre-requisites"></a>Conditions préalables

Exportation des données brutes à partir de l’Engagement de Mobile requiert :

- Programme d’installation de API d’authentification pour pouvoir utiliser les API (voir le [manuel d’installation de l’authentification](mobile-engagement-api-authentication-manual.md)),
- Utiliser les API de repos ou le [Kit de développement logiciel de .net](mobile-engagement-dotnet-sdk-service-api.md),
- Un compte de stockage Azure.

>[AZURE.NOTE] Nous conseille également l' excellent [Explorateur de stockage Microsoft Azure](http://storageexplorer.com/), au moins au cours de la phase de développement car il fournit une interface utilisateur facile à utiliser pour l’interaction avec le stockage Azure.

## <a name="what-can-be-exported"></a>Ce qui peut être exporté ?

Engagement de Mobile permet à ses utilisateurs de collecter de nombreux types de données et par conséquent a plusieurs types d’exportation adaptées à ces différents types de données.
Il existe 2 types essentiels d’exportation :

- Snapshot : utilisé en général pour exporter les données, qui représente un état et pour lesquelles Mobile Engagement ne dispose pas d’un historique. Cela comprend des balises (app-info), jetons ou des évaluations de campagne push par exemple. En conséquence ces types d’exportation ne sont pas liées à une date.
- historique : ce type d’exportation est utilisé pour les données qui s’accumulent au fil du temps, notamment des événements et des activités par exemple.

Le tableau ci-dessous décrit de manière exhaustive tous les possibles exportations :

| Type d’exportation | Type de données | Description                                                                                                                                 |
|-------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Capture instantanée    | Push      | Génère une exportation de commentaires de pousser les campagnes sur une base par deviceid/nom d’utilisateur                                                              |
| Capture instantanée    | Balise       | Génère une exportation des balises (app-info) associées à chaque périphérique                                                                       |
| Capture instantanée    | DISPOSITIF    | Génère une exportation de la plupart des données sur des périphériques tels que les technicals (modèle, paramètres régionaux, fuseau horaire,...), les balises, la première fois vu... |
| Capture instantanée    | Jeton     | Génère une exportation de tous les jetons valides                                                                                                 |
| Historique  | Activité  | Génère une exportation de toutes les activités de chaque périphérique sur une période donnée                                                           |
| Historique  | Événement     | Génère une exportation de toutes les activités de chaque périphérique sur une période donnée                                                           |
| Historique  | Travail       | Génère une exportation de tous les traitements pour chaque périphérique sur une période donnée                                                                 |
| Historique  | Erreur     | Génère une exportation de toutes les erreurs de chaque périphérique sur une période donnée                                                               |

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Les exportations sont longues exécutant des tâches qui peut-être produire des fichiers de données volumineux. Pour cette raison, ils ne peut pas être appelées pour renvoyer immédiatement un fichier à télécharger.
Pour exporter des données à partir de l’Engagement de Mobile, vous devez créer une **Tâche d’exportation** via l’API où vous indiquez généralement :

- Le type d’exportation (capture instantanée ou historique),
- Le type de données
- Le **Conteneur de stockage Azure** (y compris un SAS valide avec un accès en écriture) où sera écrit le résultat de l’exportation.

Veuillez noter que peut prendre quelques minutes pour démarrer votre opération, et il peut exécuté de quelques secondes pour les applications de petite taille à plusieurs heures pour les applications avec un grand nombre d’utilisateurs ou d’activité.

Une fois la tâche créée, il est possible vérifier son état pour voir si elle est en cours d’exécution ou si elle est terminée.

Une fois que le travail est réussi, le fichier de données qui en résulte est disponible sur le conteneur de stockage fourni.
