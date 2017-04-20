<properties
    pageTitle="Mise en route de concentrateurs de Notification Azure à l’aide de Baidu | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser des concentrateurs de Notification Azure pour les notifications de type Pousser aux périphériques Android à l’aide de Baidu."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="mobile-baidu"
    ms.workload="mobile"
    ms.date="08/19/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-using-baidu"></a>Mise en route de concentrateurs de Notification à l’aide de Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Transmission de nuage Baidu est un service en nuage chinois que vous pouvez utiliser pour envoyer des notifications de type Pousser aux périphériques mobiles. Ce service est particulièrement utile en Chine, où livrer les notifications de type pousser à Android est complexe en raison de la présence de magasins d’applications différentes et les services de type Pousser, en plus de la disponibilité des appareils Android qui ne sont pas généralement connectés au GCM (Google Cloud messagerie).

##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ Android SDK (nous supposons que vous allez utiliser Eclipse), que vous pouvez télécharger à partir du <a href="http://go.microsoft.com/fwlink/?LinkId=389797">site Android</a>
+ [Kit de développement logiciel Services mobiles Android]
+ [Kit de développement logiciel Android Baidu Push]

>[AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##<a name="create-a-baidu-account"></a>Créer un compte Baidu

Pour utiliser Baidu s’affiche, vous devez disposer d’un compte Baidu. Si vous avez déjà un, connectez-vous au [portail de Baidu] et passez à l’étape suivante. Dans le cas contraire, consultez les instructions ci-dessous créer un compte Baidu.  

1. Accédez au [portail de Baidu] et cliquez sur le lien**登录**(**connexion**). Cliquez sur**立即注册**pour démarrer le processus d’inscription.

    ![][1]

2. Entrez les informations requises : code adresse, mot de passe et la vérification de téléphone et de messagerie — cliquez sur **inscription**.

    ![][2]

3. Vous recevrez un courrier électronique à l’adresse e-mail que vous avez entré avec un lien pour activer votre compte Baidu.

    ![][3]

4. Se connecter à votre compte de messagerie, ouvrez le message d’activation Baidu et cliquez sur le lien d’activation pour activer votre compte Baidu.

    ![][4]

Une fois que vous avez un compte Baidu activé, connectez-vous au [portail de Baidu].

##<a name="register-as-a-baidu-developer"></a>Enregistrer en tant que développeur Baidu

1. Une fois que vous êtes connecté sur le [portail de Baidu s’affiche], cliquez sur**更多 >>** (**plus**).

    ![][5]

2. Faites défiler la liste vers le bas dans la section**站长与开发者服务 (administrateur et développeur de Services)** , puis cliquez sur**百度开放云平台**(**Baidu ouvrir la plateforme en nuage**).

    ![][6]

3. Sur la page suivante, cliquez sur**开发者服务**(**Developer Services**) dans le coin supérieur droit.

    ![][7]

4. Sur la page suivante, cliquez sur**注册开发者**(**Enregistré les développeurs**) à partir du menu dans le coin supérieur droit.

    ![][8]

5. Entrez votre nom, une description et un numéro de téléphone portable pour la réception d’un message texte de vérification, puis cliquez sur**送验证码**(**Envoyer un Code de vérification**). Notez que pour les numéros de téléphone internationaux, vous devez placer le code de pays entre parenthèses. Par exemple, pour un certain nombre des États-Unis d’Amérique, il sera **(1) 1234567890**.

    ![][9]

6. Vous recevrez ensuite un message avec un numéro de vérification, comme illustré dans l’exemple suivant :

    ![][10]

7. Entrez le numéro de vérification à partir du message dans les**验证码**(**code de Confirmation**).

8. Enfin, terminez l’inscription du développeur en acceptant l’accord Baidu en cliquant sur les**提交**(**envoi**). Vous verrez la page suivante après réussite de l’enregistrement :

    ![][11]

##<a name="create-a-baidu-cloud-push-project"></a>Créez un projet de push Baidu cloud

Lorsque vous créez un projet de push Baidu cloud, vous recevez votre ID app, clé d’API et de clé secrète.

1. Une fois que vous êtes connecté sur le [portail de Baidu s’affiche], cliquez sur**更多 >>** (**plus**).

    ![][5]

2. Faites défiler la liste vers le bas dans la section**站长与开发者服务**(**administrateur et développeur de Services**), puis cliquez sur**百度开放云平台**(**Baidu ouvrir la plateforme en nuage**).

    ![][6]

3. Sur la page suivante, cliquez sur**开发者服务**(**Developer Services**) dans le coin supérieur droit.

    ![][7]

4. Sur la page suivante, cliquez sur**云推送**(**Cloud Push**) à partir de la section**云服务**(**Services en nuage**).

    ![][12]

5. Une fois que vous êtes un développeur enregistré, vous verrez**管理控制台**(**Management Console**) dans le menu supérieur. Cliquez sur**开发者服务管理**(**Gestion des services de développeurs**).

    ![][13]

6. Sur la page suivante, cliquez sur**创建工程**(**Create Project**).

    ![][14]

7. Entrez un nom d’application, puis cliquez sur**创建**(**créer**).

    ![][15]

8. Lors de la création réussie d’un projet push du nuage Baidu s’affiche, vous verrez une page avec la **Clé secrète**, **Clé d’API**et **AppID**. Prenez note de la clé d’API et de la clé secrète, que nous utiliserons plus tard.

    ![][16]

9. Configurez le projet pour les notifications de type Pousser en cliquant sur**云推送**(**Émetteur de nuage**) dans le volet gauche.

    ![][31]

10. Sur la page suivante, cliquez sur le bouton**推送设置**(**paramètres de distribution**).

    ![][32]  

11. Sur la page de configuration, ajoutez le nom de package que vous utiliserez dans votre projet Android dans le champ**应用包名**(**package de l’Application**) et puis cliquez sur**保存设置**(**Enregistrer**).  

    ![][33]

Vous verrez la**保存成功 !** (**enregistrement réussi !**) message.

##<a name="configure-your-notification-hub"></a>Configurer votre concentrateur de notification

1. Connectez-vous au [Portail classique d’Azure], puis cliquez sur **+ Nouveau** en bas de l’écran.

2. Cliquez sur les **Services d’application**et cliquez sur **Bus de Service**, cliquez sur **Concentrateur de Notification**, puis cliquez sur **Création rapide**.

3. Fournissez un nom pour votre **Concentrateur de Notification**, sélectionnez la **région** et le **Namespace** où ce concentrateur de notification sera créé, puis cliquez sur **créer un nouveau concentrateur de Notification**.  

    ![][17]

4. Cliquez sur l’espace de noms dans lequel vous avez créé votre concentrateur de notification, puis cliquez sur **Notification concentrateurs** en haut.

    ![][18]

5. Sélectionnez le concentrateur de notification que vous avez créé, puis cliquez sur **configurer** dans le menu supérieur.

    ![][19]

6. Faites défiler jusqu'à la section **paramètres de notification baidu** et entrez la clé d’API et de la clé secrète que vous avez précédemment obtenu à partir de la console Baidu pour votre projet push du nuage Baidu. Cliquez sur **Enregistrer**.

    ![][20]

7. Cliquez sur l’onglet **tableau de bord** en haut pour le concentrateur de notification, puis cliquez sur **Affichage de chaîne de connexion**.

    ![][21]

8. Prenez note de la **DefaultListenSharedAccessSignature** et de **DefaultFullSharedAccessSignature** à partir de la fenêtre **d’informations de connexion d’accès** .

    ![][22]

##<a name="connect-your-app-to-the-notification-hub"></a>Votre application de se connecter au concentrateur de notification

1. En Eclipse TSA, créez un nouveau projet de Android (**fichier** > **Nouveau** > **Android projet d’Application**).

    ![][23]

2. Entrez un **Nom d’Application** et vérifiez que la version du **SDK de Minimum requis** est définie sur **API 16 : 4.1 Android**.

    ![][24]

3. Cliquez sur **suivant** et continuez à suivre l’Assistant jusqu'à ce que la fenêtre **Créer une activité** s’affiche. Assurez-vous que **l’Activité vide** est sélectionnée et enfin sélectionnez **Terminer** pour créer une nouvelle Application d’Android.

    ![][25]

4. Assurez-vous que la **Cible Build de projet** est définie correctement.

    ![][26]

5. Téléchargez le fichier de notification-hubs-0.4.jar à partir de l’onglet **fichiers** de la [Notification-Hubs-Android-SDK sur Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Ajouter le fichier au dossier de votre projet Eclipse **libs** et actualiser le dossier *et les bibliothèques* .

6. Télécharger et décompresser le [SDK de Android Push Baidu], ouvrir le dossier **et les bibliothèques** et puis copiez le fichier jar **x.y.z-pushservice** et **armeabi** & **mips** des dossiers dans le dossier **et les bibliothèques** de votre application d’Android.

7. Ouvrez le fichier **AndroidManifest.xml** de votre projet Android et ajouter les autorisations qui sont requises par le SDK Baidu.

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Ajoutez la propriété **android : nom** à votre élément **d’application** dans **AndroidManifest.xml**, remplace *yourprojectname* (par exemple, **com.example.BaiduTest**). Assurez-vous que ce nom de projet correspond à celui que vous avez configuré dans la console Baidu.

        <application android:name="yourprojectname.DemoApplication"

9. Ajouter la configuration suivante dans l’élément d’application après le **. MainActivity** élément d’activité, remplacement *yourprojectname* (par exemple, **com.example.BaiduTest**) :

        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Ajoutez une nouvelle classe nommée **ConfigurationSettings.java** au projet.

    ![][28]

    ![][29]

10. Ajouter le code suivant :

        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }

    La valeur de **API_KEY** avec ce que vous extrait du Baidu nuage projet précédemment, **NotificationHubName** avec votre nom de concentrateur de notification à partir du portail classique Azure et **NotificationHubConnectionString** avec DefaultListenSharedAccessSignature à partir du portail classique d’Azure.

11. Ajoutez une nouvelle classe appelée **DemoApplication.java**et lui ajouter le code suivant :

        import com.baidu.frontia.FrontiaApplication;

        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }

