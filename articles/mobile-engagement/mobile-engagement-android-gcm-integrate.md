<properties
    pageTitle="Intégration du SDK Android Azure Engagement Mobile"
    description="Dernières mises à jour et les procédures pour Android SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-gcm-with-mobile-engagement"></a>Comment intégrer le GCM avec Engagement Mobile

> [AZURE.IMPORTANT] Vous devez suivre la procédure d’intégration décrite dans comment intégrer un Engagement sur document Android avant de suivre ce guide.
>
> Ce document est utile uniquement si vous déjà intégré le module de la portée et le plan pour pousser des périphériques de lecture de Google. Pour intégrer la portée des campagnes dans votre application, lisez d’abord comment intégrer Engagement atteindre sur Android.

##<a name="introduction"></a>Introduction

Intégration de GCM permet à votre application à subir un push.

Charges GCM poussées toujours le Kit de développement SDK contient la `azme` clé dans l’objet de données. Ainsi, si vous utilisez GCM pour un autre objet dans votre application, vous pouvez filtrer les push basé sur cette clé.

> [AZURE.IMPORTANT] Seuls les périphériques exécutant Android 2.2 ou ci-dessus, dont Google Play installé et avoir Google connexion arrière-plan activé peut être envoyée à l’aide de GCM ; Toutefois, vous pouvez intégrer ce code en toute sécurité sur des périphériques non pris en charge (il utilise simplement intentions).

##<a name="create-a-google-cloud-messaging-project-with-api-key"></a>Créez un projet de messagerie de Google Cloud avec clé API

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

##<a name="sdk-integration"></a>Intégration du Kit de développement logiciel

### <a name="managing-device-registrations"></a>Gestion des enregistrements de périphérique

Chaque périphérique doit envoyer une commande d’enregistrement sur les serveurs Google, sinon ils ne peut pas être atteint.

Un périphérique peut également annuler l’enregistrement de notifications de GCM (le périphérique est automatiquement annulé si l’application est désinstallée).

Si vous n’utilisez pas [Google lire le Kit de développement] , ou vous ne pas envoyer déjà l’intention d’inscription vous-même, vous pouvez apporter à Engagement d’inscrire automatiquement de l’appareil pour vous.

Pour ce faire, ajoutez le code suivant à votre `AndroidManifest.xml` de fichiers, à l’intérieur du `<application/>` balise :

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Numéro d’inscription au service Push de l’Engagement de communiquer et de recevoir des notifications

Pour communiquer l’id d’enregistrement de l’équipement pour le service Push de l’Engagement et recevoir ses notifications, ajoutez le code suivant à votre `AndroidManifest.xml` de fichiers, à l’intérieur du `<application/>` (même si vous gérez vous-même les enregistrements du périphérique) de la balise :

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Assurez-vous de disposer des autorisations suivantes votre `AndroidManifest.xml` (après le `</application>` balise).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##<a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Engagement Mobile l’accès à votre clé d’API GCM

Suivez [ce guide](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) pour accorder l’accès de l’Engagement de Mobile à votre clé d’API GCM.

[Lecture de Google SDK]:https://developers.google.com/cloud-messaging/android/start
