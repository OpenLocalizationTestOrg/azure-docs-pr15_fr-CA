<properties
    pageTitle="Vue d’ensemble des connecteurs d’applications logique | Microsoft Azure"
    description="Vue d’ensemble des connecteurs qui peuvent être utilisés dans une application de logique"
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
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="using-connectors-in-a-logic-app"></a>À l’aide de connecteurs dans une logique d’application

Les connecteurs fournissent un accès rapide à des actions, des données et des événements sur plusieurs plates-formes, de protocoles et de services.  La liste complète des connecteurs qui prend en charge les applications de la logique peut [être trouvé ici](apis-list.md).  Connecteurs peut être utilisés comme un déclencheur ou d’une action dans une logique d’application et peut nécessiter une configuration *connexion* à utiliser (par exemple : autoriser un compte Twitter pour accéder ou la validation de votre part).

## <a name="basics"></a>Notions de base

Les connecteurs sont des services hébergés, vous pouvez accéder dans le cadre d’une application de logique pour l’intégration avec d’autres services comme Dynamics, Azure, force de vente, [et plus](apis-list.md).  Ils sont déployés et gérés par Microsoft, vous pouvez développer vos flux de travail d’intégration à l’échelle, de débit et de sécurité pris en charge.  Vous pouvez ajouter un lien à une application de logique en recherche et en sélectionnant une action de lien ou d’un trigger sous **Afficher la Microsoft d’API gérées**.

![Menu Action pour sélectionner le déclencheur][1]

Chaque action de lien ou d’un déclencheur aura son jeu de propriétés à configurer.  Vous pouvez cliquez sur les boutons d’infos pour en savoir plus sur l’action, ou faire référence à sa documentation [pour en savoir plus](apis-list.md).

Si vous souhaitez intégrer à un service ou d’une API qui n’est pas encore un connecteur, vous pouvez également étendre la logique des applications via un [lien personnalisé](../app-service-logic/app-service-logic-create-api-app.md) ou appeler directement au service via un protocole tel que HTTP.

## <a name="triggers"></a>Déclencheurs

Certains liens n’ont un déclencheur, ce qui signifie un événement à partir de ce lien déclenche une application logique, passez toutes les données en tant que partie du déclencheur.  Un déclencheur est toujours la première étape dans une logique d’application.  Des déclencheurs les plus courants incluent des opérations telles que :
 
 * Périodicité - exécute toutes les heures
 * Lors de la réception d’une demande HTTP
 * Lorsqu’un élément est ajouté à une file d’attente
 * Lors de la réception d’un message électronique
 
Certains déclencheurs se déclenchera un événement se produit au cours d’une notification à l’application de la logique de l’instantanée et d’autres nécessiteront un intervalle de récurrence configuré sur la fréquence à laquelle l’application logique vérifiera le service pour un événement (jusqu'à toutes les 15 secondes).  

Une fois qu’un événement est reçu, l’application logique fonctionnent se déclenche et les actions dans le flux de travail démarre.  Vous pourrez également accéder à des données à partir du déclencheur dans le flux de travail (par exemple le déclencheur 'sur un nouveau tweet' passe le tweet dans l’exécution).

## <a name="actions"></a>Actions

La plupart des connecteurs ont un ou plusieurs des actions pouvant être exécutées dans le cadre du flux de travail.  Les actions sont les étapes qui se produisent après le déclenchement de l’exécution d’un déclencheur.  Pour ajouter un action, cliquez sur le bouton **Nouvelle étape** et la recherche pour le connecteur vous souhaitez utiliser.  Une fois sélectionné (et après configuration toutes les [connexions](#connections) qui peuvent être requis), vous voyez la fiche action que vous pouvez configurer.  Vous pouvez sélectionner les données à partir des étapes précédentes en cliquant sur un des jetons pour les sorties, ou entrer dans une autre configuration si nécessaire.

![Configuration d’une action de connecteur][2]

## <a name="connections"></a>Connexions

La plupart des connecteurs vous obliger à configurer une *connexion* avant de pouvoir utiliser le connecteur.  Une *connexion* est n’importe quelle configuration d’ouverture de session ou de connexion nécessaire pour accéder au connecteur.  Pour les connecteurs qui utilisent OAuth, créez une connexion signifie connecter au service (par exemple, Office 365, force de vente ou GitHub) où votre jeton d’accès peut être cryptée et stockée en toute sécurité dans un magasin des secrets Azure.  Autres connecteurs (tels que FTP et SQL) nécessitent une connexion qui contient la configuration comme adresse de serveur, nom d’utilisateur et mot de passe.  Ces détails de connexion de configuration sont également cryptées et stockées en toute sécurité.  Connexions seront en mesure d’accéder le service pour tant que le service le permet.  Pour les connexions d’Azure Active Directory OAuth (par exemple, Office 365 et Dynamics) nous pouvons continuer l’actualisation d’indéfiniment le jeton d’accès.  Autres services peuvent placer des limites sur la durée pendant laquelle nous pouvons utiliser un jeton sans qu’il est en cours d’actualisation.  Certaines actions, telles que la modification d’un mot de passe invalide en général tous les jetons d’accès.  

Les connexions peuvent être affichées et gérées dans Azure en cliquant sur **Parcourir** et en sélectionnant les **Connexions d’API**.  À partir de la ressource de connexions d’API, vous pouvez afficher, modifier, mettre à jour ou nouveau autoriser toutes les connexions que vous avez créé.

## <a name="next-steps"></a>Étapes suivantes

- [Créer votre première application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Obtenir des informations courantes des utilisations et des exemples d’applications de logique](../app-service-logic/app-service-logic-examples-and-scenarios.md)
- [Mise en route avec les actions et les déclencheurs de l’intégration entreprise](../app-service-logic/app-service-logic-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png