12. Ajoutez une autre nouvelle classe appelée **MyPushMessageReceiver.java**et ajoutez le code ci-dessous. Il s’agit de la classe qui gère les notifications de type Pousser sont reçues à partir du serveur d’émission Baidu.

        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();

            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;

                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }

                registerWithNotificationHubs();
            }

            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }

            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }

            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }

            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }

13. Ouvrez **MainActivity.java**et ajoutez le code suivant à la méthode **onCreate** :

            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Ouvrir les instructions d’importation suivantes en haut :

            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

##<a name="send-notifications-to-your-app"></a>Envoyer des notifications à votre application


Vous pouvez rapidement tester la réception de notifications dans votre application en envoyant des notifications dans [Azure Portal](https://portal.azure.com/) à l’aide du bouton **Tester Envoyer** sur le concentrateur de notification, comme indiqué dans l’écran ci-dessous.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Les notifications de transmission sont généralement envoyées dans un service back-end, tels que les Services mobiles ou ASP.NET à l’aide d’une bibliothèque compatible. Vous pouvez également utiliser l’API REST directement à envoyer des messages de notification si une bibliothèque n’est pas disponible pour votre back-end.

Dans ce didacticiel, nous faire simple et simplement démontrer votre application cliente de test en envoyant des notifications à l’aide du Kit de développement .NET pour moyeux de notification dans une application de console au lieu d’un service back-end. Nous vous recommandons le didacticiel [Utilisation des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) que l’étape suivante pour l’envoi de notifications à partir d’un serveur principal d’ASP.NET. Toutefois, les approches suivantes peuvent être utilisés pour envoyer des notifications :

* **Interface REST**: vous pouvez bénéficier de notification sur n’importe quelle plate-forme de serveur principal à l’aide de l' [interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Kit de développement logiciel Microsoft Azure Notification concentrateurs .NET**: dans le Nuget Gestionnaire de package de Visual Studio, exécutez [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : [l’utilisation de concentrateurs de Notification de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

* **Applications Mobile**: pour obtenir un exemple de la façon d’envoyer des notifications à partir d’un back-end le Service Azure App Apps Mobile qui est intégré avec les concentrateurs de Notification, reportez-vous à la section [Ajouter des notifications de type pousser à votre application mobile](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java / PHP**: pour obtenir un exemple de la façon d’envoyer des notifications à l’aide de l’API reste, reportez-vous à la section « Comment faire pour utiliser des concentrateurs de Notification à partir de Java/PHP » ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

##<a name="optional-send-notifications-from-a-net-console-app"></a>(Facultatif) Envoyer des notifications à partir d’une application de console .NET.

Dans cette section, nous montrons à envoyer une notification à l’aide d’une application de console .NET.

1. Créez une nouvelle application de console Visual C# :

    ![][30]

2. La valeur **par défaut du projet de** votre projet d’application console dans la fenêtre de la Console du Gestionnaire de package et puis dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package Microsoft.Azure.NotificationHubs

    Cette opération ajoute une référence à la Notification de concentrateurs Azure SDK à l’aide du <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package Microsoft.Azure.Notification concentrateurs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Ouvrez le fichier **Program.cs** et ajoutez l’instruction using :

        using Microsoft.Azure.NotificationHubs;

4. Dans votre `Program` de classe, ajoutez la méthode suivante et remplacez *DefaultFullSharedAccessSignatureSASConnectionString* et *NotificationHubName* avec les valeurs que vous avez.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }

5. Ajoutez les lignes suivantes dans votre méthode **Main** :

         SendNotificationAsync();
         Console.ReadLine();

##<a name="test-your-app"></a>Tester votre application.

Pour tester cette application avec un de téléphone, il suffit de connecter le téléphone à votre ordinateur à l’aide d’un câble USB. Cela charge votre application sur le téléphone attaché.

Pour tester cette application avec l’émulateur, sur la barre d’outils supérieure de Eclipse, cliquez sur **exécuter**, puis sélectionnez votre application. Cela démarre l’émulateur et puis charge et exécute l’application.

L’application récupère le « userId » et « channelId » à partir du service de notification Baidu Push et inscrit avec le concentrateur de notification.

Pour envoyer une notification de test, vous pouvez utiliser l’onglet débogage du portail Azure classique. Si vous avez créé l’application de console pour Visual Studio .NET, appuyez simplement sur la touche F5 dans Visual Studio pour exécuter l’application. L’application envoie une notification qui s’affiche dans la zone supérieure de votre périphérique ou l’émulateur.


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Kit de développement logiciel Services mobiles Android]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Kit de développement logiciel Android Baidu Push]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure Portal classique]: https://manage.windowsazure.com/
[Portail de Baidu]: http://www.baidu.com/
