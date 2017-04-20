<properties 
    pageTitle="Intégration de kit de développement logiciel Windows portée universelle de Apps" 
    description="Comment intégrer applications universel de Windows Azure Engagement Mobile portée"
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

# <a name="windows-universal-apps-reach-sdk-integration"></a>Intégration de kit de développement logiciel Windows portée universelle de Apps

Vous devez suivre la procédure d’intégration décrite dans l' [Intégration du Kit de développement logiciel Windows universel Engagement](mobile-engagement-windows-store-integrate-engagement.md) avant de suivre ce guide.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Intégrer le Kit de développement Engagement atteindre votre projet Windows universel

Vous n’avez pas à ajouter. `EngagementReach`références et ressources sont déjà dans votre projet.

> [AZURE.TIP] Vous pouvez personnaliser les images situé dans le `Resources` dossier de votre projet, en particulier l’icône de la marque (qui par défaut pour l’icône de l’Engagement). Sur les applications universel vous pouvez également déplacer la `Resources` dossier dans votre projet partagé de partager ses contenus entre les applications, mais vous devrez conserver la `Resources\EngagementConfiguration.xml` sur son emplacement par défaut du fichier car il est dépendant de la plate-forme.

## <a name="enable-the-windows-notification-service"></a>Activer le Service de Notification de Windows

### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x et Windows Phone 8.1 uniquement

Pour utiliser le **Service de Notification de Windows** (appelée WNS) dans votre `Package.appxmanifest` sur le fichier `Application UI` cliquez sur `All Image Assets` dans la zone de gauche bot. À droite de la zone de `Notifications`, modifier `toast capable` de `(not set)` à `(Yes)`.

### <a name="all-platforms"></a>Toutes les plates-formes

Vous devez synchroniser votre application à votre compte Microsoft et à la plate-forme de l’engagement. Pour cela, vous devez créer un compte ou connectez-vous au [Centre de développement windows](https://dev.windows.com). Après cela, créez une nouvelle application et trouver le SID et la clé secrète. Sur le site Web frontal de l’engagement, allez sur la configuration de votre application dans `native push` et collez vos informations d’identification. Ensuite, cliquez droit sur votre projet, sélectionnez `store` et `Associate App with the Store...`. Vous devez simplement sélectionner l’application que vous avez créer avant de la pour synchroniser.

## <a name="initialize-the-engagement-reach-sdk"></a>Initialiser la portée de l’Engagement SDK

Modifier la `App.xaml.cs`:

-   Insérer `EngagementReach.Instance.Init` juste après `EngagementAgent.Instance.Init` dans votre `InitEngagement` méthode :

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
          EngagementReach.Instance.Init(e);
        }

    Le `EngagementReach.Instance.Init` s’exécute dans un thread dédié. Vous n’êtes pas obligé de le faire vous-même.

