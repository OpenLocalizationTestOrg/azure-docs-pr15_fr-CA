<properties
    pageTitle="Mise en route de la mission de Mobile Android applications Azure"
    description="Découvrez comment utiliser Azure Mobile Engagement avec analytique et l’envoi de notifications pour les applications Android."
    services="mobile-engagement"
    documentationCenter="android"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Mise en route avec Azure Mobile Engagement pour les applications Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous indique comment Azure Mobile Engagement permet de comprendre l’utilisation de votre application et envoyer des notifications de type pousser à segmenté d’utilisateurs d’une application d’Android.
Ce didacticiel illustre le scénario de diffusion simple à l’aide de Mobile Engagement. Dans, vous créez une application d’Android vide qui collecte les données de base et qui reçoit des notifications de type pousser à l’aide de la messagerie de nuage Google (GCM).

## <a name="prerequisites"></a>Conditions préalables

À la fin de ce didacticiel requiert les [Outils de développement Android](https://developer.android.com/sdk/index.html), qui inclut l’environnement de développement intégré Android et la dernière plate-forme Android.

Il requiert également le [Kit de développement Mobile Engagement Android](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Configurer Mobile Engagement pour votre application d’Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connecter votre application à la principale mission de Mobile

Ce didacticiel présente une « intégration de base », qui est l’ensemble minimal requis pour collecter des données et d’envoyer une notification de transmission. Vous créez une application de base avec Android Studio pour démontrer l’intégration.

Vous trouverez de la documentation sur l’intégration complète dans l' [intégration du Kit de développement Mobile Engagement Android](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Créez un projet Android

1. Démarrez **Studio Android**et dans le menu contextuel, sélectionnez **Démarrer un nouveau projet de Studio Android**.

    ![][1]

2. Fournir un domaine d’application nom et société. Prenez note de ce que vous remplissez, dans la mesure où vous en auriez besoin ultérieurement. Cliquez sur **suivant**.

    ![][2]

3. Sélectionnez le niveau de l’API et le facteur de forme cible, puis cliquez sur **suivant**.

    >[AZURE.NOTE] Engagement de Mobile requiert niveau minimum 10 (Android 2.3.3) de l’API.

    ![][3]

4. Permet de sélectionner **Une activité vide** ici, qui est l’écran uniquement pour cette application, puis cliquez sur **suivant**.

    ![][4]

5. Enfin, laissez les valeurs par défaut et cliquez sur **Terminer**.

    ![][5]

Android Studio crée l’application de démonstration dans lequel nous intégrer Mobile Engagement.

### <a name="include-the-sdk-library-in-your-project"></a>Inclure la bibliothèque SDK dans votre projet

1. Téléchargez le [Kit de développement logiciel Android Engagement Mobile](https://aka.ms/vq9mfn).
2. Extrayez le fichier d’archive dans un dossier de votre ordinateur.
3. Identifier la bibliothèque .jar pour la version actuelle de ce kit et le copier dans le Presse-papiers.

      ![][6]

4. Accédez à la section **projet** (1) et collez le .jar dans le dossier libs (2).

      ![][7]

5. Pour charger la bibliothèque, la synchronisation du projet.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Votre application de se connecter au back-end de l’Engagement de Mobile avec la chaîne de connexion

1. Copier les lignes de code suivantes dans la création de l’activité (doit être effectuée uniquement dans un seul endroit de votre application, généralement l’activité principale). Pour cet exemple d’application, ouvrez le MainActivity sous src -> main -> dossier java et ajoutez le code suivant :

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Résoudre les références en appuyant sur Alt + Entrée ou en ajoutant des instructions d’importation suivantes :

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. Revenez au portail classique Azure dans la page **d’Informations sur la connexion** de votre application et copier la **Chaîne de connexion**.

      ![][9]

4. Coller dans les `setConnectionString` paramètre, remplaçant la chaîne entière est indiquée dans le code suivant :

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Ajouter une déclaration de service et des autorisations

1. Ajouter ces autorisations pour le Manifest.xml de votre projet, immédiatement avant ou après le `<application>` balise :

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Pour déclarer le service de l’agent, ajoutez le code suivant entre les `<application>` et `</application>` balises :

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. Dans le code que vous avez collé, remplacez `"<Your application name>"` dans l’étiquette, qui est affiché dans le menu **paramètres** , où vous pouvez consulter les services en cours d’exécution sur le périphérique. Vous pouvez ajouter le mot « Service » de cette étiquette par exemple.

### <a name="send-a-screen-to-mobile-engagement"></a>Envoyer un écran à l’Engagement de Mobile

Pour commencer l’envoi de données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) sur le serveur principal d’Engagement de Mobile.

Accédez à **MainActivity.java** et ajoutez le code suivant pour remplacer la classe de base de **MainActivity** à **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] Si votre classe de base n’est pas *activité*, consultez [Avancés le rapports Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes) comment hériter de classes différentes.


Commentez la ligne suivante pour ce scénario de l’exemple simple :

    // setSupportActionBar(toolbar);

Si vous souhaitez conserver la `ActionBar` dans votre application, reportez-vous à la section [Avancée de Reporting Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes).

## <a name="connect-app-with-real-time-monitoring"></a>Application de se connecter grâce à une surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Activer les notifications de transmission et de la messagerie dans l’application

Au cours d’une campagne, Engagement de Mobile vous permet d’interagir avec et accéder à vos utilisateurs avec des notifications de transmission et de la messagerie dans l’application. Ce module est appelé la portée dans le portail Mobile Engagement.
La section suivante configure votre application pour les recevoir.

### <a name="copy-sdk-resources-in-your-project"></a>Copie des ressources de kit de développement de votre projet

1. Accédez à votre contenu de téléchargement du Kit de développement logiciel et copiez le dossier **res** .

    ![][10]

2. Accédez au Studio Android, sélectionnez le répertoire **principal** de vos fichiers projet, puis la coller pour ajouter les ressources à votre projet.

    ![][11]

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Accédez à [Android SDK](mobile-engagement-android-sdk-overview.md) pour obtenir des connaissances détaillées à propos de l’intégration du Kit de développement logiciel.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
