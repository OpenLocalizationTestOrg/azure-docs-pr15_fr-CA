<properties
    pageTitle="Azure B2C de répertoire actif : Appeler un API web à partir d’une application d’Android | Microsoft Azure"
    description="Cet article vous explique comment créer un Android 'liste de tâches' application qui appelle une API de web Node.js en utilisant des jetons de support OAuth 2.0. L’application d’Android et de l’API web utilisent Azure Active Directory B2C pour gérer les identités d’utilisateur et d’authentifier les utilisateurs."
    services="active-directory-b2c"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C : Appeler un API web à partir d’une application d’Android

> [AZURE.WARNING] Ce didacticiel requiert certaines mises à jour importantes, spécifiquement pour supprimer l’utilisation de ADAL Android pour B2C.  Nous allons maintenant publier des instructions de FRAICHES d’utilisation de Azure AD B2C dans les applications Android de la semaine suivante, et nous vous recommandons de maintenant qu’à ce moment-là.  Mais si vous souhaitez simplement faire des tests, n’hésitez pas à poursuivre l’article ci-dessous.



À l’aide de B2C d’Azure Active Directory (AD Azure), vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissant à vos applications Android et les API de web en quelques étapes. Cet article vous explique comment créer une « liste de tâches » de Android application qui appelle une API de web Node.js en utilisant des jetons de support OAuth 2.0. L’application d’Android et de l’API web utilisent Azure AD B2C pour gérer les identités d’utilisateur et d’authentifier les utilisateurs.

Ce démarrage rapide nécessite d’avoir un site web API qui est protégé par la publicité Azure B2C pour fonctionner correctement. Nous avons élaboré une pour .NET et Node.js à utiliser. Cette procédure suppose que l’exemple de web API Node.js est configuré. Pour plus d’informations, consultez le [web d’Azure AD B2C API Node.js didacticiel](active-directory-b2c-devquickstarts-api-node.md).

Pour les clients Android qui doivent accéder aux ressources protégées, AD Azure fournit la bibliothèque de l’authentification Active Directory (ADAL). Le seul but de ADAL est de faciliter votre application obtenir les jetons d’accès. De démontrer combien il est facile, dans ce guide, nous créerons une application de liste de tâches Android qui :

- Obtient les jetons d’accès qui appellent une API de liste de tâches à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
- Obtient les listes de tâches des utilisateurs.
- Signes des utilisateurs.

