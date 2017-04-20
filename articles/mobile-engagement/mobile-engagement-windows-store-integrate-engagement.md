<properties 
    pageTitle="Intégration de kit de développement logiciel Windows applications universel Engagement" 
    description="Comment intégrer applications universel de Windows Azure Engagement Mobile"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="windows-universal-apps-engagement-sdk-integration"></a>Intégration de kit de développement logiciel Windows applications universel Engagement

> [AZURE.SELECTOR] 
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md) 
- [e/s](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Cette procédure décrit la méthode la plus simple pour activer Analytique d’Engagement et le contrôle des fonctions dans votre application Windows universel.

Les étapes suivantes sont suffisantes pour activer le rapport des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, les Sessions, les activités, les pannes et les Technicals. L’état des journaux nécessaires pour calculer d’autres statistiques, telles que les événements, les erreurs et les tâches doit être effectuée manuellement à l’aide de l’API d’Engagement (voir [comment utiliser l’Engagement Mobile avancée API dans votre application Windows universel de marquage](mobile-engagement-windows-store-use-engagement-api.md) dans la mesure où ces statistiques sont dépend de l’application.

## <a name="supported-versions"></a>Versions prises en charge

L’Engagement SDK pour Windows universel applications Mobile peuvent uniquement être intégrées dans Windows Runtime et les applications universel Windows plate-forme :

-   Windows 8
-   8.1 de Windows
-   Windows Phone 8.1
-   Windows 10 (familles mobiles et de bureau)

> [AZURE.NOTE] Si vous ciblez Silverlight de Windows Phone, reportez-vous à la [procédure d’intégration de Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md).

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Installez le SDK de Apps Mobile Engagement universel

### <a name="all-platforms"></a>Toutes les plates-formes

L’Engagement SDK pour Windows Universal application Mobile est disponible sous la forme d’un package Nuget appelé *MicrosoftAzure.MobileEngagement*. Vous pouvez l’installer à partir de Gestionnaire de package Nuget Visual Studio.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x et Windows Phone 8.1

NuGet déploie automatiquement les ressources du Kit de développement logiciel de la `Resources` dossier à la racine de votre projet d’application.

### <a name="windows-10-universal-windows-platform-applications"></a>Applications de plate-forme de Windows universel Windows 10

NuGet ne déploie pas automatiquement les ressources du Kit de développement logiciel dans votre application UWP encore. Vous devrez le faire manuellement jusqu'à ce que le déploiement des ressources est réintroduit dans NuGet :

1.  Ouvrez votre Explorateur de fichiers.
2.  Accédez à l’emplacement suivant (**x.x.x** correspond à la version d’Engagement que vous installez) : *% USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3.  Glisser-déplacer le dossier de **ressources** à partir de l’Explorateur de fichiers à la racine de votre projet dans Visual Studio.
4.  Dans Visual Studio, sélectionnez votre projet et activer l’icône **Afficher tous les fichiers** en haut de l' **Explorateur de solutions**.
5.  Certains fichiers ne sont pas inclus dans le projet. Pour importer à la fois avec le bouton droit à cliquer sur le dossier **ressources** , **exclure du projet** puis un autre avec le bouton droit sur le dossier **ressources** , **inclure dans le projet** à nouveau inclure tout le dossier. Tous les fichiers dans le dossier de **ressources** sont désormais inclus dans votre projet.

## <a name="add-the-capabilities"></a>Ajouter les fonctionnalités

Le Kit de développement de mission a besoin de certaines fonctionnalités du SDK Windows pour fonctionner correctement.

Ouvrir votre `Package.appxmanifest` de fichiers et vérifiez que les fonctionnalités suivantes sont déclarées :

-   `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Initialiser l’Engagement du Kit de développement logiciel

### <a name="engagement-configuration"></a>Configuration de l’engagement

La configuration de l’Engagement est centralisée dans la `Resources\EngagementConfiguration.xml` fichier de votre projet.

Modifier ce fichier pour spécifier :

-   Votre chaîne de connexion d’application entre les balises `<connectionString>` et `<\connectionString>`.

Si vous voulez le spécifier lors de l’exécution au lieu de cela, vous pouvez appeler la méthode suivante avant l’initialisation de l’agent d’Engagement :
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

La chaîne de connexion de votre application s’affiche sur le portail classique d’Azure.

### <a name="engagement-initialization"></a>Initialisation de l’engagement

Lorsque vous créez un nouveau projet, un `App.xaml.cs` fichier est généré. Cette classe hérite de `Application` et contient plusieurs méthodes importantes. Elle servira également pour initialiser le SDK de l’Engagement.

Modifier la `App.xaml.cs`:

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Définissez une méthode pour partager l’initialisation de l’Engagement qu’une seule fois pour tous les appels :

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
        
          // or
        
          EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Appelez `InitEngagement` dans les `OnLaunched` méthode :

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
          InitEngagement(e);
        }

-   Lorsque votre application est lancée à l’aide d’un modèle personnalisé, une autre application ou la ligne de commande la `OnActivated` méthode est appelée. Vous devrez également lancer le SDK de l’Engagement, lorsque votre application est activée. Pour ce faire, vous devez substituer `OnActivated` méthode :

        protected override void OnActivated(IActivatedEventArgs args)
        {
          InitEngagement(args);
        }

> [AZURE.IMPORTANT] Nous déconseillons fortement pour ajouter l’initialisation de l’Engagement à un autre endroit de votre application.

## <a name="basic-reporting"></a>Création de rapports de base

### <a name="recommended-method-overload-your-page-classes"></a>Méthode recommandée : surcharger votre `Page` classes

Pour activer l’état de tous les journaux requis par l’Engagement pour calculer les utilisateurs, les Sessions, les activités, les pannes et les techniques statistiques, vous pouvez simplement effectuer tous les votre `Page` sous-classes héritent de le `EngagementPage` classes.

Voici un exemple de la façon de le faire pour une page de votre application. Vous pouvez faire la même chose pour toutes les pages de votre application.

#### <a name="c-source-file"></a>Fichier Source C#

Modifier votre page `.xaml.cs` fichier :

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Remplacer `Page` avec `EngagementPage`:

**Sans Engagement :**
    
        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Avec Engagement :**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] Si votre page remplace la `OnNavigatedTo` méthode, veillez à appeler `base.OnNavigatedTo(e)`. Dans le cas contraire, l’activité n’est pas signalée (le `EngagementPage` appelle `StartActivity` à l’intérieur de sa `OnNavigatedTo` méthode).

#### <a name="xaml-file"></a>Fichier XAML

Modifier votre page `.xaml` fichier :

-   Ajoutez à vos déclarations d’espaces de noms :

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Remplacer `Page` avec `engagement:EngagementPage`:

**Sans Engagement :**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Avec Engagement :**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Substituer le comportement par défaut

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

Si vous ne pouvez pas ou ne voulez pas surcharger votre `Page` classes, vous pouvez démarrer à la place de vos activités en appelant `EngagementAgent` méthodes directement.

Nous vous recommandons d’appeler `StartActivity` à l’intérieur de votre `OnNavigatedTo` méthode de votre Page.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Assurez-vous que vous terminez votre session correctement.
> 
> Le Kit de développement logiciel universel Windows appelle automatiquement le `EndActivity` méthode lors de la fermeture de l’application. Par conséquent, il est **vivement** recommandé d’appeler le `StartActivity` méthode chaque fois que l’activité de l’utilisateur modifier, **jamais** appeler le `EndActivity` méthode, cette méthode envoie au serveur de l’Engagement qu’utilisateur actuel a quitter l’application, ce sera a une incidence sur tous les journaux de l’application.

## <a name="advanced-reporting"></a>Reporting avancé

Éventuellement, vous pouvez souhaiter d’événements spécifiques de l’application rapport, les erreurs et les tâches, pour ce faire, utiliser les autres méthodes trouvés dans la `EngagementAgent` classe. L’API de l’Engagement permet d’utiliser toutes les fonctionnalités avancées de l’Engagement.

Pour plus d’informations, voir [comment utiliser l’Engagement Mobile avancée API dans votre application Windows universel de marquage](mobile-engagement-windows-store-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configuration avancée

### <a name="disable-automatic-crash-reporting"></a>Désactiver le rapport d’incident automatique

Vous pouvez désactiver la fonctionnalité d’Engagement de rapport d’incident automatique. Puis, lorsqu’une exception non gérée se produit, Engagement de ne rien faire.

> [AZURE.WARNING] Si vous envisagez de désactiver cette fonctionnalité, n’oubliez pas que lorsqu’un incident non géré se produit dans votre application, Engagement n’envoie pas que l’incident **et** ne ferme pas la session et les tâches.

Pour désactiver le rapport d’incident automatique, vous devez simplement personnaliser votre configuration en fonction de la façon dont vous l’avez déclaré :

#### <a name="from-engagementconfigurationxml-file"></a>À partir de `EngagementConfiguration.xml` fichier

Blocage de rapport la valeur `false` entre `<reportCrash>` et `</reportCrash>` balises.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>À partir de `EngagementConfiguration` objet au moment de l’exécution

Blocage de rapport la valeur false à l’aide de l’objet EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Mode rafale

Par défaut, les rapports de service Engagement journaux en temps réel. Si votre application rapports journaux très fréquemment, il est préférable pour les journaux de la mémoire tampon et de les signaler à la fois sur une base temporelle normale (Ceci s’appelle le « mode rafale »).

Pour ce faire, appelez la méthode :

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L’argument est une valeur en **millisecondes**. À tout moment, si vous souhaitez réactiver l’enregistrement en temps réel, appelez simplement la méthode sans paramètre ou avec la valeur 0.

Le mode rafale est légèrement augmenter l’autonomie de batterie mais a un impact sur le moniteur de l’Engagement : toutes les sessions et les travaux la durée est arrondis à la valeur seuil de transmission en rafale (par conséquent, les sessions et les tâches plus courtes que le seuil de transmission en rafale ne soient pas visible). Il est recommandé d’utiliser un seuil de transmission en rafale ne sont plus à 30000 (30 s). Vous devez être conscient que des journaux enregistrés sont limités à 300 articles. Si l’envoi est trop long, vous pouvez perdre certains journaux.

> [AZURE.WARNING] Impossible de configurer le seuil de transmission en rafale pour une période inférieure à 1 s. Si vous tentez de le faire, le Kit de développement affiche une trace de l’erreur et rétablit automatiquement la valeur par défaut, c'est-à-dire 0. Cela déclenche le SDK pour signaler les journaux en temps réel.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
 
