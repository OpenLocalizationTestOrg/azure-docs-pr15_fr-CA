<properties 
    pageTitle="Concentrateurs de Notification Azure - instructions de diagnostic" 
    description="Instructions sur la façon de diagnostiquer des problèmes courants avec les concentrateurs de Notification Azure." 
    services="notification-hubs" 
    documentationCenter="Mobile" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs---diagnosis-guidelines"></a>Concentrateurs de Notification Azure - instructions de diagnostic

##<a name="overview"></a>Vue d’ensemble

Une des questions plus courantes que nous entendons par des clients de concentrateurs de Notification Azure est comment déterminer pourquoi ils ne voient pas une notification envoyée à partir de leur back-end de l’application s’affiche sur le périphérique client - où et pourquoi les notifications ont été supprimées et comment résoudre ce problème. Dans cet article nous étudierons les différentes raisons pourquoi les notifications peuvent être perdues ou ne finissent pas sur les périphériques. Nous allons également par l’intermédiaire de méthodes dans lequel vous pouvez analyser et déterminer la cause. 

Tout d’abord, il est essentiel de comprendre comment concentrateurs de Notification Azure exécute un push de notifications pour les périphériques.
![][0]

Dans un flux de notification d’envoi par défaut, le message est envoyé à partir de l' **application principale** pour **Azure Notification Hub (NH)** , qui à son tour effectue un traitement sur tous les enregistrements en prenant en compte les balises configurés et expressions de balise pour déterminer les « cibles », c'est-à-dire tous les enregistrements qui doivent recevoir les notifications. Ces enregistrements peuvent s’étendre sur tout ou partie de la nos plates-formes prises en charge - iOS, Google, Windows, Windows Phone, Kindle et Baidu pour Android de Chine. Une fois que les cibles sont établies, NH puis pousse les notifications, divisée en plusieurs lots d’enregistrements, à la plate-forme de périphérique spécifique **Du Service de Notification Push (solution)** - par exemple, APNS pour Apple, GCM pour Google etc.. NH authentifie avec la solution respectives selon les informations d’identification que vous définissez dans le portail classique d’Azure dans la page Configurer le concentrateur Notification. La solution transmet les notifications aux **périphériques clients**respectifs. Il s’agit de la plateforme recommandée consiste à fournir des notifications de transmission et de remarque que le dernier tronçon de la remise de notification a lieu entre la solution de plate-forme et le périphérique. Par conséquent, nous ont quatre composants majeurs - *client*, *back-end de l’application* *Azure Notification concentrateurs (NH)* et *Appuyez sur Notification Services (solution)* et un de ces peut entraîner, notifications interrompues Plus de détails sur cette architecture est disponible dans la [Vue d’ensemble des concentrateurs de Notification].

Échec pour livrer des notifications peut se produire pendant le test/mise en œuvre initiale de phase qui peut indiquer un problème de configuration ou il peut se produire dans la production où tout ou partie des notifications peut obtenir perdus indiquant une application plus profonde ou problème de modèle de messagerie. Dans la section ci-dessous nous allons examiner différents scénarios de notifications ignorées comprise entre courants et le type plus rare, que certains d'entre eux vous trouverez évident et d’autres pas trop. 

##<a name="azure-notifications-hub-mis-configuration"></a>Mauvaise configuration de Azure concentrateur de Notifications 

Concentrateurs de Notification Azure doit s’authentifier lui-même dans le contexte de l’application du développeur pour pouvoir envoyer des notifications à la solution respectifs. Cela est rendu possible par le développeur de création d’un compte de développeur avec la plate-forme correspondante (Google, Apple, etc. de Windows), puis l’enregistrer leur application où obtenir des informations d’identification qui doivent être configurés dans le portail dans la section de configuration des concentrateurs de Notification. Si aucune notification se font par l’intermédiaire, la première étape doit consister pour s’assurer que les informations d’identification correctes sont configurées dans le concentrateur de Notification leur mise en correspondance avec l’application créé sous leur compte de développeur spécifiques de plate-forme. Vous trouverez nos [Didacticiels de démarrage lors de l’obtention] de utile de ce processus de manière étape par étape. Voici certaines communes en matière de configuration :

