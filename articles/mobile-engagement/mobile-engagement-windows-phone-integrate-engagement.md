<properties 
    pageTitle="Intégration de kit de développement logiciel Windows Phone Silverlight Engagement" 
    description="Comment intégrer Azure Engagement Mobile avec les applications Silverlight de Windows Phone"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-engagement-sdk-integration"></a>Intégration de kit de développement logiciel Windows Phone Silverlight Engagement

> [AZURE.SELECTOR] 
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md) 
- [e/s](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Cette procédure décrit la méthode la plus simple pour activer Analytique d’Engagement Azure Mobile et la surveillance des fonctions dans votre application Silverlight de Windows Phone.

Les étapes suivantes sont suffisantes pour activer le rapport des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, les Sessions, les activités, les pannes et les Technicals. L’état des journaux nécessaires pour calculer d’autres statistiques, telles que les événements, les erreurs et les tâches doit être effectuée manuellement à l’aide de l’API d’Engagement (voir [comment utiliser l’Engagement Mobile avancée API dans votre application Silverlight de Windows Phone de repérage](mobile-engagement-windows-phone-use-engagement-api.md) ci-dessous) dans la mesure où ces statistiques dépendent de l’application.

##<a name="supported-versions"></a>Versions prises en charge

Le Mobile Engagement SDK de Silverlight pour Windows peut uniquement être intégré dans les applications qui ciblent :

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE] Si vous ciblez Windows Phone 8.1 (non-Silverlight), reportez-vous à la [procédure d’intégration universel de Windows](mobile-engagement-windows-store-integrate-engagement.md).

##<a name="install-the-mobile-engagement-silverlight-sdk"></a>Installer le Kit de développement de Silverlight Mobile Engagement

Le Mobile Engagement SDK de Silverlight pour Windows est disponible sous la forme d’un package Nuget appelé *MicrosoftAzure.MobileEngagement*. Vous pouvez l’installer à partir de Gestionnaire de package Nuget Visual Studio. 

##<a name="add-the-capabilities"></a>Ajouter les fonctionnalités

Le SDK de l’Engagement a besoin de certaines fonctionnalités du kit SDK de Silverlight pour Windows Phone afin de fonctionner correctement.

Ouvrir votre `WMAppManifest.xml` de fichiers et vérifiez que les fonctionnalités suivantes sont déclarées dans le `Capabilities` panneau :

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##<a name="initialize-the-engagement-sdk"></a>Initialiser l’Engagement du Kit de développement logiciel

### <a name="engagement-configuration"></a>Configuration de l’engagement

La configuration de l’Engagement est centralisée dans la `Resources\EngagementConfiguration.xml` fichier de votre projet.

Modifier ce fichier pour spécifier :

-   Votre chaîne de connexion d’application entre les balises `<connectionString>` et `<\connectionString>`.

Si vous voulez le spécifier lors de l’exécution au lieu de cela, vous pouvez appeler la méthode suivante avant l’initialisation de l’agent d’Engagement :

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

La chaîne de connexion de votre application s’affiche sur le portail classique d’Azure.

### <a name="engagement-initialization"></a>Initialisation de l’engagement

Lorsque vous créez un nouveau projet, un `App.xaml.cs` fichier est généré. Cette classe hérite de `Application` et contient plusieurs méthodes importantes. Elle servira également pour initialiser le SDK de l’Engagement.

Modifier la `App.xaml.cs`:

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Insérer `EngagementAgent.Instance.Init` dans les `Application_Launching` méthode :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
          EngagementAgent.Instance.Init();
        }

-   Insérer `EngagementAgent.Instance.OnActivated` dans les `Application_Activated` méthode :

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
        }

> [AZURE.WARNING] Nous déconseillons fortement pour ajouter l’initialisation de l’Engagement à un autre endroit de votre application. Toutefois, sachez que la `EngagementAgent.Instance.Init` méthode s’exécute sur un thread dédié et non sur le thread d’interface utilisateur.

##<a name="basic-reporting"></a>Création de rapports de base

### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Méthode recommandée : surcharger votre `PhoneApplicationPage` classes

Pour activer l’état de tous les journaux requis par l’Engagement pour calculer les utilisateurs, les Sessions, les activités, les pannes et les techniques statistiques, vous pouvez simplement effectuer tous les votre `PhoneApplicationPage` sous-classes héritent de le `EngagementPage` classes.

Voici un exemple de la façon de le faire pour une page de votre application. Vous pouvez faire la même chose pour toutes les pages de votre application.

#### <a name="c-source-file"></a>Fichier Source C#

Modifier votre page `.xaml.cs` fichier :

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Remplacer `PhoneApplicationPage` avec `EngagementPage` :

**Sans Engagement :**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Avec Engagement :**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.WARNING] Si votre page hérite de la `OnNavigatedTo` méthode, veillez à laisser le `base.OnNavigatedTo(e)` appeler. Dans le cas contraire, l’activité n’est pas signalée. En effet, le `EngagementPage` appelle `StartActivity` à l’intérieur de la `OnNavigatedTo` méthode.

