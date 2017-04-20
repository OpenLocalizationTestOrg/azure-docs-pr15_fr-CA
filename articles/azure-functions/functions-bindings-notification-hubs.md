<properties
    pageTitle="Liaison de concentrateur de Notification de fonctions Azure | Microsoft Azure"
    description="Comprendre comment utiliser la liaison de concentrateur de Notification Azure dans les fonctions d’Azure."
    services="functions"
    documentationCenter="na"
    authors="wesmc7777"
    manager="erikre"
    editor=""
    tags=""
    keywords="fonctions Azure, fonctions, traitement de l’événement, calcul dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="10/27/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-notification-hub-output-binding"></a>Liaison de sortie Azure concentrateur de Notification de fonctions

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et liaisons de concentrateur de Notification Azure de code dans des fonctions d’Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Vos fonctions peuvent envoyer des notifications de type pousser à l’aide d’un concentrateur de Notification Azure configuré avec quelques lignes de code. Toutefois, le concentrateur de Notification Azure doit être configuré pour les Services (solution plate-forme Notifications) à utiliser. Pour plus d’informations sur la configuration d’un concentrateur de Notification Azure et de développement d’une application cliente que vous inscrire pour recevoir des notifications, reportez-vous à la section [mise en route avec les concentrateurs de Notification](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) , puis cliquez sur la plate-forme du client cible en haut.

Les notifications que vous envoyez peuvent être des notifications natives ou des notifications de modèle. Notifications natives ciblent une plate-forme de notification spécifique, tel que configuré dans les `platform` propriétés de la liaison de sortie. Une notification de modèle peut servir pour plusieurs plates-formes.   

## <a name="notification-hub-output-binding-properties"></a>Propriétés de liaison de sortie notification concentrateur

Le fichier function.json fournit les propriétés suivantes :

