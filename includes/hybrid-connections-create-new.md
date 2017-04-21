
1. Sur l’ordinateur local, ouvrez une session sur le [Portail de gestion Azure](http://manager.windowsazure.com) (il s’agit de l’ancien portail).

2. En bas du volet de navigation, sélectionnez **+ Nouveau** > **Application Services** > **BizTalk Service** > **Créer des personnalisées**.

3. Fournissez un **Nom de Service BizTalk** et sélectionnez une **Édition**. 

    Ce didacticiel utilise **mobile1**. Vous devez fournir un nom unique pour votre nouveau BizTalk Service.

4. Une fois que le BizTalk Service a été créé, cliquez sur l’onglet **Connexions de l’hybride** , puis cliquez sur **Ajouter**.

    ![Ajouter une connexion de hybride](./media/hybrid-connections-create-new/3.png)

    Cela crée une nouvelle connexion hybride.

5. Fournir un **Nom d’hôte** et un **nom** pour votre connexion hybrides et **Port** la valeur `1433`. 
  
    ![Configurer la connexion de hybride](./media/hybrid-connections-create-new/4.png)

    Le nom d’hôte est le nom du serveur local. Cela permet de configurer la connexion hybride pour accéder à SQL Server en cours d’exécution sur le port 1433. Si vous utilisez une instance nommée de SQL Server, utilisez plutôt le port statique que vous avez défini précédemment.

6. Une fois la nouvelle connexion est créée, le statut de la de la nouvelle connexion présente **sur site d’installation incomplète**.

7. Revenir à votre service mobile, cliquez sur **configurer**, recherchez **les connexions hybride** et cliquez sur **Ajouter une connexion hybride**, sélectionnez la connexion hybride que vous venez de créer puis cliquez sur **OK**.

    Cela permet à votre service mobile à utiliser votre nouvelle connexion hybride.

Ensuite, vous devez installer le Gestionnaire de connexion hybride sur l’ordinateur local.