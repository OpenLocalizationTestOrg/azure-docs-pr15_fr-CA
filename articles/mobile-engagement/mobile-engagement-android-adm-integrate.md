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


#<a name="how-to-integrate-adm-with-engagement"></a>Comment intégrer des ADM et Engagement

> [AZURE.IMPORTANT] Vous devez suivre la procédure d’intégration décrite dans comment intégrer un Engagement sur document Android avant de suivre ce guide.
>
> Ce document est utile uniquement si vous déjà intégré le module de la portée et le plan pour pousser les périphériques Amazon. Pour intégrer la portée des campagnes dans votre application, lisez d’abord comment intégrer Engagement atteindre sur Android.

##<a name="introduction"></a>Introduction

Intégration ADM permet à votre application à être envoyée lorsque vous ciblez des appareils Android Amazon.

Charges d’ADM poussées toujours le Kit de développement SDK contient la `azme` clé dans l’objet de données. Ainsi, si vous utilisez ADM pour un autre objet dans votre application, vous pouvez filtrer les push basé sur cette clé.

> [AZURE.IMPORTANT] Seuls les périphériques Amazon Kindle 4.0.3 Android en cours d’exécution ou ci-dessus sont pris en charge par la messagerie de périphérique Amazon ; Toutefois, vous pouvez intégrer ce code en toute sécurité sur d’autres périphériques.

##<a name="sign-up-to-adm"></a>Inscrivez-vous pour ADM

Si n’est déjà fait, vous devez activer ADM sur votre compte Amazon.

La procédure est détaillée à : [<https://developer.amazon.com/sdk/adm/credentials.html>].

À la fin de la procédure, vous obtenez :

-   Identification de OAuth (un ID de Client et un Secret de Client) pour mission de pouvoir distribuer vos périphériques.
-   Une clé d’API qui doivent être intégrées dans votre application.

##<a name="sdk-integration"></a>Intégration du Kit de développement logiciel

### <a name="managing-device-registrations"></a>Gestion des enregistrements de périphérique

Chaque périphérique doit envoyer une commande d’enregistrement aux serveurs ADM, sinon ils ne peut pas être atteint.

Si vous déjà utilisez la [bibliothèque de client ADM]et avez déjà [intégré ADM] , vous pouvez accéder directement à recevoir d’android-sdk-adm.

Si vous n’avez pas encore intégré ADM, Engagement a un moyen plus simple pour l’activer dans votre application :

Modifier vos `AndroidManifest.xml` fichier :

-   Ajouter de l’espace de noms d’Amazon, ce fichier doit commencer comme suit :

        <?xml version="1.0" encoding="utf-8"?>
        <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   À l’intérieur de la `<application/>` , ajoutez cette section :

        <amazon:enable-feature
           android:name="com.amazon.device.messaging"
           android:required="false"/>

        <meta-data android:name="engagement:adm:register" android:value="true" />

-   Après l’ajout de la balise d’amazon, vous pouvez avoir une erreur de build si votre cible de Build de projet est inférieur à 2.1 Android. Vous devez utiliser une cible de génération **Android 2.1 +** (ne vous inquiétez pas, vous avez encore une `minSdkVersion` la valeur 4).
-   Intégrer la clé API ADM une immobilisation en suivant [cette procédure].

Puis suivez les instructions des sections suivantes.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Numéro d’inscription au service Push de l’Engagement de communiquer et de recevoir des notifications

Pour communiquer l’id d’enregistrement de l’équipement pour le service Push de l’Engagement et recevoir ses notifications, ajoutez le code suivant à votre `AndroidManifest.xml` de fichiers, à l’intérieur du `<application/>` (même si vous utilisez ADM sans Engagement) de la balise :

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Assurez-vous de disposer des autorisations suivantes votre `AndroidManifest.xml` (avant la `</application>` balise).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##<a name="grant-engagement-oauth-credentials"></a>Informations d’identification de subvention Engagement OAuth

Envoyer vos informations d’authentification OAuth (ID de Client et le code Secret du Client) dans un portail d’Engagement.

[< https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[Bibliothèque client ADM]:https://developer.amazon.com/sdk/adm/setup.html
[ADM intégré]:https://developer.amazon.com/sdk/adm/integrating-app.html
[Cette procédure]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