1. **Général**
 
    un) Vérifiez que votre nom de concentrateur de notification (sans les fautes de frappe) est le même :

    - Lorsque vous enregistrez à partir du client, 
    - Où vous envoyez des notifications à partir du serveur principal,  
    - Lorsque vous avez configuré les informations d’identification de la solution et 
    - Dont informations d’identification d’associations de sécurité que vous avez configuré sur le client et le serveur principal. 
        
    b) Vérifiez que vous utilisez les chaînes sa configuration sur le client et le serveur principal chargé de l’application. En règle générale, vous devez utiliser le **DefaultListenSharedAccessSignature** sur le client et le **DefaultFullSharedAccessSignature** sur le serveur d’application principal (ce qui donne l’autorisation pour pouvoir envoyer une notification à la NH)

2. **Configuration du Service de Notification Push Apple (APNS)**
 
    Vous devez conserver deux concentrateurs différents - un pour la production et un autre pour le test de fin. Cela signifie que le téléchargement du certificat que vous souhaitez utiliser dans un environnement à un autre concentrateur et le certificat que vous souhaitez utiliser dans la production à un autre concentrateur. Ne tentez pas de charger différents types de certificats au même concentrateur car elle peut entraîner des échecs de notification vers le bas de la ligne. Si vous trouvez vous-même dans un emplacement où vous avez téléchargé par inadvertance différents types de certificat au même concentrateur, il est recommandé de supprimer le concentrateur et recommencer à zéro. Si pour une raison quelconque, vous ne pourrez pas supprimer le concentrateur puis à tout le moins, vous devez supprimer tous les enregistrements existants à partir du concentrateur. 

3. **Configuration de la messagerie de nuage Google (GCM)** 

    a) à faire que que vous activez le « Google Cloud messagerie pour Android » sous votre projet de nuage. 
    
    ![][2]
    
    b) à faire que de créer une clé « serveur » lors de l’obtention d’informations d’identification le NH utilisera pour authentifier avec GCM. 
    
    ![][3]
    
    c) marque que vous avez configuré « ID de projet » sur le client qui est une entité entièrement numérique que vous pouvez obtenir à partir du tableau de bord :
    
    ![][1]

##<a name="application-issues"></a>Problèmes d’application

1) **Balises /, balise d’expressions**

Si vous utilisez des balises ou des expressions de balise pour segmenter votre public, il est toujours possible que lorsque vous envoyez la notification, il n’existe aucune cible n’a été trouvée basé sur les expressions de balises/balise que vous spécifiez dans votre appel à envoyer. Il est préférable de passer en revue les enregistrements pour vous assurer qu’il existe des balises qui lorsque vous envoyez une notification, puis vérifiez l’accusé de réception uniquement à partir des clients avec ces enregistrements correspondent. Par exemple Si tous vos enregistrements avec NH ont été réalisés avec par exemple la balise « Politique » et que vous envoyez une notification avec la balise « Sports », il ne sera pas être envoyé à n’importe quel périphérique. Un cas complexe peut impliquer des expressions de balise où vous inscrit uniquement avec « Balise A » ou « Balise B » mais lors de l’envoi des notifications, vous ciblez « Balise A & & balise B ». Dans la section diagnostiquer automatiquement les conseils ci-dessous, vous manières dans laquelle vous pouvez consulter vos enregistrements avec les mots clés. 

2) **Problèmes de modèle**

Si vous utilisez des modèles, puis vous assurer que vous suivez les instructions décrites à [l’aide du modèle]. 

3) **Enregistrements non valides**

En supposant que le concentrateur de Notification a été correctement configuré toutes les expressions des balises/balises ont été utilisées correctement résultant de la recherche de cibles valides à laquelle les notifications doivent être envoyées, NH déclenche plusieurs lots de traitement en parallèle - chaque lot d’envoi de messages à un jeu d’enregistrements. 

