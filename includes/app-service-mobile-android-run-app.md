
1. Visitez le [portail d’Azure]. Cliquez sur **Parcourir tous les** > **Applications Mobile** > le back-end que vous venez de créer. Dans les paramètres de l’application mobile, cliquez sur **démarrage rapide** > **Android)**. **Configurer votre application cliente**, cliquez sur **Télécharger**. Ceci permet de télécharger un projet Android complet pour une application préconfigurée pour vous connecter à votre serveur principal. 

2. Ouvrez le projet à l’aide de **Studio Android**, à l’aide de **importation de projet (Eclipse TSA, Gradle, etc.)**. Assurez-vous que vous effectuez cette sélection importation pour éviter toute erreur JDK.

3. Appuyez sur le bouton **exécuter 'app'** pour générer le projet et démarrer l’application dans le simulateur Android.

4. Dans l’application, tapez un texte explicite comme _terminé ce didacticiel_ et puis cliquez sur le bouton 'Ajouter'. Envoie une demande POST pour le back-end Azure que vous avez déployée précédemment. L’insertion de données back-end de la demande est dans la table TodoItem SQL et renvoie des informations sur les éléments qui vient d’être stockées dans l’application mobile. L’application mobile affiche ces données dans la liste. 

    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Azure Portal]: https://portal.azure.com/
