<properties
    pageTitle="Routage et les Expressions de la balise"
    description="Cette rubrique explique les expressions de routage et la balise de concentrateurs de notification Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="routing-and-tag-expressions"></a>Expressions de routage et la balise

##<a name="overview"></a>Vue d’ensemble

Expressions de balise vous permettent de cible des ensembles de périphériques, ou plus précisément les enregistrements, lors de l’envoi d’une notification de transmission par l’intermédiaire de concentrateurs de Notification.


## <a name="targeting-specific-registrations"></a>Ciblage des enregistrements spécifiques

Le seul moyen cible une notification spécifique des enregistrements consiste à associer les balises avec eux, ensuite cibler ces balises. Comme expliqué dans la [Gestion de l’inscription](notification-hubs-push-notification-registration-management.md), pour recevoir des notifications de type Pousser une application doit enregistrer un handle de périphérique sur un concentrateur de la notification. Une fois qu’un enregistrement est créé sur un concentrateur de la notification, le back-end de l’application peut envoyer les notifications de transmission.
Le back-end de l’application peut choisir les enregistrements à cible avec une notification spécifique dans l’une des manières suivantes :

1. **Diffusion**: tous les enregistrements dans le concentrateur de notification reçoivent la notification.
2. **Balise**: la notification de réception de tous les enregistrements qui contiennent la balise spécifiée.
3. **Expression de l’étiquette**: la notification de réception de tous les enregistrements dont l’ensemble des balises correspondent à l’expression spécifiée.

## <a name="tags"></a>Balises

Une balise peut être n’importe quelle chaîne, jusqu'à 120 caractères, contenant alphanumériques et les caractères non alphanumériques : '_', ‘@’, '#', '. ',' :', '-'. L’exemple suivant montre une application à partir de laquelle vous pouvez recevoir des notifications de toast à propos des groupes de musique spécifique. Dans ce scénario, un moyen simple pour acheminer des notifications est aux enregistrements d’étiquette avec des balises qui représentent les bandes différentes, comme dans l’illustration suivante.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Dans cette illustration, le message balisé **Beatles** atteint uniquement la tablette enregistré avec la balise **Beatles**.

Pour plus d’informations sur la création d’enregistrements pour les balises, voir [Gestion de l’inscription](notification-hubs-push-notification-registration-management.md).

Vous pouvez envoyer des notifications à des balises à l’aide des méthodes send notifications de la `Microsoft.Azure.NotificationHubs.NotificationHubClient` classe dans les [Concentrateurs de Notification de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Vous pouvez également utiliser Node.js ou les API d’autres Notifications Push.  Voici un exemple utilisant le Kit de développement logiciel.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Les balises ne doivent pas nécessairement être approvisionnées et peuvent faire référence à plusieurs concepts d’application spécifiques. Par exemple, les utilisateurs de cet exemple d’application peuvent commentaires sur bandes et à recevoir un nombre de toasts portés, non seulement pour les commentaires de leurs groupes préférés, mais également pour tous les commentaires de leurs amis, quelle que soit la bande sur laquelle ils sont commentaires. L’illustration suivante montre un exemple de ce scénario :



![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Dans cette illustration, Alice est intéressée par les mises à jour pour les Beatles, et Bob souhaite mises à jour pour les Wailers. Bob est également intéressé par les commentaires de Charlie et Charlie est intéressé par les Wailers. Lorsqu’une notification est envoyée pour commentaire de Charlie sur les Beatles, Alice et Bob reçoit.

Alors que vous pouvez coder plusieurs problèmes dans les balises (par exemple, « band_Beatles » ou « follows_Charlie »), les balises sont des chaînes simples et non des propriétés avec les valeurs. Un enregistrement est mis en correspondance uniquement sur la présence ou l’absence d’une balise spécifique.

Pour un didacticiel complet et détaillé sur l’utilisation des balises pour les envoyer à des groupes d’intérêt, consultez [Breaking News](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).


## <a name="using-tags-to-target-users"></a>À l’aide de balises pour cibler les utilisateurs

Il est un autre moyen d’utiliser les balises pour identifier tous les périphériques d’un utilisateur particulier. Les enregistrements peuvent être balisées avec une balise qui contient un id d’utilisateur, comme dans l’illustration suivante :


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Dans cette illustration, l’uid:Alice message balisé atteint tous les enregistrements des uid:Alice balisé ; Par conséquent, tous les périphériques d’Alice.


##<a name="tag-expressions"></a>Expressions de balise

Il existe des cas dans lesquels une notification a cibler un jeu d’enregistrements identifié pas en une seule balise, mais d’une expression booléenne sur les balises.

Considérez une application de sport qui envoie un rappel à tout le monde à Boston concernant un jeu entre les Red Sox et Cardinals. Si l’application cliente enregistre des balises sur les intérêts dans les équipes et l’emplacement, la notification doit être ciblée à tout le monde à Boston est intéressé dans la loi Sox rouge ou les Cardinals. Cette condition peut être exprimée par l’expression booléenne suivante :

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Expressions de la balise peuvent contenir tous les opérateurs booléens, tels que AND (& &), ou (|) et pas ( !). Ils peuvent également contenir des parenthèses. Expressions de balise sont limitées à 20 balises s’ils contiennent uniquement des ORs ; dans le cas contraire, ils sont limités aux 6 balises.

Voici un exemple d’envoi des notifications avec des expressions de balise à l’aide du SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)"; 

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
