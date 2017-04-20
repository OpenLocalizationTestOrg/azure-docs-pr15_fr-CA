## <a name="download-install-and-register-the-azure-backup-agent"></a>Télécharger, installer et inscrire l’agent de sauvegarde d’Azure

Après avoir créé le coffre-fort de la sauvegarde d’Azure, un agent doit être installé sur chacun de vos ordinateurs de Windows (Windows Server, client Windows, System Center Data Protection Manager server ou serveur de sauvegarde Azure) qui permet de sauvegarder des données et applications Azure.

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com/)

2. Cliquez sur **Services de récupération**, puis sélectionnez le coffre-fort de sauvegarde que vous souhaitez enregistrer avec un serveur. La page de démarrage rapide pour ce coffre-fort de sauvegarde s’affiche.

    ![Démarrage rapide](./media/backup-install-agent/quickstart.png)

3. Sur la page de démarrage rapide, cliquez sur l’option **de Windows Server System Center Data Protection Manager ou Windows client** sous **Agent de téléchargement**. Cliquez sur **Enregistrer** pour la copier sur l’ordinateur local.

    ![Enregistrer l’agent](./media/backup-install-agent/agent.png)

4. Une fois que l’agent est installé, double-cliquez sur MARSAgentInstaller.exe pour lancer l’installation de l’agent de sauvegarde d’Azure. Choisissez le dossier d’installation et le dossier de travail requise pour l’agent. L’emplacement du cache spécifié doit avoir un espace libre qui est au moins 5 % des données de sauvegarde.

5.  Si vous utilisez un serveur proxy pour se connecter à internet, procédez comme suit : dans l’écran **configuration du serveur Proxy** , entrez les détails du serveur proxy. Si vous utilisez un proxy authentifié, entrez les détails de nom et de mot de passe de l’utilisateur dans cet écran.

6.  L’agent de sauvegarde d’Azure installe.NET Framework 4.5 et Windows PowerShell (s’il est disponible) pour terminer l’installation.

7.  Une fois que l’agent est installé, cliquez sur le bouton **Continuer pour l’enregistrement** pour poursuivre le flux de travail.

    ![Enregistrer](./media/backup-install-agent/register.png)

8. Dans l’écran d’informations d’identification de coffre-fort, recherchez et sélectionnez le fichier d’informations d’identification de coffre-fort qui a été préalablement téléchargé.

    ![Informations d’identification de la chambre forte](./media/backup-install-agent/vc.png)

    Le fichier d’informations d’identification de coffre-fort est uniquement valide pour 48 heures (après l’avoir téléchargée à partir du portail). Si vous rencontrez une erreur dans cet écran (par exemple « fichier d’informations d’identification de coffre-fort fourni a expiré »), connexion au portail Azure et que vous téléchargez de nouveau le fichier d’informations d’identification de coffre-fort.

    Assurez-vous que le fichier d’informations d’identification de coffre-fort est disponible dans un emplacement accessible par l’application d’installation. Si vous rencontrez des erreurs connexes d’accès, copiez le fichier d’informations d’identification de coffre-fort dans un emplacement temporaire sur cet ordinateur et recommencez l’opération.

    Si vous rencontrez une erreur d’informations d’identification de coffre-fort non valide (par ex. « credentials vault non valide fournis »), le fichier est endommagé ou n'est pas avoir les informations d’identification plus récentes associé au service de récupération. Réessayez l’opération après avoir téléchargé un nouveau fichier d’informations d’identification de coffre-fort à partir du portail. Cette erreur se produite généralement si l’utilisateur clique sur l’option **informations d’identification du coffre-fort de télécharger** dans le portail d’Azure, en succession rapide. Dans ce cas, uniquement le deuxième fichier de d’informations d’identification de coffre-fort est valide.

9. Dans l’écran **paramètres de cryptage** , vous pouvez générer un mot de passe ou fournir un mot de passe (minimum de 16 caractères). N’oubliez pas d’enregistrer le mot de passe dans un endroit sûr.

    ![Cryptage](./media/backup-install-agent/encryption.png)

    > [AZURE.WARNING] Si le mot de passe est perdu ou oublié ; Microsoft ne peut aider à récupérer les données de sauvegarde. L’utilisateur possède le mot de passe de cryptage et Microsoft n’a pas de visibilité sur le mot de passe utilisé par l’utilisateur final. Enregistrez le fichier dans un emplacement sécurisé car il est requis au cours d’une opération de récupération.

10. Une fois que vous cliquez sur le bouton **Terminer** , la machine a été enregistrée dans la chambre forte et vous êtes maintenant prêt à commencer la sauvegarde Microsoft Azure.

11. Lorsque vous utilisez Microsoft Azure sauvegarde autonome, vous pouvez modifier les paramètres spécifiés pendant le processus d’inscription en cliquant sur l’option **Modifier les propriétés** dans le composant logiciel enfichable mmc de sauvegarde d’Azure dans.

    ![Modifier les propriétés](./media/backup-install-agent/change.png)

    Vous pouvez également modifier les paramètres spécifiés pendant le processus d’inscription en cliquant sur l’option de **configuration** en sélectionnant **en ligne** sous l’onglet **gestion** lors de l’utilisation de Data Protection Manager.

    ![Configurer la sauvegarde Azure](./media/backup-install-agent/configure.png)