#### <a name="xaml-file"></a>Fichier XAML

Modifier votre page `.xaml` fichier :

-   Ajoutez à vos déclarations d’espaces de noms :

        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Remplacer `phone:PhoneApplicationPage` avec `engagement:EngagementPage` :

**Sans Engagement :**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Avec Engagement :**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
        
            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Substituer le comportement par défaut

Par défaut, le nom de classe de la page est signalé en tant que nom de l’activité, aucun extra. Si la classe utilise le suffixe « Page », Engagement sera également la supprimer.

Si vous souhaitez substituer le comportement par défaut pour le nom, ajoutez simplement ceci à votre code :

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Si vous souhaitez signaler des informations supplémentaires à votre activité, vous pouvez ajouter cela à votre code :

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Ces méthodes sont appelées à partir du `OnNavigatedTo` méthode de votre page.

### <a name="alternate-method-call-startactivity-manually"></a>Méthode alternative : appelez `StartActivity()` manuellement

Si vous ne pouvez pas ou ne voulez pas surcharger votre `PhoneApplicationPage` classes, vous pouvez démarrer à la place de vos activités en appelant `EngagementAgent` méthodes directement.

Nous vous recommandons d’appeler `StartActivity` à l’intérieur de votre `OnNavigatedTo` PROCEDE de votre PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [AZURE.IMPORTANT] Assurez-vous que vous terminez votre session correctement.
>
> Le Kit de développement appelle automatiquement le `EndActivity` méthode lors de la fermeture de l’application. Par conséquent, il est **vivement** recommandé d’appeler le `StartActivity` méthode chaque fois que l’activité de l’utilisateur modifier, **jamais** appeler le `EndActivity` méthode. Cette méthode envoie un message vers le serveur de l’Engagement que l’utilisateur actuel a quitté l’application et que cela a une incidence sur tous les journaux de l’application.

##<a name="advanced-reporting"></a>Reporting avancé

Éventuellement, vous pouvez souhaiter d’événements spécifiques de l’application rapport, les erreurs et les tâches, pour ce faire, utiliser les autres méthodes trouvés dans la `EngagementAgent` classe. L’API de l’Engagement permet d’utiliser toutes les fonctionnalités avancées de l’Engagement.

Pour plus d’informations, voir [comment utiliser l’Engagement Mobile avancée balisage API dans votre application Silverlight de Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configuration avancée

### <a name="disable-automatic-crash-reporting"></a>Désactiver le rapport d’incident automatique

Vous pouvez désactiver la fonctionnalité d’Engagement de rapport d’incident automatique. Puis, lorsqu’une exception non gérée se produit, Engagement de ne rien faire.

> [AZURE.WARNING] Si vous envisagez de désactiver cette fonctionnalité, n’oubliez pas que lorsqu’un incident non géré se produit dans votre application, Engagement n’envoie pas l’incident **et** qu’il ne ferme pas la session et les tâches.

Pour désactiver le rapport d’incident automatique, vous devez simplement personnaliser votre configuration en fonction de la façon dont vous l’avez déclaré :

#### <a name="from-engagementconfigurationxml-file"></a>À partir de `EngagementConfiguration.xml` fichier

Blocage de rapport la valeur `false` entre `<reportCrash>` et `</reportCrash>` balises.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>À partir de `EngagementConfiguration` objet au moment de l’exécution

Blocage de rapport la valeur false à l’aide de l’objet EngagementConfiguration.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Mode rafale

Par défaut, les rapports de service Engagement journaux en temps réel. Si votre application rapports journaux très fréquemment, il est préférable pour les journaux de la mémoire tampon et de les signaler à la fois sur une base temporelle normale (Ceci s’appelle le « mode rafale »).

Pour ce faire, appelez la méthode :

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L’argument est une valeur en **millisecondes**. À tout moment, si vous souhaitez réactiver l’enregistrement en temps réel, appelez simplement la méthode sans paramètre ou avec la valeur 0.

Le mode rafale est légèrement augmenter l’autonomie de batterie mais a un impact sur le moniteur de l’Engagement : toutes les sessions et les travaux la durée est arrondis à la valeur seuil de transmission en rafale (par conséquent, les sessions et les tâches plus courtes que le seuil de transmission en rafale ne soient pas visible). Il est recommandé d’utiliser un seuil de transmission en rafale ne sont plus à 30000 (30 s). Vous devez être conscient que des journaux enregistrés sont limités à 300 articles. Si l’envoi est trop long, vous pouvez perdre certains journaux.

> [AZURE.WARNING] Le seuil de transmission en rafale ne peut pas être configuré pour une période inférieure à une seconde. Si vous tentez de le faire, le Kit de développement affiche une trace de l’erreur et sera automatiquement rétablie à la valeur par défaut, c'est-à-dire zéro seconde. Cela déclenche le SDK pour signaler les journaux en temps réel.
 