> [AZURE.NOTE] Si vous utilisez des notifications de type Pousser ailleurs dans votre application vous devez [partager votre canal de transmission](#push-channel-sharing) avec Engagement atteindre.

## <a name="integration"></a>Intégration

Engagement fournit deux méthodes pour ajouter les bannières de portée dans l’application et des vues INTERSTITIELLES pour des annonces et des sondages dans votre application : l’intégration de superposition et de l’intégration manuelle de vues web. Vous ne devez pas combiner les deux approches sur la même page.

Le choix entre l’intégration de deux pouvait se résumer ainsi :

-   Vous pouvez choisir l’intégration de superposition si vos pages hérite déjà de l’Agent `EngagementPage`, il vous suffit de remplacement `EngagementPage` par `EngagementPageOverlay` et `xmlns:engagement="using:Microsoft.Azure.Engagement"` par `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` dans vos pages.
-   Vous pouvez choisir les vues web intégration manuelle si vous souhaitez placer avec précision l’interface utilisateur de joindre dans vos pages, ou si vous ne souhaitez pas ajouter un autre niveau d’héritage à vos pages. 

### <a name="overlay-integration"></a>Intégration de superposition

La superposition d’Engagement ajoute dynamiquement les éléments de l’interface utilisateur permettant d’afficher les campagnes de portée dans votre page. Si la superposition ne répondent à votre disposition vous tenez compte des affichages web de l’intégration manuelle à la place.

Dans modification de votre fichier .xaml `EngagementPage` référence à`EngagementPageOverlay`

-   Ajoutez à vos déclarations d’espaces de noms :

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Remplacer `engagement:EngagementPage` avec `engagement:EngagementPageOverlay`:

**Avec EngagementPage :**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
        
            <!-- Your layout -->
        </engagement:EngagementPage>

**Avec EngagementPageOverlay :**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
        
            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Puis, dans votre fichier .cs balise dans votre page de `EngagementPageOverlay` à la place de `EngagementPage` et importer `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

-   Remplacer `EngagementPage` avec `EngagementPageOverlay`:

**Avec EngagementPage :**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Avec EngagementPageOverlay :**

            using Microsoft.Azure.Engagement.Overlay;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


La superposition d’Engagement ajoute un `Grid` élément en haut de votre page composée de votre mise en page et les deux `WebView` éléments, un pour la bannière et l’autre pour la vue INTERSTITIELLE.

Vous pouvez personnaliser les éléments de superposition directement dans le `EngagementPageOverlay.cs` fichier.

### <a name="web-views-manual-integration"></a>Intégration manuelle de vues Web

Portée recherchent vos pages pour les deux `WebView` éléments chargés pour l’affichage de la bannière et la vue INTERSTITIELLE. La seule chose que vous avez à faire est d’ajouter ces deux `WebView` éléments quelque part dans vos pages, Voici un exemple :

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


Dans cet exemple le `WebView` éléments sont étirés pour s’ajuster à leur conteneur qui ré-redimensionne automatiquement les les en cas de changement de taille de fenêtre de rotation ou de l’écran.

> [AZURE.WARNING] Il est important de conserver la même appellation `engagement_notification_content` et `engagement_announcement_content` de la `WebView` éléments. Atteindre les identifiant par leur nom. 

## <a name="handle-datapush-optional"></a>Gérer des datapush (facultatif)

Si vous souhaitez que votre application soit en mesure de recevoir les push de données de portée, vous devez implémenter deux événements de la classe EngagementReach :

Dans App.xaml.cs dans le constructeur de App(), ajoutez :

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };
            
            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Vous pouvez voir que le rappel de chaque méthode retourne une valeur booléenne. Engagement envoie une rétroaction pour sa principale après la transmission de la transmission de données. Si le rappel renvoie la valeur false, le `exit` commentaires sera envoyé. Dans le cas contraire, il sera `action`. Si aucun rappel n’est définie pour les événements, les `drop` commentaires sera renvoyé à l’Engagement.

> [AZURE.WARNING] Engagement n’est pas en mesure de recevoir des évaluations multiples pour une transmission de données. Si vous envisagez de définir plusieurs gestionnaires d’un événement, sachez que l’évaluation correspond à la dernière un envoyé. Dans ce cas, nous vous recommandons de toujours renvoie la même valeur pour éviter la confusion des commentaires sur le serveur frontal.

## <a name="customize-ui-optional"></a>Personnaliser l’interface utilisateur (facultatif)

### <a name="first-step"></a>Première étape

Nous vous permettent de personnaliser la portée de l’interface utilisateur.

Pour ce faire, vous devez créer une sous-classe de la `EngagementReachHandler` classe.

**Exemple de Code :**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Ensuite, définissez le contenu de la `EngagementReach.Instance.Handler` champ avec votre objet personnalisé dans votre `App.xaml.cs` classe au sein de la `App()` méthode.

**Exemple de Code :**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [AZURE.NOTE]Par défaut, Engagement utilise sa propre implémentation de `EngagementReachHandler`.
> Vous n’êtes pas obligé de créer votre propre, et si vous procédez ainsi, vous n’êtes pas obligé de substituer toutes les méthodes. Le comportement par défaut est de sélectionner l’objet de base d’Engagement.

### <a name="web-view"></a>Affichage Web

Par défaut, portée utilise les ressources incorporées de la DLL pour afficher les pages et les notifications.

Pour fournir une personnalisation complète de possibilités que nous n’utilisons que l’affichage web. Si vous souhaitez personnaliser des mises en page, substituer directement les fichiers de ressources `EngagementAnnouncement.html` et `EngagementNotification.html`. Engagement doit tout le code dans `<body></body>` pour s’exécuter correctement. Mais vous pouvez ajouter la balise extérieure `engagement_webview_area`.

Toutefois, vous pouvez décider d’utiliser vos propres ressources.

Vous pouvez substituer `EngagementReachHandler` méthodes dans votre sous-classe pour indiquer l’Engagement à utiliser vos mises en page, mais veillez à incorporé le mécanisme d’engagement :

**Exemple de Code :**
            
            // In your subclass of EngagementReachHandler
            
            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Par défaut, AnnouncementHTML est `ms-appx-web:///Resources/EngagementAnnouncement.html`. Il représente le fichier html que vous concevez le contenu d’un message de type Pousser (annonce de texte, Web anoucement et annonce de sondage). AnnouncementName est `engagement_announcement_content`. C’est le nom de la conception de l’affichage Web dans votre page xaml.

NotfificationHTML est `ms-appx-web:///Resources/EngagementNotification.html`. Il représente le fichier html que vous concevez la notification d’un message de type Pousser. NotfificationName est `engagement_notification_content`. C’est le nom de la conception de l’affichage Web dans votre page xaml.

### <a name="customization"></a>Personnalisation

Vous pouvez personnaliser annonce affichage web a de choix si vous conservez l’objet de l’Engagement et notification. Prenez soin de ce mode d’affichage Web objet décrit trois fois : la première fois dans votre xaml, seconde fois dans votre fichier .cs dans la méthode « setwebview() » et troisième fois dans le fichier html.

-   Dans votre code xaml vous décrivez le composant webview disposition graphique en cours.
-   Dans votre fichier .cs, vous pouvez définir « setwebview() », dans lequel vous définissez la dimension de deux webview (notification, annonce). Il est très efficace lors du redimensionnement de l’application.
-   Dans le fichier html de mission nous décrire le contenu de l’affichage en mode Web, de conception et les positions des éléments entre eux.

### <a name="launch-message"></a>Lancement de message

Lorsqu’un utilisateur clique sur une notification du système (un toast), Engagement lance l’application, chargez le contenu des messages envoyés et afficher la page de la campagne correspondante.

Il existe un délai entre le lancement de l’application et l’affichage de la page (en fonction de la vitesse de votre réseau).

Pour indiquer à l’utilisateur que quelque chose se charge, vous devez fournir une informations visuelles, comme une barre de progression ou un indicateur de progression. Engagement ne peut gérer que lui-même, mais fournit quelques gestionnaires pour vous.

Pour implémenter le rappel, dans App.xaml.cs dans « Public App() {} », ajouter :

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
            
            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
            
            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Vous pouvez définir le rappel dans votre méthode « Public App() {} » de votre `App.xaml.cs` fichier, de préférence avant le `EngagementReach.Instance.Init()` appeler.

> [AZURE.TIP] Chaque gestionnaire est appelé par le Thread de l’interface utilisateur. Vous n’êtes pas obligé de vous inquiétez pas si vous utilisez un MessageBox ou quelque chose liés à l’interface utilisateur.

##<a id="push-channel-sharing"></a>Poussez le partage de canal

Si vous utilisez des notifications de transmission pour un autre objet dans votre application vous devez utiliser le canal de transmission fonctionnalité du SDK Engagement de partage. Cela est d’éviter de push manquée.

- Vous pouvez fournir votre propre canal de transmission à l’initialisation de l’Engagement atteindre. Le Kit de développement logiciel l’utilise au lieu de demander un nouveau.

Mise à jour de l’initialisation de l’Engagement atteindre avec votre canal de transmission dans les `InitEngagement` méthode à partir de la `App.xaml.cs` fichier :
    
    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

- Également, si vous souhaitez simplement utiliser le canal de transmission après que l’initialisation de la portée, vous pouvez définir un rappel sur l’Engagement atteindre pour obtenir le canal de commande une fois qu’il est créé par le Kit de développement logiciel.

Définissez votre rappel à n’importe quel endroit du **après** l’initialisation de la portée :

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Astuce de jeu personnalisé

Nous assurer d’une utilisation de couleurs personnalisé. Vous pouvez envoyer un type différent de l’URI de frontal d’engagement à utiliser dans votre application d’engagement. Schéma par défaut comme `http, ftp, ...` gérer par Windows, une fenêtre de se demander si elles ne sont aucune application par défaut installée sur le périphérique. Vous pouvez également créer votre propre modèle personnalisé pour votre application.

La simple pour définir un modèle personnalisé dans votre application consiste à ouvrir votre `Package.appxmanifest` allez dans `Declarations` panneau. Sélectionnez `Protocol` dans les déclarations disponibles faire défiler la zone et de l’ajouter. Modifier la `Name` nom de champ à l’aide de votre nouveau protocole voulu.

Pour utiliser ce protocole, modifier votre `App.xaml.cs` avec la `OnActivated` méthode et n’oubliez pas d’initialiser engagement ici également :

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);
            
              //TODO design action to do when app is launch
            
              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
            
                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion
 
