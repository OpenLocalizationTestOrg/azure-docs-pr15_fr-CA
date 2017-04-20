<properties
    pageTitle="Mise en route avec Azure Engagement Mobile pour Xamarin.Android"
    description="Découvrez comment utiliser Azure Mobile Engagement avec Analytique et d’envoyer des Notifications pour les applications Xamarin.Android."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/16/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Mise en route avec Azure Engagement Mobile pour des applications Xamarin.Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous indique comment Azure Mobile Engagement permet de comprendre l’utilisation de votre application et envoyer des notifications de type pousser à segmenté d’utilisateurs d’une application Xamarin.Android.
Ce didacticiel illustre le scénario de diffusion simple à l’aide de Mobile Engagement. Dans, vous créez une application Xamarin.Android vide qui collecte les données de base et qui reçoit des notifications de type pousser à l’aide de la messagerie de nuage Google (GCM).

Ce didacticiel requiert les éléments suivants :

+ [Xamarin Studio](http://xamarin.com/studio). Ce didacticiel utilise Xamarin Studio, mais vous pouvez également utiliser Visual Studio avec Xamarin. Pour les instructions d’installation, consultez [le programme d’installation et installer pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ [Kit de développement logiciel de Mobile Engagement Xamarin](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).

##<a id="setup-azme"></a>Le programme d’installation de Mobile Engagement pour votre application d’Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application à la principale mission de Mobile

Ce didacticiel présente une « intégration de base », qui est l’ensemble minimal requis pour collecter des données et d’envoyer une notification de transmission. 

Nous allons créer une application de base avec Xamarin Studio pour illustrer l’intégration.

###<a name="create-a-new-xamarinandroid-project"></a>Créez un nouveau projet Xamarin.Android

1. Lancement **Xamarin Studio** , accédez au **fichier** -> **Nouveau** -> **Solution** 

    ![][1]

2. Sélectionnez **Application Android** , puis assurez-vous que la langue sélectionnée est **C#** et cliquez sur **suivant**.

    ![][2]

3. Renseignez le **Nom de l’application** et l' **identificateur de l’organisation**. Assurez-vous que coche **Services de lecture de Google** et puis cliquez sur **suivant**. 

    ![][3]
    
4. Mettre à jour le **Nom du projet**, le **Nom de la Solution** et **l’emplacement** si nécessaire et cliquez sur **créer**.

    ![][4]
 
Xamarin Studio crée l’application dans laquelle nous intégrera Mobile Engagement. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Votre application de se connecter au back-end de l’Engagement de Mobile

1. Cliquez avec le bouton droit sur le dossier **Packages** dans les fenêtres de la Solution et sélectionnez **Ajouter des Packages...**

    ![][5]

2. Rechercher le **Kit de développement logiciel Microsoft Azure Mobile Engagement Xamarin** et l’ajouter à votre solution.  

    ![][6]
   
3. Ouvrez **MainActivity.cs** et ajoutez le code suivant à l’aide des instructions :

        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;

4. Dans le `OnCreate` méthode, ajoutez le code suivant pour initialiser la connexion avec le serveur principal Mobile Engagement. Assurez-vous d’ajouter votre **ConnectionString**. 

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

###<a name="add-permissions-and-a-service-declaration"></a>Ajouter une déclaration de service et des autorisations

1. Ouvrez le fichier **Manifest.xml** sous le dossier Propriétés. Sélectionnez l’onglet Source afin que vous mettre directement à jour la source XML.
 
2. Ajouter ces autorisations pour le Manifest.xml (qui se trouve sous le dossier **Propriétés** ) de votre projet, immédiatement avant ou après le `<application>` balise :

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. Ajoutez le code suivant entre les `<application>` et `</application>` balises pour déclarer le service de l’agent :

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

4. Dans le code que vous venez de coller, remplacez `"<Your application name>"` dans l’étiquette. Il s’affiche dans le menu **paramètres** , où les utilisateurs peuvent voir les services qui s’exécutent sur le périphérique. Vous pouvez ajouter le mot « Service » de cette étiquette par exemple.

###<a name="send-a-screen-to-mobile-engagement"></a>Envoyer un écran à l’Engagement de Mobile

Avant de commencer l’envoi de données et d’assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran à la principale mission de Mobile. Pour ce faire-s’assurer que les `MainActivity` hérite de `EngagementActivity` à la place de `Activity`.

    public class MainActivity : EngagementActivity
    
Ou bien, si vous ne pouvez pas hériter de `EngagementActivity` puis vous devez ajouter `.StartActivity` et `.EndActivity` méthodes de `OnResume` et `OnPause` respectivement.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }
    
            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

##<a id="monitor"></a>Application de se connecter grâce à une surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activer les notifications de transmission et de la messagerie dans l’application

Engagement de Mobile vous permet de manipuler et d’atteindre vos utilisateurs avec des notifications de transmission et de la messagerie dans le cadre de campagnes dans l’application. Ce module est appelé la portée dans le portail Mobile Engagement.
Les sections suivantes définit votre application pour les recevoir.

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
