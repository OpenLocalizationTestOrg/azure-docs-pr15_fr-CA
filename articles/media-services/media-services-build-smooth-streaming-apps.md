<properties 
    pageTitle="Lisser la diffusion en continu de didacticiel d’application Windows Store | Microsoft Azure" 
    description="Découvrez comment utiliser Azure Media Services pour créer une application C# Windows Store avec un contrôle MediaElement de XML pour flux de fluide de lecture du contenu." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>



#<a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Comment générer un Smooth Streaming Application du Windows Store

Le bon flux Client SDK pour Windows 8 permet aux développeurs de créer des applications Windows Store qui peuvent jouer en direct et à la demande de contenu de diffusion en continu lisse. En plus de la lecture de base de diffusion en continu lisse le contenu, que le SDK fournit également des fonctionnalités riches telles que Microsoft PlayReady protection, la qualité audio de restriction de niveau, DVR Live, flux de commutation, en écoutant les événements d’erreur et les mises à jour de l’état (par exemple, les modifications au niveau de la qualité) et ainsi de suite. Pour plus d’informations les fonctionnalités prises en charge, consultez les [notes de version](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Pour plus d’informations, consultez [le lecteur Framework pour Windows 8](http://playerframework.codeplex.com/). 

Ce didacticiel contient quatre leçons :

1. Créer une Application de banque de diffusion en continu lisse base
2. Ajouter une barre de défilement pour contrôler la progression de Media
3. Sélectionnez le flux de la diffusion en continu lisse
4. Sélectionnez les pistes de diffusion en continu lisse

##<a name="prerequisites"></a>Conditions préalables

- Windows 8 32 bits ou 64 bits. Vous pouvez obtenir [D’évaluation de Windows 8 Entreprise](http://msdn.microsoft.com/evalcenter/jj554510.aspx) à partir de MSDN.
- Visual Studio 2012 ou 2012 de Express de Visual Studio (ou une version ultérieure). Vous pouvez obtenir la version d’évaluation à partir [d’ici](http://www.microsoft.com/visualstudio/11/downloads).
- [Microsoft Smooth Streaming Client SDK pour Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).


La solution terminée pour chaque leçon peut être téléchargée à partir d’exemples de Code MSDN Developer (galerie de Code) : 

- [Leçon 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - un Smooth Streaming Media Player, est Simple Windows 8 
- [Leçon 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - un lecteur multimédia Simple Windows 8 diffusion en continu lisse avec une barre de défilement de contrôle, 
- [Leçon 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - un Smooth Streaming Media Player avec sélection de flux de données, est Windows 8  
- [Leçon 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) : un Smooth Streaming Media Player avec sélection d’une piste est Windows 8.

##<a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Leçon 1 : Créer une Application de banque en continu lisse base

Dans cette leçon, vous créerez une application Windows Store avec un contrôle MediaElement pour lire le flux fluide contenu.  L’exécution de l’application ressemble à :

![Exemple d’application Windows Store de la diffusion en continu lisse][PlayerApplication]
 
Pour plus d’informations sur le développement d’applications du Windows Store, voir [développer des applications pour Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). Cette leçon contient les procédures suivantes :

1.  Créez un projet Windows Store
2.  Conception de l’interface utilisateur (XAML)
3.  Modifier le fichier code-behind
4.  Compiler et tester l’application

**Pour créer un projet Windows Store**

1.  Exécutez Visual Studio 2012 ou une version ultérieure.
2.  Dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.
3.  À partir de la boîte de dialogue Nouveau projet, tapez ou sélectionnez les valeurs suivantes :

Nom|Valeur
---|---
Groupe de modèles|Installation/modèles/Visual C# / Windows stockent
Modèle|Application vide (XAML)
Nom|SSPlayer
Emplacement|C:\SSTutorials
Nom de la solution|SSPlayer
Créer le répertoire pour la solution|(sélectionné)

4.  Cliquez sur **OK**.

**Pour ajouter une référence pour le SDK de Client de diffusion en continu lisse**

1.  À partir de l’Explorateur de solutions, droit sur **SSPlayer**, puis cliquez sur **Ajouter une référence**.
2.  Tapez ou sélectionnez les valeurs suivantes :

Nom|Valeur
---|---
Groupe de référence|Extensions/Windows
Référence|Sélectionnez lisser Microsoft Streaming Client SDK pour Windows 8 et Microsoft Visual C++ Runtime Package
    
3.  Cliquez sur **OK**. 

Après avoir ajouté les références, vous devez sélectionner la plate-forme ciblée (x64 ou x86), ajout de références ne fonctionne pas pour la configuration de la plate-forme Any CPU.  Dans l’Explorateur de solutions, vous verrez d’interrogation jaune Avertissement pour ces ajouté des références.

**Pour concevoir l’interface utilisateur de lecteur**

1.  À partir de l’Explorateur de solutions, double-cliquez sur **MainPage.xaml** pour l’ouvrir en mode Création.
2.  Recherchez la ** &lt;grille&gt; ** et ** &lt;/Grid&gt; ** le fichier XAML des balises et collez le code suivant entre les balises de deux :

        <Grid.RowDefinitions>
            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
        </Grid.RowDefinitions>
        
        <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
        </StackPanel>

        <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
        </StackPanel>

        <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
        </StackPanel>

    Le contrôle MediaElement est utilisée pour lire les médias. Le contrôle de curseur nommé sliderProgress servira dans la leçon suivante pour contrôler la progression du média.

3.  Appuyez sur **CTRL + S** pour enregistrer le fichier.

Le contrôle MediaElement ne prend pas en charge Smooth Streaming contenu out-of-box. Pour activer la prise en charge de la diffusion en continu lisse, vous devez enregistrer le Gestionnaire de flux d’octets de diffusion en continu lisse par extension de nom de fichier et le type MIME.  Pour vous inscrire, vous utilisez la méthode MediaExtensionManager.RegisterByteStremHandler de l’espace de noms Windows.Media.

Dans ce fichier XAML, des gestionnaires d’événements sont associés à des contrôles.  Vous devez définir ces gestionnaires d’événements.

**Pour modifier le fichier code-behind**

1.  À partir de l’Explorateur de solutions, cliquez droit sur le **fichier MainPage.xaml**, puis cliquez sur **Afficher le Code**.
2.  En haut du fichier, ajoutez l’instruction using :

        using Windows.Media;

3.  Au début de la classe **MainPage** , ajoutez le membre de données suivant :

        private MediaExtensionManager extensions = new MediaExtensionManager();

4.  À la fin du constructeur **MainPage** , ajoutez les deux lignes suivantes :

        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
        
5.  À la fin de la classe **MainPage** , après le code suivant :

        #region UI Button Click Events
        private void btnPlay_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Play();
            txtStatus.Text = "MediaElement is playing ...";
        }
        
        private void btnPause_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Pause();
            txtStatus.Text = "MediaElement is paused";
        }
        
        private void btnSetSource_Click(object sender, RoutedEventArgs e)
        {
            sliderProgress.Value = 0;
            mediaElement.Source = new Uri(txtMediaSource.Text);
        
            if (chkAutoPlay.IsChecked == true)
            {
                txtStatus.Text = "MediaElement is playing ...";
            }
            else
            {
                txtStatus.Text = "Click the Play button to play the media source.";
            }
        }
        
        private void btnStop_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Stop();
            txtStatus.Text = "MediaElement is stopped";
        }
        
        private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            txtStatus.Text = "Seek to position " + sliderProgress.Value;
            mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
        }
        #endregion

    Le Gestionnaire d’événements sliderProgress_PointerPressed est défini ici.  Il y a des travaux plus faire pour qu’il fonctionne, qui seront couvertes dans la leçon suivante de ce didacticiel.
6.  Appuyez sur **CTRL + S** pour enregistrer le fichier.

La fin du fichier code-behind doit ressembler à ceci :

![CodeView dans Visual Studio de fluide continu application du Windows Store][CodeViewPic]

**Pour compiler et tester l’application**

1.  Dans le menu **Générer** , cliquez sur **Gestionnaire de Configuration**.
2.  Modifier la **plateforme de la solution Active** correspondant à votre plate-forme de développement.
3.  Appuyez sur **F6** pour compiler le projet. 
4.  Appuyez sur **F5** pour exécuter l’application.
5.  En haut de l’application, vous pouvez utiliser l’URL de diffusion en continu lisse par défaut ou saisir un autre. 
6.  Cliquez sur **définir le code Source**. Étant donné que la **Lecture automatique** est activée par défaut, la lecture du média est automatiquement.  Vous pouvez contrôler le média à l’aide de la **lecture**, les boutons **Pause** et **Arrêter** .  Vous pouvez contrôler le volume de support en utilisant le curseur vertical.  Toutefois le curseur horizontal pour contrôler la progression du média n'est pas totalement implémenté encore. 

Vous avez terminé de lesson1.  Dans cette leçon, vous utilisez un contrôle MediaElement à la lecture de contenu de diffusion en continu lisse.  Dans la leçon suivante, vous allez ajouter un curseur pour contrôler la progression du contenu de diffusion en continu lisse.


##<a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Leçon 2 : Ajouter une barre de défilement pour contrôler la progression de Media
Dans la leçon 1, vous avez créé une application du Windows Store avec un contrôle MediaElement XAML pour la lecture multimédia de diffusion en continu lisse.  Il s’agit de certaines fonctions de support de base telles que le démarrage, arrêt et pause.  Dans cette leçon, vous allez ajouter un contrôle de barre de curseur à l’application.

Dans ce didacticiel, nous utiliserons une minuterie pour mettre à jour de la position du curseur en fonction de la position actuelle du contrôle MediaElement.  Le début du curseur et la fin du temps également devoir être mis à jour en cas de contenu en direct.  Cela peut être mieux géré dans l’événement de mise à jour de source adaptive.

Sources de support sont des objets qui génèrent des données de media.  Le résolveur de source prend un flux d’URL ou de l’octet et crée la source de média approprié pour ce contenu.  Le résolveur de source est le moyen standard pour les applications de créer des sources de médias. 

Cette leçon contient les procédures suivantes :

1.  Inscrire le Gestionnaire de diffusion en continu lisse 
2.  Ajoutez les gestionnaires d’événements au niveau de gestionnaire source ADAPTATIF
3.  Ajoutez les gestionnaires d’événement de niveau source ADAPTATIF
4.  Ajouter des gestionnaires d’événements de MediaElement
5.  Ajouter barre code connexe du curseur
6.  Compiler et tester l’application

**Pour inscrire le Gestionnaire de flux d’octets de diffusion en continu lisse et passez la classe propertyset**

1.  À partir de l’Explorateur de solutions, cliquez avec le bouton droit sur **MainPage.xaml**et sélectionnez **Afficher le Code**.
2.  Au début du fichier, ajoutez l’instruction using :

        using Microsoft.Media.AdaptiveStreaming;

3.  Au début de la classe MainPage, ajoutez les membres de données suivants :

        private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
        private IAdaptiveSourceManager adaptiveSourceManager;
    
4.  Dans le constructeur de **MainPage** , ajouter le code suivant après le **. Initialiser Components() ;** ligne et les lignes de code d’enregistrement écrits dans la leçon précédente :
    
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
    
5.  Dans le constructeur de **MainPage** , modifier les deux méthodes RegisterByteStreamHandler pour ajouter la suite les paramètres :

        // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
        // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
        // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
        propertySet);

6.  Appuyez sur **CTRL + S** pour enregistrer le fichier.

**Pour ajouter le Gestionnaire d’événements au niveau de gestionnaire source ADAPTATIF**

1.  À partir de l’Explorateur de solutions, cliquez avec le bouton droit sur **MainPage.xaml**et sélectionnez **Afficher le Code**.
2.  À l’intérieur de la classe **MainPage** , ajoutez le membre de données suivant :

        private AdaptiveSource adaptiveSource = null;

3.  À la fin de la classe **MainPage** , ajoutez le Gestionnaire d’événements suivant :
    
        #region Adaptive Source Manager Level Events
        private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
        }
        #endregion Adaptive Source Manager Level Events

4.  À la fin du constructeur **MainPage** , ajoutez la ligne suivante pour vous abonner à l’événement open source adaptatif :
    
    adaptiveSourceManager.AdaptiveSourceOpenedEvent += AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened) nouveau ;

