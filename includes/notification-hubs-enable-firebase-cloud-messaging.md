

1. Connectez-vous à la [console de Firebase](https://firebase.google.com/console/). Créer un nouveau projet Firebase si vous n’en avez pas déjà.
2. Après avoir créé votre projet, cliquez sur **Ajoutez Firebase à votre application d’Android** et suivez les instructions fournies.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Dans la Console de Firebase, cliquez sur le cog pour votre projet, puis cliquez sur **Paramètres du projet**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. Cliquez sur l’onglet **Messagerie de nuage** dans vos paramètres de projet et copiez la valeur de la **clé de serveur** et **ID de l’expéditeur**.  Ces valeurs serviront ultérieurement pour configurer la stratégie d’accès du concentrateur notification et votre gestionnaire de notification dans l’application.
  