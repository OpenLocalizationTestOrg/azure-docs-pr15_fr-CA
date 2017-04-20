### <a name="prerequisites"></a>Conditions préalables
- Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)
- Une [Base de données de SQL Azure](../articles/sql-database/sql-database-get-started.md) de ses données de connexion, en incluant le nom du serveur, nom de la base de données et nom d’utilisateur/mot de passe. Ces informations sont incluses dans la chaîne de connexion de base de données SQL :
  
    Server = tcp :*yoursqlservername*. database.windows.net,1433;Initial catalogue =*yourqldbname*; Persist Security Info = False ; ID utilisateur = {utilisateur} ; Mot de passe = {your_password} ; MultipleActiveResultSets = False ; Crypter = True ; TrustServerCertificate = False ; Délai de connexion = 30 ;

    Apprenez-en plus sur les [Bases de données SQL Azure](https://azure.microsoft.com/services/sql-database).

> [AZURE.NOTE] Lorsque vous créez une base de données de SQL Azure, vous pouvez également créer des bases de données exemples inclus avec SQL. 



Avant d’utiliser votre base de données de SQL Azure dans une logique d’application, se connecter à votre base de données SQL. Vous pouvez le faire facilement dans votre application logique sur le portail Azure.  

Se connecter à votre base de données de SQL Azure en utilisant les étapes suivantes :  

1. Créer une application de logique. Dans le Concepteur d’applications de logique, ajouter un déclencheur et ensuite ajouter une action. Sélectionnez **afficher de Microsoft d’API gérées** dans la liste déroulante et entrez « sql » dans la zone de recherche. Sélectionnez une des actions :  

    ![Étape de création de connexion SQL Azure](./media/connectors-create-api-sqlazure/sql-actions.png)

2. Si vous n’avez pas créé les connexions de base de données SQL, vous êtes invité pour les détails de connexion :  

    ![Étape de création de connexion SQL Azure](./media/connectors-create-api-sqlazure/connection-details.png) 

3. Entrez les détails de la base de données SQL. Les propriétés d’un astérisque sont obligatoires.

    | Propriété | Détails |
|---|---|
| Se connecter via la passerelle | Laissez non coché. Il est utilisé lors de la connexion à un SQL Server de locaux. |
| Nom de connexion * | Entrez un nom pour votre connexion. | 
| Nom de SQL Server * | Entrez le nom du serveur ; qui est similaire à *servername.database.windows.net*. Le nom du serveur est affiché dans les propriétés de la base de données SQL dans Azure portal et également affiché dans la chaîne de connexion. | 
| Nom de base de données SQL * | Entrez le nom que vous avez donné à votre base de données SQL. Elle est répertoriée dans les propriétés de la base de données SQL dans la chaîne de connexion : Initial Catalog =*yoursqldbname*. | 
| Nom d’utilisateur * | Entrez le nom d’utilisateur que vous avez créé lors de la création de la base de données SQL. Elle est répertoriée dans les propriétés de la base de données SQL dans le portail Azure. | 
| Mot de passe * | Entrez le mot de passe que vous avez créé lors de la création de la base de données SQL. | 

    Ces informations sont utilisées pour autoriser votre application logique pour se connecter et accéder à vos données SQL. Une fois terminé, les détails de votre connexion ressembler à ce qui suit :  

    ![Étape de création de connexion SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png) 

4. Sélectionnez **créer**. 

5. Notez que la connexion a été créée. Maintenant, exécutez les autres étapes dans votre logique d’application : 

    ![Étape de création de connexion SQL Azure](./media/connectors-create-api-sqlazure/table.png)