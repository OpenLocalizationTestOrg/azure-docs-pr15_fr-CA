<properties
    pageTitle="Concentrateurs de notification localisé didacticiel actualités de dernière minute"
    description="Apprenez à utiliser des concentrateurs de Notification Azure pour envoyer des notifications de news rupture localisée."
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

# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>Concentrateurs de Notification permet d’envoyer des informations de dernière minute localisée

> [AZURE.SELECTOR]
- [Magasin de Windows C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
- [e/s](notification-hubs-ios-xplat-localized-apns-push-notification.md)

##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous indique comment utiliser la fonctionnalité de **modèle** de concentrateurs de Notification Azure pour diffuser les notifications de news avec rupture qui ont été localisées par langue et par périphérique. Dans ce didacticiel, vous démarrez avec l’application Windows Store créée dans [Utilisation des concentrateurs de Notification pour envoyer des informations de dernière minute]. Lorsque vous avez terminé, vous ne pourrez pas enregistrer de catégories qui que vous intéressez, spécifiez un langage dans lequel vous souhaitez recevoir des notifications et de recevoir des notifications de transmission uniquement pour les catégories sélectionnées dans cette langue.


Il existe deux parties de ce scénario :

- permet à l’application du Windows Store client périphériques pour spécifier la langue et de s’abonner à la rupture de différentes catégories d’actualités ;

- back-end diffuse les notifications, à l’aide de la **balise** et le **modèle** fondements de concentrateurs de Notification Azure.



##<a name="prerequisites"></a>Conditions préalables

Vous devez déjà avoir effectué le didacticiel [Utilisation des concentrateurs de Notification pour envoyer des informations de dernière minute] et que le code n’est disponible, car ce didacticiel s’appuie directement sur ce code.

Vous devez également Visual Studio 2012 ou une version ultérieure.


##<a name="template-concepts"></a>Concepts du modèle

Dans [Utiliser des concentrateurs de Notification pour envoyer des informations de dernière minute] , vous avez créé une application qui permet de s’abonner à des notifications pour les catégories d’informations différentes **balises** .
De nombreuses applications, toutefois, ciblent plusieurs marchés et à localiser. Cela signifie que le contenu des notifications eux-mêmes doivent être localisés et remis à l’ensemble de périphériques correct.
Dans cette rubrique, nous apprendrons à utiliser la fonctionnalité de **modèle** de concentrateurs de Notification pour livrer des notifications de news de rupture localisée de facilement.

Remarque : un pour envoyer des notifications localisées consiste à créer plusieurs versions de chaque balise. Par exemple, pour prendre en charge le Mandarin, anglais et Français, il nous faudrait trois balises différentes pour dépêches : « world_en », « world_fr » et « world_ch ». Il faut envoyer une version localisée de l’actualité mondiale à chacune de ces balises. Dans cette rubrique, nous utilisons des modèles afin d’éviter la prolifération des balises et la nécessité de l’envoi de plusieurs messages.

À un niveau élevé, les modèles sont un moyen de spécifier comment un périphérique spécifique doit recevoir une notification. Le modèle spécifie le format de charge utile exact en faisant référence à des propriétés qui font partie du message envoyé par votre application back-end. Dans notre cas, nous envoie un message agnostique en termes de paramètres régionaux contenant toutes les langues :

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Ensuite, nous nous assurons que les périphériques enregistrent avec un modèle qui fait référence à la propriété appropriée. Par exemple, une application du Windows Store qui souhaite recevoir un message simple toast inscrira pour le modèle suivant avec les balises correspondantes :

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



Les modèles sont une fonctionnalité très puissante que vous pouvez apprendre dans notre article de [modèles](notification-hubs-templates-cross-platform-push-messages.md) . 


##<a name="the-app-user-interface"></a>Interface utilisateur de l’application

Nous allons maintenant modifier l’application Breaking News que vous avez créé dans la rubrique [Utilisation des concentrateurs de Notification pour envoyer des informations de dernière minute] pour envoyer des actualités localisées à l’aide de modèles.

Dans votre application du Windows Store :

Modifier votre fichier MainPage.xaml pour inclure une zone de liste déroulante paramètres régionaux :

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

##<a name="building-the-windows-store-client-app"></a>Création de l’application du client Windows Store

1. Dans votre classe de Notifications, ajoutez un paramètre de paramètres régionaux pour les méthodes *StoreCategoriesAndSubscribe* et *SubscribeToCateories* .

        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }

        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }

    Notez qu’au lieu d’appeler la méthode *RegisterNativeAsync* nous appeler *RegisterTemplateAsync*: nous enregistrons un format de notification spécifique dans lequel le modèle varie selon les paramètres régionaux. Nous fournissons également un nom pour le modèle (« localizedWNSTemplateExample »), étant donné que nous voulons enregistrer plus d’un modèle (par exemple un pour les notifications de toast) et un pour les mosaïques et nous devons leur attribuer un nom afin de pouvoir mettre à jour ou les supprimer.

    Notez que si un périphérique enregistre plusieurs modèles avec la même balise, un ciblage de messages entrants qui balise entraîne plusieurs notifications remis au périphérique (un pour chaque modèle). Ce comportement est utile lorsque le même message logique dispose de plusieurs notifications visuelles, par exemple avec un badge et un toast à la fois dans une application Windows Store.

2. Ajoutez la méthode suivante pour extraire les paramètres régionaux stockée :

        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. Dans votre MainPage.xaml.cs, mise à jour de votre bouton Cliquez sur Gestionnaire d’en récupérer la valeur actuelle de la zone de liste déroulante paramètres régionaux et les fournir à l’appel à la classe de Notifications, comme indiqué :

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;

            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);

            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }


4. Enfin, dans votre fichier App.xaml.cs, veillez à mettre à jour votre `InitNotificationsAsync` méthode pour récupérer les paramètres régionaux et l’utiliser lors de l’abonnement :

        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


##<a name="send-localized-notifications-from-your-back-end"></a>Envoyer des notifications localisées à partir de votre back-end

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]






<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Concentrateurs de Notification permet d’envoyer des informations de dernière minute]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
