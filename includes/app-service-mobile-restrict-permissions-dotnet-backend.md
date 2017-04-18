
Par défaut, API dans un back-end de l’application Mobile peut être appelé de manière anonyme. Ensuite, vous devez restreindre l’accès aux seuls les clients authentifiés.  

+ **Back-end Node.js (via le portail)** :  
    
    Dans **paramètres**de votre application Mobile, cliquez sur **Tables facile** et sélectionnez votre table. Cliquez sur **Modifier les autorisations**, sélectionnez **uniquement les accès authentifié** pour toutes les autorisations, puis cliquez sur **Enregistrer**. 

+ **Back-end de .NET (C#)**:  

    Dans le projet serveur, accédez à des **contrôleurs de** > **TodoItemController.cs**. Ajouter le `[Authorize]` d’attribut à la classe **TodoItemController** , comme suit. Pour restreindre l’accès uniquement aux méthodes spécifiques, vous pouvez également appliquer cet attribut uniquement à ces méthodes au lieu de la classe. Publiez de nouveau le projet serveur.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Back-end Node.js (via du code Node.js)** :  
    
    Pour exiger une authentification pour accéder à la table, ajoutez la ligne suivante au script de serveur Node.js :


        table.access = 'authenticated';

    Pour plus d’informations, reportez-vous à [Comment : exiger une authentification pour l’accès aux tables](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Pour savoir comment télécharger le projet de code de démarrage rapide de votre site, consultez [Comment : télécharger le projet de code quickstart Node.js back-end à l’aide de Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).

