## <a name="using-vault-credentials-to-authenticate-with-the-azure-backup-service"></a>Avec des informations d’identification de coffre-fort pour s’authentifier auprès du service de sauvegarde d’Azure

Le serveur local (client Windows ou serveur de Windows Server ou de Data Protection Manager) doit être authentifié avec un coffre-fort de sauvegarde avant qu’il peut sauvegarder des données sur Azure. L’authentification est réalisée à l’aide de « chambre forte des informations d’identification ». Le concept d’informations d’identification de la chambre forte est similaire au concept d’un fichier « paramètres de publication » qui est utilisé dans Azure PowerShell.

### <a name="what-is-the-vault-credential-file"></a>Quel est le fichier d’informations d’identification de coffre-fort ?

Le fichier d’informations d’identification de coffre-fort est un certificat généré par le portail pour chaque sauvegarde coffre-fort. Le portail transmet ensuite la clé publique pour Access Control Service (ACS). La clé privée du certificat est rendue disponible pour l’utilisateur dans le cadre du flux de travail qui est donné comme une entrée dans le workflow de machine d’inscription. Il authentifie l’ordinateur pour envoyer des données de sauvegarde à un coffre-fort identifié dans le service de sauvegarde d’Azure.

Les informations d’identification de la chambre forte sont utilisée uniquement pendant le processus d’inscription. Il est responsable de l’utilisateur pour vous assurer que le fichier d’informations d’identification de coffre-fort n’est pas compromis. S’il se trouve entre les mains d’un utilisateur non fiable, le fichier d’informations d’identification de coffre-fort utilisable pour enregistrer d’autres machines par rapport à la chambre forte même. Toutefois, comme les données de sauvegarde sont cryptées à l’aide d’un mot de passe qui appartient au client, les données de sauvegarde existantes ne sont pas compromises. Pour atténuer ce problème, les informations d’identification de la chambre forte sont configurées pour expirer dans 48hrs. Vous pouvez télécharger les informations d’identification de la chambre forte d’un coffre-fort sauvegarde autant de fois – mais seulement le dernier fichier de d’informations d’identification de coffre-fort est applicable pendant le processus d’inscription.

### <a name="download-the-vault-credential-file"></a>Téléchargez le fichier d’informations d’identification de coffre-fort

Le fichier d’informations d’identification de chambre forte est transférée via un canal sécurisé à partir du portail Azure. Le service de sauvegarde d’Azure ne connaît la clé privée du certificat et la clé privée n’est pas conservée dans le portail ou le service. Utilisez les étapes suivantes pour télécharger le fichier d’informations d’identification de coffre-fort pour un ordinateur local.

1.  Connectez-vous au [portail de gestion](https://manage.windowsazure.com/)
2.  Dans le volet de navigation gauche, cliquez sur **Services de récupération** et sélectionnez le coffre-fort de sauvegarde que vous avez créée. Cliquez sur l’icône de nuage pour accéder à l’affichage du démarrage rapide de la chambre forte de sauvegarde.

    ![Aperçu rapide](./media/backup-download-credentials/quickview.png)

3.  Dans la page de démarrage rapide, cliquez sur **références pour le téléchargement en chambre forte**. Le portail génère le fichier d’informations d’identification de coffre-fort, qui est rendu disponible pour téléchargement.

    ![Télécharger](./media/backup-download-credentials/downloadvc.png)

4.  Le portail génère une information d’identification de coffre-fort à l’aide d’une combinaison du nom de coffre-fort et de la date actuelle. Cliquez sur **Enregistrer** pour télécharger les informations d’identification de la chambre forte au dossier de téléchargements d’un compte local, ou sélectionnez Enregistrer sous dans le menu Enregistrer spécifier un emplacement pour les informations d’identification de la chambre forte.

### <a name="note"></a>Remarque
- Vérifiez que les informations d’identification de la chambre forte est enregistré dans un emplacement accessible à partir de votre ordinateur. S’il est stocké dans un partage de fichier/SMB, vérifiez les autorisations d’accès.
- Le fichier d’informations d’identification de coffre-fort est utilisé uniquement pendant le processus d’inscription.
- Le fichier d’informations d’identification de coffre-fort expire après 48hrs et peut être téléchargé à partir du portail.
- Consultez le [Forum aux questions](../articles/backup/backup-azure-backup-faq.md) de Azure sauvegarde pour toute question sur le flux de travail.
