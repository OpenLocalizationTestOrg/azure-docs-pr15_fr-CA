Le marché Azure met à disposition un large éventail d’applications web populaire développé par Microsoft, des entreprises tierces et initiatives de logiciels open source. Les applications Web créées à partir de l’Azure Marketplace ne nécessitent pas l’installation de logiciels autres que le navigateur utilisé pour se connecter au [Portail d’aperçu Azure](http://go.microsoft.com/fwlink/?LinkId=529715). 

Dans ce didacticiel, vous allez apprendre :

- Comment créer une nouvelle application web par le biais de l’Azure Marketplace.

- Le déploiement de l’application web via le portail d’aperçu Azure.
 
Vous allez créer un blog WordPress qui utilise un modèle par défaut. L’illustration suivante montre l’application terminée :


![Blog de WordPress][13]

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="create-a-web-app-in-the-portal"></a>Créer une application web sur le portail

1. Connectez-vous au portail Azure aperçu.

2. Permet d’ouvrir le marché Azure soit en cliquant sur l’icône de **l’Outil statistique** :

    ![Icône du marché][marketplace]

    Ou en cliquant sur l’icône de **Nouveau** dans le coin supérieur droit du tableau de bord et en sélectionnant le **marché** à la bottow de la liste.
    
    ![Créer de nouvelles][5]
    
3. Sélectionnez **Web + Mobile**. Rechercher **WordPress** et cliquez sur l’icône **WordPress** .

    ![WordPress à partir de la liste][7]
    
5. Après avoir lu la description de l’application WordPress, sélectionnez **créer**.

6. Cliquez sur **application Web**et fournir les valeurs requises pour la configuration de votre application web.
    
    ![configurer votre application][8]

7. Cliquez sur **base de données**et fournir les valeurs requises pour la configuration de votre base de données MySQL. 

    ![configurer la base de données][database]

8. Fournissez un nom pour un nouveau groupe de ressources.

    ![Définir le groupe de ressources][groupname]

8. Si nécessaire, cliquez sur **l’abonnement**et spécifier l’abonnement à utiliser. 

7. Lorsque vous avez terminé de définir l’application web, cliquez sur **créer**et patientez pendant la création de la nouvelle application web.

   Lorsque l’application a été créée, vous verrez le groupe de ressources contenant la base de données et d’application web.

   ![afficher le groupe][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Lancer et gérer votre application de web WordPress
    
1. Cliquez sur votre nouvelle application web pour afficher plus d’informations sur votre application.

    ![lancer le tableau de bord][10]

2. Sur la page **Essentials** , cliquez sur **Parcourir** ou sur le lien sous l' **Url** pour ouvrir la page d’accueil de l’application web.

    ![URL du site][browse]

3. Si vous n’avez pas installé WordPress, entrez les informations de configuration appropriées requises par WordPress et cliquez sur **Installer un WordPress** pour finaliser la configuration et ouvrez la page de connexion de l’application web.

4. Cliquez sur **connexion** et entrez vos informations d’identification.  

5. Vous aurez une nouvelle application web WordPress ressemblant à l’application web ci-dessous.    

    ![votre site WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png
