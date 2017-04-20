<properties 
   pageTitle="Guides de dépannage Azure Engagement Mobile" 
   description="Guide de dépannage pour Azure Engagement Mobile" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Engagement Mobile - Guide de dépannage

## <a name="introduction"></a>Introduction
Le guide de dépannage suivant vous aidera à comprendre les causes profondes de certains problèmes couramment rencontrées et permettent de résoudre les problèmes sur votre propre. 

## <a name="general"></a>Général

En général, vous devez toujours vous assurer les éléments suivants :

1. Assurez-vous que vous avez parcouru toutes les étapes requises pour l’intégration dans nos [didacticiels de mise en route](mobile-engagement-windows-store-dotnet-get-started.md)
2. Vous utilisez la dernière version de la plate-forme SDK. 
3. Testez sur un périphérique réel et un émulateur car certains problèmes sont spécifiques à l’émulateur uniquement. 
4. Vous atteignez pas les limites/limitations d’Engagement de Mobile qui sont documentées [ici](../azure-subscription-service-limits.md)
5. Si vous n’êtes pas en mesure de se connecter pour le back-end du service Mobile Engagement ou voir ne pas en cours de chargement en continu puis faire en sorte qu’il n’y a aucun incident de service en cours en vérifiant [ici](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problèmes de « Contrôle »

### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Je ne vois pas mon périphérique s’affichent sous l’onglet Moniteur
Onglet Moniteur affiche les périphériques connectés à votre plate-forme Mobile Engagement en temps réel. Si vous déboguez sur un émulateur et un périphérique, vous devez voir au moins une session ici. Si l’application a été distribuée, puis vous verrez l’indicateur de Sessions actives reflètent les périphériques qui sont connectés à la plate-forme en temps réel. 

Si vous ne voyez pas votre appareil dans l’onglet Moniteur, il est probablement d’un problème d’intégration du SDK. Certaines étapes communes à suivre pour résoudre les problèmes sont les suivants :

1. Assurez-vous que vous utilisez la chaîne de connexion correcte dans l’application mobile et c’est à partir de la section clés SDK et pas la section clés de API. La chaîne de connexion connecte à votre application mobile à l’instance de l’application Mobile Engagement dans lequel vous verrez votre périphérique sous l’onglet Moniteur. 
2. Pour les plates-formes Windows - si votre page remplace la `OnNavigatedTo` méthode, veillez à appeler `base.OnNavigatedTo(e)`.
3. Si vous intégrez Mobile Engagement dans une application mobile existante, puis vous pouvez également vous assurer qu’il ne manque toutes les étapes en consultant les étapes d’intégration avancée [ici](mobile-engagement-windows-store-integrate-engagement.md)
4. Vérifiez que vous envoyez au moins un écran/activité en substituant la page avec la EngagementActivity en fonction de la plate-forme que vous utilisez comme décrit dans les [didacticiels de mise en route](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>J’obtiens l’onglet Moniteur affichant une session même lorsque j’ai déconnecté ou fermé mon application / émulateur. 
Si vous êtes le seul connecté à la plate-forme à ce stade et que vous utilisez un émulateur pour ouvrir l’application il s’agit probablement en raison de particularités de l’émulateur. En général, vous devez vous assurer que vous revenez à l’écran d’accueil sur l’émulateur pour la session de l’application pour se déconnecter correctement. En outre, sur la plate-forme Windows, pendant le débogage avec Visual Studio, vous devez assurer que dans Visual Studio, vous accédez à la barre de menu des **Événements de cycle de vie** et cliquez sur **Suspend** vraiment fermer la session. Pour plus d’informations, consultez [didacticiel de Windows](mobile-engagement-windows-store-dotnet-get-started.md) . 

## <a name="analytics-issues"></a>Problèmes de « Analytique »

### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>Je ne vois pas toutes les données / actualisé les données dans les onglet Analytique 
Données d’Analytique sont recalculées sur une base régulière et elle peut prendre jusqu'à 24 heures pour cette actualisation. Ces données n’est pas en temps réel, et vous verrez qu’il est actualisé dans cette période de 24 heures.
Veuillez Vérifiez toutefois que vous envoyez au moins un écran ou une activité sur le back-end de plate-forme d’une substitution au moins une page avec `EngagementActivity` ou l’appel `SendActivity` explicitement. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>J’aperçois incorrectement capturée date/heure pour un périphérique sous l’onglet Analytique
La période d’Analytique est basée sur la date à partir des paramètres de périphérique de l’utilisateur. Assurez-vous que le périphérique est la date correctement définie. 

## <a name="segment-issues"></a>Problèmes de 'Segment'

### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>J’ai créé un segment et il est s’affiche en grisé ou n’affiche ne pas toutes les données
Création de segment n’est pas en temps réel pour le moment. Il est calculé en même temps que les données d’analytique sont agrégées et par conséquent il peut prendre jusqu'à 24 heures. Vous devez revérifier plus tard, mais dans le même temps vous devez également vous assurer que vos applications mobiles envoyez effectivement les données sur la base desquels vous les segments sont de formation. Par exemple Si un événement dites 'foo' n’est pas envoyé par n’importe quel appareil mobile, puis il ne serait pas les données de segment pour un segment créé avec EventName = foo comme critère. Vous devez également vérifier votre intégration du Kit de développement logiciel pour votre application mobile envoie correctement les données. 

## <a name="reach-or-push-notifications-issues"></a>Problèmes de « Atteindre » ou pousser des Notifications

### <a name="my-push-messages-are-not-being-delivered"></a>Mes messages envoyés ne sont pas remis 

1. Essayez d’envoyer des notifications à un dispositif de test pour vous assurer que tous les composants - application mobile, Kit de développement logiciel et le service sont correctement connectés et capable de fournir des notifications de type Pousser. 
2. Toujours envoyer la notification de hors-de-app' simple' tout d’abord par une campagne qui n’est pas planifiée et ni aucun critère public spécifié. Il s’agit à nouveau de prouver que la connectivité de notification fonctionne correctement. 
3. Si vous rencontrez des problèmes lors de la remise dans l’application notifications puis également qu’il est la première étape pour essayer tout d’abord d’envoyer une notification hors-de-app. 
4. Assurez-vous que le « diffuser natif » est correctement configuré pour votre application mobile. En fonction de la plate-forme il impliquera soit clés (Android, Windows) ou des certificats (iOS). Voir [Interface utilisateur - paramètres](mobile-engagement-user-interface-settings.md)
5. En dehors de l’application notifications peuvent également être bloquées par l’utilisateur via le système d’exploitation mobile ainsi garantir que cela n’est pas le cas. 
6. Assurez-vous que vous ne définissez pas l’option *Ignorer une Audience, push sera envoyé aux utilisateurs via l’API* dans la section **campagne** d’une campagne de portée, car cela permet de garantir que les notifications de transmission peuvent uniquement être envoyées via des API. 
7. Assurez-vous que vous testiez votre campagne push avec à la fois un périphérique connecté via Wi-Fi et téléphone réseau de l’opérateur pour éliminer la connexion réseau sous la forme d’une source potentielle de problèmes.
8. Vérifiez que l’horloge système sur votre émulateur de périphériques/est correct car n’importe quel périphérique désynchronisé sera également interférer avec possibilité de Push Notification du Service pour livrer des notifications. 

Plus spécifique de la plate-forme résolution des instructions ci-dessous :

1. **e/s** 

    - Vérifiez que les certificats sont valides et non expirés pour pousser des Notifications d’iOS. 
    - Vérifiez que vous configurez correctement un certificat de *Production* dans votre application Mobile Engagement. 
    - Assurez-vous que vous testez sur un *périphérique physique réel.* Le simulateur d’e/s ne peut pas traiter les messages de type Pousser.
    - Vérifiez que l’identificateur de l’offre groupée est correctement configuré dans l’application mobile. Consultez les instructions fournies [ici](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
    - Lors de l’essai, utiliser une distribution « Ad Hoc » dans votre profil de mise en service mobile. Vous ne serez pas en mesure de recevoir une notification si votre application est compilée à l’aide de « Debug »

2. **Android**

    - Vérifiez que vous avez spécifié le bon numéro de projet dans le fichier de AndroidManifest.xml de votre application mobile qui est suivie par le caractère \n. 
    
            <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
        
    - Vérifiez que vous ne sont pas manquants ou mal configuré des autorisations dans le fichier de manifeste Android 
    - Assurez-vous que le numéro de projet que vous ajoutez à votre application client à partir du même compte où vous avez obtenu la clé GCM du serveur. Toute différence entre les deux empêche votre pousse de sortir. 
    - Si vous recevez des notifications du système, mais pas dans l’application puis la [spécifier une icône de section de notifications](mobile-engagement-android-get-started.md) susceptible de révision, vous ne spécifiez pas l’icône correcte dans le fichier de manifeste Android. 
    - Si vous envoyez une notification BigPicture, puis de sorte que, si vous avez des serveurs d’images externes, puis ils doivent être en mesure de prendre en charge HTTP « GET » et « HEAD ».

3. **Windows**
    
    - Vérifiez que vous avez associé à l’application une application du Windows Store valide. Dans Visual Studio - vous devrez cliquez avec le bouton droit sur le projet et sélectionnez l’option « Associer App avec magasin » et sélectionnez l’application que vous avez créé dans le Windows Store. Cette application du Windows Store doit être le même que celui d’où vous avez obtenu les informations d’identification de push natif pour configurer le portail Mobile Engagement.
    - Si vous recevez des notifications de type Pousser de hors-de-app, mais pas dans l’application des notifications avec `EngagementOverlay` intégration puis vérifiez est un élément de grille racine dans votre page. EngagementOverlay les utilise le premier élément de la « grille » qu’il trouve dans votre fichier xaml pour ajouter deux web affiche sur votre page. Si vous souhaitez trouver où les vues web seront définies, vous pouvez définir une grille nommée « EngagementGrid » comme toutefois avoir afin de garantir suffisamment de hauteur et de largeur pour le web qui suit deux vues qui s’affichent la notification et l’annonce suivante sous de notification dans l’application :
        
            <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>J’ai créé une annonce notification push/de la campagne, et même après qu’il m’a envoyé la notification, il est affiché en tant que 'Active'. Ce que cela signifie ? 
La **campagne** que vous avez créé lors de l’Engagement Mobile est appelée car il s’agit d’un sens de notification push longue nouveaux périphériques se connectent à votre plate-forme mobile d’engagement, ils seront automatiquement envoyés la notification que vous configurez ici, dans la mesure où ils remplissent les critères que vous définissez dans la campagne. Ce n'est pas un un tir de notification unique le programme d’installation. Vous devrez manuellement Cliquez sur le bouton **Terminer** pour mettre fin à la campagne afin qu’il n’envoie pas d’autres notifications. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>J’ai créé une campagne push et je reçois notifications correctement toutefois à chaque fois que j’ouvre l’application, j’obtiens la même notification même lorsque j’avais initié avant ? 
Cela est susceptible de se produire au cours du test et si vous utilisez des émulateurs ou certains test framework comme TestFlight. Ce qui se passe ici est qu’à chaque application d’exécuter l’instance, est acquisition d’un nouvel ID de périphérique et de l’envoyer à notre principal qui est à l’origine de la plate-forme Mobile Engagement pour le traiter comme un nouveau périphérique et envoi de la notification. 

## <a name="getting-support"></a>Prise en charge

Si vous ne parvenez pas à résoudre le problème vous-même, vous pouvez :

1. Recherchez votre problème dans les threads existants sur le forum de StackOverflow et [forum MSDN](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) et si puis pas poser une question. 
2. Si vous trouvez une fonctionnalité manquante puis ajouter/je vote pour la demande sur notre [forum de UserVoice](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Si vous avez Microsoft prennent en charge ouvrir un incident de support en fournissant les informations suivantes : 
    - ID d’abonnement Azure
    - Plate-forme (par exemple, iOS, Android etc.)
    - ID de l’application
    - ID de la campagne (pour les problèmes de notification de transmission)
    - ID de périphérique
    - Version du Kit de développement logiciel de Mobile Engagement (par exemple, le Kit de développement logiciel Android v2.1.0)
    - Détails de l’erreur avec le message d’erreur exact et de scénario
