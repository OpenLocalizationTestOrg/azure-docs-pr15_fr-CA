
1. Sur votre Mac, visitez le [Portail d’Azure]. Cliquez sur **Parcourir tous les** > **Applications Mobile** > le back-end que vous venez de créer. Dans les paramètres de l’application mobile, cliquez sur **démarrage rapide** > **iOS (Objective-C)**. Si vous préférez Swift, cliquez sur **démarrage rapide** > **e/s (Swift)** à la place. **Téléchargez et exécutez votre projet iOS**, cliquez sur **Télécharger**. Ceci permet de télécharger un projet Xcode complet pour une application préconfigurée pour vous connecter à votre serveur principal. Ouvrez le projet à l’aide de Xcode.

2. Appuyez sur le bouton **exécuter** pour générer le projet et démarrer l’application dans le simulateur d’e/s.

3. Dans l’application, tapez un texte explicite comme _terminé ce didacticiel_ et puis cliquez sur le signe plus (**+**) icône. Envoie une demande POST pour le back-end Azure que vous avez déployée précédemment. L’insertion de données back-end de la demande est dans la table TodoItem SQL et renvoie des informations sur les éléments qui vient d’être stockées dans l’application mobile. L’application mobile affiche ces données dans la liste. 

    ![](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
