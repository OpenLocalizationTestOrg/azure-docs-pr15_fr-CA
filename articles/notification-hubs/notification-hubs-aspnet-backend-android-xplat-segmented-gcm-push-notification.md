<properties
    pageTitle="Notification de concentrateurs de dernières actualités didacticiel - Android"
    description="Apprenez à utiliser des concentrateurs de Notification de Bus de Service Azure pour envoyer des notifications de news de dernière minute à des appareils Android."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>


# <a name="use-notification-hubs-to-send-breaking-news"></a>Concentrateurs de Notification permet d’envoyer des informations de dernière minute

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous indique comment utiliser des concentrateurs de Notification Azure pour diffuser des notifications de news de dernière minute à une application d’Android. Lorsque vous avez terminé, vous pourrez inscrire pour casser des catégories d’informations que qui ne vous intéressent et recevoir les notifications de transmission uniquement pour ces catégories. Ce scénario est un modèle courant pour nombreuses applications où les notifications doivent être envoyées à des groupes d’utilisateurs que vous ont déclaré précédemment d’intérêt, par exemple, le lecteur RSS, applications pour les passionnés de musique, etc..

Scénarios de diffusion sont activés en incluant une ou plusieurs _balises_ lors de la création d’un enregistrement dans le concentrateur de notification. Lorsque les notifications sont envoyées à une balise, tous les périphériques qui ont inscrit pour la balise recevra la notification. Étant donné que les balises sont simplement des chaînes, ils n’ont pas à être mis en service à l’avance. Pour plus d’informations sur les balises, consultez [routage des concentrateurs de Notification et les Expressions de la balise](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Conditions préalables

Cette rubrique s’appuie sur l’application que vous avez créé dans la [mise en route de concentrateurs de Notification][get-started]. Avant de commencer ce didacticiel, vous devez déjà avoir effectué [mise en route de concentrateurs de Notification][get-started].

##<a name="add-category-selection-to-the-app"></a>Ajouter la sélection de la catégorie à l’application

La première étape consiste à ajouter des éléments d’interface utilisateur à votre activité principale existante qui permettent à l’utilisateur de sélectionner les catégories à inscrire. Les catégories sélectionnées par un utilisateur sont stockées sur le périphérique. Lorsque l’application démarre, un enregistrement de périphérique est créé dans votre centre de notification avec les catégories sélectionnées sous forme de balises.

1. Ouvrez votre fichier res/layout/activity_main.xml et remplacez le contenu par le texte suivant :

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">

                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>

2. Ouvrez le fichier res/values/strings.xml et ajoutez les lignes suivantes :

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    Votre mise en page graphique main_activity.xml doit maintenant ressembler à ceci :

    ![][A1]

3. Maintenant, créez une classe de **Notifications** dans le même package que la classe **MainActivity** .

        import java.util.HashSet;
        import java.util.Set;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;

        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;

            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
        
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }

            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }

            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }

            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
        
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
        
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
        
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }

        }

    Cette classe utilise le stockage local pour stocker les catégories de news que ce périphérique doit recevoir. Il contient également des méthodes pour vous inscrire à ces catégories.


4. Supprimer vos champs privés pour **NotificationHub** et **GoogleCloudMessaging**dans votre classe de **MainActivity** , et ajouter un champ pour les **Notifications**:

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

