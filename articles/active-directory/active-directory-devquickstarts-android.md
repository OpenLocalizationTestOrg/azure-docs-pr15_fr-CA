<properties
    pageTitle="Azure AD Android route | Microsoft Azure"
    description="Comment générer une application d’Android qui s’intègre avec Azure AD pour l’ouverture de session et appelle AD Azure protégé d’API utilisant OAuth."
    services="active-directory"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-android-app"></a>Intégrer AD Azure dans une application d’Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Si vous développez une application de bureau, AD Azure rend très simple vous permettant d’authentifier les utilisateurs à l’aide de leurs comptes Active Directory.  Il permet également à votre application d’utiliser en toute sécurité de n’importe quel web API protégé par AD Azure, telles que les API d’Office 365 ou l’API d’Azure.

Pour les clients Android qui doivent accéder aux ressources protégées, AD Azure fournit la bibliothèque de l’authentification Active Directory ou ADAL.  Seul but de ADAL dans la vie est de faciliter votre application obtenir les jetons d’accès.  Pour illustrer simplement combien il est facile, ici que nous créerons une application d’Android liste des actions qui :

-   Obtient accès jetons pour l’appel d’une API de liste de tâches à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Obtient la liste des actions d’un utilisateur
-   Signe les utilisateurs.

Pour commencer, vous aurez besoin d’un locataire AD Azure dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous ne disposez pas d’un client, à [savoir comment en obtenir un](active-directory-howto-tenant.md).

