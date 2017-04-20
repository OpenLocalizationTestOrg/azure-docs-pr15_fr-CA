<properties
    pageTitle="Configuration avancée pour Azure Mobile Engagement Android SDK"
    description="Décrit les options de configuration avancées, notamment le manifeste Android avec Azure Mobile Engagement Android SDK"
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
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Configuration avancée pour Azure Mobile Engagement Android SDK

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)
- [e/s](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Cette procédure décrit comment configurer différentes options de configuration pour les applications Azure Mobile Engagement Android.

## <a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Autorisations requises
Certaines options nécessitent des autorisations spécifiques, qui sont répertoriées ici pour référence et en ligne dans la fonction. Ajouter ces autorisations à l’AndroidManifest.xml de votre projet, immédiatement avant ou après le `<application>` balises.

Le code d’autorisation doit ressembler à ceci, lorsque vous renseignez l’autorisation appropriée à partir de la table qui suit.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Autorisation | Lorsqu’il est utilisé |
| ---------- | --------- |
| INTERNET | Obligatoire. Pour les rapports de base |
| ACCESS_NETWORK_STATE | Obligatoire. Pour les rapports de base |
| RECEIVE_BOOT_COMPLETED | Obligatoire. Pour afficher le centre de notifications après le redémarrage du périphérique |
| WAKE_LOCK | Recommandé. Permet la collecte des données lors de l’utilisation de Wi-Fi ou lorsque l’écran est désactivée |
| VIBREUR | Facultatif. Active des vibrations lors de la réception de notifications |
| DOWNLOAD_WITHOUT_NOTIFICATION | Facultatif. Active la Notification de présentation Android |
| WRITE_EXTERNAL_STORAGE | Facultatif. Active la Notification de présentation Android |
| ACCESS_COARSE_LOCATION | Facultatif. Permet le reporting sur l’emplacement en temps réel |
| ACCESS_FINE_LOCATION | Facultatif. Permet le reporting sur l’emplacement de base de GPS |

En commençant par M Android, [certaines autorisations sont gérées au moment de l’exécution](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Si vous utilisez déjà ``ACCESS_FINE_LOCATION``, puis vous n’avez pas besoin d’utiliser également des ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Options de configuration de manifeste Android

### <a name="crash-report"></a>Rapport d’incident

Pour désactiver les rapports d’incidents, ajoutez ce code entre les `<application>` et `</application>` balises :

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Seuil de transmission en rafale

Par défaut, les rapports de service Engagement journaux en temps réel. Si vos journaux d’application rapport varie fréquemment, il est préférable pour les journaux de la mémoire tampon et de les signaler à la fois sur un base régulière de temps (appelé « mode rafale »). Pour ce faire, ajoutez le code suivant entre les `<application>` et `</application>` balises :

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Le mode rafale est légèrement augmente la durée de vie de la batterie mais a un impact sur le moniteur de l’Engagement : toutes les sessions et les travaux la durée sont arrondies à la valeur seuil de transmission en rafale (par conséquent, les sessions et les tâches plus courtes que le seuil de transmission en rafale ne soient pas visible). Votre seuil de transmission en rafale doit dépasser 30000 (30 s).

### <a name="session-timeout"></a>Délai d’expiration de session

 Vous pouvez mettre fin à une activité en appuyant sur la touche **Home** ou **vers l’arrière** , en définissant le téléphone inactif ou par passage dans une autre application. Par défaut, une session est terminée dix secondes après la fin de sa dernière activité. Cela permet d’éviter un fractionnement de session chaque fois que l’utilisateur quitte et retourne à l’application, qui peut se produire lorsque l’utilisateur sélectionne une image, la réintègre une notification, etc.. Vous souhaiterez peut-être modifier ce paramètre. Pour ce faire, ajoutez le code suivant entre les `<application>` et `</application>` balises :

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Désactiver les rapports de journal

### <a name="using-a-method-call"></a>À l’aide d’un appel de méthode

Si vous souhaitez que l’Engagement pour arrêter l’envoi de journaux, vous pouvez appeler :

    EngagementAgent.getInstance(context).setEnabled(false);

Cet appel est persistant : il utilise un fichier de préférences partagées.

Si le contrat est actif lorsque vous appelez cette fonction, il peut prendre une minute pour l’arrêt du service. Toutefois, il ne lance le service tout la prochaine fois que vous lancez l’application.

Vous pouvez activer le journal de création de rapports en appelant la même fonction avec `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Intégration dans votre propre`PreferenceActivity`

Au lieu d’appeler cette fonction, vous pouvez également intégrer ce paramètre directement dans votre `PreferenceActivity`.

Vous pouvez configurer l’Engagement d’utiliser votre fichier de préférences (avec le mode de votre choix) dans le `AndroidManifest.xml` de fichier `application meta-data`:

-   Le `engagement:agent:settings:name` clé est utilisée pour définir le nom du fichier de préférences partagées.
-   Le `engagement:agent:settings:mode` clé est utilisée pour définir le mode du fichier Préférences partagés. Utiliser le même mode comme dans votre `PreferenceActivity`. Le mode doit être passé sous la forme d’un nombre : Si vous utilisez une combinaison d’indicateurs de constantes dans votre code, vérifiez la valeur totale.

Engagement toujours utilise le `engagement:key` clé boolean dans le fichier de préférences pour gérer ce paramètre.

L’exemple suivant de `AndroidManifest.xml` affiche les valeurs par défaut :

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

Vous pouvez alors ajouter un `CheckBoxPreference` dans la disposition de préférence comme la suivante :

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />
