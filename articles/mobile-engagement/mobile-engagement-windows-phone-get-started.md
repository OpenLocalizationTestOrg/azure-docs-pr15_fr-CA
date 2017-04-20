<properties
    pageTitle="Mise en route avec les applications Azure Mobile Engagement pour Windows Phone Silverlight"
    description="Découvrez comment utiliser Azure Mobile Engagement avec analytique et l’envoi de notifications pour les applications Silverlight de Windows Phone."
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Mise en route avec les applications Azure Mobile Engagement pour Windows Phone Silverlight

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous indique comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et d’envoyer des notifications de type pousser à segmenté d’utilisateurs d’une application Silverlight de Windows Phone.
Ce didacticiel illustre le scénario de diffusion simple à l’aide de Mobile Engagement. Dans, vous créez une application Silverlight de Windows Phone vide qui collecte les données de base et qui reçoit des notifications de type pousser à l’aide de Microsoft Push Notification Service (MPNS).

> [AZURE.NOTE] Si vous ciblez Windows Phone 8.1 (non-Silverlight), reportez-vous au [didacticiel universel de Windows](mobile-engagement-windows-store-dotnet-get-started.md).

Ce didacticiel requiert les éléments suivants :

+ Visual Studio 2013
+ Package Nuget de [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Le programme d’installation de Mobile Engagement pour votre Windows Phone app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application à la principale mission de Mobile

Ce didacticiel présente une « intégration de base », qui est l’ensemble minimal requis pour collecter des données et d’envoyer une notification de transmission. Vous trouverez la documentation sur l’intégration complète dans l' [intégration du Kit de développement logiciel Windows Phone Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)

Nous allons créer une application de base avec Visual Studio pour illustrer l’intégration.

###<a name="create-a-new-windows-phone-silverlight-project"></a>Créez un nouveau projet Silverlight de Windows Phone

Les étapes suivantes supposent l’utilisation de Visual Studio 2015, bien que les étapes sont identiques dans les versions antérieures de Visual Studio. 

1. Démarrez Visual Studio et dans l’écran **d’accueil** , sélectionnez **Nouveau projet**.

2. Dans le menu contextuel, sélectionnez **Windows 8** -> **De Windows Phone** -> **Application vide (Silverlight de Windows Phone)**. Renseignez le **nom** d’application et le **nom de la Solution**, puis cliquez sur **OK**.

    ![][1]

3. Vous pouvez choisir de cibler **Windows Phone 8.0** ou **Windows Phone 8.1**.

Vous avez maintenant créé une nouvelle application Silverlight de Windows Phone dans lequel nous intégrera l’Engagement de Mobile Azure SDK.

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connecter votre application à la principale mission de Mobile

1. Installez le package nuget [MicrosoftAzure.MobileEngagement] dans votre projet.

2. Ouvrir `WMAppManifest.xml` (sous le dossier Propriétés) et assurez-vous que les éléments suivants sont déclarés (les ajouter si elles ne le sont pas) dans le `<Capabilities />` balise :

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Coller la chaîne de connexion que vous avez copié précédemment pour votre application Mobile Engagement maintenant et le coller dans le `Resources\EngagementConfiguration.xml` de fichiers, entre la `<connectionString>` et `</connectionString>` balises :

    ![][3]

4. Dans le `App.xaml.cs` fichier :

    une barre d’outils. Ajouter le `using` instruction :

            using Microsoft.Azure.Engagement;

    b. Initialiser le SDK dans le `Application_Launching` méthode :

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c. Insérez le code suivant dans la `Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

##<a id="monitor"></a>Activer l’analyse en temps réel

Avant de commencer l’envoi de données et de veiller à ce que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) sur le serveur principal d’Engagement de Mobile.

1. Dans la MainPage.xaml.cs, ajoutez le `using` instruction :

        using Microsoft.Azure.Engagement;

2. Remplacez la classe de base de **MainPage**, c'est-à-dire avant **PhoneApplicationPage**, **EngagementPage**.

        class MainPage : EngagementPage 
    
3. Dans votre `MainPage.xml` fichier :

    une barre d’outils. Ajoutez à vos déclarations d’espaces de noms :

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b. Remplacer `phone:PhoneApplicationPage` dans le nom de balise XML avec `engagement:EngagementPage`.

##<a id="monitor"></a>Application de se connecter grâce à une surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activer les notifications de transmission et de la messagerie dans l’application

Engagement de Mobile vous permet d’interagir et de parvenir à vos utilisateurs avec l’envoi de Notifications et de messagerie dans l’application dans le cadre de campagnes. Ce module est appelé la portée dans le portail Mobile Engagement.
Les sections suivantes, configurées votre application pour les recevoir.

###<a name="enable-your-app-to-receive-mpns-push-notifications"></a>Activer votre application de recevoir des Notifications Push de MPNS

Ajouter de nouvelles fonctions à votre `WMAppManifest.xml` fichier :

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###<a name="initialize-the-reach-sdk"></a>Initialiser le Kit de développement de portée

1. Dans `App.xaml.cs`, appelez `EngagementReach.Instance.Init();` dans la fonction **Application_Launching** , droit après l’initialisation de l’agent :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. Dans `App.xaml.cs`, appelez `EngagementReach.Instance.OnActivated(e);` dans la fonction **Application_Activated** , droit après l’initialisation de l’agent :

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Vous êtes prêt. Nous allons maintenant vérifier que vous avez correctement crié à l’intégration de cette base.

##<a id="send"></a>Envoyer une notification à votre application

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Vous devez maintenant voir une notification sur votre périphérique qui s’afficheront sous la forme d’une notification dans l’application si l’application est ouverte sous la forme d’une notification de toast comme suit : 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