> [AZURE.NOTE] Dans la mesure où nous faisons le traitement en parallèle, nous ne garantissent pas l’ordre dans lequel les notifications seront remises. 

Maintenant, Azure Notifications concentrateur est optimisé pour un modèle de livraison du message « au plus une fois ». Cela signifie que nous tentons une déduplication afin qu’aucune notification n’est remise plusieurs fois sur un périphérique. Pour ce faire, nous parcourir les enregistrements et veillez à que ce qu’un message est envoyé par l’identificateur de périphérique avant de l’envoyer le message à la solution. Chaque lot est envoyé à la solution, qui à son tour est acceptation et la validation des enregistrements, il est possible que la solution détecte une erreur avec un ou plusieurs des enregistrements dans un lot, renvoie une erreur à Azure NH et arrête le traitement ainsi supprimer complètement ce lot. Cela est particulièrement vrai avec les APNS qui utilise un protocole de flux TCP. Bien que nous sommes optimisés au plus une fois que la remise, dans ce cas, nous supprimer l’enregistrement défaillant à partir de notre base de données et recommencez l’envoi des notifications pour le reste des périphériques dans ce lot.

Vous pouvez obtenir des informations sur les erreurs lors de la tentative d’échec de la remise par rapport à un enregistrement à l’aide de l’API Azure Notification concentrateurs reste : [par télémétrie de Message : obtenir un télémétrie de Message de Notification](https://msdn.microsoft.com/library/azure/mt608135.aspx) et [Commentaires de la solution](https://msdn.microsoft.com/library/azure/mt705560.aspx). Consultez le code par exemple [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) .

##<a name="pns-issues"></a>Problèmes de la solution

Une fois que le message de notification a été reçu par la solution respectifs, il est de sa responsabilité pour livrer la notification au périphérique. Azure concentrateurs de Notification est en dehors de l’image ici et n’a aucun contrôle lorsqu’ou si la notification doit être remis au périphérique. Dans la mesure où les services de notification de plate-forme sont assez robustes, notifications ont tendance à atteindre les périphériques en quelques secondes à partir de la solution. Si la solution est toutefois limitation Azure Notification concentrateurs applique-t-elle une stratégie d’interruption exponentielle et si la solution reste inaccessible pendant 30 min puis nous avons une stratégie en place à la date d’expiration et les messages de supprimer définitivement. 

Si une solution tente de remettre une notification, mais le périphérique est en mode hors connexion, la notification est stockée par la solution pour une période de temps limitée et remise sur le périphérique lorsqu’il est disponible. Une seule notification récente d’une application particulière est stockée. Si plusieurs notifications sont envoyées lorsque le périphérique est en mode hors connexion, chaque nouvelle notification entraîne la notification préalable sont ignorées. Ce comportement de conserver uniquement la notification plus récente est appelé fusion des notifications de APNS et de réduction dans le GCM (qui utilise une clé de réduction). Si le périphérique reste hors connexion pendant une longue période, toutes les notifications qui ont été stockées pour lui sont ignorées. Source - [Guide de APNS] & [Guide de GCM]

Grâce aux concentrateurs Notification Azure - vous pouvez transmettre une clé de fusion via un en-tête HTTP à l’aide du modèle générique `SendNotification` API (par exemple, pour le Kit de développement .NET – `SendNotificationAsync`) qui accepte également les en-têtes HTTP qui sont passées en tant qu’est la solution respectifs. 

##<a name="self-diagnose-tips"></a>Conseils de diagnostiquer automatiquement

Ici, nous allons examiner les différents moyens pour diagnostiquer et racine provoquent des problèmes de concentrateur de Notification :

###<a name="verify-credentials"></a>Vérifiez les informations d’identification

1. **Portail de développement de solution**

    Vérifiez les au respectif portail de développement de solution (etc. WNS APNS, GCM,) à l’aide de nos [Didacticiels de démarrage lors de l’obtention].

2. **Azure portal de classique**

    Accédez à l’onglet Configurer, consulter et faire correspondre les informations d’identification avec ceux obtenus à partir du portail de développement de solution. 

    ![][4]

###<a name="verify-registrations"></a>Vérifier les inscriptions

1. **Visual Studio**

    Si vous utilisez Visual Studio pour le développement vous pouvez vous connecter à Microsoft Azure et afficher et gérer un ensemble de services Azure, y compris le concentrateur de Notifications à partir de l’Explorateur du serveur « ». Cela est principalement utile pour votre environnement de développement/test. 

    ![][9]

    Vous pouvez afficher et gérer tous les enregistrements dans votre concentrateur qui sont bien classés de la plate-forme native ou d’enregistrement du modèle, des balises, solution identificateur, id de l’enregistrement et la date d’expiration. Vous pouvez également modifier un enregistrement à la volée - ce qui est utile par exemple si vous souhaitez modifier des balises. 

    ![][8]
 
    > [AZURE.NOTE] Fonctionnalités de Visual Studio pour modifier les enregistrements doivent uniquement servir au cours du développement/test avec un nombre limité d’enregistrements. Cas il faut résoudre vos enregistrements en vrac, pensez à l’aide de la fonctionnalité d’enregistrement d’importation/exportation décrite ici - [Exporter/importer les enregistrements] (https://msdn.microsoft.com/library/dn790624.aspx)

2. **Explorateur de Bus des services**

    De nombreux clients utilisent ServiceBus explorer décrit ici - [ServiceBus Explorer] pour l’affichage et la gestion de leur centre de notification. Il s’agit d’un projet open source disponible à partir de code.microsoft.com - [code Explorer de ServiceBus]

###<a name="verify-message-notifications"></a>Vérifier les notifications de messages

1. **Azure Portal classique**

    Vous pouvez accéder à l’onglet « Déboguer » pour envoyer des notifications de test sur vos clients sans avoir besoin d’un back-end de service des et en cours d’exécution. 

    ![][7]

2. **Visual Studio**

    Vous pouvez également envoyer des notifications de test dans le confort de Visual Studio :

    ![][10]

    Vous pouvez en savoir plus sur les fonctionnalités de l’Explorateur Visual Studio Azure de concentrateur de Notification ici - 
    
    - [Vue d’ensemble de l’Explorateur de serveur VS]
    - [VS Server Explorer de blog - 1]
    - [VS Server Explorer de blog - 2]

###<a name="debug-failed-notifications-review-notification-outcome"></a>Déboguer les notifications ayant échoué / analyse des résultats de notification

**Propriété de EnableTestSend**

Lorsque vous envoyez une notification par Notification concentrateurs, initialement il simplement Obtient la file d’attente pour NH effectuer un traitement pour connaître toutes ses cibles et finalement NH envoie ensuite à la solution. Cela signifie que lorsque vous utilisez l’API REST ou kit de développement logiciel client, l’un retour de votre appel d’envoi signifie uniquement que le message a été correctement la file d’attente avec concentrateur de Notification. Il ne donne pas une vision de ce qu’ont vécu NH finalement obtenu envoyer le message à la solution. Si votre notification n’est pas arrivée au niveau du périphérique client, il est possible que lorsque NH a essayé de remettre le message à la solution, une erreur est survenue, par exemple la taille de la charge utile a dépassé le maximum autorisé par la solution ou les informations d’identification configurées dans NH sont etc. non valide. Pour obtenir un aperçu des erreurs de la solution, nous avons introduit une propriété appelée [fonction de EnableTestSend]. Cette propriété est automatiquement activée lorsque vous envoyez des messages de test à partir du portail ou d’un client de Visual Studio et vous permet de voir les informations de débogage détaillées. Vous pouvez utiliser cette fonction via des API en prenant l’exemple du Kit de développement .NET où elle est désormais disponible et seront ajoutés par la suite pour tous les kits de développement logiciel client. Pour utiliser cette option avec l’appel reste, consiste à ajouter un paramètre querystring appelé « test » à la fin de votre appel d’envoi, par exemple 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Exemple (Kit de développement .NET)*
 
Supposons que vous utilisez le Kit de développement .NET pour envoyer une notification de toast natif :

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State`sera simplement l’état `Enqueued` à la fin de l’exécution sans toute une visibilité qu’est-il advenu de votre commande. Vous pouvez désormais utiliser le `EnableTestSend` propriété booléenne lors de l’initialisation du `NotificationHubClient` et obtenir un état détaillé sur les erreurs de la solution a rencontré lors de l’envoi de la notification. L’appel d’envoi ici prendra plus de temps pour la renvoyer, car il retourne uniquement une fois NH a livré la notification à la solution pour déterminer le résultat. 
 
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
    
    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);
    
    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Exemple de sortie*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
Ce message indique soit informations d’identification non valides sont configurées dans le concentrateur de notification ou d’un problème avec les enregistrements sur le concentrateur et est recommandé de supprimer cet enregistrement et laisser le client le recréer avant d’envoyer le message. 
 
> [AZURE.NOTE] Notez que l’utilisation de cette propriété est très limitée, et par conséquent, vous ne devez utiliser ce dans l’environnement de développement/test avec un jeu limité d’enregistrements. Nous envoyer uniquement des notifications de débogage à 10 périphériques. Nous avons également une limite de traitement des envois de débogage 10 par minute. 

###<a name="review-telemetry"></a>Examinez la télémétrie 

1. **Utilisez Azure Portal classique**

    Le portail vous permet d’obtenir une vue d’ensemble rapide de toutes les activités sur votre concentrateur de Notification. 
    
    un) à partir de l’onglet « tableau de bord », vous pouvez afficher une vue agrégée des enregistrements, les notifications ainsi que les erreurs par plate-forme. 
    
    ![][5]
    
    b) vous pouvez également ajouter de nombreux autres mesures spécifiques de plate-forme à partir de l’onglet « Moniteur » pour examiner plus particulièrement les erreurs spécifiques de solution retourné lorsque NH tente d’envoyer la notification à la solution. 
    
    ![][6]
    
    c) vous devez commencez par examiner les **Messages entrants**, **Les opérations d’enregistrement**, **Les Notifications de succès** et atteindre par onglet de plate-forme pour examiner les erreurs spécifiques de solution. 
    
    d) si vous avez le concentrateur de notification configuré avec les paramètres d’authentification, erreur de solution d’authentification s’affiche. Il s’agit d’une bonne indication pour vérifier les informations d’identification de la solution. 

2) **Accès par programme**

Plus de détails ici- 

- [Accès de télémétrie par programmation]
- [Accès de télémétrie par exemple d’API] 

> [AZURE.NOTE] Télémétrie plusieurs des fonctionnalités liées comme **Des enregistrements d’importation/exportation**, **Accès télémétrie via des API** etc. sont uniquement disponibles dans une couche Standard. Si vous essayez d’utiliser ces fonctionnalités dans le libre ou de la couche de base puis vous obtenez message d’exception à cet effet lors de l’utilisation du Kit de développement logiciel et un HTTP 403 (refusé) lorsque vous les utilisez directement à partir de l’API de reste. Assurez-vous que vous avez déplacé à la norme de couche via le portail classique d’Azure.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[Vue d’ensemble des concentrateurs de notification]: notification-hubs-push-notification-overview.md
[Didacticiels mise en route]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Directives de modèle]: https://msdn.microsoft.com/library/dn530748.aspx 
[Conseils de APNS]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[Conseils de GCM]: http://developer.android.com/google/gcm/adv.html
[Exportation/importation des enregistrements]: http://msdn.microsoft.com/library/dn790624.aspx
[Explorateur de ServiceBus]: http://msdn.microsoft.com/library/dn530751.aspx
[Code ServiceBus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Vue d’ensemble de l’Explorateur de serveur VS]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[VS Server Explorer de blog - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[VS Server Explorer de blog - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[Fonction de EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Accès de télémétrie par programmation]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Accès de télémétrie par exemple d’API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 