5.  Appuyez sur **CTRL + S** pour enregistrer le fichier.

**Pour ajouter des gestionnaires d’événement de niveau source ADAPTATIF**

1.  À partir de l’Explorateur de solutions, cliquez avec le bouton droit sur **MainPage.xaml**et sélectionnez **Afficher le Code**.
2.  À l’intérieur de la classe **MainPage** , ajoutez le membre de données suivant :
    
        private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
        private Manifest manifestObject;
    
3.  À la fin de la classe **MainPage** , ajoutez les gestionnaires d’événements suivants :

        #region Adaptive Source Level Events
        private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
        }
        
        private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
        {
            adaptiveSourceStatusUpdate = args;
        }
        
        private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
        {
            txtStatus.Text = "Error: " + args.HttpResponse;
        }
        #endregion Adaptive Source Level Events

4.  À la fin de la méthode **mediaElement AdaptiveSourceOpened** , ajoutez le code suivant pour vous abonner aux événements :
    
        adaptiveSource.ManifestReadyEvent +=
                    mediaElement_ManifestReady;
        adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
            mediaElement_AdaptiveSourceStatusUpdated;
        adaptiveSource.AdaptiveSourceFailedEvent += 
            mediaElement_AdaptiveSourceFailed;
    
5.  Appuyez sur **CTRL + S** pour enregistrer le fichier.

