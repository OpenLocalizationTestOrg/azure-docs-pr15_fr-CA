1. Ouvrez une session sur le [Portail classique d’Azure](https://manage.windowsazure.com), puis cliquez sur **+ Nouveau** en bas de l’écran.

2. Cliquez sur **Services d’application**, puis **Engagement Mobile**, puis sur **créer**.

    ![](./media/mobile-engagement-create-app-in-portal/create-mobile-engagement-app.png)

3. Dans le menu contextuel qui s’affiche, entrez les informations suivantes :

    ![](./media/mobile-engagement-create-app-in-portal/create-azme-popup.png)

    - **Nom de l’application**: nom de votre application. 
    - **Platform**: plate-forme cible pour l’application. Vous devez créer une application Mobile Engagement par la plateforme cible pour votre application mobile. 
    - **Nom de ressource de l’application**: nom par lequel cette application sera accessible via les API et les URL. 
    - **Emplacement**: centre de données/de la région où cette application et la collection d’application seront hébergées.
    - **Collection**: sélectionnez une Collection créée précédemment ou sélectionnez « Nouvelle Collection ».
    - **Nom de la collection**: représente le groupe d’applications. Cela garantit également que toutes vos applications sont dans un groupe qui permettra de calculs agrégés des mesures. Vous devez utiliser votre nom de la société ou du département ici le cas échéant.

4. Sélectionnez l’application que vous venez de créer dans l’onglet **Applications** .

5. Cliquez sur **Informations sur la connexion** pour afficher les paramètres de connexion dans votre intégration du Kit de développement logiciel dans votre application mobile.

6. Copie de la **Chaîne de connexion** , c’est ce que vous devez identifier cette application dans votre code d’Application et de se connecter avec Engagement Mobile à partir de votre application.

    ![](./media/mobile-engagement-create-app-in-portal/app-connection-info-page.png)

