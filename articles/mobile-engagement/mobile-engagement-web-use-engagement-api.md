<properties
    pageTitle="Azure Engagement Mobile Web SDK API | Microsoft Azure"
    description="Les dernières mises à jour et des procédures pour le Kit de développement Web pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />

# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Utiliser l’API de mission Azure Mobile dans une application web

Ce document est un supplément pour le document qui vous indique comment [intégrer l’Engagement Mobile dans une application web](mobile-engagement-web-integrate-engagement.md). Il fournit des informations détaillées sur l’utilisation de l’API de mission Azure Mobile à votre application des statistiques.

L’API d’Engagement Mobile est fourni par le `engagement.agent` objet. La valeur par défaut de kit de développement Web Azure Mobile Engagement alias est `engagement`. Vous pouvez redéfinir l’alias à partir de la configuration du Kit de développement logiciel.

## <a name="mobile-engagement-concepts"></a>Concepts de l’Engagement de Mobile

Les parties suivantes affiner commun [concepts d’Engagement du Mobile](mobile-engagement-concepts.md) pour la plate-forme web.

### <a name="session-and-activity"></a>`Session`et`Activity`

Si l’utilisateur demeure inactif pendant plus de quelques secondes entre deux activités, séquence de l’utilisateur des activités est divisée en deux sessions distinctes. Ces quelques secondes sont appelés à l’expiration de la session.

Si votre application web n’est pas déclaré à la fin des activités de l’utilisateur par lui-même (en appelant le `engagement.agent.endActivity` fonction), le serveur Mobile Engagement expire automatiquement la session de l’utilisateur dans les trois minutes après la fermeture de la page d’application. Il s’agit du délai d’expiration de la session serveur.

### `Crash`

Rapports automatisés des exceptions non interceptées de JavaScript ne sont pas créés par défaut. Toutefois, vous pouvez signaler les incidents manuellement à l’aide de la `sendCrash` fonction (reportez-vous à la section sur les rapports d’incidents).

## <a name="reporting-activities"></a>Rapports d’activités

Création de rapports sur l’activité de l’utilisateur inclut lorsqu’un utilisateur démarre une nouvelle activité, et lorsque l’utilisateur met fin à l’activité en cours.

### <a name="user-starts-a-new-activity"></a>Utilisateur démarre une nouvelle activité

    engagement.agent.startActivity("MyUserActivity");

Vous devez appeler `startActivity()` chaque activité utilisateur change. Le premier appel à cette fonction démarre une nouvelle session de l’utilisateur.

### <a name="user-ends-the-current-activity"></a>Utilisateur met fin à l’activité en cours

    engagement.agent.endActivity();

Vous devez appeler `endActivity()` au moins une fois lorsque l’utilisateur termine de leur dernière activité. Cela informe le Kit de développement logiciel de Mobile Engagement Web que l’utilisateur est actuellement inactive, et que la session de l’utilisateur doit être fermé après l’expiration du délai de la session. Si vous appelez `startActivity()` avant l’expiration du délai d’expiration de la session, la session est tout simplement reprise.

Dans la mesure où il n’y a aucun appel fiable pour la fermeture de la fenêtre du navigateur, il est souvent difficile, voire impossible d’intercepter la fin des activités de l’utilisateur à l’intérieur d’un environnement web. C’est pourquoi le serveur Mobile Engagement expire automatiquement la session de l’utilisateur dans les trois minutes après la fermeture de la page d’application.

## <a name="reporting-events"></a>Événements de génération de rapports

Création de rapports sur les événements traite les événements de session et autonome.

### <a name="session-events"></a>Événements de session

Événements de session sont généralement utilisés pour signaler les actions exécutées par un utilisateur au cours de la session de l’utilisateur.

**Exemple sans données supplémentaires :**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Exemple avec des données supplémentaires :**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Événements autonome

Contrairement aux événements de la session, les événements autonome peuvent se produire en dehors du contexte d’une session.

Pour ce faire, utilisez ``engagement.agent.sendEvent`` au lieu de ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Rapport d’erreurs