Les mêmes événements sont disponibles sur Adaptive Gestionnaire niveau Source, qui peut être utilisé pour la gestion des fonctionnalités communes à tous les éléments de média dans l’application. Chaque AdaptiveSource inclut ses propres événements et tous les événements AdaptiveSource va être montés en cascade sous AdaptiveSourceManager.

**Pour ajouter des gestionnaires d’événement élément de média**

1.  À partir de l’Explorateur de solutions, cliquez avec le bouton droit sur **MainPage.xaml**et sélectionnez **Afficher le Code**.
2.  À la fin de la classe **MainPage** , ajoutez les gestionnaires d’événements suivants :
    
        #region Media Element Event Handlers 
        private void MediaOpened(object sender, RoutedEventArgs e)
        {
            txtStatus.Text = "MediaElement opened";
        }
        
        private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
        {
            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
        }
        
        private void MediaEnded(object sender, RoutedEventArgs e)
        {
            txtStatus.Text ="MediaElement ended.";
        }
        #endregion Media Element Event Handlers

3.  À la fin du constructeur **MainPage** , ajoutez le code suivant à un indice pour les événements :
    
        mediaElement.MediaOpened += MediaOpened;
        mediaElement.MediaEnded += MediaEnded;
        mediaElement.MediaFailed += MediaFailed;

