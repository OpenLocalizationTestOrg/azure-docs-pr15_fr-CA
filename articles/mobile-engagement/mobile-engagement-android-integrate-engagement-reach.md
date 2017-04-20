<properties
    pageTitle="Intégration du SDK Android Azure Engagement Mobile"
    description="Dernières mises à jour et les procédures pour Android SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-reach-on-android"></a>Comment intégrer la portée d’Engagement sur Android

> [AZURE.IMPORTANT] Vous devez suivre la procédure d’intégration décrite dans comment intégrer un Engagement sur document Android avant de suivre ce guide.

##<a name="standard-integration"></a>Intégration standard

Le Kit de développement atteindre requiert la **bibliothèque de prise en charge Android (v4)**.

Le moyen le plus rapide pour ajouter la bibliothèque à votre projet dans **Eclipse** est `Right click on your project -> Android Tools -> Add Support Library...`.

Si vous n’utilisez pas Eclipse, vous pouvez lire les instructions [ici].

Kit de développement SDK dans votre projet, copiez les fichiers de ressources de portée :

-   Copier les fichiers à partir de la `res/layout` dossier fourni avec le Kit de développement dans le `res/layout` dossier de votre application.
-   Copier les fichiers à partir de la `res/drawable` dossier fourni avec le Kit de développement dans le `res/drawable` dossier de votre application.

Modifier vos `AndroidManifest.xml` fichier :

-   Ajoutez la section suivante (entre la `<application>` et `</application>` balises) :

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
              <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
              </intent-filter>
            </receiver>

-   Vous avez besoin de cette autorisation pour relire les notifications du système qui n’étaient pas activées au démarrage (dans le cas contraire elles sont conservées sur le disque, mais ne s’affiche plus, vous devez inclure).

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   Spécifier une icône utilisée pour les notifications (à la fois dans l’application et ceux de système) par la copie et la modification de la section suivante (entre la `<application>` et `</application>` balises) :

            <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT] Cette section est **obligatoire** si vous envisagez d’utiliser des notifications du système lors de la création de campagnes de portée. Android empêche l’affiché des notifications du système sans icônes. Si vous omettez cette section, les utilisateurs finaux ne pourra donc en mesure de les recevoir.

-   Si vous créez des campagnes marketing avec les notifications à l’aide de la vue d’ensemble du système, vous devez ajouter les autorisations suivantes (après la `</application>` tag) s’il est manquant :

            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
            <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

  -   Sur M Android et si votre application cible le niveau API Android 23 ou supérieur, ``WRITE_EXTERNAL_STORAGE`` autorisation nécessite l’approbation de l’utilisateur. Lisez [Cette section](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

-   Pour les notifications du système vous pouvez également spécifier dans la campagne portée si le périphérique doit sonner et/ou vibreur. Pour qu’il fonctionne, vous devez vous assurer que vous avez déclaré l’autorisation suivante (après la `</application>` tag) :

            <uses-permission android:name="android.permission.VIBRATE" />

    Sans cette autorisation, Android empêche les notifications système d’être affichée si vous avez coché l’option a Vibreur dans le Gestionnaire de campagnes d’atteindre ou l’anneau.

-   Si vous générez votre application à l’aide de **ProGuard** et que les erreurs liées à la bibliothèque de prise en charge Android ou le fichier jar d’Engagement, ajoutez les lignes suivantes à votre `proguard.cfg` fichier :

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

## <a name="native-push"></a>Push natif

Maintenant que vous avez configuré le module de la portée, vous devez configurer l’émetteur native pour être en mesure de recevoir les campagnes marketing sur le périphérique.

Nous prenons en charge les deux services sur Android :

  - Les périphériques de lecture de Google : utiliser la [Messagerie de nuage de Google] en suivant le guide [comment intégrer le GCM avec guide d’Engagement](mobile-engagement-android-gcm-integrate.md) .
  - Dispositifs d’Amazon : utilisation [De messagerie périphérique Amazon] en suivant le guide [comment intégrer l’ADM avec guide d’Engagement](mobile-engagement-android-adm-integrate.md) .

Si vous souhaitez cibler des périphériques Amazon et de lecture de Google, son possible pour que tous les éléments à l’intérieur de 1 AndroidManifest.xml/APK pour le développement. Mais lors de l’envoi d’Amazon, ils peuvent rejeter votre application si elles constatent code GCM.

Vous devez utiliser plusieurs APKs dans ce cas.

**Votre application est maintenant prête à recevoir et afficher des campagnes de portée.**

##<a name="how-to-handle-data-push"></a>Comment gérer la transmission de données

### <a name="integration"></a>Intégration

Si vous souhaitez que votre application soit en mesure de recevoir les push de données de portée, vous devez créer une sous-classe de `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` et le référencer dans le `AndroidManifest.xml` fichier (entre la `<application>` et/ou `</application>` balises) :

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Vous pouvez remplacer la `onDataPushStringReceived` et `onDataPushBase64Received` les rappels. Voici un exemple :

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Catégorie

Le paramètre category est facultatif lorsque vous créez une campagne de Push de données et permet la qu'exécute un push de vous permet de filtrer les données. Cette option est utile si vous avez plusieurs récepteurs de diffusion gestion des différents types de données push, ou si vous souhaitez distribuer les différents types de `Base64` données et que vous souhaitez pour identifier leur type avant leur analyse.

### <a name="callbacks-return-parameter"></a>Paramètre de retour des rappels

Voici quelques conseils pour gérer correctement le paramètre de retour de `onDataPushStringReceived` et `onDataPushBase64Received`:

-   Un récepteur de radiodiffusion doit retourner `null` dans le rappel si elle ne sait pas comment traiter une transmission de données. Vous devez utiliser la catégorie pour déterminer si votre récepteur de radiodiffusion doit gérer la diffusion de données ou non.
-   Un de du récepteur de radiodiffusion doit retourner `true` dans le rappel si elle accepte la transmission de données.
-   Un de du récepteur de radiodiffusion doit retourner `false` dans le rappel s’il reconnaît la transmission de données, mais il ignore pour une raison quelconque. Par exemple, `false` lorsque les données reçues ne sont pas valides.
-   Si un récepteur renvoie la diffusion `true` tandis que l’autre une renvoie `false` pour le même émetteur de données, le comportement n’est pas défini, vous ne devez jamais faire cela.

Le type de retour est utilisé uniquement pour les statistiques de la portée :

-   `Replied`est incrémenté si un des récepteurs de diffusion renvoyées `true` ou `false`.
-   `Actioned`est incrémenté que si un des récepteurs de diffusion a retourné `true`.

##<a name="how-to-customize-campaigns"></a>Comment personnaliser des campagnes

Pour personnaliser des campagnes, vous pouvez modifier les mises en page fournies dans le Kit de développement atteint.

Vous devez conserver tous les identificateurs utilisés dans les présentations et conserver les types de vues qui utilisent un identificateur, en particulier pour les affichages de texte et image. Certaines vues sont uniquement utilisées pour masquer ou afficher les zones afin que son type peut être modifié. Veuillez vérifier le code source si vous souhaitez modifier le type d’un affichage dans les mises en page fournies.

### <a name="notifications"></a>Notifications

Il existe deux types de notifications : les notifications système et dans l’application qui utilisent des fichiers de mise en page différente.

#### <a name="system-notifications"></a>Notifications du système

Pour personnaliser les notifications système, vous devez utiliser les **catégories**. Vous pouvez accéder à des [catégories](#categories).

#### <a name="in-app-notifications"></a>Notifications dans l’application

Par défaut, une notification dans l’application est une vue qui est dynamiquement ajoutée à l’interface utilisateur actuelle du activité grâce à la méthode Android `addContentView()`. Il s’agit d’une superposition de notification. Les superpositions de notification sont pour une intégration rapide car ils ne nécessitent pas vous permet de modifier n’importe quelle disposition dans votre application.

Pour modifier l’aspect de vos superpositions de notification, vous pouvez simplement modifier le fichier `engagement_notification_area.xml` à vos besoins.

> [AZURE.NOTE] Le fichier `engagement_notification_overlay.xml` est celle qui est utilisée pour créer une superposition de la notification, elle inclut le fichier `engagement_notification_area.xml`. Vous pouvez également le personnaliser pour l’adapter à vos besoins (tels que le positionnement de la zone de notification dans la superposition).

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Inclure la mise en forme de notification dans le cadre d’une mise en page de l’activité

Superpositions sont très utiles pour une intégration rapide, mais peuvent être gênant ou avoir des effets secondaires dans des cas particuliers. Le système de recouvrement peut être personnalisé au niveau de l’activité, ce qui vous permet d’empêcher les effets secondaires des activités spéciales.

Vous pouvez décider d’inclure notre disposition de notification dans votre mise en page existante grâce à l’instruction Android **inclure** . Voici un exemple d’un texte modifié `ListActivity` mise en page contenant uniquement un `ListView`.

**Avant l’intégration de l’Engagement :**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Après l’intégration d’Engagement :**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

Dans cet exemple, nous avons ajouté un conteneur parent étant donné que la mise en page d’origine utilisé un affichage de liste comme élément de niveau supérieur. Nous avons également ajouté `android:layout_weight="1"` pour pouvoir ajouter un affichage au-dessous d’un affichage de liste configuré avec `android:layout_height="fill_parent"`.

Le Kit de développement atteindre Engagement détecte automatiquement que la présentation de la notification est incluse dans cette activité et qu’il n’ajoute pas une superposition de cette activité.

> [AZURE.TIP] Si vous utilisez un ListActivity dans votre application, une superposition de portée visible vous empêchera de réponse à un clic sur les éléments dans l’affichage de la liste plus. Il s’agit d’un problème connu. Pour contourner ce problème nous vous suggérons d’incorporer la présentation de la notification dans votre propre disposition d’activité liste comme dans l’exemple précédent.

##### <a name="disabling-application-notification-per-activity"></a>Désactivation de la notification d’application par activité

Si vous ne souhaitez pas que la superposition à ajouter à votre activité, et si vous n’incluez pas la mise en forme de notification dans votre propre mise en page, vous pouvez désactiver la superposition pour cette activité dans le `AndroidManifest.xml` en ajoutant un `meta-data` section comme dans l’exemple suivant :

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a>Catégories

Lorsque vous modifiez les mises en page fournies, vous modifiez l’aspect de toutes vos notifications. Les catégories vous permettent de définir recherche ciblée différentes (et éventuellement des comportements) pour les notifications. Une catégorie peut être spécifiée lorsque vous créez une campagne de portée. Gardez à l’esprit que catégories vous permettent également de personnaliser les annonces et les sondages, qui est décrit plus loin dans ce document.

Pour enregistrer un gestionnaire de catégorie pour les notifications, vous devez ajouter un appel lorsque l’application est initialisée.

> [AZURE.IMPORTANT] Veuillez lire l’avertissement concernant l’attribut android : processus \<android-sdk--processus d’engagement\> dans la comment intégrer un Engagement sur Android rubrique avant de continuer.

L’exemple suivant suppose que vous accusé réception de l’avertissement précédent et que vous utilisez une sous-classe de `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

Le `MyNotifier` objet est l’implémentation du Gestionnaire de catégorie de notification. Il s’agit d’une mise en oeuvre de la `EngagementNotifier` interface ou une sous-classe de l’implémentation par défaut : `EngagementDefaultNotifier`.

Notez que le même notifiant peut gérer plusieurs catégories, vous pouvez les enregistrer comme suit :

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Pour remplacer la mise en oeuvre de la catégorie par défaut, vous pouvez enregistrer votre mise en œuvre comme dans l’exemple suivant :

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

La catégorie en cours utilisée dans un gestionnaire est passée comme un paramètre dans la plupart des méthodes que vous pouvez substituer dans `EngagementDefaultNotifier`.

Il est passé comme un `String` paramètre ou indirectement dans une `EngagementReachContent` objet qui a une `getCategory()` méthode.

Vous pouvez modifier la plupart des processus de création de la notification en redéfinissant des méthodes sur `EngagementDefaultNotifier`, pour une personnalisation plus avancée n’hésitez pas à consulter la documentation technique et le code source.

##### <a name="in-app-notifications"></a>Notifications dans l’application

Si vous souhaitez simplement utiliser différentes dispositions pour une catégorie spécifique, vous pouvez implémenter ceci comme dans l’exemple suivant :

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Exemple de `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Comme vous pouvez le voir, l’identificateur de mode de superposition est différent de celui standard. Il est important que chaque mise en page utilisent un identificateur unique pour les superpositions.

**Exemple de `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Comme vous pouvez le voir, l’identificateur d’Affichage de zone de notification est différent de celui standard. Il est important que chaque disposition utilise un identificateur unique pour la zone de notification.

Cet exemple simple de la catégorie permet de notifications d’application (ou dans l’application) s’affichées en haut de l’écran. Nous n’avez pas modifié les identificateurs standard utilisés dans la zone de notification lui-même.

Si vous souhaitez modifier, vous devez redéfinir le `EngagementDefaultNotifier.prepareInAppArea` méthode. Il est recommandé de consulter la documentation technique et le code source de `EngagementNotifier` et `EngagementDefaultNotifier` si vous souhaitez que ce niveau de personnalisation avancée.

##### <a name="system-notifications"></a>Notifications du système

En étendant `EngagementDefaultNotifier`, vous pouvez substituer `onNotificationPrepared` pour modifier la notification qui a été préparée par l’implémentation par défaut.

Par exemple :

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Cet exemple montre une notification du système pour un contenu affiché sous la forme d’un événement en cours lorsque la catégorie « en cours » est utilisée.

Si vous souhaitez générer le `Notification` objet à partir de zéro, vous pouvez retourner `false` à la méthode et l’appel `notify` vous-même sur le `NotificationManager`. Dans ce cas, il est important de le maintenir un `contentIntent`, `deleteIntent` et l’identificateur de notification utilisé par `EngagementReachReceiver`.

Voici un exemple correct de cette implémentation :

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Annonces de seuls de notification

La gestion du clic sur une notification annonce uniquement peut être personnalisé en substituant `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` modifier le prêt `Intent`. À l’aide de cette méthode vous permet de régler les indicateurs facilement.

Par exemple, pour ajouter la `SINGLE_TOP` indicateur :

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Pour les utilisateurs d’Engagement existants, veuillez noter que les notifications système sans action URL maintenant lance l’application s’il était en arrière-plan, afin que cette méthode peut être appelée avec une annonce sans l’URL de l’action. Vous devez envisager que lors de la personnalisation de l’objectif.

Vous pouvez également implémenter `EngagementNotifier.executeNotifAnnouncementAction` à partir de zéro.

##### <a name="notification-life-cycle"></a>Cycle de vie de notification

Lorsque vous utilisez la catégorie par défaut, certaines méthodes de cycle de vie sont appelées sur le `EngagementReachInteractiveContent` objet de rapports statistiques et mettre à jour l’état de la campagne :

-   Lorsque la notification est affichée dans l’application ou placer dans la barre d’état, le `displayNotification` méthode est appelée (rapports statistiques) par `EngagementReachAgent` si `handleNotification` renvoie `true`.
-   Si la notification est fermée, le `exitNotification` méthode est appelée, la statistique est signalée et campagnes suivant peuvent maintenant être traitées.
-   Si la notification est activée, `actionNotification` est appelée, statistique est signalée et l’objectif associé est lancée.

Si votre implémentation de `EngagementNotifier` contourne le comportement par défaut, vous devez appeler ces méthodes de cycle de vie par vous-même. Les exemples suivants illustrent certains cas où le comportement par défaut est ignoré :

-   Vous ne dépassent pas `EngagementDefaultNotifier`, par exemple, vous avez implémenté gestion de catégorie à partir de zéro.
-   Pour les notifications du système, vous a substitué le `onNotificationPrepared` et vous avez modifié `contentIntent` ou `deleteIntent` dans les `Notification` objet.
-   Pour les notifications dans l’application, vous a substitué `prepareInAppArea`, veillez à mapper au moins `actionNotification` pour un de vos U.I des contrôles.

> [AZURE.NOTE] Si `handleNotification` lève une exception, le contenu est supprimée et `dropContent` est appelée. Ceci est signalé dans les statistiques et les campagnes suivant peuvent maintenant être traitées.

### <a name="announcements-and-polls"></a>Annonces et sondages

#### <a name="layouts"></a>Mises en page

Vous pouvez modifier le `engagement_text_announcement.xml`, `engagement_web_announcement.xml` et `engagement_poll.xml` fichiers pour personnaliser le texte annonces, des annonces du web et des sondages.

Ces fichiers partagent deux dispositions courantes pour la zone de titre et de la zone du bouton. La mise en page pour le titre est `engagement_content_title.xml` et utilise le fichier drawable éponyme pour l’arrière-plan. La mise en page pour les boutons d’action et de sortie est `engagement_button_bar.xml` et utilise le fichier drawable éponyme pour l’arrière-plan.

Dans un sondage, la mise en page de question et de leurs choix est gonflés dynamiquement à l’aide de plusieurs fois le `engagement_question.xml` fichier de disposition pour les questions et les `engagement_choice.xml` fichier des choix.

#### <a name="categories"></a>Catégories

##### <a name="alternate-layouts"></a>Autres dispositions

Telles que les notifications, la catégorie de la campagne peut servir pour que vos annonces et les interrogations des formes différentes.

Par exemple, pour créer une catégorie pour une annonce de texte, vous pouvez étendre `EngagementTextAnnouncementActivity` et référencer le `AndroidManifest.xml` fichier :

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Notez que la catégorie dans le mode de filtrage est utilisée pour faire la différence avec l’activité d’annonce par défaut.

Le SDK d’atteindre utilise le système de tentatives pour résoudre l’activité droite pour une catégorie spécifique et qu’il revient de la catégorie par défaut en cas d’échec de la résolution.

Vous devrez implémenter `MyCustomTextAnnouncementActivity`, si vous souhaitez simplement modifier la disposition (en conservant les mêmes identificateurs de vue), il vous suffit de définir la classe comme dans l’exemple suivant :

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Pour remplacer la catégorie par défaut des annonces de texte, remplacez simplement `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` par votre implémentation.

Sondages et les annonces de web peuvent être personnalisés de la même manière.

Les annonces du web, vous pouvez étendre `EngagementWebAnnouncementActivity` et déclarer votre activité dans les `AndroidManifest.xml` comme dans l’exemple suivant :

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Pour les sondages, vous pouvez étendre `EngagementPollActivity` et déclarer votre dans le `AndroidManifest.xml` comme dans l’exemple suivant :

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Mise en œuvre de toutes pièces

Vous pouvez implémenter des catégories pour les activités de votre annonce (et interrogation) sans étendre un de le `Engagement*Activity` classes fournies par le Kit de développement atteint. Ceci est utile par exemple si vous souhaitez définir une mise en page qui n’utilise pas les mêmes vues comme les mises en page standard.

Comme pour la personnalisation de notification avancée, il est recommandé d’examiner le code source de la mise en oeuvre standard.

Voici quelques éléments à garder à l’esprit : portée lance l’activité dans un but spécifique (correspondant au filtre d’intention) plus un paramètre supplémentaire qui est l’identificateur de contenu.

Pour récupérer l’objet de contenu contenant les champs que vous avez spécifié lors de la création de la campagne marketing sur le site web vous pouvez pour cela :

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Pour les statistiques, vous devez signaler le contenu s’affiche dans le `onResume` événement :

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Ensuite, n’oubliez pas d’appeler `actionContent(this)` ou `exitContent(this)` sur l’objet de contenu avant que l’activité se passe en arrière-plan.

Si vous n’appelez pas soit `actionContent` ou `exitContent`, statistiques ne seront pas envoyées (c'est-à-dire aucun analytique sur la campagne) et plus important encore, les campagnes suivant ne seront pas avertis qu’après le redémarrage du processus de l’application.

Orientation ou autres modifications de configuration peuvent rendre le code difficile à déterminer si l’activité passe en arrière-plan ou non, la mise en œuvre standard assure le contenu quitté si l’utilisateur laisse l’activité est déclaré (en appuyant sur `HOME` ou `BACK`), mais pas si l’orientation est modifiée.

Voici la partie intéressante de l’implémentation :

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Comme vous pouvez le voir, si vous avez appelé `actionContent(this)` puis de la fin de l’activité, `exitContent(this)` peut être appelée en toute sécurité sans aucun effet.

[ici]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Nuage de Google messagerie]:http://developer.android.com/guide/google/gcm/index.html
[Dispositif de messagerie Amazon]:https://developer.amazon.com/sdk/adm.html
