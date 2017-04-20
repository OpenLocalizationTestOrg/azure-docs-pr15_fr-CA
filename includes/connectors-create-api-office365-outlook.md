#### <a name="prerequisites"></a>Conditions préalables
- Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)
- Un compte [Office 365](https://office365.com)  

Avant d’utiliser votre compte Office 365 dans une logique d’application, autoriser l’application de la logique pour se connecter à votre compte Office 365. Vous pouvez le faire facilement dans votre application logique sur le portail Azure.  

Autoriser votre application logique pour se connecter à votre compte Office 365 à l’aide des étapes suivantes :

1. Créer une application de logique. Dans le Concepteur d’applications de logique, sélectionnez **Afficher les Microsoft d’API gérées** dans la liste déroulante et puis entrez « office 365 » dans la zone de recherche. Sélectionnez un des déclencheurs ou des actions :  
    ![Étape de création de connexion Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  

2. Si vous n’avez pas créé les connexions vers Office 365, vous êtes invité à vous connecter en utilisant vos informations d’identification d’Office 365 :  
    ![Étape de création de connexion Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  

3. Sélectionnez **se connecter**et entrez votre nom d’utilisateur et le mot de passe. Sélectionnez **se connecter**:  
    ![Étape de création de connexion Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)

    Ces informations sont utilisées pour autoriser votre application logique pour se connecter à et accéder à votre compte Office 365. 

4. Notez que la connexion a été créée. Maintenant, exécutez les autres étapes dans votre logique d’application :   
    ![Étape de création de connexion Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  
  