4.  Appuyez sur **CTRL + S** pour enregistrer le fichier.

**Pour ajouter la barre de défilement de code connexes**

1.  À partir de l’Explorateur de solutions, cliquez avec le bouton droit sur **MainPage.xaml**et sélectionnez **Afficher le Code**.
2.  Au début du fichier, ajoutez l’instruction using :
    
        using Windows.UI.Core;

3.  À l’intérieur de la classe **MainPage** , ajoutez les membres de données suivants :
    
        public static CoreDispatcher _dispatcher;
        private DispatcherTimer sliderPositionUpdateDispatcher;
    
4.  À la fin du constructeur **MainPage** , ajoutez le code suivant :

        _dispatcher = Window.Current.Dispatcher;
        PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
        sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
    
5.  À la fin de la classe **MainPage** , ajoutez le code suivant :
    
        #region sliderMediaPlayer
        private double SliderFrequency(TimeSpan timevalue)
        {
            long absvalue = 0;
            double stepfrequency = -1;
        
            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }
        
            TimeSpan totalDVRDuration = new TimeSpan(absvalue);
        
            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }
        
            return stepfrequency;
        }
        
        void updateSliderPositionoNTicks(object sender, object e)
        {
            sliderProgress.Value = mediaElement.Position.TotalSeconds;
        }
        
        public void setupTimer()
        {
            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
        }

        public void startTimer()
        {
            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderStartTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderEndTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
        }
        #endregion sliderMediaPlayer

    **Remarque :** CoreDispatcher est utilisé pour apporter des modifications au thread d’interface utilisateur à partir du Thread d’interface utilisateur non. En cas de goulot d’étranglement sur le thread du répartiteur, le développeur peut choisir d’utiliser le répartiteur fournie par l’élément d’interface utilisateur qu’il a l’intention de mettre à jour.  Par exemple :
    
        await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
          timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
        double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
          sliderProgress.Maximum = absvalue; }); 
        

