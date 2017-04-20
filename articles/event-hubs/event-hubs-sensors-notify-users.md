<properties 
   pageTitle="Avertir les utilisateurs des données provenance des capteurs ou d’autres systèmes | Microsoft Azure"
   description="Décrit comment utiliser l’événement concentrateurs pour avertir les utilisateurs des données de capteur."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="notify-users-of-data-received-from-sensors-or-other-systems"></a>Avertir les utilisateurs des données provenance des capteurs ou d’autres systèmes

Supposons que vous ayez une application qui surveille les données en temps réel, génère des rapports selon un calendrier. Si vous consultez le site Web sur lequel figurent les graphiques en temps réel ou des rapports, vous pouvez voir quelque chose qui nécessite une action. Que se passe-t-il si vous devez être alertée pour ces situations, au lieu de vous fier à n’oubliez pas de consulter le site Web ? Imaginez que vous avez une lumière de croissance dans une serre, et que vous devez savoir immédiatement si le voyant s’éteint. Une façon de procéder serait avec un capteur de luminosité dans la serre, réorganisation à envoyer un message électronique si le voyant est éteint.

![][1]

Dans un autre scénario, imaginez que vous exécutez une installation d’embarquement pour animaux de compagnie, et vous devez en être averti aux niveaux de fourniture de stock faible. Par exemple, vous pouvez organiser à envoyer un message texte à partir de votre système ERP si votre inventaire de nourriture pour chien est tombée à un niveau critique. 

![][2]

Le problème est l’obtention d’informations critiques lorsque certaines conditions sont remplies, et non lorsque vous contourner pour la récupération d’un rapport statique. Si vous utilisez un [Concentrateur d’événements Azure][] ou [l’Azure IoT concentrateur][] pour recevoir des données à partir des périphériques ou des applications d’entreprise comme [Dynamics AX][], vous disposez de plusieurs options pour savoir comment les traiter. Vous pouvez les afficher sur un site Web, vous pouvez les analyser, vous pouvez les stocker, et vous pouvez les utiliser pour déclencher des commandes pour effectuer une action. Pour ce faire, vous pouvez utiliser les puissants outils tels que les [Sites Web d’Azure][], [SQL Azure][], [HDInsight][], [Cortana Intelligence Suite][], [IoT Suite][], [Logique d’applications][]ou [Concentrateurs de Notification Azure][]. Mais parfois vous souhaitez uniquement envoyer ces données à un utilisateur avec un minimum de surcharge. Pour vous montrer comment procéder avec un peu de code, nous avons fourni un nouvel échantillon, [AppToNotifyUsers][]. Options incluses sont téléphone, SMS et messagerie électronique (SMTP).

## <a name="application-structure"></a>Structure d’application

L’application est écrite en C#, et le fichier Lisezmoi de l’exemple contient toutes les informations que vous devez modifier, générer et publier l’application. Les sections suivantes fournissent une vue d’ensemble de l’application.

Nous allons commencer en partant du principe que vous avez lancés un concentrateur d’événements Azure ou le concentrateur de IoT les événements critiques. N’importe quel concentrateur va faire, tant que vous y avez accès et que vous connaissez la chaîne de connexion.

