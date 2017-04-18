
1. Visitez le [portail d’Azure]. Cliquez sur **Parcourir tous les** > **Applications Mobile** > le back-end que vous venez de créer. Dans les paramètres de l’application mobile, cliquez sur **démarrage rapide** > **Cordova**. Sous **configurer votre application de client**, sélectionnez **créer une nouvelle application**, puis cliquez sur **Télécharger**. Ceci permet de télécharger un projet de Cordova complet pour une application préconfigurée pour vous connecter à votre serveur principal.

2. Décompressez le fichier ZIP téléchargé dans un répertoire sur votre disque dur, naviguez vers le fichier solution (.sln) et l’ouvrir à l’aide de Visual Studio.

5. Dans Visual Studio, cliquez sur la plateforme de la solution (Android, iOS ou Windows) à partir de la liste déroulante en regard de la flèche de début, puis sélectionnez un émulateur ou un périphérique de déploiement spécifiques en cliquant sur le menu déroulant sur la flèche verte. Notez que vous pouvez utiliser la plate-forme Android de par défaut et d’un émulateur d’ondulation. Didacticiels plus avancées nécessite que vous sélectionnez un émulateur ou un périphérique pris en charge. 

6. Appuyez sur F5 ou cliquez sur la flèche verte pour générer et et exécuter votre application Cordova. Si vous voyez une boîte de dialogue de sécurité dans l’émulateur demandant l’accès au réseau, l’accepter.   

7. Après le démarrage de l’application sur le périphérique ou l’émulateur, tapez texte explicite **Entrez le nouveau texte**, par exemple de _Terminer le didacticiel_ et puis cliquez sur le bouton **Ajouter** .  
Envoie une demande POST pour le back-end Azure que vous avez déployée précédemment. L’insertion de données back-end de la demande est dans la table TodoItem de la base de données SQL et renvoie des informations sur les éléments qui vient d’être stockées dans l’application mobile. L’application mobile affiche ces données dans la liste.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)
    
8. Répétez les trois étapes précédentes pour chaque plate-forme de périphérique que vous prévoyez de prendre en charge.

[Azure Portal]: https://portal.azure.com/