> [AZURE.NOTE] Cet article ne couvre pas la mise en œuvre d’ouverture de session, d’inscription et gestion de profil à l’aide d’Azure AD B2C. Il met l’accent sur la façon d’appeler les API de web une fois que l’utilisateur est authentifié. Si vous ne l’avez pas déjà, vous devez démarrer avec le [.NET web app en route](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les bases d’Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire, ou client. Un répertoire est un conteneur pour tous vos utilisateurs, applications, groupes et bien plus encore. Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de poursuivre dans ce guide.

## <a name="create-an-application"></a>Création d’une application

Ensuite, vous devez créer une application dans votre répertoire B2C. Ainsi, les informations AD Azure dont il a besoin pour communiquer en toute sécurité avec votre application. L’application et le web API sont représentés dans ce cas, par un seul **ID de l’Application** parce qu’elles comprennent une application logique. Pour créer une application, suivez [les instructions](active-directory-b2c-app-registration.md). Veillez à :

- Inclure une **application web**/**API web** dans l’application.
- Entrez `urn:ietf:wg:oauth:2.0:oob` sous la forme d’une **URL de la réponse**. Il s’agit de l’URL par défaut pour cet exemple de code.
- Créer un **secret de l’Application** pour votre application et copiez-le. Vous en aurez besoin ultérieurement. Notez que cette valeur doit être [l’objet d’un échappement XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) avant de l’utiliser.
- Copiez l' **ID de l’Application** qui est assigné à votre application. Vous devez également cela plus tard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dans Azure B2C d’Active Directory, chaque expérience de l’utilisateur est défini par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient trois expériences d’identité : inscrire, vous connecter et connectez-vous à l’aide de Facebook.  Vous devez créer une stratégie de chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lorsque vous créez vos trois stratégies, veillez à :

- Cliquez sur le **nom d’affichage** et autres attributs d’abonnement dans votre stratégie d’inscription.
- Choisissez les demandes d’application de **nom complet** et **l’ID d’objet** dans chaque stratégie. Vous pouvez choisir les autres déclarations.
- Copier le **nom** de chaque stratégie après que l’avoir créé. Il doit avoir le préfixe `b2c_1_`.  Vous devez ensuite ces noms de la stratégie.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Après avoir créé les trois stratégies, vous êtes prêt à créer votre application.

Notez que cet article ne couvre pas comment utiliser les stratégies que vous venez de créer. Pour en savoir plus sur le fonctionnement des stratégies dans Azure AD B2C, démarrer avec le [.NET web app en route](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Télécharger le code

Le code de ce didacticiel [est conservée sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Pour générer l’exemple à mesure que vous progressez, vous pouvez [Télécharger le squelette de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). Vous pouvez également cloner le squelette :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **Vous devez télécharger le squelette pour terminer ce didacticiel.** En raison de la complexité de l’implémentation d’une application d’Android entièrement fonctionnelle, le squelette a code UX qui s’exécutera après avoir terminé ce didacticiel. Il s’agit d’une mesure de gagner du temps pour les développeurs. Le code UX n’est pas pertinentes pour la rubrique comment ajouter B2C à une application d’Android.

L’application terminée est également [disponible sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) ou sur le `complete` branche du même référentiel.

Pour générer avec Maven, vous pouvez utiliser `pom.xml` au niveau supérieur.

  1. Suivez les étapes de [la section conditions requises pour configurer Maven pour Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Permet de paramétrer un émulateur avec le Kit de développement logiciel 21.
  3. Accédez au dossier racine où vous cloné le mis en pension.
  4. Exécutez la commande `mvn clean install`.
  5. Changer le répertoire de l’exemple de démarrage rapide `cd samples\hello`.
  6. Exécutez la commande `mvn android:deploy android:run`.

Vous devez voir l’application à lancer. Entrez les informations d’identification utilisateur de test pour l’essayer.

Les packages Java JAR (Archive) seront également soumises à côté du package Android archivage anticipé.

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>Téléchargez le ADAL Android et l’ajouter à votre espace de travail de Studio Android

Vous disposez des options pour l’utilisation de cette bibliothèque dans votre projet Android :

* Vous pouvez utiliser le code source pour importer la bibliothèque dans Eclipse et le lier à votre application.
* Si vous utilisez Studio Android, vous pouvez utiliser le format de package AAR et référencer les fichiers binaires.

### <a name="option-1-binaries-via-gradle-recommended"></a>Option 1 : Les binaires via Gradle (recommandé)

Vous pouvez obtenir les fichiers binaires de la mis en pension central Maven. Le package AAR peut être inclus dans votre projet dans Studio Android (par exemple, dans `build.gradle`) de cette façon :

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### <a name="option-2-aar-via-maven"></a>Option 2 : Les AAR via Maven

Si vous utilisez la `m2e` plug-in dans Eclipse, vous pouvez spécifier la dépendance dans votre `pom.xml` fichier :

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>Option 3 : Source via Git (dernier recours)

Pour obtenir le code source du Kit de développement via Git, entrez :

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Utilisez la branche **convergence.**

## <a name="set-up-your-configuration-file"></a>Configurer votre fichier de configuration

Utiliser la configuration que vous définissez plus haut dans le portail B2C pour configurer le projet Android.

Ouvrir `helpes/Constants.java` et renseignez les valeurs pour les éléments suivants :

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
- `SCOPES`: Les étendues que vous passez au serveur que vous souhaitez demander à partir du serveur lorsqu’un utilisateur se connecte. Pour l’aperçu B2C, vous transmettez `client_id`. Toutefois, il est supposé changer de `read scopes` à l’avenir. Ce document sera mis à jour lorsque qui se produit.
- `ADDITIONAL_SCOPES`: Étendues supplémentaires que vous souhaitez utiliser pour votre application. Ils doivent servir à l’avenir.
- `CLIENT_ID`: L’ID de l’Application vous avez obtenu à partir du portail.
- `REDIRECT_URL`: La redirection dans laquelle vous pensez le jeton soit publié.
- `EXTRA_QP`: Quoi vous souhaitez transmettre au serveur dans un format codé en URL.
- `FB_POLICY`: La stratégie que vous appelez. Il s’agit de la partie la plus importante pour cette procédure.
- `EMAIL_SIGNIN_POLICY`: La stratégie que vous appelez. Il s’agit de la partie la plus importante pour cette procédure.
- `EMAIL_SIGNUP_POLICY`: La stratégie que vous appelez. Il s’agit de la partie la plus importante pour cette procédure.

## <a name="add-references-to-android-adal-to-your-project"></a>Ajoutez des références aux Android ADAL à votre projet

> [AZURE.NOTE]  ADAL pour Android utilise un modèle basé sur l’intention d’appeler l’authentification. Modes de « poser » l’application pour effectuer le travail. Cet exemple complet, tous les ADAL pour Android, centres et comment gérer les intentions et transmettre des informations entre eux.

Indiquez tout d’abord, Android à la disposition de votre application, y compris les intentions que vous souhaitez utiliser. Ces modes seront expliqués en détail plus loin dans ce didacticiel.

De votre projet de mise à jour `AndroidManifest.xml` pour inclure toutes vos intentions de fichier :

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Comme vous pouvez le voir, vous définissez les cinq activités. Vous allez utiliser tous ces éléments.

- `AuthenticationActivity`: Cette information provient du ADAL, et il fournit la vue de la connexion web.
- `LoginActivity`: Affiche vos stratégies de connexion et les boutons pour chaque stratégie.
- `SettingsActivity`: Vous permet de modifier les paramètres d’application au moment de l’exécution.
- `AddTaskActivity`: Utilisez ce pour ajouter des tâches à votre API REST protégés par AD Azure.
- `ToDoActivity`: Il s’agit de l’activité principale qui affiche les tâches.

## <a name="create-the-sign-in-activity"></a>Création de l’activité de connexion

Créer une activité principale et l’appeler `LoginActivity`.

Créez un fichier appelé `LoginActivity.java`.

Vous devez initialiser l’activité et ajouter des boutons qui contrôlent l’interface utilisateur. Cela est familier si vous avez écrit du code Android avant.

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
Vous avez maintenant créé les boutons qui appellent votre `ToDoActivity` intention (qui appelle ADAL lorsque vous avez besoin d’un jeton). Pour ce faire, elles sous la forme d’une référence et un paramètre supplémentaire à l’aide de votre activité. Ce paramètre supplémentaire est passé par le `intent.putExtra()` méthode. Vous définissez `"thePolicy"` à l’aide de ce que vous avez spécifié dans `Constants.java`. Cette option indique à l’intention de la stratégie à appeler lors de l’authentification.

## <a name="create-the-settings-activity"></a>Création de l’activité de paramètres

Il s’agit d’une activité qui remplit les paramètres de l’interface utilisateur.

Créez un fichier appelé `SettingsActivity.java` pour simple créer, lire, mettre à jour et supprimer des opérations.

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

    //private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        loadSettings();
//      checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//      checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


    }

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

    private void saveSettings(String key, boolean value) {
        SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
                Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
        Editor prefsEditor = prefs.edit();
        prefsEditor.putBoolean(key, value);
        prefsEditor.commit();
    }
}
```

## <a name="create-the-add-task-activity"></a>Création de l’activité d’ajouter-tâche

Cette activité vous permet d’ajouter une tâche à votre point de terminaison API REST.

Créez un fichier appelé `AddTaskActivity.java` et écrire ce qui suit.

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## <a name="create-the-to-do-list-activity"></a>Création de l’activité de liste de tâches

Il s’agit de l’activité plus importante. Vous pouvez utiliser pour obtenir un jeton à partir d’AD Azure pour une stratégie et puis utilisez ce jeton pour appeler le serveur API REST de tâche.

Créez un fichier appelé `ToDoActivity.java` et écrire ce qui suit. (Les appels seront expliqués ultérieurement.)

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 Vous avez peut-être remarqué que cela s’appuie sur les méthodes qui n’ont pas encore été écrit. Ils incluent `updateLoggedInUser()`, `clearSessionCookie()`, et `getTasks()`. Vous allez écrire plus loin dans ce guide. Vous pouvez ignorer en toute sécurité les erreurs dans un Studio Android pour l’instant.

Explication des paramètres :

  - `SCOPES`: Obligatoire, les étendues que vous essayez de demander l’accès pour. Pour l’aperçu B2C, c’est le même que `client_id`, mais cela devrait changer à l’avenir.
  - `POLICY`: La stratégie lorsque vous souhaitez authentifier l’utilisateur.
  - `CLIENT_ID`: Obligatoire, est fourni à partir du portail Azure AD.
  - `redirectUri`: Peut être défini comme le nom de votre package. Il n’est pas nécessaire de prévoir pour le `acquireToken` appeler.
  - `getUserInfo()`: Permet de rechercher si un utilisateur est déjà dans le cache. Le paramètre décrit également comment demander à l’utilisateur si celui-ci n’est pas trouvé ou si le jeton d’accès utilisateur n’est pas valide. Cette méthode sera écrit plus loin dans ce guide.
  - `PromptBehavior.always`: Permet de demander des informations d’identification d’ignorer le cache et le cookie.
  - `Callback`: Appelée après un jeton est échangée contre un code d’autorisation. Il possède un objet `AuthenticationResult`, qui contient le jeton d’accès, date d’expiration et les informations d’ID de jeton.

> [AZURE.NOTE]  L’application de portail Microsoft Intune société fournit le composant broker, et il peut être installé sur le périphérique de l’utilisateur. L’application fournit le seul accès sign-on (SSO) pour toutes les applications sur le périphérique. Les développeurs doivent être préparées pour Intune. ADAL pour Android utilise le compte de service broker s’il existe un compte d’utilisateur créé dans l’authentificateur. Pour utiliser le service broker, le développeur doit enregistrer un spécial `redirectUri` pour le service Broker pour les utiliser. `redirectUri`est au format msauth://packagename/Base64UrlencodedSignature. Vous pouvez obtenir `redirectUri` pour votre application à l’aide du script `brokerRedirectPrint.ps1` ou à l’aide de l’appel API `mContext.getBrokerRedirectUri()`. La signature est liée à votre signature des certificats dans le magasin de lecture de Google.

 Vous pouvez ignorer l’utilisateur broker à l’aide de :

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Afin de réduire la complexité de ce démarrage rapide de B2C, nous avons opté pour ignorer le broker dans notre exemple.

Ensuite, créez des méthodes d’assistance qui obtenir le jeton uniquement au cours de l’authentification des appels à l’API de la tâche.

De la même `ToDoActivity.java` fichier, écrire ce qui suit.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Également ajouter des méthodes qui seront « set » et « get » `AuthenticationResult` (avec votre jeton) au modèle global `Constants`. Même si `ToDoActivity.java` utilise `sResult` dans son flux, vous devez ajouter ces méthodes. Si vous ne le faites pas, vous n’ont pas accès au jeton pour effectuer le travail pour vos autres activités (telles que l’ajout d’une tâche dans `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>Créez une méthode pour retourner un identificateur d’utilisateur

ADAL pour Android représente l’utilisateur sous la forme d’une `UserIdentifier` objet. Cela gère l’utilisateur. Vous pouvez utiliser l’objet pour déterminer si le même utilisateur est utilisé dans les appels. À l’aide de ces informations, vous pouvez compter sur le cache, plutôt qu’effectuer un nouvel appel au serveur. Pour ce faire, nous avons créé le `getUserInfo()` méthode qui renvoie `UserIdentifier`. Vous pouvez l’utiliser avec `acquireToken()`. Nous avons également créé une `getUniqueId()` méthode qui retourne l’ID de `UserIdentifier` dans le cache.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### <a name="write-helper-methods"></a>Écrire des méthodes d’assistance

Ensuite, écrivez des méthodes d’assistance pour vous aider à clarifier les cookies et de fournir `AuthenticationCallback`. Ces méthodes sont utilisées exclusivement pour l’exemple pour vous assurer que vous êtes dans un état propre lorsque vous appelez votre `ToDo` activité.

Dans le même fichier appelé `ToDoActivity.java`, écrire ce qui suit.

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## <a name="call-the-task-api"></a>Appeler l’API de la tâche

Une fois que votre activité prête à saisir des jetons, vous pouvez écrire votre API pour accéder au serveur de la tâche.

`getTasks`fournit un tableau qui représente les tâches de votre serveur.

Démarrer avec `getTasks`.

Dans le même fichier appelé `ToDoActivity.java`, écrire ce qui suit.

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

Également d’écrire une méthode qui initialisera les tables lors de la première exécution.

Dans le même fichier appelé `ToDoActivity.java`, écrire ce qui suit.

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

Vous pouvez voir que le code requiert des méthodes supplémentaires pour faire son travail. Écrire ces suivant.

### <a name="create-an-endpoint-url-generator"></a>Créer un générateur d’URL de point de terminaison

Vous devez générer l’URL de point de terminaison que vous allez vous connecter à. Le faire dans le même fichier de classe.

**Dans le même fichier** appelé `ToDoActivity.java`, écrire ce qui suit.

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


Notez que vous ajoutez le jeton d’accès à la demande dans le code présenté dans la section suivante.

## <a name="write-some-ux-methods"></a>Écrire des méthodes UX

Android vous demande de traiter certains rappels pour l’application de fonctionner. Il s’agit de `createAndShowDialog` et `onResume()`. Cela est familier si vous avez écrit du code Android avant.

Dans le même fichier appelé `ToDoActivity.java`, écrire ce qui suit.

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

Ensuite, gérer vos rappels de la boîte de dialogue.

Dans le même fichier appelé `ToDoActivity.java`, écrire ce qui suit.

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

Vous devez maintenant avoir un `ToDoActivity.java` compilé. L’ensemble du projet doit également compiler à ce stade.

## <a name="run-the-sample-app"></a>Exécutez l’exemple d’application

Enfin, générez et exécutez l’application dans un Studio Android ou Eclipse. S’inscrire ou se connecter l’application. Créer des tâches de l’utilisateur connecté. Vous déconnecter et vous reconnecter en tant qu’autre utilisateur et puis créer des tâches pour cet utilisateur.

Notez que les tâches sont stockées par utilisateur sur le API, car l’API extrait l’identité de l’utilisateur à partir du jeton d’accès qu’il reçoit.

Pour référence, l’exemple complet [est fourni sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Vous pouvez également cloner GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## <a name="important-information"></a>Informations importantes


### <a name="encryption"></a>Cryptage

ADAL crypte les jetons et les stocker dans la `SharedPreferences` par défaut. Vous pouvez consulter la `StorageHelper` classe pour afficher les détails. Android introduit des **AndroidKeyStore de 4.3(API18)** un stockage sécurisé des clés privées. ADAL utilise pour API18 et ci-dessus. Si vous souhaitez utiliser ADAL pour les versions du Kit de développement logiciel inférieure, vous devez fournir une clé secrète à `AuthenticationSettings.INSTANCE.setSecretKey`.

### <a name="session-cookies-in-web-view"></a>Cookies de session dans l’affichage web

Affichage web Android n’efface pas les cookies de session après la fermeture de l’application. Vous pouvez le gérer avec l’exemple de code suivant.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[En savoir plus sur les cookies](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).