Si vous ne disposez pas déjà d’un concentrateur concentrateur d’événements ou IoT, vous pouvez facilement configurer un banc d’essai avec un bouclier Arduino et un Pi de framboises, en suivant les instructions fournies dans le projet de [Relier les points](https://github.com/Azure/connectthedots) . Le capteur de lumière sur le bouclier Arduino envoie les niveaux de lumière par le biais de la Pi à un [Concentrateur d’événements Azure][] (**ehdevices**) et un travail [Analytique de flux Azure](https://azure.microsoft.com/services/stream-analytics/) exécute un push d’alertes à un deuxième concentrateur d’événements (**ehalerts**) si la lumière reçue sont inférieures à un certain niveau.

**AppToNotify** au démarrage, il lit un fichier de configuration (App.config) pour obtenir l’URL et les informations d’identification pour le concentrateur d’événements reçoit les alertes. Il génère ensuite un processus pour surveiller en permanence que concentrateur d’événements pour les messages entrant par le biais de – dans la mesure où vous avez peuvent accéder à l’URL pour le concentrateur d’événements ou IoT du concentrateur et les informations d’identification valides, ce code de lecteur de concentrateurs d’événement sera lecture en continu de nouveautés. Pendant le démarrage, l’application lit également les URL et les informations d’identification pour le service de messagerie (téléphone e-mail, SMS) à utiliser et le nom/adresse de l’expéditeur et une liste de destinataires.

Une fois le moniteur d’événement concentrateur détecte un message, il déclenche un processus qui envoie ce message à l’aide de la méthode spécifiée dans le fichier de configuration. Notez qu’il envoie tous les messages qu’il détecte. Si vous définissez le moniteur pour pointer à un concentrateur d’événements qui reçoit les dix messages par seconde, l’expéditeur envoie 10 messages par seconde – dix messages par seconde, dix de SMS par seconde, dix appels par seconde. Pour cette raison, assurez-vous que vous surveillez un concentrateur d’événements qui reçoit uniquement les alertes qui doivent être envoyés, pas un concentrateur d’événements qui reçoit toutes les données brutes à partir de vos applications ou les capteurs.

## <a name="applicability"></a>Applicabilité

Le code dans cet exemple illustre uniquement comment surveiller des événements concentrateurs et comment appeler les services de messagerie externes, dans le cas où vous souhaitez ajouter cette fonctionnalité à votre application. Notez que cette solution est un soi-même, exemple destinés aux développeurs. Il ne traite pas les besoins de l’entreprise telles que la redondance, le basculement, redémarrage lors de la défaillance, etc.. Pour plus de solutions complètes et de production, consultez les rubriques suivantes :

- À l’aide des connecteurs ou des notifications de type Pousser en utilisant le service [Azure logique d’applications](../app-service-logic/app-service-logic-connectors-list.md) .
- À l’aide de [Concentrateurs de Notification d’Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx), comme le décrit le blog de [notifications de type Pousser de diffusion à des millions d’appareils mobiles à l’aide de concentrateurs de Notification Azure](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs). 

## <a name="next-steps"></a>Étapes suivantes

Il est simple de créer un service simple de notification envoie des e-mails ou des messages aux destinataires, ou les appels à relayer des données reçues par un concentrateur d’événements ou un Hub de IoT. Pour déployer la solution pour avertir les utilisateurs en fonction de données reçues par ces concentrateurs, visitez le site [AppToNotifyUsers][].

Pour plus d’informations sur ces concentrateurs, consultez les articles suivants :

- [Événement Azure concentrateurs]
- [Concentrateur de IoT Azure]
- Mise en route avec un [didacticiel de concentrateurs de l’événement].
- Un [exemple d’application qui utilise des concentrateurs d’événement]complète.

Pour déployer la solution pour avertir les utilisateurs en fonction des données reçues par ces concentrateurs, visitez le site :

- [AppToNotifyUsers][]

[Didacticiel de concentrateurs d’événement]: event-hubs-csharp-ephcs-getstarted.md
[Concentrateur de IoT Azure]: https://azure.microsoft.com/services/iot-hub/
[Événement Azure concentrateurs]: https://azure.microsoft.com/services/event-hubs/
[Concentrateur d’événements Azure]: https://azure.microsoft.com/services/event-hubs/
[exemple d’application qui utilise des concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Sites Web Azure]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Intelligence Suite]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT Suite]: https://azure.microsoft.com/solutions/iot-suite/
[Applications de logique]: https://azure.microsoft.com/services/app-service/logic/
[Concentrateurs de Notification Azure]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png