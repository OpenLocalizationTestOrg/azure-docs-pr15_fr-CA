<properties
    pageTitle="Ajouter une authentification sur Cordova Apache avec applications Mobile | Service d’application Azure"
    description="Apprenez à utiliser les applications Mobile dans le Service d’application Azure pour authentifier les utilisateurs de votre application Apache Cordova via un grand nombre de fournisseurs d’identité, y compris de Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-apache-cordova-app"></a>Ajouter l’authentification à votre application d’Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
    
## <a name="summary"></a>Résumé

Dans ce didacticiel, vous ajoutez l’authentification pour le projet de démarrage rapide de liste des tâches sur Cordova Apache à l’aide d’un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [mise en route avec les applications mobiles] , vous devez effectuer tout d’abord.

##<a name="register"></a>Enregistrer votre application pour l’authentification et de configurer le Service de l’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Regardez une vidéo illustrant les étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

##<a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Maintenant, vous pouvez vérifier que l’accès anonyme à votre serveur principal a été désactivé. Dans Visual Studio, ouvrez le projet que vous avez créé lorsque vous terminé le didacticiel [mise en route avec les applications Mobile], puis exécutez votre application dans l' **Émulateur Android de Google** et vérifiez qu’un échec de connexion inattendu s’affiche après le démarrage de l’application.

Ensuite, vous mettra à jour l’application pour authentifier les utilisateurs avant de demander des ressources à partir du serveur principal chargé de l’application Mobile.

##<a name="add-authentication"></a>Ajouter une authentification à l’application

1. Ouvrez votre projet dans **Visual Studio**, puis ouvrez le `www/index.html` fichier pour le modifier.

2. Recherchez la `Content-Security-Policy` balise meta dans la section head.  Vous devez ajouter l’hôte OAuth à la liste de sources autorisées.

  	| Fournisseur               | Nom du fournisseur de kit de développement logiciel | Hôte de OAuth                  |
  	| :--------------------- | :---------------- | :-------------------------- |
  	| Azure Active Directory | DAS               | https://login.Windows.NET   |
  	| Facebook               | Facebook          | https://www.Facebook.com    |
  	| Google                 | Google            | https://Accounts.Google.com |
  	| Microsoft              | MicrosoftAccount  | https://login.Live.com      |
  	| Twitter                | Twitter           | https://API.twitter.com     |

    Voici un exemple de contenu-stratégie de sécurité (implémenté pour Azure Active Directory) :

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    Vous devez remplacer `https://login.windows.net` avec l’hôte OAuth dans le tableau ci-dessus.  Pour plus d’informations sur cette balise meta, consultez la [documentation de la stratégie de sécurité de contenu] .

    Notez que certains fournisseurs d’authentification ne nécessitent pas la que stratégie de sécurité de contenu change lorsqu’elle est utilisée sur les appareils mobiles appropriées.  Par exemple, aucune modification de la stratégie de sécurité de contenu n’est nécessaires lors de l’utilisation de l’authentification de Google sur un appareil Android.

3. Ouvrir le `www/js/index.js` pour la modification du fichier, recherchez le `onDeviceReady()` méthode, et dans la création de client, code ajoutez ce qui suit :

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Notez que ce code remplace le code existant qui crée la référence de table et de l’interface utilisateur.

    La méthode login() commence le fournisseur d’authentification. La méthode login() est une fonction asynchrone qui renvoie une promesse de JavaScript.  Le reste de l’initialisation est placé à l’intérieur de la réponse de la promesse afin qu’il n’est pas exécutée tant que la méthode login() est terminée.

4. Dans le code que vous venez d’ajouter, remplacer `SDK_Provider_Name` avec le nom de votre fournisseur de connexion. Par exemple, pour Azure Active Directory, utilisez `client.login('aad')`.

4. Exécutez votre projet.  Lorsque le projet a terminé l’initialisation, votre application affiche la page de connexion d’accès OAuth pour le fournisseur d’authentification choisi.

##<a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus [à propos de l’authentification] avec le Service d’application Azure.
* Continuer le didacticiel en ajoutant les [Pousser des Notifications] à votre application d’Apache Cordova.

Apprenez à utiliser les kits de développement logiciel.

* [Kit de développement logiciel Apache Cordova]
* [ASP.NET Server (SDK)]
* [Node.js Server (SDK)]

<!-- URLs. -->
[Mise en route avec les applications Mobile]: app-service-mobile-cordova-get-started.md
[Documentation de la stratégie de sécurité de contenu]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notifications de type Pousser]: app-service-mobile-cordova-get-started-push.md
[À propos de l’authentification]: app-service-mobile-auth.md
[Kit de développement logiciel Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md 
[ASP.NET Server (SDK)]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server (SDK)]: app-service-mobile-node-backend-how-to-use-server-sdk.md
