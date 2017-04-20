<properties
    pageTitle="Concentrateurs de Notification permet d’envoyer des informations de dernière minute (Windows Phone)"
    description="Concentrateurs de Notification Azure permet d’utiliser la balise dans les enregistrements pour envoyer des informations de dernière minute à une application Windows Phone."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Concentrateurs de Notification permet d’envoyer des informations de dernière minute

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous indique comment utiliser des concentrateurs de Notification Azure pour diffuser les notifications de news de rupture pour une application Silverlight de Windows Phone 8.0/8.1. Si vous ciblez Windows Store ou application de Windows Phone 8.1, reportez-vous à la version [Windows universel](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) . Lorsque vous avez terminé, vous pourrez inscrire pour casser des catégories d’informations que qui ne vous intéressent et recevoir les notifications de transmission uniquement pour ces catégories. Ce scénario est un modèle courant pour nombreuses applications où les notifications doivent être envoyées à des groupes d’utilisateurs que vous ont déclaré précédemment d’intérêt, par exemple, le lecteur RSS, applications pour les passionnés de musique, etc..

Scénarios de diffusion sont activés en incluant une ou plusieurs _balises_ lors de la création d’un enregistrement dans le concentrateur de notification. Lorsque les notifications sont envoyées à une balise, tous les périphériques qui ont inscrit pour la balise recevra la notification. Étant donné que les balises sont simplement des chaînes, ils n’ont pas à être mis en service à l’avance. Pour plus d’informations sur les balises, consultez [routage des concentrateurs de Notification et les Expressions de la balise](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Conditions préalables

Cette rubrique s’appuie sur l’application que vous avez créé dans la [mise en route de concentrateurs de Notification]. Avant de commencer ce didacticiel, vous devez déjà avoir effectué [mise en route de concentrateurs de Notification].

##<a name="add-category-selection-to-the-app"></a>Ajouter la sélection de la catégorie à l’application

La première étape consiste à ajouter des éléments d’interface utilisateur à votre page principale existante qui permettent à l’utilisateur de sélectionner les catégories à inscrire. Les catégories sélectionnées par un utilisateur sont stockées sur le périphérique. Lorsque l’application démarre, un enregistrement de périphérique est créé dans votre centre de notification avec les catégories sélectionnées sous forme de balises.

1. Ouvrez le fichier de projet de fichier MainPage.xaml, puis remplacer les éléments de **grille** nommés `TitlePanel` et `ContentPanel` par le code suivant :

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. Dans le projet, créez une nouvelle classe nommée **Notifications**, d’ajoutez le modificateur **public** à la définition de classe, puis ajoutez les instructions **using** suivantes au nouveau fichier de code :

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;

3. Copiez le code suivant dans la nouvelle classe de **Notifications** :

        private NotificationHub hub;

        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();

            return await SubscribeToCategories();
        }

        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();

            var channel = HttpNotificationChannel.Find("MyPushChannel");

            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;

                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }

            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
            
            return await registrationTask.Task;
        }

        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }

        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";

            // The stored categories tags are passed with the template registration.

            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

            return await registrationTask.Task;
        }

        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;

            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }

            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }


    Cette classe utilise le stockage isolé pour stocker les catégories de news que ce périphérique doit recevoir. Il contient également des méthodes pour vous inscrire à ces catégories à l’aide d’un enregistrement de [modèle de](notification-hubs-templates-cross-platform-push-messages.md) notification.


4. Dans le fichier de projet App.xaml.cs, ajoutez la propriété suivante à la classe **App** . Remplacer la `<hub name>` et `<connection string with listen access>` des espaces réservés avec votre nom de concentrateur de notification et de la chaîne de connexion pour *DefaultListenSharedAccessSignature* que vous avez obtenu précédemment.

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    > [AZURE.NOTE] Étant donné que les informations d’identification qui sont distribuées avec une application client ne sont pas généralement sécurisées, vous devez distribuer la clé pour l’accès d’écoute avec votre application cliente. Écouter access Active votre application à s’inscrire pour les notifications, mais les enregistrements existants ne peuvent pas être modifiée et notifications ne peuvent pas être envoyées. La clé d’accès complet est utilisée dans un service back-end sécurisé pour l’envoi de notifications et de modifier des enregistrements existants.

5. Dans votre MainPage.xaml.cs, ajoutez la ligne suivante :

        using Windows.UI.Popups;

6. Dans le fichier de projet MainPage.xaml.cs, ajoutez la méthode suivante :

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
    
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
    
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }

    Cette méthode crée une liste de catégories et utilise la classe de **Notifications** pour stocker la liste dans le stockage local et inscrire les balises correspondantes avec votre concentrateur de notification. Lorsque les catégories sont modifiées, l’enregistrement est recréé avec les nouvelles catégories.

Votre application est maintenant en mesure de stocker un jeu de catégories dans le stockage local sur le périphérique et l’enregistrer avec le concentrateur de notification chaque fois que l’utilisateur modifie la sélection de catégories.

##<a name="register-for-notifications"></a>S’inscrire pour les notifications

Ces étapes s’inscrire avec le hub de notification au démarrage en utilisant les catégories qui ont été stockés dans le stockage local.

> [AZURE.NOTE] Car l’URI affecté par Microsoft Push Notification Service (MPNS) du canal peut modifier à tout moment, vous devez vous inscrire pour les notifications fréquemment afin d’éviter les échecs de notification. Cet exemple s’inscrit aux notifications chaque fois que l’application démarre. Pour les applications qui sont exécutées fréquemment, plus d’une fois par jour, vous pouvez de probablement ignorer l’enregistrement pour préserver la bande passante si l’inscription précédente remonte à moins d’un jour.


1. Ouvrez le fichier App.xaml.cs et ajoutez le modificateur de **asynchrone** à la méthode de **Application_Launching** et remplacez le code de l’enregistrement de concentrateurs de Notification que vous avez ajoutés dans la [mise en route de concentrateurs de Notification] par le code suivant :

        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();

            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }

    Cela permet de s’assurer que chaque fois que l’application démarre, il extrait les catégories à partir du stockage local et demande un enregistrement pour ces catégories.

2. Dans le fichier de projet MainPage.xaml.cs, ajoutez le code suivant implémente la méthode **OnNavigatedTo** :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
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

    ![][2]

3. Après avoir reçu confirmation que vos catégories ont été abonnement terminée, exécutez l’application console pour envoyer des notifications pour chaque catégories. Vérifiez que vous recevez uniquement une notification pour les catégories qu'auxquels vous êtes abonné.

    ![][3]

Vous avez terminé cette rubrique.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Mise en route de concentrateurs de Notification]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

