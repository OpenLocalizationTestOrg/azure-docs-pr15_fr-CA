<properties
    pageTitle="Envoi de notifications de type Pousser avec Azure Notification concentrateurs et Node.js"
    description="Apprenez à utiliser des concentrateurs de Notification pour envoyer des notifications de transmission à partir d’une application Node.js."
    keywords="notification de transmission, émission par émission de notifications,node.js, envoi d’e/s"
    services="notification-hubs"
    documentationCenter="nodejs"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Envoi de notifications de type Pousser avec Azure Notification concentrateurs et Node.js
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##<a name="overview"></a>Vue d’ensemble

> [AZURE.IMPORTANT] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Ce guide vous explique comment faire pour envoyer des notifications de type pousser à l’aide de concentrateurs de Notification Azure directement à partir d’une application Node.js. 

Les scénarios présentés incluent l’envoi de notifications de type Pousser aux applications sur les plates-formes suivantes :

* Android
* e/s
* Windows Phone
* Plate-forme Windows universel 

Pour plus d’informations sur les concentrateurs de notification, reportez-vous à la section [Étapes suivantes](#next) .

##<a name="what-are-notification-hubs"></a>Que sont les concentrateurs de Notification ?

Les concentrateurs de Notification Azure offrent une infrastructure facile à utiliser, multi-plateforme, évolutive pour l’envoi de notifications de type Pousser aux périphériques mobiles. Pour plus d’informations sur l’infrastructure de service, voir la page de [Concentrateurs de Notification Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

##<a name="create-a-nodejs-application"></a>Créer une Application Node.js

La première étape de ce didacticiel crée une nouvelle application Node.js vide. Pour obtenir des instructions sur la création d’une application Node.js, consultez [créer et déployer une application Node.js sur le Site Web Azure][nodejswebsite], [Service de Cloud Node.js] [ Node.js Cloud Service] à l’aide de Windows PowerShell, ou un [Site Web avec WebMatrix].

##<a name="configure-your-application-to-use-notification-hubs"></a>Configurez votre Application pour utiliser des concentrateurs de Notification

Pour utiliser des concentrateurs de Notification d’Azure, vous devez télécharger et utiliser le Node.js [package azure](https://www.npmjs.com/package/azure), qui inclut un ensemble intégré de bibliothèques d’assistance qui communiquent avec les services de reste de notification de transmission.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilisez le nœud Gestionnaire de package (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Linux) et naviguez jusqu’au dossier où vous avez créé votre application vide.

2.  Dans la fenêtre de commande, tapez **npm installer azure-sb** .

3.  Vous pouvez exécuter manuellement la commande **ls** ou **dir** pour vérifier si un **nœud\_modules** dossier a été créé. À l’intérieur de ce dossier, recherchez le package **d’azure** , qui contient les bibliothèques que vous devez accéder au concentrateur de Notification.

>[AZURE.NOTE] Vous pouvez en savoir plus sur l’installation de NPM sur [NPM blog](http://blog.npmjs.org/post/85484771375/how-to-install-npm)officiel. 

### <a name="import-the-module"></a>Le module d’importation

À l’aide d’un éditeur de texte, ajoutez le code suivant en haut du fichier **server.js** de l’application :

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Programme d’installation une connexion concentrateur de Notification Azure

L’objet **NotificationHubService** vous permet de travailler avec des concentrateurs de notification. Le code suivant crée un objet **NotificationHubService** pour le concentrateur de notification nommé **hubname**. L’ajouter dans la partie supérieure du fichier **server.js** , après l’instruction pour importer le module azure :

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

La valeur de **connectionstring** de connexion peut être obtenue à partir du [Portail Azure] , effectuez les opérations suivantes :

1. Dans le volet de navigation de gauche, cliquez sur **Parcourir**.

2. Sélectionnez les **Concentrateurs de la Notification**et recherchez le concentrateur que vous souhaitez utiliser pour l’exemple. Si vous avez besoin d’aide pour créer un nouveau concentrateur de Notification, vous pouvez consulter le [didacticiel de Windows Store mise en route](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .

3. Sélectionnez **paramètres**.

4. Cliquez sur **stratégies d’accès**. Vous verrez les deux chaînes de connexion d’accès partagé et complet.

![Azure Portal - concentrateurs de Notification](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] Vous pouvez également récupérer la chaîne de connexion à l’aide de l’applet de commande **Get-AzureSbNamespace** fournie par [PowerShell d’Azure](../powershell-install-configure.md) ou de la commande **show d’espace de noms sb azure** avec l' [Interface de ligne de commande de Azure (Azure CLI)](../xplat-cli-install.md).

##<a name="general-architecture"></a>Architecture générale

L’objet **NotificationHubService** expose les instances d’objet suivants pour l’envoi de notifications de type Pousser vers des applications et des périphériques spécifiques :

* **Android** - utilisez l’objet **GcmService** , qui est disponible à l’adresse **notificationHubService.gcm**
* **iOS** - utilisez l’objet **ApnsService** , qui est accessible au **notificationHubService.apns**
* **Windows Phone** - utilisez l’objet **MpnsService** , qui est disponible à l’adresse **notificationHubService.mpns**
* **Plate-forme de Windows universel** - utilisez l’objet **WnsService** , qui est disponible à l’adresse **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Comment : envoyer des notifications de type Pousser aux applications Android

L’objet **GcmService** fournit une méthode qui peut être utilisée pour envoyer des notifications de type Pousser aux applications Android **Envoyer** . La méthode **send** accepte les paramètres suivants :

* **Balises** - identifiant de la balise. Si aucune balise n’est fourni, la notification sera adressée aux clients d’al.
* **Charge utile** - JSON ou la charge utile de chaîne brute du message.
* **Rappel** - la fonction de rappel.

Pour plus d’informations sur le format de charge utile, reportez-vous à la section de la **charge utile** du document [Serveur de GCM mise en œuvre](http://developer.android.com/google/gcm/server.html#payload) .

Le code suivant utilise l’instance de **GcmService** exposé par le **NotificationHubService** pour envoyer une notification de transmission à tous les clients inscrits.

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Comment : envoyer des notifications de type Pousser aux applications d’e/s

Même avec les applications Android décrite ci-dessus, l’objet **ApnsService** fournit une méthode qui peut être utilisée pour envoyer des notifications de type Pousser aux applications d’iOS **Envoyer** . La méthode **send** accepte les paramètres suivants :

* **Balises** - identifiant de la balise. Si aucune balise n’est fourni, la notification est adressée à tous les clients.
* **Charge utile** - JSON du message ou charge utile de la chaîne.
* **Rappel** - la fonction de rappel.

Pour plus d’informations au format de charge utile, reportez-vous à la section de la **Charge utile de Notification** du document [Local et Guide de programmation de Notification Push](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

Le code suivant utilise l’instance de **ApnsService** exposé par le **NotificationHubService** pour envoyer un message d’alerte à tous les clients :

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Comment : envoyer des notifications de type Pousser pour les applications Windows Phone

L’objet **MpnsService** fournit une méthode **d’envoi** qui peut être utilisée pour envoyer des notifications de type Pousser pour les applications Windows Phone. La méthode **send** accepte les paramètres suivants :

* **Balises** - identifiant de la balise. Si aucune balise n’est fourni, la notification est adressée à tous les clients.
* **Charge utile** - charge XML du message.
* **TargetName**  -  `toast` pour les notifications de toast. `token`pour les notifications de la mosaïque.
* **Classe de notification** - la priorité de la notification. Consultez la section **Éléments d’en-tête HTTP** du document [notifications à partir d’un serveur de transmission](http://msdn.microsoft.com/library/hh221551.aspx) de valeurs valides.
* **Options** - en-têtes de demande facultatives.
* **Rappel** - la fonction de rappel.

Pour obtenir une liste des options valides de **TargetName**, d’une **classe de notification** et d’en-tête, consultez la page [notifications à partir d’un serveur de transmission](http://msdn.microsoft.com/library/hh221551.aspx) .

L’exemple de code suivant utilise l’instance de **MpnsService** exposé par le **NotificationHubService** pour envoyer une notification de transmission toast :

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Comment : envoyer des notifications de type Pousser aux applications de la plate-forme de Windows universel (UWP)

L’objet **WnsService** fournit une méthode qui peut être utilisée pour envoyer des notifications de type Pousser aux applications de la plate-forme de Windows universel **Envoyer** .  La méthode **send** accepte les paramètres suivants :

* **Balises** - identifiant de la balise. Si aucune balise n’est fourni, la notification est adressée à tous les clients inscrits.
* **Charge utile** - la charge utile du message XML.
* **Type** - le type de notification.
* **Options** - en-têtes de demande facultatives.
* **Rappel** - la fonction de rappel.

Pour obtenir une liste de types valides et les en-têtes de demande, consultez [pousser les en-têtes de demande et de réponse de service de notification](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Le code suivant utilise l’instance de **WnsService** exposé par le **NotificationHubService** pour envoyer une notification de transmission toast à une application UWP :

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <a name="next-steps"></a>Étapes suivantes

Les extraits d’exemple ci-dessus permettent de créer facilement des infrastructures de service pour fournir des notifications de type pousser à une grande variété de périphériques. Maintenant que vous avez appris les notions de base de l’utilisation de concentrateurs de Notification avec node.js, suivre ces liens pour en savoir plus sur comment vous pouvez étendre ces fonctions supplémentaires.

-   Consultez la référence MSDN pour [moyeux de Notification Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
-   Visitez le référentiel [Azure SDK pour nœud] sur GitHub pour plus d’exemples et détails d’implémentation.

  [Azure SDK de nœud]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Site Web avec WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [Azure Portal]: https://portal.azure.com
