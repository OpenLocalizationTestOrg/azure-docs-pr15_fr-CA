
   * Connectez-vous au compte Azure en entrant vos informations d’identification.

     Cette méthode est plus rapide et plus facile, mais si vous utilisez cette méthode vous ne pourrez pas voir les Services mobiles dans la fenêtre de **l’Explorateur de serveurs** ou base de données de SQL Azure.

     Dans l' **Explorateur de serveurs**, cliquez sur le bouton **connexion pour Azure** . Une alternative consiste au droit **d’Azure** , puis cliquez sur **se connecter à Azure** dans le menu contextuel.

   * Installez un certificat de gestion qui permet d’accéder à votre compte.

     Dans l' **Explorateur de serveurs**, cliquez sur le nœud **d’Azure** et puis cliquez sur **Gérer les abonnements** dans le menu contextuel. Dans la boîte de dialogue **Gérer les abonnements Azure** , cliquez sur l’onglet **certificats** , puis cliquez sur **Importer**. Suivez les instructions pour télécharger et ensuite importer un fichier d’abonnement (également appelé fichier *.publishsettings* ) pour votre compte Azure.

     > [AZURE.NOTE] Téléchargez le fichier d’abonnement à un dossier en dehors de vos répertoires de code source (par exemple, dans le dossier Téléchargements) et ensuite de le supprimer une fois l’importation terminée. Un utilisateur malveillant qui parvient à accéder au fichier d’abonnement peut modifier, créer et supprimer vos services Azure.

    Pour plus d’informations, consultez [comment se connecter à Azure à partir de Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796).