> [AZURE.TIP] Essayez de l’aperçu de notre nouveau [portail destiné aux développeurs](https://identity.microsoft.com/Docs/Android) qui vous permettra de devenir opérationnel avec Azure Active Directory en quelques minutes !  Le portail des développeurs vous guidera tout au long du processus d’inscription d’une application et d’intégration AD Azure dans votre code.  Lorsque vous avez terminé, vous aurez une application simple qui peut authentifier les utilisateurs de vos clients et un back-end qui peuvent accepter des jetons et effectuer la validation. 

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Étape 1 : Télécharger et exécuter l’exemple de TODO Node.js reste API serveur

Cet exemple est écrit spécifiquement pour fonctionner avec notre exemple existant pour la création d’un client unique action reste API pour Active Directory de Microsoft Azure. Il s’agit d’une condition requise pour le démarrage rapide.

Pour plus d’informations sur la façon de configurer ce paramètre, consultez nos exemples d’existants :

* [Service d’API Microsoft Azure Active Directory exemple reste pour Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## <a name="step-2-register-your-web-api-with-your-microsoft-azure-ad-tenant"></a>Étape 2 : Enregistrer votre site Web API avec vos clients d’annonces Microsoft Azure

**Ce que je fais ?**

*Microsoft Active Directory prend en charge l’ajout de deux types d’applications. Web API qui offrent des services pour les utilisateurs et les applications (soit sur le web ou une application qui s’exécute sur un périphérique) qui accèdent à ces API Web. Dans cette étape, vous enregistrez l’API Web vous exécutez localement pour le test de cet exemple. Normalement, cette API Web est un service reste qui propose des fonctionnalités que vous souhaitez qu’une application pour accéder à. Microsoft Azure Active Directory peut protéger n’importe quel point de terminaison !*

*Nous supposons ici vous enregistrez l’API REST TODO référencé ci-dessus, mais cela fonctionne pour n’importe quelle API Web Azure Active Directory pour protéger souhaité.*

Étapes à suivre pour enregistrer une API Web avec Microsoft Azure AD

1. Ouvrez une session sur le [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur Active Directory dans le NAV de gauche.
3. Cliquez sur le locataire du répertoire où vous souhaitez enregistrer l’exemple d’application.
4. Cliquez sur l’onglet Applications.
5. Dans le bac d’alimentation, cliquez sur Ajouter.
6. Cliquez sur « Ajouter une application du développement de mon organisation ».
7. Entrez un nom convivial pour la sélection de l’application, par exemple « TodoListService », « API Web et/ou Web Application » et cliquez sur Suivant.
8. Pour l’URL de session, entrez l’URL de base pour l’exemple, qui est par défaut `https://localhost:8080`.
9. Entrez l’URI ID App, `https://<your_tenant_name>/TodoListService`, en remplaçant `<your_tenant_name>` avec le nom de vos clients AD Azure.  Cliquez sur OK pour terminer l’enregistrement.
10. Dans le portail Azure, cliquez sur l’onglet de configuration de votre application.
11. **Trouver la valeur de l’ID de Client et de les copier mis de côté**, vous devez cela plus tard lors de la configuration de votre application.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Étape 3 : Enregistrer l’exemple d’application Android Native Client

Enregistrement de votre application web est la première étape. Ensuite, vous devrez informer Azure Active Directory de votre application ainsi. Cela permet à votre application de communiquer avec l’API Web inscrits uniquement

**Ce que je fais ?**  

*Comme indiqué ci-dessus, Microsoft Azure Active Directory prend en charge l’ajout de deux types d’applications. Web API qui offrent des services pour les utilisateurs et les applications (soit sur le web ou une application qui s’exécute sur un périphérique) qui accèdent à ces API Web. Dans cette étape, vous enregistrez l’application dans cet exemple. Vous devez le faire dans l’ordre de cette application pour être en mesure de demande à accéder à l’API Web que vous venez d’enregistrer. Pour permettre à votre application à la demande pour la connexion, sauf si elle est enregistrée même refuse Azure Active Directory ! Qui est la partie de la sécurité du modèle.*

*Nous supposons ici vous enregistrez cet exemple d’application référencé ci-dessus, mais cela fonctionne pour n’importe quelle application que vous développez.*

**Pourquoi me mettre une application et une API Web en priorité ?**

*Comme vous l’avez peut-être deviné, vous pourriez créer une application qui accède à une API externe qui est enregistrée dans Azure Active Directory à partir d’un autre client. Si vous procédez ainsi, vos clients devront consent à l’utilisation de l’API de l’application. La partie intéressante est, bibliothèque de l’authentification Active Directory pour iOS prend en charge ce consentement pour vous ! Comme nous allons des fonctions plus avancées, vous verrez que c’est une partie importante du travail nécessaire pour accéder à la suite de APIs Microsoft Azure et Office ainsi que tout autre fournisseur de services. Pour l’instant, car vous avez enregistré votre application sous le même client et Web API vous ne verrez pas les invites de consentement. C’est généralement le cas si vous développez une application pour votre entreprise à utiliser.*

1. Ouvrez une session sur le [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur Active Directory dans le NAV de gauche.
3. Cliquez sur le locataire du répertoire où vous souhaitez enregistrer l’exemple d’application.
4. Cliquez sur l’onglet Applications.
5. Dans le bac d’alimentation, cliquez sur Ajouter.
6. Cliquez sur « Ajouter une application du développement de mon organisation ».
7. Entrez un nom convivial pour l’application, par exemple « TodoListClient-Android », sélectionnez « Client Application Native », puis cliquez sur Suivant.
8. Pour l’URI de redirection, entrez `http://TodoListClient`.  Cliquez sur Terminer.
9. Cliquez sur l’onglet Configuration de l’application.
10. Trouver la valeur de l’ID de Client et de les copier, vous en avez besoin plus tard lors de la configuration de votre application.
11. Dans « Autorisations pour les autres Applications », cliquez sur « Ajouter l’Application ».  Sélectionnez « Autre » dans la liste déroulante « Show », puis cliquez sur la case à cocher supérieure.  Recherchez et cliquez sur le TodoListService et cliquez sur la coche en bas pour ajouter l’application.  Sélectionnez « Accès TodoListService » dans la liste déroulante « Autorisations déléguées » et enregistrer la configuration.



Pour générer avec Maven, vous pouvez utiliser le pom.xml au niveau supérieur

  * Dupliquer cette mis en pension dans un répertoire de votre choix :

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  

  * Suivez les étapes décrites à la [section Prerequests vous permet de configurer votre maven pour android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * Le programme d’installation de l’émulateur avec le Kit de développement logiciel 19
  * Accédez au dossier racine où vous cloné le mis en pension
  * Exécutez la commande : mvn nouvelle installation
  * Changer le répertoire de l’exemple de démarrage rapide : cd samples\hello
  * Exécutez la commande : mvn android : déployer android : exécution
  * Vous devriez voir Lancement d’application
  * Entrez les informations d’identification utilisateur de test pour essayer !

JAR packages seront également soumis à côté du package aar.

### <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Étape 4 : Télécharger Android ADAL et l’ajouter à votre espace de travail Éclipse

Nous avons simplifié d’avoir plusieurs options à utiliser cette bibliothèque dans votre projet Android :

* Vous pouvez utiliser le code source pour importer cette bibliothèque dans Eclipse et le lier à votre application.
* Si vous utilisez Studio Android, vous pouvez utiliser le format de package *aar* et référencer les fichiers binaires.

####<a name="option-1-source-zip"></a>Option 1 : Source Zip

Pour télécharger une copie du code source, cliquez sur « ZIP de téléchargement » sur le côté droit de la page ou cliquez [ici](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####<a name="option-2-source-via-git"></a>Option 2 : Les sources via Git

Pour obtenir le code source du Kit de développement via git tapez seulement :

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####<a name="option-3-binaries-via-gradle"></a>Option 3 : Les binaires via Gradle

Vous pouvez obtenir les fichiers binaires de central mis en pension de Maven. Package de AAR peut être inclus comme suit dans votre projet en AndroidStudio :

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####<a name="option-4-aar-via-maven"></a>Option 4 : les aar via Maven

Si vous utilisez le plug-in m2e dans Eclipse, vous pouvez spécifier la dépendance dans votre fichier pom.xml :

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####<a name="option-5-jar-package-inside-libs-folder"></a>Option 5 : jar de package à l’intérieur du dossier de bibliothèques
Vous pouvez obtenir le fichier jar à partir de maven le mis en pension et le déposer dans le dossier *et les bibliothèques* dans votre projet. Vous devez copier les ressources nécessaires à votre projet, ainsi que dans la mesure où les packages jar ne les incluent pas.


### <a name="step-5-add-references-to-android-adal-to-your-project"></a>Étape 5 : Ajouter des références à ADAL Android à votre projet


2. Ajoutez une référence à votre projet et le spécifier comme une bibliothèque Android. Si vous ne savez pas comment effectuer cette opération, [Cliquez ici pour plus d’informations] (http://developer.android.com/tools/projects/projects-eclipse.html)

3. Ajouter la dépendance de projet pour le débogage dans vos paramètres de projet

4. Mettre à jour le fichier AndroidManifest.xml du projet à inclure :

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. Créez une instance de AuthenticationContext à votre activité principale. Les détails de cet appel sont au-delà de la portée de ce fichier Lisez-moi, mais vous pouvez prendre un bon départ en regardant l' [Exemple de Client natif Android](https://github.com/AzureADSamples/NativeClient-Android). Voici un exemple :

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * Remarque : mContext est un champ dans votre activité.

8. Copiez ce bloc de code pour gérer la fin de AuthenticationActivity une fois que l’utilisateur entre des informations d’identification et reçoit le code d’autorisation :

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Pour demander un jeton, vous définissez un rappel

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Enfin, demandez un jeton à l’aide de ce rappel :

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Explication des paramètres :

  * Ressource est requis et la ressource que vous essayez d’accéder.
  * ClientID est obligatoire et est fourni à partir du portail de AzureAD.
  * Vous pouvez définir redirectUri comme votre packagename. Il n’est pas nécessaire pour l’appel d’acquireToken.
  * PromptBehavior vous permet de demander des informations d’identification à ignorer le cache et le cookie.
  * Rappel sera appelée une fois que le code d’autorisation est échangée contre un jeton.

  Le rappel aura un objet de AuthenticationResult qui est accesstoken, la date a expiré et les informations d’idtoken.

Facultatif : **acquireTokenSilent**

Vous pouvez appeler **acquireTokenSilent** pour gérer la mise en cache et actualisation de jeton. Il fournit également les version de synchronisation. Il accepte des ID utilisateur comme paramètre.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Service Broker**: application de portail de Microsoft Intune société fournira le composant broker. ADAL utiliser le compte de service broker, s’il existe un compte d’utilisateur est créé sur ce authentificateur et le développeur choisit ne pas à ignorer. Développeur peut ignorer l’utilisateur broker avec :

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Développeur doit inscrire redirectUri spéciale pour l’utilisation de service broker. Le format de msauth://packagename/Base64UrlencodedSignature est RedirectUri. Vous pouvez obtenir votre redirecturi pour votre application en utilisant le script « brokerRedirectPrint.ps1 » ou utiliser des API appel mContext.getBrokerRedirectUri. Signature est associée à votre certificat de signature.

 Modèle de service broker en cours est d’un utilisateur. AuthenticationContext fournit la méthode API pour obtenir de l’utilisateur de service broker.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 Utilisateur de service Broker sera retournée si le compte est valide.

 Votre manifeste d’application doit avoir des autorisations d’utiliser des comptes d’AccountManager qui suit : http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * UTILISER_DES_RÉFÉRENCES
 * MANAGE_ACCOUNTS


À l’aide de cette procédure pas à pas, vous devez avoir ce que vous devez intégrer parfaitement Azure Active Directory. Pour plus d’exemples de ce travail, visitez le AzureADSamples / référentiel sur GitHub.

## <a name="important-information"></a>Informations importantes

### <a name="customization"></a>Personnalisation

Ressources de bibliothèque de projet peuvent être remplacés par vos ressources d’application. Cela se produit lors de la création de votre application. Pour cette raison, vous pouvez personnaliser à disposition de l’activité d’authentification comme vous le souhaitez. Vous devez vous assurer de conserver l’id des contrôles qui uses(Webview) ADAL.

### <a name="broker"></a>Service Broker

Composant Broker sera livré avec une application de portail de Microsoft Intune société. Compte sera créé dans le Gestionnaire de comptes. Type de compte est « com.microsoft.workaccount ». Il autorise uniquement le compte d’authentification unique. Il créera le cookie d’authentification unique pour cet utilisateur défi de périphérique pour une des applications à la fin.

### <a name="authority-url-and-adfs"></a>ADFS et l’Url de l’autorité

ADFS n’est pas reconnu en tant que production SharePoint Team Services, vous devez activer de découverte d’instance et passez la valeur false au constructeur de AuthenticationContext.

Url de l’autorité doit STS instance et le nom du client : https://login.windows.net/yourtenant.onmicrosoft.com

### <a name="querying-cache-items"></a>Interrogation des éléments de cache

ADAL fournit les fonctions de requête de cache par défaut SharedPreferences avec un simple cache. Vous pouvez obtenir le cache actuel à partir de AuthenticationContext avec :
```Java
 ITokenCacheStore cache = mContext.getCache();
```
Vous pouvez également fournir votre implémentation de cache, si vous souhaitez personnaliser.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### <a name="promptbehavior"></a>PromptBehavior

ADAL offre la possibilité de spécifier comportement de l’invite. PromptBehavior.Auto s’affiche l’interface utilisateur si le jeton d’actualisation n’est pas valide et les informations d’identification utilisateur sont requises. PromptBehavior.Always ignore l’utilisation du cache et toujours afficher l’interface utilisateur.

### <a name="silent-token-request-from-cache-and-refresh"></a>Demande de jeton en mode silencieux à partir du cache et l’actualisation

Cette méthode n’utilise pas les pop de l’interface utilisateur vers le haut et ne nécessitent pas une activité. Il retournera jeton à partir du cache si elle est disponible. Si le jeton est arrivé à expiration, il va tenter d’actualiser. Si le jeton d’actualisation est arrivé à expiration ou a échoué, il renvoie AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

Vous pouvez également effectuer la synchronisation d’appeler cette méthode. Vous pouvez définir null pour un rappel ou utilisez acquireTokenSilentSync.

### <a name="diagnostics"></a>Tests de diagnostic

Les sources principales d’informations pour diagnostiquer les problèmes sont les suivantes :

+ Exceptions
+ Journaux
+ Traces du réseau

Notez également qu’ID de corrélation sont essentiels pour les tests de diagnostic dans la bibliothèque. Vous pouvez définir votre correspondance ID sur une base par demande si vous souhaitez établir une corrélation entre une ADAL demande avec d’autres opérations dans votre code. Si vous ne définissez pas un id de corrélations puis ADAL génère un aléatoire une et tous les messages du journal et les appels de réseau seront marqués avec l’id de corrélation. L’auto généré id change à chaque demande.

#### <a name="exceptions"></a>Exceptions

C’est évidemment le premier test de diagnostic. Nous essayons de fournir des messages d’erreur utiles. Si vous en trouvez une qui n’est pas utile, veuillez soumettre un problème et nous le faire savoir. Veuillez aussi fournir des informations sur le périphérique comme modèle et Kit de développement logiciel #.

#### <a name="logs"></a>Journaux

Vous pouvez configurer la bibliothèque pour générer des messages de journal que vous pouvez utiliser pour diagnostiquer les problèmes. Vous configurez l’enregistrement par l’appel suivant pour configurer un rappel ADAL utilise à chaque message de journal lorsqu’elles sont générées.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
Messages puissent être écrits dans un fichier journal personnalisé, comme indiqué ci-dessous. Malheureusement, il n’existe aucune méthode standard d’accès aux journaux d’un périphérique. Il existe certains services qui peuvent vous y aider. Vous pouvez également inventez vos propres, telles que l’envoi du fichier à un serveur.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### <a name="logging-levels"></a>Niveaux de journalisation

+ Error(exceptions)
+ Warn(Warning)
+ Info (à titre d’Information)
+ Verbose (plus de détails)

Vous définissez le niveau de journal comme suit :
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Tous les messages de journal sont envoyés à logcat en plus des rappels de journal personnalisé.
Vous pouvez obtenir un logcat d’écran fichier journal comme indiqué belog :

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 Obtenir d’autres exemples adb cmds : https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### <a name="network-traces"></a>Traces du réseau

Vous pouvez utiliser différents outils pour capturer le trafic HTTP qui génère l’ADAL.  Cela est particulièrement utile si vous êtes familiarisé avec le protocole OAuth ou si vous avez besoin fournir des informations de diagnostic à Microsoft ou à d’autres canaux de prise en charge.

Fiddler est l’outil de suivi HTTP simple.  Utilisez les liens suivants d’enregistrement correctement ADAL trafic de réseau.  Pour pouvoir être utiles, il est nécessaire de configurer de fiddler, ou tout autre outil tel que Charles, d’enregistrer le trafic SSL crypté.  Remarque : Les suivis générés de cette manière peuvent contenir des informations hautement privilégiées, tels que les jetons d’accès, les noms d’utilisateur et les mots de passe.  Si vous utilisez des comptes de production, ne partagez pas ces traces avec 3 parties.  Si vous avez besoin de fournir une trace à une personne pour obtenir de l’assistance, reproduire le problème avec un compte temporaire avec les noms d’utilisateur et mots de passe que vous êtes prêt à partager.

+ [Configuration de Fiddler pour Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configurer les règles de Fiddler pour ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### <a name="dialog-mode"></a>Mode de la boîte de dialogue
méthode d’acquireToken sans activité prend en charge l’invite de la boîte de dialogue.

### <a name="encryption"></a>Cryptage

ADAL crypte les jetons et les stocker dans la SharedPreferences par défaut. Vous pouvez consulter la classe StorageHelper pour afficher les détails. Android introduit des AndroidKeyStore de 4.3(API18) un stockage sécurisé des clés privées. ADAL utilise pour API18 et ci-dessus. Si vous souhaitez utiliser ADAL pour les versions du Kit de développement logiciel inférieure, vous devez fournir une clé secrète à AuthenticationSettings.INSTANCE.setSecretKey

### <a name="oauth2-bearer-challenge"></a>Défi du support de Oauth2

AuthenticationParameters classe fournit des fonctionnalités pour obtenir l’authorization_uri de stimulation de porteur de Oauth2.

### <a name="session-cookies-in-webview"></a>Cookies de la session en mode Web

Webview Android n’efface pas les cookies de session après la fermeture de l’application. Vous pouvez le gérer avec l’exemple de code ci-dessous :
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Plus d’informations sur les cookies : http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### <a name="resource-overrides"></a>Substitutions de ressources

La bibliothèque ADAL inclut les chaînes en anglais pour les deux messages suivants dans la ProgressDialog.

Votre application doit remplacer les chaînes localisées sont éventuellement.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### <a name="ntlm-dialog"></a>Boîte de dialogue NTLM
Adal version 1.1.0 prend en charge la boîte de dialogue NTLM qui est traitée par l’événement onReceivedHttpAuthRequest à partir de WebViewClient. Chaînes et mise en page de la boîte de dialogue peuvent être personnalisées.

### <a name="cross-app-sso"></a>Cross-application SSO
Découvrez [comment activer SSO entre-app sur Android à l’aide de ADAL](active-directory-sso-android.md)  


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