5. Puis, dans la méthode **onCreate** , supprimez l’initialisation du champ **concentrateur** et la méthode **registerWithNotificationHubs** . Puis ajoutez les lignes suivantes qui initialise une instance de la classe de **Notifications** . 


        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;
    
            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
    
            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);
    
            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`et `HubListenConnectionString` doit déjà être défini avec la `<hub name>` et `<connection string with listen access>` des espaces réservés avec votre nom de concentrateur de notification et de la chaîne de connexion pour *DefaultListenSharedAccessSignature* que vous avez obtenu précédemment.

    > [AZURE.NOTE] Étant donné que les informations d’identification qui sont distribuées avec une application client ne sont pas généralement sécurisées, vous devez distribuer la clé pour l’accès d’écoute avec votre application cliente. Écouter access Active votre application à s’inscrire pour les notifications, mais les enregistrements existants ne peuvent pas être modifiée et notifications ne peuvent pas être envoyées. La clé d’accès complet est utilisée dans un service back-end sécurisé pour l’envoi de notifications et de modifier des enregistrements existants.


6. Ensuite, ajoutez les importations ci-après et `subscribe` méthode pour gérer le bouton s’abonner cliquez sur événement :
        
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;

        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");

            notifications.storeCategoriesAndSubscribe(categories);
        }

    Cette méthode crée une liste de catégories et utilise la classe de **Notifications** pour stocker la liste dans le stockage local et inscrire les balises correspondantes avec votre concentrateur de notification. Lorsque les catégories sont modifiées, l’enregistrement est recréé avec les nouvelles catégories.

Votre application est maintenant en mesure de stocker un jeu de catégories dans le stockage local sur le périphérique et l’enregistrer avec le concentrateur de notification chaque fois que l’utilisateur modifie la sélection de catégories.

##<a name="register-for-notifications"></a>S’inscrire pour les notifications

Ces étapes s’inscrire avec le hub de notification au démarrage en utilisant les catégories qui ont été stockés dans le stockage local.

> [AZURE.NOTE] Dans la mesure où les attributs de registrationId affectée par Google Cloud messagerie (GCM) peut modifier à tout moment, vous devez vous inscrire pour les notifications fréquemment afin d’éviter les échecs de notification. Cet exemple s’inscrit pour la notification chaque fois que l’application démarre. Pour les applications qui sont exécutées fréquemment, plus d’une fois par jour, vous pouvez de probablement ignorer l’enregistrement pour préserver la bande passante si l’inscription précédente remonte à moins d’un jour.


1. Ajoutez le code suivant à la fin de la méthode **onCreate** dans la classe **MainActivity** :

        notifications.subscribeToCategories(notifications.retrieveCategories());

    Cela permet de s’assurer que chaque fois que l’application démarre, il extrait les catégories à partir du stockage local et demande une registeration pour ces catégories. 

2. Puis mettez à jour la `onStart()` méthode de le `MainActivity` de classe comme suit :

    @Overrideprotected void onStart() {super.onStart() ;      isVisible = true ;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }

    Cela met à jour l’activité principale en fonction de l’état des catégories précédemment enregistrés.

L’application est maintenant terminée et peut de stocker un jeu de catégories dans le stockage local du périphérique utilisé pour enregistrer avec le concentrateur de notification chaque fois que l’utilisateur modifie la sélection de catégories. Ensuite, nous allons définir un back-end qui peut envoyer des notifications de catégorie pour cette application.

##<a name="sending-tagged-notifications"></a>Envoi des notifications

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Exécutez l’application et générer des notifications

1. Dans Studio Android, générer l’application et le démarrer sur un périphérique ou un émulateur.

    Notez que l’interface utilisateur de l’application fournit un jeu de bascule qui vous permet de choisir les catégories pour vous abonner à.

2. Activer un ou plusieurs bascule de catégories, puis cliquez sur **s’abonner**.

    L’application convertit les catégories sélectionnées dans des balises et demande une nouvelle inscription de périphérique pour les balises sélectionnées à partir du concentrateur de notification. Les catégories inscrites sont retournés et affichés dans une notification toast.

4. Envoyer une notification de nouveau en exécutant l’application de Console de .NET.  Vous pouvez également envoyer des notifications de modèle marquées à l’aide de l’onglet déboguer de votre centre de notification dans le [Portail classique d’Azure].

    Les notifications pour les catégories sélectionnées s’affichent sous forme de notifications de toast.

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons appris à diffuser des informations de dernière minute par catégorie. Pensez à la fin d’un des didacticiels suivants qui mettent en évidence d’autres scénarios avancés de concentrateurs de Notification :

+ [Utiliser des concentrateurs de Notification pour diffuser des informations de dernière minute localisée]

    Apprenez à développer l’application d’actualités rupture pour activer l’envoi de notifications localisée.





<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Utiliser des concentrateurs de Notification pour diffuser des informations de dernière minute localisée]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure Portal classique]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