Création de rapports sur les erreurs traite les erreurs de session et autonome.

### <a name="session-errors"></a>Erreurs de session

Erreurs de session sont généralement utilisés pour signaler les erreurs qui ont un impact sur l’utilisateur au cours de la session de l’utilisateur.

**Exemple sans données supplémentaires :**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Exemple avec des données supplémentaires :**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Erreurs d’autonome

Contrairement aux erreurs de session, les erreurs autonome peuvent se produire en dehors du contexte d’une session.

Pour ce faire, utilisez `engagement.agent.sendError` au lieu de `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Tâches de Reporting

Création de rapports sur les couvertures de travaux signaler les erreurs et les événements qui se produisent lors d’un travail et les rapports d’incidents.

**Exemple :**

Si vous souhaitez surveiller une requête AJAX, vous utilisez les éléments suivants :

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Rapport d’erreurs lors d’un travail

Les erreurs peuvent être associés à une tâche en cours d’exécution plutôt qu’à la session utilisateur en cours.

**Exemple :**

Si vous souhaitez signaler une erreur en cas d’échec d’une requête AJAX :

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Rapport d’événements lors d’un travail

Les événements peuvent être associés à une tâche en cours d’exécution plutôt qu’à la session utilisateur en cours, grâce à la `engagement.agent.sendJobEvent` fonction.

Cette fonction fonctionne exactement comme `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Reporting des pannes

Utilisez le `sendCrash` fonction d’état se bloque manuellement.

Le `crashid` argument est une chaîne qui identifie le type d’incident.
Le `crash` argument est généralement la trace de la pile de l’incident en tant que chaîne.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Paramètres supplémentaires

Vous pouvez joindre des données arbitraires à un événement, une erreur, une activité ou une tâche.

Les données peuvent être n’importe quel objet JSON (mais pas un tableau ou type primitif).

**Exemple :**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Limites

Dans les domaines des expressions régulières pour les clés, les types valeur et la taille sont des limites qui s’appliquent aux paramètres supplémentaires.

#### <a name="keys"></a>Clés

Chaque clé de l’objet doit correspondre à l’expression régulière suivante :

    ^[a-zA-Z][a-zA-Z_0-9]*

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, des chiffres ou des traits de soulignement (\_).

#### <a name="values"></a>Valeurs

Les valeurs sont limitées à la chaîne, nombre et les types Boolean.

#### <a name="size"></a>Taille

Extras sont limités à 1 024 caractères par appel (une fois que le Kit de développement logiciel de Mobile Engagement Web encode dans JSON).

## <a name="reporting-application-information"></a>Informations sur l’application de création de rapports

Vous pouvez signaler manuellement le suivi d’informations (ou toutes les autres informations spécifiques à l’application) à l’aide de la `sendAppInfo()` fonction.

Notez que ces informations peuvent être envoyées de façon incrémentielle. Uniquement la dernière valeur d’une clé spécifique est conservée pour un périphérique spécifique.

Comme événement extras, vous pouvez utiliser n’importe quel objet JSON pour extraire les informations de l’application. Notez que les tableaux ou les sous-objets sont traitées sous forme de chaînes à deux dimensions (à l’aide de la sérialisation JSON).

**Exemple :**

Voici un exemple de code pour l’envoi du sexe de l’utilisateur et la date de naissance :

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Limites

Les limites qui s’appliquent aux informations de l’application sont dans les domaines des expressions régulières pour les clés et la taille.

#### <a name="keys"></a>Clés

Chaque clé de l’objet doit correspondre à l’expression régulière suivante :

    ^[a-zA-Z][a-zA-Z_0-9]*

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, des chiffres ou des traits de soulignement (\_).

#### <a name="size"></a>Taille

Informations sur l’application sont limitées à 1 024 caractères par appel (une fois que le Kit de développement logiciel de Mobile Engagement Web encode dans JSON).

Dans l’exemple précédent, le JSON envoyé au serveur est 44 caractères :

    {"birthdate":"1983-12-07","gender":"female"}
