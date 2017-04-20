<properties 
    pageTitle="Procédures de mise à niveau des applications universel de Windows SDK" 
    description="Procédures de mise à niveau de Universal applications SDK Windows pour Azure Engagement Mobile"                     
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

#<a name="windows-universal-apps-sdk-upgrade-procedures"></a>Procédures de mise à niveau des applications universel de Windows SDK

Si vous avez déjà intégré une version plus ancienne de l’Engagement dans votre application, vous devez considérer les points suivants lors de la mise à niveau de la SDK.

Il se peut que vous deviez suivre plusieurs procédures si vous avez manqué plusieurs versions du Kit de développement. Par exemple, si vous migrez à partir de 0.10.1 vers 0.11.0, que vous devez tout d’abord suivre la procédure « à partir de 0.9.0 à 0.10.1 » puis la procédure « à partir de 0.10.1 à 0.11.0 ».

##<a name="from-330-to-340"></a>À partir de 3.3.0 à 3.4.0

### <a name="test-logs"></a>Journaux des tests

Journaux produits par le Kit de développement peuvent désormais être activé/désactivé/filtrées. Pour le personnaliser, mettre à jour de la propriété `EngagementAgent.Instance.TestLogEnabled` à l’une de disponible à partir de la valeur de la `EngagementTestLogLevel` énumération, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Ressources

La superposition de portée a été améliorée. Il fait partie des ressources package NuGet du Kit de développement logiciel.

Lors de la mise à niveau vers la nouvelle version du Kit de développement vous pouvez choisir si vous souhaitez conserver vos fichiers à partir du dossier de la superposition des ressources ou non :

* Si la superposition précédente fonctionne pour vous ou vous intégrez les `WebView` les éléments manuellement puis vous pouvez décider de conserver vos fichiers de sortie, il continuera de fonctionner. 
* Si vous souhaitez mettre à jour vers la nouvelle superposition, remplacez simplement l’ensemble `overlay` dossier provenant de vos ressources avec une nouvelle à partir du package SDK (UWP applications : après la mise à niveau, vous pouvez obtenir le nouveau dossier de superposition %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] À l’aide de la nouvelle superposition remplaceront toutes les personnalisations apportées à la version précédente.

##<a name="from-320-to-330"></a>À partir de 3.2.0 à 3.3.0

### <a name="resources"></a>Ressources
Cette étape concerne uniquement les ressources personnalisées. Si vous avez personnalisé les ressources fournies par le Kit de développement (html, images, superposition) vous devez les sauvegarder avant la mise à niveau et de réappliquer votre personnalisation sur les ressources mises à niveau.

##<a name="from-310-to-320"></a>À partir de 3.1.0 à 3.2.0

### <a name="resources"></a>Ressources
Cette étape concerne uniquement les ressources personnalisées. Si vous avez personnalisé les ressources fournies par le Kit de développement (html, images, superposition) vous devez les sauvegarder avant la mise à niveau et de réappliquer votre personnalisation sur les ressources mises à niveau.

### <a name="webview-integration"></a>Intégration de l’affichage en mode Web
Les améliorations apportées aux correspondent aux facteurs de forme de périphérique ont été introduites dans cette version. Assurez-vous que votre intégration de webview correspondent aux actions suivantes :

Dans votre page XAML () :

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

Et dans votre fichier .cs associé :

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
            
              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }
              
              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif
            
              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }
            
              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

##<a name="from-200-to-300"></a>À partir de 2.0.0 à 3.0.0

### <a name="resources"></a>Ressources
Cette étape concerne uniquement les ressources personnalisées. Si vous avez personnalisé les ressources fournies par le Kit de développement (html, images, superposition) vous devez les sauvegarder avant la mise à niveau et de réappliquer votre personnalisation sur les ressources mises à niveau.

##<a name="from-111-to-200"></a>À partir de 1.1.1 à 2.0.0

Voici comment migrer une intégration SDK à partir du service de Capptain offert par Capptain SAS dans une application grâce à Azure Mobile Engagement. 

> [Azure.IMPORTANT] Capptain et l’Engagement de Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous ne souligne que la migration de l’application cliente. Le Kit de développement de l’application de la migration ne migre pas vos données à partir des serveurs Capptain vers les serveurs Mobile Engagement

Si vous migrez à partir d’une version antérieure, veuillez consulter le site web Capptain pour effectuer une migration vers 1.1.1 tout d’abord, puis appliquer la procédure suivante

