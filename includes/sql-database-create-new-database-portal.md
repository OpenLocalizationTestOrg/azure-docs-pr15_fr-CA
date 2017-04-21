
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## <a name="create-a-new-azure-sql-database"></a>Créer une nouvelle base de données SQL d’Azure

Utilisez les étapes suivantes dans le portail Azure pour créer une nouvelle base de données Azure SQL sur un serveur logique de base de données de SQL Azure nouvel ou existant.

1. Si vous n’êtes pas actuellement connecté, connectez-vous au [portail Azure](http://portal.azure.com).
2. Cliquez sur **Nouveau**, tapez **SQL de base de données**, puis cliquez sur **Base de données SQL (base de données)**.

     ![Nouvelle base de données](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)

3. Cliquez sur **Base de données SQL (base de données)**.

     ![Nouvelle base de données](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)

4. Cliquez sur **créer** pour créer une nouvelle base de données dans le service de base de données SQL.

     ![Nouvelle base de données](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)

5. Fournir les valeurs pour les propriétés de serveur suivantes :

 - Nom de la base de données
 - Abonnement : Ceci s’applique uniquement si vous disposez de plusieurs abonnements.
 - Groupe de ressources : Si vous êtes novice, utilisez le groupe de ressources du serveur logique.
 - Sélectionner la source : vous pouvez choisir une base de données vide, des exemples de données ou une sauvegarde de base de données Azure. Pour migrer une base de données de SQL Server sur site ou charger des données à l’aide de l’outil de ligne de commande BCP, consultez les liens à la fin de cet article.
 - Serveur : Un nouveau ou existant serveur logique.
 - Ouverture de session admin Server
 - Mot de passe
 - Niveau de tarification : Si vous êtes novice, utilisez la valeur par défaut S0.
 - Classement : Cela s’applique uniquement si une base de données vide a été choisi.

        ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)

6.  Cliquez sur **créer**. Dans la zone de notification, vous pouvez voir que le déploiement a commencé.

     ![Nouvelle base de données](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)

7. Attendez que le déploiement se termine avant de passer à l’étape suivante.

     ![Nouvelle base de données](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)
