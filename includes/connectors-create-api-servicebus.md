### <a name="prerequisites"></a>Conditions préalables

Vous devez disposer d’un compte de [Service de Bus](https://azure.microsoft.com/services/service-bus/) .  

Avant de pouvoir utiliser votre compte de Bus des services Azure dans une logique d’application, vous devez autoriser l’application de la logique pour se connecter à votre compte de service de bus. Heureusement, cela peut facilement à partir de dans votre application logique sur le portail Azure.  

Voici les étapes pour autoriser votre application logique pour se connecter à votre compte de Service Bus :  

1. Pour créer une connexion au Bus de Service, dans le Concepteur d’application logique, sélectionnez **Afficher les Microsoft d’API gérées** dans la liste déroulante. Entrez ensuite le **bus de service** dans la zone de recherche. Sélectionnez l’action à utiliser ou le déclencheur.  
    ![Image de connexion de service Bus 1](./media/connectors-create-api-servicebus/servicebus-1.png)  

2. Si vous n’avez pas créé les connexions de Bus de Service avant, vous devrez fournir vos informations d’identification de Service Bus. Ces informations sont utilisées pour autoriser votre application logique pour se connecter à et d’accéder aux données de votre compte Service Bus. Le connecteur de Bus de Service a besoin de la chaîne de connexion pour l’espace de noms du Bus de Service. Il requiert également **Gérer** les autorisations. Un bon moyen de savoir si votre chaîne de connexion est l’espace de noms ou une entité spécifique est si elle contient la `EntityPath` paramètre. Dans ce cas, il n’est pas la chaîne de connexion de droite pour une application de logique.  
    ![Chaîne de connexion de Bus des services](./media/connectors-create-api-servicebus/connectionstring.png)

1. Une fois que vous avez reçu la chaîne de connexion pour l’espace de noms, vous pouvez l’utiliser pour la connexion de l’API dans la logique d’applications.  
    ![Image de connexion de service Bus 2](./media/connectors-create-api-servicebus/servicebus-2.png)  

3. Notez la connexion a été créée, et vous pouvez maintenant poursuivre les autres étapes dans votre logique d’application.  
    ![Image de Bus des services de connexion 3](./media/connectors-create-api-servicebus/servicebus-3.png)   
