<properties
    pageTitle="Azure AD Cordova mise en route | Microsoft Azure"
    description="Comment générer une application Cordova s’intègre avec Azure AD pour l’ouverture de session et appelle AD Azure protégé d’API utilisant OAuth."
    services="active-directory"
    documentationCenter=""
    authors="vibronet"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="vittorib"/>

# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Intégrer AD Azure avec un Apache Cordova app

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova rend possible vous permettant de développer des applications HTML5/JavaScript qui peuvent s’exécuter sur des périphériques mobiles comme les applications natives à part entière.
Avec AD Azure, vous pouvez ajouter des capacités d’authentification de niveau entreprise pour vos applications Cordova. Grâce à un plug-in Cordova habillage des kits de développement logiciel d’Azure AD natifs sur iOS, Android, Windows Store et Windows Phone, vous pouvez améliorer votre application de prise en charge de signe sur avec les comptes des utilisateurs Active Directory, accéder à Office 365 et API d’Azure et même protéger des appels à vos propres API Web personnalisé.

Dans ce didacticiel, nous utiliserons le plug-in Apache Cordova pour Active Directory bibliothèque d’authentification (ADAL) pour améliorer une application simple avec les fonctionnalités suivantes :

-   Avec seulement quelques lignes de code authentifier un utilisateur AD et obtenir un jeton pour l’appel de l’API Azure AD graphique.
-   Utilisation de ce jeton pour appeler l’API graphique pour rechercher ce répertoire et afficher les résultats  
-   Exploiter le cache de jetons ADAL pour minimiser les invites d’authentification de l’utilisateur.

Pour ce faire, vous devez :

2. Inscrire une application avec Azure AD
2. Ajoutez du code à votre application aux jetons de demande
3. Ajoutez le code pour utiliser le jeton pour l’interrogation de l’API de graphique et afficher les résultats.
4. Créer le projet de déploiement Cordova avec toutes les plates-formes que vous souhaitez cibler et le plug-in Cordova ADAL et tester la solution dans les émulateurs.

## <a name="0--prerequisites"></a>*0. conditions préalables de*

Pour terminer ce didacticiel, que vous devez :

- Un locataire AD Azure où vous disposez d’un compte disposant de droits de développement d’application
- Un environnement de développement configuré pour utiliser Apache Cordova  

Si vous les avez déjà configurée, passez directement à l’étape 1.

Si vous n’avez pas un locataire Azure AD, vous trouverez des [savoir comment en obtenir un ici](active-directory-howto-tenant.md).

Si vous n’avez pas Cordova Apache configurée sur votre ordinateur, installez les éléments suivants :