6.  À la fin de la méthode **mediaElement_AdaptiveSourceStatusUpdated** , ajoutez le code suivant :
    
        setSliderStartTime(args.StartTime);
        setSliderEndTime(args.EndTime);

7.  À la fin de la méthode **MediaOpened** , ajoutez le code suivant :
    
    sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan) ; sliderProgress.Width = mediaElement.Width ; setupTimer() ;

8.  Appuyez sur **CTRL + S** pour enregistrer le fichier.

**Pour compiler et tester l’application**

1. Appuyez sur **F6** pour compiler le projet. 
2.  Appuyez sur **F5** pour exécuter l’application.
3.  En haut de l’application, vous pouvez utiliser l’URL de diffusion en continu lisse par défaut ou saisir un autre. 
4.  Cliquez sur **définir le code Source**. 
5.  Test de la barre de défilement.

Vous avez terminé la leçon 2.  Dans cette leçon, vous avez ajouté un curseur à l’application. 

##<a name="lesson-3-select-smooth-streaming-streams"></a>Leçon 3 : Sélectionnez le flux de la diffusion en continu lisse
Diffusion en continu lisse est capable de contenu de flux avec plusieurs pistes audio de langue qui sont sélectionnables par les utilisateurs.  Dans cette leçon, vous allez activer des visionneuses sélectionner le flux de données. Cette leçon contient les procédures suivantes :

1. Modifier le fichier XAML
2. Modifiez le fichier behand de code
3. Compiler et tester l’application


**Pour modifier le fichier XAML**

1. À partir de l’Explorateur de solutions, droit sur **MainPage.xaml**et sélectionnez **Concepteur de vues**.
2. Recherchez &lt;Grid.RowDefinitions&gt;et modifier les RowDefinitions afin qu’ils ressemble à :

        <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
        </Grid.RowDefinitions>

3. À l’intérieur de la &lt;grille&gt;&lt;/Grid&gt; balises, ajoutez le code suivant pour définir un contrôle de zone de liste, afin que les utilisateurs peuvent afficher la liste des flux disponibles et sélectionnez le flux de données :

        <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
        </Grid>

4. Appuyez sur **CTRL + S** pour enregistrer les modifications.


**Pour modifier le fichier code-behind**

1. À partir de l’Explorateur de solutions, cliquez droit sur le **fichier MainPage.xaml**, puis cliquez sur **Afficher le Code**.
2. À l’intérieur de l’espace de noms SSPlayer, ajoutez une nouvelle classe : classe de #region flux
    
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
    
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
    
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
    
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream

3. Au début de la classe MainPage, ajoutez les définitions de variable suivantes :

        private List<Stream> availableStreams;
        private List<Stream> availableAudioStreams;
        private List<Stream> availableTextStreams;
        private List<Stream> availableVideoStreams;

4. À l’intérieur de la classe MainPage, ajouter la région suivante :

        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
        
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
        
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
        
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
        
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
        
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
        
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
        
            // Select the frist video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
        
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
        
            // Select the frist audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
        
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
        
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection

