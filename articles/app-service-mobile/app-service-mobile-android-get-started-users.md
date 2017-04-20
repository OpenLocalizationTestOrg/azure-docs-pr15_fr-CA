<properties
    pageTitle="Ajouter une authentification sur Android avec applications Mobile | Service d’application Azure"
    description="Apprenez à utiliser les applications Mobile dans le Service d’application Azure pour authentifier les utilisateurs de votre application d’Android via un grand nombre de fournisseurs d’identité, y compris de Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-android-app"></a>Ajouter l’authentification à votre application d’Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous ajoutez l’authentification pour le projet de démarrage rapide de liste des tâches sur Android à l’aide d’un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [mise en route avec les applications mobiles] , vous devez effectuer tout d’abord.

##<a name="register"></a>Enregistrer votre application pour l’authentification et de configurer le Service de l’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ Dans Studio Android, ouvrez le projet projeté vous terminé avec le didacticiel [mise en route avec les applications mobiles]. Dans le menu **exécution** sur **exécuter l’application** et vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est déclenchée après le démarrage de l’application.

     Cette exception se produit, car l’application essaie d’accéder le serveur principal en tant qu’un utilisateur non authentifié, mais la table de _TodoItem_ requiert l’authentification.

Ensuite, vous mettez à jour l’application pour authentifier les utilisateurs avant de demander des ressources à partir du serveur principal chargé de l’application Mobile.

## <a name="add-authentication-to-the-app"></a>Ajouter une authentification à l’application

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Mettre en cache les jetons d’authentification sur le client

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous terminé ce didacticiel de l’authentification de base, pensez à passer à un des didacticiels suivants :

+ [Ajouter des notifications de type pousser à votre application d’Android](app-service-mobile-android-get-started-push.md) Découvrez comment configurer votre back-end de l’application Mobile pour utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type Pousser.

+ [Activer la synchronisation hors connexion pour votre application d’Android](app-service-mobile-android-get-started-offline-data.md) Découvrez comment ajouter la prise en charge en mode hors connexion de votre application à l’aide d’un back-end de l’application Mobile. Synchronisation hors connexion permet aux utilisateurs d’interagir avec une application mobile&mdash;affichage, ajout ou modification de données&mdash;même lorsqu’il n’y a pas de connexion réseau.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Mise en route avec les applications Mobile]: app-service-mobile-android-get-started.md
