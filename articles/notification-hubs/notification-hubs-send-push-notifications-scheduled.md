<properties
    pageTitle="Comment faire pour envoyer des notifications planifiées | Microsoft Azure"
    description="Cette rubrique décrit l’utilisation de la planification des Notifications avec Azure Notification concentrateurs."
    services="notification-hubs"
    documentationCenter=".net"
    keywords="notifications de type Pousser, notification de transmission, planification des notifications de type Pousser"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="how-to-send-scheduled-notifications"></a>Comment : Envoyer des notifications planifiées


##<a name="overview"></a>Vue d’ensemble

Si vous avez un scénario dans lequel vous souhaitez envoyer une notification à un moment donné, à l’avenir, mais n’avez pas un moyen simple de mettre en éveil votre code principal d’envoyer la notification. Couche standard concentrateurs de Notification prend en charge une fonctionnalité qui vous permet de planifier les notifications des 7 jours à l’avenir.

Lors de l’envoi d’une notification, utilisez simplement la classe [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) dans le Kit de développement de concentrateurs de Notification comme indiqué dans l’exemple suivant :

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

En outre, vous pouvez annuler une notification précédemment planifiée à l’aide de ses notificationId :

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Il n’y a aucune limite sur le nombre de notifications planifiées, que vous pouvez envoyer.