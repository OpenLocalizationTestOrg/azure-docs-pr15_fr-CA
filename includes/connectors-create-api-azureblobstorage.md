### <a name="prerequisites"></a>Conditions préalables
- Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)
- Un [compte de stockage des objets Blob Azure](../articles/storage/storage-create-storage-account.md) , y compris le nom de compte de stockage et la touche d’accès rapide. Ces informations sont répertoriées dans les propriétés du compte de stockage dans le portail Azure. Apprenez-en plus sur [Le stockage Azure](../articles/storage/storage-introduction.md).

Avant d’utiliser votre compte de stockage des objets Blob Azure dans une logique d’application, se connecter à votre compte de stockage des objets Blob Azure. Vous pouvez le faire facilement dans votre application logique sur le portail Azure.  

Se connecter à votre compte de stockage des objets Blob Azure à l’aide des étapes suivantes :  

1. Créer une application de logique. Dans le Concepteur d’applications de logique, ajouter un déclencheur et ensuite ajouter une action. Sélectionnez **Afficher les Microsoft d’API gérées** dans la liste déroulante et entrez « blob » dans la zone de recherche. Sélectionnez une des actions :  

    ![Étape de création de connexion du stockage Blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  

2. Si vous n’avez pas créé les connexions de stockage Azure, vous devez indiquer les détails de connexion :   

    ![Étape de création de connexion du stockage Blob Azure](./media/connectors-create-api-azureblobstorage/connection-details.png)  

3. Entrez les détails de compte de stockage. Les propriétés d’un astérisque sont obligatoires.

    | Propriété | Détails |
|---|---|
| Nom de connexion * | Entrez un nom pour votre connexion. |
| Nom du compte de stockage Azure * | Entrez le nom de compte de stockage. Le nom de compte de stockage est affiché dans les propriétés de stockage dans le portail Azure. |
| Touche d’accès rapide stockage Azure compte * | Entrez la clé de compte de stockage. Les touches d’accès rapide sont affichés dans les propriétés de stockage dans le portail Azure. |

    Ces informations sont utilisées pour autoriser votre application logique pour se connecter et accéder à vos données. 

4. Sélectionnez **créer**.

5. Notez que la connexion a été créée. Maintenant, exécutez les autres étapes dans votre logique d’application : 

    ![Étape de création de connexion du stockage Blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  
