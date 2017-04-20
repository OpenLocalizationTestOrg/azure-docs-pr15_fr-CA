<properties
    pageTitle="Envoi de notifications de type Pousser pour Android avec Azure Notification concentrateurs et de la messagerie de nuage Firebase | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser des concentrateurs de Notification Azure et le nuage de messagerie Firebase pour les notifications de type Pousser aux périphériques Android."
    services="notification-hubs"
    documentationCenter="android"
    keywords="envoyer des notifications, notification de transmission, notification de transmission android, fcm, firebase de messagerie en nuage"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-android-with-azure-notification-hubs"></a>Envoi de notifications de type Pousser pour Android avec Azure Notification concentrateurs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

> [AZURE.IMPORTANT] Cette rubrique montre les notifications de transmission avec la messagerie de nuage Firebase de Google (FCM). Si vous continuez d’utiliser la messagerie de nuage Google (GCM), reportez-vous à la section [envoi des notifications de type Pousser pour Android avec GCM et les concentrateurs de Notification d’Azure](notification-hubs-android-push-notification-google-gcm-get-started.md).

Ce didacticiel vous montre comment utiliser des concentrateurs de Notification Azure et le nuage de messagerie Firebase pour envoyer des notifications de type pousser à une application d’Android.
Vous allez créer une application d’Android vide qui reçoit des notifications de type pousser à l’aide de la messagerie de nuage Firebase (FCM).



