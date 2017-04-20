<properties
    pageTitle="Emplacement de création de rapports pour le Kit de développement logiciel Android Azure Engagement Mobile"
    description="Explique comment configurer l’emplacement de création de rapports pour Azure Mobile Engagement Android SDK"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Emplacement de création de rapports pour le Kit de développement logiciel Android Azure Engagement Mobile

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

Cette rubrique décrit comment effectuer l’emplacement de création de rapports pour votre application d’Android.

## <a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Reporting sur l’emplacement

Si vous souhaitez créer des emplacements à déclarer, vous devez ajouter quelques lignes de configuration (entre la `<application>` et `</application>` balises).

### <a name="lazy-area-location-reporting"></a>Reporting sur l’emplacement zone différée

Emplacement de la zone différée reporting permet aux rapports le pays, la région et la localité associées aux périphériques. Ce type de reporting sur l’emplacement utilise uniquement des emplacements réseau (basés sur les ID de cellule ou Wi-Fi). La zone est signalée au moins une fois par session. Le périphérique GPS n’est jamais utilisé, et par conséquent ce type de rapport d’emplacement a un faible impact sur la batterie.

Zones signalées permettent de calculer des statistiques géographiques sur les utilisateurs, les sessions, les événements et les erreurs. Ils peuvent également servir comme critère dans les campagnes de la portée.

Vous activez l’emplacement de la zone différée reporting à l’aide de la configuration mentionnée précédemment dans cette procédure :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également spécifier une autorisation d’emplacement. Ce code utilise ``COARSE`` autorisation :

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Si votre application l’exige, vous pouvez utiliser ``ACCESS_FINE_LOCATION`` à la place.

### <a name="real-time-location-reporting"></a>Reporting sur l’emplacement en temps réel

Reporting sur l’emplacement en temps réel permet de reporting de la latitude et la longitude associées aux périphériques. Par défaut, ce type de reporting sur l’emplacement utilise uniquement des emplacements réseau, basés sur les ID de cellule ou Wi-Fi. La déclaration est active uniquement lorsque l’application s’exécute en premier plan (par exemple, lors d’une session).

Emplacements en temps réel ne sont *pas* utilisés pour le calcul des statistiques. Leur seul objectif est de permettre l’utilisation de gardiennage en temps réel \<portée-public-gardiennage virtuel\> critère dans les campagnes de la portée.

Pour activer l’emplacement en temps réel de création de rapports, ajouter une ligne de code pour où vous définissez la chaîne de connexion d’Engagement dans l’activité du Lanceur. Le résultat ressemble à ceci :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>Création de rapports de base de GPS

Par défaut, reporting sur l’emplacement en temps réel utilise uniquement des emplacements sur le réseau. Pour activer l’utilisation des emplacements GPS, qui sont beaucoup plus précises, utilisez l’objet de configuration :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l’autorisation suivante s’il est manquant :

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Rapport d’arrière-plan

Par défaut, reporting sur l’emplacement en temps réel est actif uniquement lorsque l’application s’exécute en premier plan (par exemple, lors d’une session). Pour activer la génération d’états également en arrière-plan, utilisez cet objet de configuration :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Lorsque l’application s’exécute en arrière-plan, seuls les emplacements sur le réseau sont signalées, même si vous avez activé le GPS.

Si l’utilisateur redémarre son périphérique, le rapport d’emplacement en arrière-plan est arrêté. Pour qu’il redémarre automatiquement au moment de l’amorçage, ajoutez ce code.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Vous devez également ajouter l’autorisation suivante s’il est manquant :

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Autorisations de M Android

En commençant par M Android, certaines autorisations sont gérées lors de l’exécution et ont besoin d’approbation de l’utilisateur.

Si vous ciblez le niveau API Android 23, les autorisations d’exécution sont désactivées par défaut pour les nouvelles installations de l’application. Sinon, elles sont activées par défaut.

Vous pouvez activer ou désactiver les autorisations dans le menu Paramètres de périphérique. Si vous désactivez les autorisations dans le menu système arrête les processus d’arrière-plan de l’application, qui est un comportement du système et n’a aucun impact sur la capacité à recevoir des transmission en arrière-plan.

Dans le contexte de localisation de mobiles Engagement reporting, les autorisations qui requièrent une approbation lors de l’exécution sont les suivantes :

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Demander des autorisations de l’utilisateur à l’aide d’une boîte de dialogue standard du système. Si l’utilisateur approuve, indiquer à ``EngagementAgent`` de prendre cette modification en compte en temps réel. Dans le cas contraire, la modification est traitée la prochaine fois que l’utilisateur lance l’application.

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
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
