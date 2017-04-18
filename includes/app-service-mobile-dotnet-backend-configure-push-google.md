Utilisez la procédure qui correspond à votre type de projet principal&mdash; [back-end de .NET](#dotnet) ou de [back-end de Node.js](#nodejs).

### <a name="dotnet"></a>Projet de back-end de .NET

1. Dans Visual Studio, cliquez sur le projet serveur et cliquez sur **Gérer les Packages NuGet**, recherchez `Microsoft.Azure.NotificationHubs`, puis cliquez sur **installer**. Cette procédure installe la bibliothèque client de concentrateurs de Notification.

2. Dans le dossier contrôleurs, ouvrez TodoItemController.cs et ajoutez le code suivant `using` instructions :

        using Microsoft.Azure.Mobile.Server.Config;
        using Microsoft.Azure.NotificationHubs;

3. Remplacer le `PostTodoItem` méthode par le code suivant :  

      
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            // Get the settings for the server project.
            HttpConfiguration config = this.Configuration;

            MobileAppSettingsDictionary settings = 
                this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

            // Get the Notification Hubs credentials for the Mobile App.
            string notificationHubName = settings.NotificationHubName;
            string notificationHubConnection = settings
                .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // Create a new Notification Hub client.
            NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

            // Android payload
            var androidNotificationPayload = "{ \"data\" : {\"message\":\"" + item.Text + "\"}}";

            try
            {
                // Send the push notification and log the results.
                var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

                // Write the success result to the logs.
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                // Write the failure result to the logs.
                config.Services.GetTraceWriter()
                    .Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

4. Publiez de nouveau le projet serveur.

### <a name="nodejs"></a>Projet de back-end Node.js

1. Si vous n’avez pas déjà fait, [Téléchargez le projet de démarrage rapide](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou bien utilisez l' [éditeur en ligne dans le portail Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
 
1. Remplacez le code existant dans le fichier todoitem.js par le texte suivant :

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
        
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the GCM payload.
        var payload = {
            "data": {
                "message": context.item.text
            }
        };   
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a GCM native notification.
                    context.push.gcm.send(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });
        
        module.exports = table;  

    Envoie une notification GCM contenant le item.text lors de l’insertion d’un nouvel élément todo. 

2. Lorsque vous modifiez le fichier sur votre ordinateur local, publiez de nouveau le projet serveur. 
