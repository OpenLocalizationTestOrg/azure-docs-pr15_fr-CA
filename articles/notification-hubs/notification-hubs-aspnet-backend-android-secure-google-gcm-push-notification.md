<properties
    pageTitle="Envoi de Notifications de transmission sécurisée avec Notification Azure concentrateurs"
    description="Apprenez à envoyer des notifications de transmission sécurisé à une application d’Android d’Azure. Exemples de code écrits en Java et C#."
    documentationCenter="android"
    keywords="Pousser la notification, les notifications de type Pousser, envoyer des messages, des notifications de type Pousser android"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

#<a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Envoi de Notifications de transmission sécurisée avec Notification Azure concentrateurs

> [AZURE.SELECTOR]
- [Universel de Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [e/s](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

##<a name="overview"></a>Vue d’ensemble

> [AZURE.IMPORTANT] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Prise en charge des notifications push dans Microsoft Azure vous permet d’accéder à une infrastructure de message push facile à utiliser, multi-plateformes, à grande échelle, ce qui simplifie considérablement l’implémentation des notifications de type Pousser pour les consommateurs et les entreprise des applications pour les plates-formes mobiles.

En raison des réglementations ou des contraintes de sécurité, une application peut parfois inclure quelque chose dans la notification ne peut pas être transmise par le biais de l’infrastructure de notification de transmission standard. Ce didacticiel explique comment réaliser la même expérience en envoyant des informations sensibles via une connexion sécurisée et authentifiée entre le périphérique Android client et le serveur principal chargé de l’application.

À un niveau élevé, le flux est la suivante :

1. Application back-end :
    - Magasins transport de données sécurisé dans la base de données back-end.
    - Renvoie l’ID de la notification au périphérique Android (aucune information sécurisée n’est envoyée).
2. L’application sur le périphérique, lors de la réception de la notification :
    - L’appareil Android contacte back-end demandant le transport de données sécurisé.
    - L’application peut afficher la charge utile sous la forme d’une notification sur le périphérique.

Il est important de noter que dans le flux précédent (et, dans ce didacticiel), nous partons du principe que l’appareil stocke un jeton d’authentification dans le stockage local, une fois que l’utilisateur se connecte. Cela garantit une expérience complètement transparente, comme le périphérique peut récupérer de données sécurisé de la notification à l’aide de ce jeton. Si votre application ne stocke pas les jetons d’authentification sur le périphérique, ou si ces jetons peuvent être expirés, l’application de périphérique, lors de la réception de la notification de transmission doit afficher une notification générique, demander à l’utilisateur pour lancer l’application. Ensuite, l’application authentifie l’utilisateur et indique la charge utile de notification.

Ce didacticiel montre comment envoyer des notifications de transmission sécurisée. Il s’appuie sur le didacticiel [d’Avertir les utilisateurs](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , afin que vous devez effectuer tout d’abord les étapes dans ce didacticiel si vous ne l’avez pas déjà.

> [AZURE.NOTE] Ce didacticiel suppose que vous avez créé et configuré votre concentrateur de notification, comme décrit dans [Mise en route avec les concentrateurs de Notification (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modifier le projet Android

Maintenant que vous avez modifié votre back-end app pour envoyer simplement l' *id* d’une notification de transmission, vous devez modifier votre application Android pour gérer cette notification et de rappel de vos principaux pour récupérer le message de sécurité s’affiche.
Pour atteindre cet objectif, vous devez vous assurer que votre application Android sait comment authentifier avec votre back-end, lorsqu’il reçoit les notifications de transmission.

Nous allons maintenant modifier le flux de *connexion* pour enregistrer la valeur d’en-tête d’authentification dans les préférences partagées de votre application. Des mécanismes similaires peuvent être utilisés pour stocker n’importe quel jeton d’authentification (par exemple, OAuth jetons) que l’application devra utiliser sans avoir besoin d’informations d’identification de l’utilisateur.

1. Dans votre projet d’application Android, ajoutez les constantes suivantes en haut de la classe **MainActivity** :

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Toujours dans la classe **MainActivity** , mettre à jour la `getAuthorizationHeader()` méthode pour contenir le code suivant :

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3. Ajoutez le code suivant `import` instructions en haut du fichier **MainActivity** :

        import android.content.SharedPreferences;

Nous allons maintenant modifier le gestionnaire qui est appelé lors de la réception de la notification.

4. Dans le **MyHandler** classe modifier le `OnReceive()` méthode contient :

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

5. Ajoutez ensuite le `retrieveNotification()` méthode, en remplaçant l’espace réservé `{back-end endpoint}` avec le point de terminaison du back-end obtenue lors du déploiement de votre back-end :

        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }


Cette méthode appelle votre principale application pour récupérer le contenu de la notification à l’aide des informations d’identification stockées dans les préférences partagées et l’affiche sous la forme d’une notification standard. La notification se présente à l’utilisateur de l’application exactement comme les autres notifications push.

Notez qu’il est préférable de traiter les cas de rejet ou de propriété d’en-tête d’authentification manquants en back-end. Le traitement spécifique de ces cas dépend essentiellement de votre expérience de l’utilisateur cible. Une option consiste à afficher une notification avec un message générique à l’utilisateur de s’authentifier à récupérer de la notification.

## <a name="run-the-application"></a>Exécution de l’Application

Pour exécuter l’application, effectuez les opérations suivantes :

1. Assurez-vous que **AppBackend** est déployée dans Azure. Si vous utilisez Visual Studio, exécutez l’application Web API **AppBackend** . Une page web d’ASP.NET s’affiche.

2. Dans Eclipse, exécutez l’application sur l’émulateur ou sur un appareil Android physique.

3. Dans l’interface utilisateur d’application Android, entrez un nom d’utilisateur et le mot de passe. Ce peuvent être n’importe quelle chaîne, mais ils doivent être de la même valeur.

4. Dans l’interface utilisateur d’application Android, cliquez sur **se connecter**. Cliquez ensuite sur **Envoyer push**.
