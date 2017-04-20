<properties
    pageTitle="Options avancées de création de rapports pour Azure Mobile Engagement Android SDK"
    description="Décrit comment effectuer la génération de rapports avancée pour capturer analytique pour Azure Mobile Engagement Android SDK"
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
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-engagement-on-android"></a>Rapports avec Engagement sur Android avancée

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)
- [e/s](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Cette rubrique décrit les scénarios de création de rapports supplémentaires dans votre application d’Android. Vous pouvez appliquer ces options à l’application créée dans le didacticiel [Mise en route](mobile-engagement-android-get-started.md) .

## <a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

Le didacticiel vous terminé a été délibérément simple et direct, mais sont des avancées des options que vous pouvez choisir.

## <a name="modifying-your-activity-classes"></a>Modification de votre `Activity` classes

Dans le [didacticiel de mise en route](mobile-engagement-android-get-started.md), vous n’aviez qu’à faire consistait à rendre votre `*Activity` les sous-classes héritent correspondantes `Engagement*Activity` classes. Par exemple, si votre activité héritée étendu `ListActivity`, vous rend étendre `EngagementListActivity`.

> [AZURE.IMPORTANT] Lorsque vous utilisez `EngagementListActivity` ou `EngagementExpandableListActivity`, assurez-vous que tout appel à `requestWindowFeature(...);` est effectué avant l’appel à `super.onCreate(...);`, dans le cas contraire, un blocage se produit.

Vous trouverez ces classes dans le `src` dossier et vous pouvez les copier dans votre projet. Les classes sont également dans la **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Méthode alternative : appelez `startActivity()` et `endActivity()` manuellement

Si vous ne pouvez pas ou ne voulez pas surcharger votre `Activity` classes, vous pouvez à la place de début et de fin de vos activités en appelant le `EngagementAgent`de méthodes directement.

> [AZURE.IMPORTANT] Le Kit de développement Android n’appelle jamais la `endActivity()` méthode, même lorsque l’application est fermée (sur Android, les applications ne sont jamais fermées). Par conséquent, il est *vivement* recommandé d’appeler le `startActivity()` méthode dans le `onResume` rappel de *tous les* vos activités et la `endActivity()` méthode dans le `onPause()` rappel de *tous les* vos activités. Il s’agit de la seule façon pour vous assurer que les sessions ne sont pas fuites. Si une session est divulguée, le service de l’Engagement jamais se déconnecte du serveur principal chargé de l’Engagement (dans la mesure où le service reste connecté en tant qu’une session est en attente).

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

Cet exemple est similaire à la `EngagementActivity` classe et ses variantes, dont le code source est fourni dans le `src` dossier.

## <a name="using-applicationoncreate"></a>À l’aide de Application.onCreate()

Tout code que vous placez dans `Application.onCreate()` et exécution de rappels dans une autre application pour les processus de tous les votre application, y compris le contrat service. Il peut avoir des effets secondaires indésirables, tels que les allocations de mémoire inutiles et les threads dans les processus de l’Engagement, ou des récepteurs de diffusion en double ou des services.

Si vous substituez `Application.onCreate()`, nous vous recommandons d’ajouter l’extrait de code suivantes au début de votre `Application.onCreate()` fonction :

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Vous pouvez faire la même chose `Application.onTerminate()`, `Application.onLowMemory()`, et `Application.onConfigurationChanged(...)`.

Vous pouvez également étendre `EngagementApplication` au lieu de l’extension `Application`: le rappel `Application.onCreate()` effectue le contrôle de processus et les appels `Application.onApplicationProcessCreate()` uniquement si le processus en cours n’est pas celui qui héberge le service de l’Engagement, les mêmes règles s’appliquent pour les autres rappels.

## <a name="tags-in-the-androidmanifestxml-file"></a>Balises dans le fichier AndroidManifest.xml

Dans la balise de service dans le fichier AndroidManifest.xml, le `android:label` attribut vous permet de choisir le nom du service Engagement tel qu’il apparaît aux utilisateurs finaux dans l’écran « Exécution de services » de leur téléphone. Il est recommandé de définir cet attribut `"<Your application name>Service"` (par exemple, `"AcmeFunGameService"`).

Spécification de la `android:process` attribut garantit que le service Engagement s’exécute dans son propre processus (en cours d’exécution Engagement dans le même processus que votre application rend votre thread principal/interface utilisateur potentiellement moins réactifs).

## <a name="building-with-proguard"></a>Bâtiment avec ProGuard

Si vous générez votre package d’application avec ProGuard, vous devez conserver certaines classes. Vous pouvez utiliser l’extrait de configuration suivant :

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
    }
