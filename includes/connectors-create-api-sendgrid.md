### <a name="prerequisites"></a>Conditions préalables
- Un compte [SendGrid](https://www.SendGrid.com/) 

Avant de pouvoir utiliser votre compte de SendGrid dans une logique d’application, vous devez autoriser l’application logique pour se connecter à votre compte de SendGrid. Heureusement, cela peut facilement à partir de dans votre application logique sur le portail d’Azure. 

Voici les étapes pour autoriser votre application logique pour se connecter à votre compte de SendGrid :

1. Pour créer une connexion à SendGrid, dans le Concepteur d’application logique, sélectionnez **Afficher les Microsoft d’API gérées** dans la liste déroulante, puis entrez *SendGrid* dans la zone de recherche. Sélectionnez l’action que vous allez utiliser ou un déclencheur :  
  ![SendGrid étape 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Si vous n’avez pas créé les connexions de SendGrid avant, vous devrez obtenir pour fournir vos informations d’identification de SendGrid. Ces informations d’identification seront utilisées pour autoriser votre application logique pour se connecter à et accéder aux données de votre compte SendGrid :  
  ![SendGrid étape 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Notez la connexion a été créée et vous pouvez maintenant poursuivre les autres étapes dans votre logique d’application :  
  ![SendGrid étape 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   