5. Recherchez la méthode mediaElement_ManifestReady, ajoutez le code suivant à la fin de la fonction :
    
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();

    Donc lorsque le manifeste du MediaElement est prêt, le code obtient la liste des flux disponibles et remplit la zone de liste de l’interface utilisateur avec la liste.

6. À l’intérieur de la classe MainPage, recherchez l’interface utilisateur de boutons cliquez sur région d’événements et ajoutez la définition de fonction suivante :

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Pour compiler et tester l’application**

1. Appuyez sur **F6** pour compiler le projet. 
2.  Appuyez sur **F5** pour exécuter l’application.
3.  En haut de l’application, vous pouvez utiliser l’URL de diffusion en continu lisse par défaut ou saisir un autre. 
4.  Cliquez sur **définir le code Source**. 
5.  La langue par défaut est audio_eng. Essayez de basculer entre audio_eng et audio_es. Chaque fois, vous sélectionnez un flux de données, vous devez cliquer sur le bouton Envoyer.

Vous avez terminé la leçon 3.  Dans cette leçon, vous ajoutez la fonctionnalité pour choisir le flux de données.

##<a name="lesson-4-select-smooth-streaming-tracks"></a>Leçon 4 : Sélectionner les pistes de diffusion en continu lisse
Une présentation de diffusion en continu lisse peut contenir plusieurs fichiers vidéo codés avec différents niveaux de qualité (débit) et les résolutions. Dans cette leçon, vous permettra aux utilisateurs de sélectionner les pistes. Cette leçon contient les procédures suivantes :

1. Modifier le fichier XAML
2. Modifiez le fichier behand de code
3. Compiler et tester l’application

**Pour modifier le fichier XAML**

1. À partir de l’Explorateur de solutions, droit sur **MainPage.xaml**et sélectionnez **Concepteur de vues**.
2. Recherchez la &lt;grille&gt; de la balise avec le nom **gridStreamAndBitrateSelection**, ajoutez le code suivant à la fin de la balise :

        <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
        </StackPanel>

3. Appuyez sur **CTRL + S** pour enregistrer les modifications de he


**Pour modifier le fichier code-behind**

1. À partir de l’Explorateur de solutions, cliquez droit sur le **fichier MainPage.xaml**, puis cliquez sur **Afficher le Code**.
2. À l’intérieur de l’espace de noms SSPlayer, ajoutez une nouvelle classe :
    
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
    
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
    
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
    
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track

3. Au début de la classe MainPage, ajoutez les définitions de variable suivantes :
    
        private List<Track> availableTracks;

4. À l’intérieur de la classe MainPage, ajouter la région suivante :
    
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>

        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();

            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;

                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }

        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }

        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }

        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }

        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection

5. Recherchez la méthode mediaElement_ManifestReady, ajoutez le code suivant à la fin de la fonction :

        getTracks(manifestObject);
        refreshAvailableTracksListBoxItemSource();

6. À l’intérieur de la classe MainPage, recherchez l’interface utilisateur de boutons cliquez sur région d’événements et ajoutez la définition de fonction suivante :

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Pour compiler et tester l’application**

1. Appuyez sur **F6** pour compiler le projet. 
2.  Appuyez sur **F5** pour exécuter l’application.
3.  En haut de l’application, vous pouvez utiliser l’URL de diffusion en continu lisse par défaut ou saisir un autre. 
4.  Cliquez sur **définir le code Source**. 
5.  Par défaut, toutes les pistes de la séquence vidéo sont sélectionnés. Pour tester les modifications de taux de bits, vous pouvez sélectionner la vitesse de transmission le plus bas disponible et puis sélectionnez la vitesse de transmission le plus élevée disponible. Vous devez cliquer sur Envoyer après chaque modification.  Vous pouvez voir les modifications de la qualité de la vidéo.

Vous avez terminé la leçon 4.  Dans cette leçon, vous ajoutez la fonctionnalité pour choisir les pistes.


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="other-resources"></a>Autres ressources :
- [Comment créer une application sans heurts de diffusion en continu Windows 8 JavaScript avec des fonctionnalités avancées](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
- [Présentation technique de diffusion en continu lisse](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
 
