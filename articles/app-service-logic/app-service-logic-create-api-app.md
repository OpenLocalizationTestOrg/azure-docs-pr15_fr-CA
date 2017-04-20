<properties 
    pageTitle="Créer une API pour les applications de logique" 
    description="Création d’une API personnalisée à utiliser avec des applications de logique" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na" 
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>
    
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>Création d’une API personnalisée à utiliser avec des applications de logique

Si vous souhaitez étendre la plate-forme d’applications de logique, il existe de nombreuses façons d’appeler des API ou systèmes qui ne sont pas disponibles comme l’un de nos nombreux connecteurs de-l’emploi.  Une de ces méthodes pour créer une application API que vous pouvez appeler à partir d’un flux de travail d’application de la logique.

## <a name="helpful-tools"></a>Outils utiles

Pour API pour un fonctionnement optimal avec les applications de logique, nous vous recommandons de génération d’un document [swagger](http://swagger.io) , détaillant les opérations prises en charge et les paramètres de votre API.  Il existe de nombreuses bibliothèques (comme [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) qui généreront automatiquement le swagger pour vous.  Vous pouvez également utiliser [TRex](https://github.com/nihaue/TRex) pour annoter le swagger pour fonctionner correctement avec les applications de logique (affiche les noms, les types de propriété, etc..).  Pour certains exemples d’applications API intégrée pour les applications de logique, n’oubliez pas de consulter notre [référentiel de GitHub](http://github.com/logicappsio) ou d’un [blog](http://aka.ms/logicappsblog).

## <a name="actions"></a>Actions

L’action de base pour une application de logique est un contrôleur qui acceptera une demande HTTP et renvoie une réponse (généralement 200).  Cependant, il existe différents modèles que vous pouvez suivre pour étendre des actions en fonction de vos besoins.

Par défaut, le moteur de l’application de la logique expire une demande après 1 minute.  Toutefois, vous pouvez avoir votre API exécuter des actions qui prennent plus de temps, et ont le moteur à attendre la fin, en suivant le modèle un async ou webhook détaillé ci-dessous.

Pour les actions standard, suffit d’écrire une méthode de demande HTTP dans votre API qui est exposée via swagger.  Vous pouvez voir des exemples d’applications d’API qui fonctionnent avec les applications de logique dans notre [référentiel de GitHub](https://github.com/logicappsio).  Voici comment réaliser des modèles communs avec un lien personnalisé.

### <a name="long-running-actions---async-pattern"></a>Actions de longue durée-modèle asynchrone

Lors de l’exécution d’une tâche ou une étape longue, la première chose que vous devez faire est de vous assurer que le moteur sait que vous n’avez pas dépassé. Vous devez également communiquer avec le moteur comment il saura lorsque vous avez terminé la tâche, et enfin, vous devez retourner les données pertinentes au moteur de poursuivre avec le flux de travail. Vous pouvez effectuer que via une API en suivant le flux ci-dessous. Ces étapes sont à partir du point de vue de l’API personnalisé :

1. Lorsqu’une demande est reçue, retourner immédiatement une réponse (avant que le travail est effectué). Cette réponse sera un `202 ACCEPTED` réponse, informer le moteur vous avez reçu les données, accepté la charge utile et de traitement sont maintenant. La réponse 202 doit contenir les en-têtes suivants : 
 * `location`en-tête (obligatoire) : il s’agit d’une absolue chemin d’accès pour les applications de la logique d’URL permet de vérifier l’état de la tâche.
 * `retry-after`(facultatif, sera par défaut à 20 pour les actions). Ceci est le nombre de secondes que le moteur doit attendre avant d’interroger des URL d’emplacement de l’en-tête pour vérifier l’état.

2. Lorsque le statut de tâche est activé, effectuez les vérifications suivantes : 
 * Si le projet est terminé : renvoyer un `200 OK` réponse, avec la charge utile de réponse.
 * Si la tâche est en cours de traitement : retourner un autre `202 ACCEPTED` réponse, avec les mêmes en-têtes comme la réponse initiale

Ce modèle vous permet d’exécuter des tâches très longs dans un thread de votre API personnalisé, mais maintenir une connexion active avec le moteur logique Apps afin qu’il n’expire pas continuer avant que le travail est terminé. Lorsque vous ajoutez ceci dans votre application logique, il est important de noter la que vous n’avez pas besoin de faire quoi que ce soit dans la définition de l’application de la logique de continuer à interroger et vérifier l’état. Dès que le moteur voit une réponse accepté 202 avec un en-tête d’emplacement valide, il respecte le modèle asynchrone et continuer à interroger l’en-tête location tant qu’une non-202 est renvoyé.

Vous pouvez voir un exemple de ce modèle dans GitHub [ici](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### <a name="webhook-actions"></a>Actions de Webhook

Au cours de votre flux de travail, vous pouvez avoir l’application logique interrompre et attendre un « rappel » continuer.  Ce rappel est fourni sous la forme d’un verbe HTTP POST.  Pour implémenter ce modèle, vous devez fournir les deux points de terminaison sur votre contrôleur : s’abonner et annuler l’abonnement.

Sur « s’inscrire », l’application logique crée et enregistrer une URL de rappel qui peut stocker des votre API et un rappel avec prêt comme un HTTP POST.  N’importe quel contenu/en-têtes sont passés dans l’application logique et peut être utilisées dans le reste du flux de travail.  Le moteur de l’application de la logique appellera le point de s’abonner à l’exécution dès qu’il atteint cette étape.

Si l’exécution a été annulée, le moteur de l’application de la logique fera un appel pour le point de terminaison « unsubscribe ».  Votre API peut puis la désinscrire l’URL de rappel selon vos besoins.

Actuellement le Concepteur d’application logique ne gère pas découvrir un point de terminaison webhook via swagger, donc pour utiliser ce type d’action, vous devez ajouter l’action « Webhook » et spécifiez l’URL, en-têtes et corps de votre demande.  Vous pouvez utiliser la `@listCallbackUrl()` fonction de workflow dans un de ces champs comme nécessaire pour passer dans l’URL de rappel.

Vous pouvez voir un exemple d’un modèle de webhook dans GitHub [ici](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="triggers"></a>Déclencheurs

En plus des actions, vous pouvez avoir votre acte API personnalisé comme un déclencheur pour une application de logique.  Il existe deux modèles que vous pouvez suivre ci-dessous pour déclencher une application logique :

### <a name="polling-triggers"></a>Déclencheurs d’interrogation

Les déclencheurs d’interrogation agissent comme les actions asynchrones en cours d’exécution de longue ci-dessus.  Le moteur de l’application de la logique appellera le point de terminaison du déclencheur après qu’une certaine période de temps écoulé (dépendant de référence, 15 secondes de prime, Standard, 1 minute et 1 heure gratuite).

Si aucune donnée n’est disponible, le déclencheur renvoie une `202 ACCEPTED` réponse, avec une `location` et `retry-after` en-tête.  Toutefois, pour les déclencheurs, il est recommandé le `location` en-tête contient un paramètre de requête de `triggerState`.  Il s’agit d’un identificateur pour votre API de savoir quand la dernière fois que l’application logique déclenché.  Si donnée n’est disponible, le déclencheur renvoie une `200 OK` réponse avec la charge utile de contenu.  Ceci va déclencher l’application de la logique.

Par exemple, si j’ai d’interrogation pour voir si un fichier n’est pas disponible, vous pouvez créer un déclencheur d’interrogation que vous devez effectuer les opérations suivantes :

* Si une demande a été reçue avec aucune triggerState l’API retourne un `202 ACCEPTED` avec un `location` en-tête ayant une triggerState de l’heure actuelle et un `retry-after` de 15.
* Si une demande a été reçue avec une triggerState :
 * Vérifiez si les fichiers ont été ajoutés après la triggerState DateTime. 
  * S’il existe 1 fichier, renvoyer un `200 OK` réponse avec la charge utile contenue, incrémenter la triggerState à la valeur DateTime du fichier I renvoyé et définir le `retry-after` à 15.
  * S’il existe plusieurs fichiers, je peux revenir 1 à la fois avec un `200 OK`, incrémenter mon triggerState dans les `location` en-tête et jeu de `retry-after` à 0.  Cela indiquera le moteur plus de données est disponible et elle demandera immédiatement à la `location` en-tête spécifié.
  * S’il n’y a pas de fichiers disponibles, renvoyer un `202 ACCEPTED` réponse et laisse le `location` triggerState le même.  La valeur `retry-after` à 15.

Vous pouvez voir un exemple d’un déclencheur d’interrogation dans GitHub [ici](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### <a name="webhook-triggers"></a>Déclencheurs de Webhook

Déclencheurs de Webhook agissent comme des Actions de Webhook ci-dessus.  Le moteur de l’application de la logique appellera le point de terminaison « s’inscrire » chaque fois qu’un déclencheur webhook est ajouté et enregistré.  Votre API peut enregistrer l’URL webhook et l’appeler via HTTP POST à chaque fois que les données sont disponibles.  La charge utile de contenu et les en-têtes sont passés dans l’application de la logique d’exécution.

Si un déclencheur webhook est jamais supprimé (soit l’application logique entièrement, ou simplement le déclencheur webhook), le moteur de faire un appel à l’URL « unsubscribe » où votre API peut annuler l’inscription de l’URL de rappel et arrêtez tous les processus en fonction des besoins.

Actuellement le Concepteur d’application logique ne gère pas découvrir un déclencheur de webhook à swagger, donc pour utiliser ce type d’action, vous devez ajouter le déclencheur « Webhook » et spécifiez l’URL, en-têtes et corps de votre demande.  Vous pouvez utiliser la `@listCallbackUrl()` fonction de workflow dans un de ces champs comme nécessaire pour passer dans l’URL de rappel.

Vous pouvez voir un exemple d’un déclencheur webhook dans GitHub [ici](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)