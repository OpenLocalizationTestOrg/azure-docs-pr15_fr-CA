<properties
    pageTitle="Concentrateurs de Notification permet d’envoyer des informations de dernière minute (Universal Windows)"
    description="Utiliser des concentrateurs de Notification Azure avec les balises dans l’enregistrement pour envoyer des informations de dernière minute à une application Windows universelle."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>


<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Concentrateurs de Notification permet d’envoyer des informations de dernière minute


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous indique comment utiliser des concentrateurs de Notification Azure pour diffuser les notifications de news de rupture pour une application de Windows Phone 8.1 (non-Silverlight) ou le Windows Store. Si vous ciblez Silverlight de Windows Phone 8.1, veuillez vous reporter à la version [De Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) . Lorsque vous avez terminé, vous pourrez inscrire pour casser des catégories d’informations que qui ne vous intéressent et recevoir les notifications de transmission uniquement pour ces catégories. Ce scénario est un modèle courant pour nombreuses applications où les notifications doivent être envoyées à des groupes d’utilisateurs que vous ont déclaré précédemment d’intérêt, par exemple, lecteur RSS, applications pour les passionnés de musique et ainsi de suite. 

Scénarios de diffusion sont activés en incluant une ou plusieurs _balises_ lors de la création d’un enregistrement dans le concentrateur de notification. Lorsque les notifications sont envoyées à une balise, tous les périphériques qui ont inscrit pour la balise recevra la notification. Étant donné que les balises sont simplement des chaînes, ils n’ont pas à être mis en service à l’avance. Pour plus d’informations sur les balises, consultez [routage des concentrateurs de Notification et les Expressions de la balise](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Conditions préalables

Cette rubrique s’appuie sur l’application que vous avez créé dans la [mise en route de concentrateurs de Notification][get-started]. Avant de commencer ce didacticiel, vous devez déjà avoir effectué [mise en route de concentrateurs de Notification][get-started].

##<a name="add-category-selection-to-the-app"></a>Ajouter la sélection de la catégorie à l’application

La première étape consiste à ajouter des éléments d’interface utilisateur à votre page principale existante qui permettent à l’utilisateur de sélectionner les catégories à inscrire. Les catégories sélectionnées par un utilisateur sont stockées sur le périphérique. Lorsque l’application démarre, un enregistrement de périphérique est créé dans votre centre de notification avec les catégories sélectionnées sous forme de balises.

1. Ouvrez le fichier de projet de fichier MainPage.xaml, puis copiez le code suivant dans l’élément de **grille** :

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. Cliquez avec le bouton droit sur le projet **Shared** et ajouter une nouvelle classe nommée **Notifications**, ajoutez le modificateur **public** à la définition de classe, puis ajoutez les instructions **using** suivantes au nouveau fichier de code :

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Copiez le code suivant dans la nouvelle classe de **Notifications** :

        private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }

    Cette classe utilise le stockage local pour stocker les catégories de news que ce périphérique doit recevoir. Notez qu’au lieu d’appeler la méthode *RegisterNativeAsync* nous appeler *RegisterTemplateAsync* pour enregistrer des catégories à l’aide d’un enregistrement de modèle. 
    
    Nous fournissons également un nom pour le modèle (« simpleWNSTemplateExample »), étant donné que nous voulons enregistrer plus d’un modèle (par exemple un pour les notifications de toast) et un pour les mosaïques et nous devons leur attribuer un nom afin de pouvoir mettre à jour ou les supprimer.

    Notez que si un périphérique enregistre plusieurs modèles avec la même balise, un ciblage de messages entrants qui balise entraîne plusieurs notifications remis au périphérique (un pour chaque modèle). Ce comportement est utile lorsque le même message logique dispose de plusieurs notifications visuelles, par exemple avec un badge et un toast à la fois dans une application Windows Store.

    Pour plus d’informations sur les modèles, consultez [modèles](notification-hubs-templates-cross-platform-push-messages.md).




4. Dans le fichier de projet App.xaml.cs, ajoutez la propriété suivante à la classe **App** :

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    Cette propriété est utilisée pour créer et accéder à une instance de **Notifications** .

    Dans le code ci-dessus, remplacez la `<hub name>` et `<connection string with listen access>` des espaces réservés avec votre nom de concentrateur de notification et de la chaîne de connexion pour *DefaultListenSharedAccessSignature* que vous avez obtenu précédemment.

    > [AZURE.NOTE] Étant donné que les informations d’identification qui sont distribuées avec une application client ne sont pas généralement sécurisées, vous devez distribuer la clé pour l’accès d’écoute avec votre application cliente. Écouter access Active votre application à s’inscrire pour les notifications, mais les enregistrements existants ne peuvent pas être modifiée et notifications ne peuvent pas être envoyées. La clé d’accès complet est utilisée dans un service back-end sécurisé pour l’envoi de notifications et de modifier des enregistrements existants.

