<properties
    pageTitle="Intégration du SDK Android Azure Engagement Mobile"
    description="Dernières mises à jour et les procédures pour Android SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-on-android"></a>Comment intégrer l’Engagement sur Android

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)
- [e/s](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Cette procédure décrit la méthode la plus simple pour activer Analytique d’Engagement et le contrôle des fonctions dans votre application d’Android.

> [AZURE.IMPORTANT] Votre niveau de Android API du Kit de développement logiciel minimale doit être de 10 ou supérieur (2.3.3 Android ou version ultérieure).

Les étapes suivantes sont assez pour activer l’état des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, les Sessions, les activités, les pannes et les Technicals. L’état des journaux nécessaires pour calculer d’autres statistiques, telles que les événements, les erreurs et les tâches doit être effectuée manuellement à l’aide de l’API d’Engagement (voir [comment utiliser l’Engagement Mobile avancée API dans votre Android de repérage](mobile-engagement-android-use-engagement-api.md) dans la mesure où ces statistiques sont dépend de l’application.

##<a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Intégrer le SDK de l’Engagement et le service de votre projet Android

Télécharger le Kit de développement Android [ici](https://aka.ms/vq9mfn) Get `mobile-engagement-VERSION.jar` et placez-les dans le `libs` dossier de votre projet Android (créer le dossier libs si elle n’existe pas encore).

> [AZURE.IMPORTANT]
> Si vous générez votre package d’application avec ProGuard, vous devez conserver certaines classes. Vous pouvez utiliser l’extrait de configuration suivant :
>
>
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
            <methods>;
            }

Spécifiez votre chaîne de connexion de mission en appelant la méthode suivante dans l’activité du Lanceur :

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

La chaîne de connexion de votre application s’affiche sur le portail Azure.

-   S’il est manquant, ajoutez les autorisations de Android suivantes (avant la `<application>` tag) :

            <uses-permission android:name="android.permission.INTERNET"/>
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Ajoutez la section suivante (entre la `<application>` et `</application>` balises) :

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

-   Modification `<Your application name>` par le nom de votre application.

> [AZURE.TIP] Le `android:label` attribut vous permet de choisir le nom du service Engagement tel qu’il apparaîtra aux utilisateurs finaux dans l’écran « Exécution de services » de leur téléphone. Il est recommandé de définir cet attribut `"<Your application name>Service"` (par exemple, `"AcmeFunGameService"`).

Spécification de la `android:process` attribut garantit que le service de l’Engagement s’exécute dans son propre processus (en cours d’exécution Engagement dans le même processus que votre application rendra votre thread principal/interface utilisateur potentiellement moins réactifs).

> [AZURE.NOTE] Tout code que vous placez dans `Application.onCreate()` et d’autres rappels de l’application seront exécutés pour les processus de tous les votre application, y compris le contrat service. Il peut avoir des effets secondaires indésirables (tels que les allocations de mémoire inutiles et les threads dans le processus de l’Engagement, des récepteurs de diffusion en double ou des services).

Si vous substituez `Application.onCreate()`, il est recommandé d’ajouter l’extrait de code suivant au début de votre `Application.onCreate()` fonction :

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Vous pouvez faire la même chose `Application.onTerminate()`, `Application.onLowMemory()` et `Application.onConfigurationChanged(...)`.

Vous pouvez également étendre `EngagementApplication` au lieu de l’extension `Application`: le rappel `Application.onCreate()` effectue le contrôle de processus et les appels `Application.onApplicationProcessCreate()` uniquement si le processus en cours n’est pas celui qui héberge le service de l’Engagement, les mêmes règles s’appliquent pour les autres rappels.

##<a name="basic-reporting"></a>Création de rapports de base

### <a name="recommended-method-overload-your-activity-classes"></a>Méthode recommandée : surcharger votre `Activity` classes

Pour activer l’état de tous les journaux requis par l’Engagement pour calculer les utilisateurs, les Sessions, les activités, les pannes et les techniques statistiques, il vous suffit de tous les votre `*Activity` sous-classes héritent correspondantes `Engagement*Activity` classes (par exemple, si votre activité héritée s’étend `ListActivity`, assurez-vous qu’il s’étend `EngagementListActivity`).

**Sans Engagement :**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Avec Engagement :**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [AZURE.IMPORTANT] Lorsque vous utilisez `EngagementListActivity` ou `EngagementExpandableListActivity`, assurez-vous que tout appel à `requestWindowFeature(...);` est effectué avant l’appel à `super.onCreate(...);`, sinon un incident se produit.

Vous trouverez ces classes dans le `src` dossier et vous pouvez les copier dans votre projet. Les classes sont également dans la **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Méthode alternative : appelez `startActivity()` et `endActivity()` manuellement

Si vous ne pouvez pas ou ne voulez pas surcharger votre `Activity` classes, vous pouvez à la place de début et de fin de vos activités en appelant `EngagementAgent`de méthodes directement.

> [AZURE.IMPORTANT] Le Kit de développement Android n’appelle jamais la `endActivity()` méthode, même lorsque l’application est fermée (sur Android, les applications sont en fait jamais fermées). Par conséquent, il est *vivement* recommandé d’appeler le `startActivity()` méthode dans le `onResume` rappel de *tous les* vos activités et la `endActivity()` méthode dans le `onPause()` rappel de *tous les* vos activités. Il s’agit de la seule façon pour vous assurer que les sessions être fuites pas. Si une session est perdue, le service de l’Engagement sera déconnecté jamais le back-end de l’Engagement (dans la mesure où le service reste connecté en tant qu’une session est en attente).

Voici un exemple :

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

Cet exemple très similaire à la `EngagementActivity` classe et ses variantes, dont le code source est fourni dans le `src` dossier.

##<a name="test"></a>Test

Vérifiez maintenant votre intégration par l’exécution de votre application mobile dans un émulateur ou un périphérique et vérifier qu’il enregistre une session sous l’onglet Moniteur.

Les sections suivantes sont facultatives.

##<a name="location-reporting"></a>Reporting sur l’emplacement

Si vous souhaitez créer des emplacements à déclarer, vous devez ajouter quelques lignes de configuration (entre la `<application>` et `</application>` balises).

### <a name="lazy-area-location-reporting"></a>Reporting sur l’emplacement zone différée

Zone paresseux reporting sur l’emplacement permet de signaler le pays, la région et la localité associée aux périphériques. Ce type de reporting sur l’emplacement utilise uniquement des emplacements réseau (basés sur les ID de cellule ou Wi-Fi). La zone est signalée au moins une fois par session. Le périphérique GPS n’est jamais utilisé, et ce type de rapport de l’emplacement a donc très peu (ne pas de vous dire non) impact sur la batterie.

Zones signalées sont utilisés pour calculer des statistiques géographiques sur les utilisateurs, les sessions, les événements et les erreurs. Ils peuvent également servir comme critère dans les campagnes de la portée.

Pour activer la zone différée reporting sur l’emplacement, vous pouvez le faire en utilisant la configuration mentionnée précédemment dans cette procédure :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l’autorisation suivante s’il est manquant :

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou vous pouvez continuer à utiliser ``ACCESS_FINE_LOCATION`` si vous l’utilisez déjà dans votre application.

### <a name="real-time-location-reporting"></a>Reporting sur l’emplacement en temps réel

Reporting sur l’emplacement en temps réel permet de signaler la latitude et la longitude associée aux périphériques. Par défaut, ce type de reporting sur l’emplacement utilise uniquement des emplacements réseau (basés sur les ID de cellule ou Wi-Fi), et la génération d’états n’est active que lorsque l’application s’exécute en premier plan (par exemple, lors d’une session).

Temps réel emplacements ne sont *pas* utilisés pour le calcul des statistiques. Leur seul objectif est de permettre l’utilisation de gardiennage en temps réel \<portée-public-gardiennage virtuel\> critère dans les campagnes de la portée.

Pour activer l’emplacement en temps réel reporting, vous pouvez le faire en utilisant la configuration mentionnée précédemment dans cette procédure :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l’autorisation suivante s’il est manquant :

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou vous pouvez continuer à utiliser ``ACCESS_FINE_LOCATION`` si vous l’utilisez déjà dans votre application.

#### <a name="gps-based-reporting"></a>Création de rapports de base de GPS

Par défaut, reporting en temps réel sur l’emplacement utilise uniquement des emplacements réseau. Pour activer l’utilisation des emplacements GPS de base (qui sont beaucoup plus précises), utilisez l’objet de configuration :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l’autorisation suivante s’il est manquant :

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Rapport d’arrière-plan

Par défaut, reporting en temps réel sur l’emplacement est actif uniquement lorsque l’application s’exécute en premier plan (par exemple, lors d’une session). Pour activer la génération d’états également en arrière-plan, utilisez l’objet de configuration :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Lorsque l’application s’exécute en arrière-plan, seuls les emplacements sur le réseau sont signalées, même si vous avez activé le GPS.

Le rapport d’emplacement arrière-plan est arrêté si l’utilisateur redémarre son périphérique, vous pouvez ajouter cela pour qu’il redémarre automatiquement au démarrage de le :

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Vous devez également ajouter l’autorisation suivante s’il est manquant :

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Autorisations de M Android

En commençant par M Android, certaines autorisations sont gérés lors de l’exécution et nécessite l’approbation de l’utilisateur.

Les autorisations d’exécution seront désactivées par défaut pour les nouvelles installations d’application si vous ciblez le niveau API Android 23. Sinon, il va être activé par défaut.

L’utilisateur peut activer ou désactiver les autorisations dans le menu Paramètres de périphérique. Désactivation des autorisations à partir du menu système arrête les processus d’arrière-plan de l’application, il s’agit d’un comportement système et n’a aucun impact sur la capacité à recevoir des transmission en arrière-plan.

Dans le cadre de l’Engagement Mobile, les autorisations qui requièrent une approbation lors de l’exécution sont les suivantes :

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE`(uniquement lorsque vous ciblez le niveau d’API Android 23 pour ce)

Le stockage externe est utilisé uniquement pour la fonctionnalité de vue d’ensemble de portée. Si vous trouvez pas demander aux utilisateurs de cette autorisation pour être sans interruption de service, vous pouvez le supprimer si vous l’avez utilisé uniquement pour l’Engagement de Mobile, mais au détriment de la désactivation de la fonctionnalité de vue d’ensemble.

Pour les fonctionnalités de l’emplacement, vous devez demander les autorisations d’utilisateur à l’aide d’une boîte de dialogue standard du système. Si l’utilisateur approuve, vous devez indiquer à ``EngagementAgent`` de prendre cette modification en compte en temps réel (sinon la modification fera la prochaine fois que l’utilisateur lance l’application).

Voici un exemple de code à utiliser dans une activité de votre application pour demander des autorisations et transmet le résultat si positif à ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##<a name="advanced-reporting"></a>Reporting avancé

Éventuellement, si vous souhaitez signaler des événements d’application spécifique, des erreurs et des travaux, vous devez utiliser l’API de l’Engagement à travers les méthodes de la `EngagementAgent` classe. Un objet de cette classe peut être récupérés en appelant le `EngagementAgent.getInstance()` méthode statique.

L’API d’Engagement permet d’utiliser toutes les fonctionnalités avancées d’Engagement et est détaillé dans la procédure pour utiliser l’API d’Engagement sur Android (ainsi que dans la documentation technique de le `EngagementAgent` classe).

##<a name="advanced-configuration-in-androidmanifestxml"></a>Configuration avancée (dans AndroidManifest.xml)

### <a name="wake-locks"></a>Verrous de mise en éveil

Si vous voulez être sûr que les statistiques sont envoyées en temps réel lors de l’utilisation de Wi-Fi ou lorsque l’écran est désactivée, ajoutez l’autorisation facultative suivante :

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Rapport d’incident

Si vous souhaitez désactiver les rapports d’incidents, ajouter (entre la `<application>` et `</application>` balises) :

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Seuil de transmission en rafale

Par défaut, les rapports de service Engagement journaux en temps réel. Si votre application rapports journaux très fréquemment, il est préférable pour les journaux de la mémoire tampon et de les signaler à la fois sur une base temporelle normale (Ceci s’appelle le « mode rafale »). Pour ce faire, ajoutez ceci (entre la `<application>` et `</application>` balises) :

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Le mode rafale est légèrement augmenter l’autonomie de batterie mais a un impact sur le moniteur de l’Engagement : toutes les sessions et les travaux la durée est arrondis à la valeur seuil de transmission en rafale (par conséquent, les sessions et les tâches plus courtes que le seuil de transmission en rafale ne soient pas visible). Il est recommandé d’utiliser un seuil de transmission en rafale ne sont plus à 30000 (30 s).

### <a name="session-timeout"></a>Délai d’expiration de session

Par défaut, une session est terminée de 10 s après la fin de sa dernière activité (ce qui se produit généralement en appuyant sur la touche accueil ou principale, en définissant le téléphone inactif ou par passage dans une autre application). C’est pour éviter une division de session chaque fois que la sortie de l’utilisateur et revenir très rapidement à l’application (qui peut se produire lorsqu’il choisir une image, vérifier une notification, etc..). Vous souhaiterez peut-être modifier ce paramètre. Pour ce faire, ajoutez ceci (entre la `<application>` et `</application>` balises) :

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

##<a name="disable-log-reporting"></a>Désactiver les rapports de journal

### <a name="using-a-method-call"></a>À l’aide d’un appel de méthode

Si vous souhaitez que l’Engagement pour arrêter l’envoi de journaux, vous pouvez appeler :

            EngagementAgent.getInstance(context).setEnabled(false);

Cet appel est persistant : il utilise un fichier de préférences partagées.

Si le contrat est actif lorsque vous appelez cette fonction, il peut prendre une minute pour l’arrêt du service. Toutefois, il ne lance le service tout la prochaine fois que vous lancez l’application.

Vous pouvez activer le journal de création de rapports en appelant la même fonction avec `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Intégration dans votre propre`PreferenceActivity`

Au lieu d’appeler cette fonction, vous pouvez également intégrer ce paramètre directement dans votre `PreferenceActivity`.

Vous pouvez configurer l’Engagement d’utiliser votre fichier de préférences (avec le mode de votre choix) dans le `AndroidManifest.xml` de fichier `application meta-data`:

-   Le `engagement:agent:settings:name` clé est utilisée pour définir le nom du fichier de préférences partagées.
-   Le `engagement:agent:settings:mode` clé est utilisée pour définir le mode du fichier Préférences partagés, vous devez utiliser le même mode comme dans votre `PreferenceActivity`. Le mode doit être passé sous la forme d’un nombre : Si vous utilisez une combinaison d’indicateurs de constantes dans votre code, vérifiez la valeur totale.

Engagement de toujours utiliser le `engagement:key` clé boolean dans le fichier de préférences pour gérer ce paramètre.

L’exemple suivant de `AndroidManifest.xml` affiche les valeurs par défaut :

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Vous pouvez alors ajouter un `CheckBoxPreference` dans la disposition de préférence comme la suivante :

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