### <a name="nuget-package"></a>Package NuGet

Remplacez **Capptain.WindowsPhone** par package Nuget de **MicrosoftAzure.MobileEngagement** .

### <a name="applying-mobile-engagement"></a>Application d’Engagement Mobile

Le Kit de développement logiciel utilise le terme `Engagement`. Vous devez mettre à jour votre projet afin de correspondre à cette modification.

Vous devez désinstaller votre package nuget de Capptain en cours. Considérez que toutes vos modifications dans le dossier Capptain ressources seront supprimées. Si vous souhaitez conserver ces fichiers puis de faire une copie.

Ensuite, installez le package nuget Microsoft Azure Engagement sur votre projet. Vous pouvez trouver directement sur [website nuget]. ou index ici. Cette action remplace tous les fichiers de ressources utilisés par l’Engagement et ajoute la nouvelle DLL d’Engagement pour les références de votre projet.

Vous devez nettoyer vos références de projet en supprimant les références Capptain DLL. Si vous ne faites pas cela, la version de Capptain est en conflit et produiront des erreurs.

Si vous avez personnalisé des ressources de Capptain, copiez le contenu de fichiers anciens et collez-les dans les nouveaux fichiers de projet. Notez que les fichiers xaml et cs doivent être mis à jour.

Une fois ces étapes terminées, il vous suffit de remplacer les anciennes références de Capptain par les nouvelles références de l’Engagement.

1. Tous les espaces de noms Capptain doivent être mis à jour.

    Avant la migration :
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Après la migration :
    
        using Microsoft.Azure.Engagement;

2. Toutes les classes de Capptain qui contiennent « Capptain » doivent contenir « Engagement ».

    Avant la migration :
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Après la migration :
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. Pour les fichiers xaml attributs et espace de noms Capptain sont également modifiés.

    Avant la migration :
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Après la migration :
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Modifications de page de superposition
    > [AZURE.IMPORTANT] Superposition change également. Le nouvel espace de noms est `Microsoft.Azure.Engagement.Overlay`. Il doit être utilisé dans les fichiers xaml et cs. En outre `CapptainGrid` est nommé `EngagementGrid`, `capptain_notification_content` et `capptain_announcement_content` sont nommés `engagement_notification_content` et `engagement_announcement_content`.
    
    Pour la superposition :
    
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
    
    Il devient alors :
    
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>

5. Pour les autres ressources telles que les images Capptain et les fichiers HTML, veuillez noter qu’elles aussi ont été renommés pour utiliser « Engagement ».

### <a name="project-declaration"></a>Déclaration du projet

Sur le Package.appxmanifest `File Type Associations` a été mis à jour à partir de :

 -   capptain\_atteindre\_contenu d’engagement\_atteindre\_contenu
 -   capptain\_journal\_fichier d’engagement\_journal\_fichier

### <a name="application-id--sdk-key"></a>ID de l’application / clé du Kit de développement logiciel

Engagement utilise une chaîne de connexion. Vous n’êtes pas obligé de spécifier un ID d’application et d’une clé du Kit de développement logiciel avec l’Engagement de Mobile, vous ne devrez spécifier une chaîne de connexion. Vous pouvez configurer sur votre fichier EngagementConfiguration.

La configuration de l’Engagement peut être définie votre `Resources\EngagementConfiguration.xml` fichier de votre projet.

Modifier ce fichier pour spécifier :

-   Votre chaîne de connexion d’application entre les balises `<connectionString>` et `<\connectionString>`.

Si vous voulez le spécifier lors de l’exécution au lieu de cela, vous pouvez appeler la méthode suivante avant l’initialisation de l’agent d’Engagement :

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

La chaîne de connexion de votre application s’affiche sur le portail classique d’Azure.

### <a name="items-name-change"></a>Changement de nom des éléments

Tous les éléments nommés *capptain* ont été nommées *engagement*. De la même façon pour les *Capptain* de *l’Engagement*.

Exemples d’éléments de Capptain couramment utilisés :

-   CapptainConfiguration nommé maintenant EngagementConfiguration
-   CapptainAgent nommé maintenant EngagementAgent
-   CapptainReach nommé maintenant EngagementReach
-   CapptainHttpConfig nommé maintenant EngagementHttpConfig
-   GetCapptainPageName nommé maintenant GetEngagementPageName

Notez que ce changement de nom affecte également les méthodes substituées.

 