[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Le code complété pour ce didacticiel peut être téléchargé à partir de GitHub [ici](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).


##<a name="prerequisites"></a>Conditions préalables

> [AZURE.IMPORTANT] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

- En plus d’un compte Azure actif mentionné ci-dessus, ce didacticiel requiert la dernière version de [Studio Android](http://go.microsoft.com/fwlink/?LinkId=389797).
- Android 2.3 ou supérieur pour la messagerie de nuage Firebase.
- Révision du référentiel de Google 27 ou supérieure est nécessaire pour la messagerie de nuage Firebase.
- Services de lecture de Google 9.0.2 ou version ultérieure pour la messagerie de nuage Firebase.
- À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels de concentrateurs de Notification pour les applications Android.


##<a name="create-a-new-android-studio-project"></a>Créez un nouveau projet Studio Android

1. Dans Android Studio, démarrez un nouveau projet de Studio Android.

    ![Studio Android - nouveau projet](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)

2. Cliquez sur le facteur de forme de **téléphone et les Tablet PC** et le **Kit de développement logiciel Minimum** que vous souhaitez prendre en charge. Puis cliquez sur **suivant**.

    ![Studio Android - workflow de création de projet](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)

3. Choisissez **Une activité vide** pour l’activité principale et cliquez sur **suivant**, puis cliquez sur **Terminer**.


##<a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Créez un projet qui prend en charge la messagerie de nuage Firebase

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]


##<a name="configure-a-new-notification-hub"></a>Configurer un nouveau concentrateur de notification

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. dans la lame des **paramètres** de votre centre de notification, sélectionnez **Notification Services** , puis sur **Google (GCM)**. Entrez la clé du serveur FCM copiés précédemment à partir de la [console de Firebase](https://firebase.google.com/console/) et cliquez sur **Enregistrer**.

&emsp;&emsp;![Concentrateurs de Notification Azure - Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

Votre concentrateur de notification est maintenant configuré pour fonctionner avec Messagin de nuage Firebase, et que les chaînes de connexion à la fois à enregistrer votre application pour recevoir et envoyer des notifications de type Pousser.

##<a id="connecting-app"></a>Votre application de se connecter au concentrateur de notification

###<a name="add-google-play-services-to-the-project"></a>Ajouter des services de lecture de Google pour le projet

[AZURE.INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###<a name="adding-azure-notification-hubs-libraries"></a>Ajout de bibliothèques de concentrateurs de Notification Azure


1. Dans le `Build.Gradle` pour l' **application**, ajoutez les lignes suivantes dans la section **dépendances** .

        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. Ajoutez le référentiel suivant après la section **dépendances** .

        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### <a name="updating-the-androidmanifestxml"></a>Mise à jour de la AndroidManifest.xml.


1. Pour prendre en charge FCM, nous devons mettre en œuvre un service d’écoute ID d’Instance dans notre code, qui est utilisée pour [obtenir les jetons de l’enregistrement](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) à l’aide des [API de FirebaseInstanceId de Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). Dans ce didacticiel, nous allons appeler la classe `MyInstanceIDService`. 
 
    Ajouter à l’intérieur de la définition de service suivante dans le fichier AndroidManifest.xml, le `<application>` balises. 

        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>



2. Une fois que nous avons reçu notre jeton d’inscription FCM à partir de l’API FirebaseInstanceId, nous allons l’utiliser pour [Enregistrer avec le concentrateur de Notification Azure](notification-hubs-push-notification-registration-management.md). Nous prendra en charge cet enregistrement dans l’arrière-plan à l’aide d’un `IntentService` nommé `RegistrationIntentService`. Ce service sera également chargé de l’actualisation de notre jeton d’inscription FCM.
 
    Ajouter à l’intérieur de la définition de service suivante dans le fichier AndroidManifest.xml, le `<application>` balises. 

        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>



3. Nous allons également définir un destinataire pour recevoir des notifications. Ajouter à l’intérieur de la définition de récepteur suivant dans le fichier AndroidManifest.xml, le `<application>` balises. Remplacer le `<your package>` espace réservé avec le votre nom de package réel indiqué dans le haut de la `AndroidManifest.xml` fichier.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>



4. Ajoutez le code suivant FCM nécessaire concernant les autorisations ci-dessous le `</application>` balises. Veillez à remplacer `<your package>` avec le nom indiqué en haut de la `AndroidManifest.xml` fichier.

    Pour plus d’informations sur ces autorisations, consultez [configuration d’une application Client de GCM pour Android](https://developers.google.com/cloud-messaging/android/client#manifest) et de [migrer une application de Client GCM pour Android à la messagerie de nuage Firebase](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm).

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />


### <a name="adding-code"></a>Ajout de code


1. Dans la vue projet, développez **app** > **src** > **principal** > **java**. Avec le bouton droit de votre dossier de package sous **java**et cliquez sur **Nouveau**, puis cliquez sur **Une classe Java**. Ajoutez une nouvelle classe nommée `NotificationSettings`. 

    ![Studio Android - nouvelle classe Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)

    Veillez à mettre à jour ces trois espaces réservés dans le code suivant pour le `NotificationSettings` classe :
    * **ID de l’expéditeur**: l’Id de l’expéditeur vous avez obtenu précédemment dans l’onglet **Messagerie de nuage** de vos paramètres de projet dans la [console de Firebase](https://firebase.google.com/console/).
    * **HubListenConnectionString**: la chaîne de connexion **DefaultListenAccessSignature** pour votre concentrateur. Vous pouvez copier cette chaîne de connexion en cliquant sur **Les stratégies d’accès** sur la lame de **paramètres** de votre concentrateur sur le [Portail Azure].
    * **HubName**: utilisez le nom de votre centre de notification qui s’affiche dans la lame de concentrateur dans le [Portail Azure].

    `NotificationSettings`code :

        public class NotificationSettings {
            public static String SenderId = "<Your project number>";
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }

2. À l’aide de la procédure ci-dessus, ajoutez une autre nouvelle classe nommée `MyInstanceIDService`. Il s’agit de notre implémentation de service d’ID de l’Instance écoute.

    Le code de cette classe appellera notre `IntentService` pour [Actualiser le jeton FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) en arrière-plan.

        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;
        
        
        public class MyInstanceIDService extends FirebaseInstanceIdService {
        
            private static final String TAG = "MyInstanceIDService";
        
            @Override
            public void onTokenRefresh() {
        
                Log.d(TAG, "Refreshing GCM Registration Token");
        
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };


3. Ajouter une autre nouvelle classe à votre projet nommé, `RegistrationIntentService`. Il s’agit de la mise en œuvre de notre `IntentService` qui va gérer [l’actualisation le jeton FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) et [l’enregistrement avec le hub de notification](notification-hubs-push-notification-registration-management.md).

    Utilisez le code suivant pour cette classe.

        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;        
        import com.google.firebase.iid.FirebaseInstanceId;
        import com.microsoft.windowsazure.messaging.NotificationHub;
        
        public class RegistrationIntentService extends IntentService {
        
            private static final String TAG = "RegIntentService";
        
            private NotificationHub hub;
        
            public RegistrationIntentService() {
                super(TAG);
            }
        
            @Override
            protected void onHandleIntent(Intent intent) {
        
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
                String storedToken = null;
        
                try {
                    String FCM_token = FirebaseInstanceId.getInstance().getToken();
                    Log.d(TAG, "FCM Registration Token: " + FCM_token);
        
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
        
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
        
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
        
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
        
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
        
                    // Check if the token may have been compromised and needs refreshing.
                    else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
        
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
        
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
        
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
        
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
        
                    else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete registration", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
        
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }


        
4. Dans votre `MainActivity` de classe, ajoutez le code suivant `import` instructions au-dessus de la déclaration de classe.

        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;

5. Ajoutez les membres privés suivants en haut de la classe. Nous utiliserons ces [vérifier la disponibilité des Services de lecture de Google comme recommandé par Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. Dans votre `MainActivity` de classe, ajoutez la méthode suivante à la disponibilité des Services de lecture de Google. 

        /**
         * Check the device to make sure it has the Google Play Services APK. If
         * it doesn't, display a dialog that allows users to download the APK from
         * the Google Play Store or enable it in the device's system settings.
         */
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }


7. Dans votre `MainActivity` de classe, ajoutez le code suivant qui recherchera des Services de lecture de Google avant d’appeler votre `IntentService` pour obtenir votre inscription FCM jeton et de s’inscrire auprès de votre centre de notification.

        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }


8. Dans le `OnCreate` méthode de le `MainActivity` de classe, ajoutez le code suivant pour démarrer le processus d’inscription lorsque l’activité est créée.

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }


9. Ajouter ces méthodes supplémentaires pour le `MainActivity` pour vérifier l’état état et l’état d’application dans votre application.

        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
    
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }


10. Le `ToastNotify` méthode utilise le *« Hello World »* `TextView` contrôle l’état et les notifications de manière permanente dans l’application. Dans votre mise en page activity_main.xml, ajoutez le code suivant pour ce contrôle.

        android:id="@+id/text_hello"

11. Ensuite, nous allons ajouter une sous-classe pour notre récepteur que nous avons défini dans le AndroidManifest.xml. Ajouter une autre nouvelle classe à votre projet nommé `MyHandler`.

12. Ajoutez les instructions d’importation suivantes en haut du `MyHandler.java`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Ajoutez le code suivant pour le `MyHandler` classe rend une sous-classe de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Ce code substitue la `OnReceive` méthode, afin que le Gestionnaire de rapport sur les notifications reçues. Le gestionnaire envoie également les notifications pour le Gestionnaire de notifications Android à l’aide de la `sendNotification()` méthode. Le `sendNotification()` méthode doit être exécutée lorsque l’application n’est pas en cours d’exécution et une notification est reçue.

        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
        
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
        
            private void sendNotification(String msg) {
        
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
        
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
        
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
        
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }


14. Dans un Studio Android sur la barre de menus, cliquez sur **Générer** > **Reconstruire le projet** pour vous assurer qu’aucune erreur n’est présents dans votre code.
15. Exécutez l’application sur un périphérique et vérifier qu'il inscrit avec succès avec le concentrateur de notification. 

    > [AZURE.NOTE] L’inscription peut échouer sur le lancement initial jusqu'à ce que la `onTokenRefresh()` méthode d’instance de service Id est appelée. L’actualisation doit déclencher une inscription réussie avec le hub de notification.

##<a name="sending-push-notifications"></a>Envoi de notifications de type Pousser

Vous pouvez tester la réception des notifications de type Pousser dans votre application en les envoyant via le [Portail Azure] - présentation de la Section de **dépannage** de la lame de concentrateur, comme illustré ci-dessous.

![Envoyer des concentrateurs de Notification Azure - Test](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Facultatif) Envoyer des notifications de type Pousser directement à partir de l’application

>[AZURE.IMPORTANT] Cet exemple de l’envoi de notifications à partir de l’application cliente est fourni uniquement à des fins pédagogiques. Dans la mesure où cette opération nécessite le `DefaultFullSharedAccessSignature` pour être présent dans l’application cliente, il expose votre concentrateur de notification pour le risque qu’un utilisateur peut accéder pour envoyer des notifications non autorisées à vos clients.

Normalement, vous envoie les notifications à l’aide d’un serveur principal. Dans certains cas, vous souhaiterez être en mesure d’envoyer des notifications de type Pousser directement à partir de l’application cliente. Cette section explique comment envoyer des notifications à partir du client à l’aide de l' [API REST de Azure Notification concentrateur](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. Dans la vue de projet Studio Android, développez **App** > **src** > **principal** > **res** > **mise en page**. Ouvrir le `activity_main.xml` fichier de mise en page et cliquez sur le **texte** de l' onglet pour mettre à jour le texte contenu dans le fichier. Mettre à jour avec le code ci-dessous, qui ajoute de nouvelles `Button` et `EditText` pour l’envoi des messages de notification envoyé au concentrateur de notification des contrôles. Ajoutez le code suivant en bas, juste avant `</RelativeLayout>`.

        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Dans la vue de projet Studio Android, développez **App** > **src** > **principal** > **res** > **valeurs**. Ouvrir le `strings.xml` et ajoutez les valeurs de chaîne qui sont référencées par la nouvelle `Button` et `EditText` contrôles. Ajouter ces éléments au bas du fichier, juste avant `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. Dans votre `NotificationSetting.java` , ajoutez le paramètre suivant à la `NotificationSettings` classe.

    Mise à jour `HubFullAccess` avec la chaîne de connexion **DefaultFullSharedAccessSignature** pour votre concentrateur. Cette chaîne de connexion peut être copiée à partir du [Portail Azure] en cliquant sur **Les stratégies d’accès** sur la lame de **paramètres** pour votre concentrateur de notification.

        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";

4. Dans votre `MainActivity.java` de fichiers, ajoutez le code suivant `import` instructions ci-dessus le `MainActivity` classe.

        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;

6. Dans votre `MainActivity.java` de fichiers, ajoutez les membres suivants en haut de la `MainActivity` classe.  

        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;

6. Vous devez créer un jeton de Signature d’accès logiciel (SaS) pour authentifier une demande POST pour envoyer des messages à votre concentrateur de notification. Pour ce faire l’analyse des données clées à partir de la chaîne de connexion, puis en créant le jeton SaS, comme indiqué dans la référence des API du reste [Des Concepts communs](http://msdn.microsoft.com/library/azure/dn495627.aspx) . Le code suivant est un exemple d’implémentation.

    Dans `MainActivity.java`, ajoutez la méthode suivante à la `MainActivity` classe pour analyser la chaîne de connexion.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
    
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }


7. Dans `MainActivity.java`, ajoutez la méthode suivante à la `MainActivity` classe pour créer un jeton d’authentification d’associations de sécurité.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
    
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
    
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
    
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
    
            return token;
        }



8. Dans `MainActivity.java`, ajoutez la méthode suivante à la `MainActivity` classe pour gérer le clic sur le bouton **Envoyer une Notification** et envoyer le message de notification envoyé au concentrateur à l’aide de l’API REST intégré.

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
    
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
    
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
    
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
    
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
    
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
    
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //      "tag1 || tag2 || tag3");
    
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
    
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }



##<a name="testing-your-app"></a>Test de votre application

####<a name="push-notifications-in-the-emulator"></a>Notifications de type Pousser dans l’émulateur

Si vous souhaitez tester les notifications de type pousser à l’intérieur d’un émulateur, assurez-vous que votre image d’émulateur prend en charge le niveau des API Google que vous avez choisi pour votre application. Si votre image ne prend en charge native APIs de Google, vous finirez avec le **SERVICE\_pas\_disponible** exception.

En outre, assurez-vous que vous avez ajouté votre compte Google à votre émulateur en cours d’exécution sous **paramètres** > **comptes**. Dans le cas contraire, votre tentative d’enregistrement avec GCM peut entraîner la **l’authentification\_échec** exception.

####<a name="running-the-application"></a>Exécution de l’application

1. Exécutez l’application et remarquez que l’ID d’enregistrement est signalée pour une inscription réussie.

    ![Test sur Android - inscription de canal](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)

2. Entrez un message de notification à envoyer à tous les appareils Android qui ont inscrit avec le hub.

    ![Test sur Android - envoi d’un message](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)

3. Appuyez sur **Envoyer la Notification**. Affichent les périphériques qui ont de l’application en cours d’exécution un `AlertDialog` instance avec le message de notification de transmission. Les périphériques qui n’ont pas l’application en cours d’exécution mais précédemment inscrits pour les notifications de type Pousser recevra une notification dans le Gestionnaire de Notification Android. Les peuvent être affichés en faisant défiler vers le bas le coin supérieur gauche.

    ![Test sur Android - notifications](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

##<a name="next-steps"></a>Étapes suivantes

Nous vous recommandons le didacticiel [Utilisation des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs] que l’étape suivante. Il va vous montrer comment envoyer des notifications à partir d’un back-end d’ASP.NET à l’aide de balises pour cibler des utilisateurs spécifiques.

Si vous souhaitez que segmenter les utilisateurs en groupes d’intérêt, consultez le didacticiel [Utilisation des concentrateurs de Notification pour envoyer des informations de dernière minute] .

Pour obtenir des informations plus générales sur les concentrateurs de Notification, consultez nos [Conseils de concentrateurs de Notification].

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Conseils de concentrateurs de notification]: notification-hubs-push-notification-overview.md
[Utiliser des concentrateurs de Notification pour les notifications de type Pousser aux utilisateurs]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Concentrateurs de Notification permet d’envoyer des informations de dernière minute]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