5. Dans votre MainPage.xaml.cs, ajoutez la ligne suivante :

        using Windows.UI.Popups;

6. Dans le fichier de projet MainPage.xaml.cs, ajoutez la méthode suivante :

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Cette méthode crée une liste de catégories et utilise la classe de **Notifications** pour stocker la liste dans le stockage local et inscrire les balises correspondantes avec votre concentrateur de notification. Lorsque les catégories sont modifiées, l’enregistrement est recréé avec les nouvelles catégories.

Votre application est maintenant en mesure de stocker un jeu de catégories dans le stockage local sur le périphérique et l’enregistrer avec le concentrateur de notification chaque fois que l’utilisateur modifie la sélection de catégories.

##<a name="register-for-notifications"></a>S’inscrire pour les notifications

Ces étapes s’inscrire avec le hub de notification au démarrage en utilisant les catégories qui ont été stockés dans le stockage local.

> [AZURE.NOTE] Car l’URI affecté par le Service de Notification de Windows (WNS) du canal peut modifier à tout moment, vous devez vous inscrire pour les notifications fréquemment afin d’éviter les échecs de notification. Cet exemple s’inscrit pour la notification chaque fois que l’application démarre. Pour les applications qui sont exécutées fréquemment, plus d’une fois par jour, vous pouvez de probablement ignorer l’enregistrement pour préserver la bande passante si l’inscription précédente remonte à moins d’un jour.

1. Ouvrez le fichier App.xaml.cs et la méthode **InitNotificationsAsync** permet de mettre à jour la `notifications` classe pour vous abonner en fonction des catégories.

        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
    
        var result = await notifications.SubscribeToCategories();

    Cela permet de s’assurer que chaque fois que l’application démarre, il extrait les catégories à partir du stockage local et demande une registeration pour ces catégories. La méthode **InitNotificationsAsync** a été créée dans le cadre de la [mise en route de concentrateurs de Notification] [ get-started] didacticiel.

3. Dans le fichier de projet MainPage.xaml.cs, ajoutez le code suivant à la méthode *OnNavigatedTo* :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }

    Cela met à jour la page principale en fonction de l’état des catégories précédemment enregistrés.

L’application est maintenant terminée et peut de stocker un jeu de catégories dans le stockage local du périphérique utilisé pour enregistrer avec le concentrateur de notification chaque fois que l’utilisateur modifie la sélection de catégories. Ensuite, nous allons définir un back-end qui peut envoyer des notifications de catégorie pour cette application.

##<a name="sending-tagged-notifications"></a>Envoi des notifications

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Exécutez l’application et générer des notifications

1. Dans Visual Studio, appuyez sur F5 pour compiler et exécuter l’application.

    ![][1]

    Notez que l’interface utilisateur de l’application fournit un jeu de bascule qui vous permet de choisir les catégories pour vous abonner à.

2. Activer un ou plusieurs bascule de catégories, puis cliquez sur **s’abonner**.

    L’application convertit les catégories sélectionnées dans des balises et demande une nouvelle inscription de périphérique pour les balises sélectionnées à partir du concentrateur de notification. Les catégories inscrites sont retournés et affichés dans une boîte de dialogue.

    ![][19]

4. Envoyer une notification à partir du serveur principal dans une des manières suivantes :

    + **Application console :** démarrer l’application console.

    + **Java/PHP :** exécuter votre application ou le script.

    Les notifications pour les catégories sélectionnées s’affichent sous forme de notifications de toast.

    ![][14]

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons appris à diffuser des informations de dernière minute par catégorie. Pensez à la fin d’un des didacticiels suivants qui mettent en évidence d’autres scénarios avancés de concentrateurs de Notification :

+ [Utiliser des concentrateurs de Notification pour diffuser des informations de dernière minute localisée]

    Apprenez à développer l’application d’actualités rupture pour activer l’envoi de notifications localisée.



<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Utiliser des concentrateurs de Notification pour diffuser des informations de dernière minute localisée]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
