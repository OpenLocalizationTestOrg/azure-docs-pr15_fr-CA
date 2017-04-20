<properties
    pageTitle="E/s Mobile Engagement Azure SDK intégration | Microsoft Azure"
    description="Dernières mises à jour et les procédures pour iOS SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-on-ios"></a>Comment faire pour intégrer un Engagement sur iOS

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)
- [e/s](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Cette procédure décrit la méthode la plus simple pour activer Analytique d’Engagement et le contrôle des fonctions dans votre application iOS.

Le Kit de développement logiciel Engagement nécessite iOS6 + et Xcode 8 : la cible de déploiement de votre application doit au moins être iOS 6.

> [AZURE.NOTE]
> Si vous dépendez vraiment XCode 7 vous pouvez utiliser l' [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Il existe un bogue connu sur le module de la portée de cette version précédente lors de l’exécution sur des périphériques d’e/s 10 voir [l’intégration du module portée](mobile-engagement-ios-integrate-engagement-reach.md) pour plus de détails. Si vous choisissez d’utiliser le Kit de développement logiciel v3.2.4 ignorer simplement les `UserNotifications.framework` importer dans l’étape suivante.

Les étapes suivantes sont suffisantes pour activer le rapport des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, les Sessions, les activités, les pannes et les Technicals. L’état des journaux nécessaires pour calculer d’autres statistiques, telles que les événements, les erreurs et les tâches doit être effectuée manuellement à l’aide de l’API d’Engagement (voir [comment utiliser l’Engagement Mobile avancée API dans votre application iOS le balisage](mobile-engagement-ios-use-engagement-api.md) dans la mesure où ces statistiques sont dépend de l’application.

##<a name="embed-the-engagement-sdk-into-your-ios-project"></a>Incorporer le SDK d’Engagement dans votre projet d’e/s

- Télécharger le Kit de développement logiciel iOS à partir [d’ici](http://aka.ms/qk2rnj).

- Ajouter le Kit de développement d’Engagement pour votre projet d’e/s : dans Xcode, cliquez avec le bouton droit sur votre projet et sélectionnez **« Ajouter des fichiers à... »** et sélectionnez le `EngagementSDK` dossier.

- Engagement nécessite des infrastructures supplémentaires pour fonctionner : dans l’Explorateur de projets, ouvrez le volet de votre projet et sélectionnez la bonne cible. Ensuite, ouvrez l’onglet **« Build phases »** et dans le menu **« binaire avec des bibliothèques de liens »** , ajoutez ces infrastructures :

    -   `UserNotifications.framework`-le lien en tant que`Optional`
    -   `AdSupport.framework`-le lien en tant que`Optional`
    -   `SystemConfiguration.framework`
    -   `CoreTelephony.framework`
    -   `CFNetwork.framework`
    -   `CoreLocation.framework`
    -   `libxml2.dylib`

> [AZURE.NOTE] Le framework AdSupport peut être supprimé. Cette structure pour collecter la IDFA a besoin d’engagement. Collection de IDFA peut cependant être désactivée \<e/s-Kit de développement logiciel-engagement-idfa\> pour se conformer à la nouvelle stratégie d’Apple concernant ce code.

##<a name="initialize-the-engagement-sdk"></a>Initialiser l’Engagement du Kit de développement logiciel

Vous devez modifier votre Application de délégué :

-   En haut de votre fichier de mise en œuvre, importer l’agent d’Engagement :

        [...]
        #import "EngagementAgent.h"

-   Initialiser l’Engagement à l’intérieur de la méthode '**applicationDidFinishLaunching :**'ou'**application : didFinishLaunchingWithOptions :**» :

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
          [...]
        }

##<a name="basic-reporting"></a>Création de rapports de base

### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Méthode recommandée : surcharger votre `UIViewController` classes

Pour activer l’état de tous les journaux requis par l’Engagement pour calculer les utilisateurs, les Sessions, les activités, les pannes et les techniques statistiques, vous pouvez simplement effectuer tous les votre `UIViewController` sous-classes héritent de le `EngagementViewController` classes (de la même règle pour `UITableViewController`  - \> `EngagementTableViewController`).

**Sans Engagement :**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Avec Engagement :**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Méthode alternative : appelez `startActivity()` manuellement

Si vous ne pouvez pas ou ne voulez pas surcharger votre `UIViewController` classes, vous pouvez démarrer à la place de vos activités en appelant `EngagementAgent`de méthodes directement.

> [AZURE.IMPORTANT] L’iOS SDK appelle automatiquement le `endActivity()` méthode lors de la fermeture de l’application. Par conséquent, il est *vivement* recommandé d’appeler le `startActivity` méthode chaque fois que l’activité de l’utilisateur modifier, *jamais* appeler le `endActivity` méthode, étant donné que l’appel de cette méthode force la session en cours à terminer.

##<a name="location-reporting"></a>Reporting sur l’emplacement

Conditions de service pour Apple ne permettent pas aux applications d’utiliser la recherche d’emplacements physiques uniquement à des fins de statistiques. Ainsi, il est recommandé d’activer l’emplacement de rapports uniquement si votre application utilise également l’emplacement de suivi pour une autre raison.

En commençant par e/s 8, vous devez fournir une description de la façon dont votre application utilise les services d’emplacement en définissant une chaîne de la clé [NSLocationWhenInUseUsageDescription] ou [NSLocationAlwaysUsageDescription] dans le fichier Info.plist de votre application. Si vous souhaitez l’emplacement du rapport en arrière-plan avec Engagement, ajoutez la clé NSLocationAlwaysUsageDescription. Dans tous les autres cas, ajoutez la clé NSLocationWhenInUseUsageDescription.

### <a name="lazy-area-location-reporting"></a>Reporting sur l’emplacement zone différée

Zone paresseux reporting sur l’emplacement permet de signaler le pays, la région et la localité associée aux périphériques. Ce type de reporting sur l’emplacement utilise uniquement des emplacements réseau (basés sur les ID de cellule ou Wi-Fi). La zone est signalée au moins une fois par session. Le périphérique GPS n’est jamais utilisé, et ce type de rapport de l’emplacement a donc très peu (ne pas de vous dire non) impact sur la batterie.

Zones signalées sont utilisés pour calculer des statistiques géographiques sur les utilisateurs, les sessions, les événements et les erreurs. Ils peuvent également servir comme critère dans les campagnes de la portée. La dernière zone connue signalée pour un périphérique peut être récupérée grâce à l' [API de périphérique].

Pour activer le rapport d’emplacement zone différée, ajoutez la ligne suivante après l’initialisation de l’agent d’Engagement :

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Reporting sur l’emplacement en temps réel

Reporting sur l’emplacement en temps réel permet de signaler la latitude et la longitude associée aux périphériques. Par défaut, ce type de reporting sur l’emplacement utilise uniquement des emplacements réseau (basés sur les ID de cellule ou Wi-Fi), et la génération d’états n’est active que lorsque l’application s’exécute en premier plan (par exemple, lors d’une session).

Temps réel emplacements ne sont *pas* utilisés pour le calcul des statistiques. Leur seul objectif est de permettre l’utilisation de gardiennage en temps réel \<portée-public-gardiennage virtuel\> critère dans les campagnes de la portée.

Pour activer le reporting sur l’emplacement en temps réel, ajoutez la ligne suivante après l’initialisation de l’agent d’Engagement :

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>Création de rapports de base de GPS

Par défaut, reporting en temps réel sur l’emplacement utilise uniquement des emplacements réseau. Pour activer l’utilisation des emplacements GPS de base (qui sont beaucoup plus précises), ajoutez :

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Rapport d’arrière-plan

Par défaut, reporting en temps réel sur l’emplacement est actif uniquement lorsque l’application s’exécute en premier plan (par exemple, lors d’une session). Pour activer la génération d’états également en arrière-plan, ajoutez :

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Lorsque l’application s’exécute en arrière-plan, seuls les emplacements sur le réseau sont signalées, même si vous avez activé le GPS.

La mise en oeuvre de cette fonction appelle [startMonitoringSignificantLocationChanges] lorsque votre application s’exécute en arrière-plan. N’oubliez pas qu’il sera relancer automatiquement votre application en arrière-plan si l’arrivée d’un nouvel événement d’emplacement.

##<a name="advanced-reporting"></a>Reporting avancé

Éventuellement, si vous souhaitez signaler des événements d’application spécifique, des erreurs et des travaux, vous devez utiliser l’API de l’Engagement à travers les méthodes de la `EngagementAgent` classe. Un objet de cette classe peut être récupéré en appelant la `[EngagementAgent shared]` une méthode statique.

L’API d’Engagement permet d’utiliser toutes les fonctionnalités avancées d’Engagement et est détaillé dans la procédure pour utiliser l’API d’Engagement sur iOS (ainsi que dans la documentation technique de le `EngagementAgent` classe).

##<a name="disable-idfa-collection"></a>Désactiver la collecte de IDFA

Par défaut, Engagement utilise [IDFA] pour identifier de manière unique un utilisateur. Mais si vous n’utilisez pas de publicité ailleurs dans l’application, vous risquent d’être refusés par le processus de révision App Store. Collection de IDFA peut être désactivée en ajoutant la macro de préprocesseur `ENGAGEMENT_DISABLE_IDFA` dans votre fichier pch (ou dans le `Build Settings` de votre application). Cela permet de garantir qu’il n’existe aucune référence à `ASIdentifierManager`, `advertisingIdentifier` ou `isAdvertisingTrackingEnabled` dans la version de l’application.

Intégration dans le fichier **prefix.pch** :

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Vous pouvez vérifier que la collection IDFA est correctement désactivée dans votre application en consultant les journaux de test d’Engagement. Voir le Test d’intégration\<e/s-Kit de développement logiciel-contrat-test-idfa\> documentation pour plus d’informations.

##<a name="disable-log-reporting"></a>Désactiver les rapports de journal

### <a name="using-a-method-call"></a>À l’aide d’un appel de méthode

Si vous souhaitez que l’Engagement pour arrêter l’envoi de journaux, vous pouvez appeler :

    [[EngagementAgent shared] setEnabled:NO];

Cet appel est persistant : il utilise `NSUserDefaults` pour stocker les informations.

Vous pouvez activer le journal de création de rapports en appelant la même fonction avec `YES`.

### <a name="integration-in-your-settings-bundle"></a>Intégration dans votre groupe de paramètres

Au lieu d’appeler cette fonction, vous pouvez également intégrer ce paramètre directement dans votre `Settings.bundle` fichier. La chaîne `engagement_agent_enabled` doit être utilisé comme un identificateur de préférence et il doivent être associés à un bouton bascule (`PSToggleSwitchSpecifier`).

L’exemple suivant de `Settings.bundle` montre comment mettre en œuvre :

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[API de l’appareil]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
