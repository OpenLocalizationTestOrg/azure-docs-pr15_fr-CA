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


#<a name="upgrade-procedures"></a>Procédures de mise à niveau

Si vous avez déjà intégré une ancienne version de notre kit de développement logiciel dans votre application, vous devez considérer les points suivants lors de la mise à niveau de la SDK.

Il se peut que vous deviez suivre plusieurs procédures si vous avez manqué plusieurs versions du Kit de développement. Par exemple, si vous migrez de 1.4.0 vers 1.6.0, que vous devez tout d’abord suivre la procédure « de 1.4.0 à 1.5.0 » puis la procédure « de 1.5.0 à 1.6.0 ».

Quelle que soit la version de mise à niveau à partir, vous devez remplacer le `mobile-engagement-VERSION.jar` par le nouveau.

##<a name="from-420-to-421"></a>À partir de 4.2.0 à 4.2.1

Cette étape peut effectivement être effectuée sur n’importe quelle version du Kit de développement, il est une amélioration de la sécurité lorsque vous intégrez des activités de la portée.

Vous devez maintenant ajouter `exported="false"` à toutes les activités de portée.

Activités de portée doivent maintenant ressembler à ceci votre `AndroidManifest.xml`:

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

##<a name="from-400-to-410"></a>À partir de 4.0.0 à 4.1.0

Le Kit de développement logiciel maintenant poignée nouveau modèle d’autorisation de M Android.

Si vous utilisez les fonctionnalités de l’emplacement ou les notifications de la vue d’ensemble, consultez [Cette section](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Outre le nouveau modèle d’autorisation, nous prennent désormais en charge les fonctionnalités emplacement configuration lors de l’exécution.
Nous sommes toujours compatibles avec les paramètres de manifeste pour l’emplacement, mais elle est maintenant désapprouvée. Pour utiliser la configuration de l’exécution, supprimez les sections suivantes à partir de votre ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

et de lire [cette procédure de mise à jour](mobile-engagement-android-integrate-engagement.md#location-reporting) pour utiliser à la place de configuration de l’exécution.

##<a name="from-300-to-400"></a>À partir de 3.0.0 à 4.0.0

### <a name="native-push"></a>Push natif

Push natif (GCM/ADM) est désormais également utilisé pour dans les notifications de l’application vous devez configurer les informations d’identification de push natif pour n’importe quel type de campagne de push.

Si pas déjà, procédez comme [suit](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Intégration de portée a été modifiée dans ``AndroidManifest.xml``.

Remplacez ceci :

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Par

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
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

Il existe probablement un écran de chargement désormais lorsque vous cliquez sur une annonce (avec texte/contenu web) ou un sondage.
Vous devez ajouter ce pour ces campagnes dans 4.0.0 :

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Ressources

Incorporer la nouvelle `res/layout/engagement_loading.xml` le fichier dans votre projet.

##<a name="from-240-to-300"></a>À partir de 2.4.0 à 3.0.0

Voici comment migrer une intégration SDK à partir du service de Capptain offert par Capptain SAS dans une application grâce à Azure Mobile Engagement. Si vous migrez à partir d’une version antérieure, consultez le site web Capptain pour effectuer une migration vers 2.4.0 tout d’abord, puis appliquez la procédure suivante.

>[AZURE.IMPORTANT] Capptain et l’Engagement de Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous ne souligne que la migration de l’application cliente. Le Kit de développement de l’application de la migration ne migre pas vos données à partir des serveurs Capptain vers les serveurs de l’Engagement de Mobile.

### <a name="jar-file"></a>Fichier JAR

Remplacer `capptain.jar` par `mobile-engagement-VERSION.jar` dans votre `libs` dossier.

### <a name="resource-files"></a>Fichiers de ressources

Chaque fichier de ressources que nous avons fournis (préfixés par `capptain_`) doit être remplacé par les nouveaux (avec le préfixe `engagement_`).

Si vous avez personnalisé les fichiers, vous devez appliquer à nouveau votre personnalisation sur les nouveaux fichiers, **tous les identificateurs dans les fichiers de ressources ont également été renommés**.

### <a name="application-id"></a>ID de l’application

Engagement utilise désormais une chaîne de connexion pour configurer les identificateurs du Kit de développement logiciel comme l’identificateur de l’application.

Vous devez utiliser `EngagementAgent.init` méthode dans votre activité de lancement comme suit :

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

La chaîne de connexion de votre application s’affiche sur le portail Azure.

Veuillez supprimer n’importe quel appel à `CapptainAgent.configure` en tant que `EngagementAgent.init` remplace cette méthode.

Le `appId` peut ne plus être configuré à l’aide de `AndroidManifest.xml`.

Veuillez supprimer cette section à partir de votre `AndroidManifest.xml` si vous l’avez :

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>API Java

Chaque appel à n’importe quelle classe Java de notre kit de développement logiciel doit donc être changé ; par exemple, `CapptainAgent.getInstance(this)` doit être renommé `EngagementAgent.getInstance(this)`, `extends CapptainActivity` doit être renommé `extends EngagementActivity` etc....

Si vous ont été intégrées avec les fichiers de préférences de l’agent par défaut, le nom de fichier par défaut est désormais `engagement.agent` et la clé est `engagement:agent`.

Lorsque vous créez des annonces du web, le binder Javascript est désormais `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml

De nombreuses modifications s’est-il passé, le service n’est pas plus partagé et un grand nombre de destinataires ne sont pas exportables plus.

La déclaration du service est désormais plus simple. supprimer le filtre d’intention et toutes les métadonnées qu’il contient et ajoutez `exportable=false`.

De plus, tout est renommé pour utiliser l’engagement.

Il ressemble maintenant à :

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Lorsque vous souhaitez activer les journaux des tests, les meta-données a maintenant été déplacé vers la balise de l’application et a été renommé :

            <application>
            
              <meta-data android:name="engagement:log:test" android:value="true" />
            
              <service/>
            
            </application>

Toutes les autres métadonnées ont simplement été renommées, voici la liste complète (cours de modification de nom uniquement celles que vous utilisez) :

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
            
            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Lecture de Google et de suivi de SmartAd a été supprimé de SDK, il vous suffit de supprimer ce sans remplacement :

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

Les activités de portée sont désormais déclarées comme suit :

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            
Si vous avez des activités personnalisées de portée, vous devez uniquement modifier les actions d’intention pour correspondre à une ou l’autre `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` ou `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Les récepteurs de diffusion ont été renommés, plus nous maintenant d’ajouter `exported=false`. Voici la liste complète des récepteurs avec la nouvelle spécification, (cours de modification de nom uniquement celles que vous utilisez) :

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Récepteur de suivi a été supprimé, donc vous devez supprimer cette section :

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Notez que la déclaration de votre implémentation du récepteur de diffusion **EngagementMessageReceiver** a été modifiée dans le `AndroidManifest.xml`. C’est parce que l’API pour envoyer et supprimer des messages XMPP arbitraires à partir des entités XMPP arbitraires et l’API pour envoyer et recevoir des messages entre les périphériques ont été supprimés. Par conséquent, vous devez également supprimer les rappels suivants à partir de votre implémentation de **EngagementMessageReceiver** :

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

et

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

Supprimez tout appel sur **EngagementAgent** pour :

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

et

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard

Configuration proguard peut être affectée par la redésignation, les règles sont désormais ressembler à :

            -dontwarn android.**
            -keep class android.support.v4.** { *; }
            
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }
 