- `name`: Nom variable utilisée dans le code de la fonction du message de notification concentrateur.
- `type`: doit être défini sur *« notificationHub »*.
- `tagExpression`: Expressions de balise permettent de spécifier que les notifications être remis à un ensemble de périphériques qui se sont enregistrés pour recevoir des notifications qui correspondent à l’expression de l’étiquette.  Pour plus d’informations, consultez [expressions de routage et de la balise](../notification-hubs/notification-hubs-tags-segment-push-message.md).
- `hubName`: Nom de la ressource de concentrateur de notification dans le portail Azure.
- `connection`: Cette chaîne de connexion doit être une chaîne de connexion de **Paramètre d’Application** définie à la valeur de *DefaultFullSharedAccessSignature* pour votre concentrateur de notification.
- `direction`: doit avoir la valeur *« out »*. 
- `platform`: La propriété platform Indique la plate-forme notification vos cibles de notification. Doit être une des valeurs suivantes :
    - `template`: Il s’agit de la plateforme par défaut si la propriété de plate-forme est omise de la liaison de sortie. Notifications de modèle vous permet de cibler n’importe quelle plate-forme configuré sur le concentrateur de Notification Azure. Pour plus d’informations sur l’utilisation des modèles pour envoyer des notifications de plates-formes croisées avec un concentrateur de Notification Azure en général, reportez-vous à la section [modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).
    - `apns`: Service de Notification de transmission Apple. Pour plus d’informations sur la configuration du concentrateur de notification pour APNS et la réception de la notification dans une application cliente, consultez [envoi des notifications de type Pousser aux e/s avec des concentrateurs de Notification Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
    - `adm`: [Échanges de dispositif d’Amazon](https://developer.amazon.com/device-messaging). Pour plus d’informations sur la configuration du concentrateur de notification pour ADM et la réception de la notification dans une application Kindle, reportez-vous à la section [Mise en route avec les concentrateurs de Notification pour les applications Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
    - `gcm`: [Échanges de nuage de Google](https://developers.google.com/cloud-messaging/). Messagerie nuage firebase, qui est la nouvelle version de GCM, est également pris en charge. Pour plus d’informations sur la configuration du concentrateur de notification pour GCM/FCM et la réception de la notification dans une application client Android, voir [envoi des notifications de type Pousser pour Android avec Azure Notification concentrateurs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
    - `wns`: [Notification Services de Push Windows](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) ciblant des plates-formes Windows. Windows Phone 8.1 et versions ultérieur est également pris en charge par WNS. Pour plus d’informations sur la configuration du concentrateur de notification pour WNS et la réception de la notification dans une application de plate-forme de Windows universel (UWP), reportez-vous à la section [mise en route avec Notification concentrateurs pour Universal plate-forme applications Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
    - `mpns`: [Service de Notification d’envoi de Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Cette plate-forme prend en charge Windows Phone 8 et les plates-formes de versions antérieures de Windows Phone. Pour plus d’informations sur la configuration du concentrateur de notification pour MPNS et la réception de la notification dans une application Windows Phone, voir [envoi des notifications de type Pousser avec des concentrateurs de Notification Azure sur Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)
 
Function.json de l’exemple :

    {
      "bindings": [
        {
          "name": "notification",
          "type": "notificationHub",
          "tagExpression": "",
          "hubName": "my-notification-hub",
          "connection": "MyHubConnectionString",
          "platform": "gcm",
          "direction": "out"
        }
      ],
      "disabled": false
    }

## <a name="notification-hub-connection-string-setup"></a>Paramètres de chaîne de connexion de concentrateur notification

Pour utiliser une sortie de concentrateur de Notification de liaison, vous devez configurer la chaîne de connexion pour le concentrateur. Cela est possible dans l’onglet *intégration* en sélectionnant votre concentrateur de notification ou en créer un nouveau. 

Vous pouvez également ajouter une chaîne de connexion pour un concentrateur existant en ajoutant une chaîne de connexion pour le *DefaultFullSharedAccessSignature* à votre concentrateur de notification. Cette chaîne de connexion fournit votre autorisation d’accès de fonction pour envoyer des messages de notification. La valeur de chaîne de connexion *DefaultFullSharedAccessSignature* est accessible à partir du bouton de **clés** dans la lame principale de votre ressource de concentrateur de notification dans le portail Azure. Pour ajouter manuellement une chaîne de connexion pour votre concentrateur, procédez comme suit : 

1. Sur la blade **d’application de la fonction** d’Azure portal, cliquez sur **fonction App Paramètres > Paramètres du Service de l’application**.

2. De la lame de **paramètres** , cliquez sur **Paramètres de l’Application**.

3. Faites défiler jusqu'à la section de **chaînes de connexion** et ajoutez une entrée nommée pour la valeur de *DefaultFullSharedAccessSignature* pour votre concentrateur de notification. Modifier le type **personnalisé**.
4. Faire référence à votre nom de chaîne de connexion dans les liaisons de sortie. Semblable à **MyHubConnectionString** , utilisée dans l’exemple ci-dessus.

## <a name="native-notification-examples"></a>Exemples de notification natif

#### <a name="apns-native-notifications-with-c-queue-triggers"></a>Déclencheurs de la file d’attente des notifications de native APNS avec C#

Cet exemple montre comment utiliser les types définis dans la [Bibliothèque de concentrateurs de Microsoft Azure Notification](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) pour envoyer une notification de APNS native. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native APNS notification is ...
        // { "aps": { "alert": "notification message" }}  

        log.Info($"Sending APNS notification of a new user");   
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{apnsNotificationPayload}");
        await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
    }

#### <a name="gcm-native-notifications-with-c-queue-triggers"></a>Déclencheurs de la file d’attente des notifications natives de GCM avec C#

Cet exemple montre comment utiliser les types définis dans la [Bibliothèque de concentrateurs de Microsoft Azure Notification](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) pour envoyer une notification de GCM native. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native GCM notification is ...
        // { "data": { "message": "notification message" }}  

        log.Info($"Sending GCM notification of a new user");    
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{gcmNotificationPayload}");
        await notification.AddAsync(new GcmNotification(gcmNotificationPayload));       
    }

#### <a name="wns-native-notifications-with-c-queue-triggers"></a>Déclencheurs de la file d’attente des notifications natives de WNS avec C#

Cet exemple montre comment utiliser les types définis dans la [Bibliothèque de concentrateurs de Microsoft Azure Notification](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) pour envoyer une notification de toast WNS native. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The XML format for a native WNS toast notification is ...
        // <?xml version="1.0" encoding="utf-8"?>
        // <toast>
        //   <visual>
        //     <binding template="ToastText01">
        //       <text id="1">notification message</text>
        //     </binding>
        //   </visual>
        // </toast>

        log.Info($"Sending WNS toast notification of a new user");  
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                        "<toast><visual><binding template=\"ToastText01\">" +
                                            "<text id=\"1\">" + 
                                                "A new user wants to be added (" + user.name + ")" + 
                                            "</text>" +
                                        "</binding></visual></toast>";

        log.Info($"{wnsNotificationPayload}");
        await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));       
    }