- [GIT](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [CLI de Cordova](https://cordova.apache.org/) (peut être installé aisément via le Gestionnaire de package NPM : `npm install -g cordova`)

Notez que ceux doivent fonctionner à la fois sur le PC et sur le Mac.

Chaque plate-forme cible a différentes conditions requises.

- Pour générer et exécuter Windows Tablet PC ou version d’application téléphonique
    - [Visual Studio 2013 pour Windows avec la mise à jour 2 ou version ultérieure](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express ou une autre version).
- Pour générer et exécuter pour iOS
    -   Xcode 5.x ou version ultérieure. Téléchargez-le sur le [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12) ou de http://developer.apple.com/downloads
    -   [ios-sim](https://www.npmjs.org/package/ios-sim) – vous permet de lancer des applications iOS sur iOS Simulator à partir de la ligne de commande (peut être installé aisément via le terminal : `npm install -g ios-sim`)

- Pour générer et exécuter l’application pour Android
    - Installer le [Kit de développement de Java (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou une version ultérieure. Assurez-vous que `JAVA_HOME` (Variable d’environnement) est correctement définie en fonction du chemin d’installation de JDK (par exemple C:\Program Files\Java\jdk1.7.0_75).
    - Installer [Le Kit de développement logiciel Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) et ajouter `<android-sdk-location>\tools` emplacement (par exemple, C:\tools\Android\android-sdk\tools) dans votre `PATH` Variable d’environnement.
    - Ouvrez le Gestionnaire du Kit de développement logiciel Android (via Terminal Server, par exemple : `android`) et de l’installer
    - *Android 5.0.1 (API 21)* platform SDK
    - *Outils de génération android SDK* version 19.1.0 ou supérieure
    - *Référentiel de prise en charge Android* (Extras)

  Le Kit de développement logiciel Android ne fournit pas de n’importe quelle instance d’émulateur par défaut. Créer un nouveau en exécutant `android avd` à partir de Terminal Server, puis en sélectionnant *créer...* si vous souhaitez exécuter une application Android sur l’émulateur. Recommandé *Niveau Api* est 19 ou supérieur, voir [AVD Manager] (http://developer.android.com/tools/help/avd-manager.html) pour plus d’informations sur les options de création et d’émulateur Android.


## <a name="1--register-an-application-with-azure-ad"></a>*1. inscription d’une application avec Azure AD*

Remarque : cette __étape est facultative__. Le didacticiel fourni des valeurs avant mise en service qui vous permet de voir l’exemple en action sans effectuer un provisionnement dans vos propres clients. Toutefois, il est recommandé que vous effectuez cette étape et vous familiariser avec le processus, car il sera requis lorsque vous créerez vos propres applications.

Annonce Azure émettra que des jetons d’applications connues. Avant de pouvoir utiliser AD Azure à partir de votre application, vous devez créer une entrée pour celui-ci dans votre client.  Pour enregistrer une nouvelle application dans vos clients,

-   Connexion au [portail de gestion Azure](https://manage.windowsazure.com)
-   Dans nav de la main gauche, cliquez sur **Active Directory**
-   Sélectionnez le client où vous souhaitez enregistrer l’application.
-   Cliquez sur l’onglet **Applications** , cliquez sur **Ajouter** dans le bac d’alimentation inférieur.
-   Suivez les invites et créez une nouvelle **Application de Client natif** (en dépit du fait que les applications de Cordova sont HTML en fonction, nous créons native client application ici donc `Native Client Application` doit être activée ; sinon, l’application ne fonctionne pas).
    -   Le **nom** de l’application décrit votre application aux utilisateurs finals
    -   L' **URI de redirection** est l’URI utilisé pour retourner des jetons à votre application. Entrez `http://MyDirectorySearcherApp`.

Une fois l’enregistrement terminé, DAS affecter à votre application un identificateur de client unique.  Vous aurez besoin de cette valeur dans les sections suivantes : vous pouvez le trouver dans l’onglet **configuration** de l’application nouvellement créée.

Pour exécuter `DirSearchClient Sample`, accorder l’autorisation d’application créée à l' _API d’Azure AD graphique_de requête :
-   Dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ».  Pour l’application « Azure Active Directory », ajoutez l’autorisation **d’accès du répertoire que l’utilisateur connecté** sous **Autorisations d’accès délégué**.  Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="2-clone-the-sample-app-repository-required-for-the-tutorial"></a>*2. le clone le référentiel d’application exemple requis pour le didacticiel*

À partir de votre environnement ou de la ligne de commande, tapez la commande suivante :

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="3-create-the-cordova-app"></a>*3. création de l’application de Cordova*

Il existe plusieurs façons de créer des applications de Cordova. Dans ce didacticiel, nous utiliserons l’interface de ligne de commande (CLI) Cordova.
À partir de votre environnement ou de la ligne de commande, tapez la commande suivante :


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Qui crée la structure de dossiers et de la génération de modèles automatique pour le projet Cordova, copie le contenu du projet de démarrage dans le sous-dossier de www.
Déplacer vers le dossier DirSearchClient.

    cd .\DirSearchClient

Ajouter à la liste d’autorisation du plug-in, nécessaire pour appeler l’API graphique.

     cordova plugin add cordova-plugin-whitelist

Ensuite, ajoutez toutes les plateformes que vous souhaitez prendre en charge. Pour avoir un exemple fonctionnel, vous devez exécuter au moins une des commandes ci-dessous. Notez que vous ne serez pas en mesure d’émuler des e/s sous Windows ou Windows/Windows Phone sur un Mac.

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

Enfin, vous pouvez ajouter la ADAL Cordova du plug-in à votre projet.

    cordova plugin add cordova-plugin-ms-adal

## <a name="3-add-code-to-authenticate-users-and-obtain-tokens-from-aad"></a>*3. Ajoutez du code pour authentifier les utilisateurs et d’obtenir les jetons de DAS*

L’application que vous développez dans ce didacticiel fournit une fonctionnalité de recherche de répertoire de nus, où l’utilisateur peut taper l’alias d’un utilisateur dans le répertoire et visualiser certains attributs de base.  Le projet de démarrage contient la définition de l’interface utilisateur de base de l’application (dans www/index.html) et la génération de modèles automatique qui associe les événements application base de cycles, liaisons d’interface utilisateur et les résultats de logique d’affichage (dans www/js/index.js). La seule chose qui reste vous est d’ajouter de la logique d’implémentation de tâches de l’identité.

La première chose à faire est d’introduire, dans votre code, les valeurs de protocole utilisées par DAS pour identifier votre application et les ressources que vous ciblez. Ces valeurs serviront pour construire les demandes jetons ultérieurement. Insérez l’extrait de code ci-dessous tout en haut du fichier index.js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

La `redirectUri` et `clientId` valeurs devraient correspondre les valeurs décrivant votre application dans un DAS. Vous pouvez trouver celles de l’onglet Configurer dans Azure portal, comme décrit à l’étape 1 plus haut dans ce didacticiel.
Remarque : Si vous avez choisi d’inscription ne pas d’une nouvelle application dans vos propres clients, vous pouvez simplement Coller les valeurs préconfigurées ci-dessus est - qui vous permettra de voir l’exécution de l’exemple, bien que vous devez toujours créer votre propre entrée pour vos applications destinées à la production.

Ensuite, nous devons ajouter le code réel demande de jeton. Insérer l’extrait de code suivant entre les `search `et `renderdata `définitions.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Examinons cette fonction en subdivisant cette tâche dans ses deux parties principales.
Cet exemple est conçu pour fonctionner avec les clients, et non pas pour être lié à l’un d'entre eux. Il utilise le « / commun » point de terminaison, qui permet à l’utilisateur d’entrer n’importe quel compte au moment de l’authentification et envoie la demande au locataire auquel il appartient.
La première partie de la méthode inspecte le cache pour voir s’il existe déjà un jeton stocké - s’il existe, il utilise les locataires qu'il provenance de nouveau lors de l’initialisation ADAL de ADAL. Cela est nécessaire pour éviter les invites supplémentaires, comme l’utilisation de « / commun » entraîne toujours demandant à l’utilisateur d’entrer un nouveau compte.
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
La deuxième partie de la méthode effectue la demande tokewn correcte.
Le `acquireTokenSilentAsync` méthode demande à ADAL pour renvoyer un jeton pour la ressource spécifiée sans afficher tout UX. Cela peut se produire si le cache a déjà un jeton d’accès stocké, ou s’il existe un jeton d’actualisation qui peut être utilisé pour obtenir un nouveau jeton d’accès sans shwoing n’importe quelle invite.
Si cette tentative échoue, il revient `acquireTokenAsync` -qui visiblement invitera l’utilisateur à authentifier.
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Maintenant que nous avons le jeton, nous pouvons enfin appeler l’API graphique et exécuter la requête que nous. Insérer l’extrait de code suivant juste en dessous du `authenticate` définition.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Les fichiers de point de départ fourni un dépouillés UX pour la saisie d’un alias de l’utilisateur dans un contrôle textbox. Cette méthode utilise cette valeur pour construire une requête, combinez-le avec le jeton d’accès, envoyez-le au graphique et analyser les résultats. Prend en charge la méthode renderData, déjà présente dans le fichier de point de départ, pour visualiser les résultats.

## <a name="4-run"></a>*4. Exécutez*
Votre application est maintenant prête à exécuter ! D’exploitation, il est très simple : une fois que l’application démarre, entrez dans la zone de texte de l’alias de l’utilisateur que vous souhaitez rechercher -, puis cliquez sur le bouton. Vous serez invité pour l’authentification. Lors de la réussite de l’authentification et la recherche réalisée avec succès, les attributs de l’utilisateur recherchée seront affiche. Les exécutions suivantes effectue la recherche sans affichage d’une invite de commandes, grâce à la présence dans le cache du jeton précédemment acquis.
Les étapes concrètes pour l’exécution de l’application varient selon la plateforme.

####<a name="windows-10"></a>Windows 10 :

   Tablet PC :`cordova run windows --archs=x64 -- --appx=uap`

   Mobile (nécessite un périphérique Windows10 Mobile connecté au PC) :`cordova run windows --archs=arm -- --appx=uap --phone`

   __Remarque__: lors du premier démarrage, vous devrez peut-être vous connecter pour une licence de développeur. Pour plus d’informations, reportez-vous à la section [licence de développeur](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-81-tabletpc"></a>8.1 de Windows Tablet PC :

   `cordova run windows`

   __Remarque__: lors du premier démarrage, vous devrez peut-être vous connecter pour une licence de développeur. Pour plus d’informations, reportez-vous à la section [licence de développeur](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-phone-81"></a>Windows Phone 8.1 :

   Pour exécuter sur le périphérique connecté :`cordova run windows --device -- --phone`

   Pour exécuter sur l’émulateur de la valeur par défaut :`cordova emulate windows -- --phone`

   Utilisez `cordova run windows --list -- --phone` pour voir toutes les cibles disponibles et `cordova run windows --target=<target_name> -- --phone` pour exécuter l’application sur le périphérique ou l’émulateur (par exemple, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

####<a name="android"></a>Android :

   Pour exécuter sur le périphérique connecté :`cordova run android --device`

   Pour exécuter sur l’émulateur de la valeur par défaut :`cordova emulate android`

   __Remarque__: Assurez-vous que vous avez créé instance émulateur à l’aide du *Gestionnaire d’AVD* comme il est montré dans la section *conditions préalables* .

   Utilisez `cordova run android --list` pour voir toutes les cibles disponibles et `cordova run android --target=<target_name>` pour exécuter l’application sur le périphérique ou l’émulateur (par exemple, `cordova run android --target="Nexus4_emulator"`).

####<a name="ios"></a>e/s :

   Pour exécuter sur le périphérique connecté :`cordova run ios --device`

   Pour exécuter sur l’émulateur de la valeur par défaut :`cordova emulate ios`

   __Remarque__: Vérifiez que vous avez `ios-sim` package installé pour s’exécuter sur l’émulateur. Pour plus d’informations, reportez-vous à la section *conditions préalables* .

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Utilisez `cordova run --help` voir génération supplémentaires et d’options d’exécution.

Pour référence, l’exemple complet (sans les valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).  Vous pouvez désormais vous déplacer à des scénarios plus avancés (OK et plus intéressantes).  Vous souhaiterez peut-être essayer :

[Sécuriser un Web Node.js API avec Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