## <a name="template-notification-examples"></a>Exemples de notification de modèle

#### <a name="template-example-for-nodejs-timer-triggers"></a>Exemple de modèle pour les déclencheurs de minuteur Node.js 

Cet exemple envoie une notification pour un [enregistrement de modèle](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) qui contient `location` et `message`.

    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
       
        if(myTimer.isPastDue)
        {
            context.log('Node.js is running late!');
        }
        context.log('Node.js timer trigger function ran!', timeStamp);  
        context.bindings.notification = {
            location: "Redmond",
            message: "Hello from Node!"
        };
        context.done();
    };

#### <a name="template-example-for-f-timer-triggers"></a>Exemple de modèle pour les déclencheurs de minuteur F#

Cet exemple envoie une notification pour un [enregistrement de modèle](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) qui contient `location` et `message`.

    let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
        notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]


#### <a name="template-example-using-an-out-parameter"></a>Exemple de modèle à l’aide d’un paramètre de sortie 

Cet exemple envoie une notification pour un [enregistrement de modèle](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) qui contient un `message` espace réservé dans le modèle.

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
     
    public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
    }
     
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return templateProperties;
    }

#### <a name="template-example-with-asynchronous-function"></a>Exemple de modèle avec la fonction asynchrone

Si vous utilisez un code asynchrone, les paramètres de sortie sont interdites. Dans ce cas utiliser `IAsyncCollector` pour renvoyer votre notification de modèle. Le code suivant est un exemple asynchrone du code ci-dessus. 

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        log.Info($"Sending Template Notification to Notification Hub");
        await notification.AddAsync(GetTemplateProperties(myQueueItem));    
    }
    
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["user"] = "A new user wants to be added : " + message;
        return templateProperties;
    }

#### <a name="template-example-using-json"></a>Exemple de modèle à l’aide de JSON 

Cet exemple envoie une notification pour un [enregistrement de modèle](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) qui contient un `message` espace réservé dans le modèle à l’aide d’une chaîne JSON valide.

    using System;
     
    public static void Run(string myQueueItem,  out string notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
    }


#### <a name="template-example-using-notification-hubs-library-types"></a>Exemple de modèle à l’aide des types de bibliothèques de concentrateurs de Notification

Cet exemple montre comment utiliser les types définis dans la [Bibliothèque de concentrateurs Notification Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 


    #r "Microsoft.Azure.NotificationHubs"

    using System;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
     
    public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
    {
       log.Info($"C# Queue trigger function processed: {myQueueItem}");
       notification = GetTemplateNotification(myQueueItem);
    }

    private static TemplateNotification GetTemplateNotification(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return new TemplateNotification(templateProperties);
    }

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
