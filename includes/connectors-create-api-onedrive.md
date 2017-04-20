#### <a name="prerequisites"></a>Conditions préalables
- Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)
- Un compte [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Avant de pouvoir utiliser votre compte de OneDrive dans une logique d’application, autoriser l’application de la logique pour se connecter à votre compte de OneDrive.  Vous pouvez le faire facilement dans votre application logique sur le portail Azure. 

Autoriser votre application logique pour se connecter à votre compte de OneDrive à l’aide des étapes suivantes :

1. Créer une application de logique. Dans le Concepteur d’applications de logique, sélectionnez **Afficher les Microsoft d’API gérées** dans la liste déroulante et puis entrez « onedrive » dans la zone de recherche. Sélectionnez un des déclencheurs ou des actions :  
  ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Si vous n’avez pas créé les connexions à OneDrive, vous êtes invité à vous connecter en utilisant vos informations d’identification de OneDrive :  
  ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Sélectionnez **se connecter**et entrez votre nom d’utilisateur et le mot de passe. Sélectionnez **se connecter**:  
  ![](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Ces informations sont utilisées pour autoriser votre application logique pour se connecter à et accéder aux données de votre compte de OneDrive. 
4. Sélectionnez **Oui** pour autoriser l’application de la logique à utiliser votre compte de OneDrive :  
  ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Notez que la connexion a été créée. Maintenant, exécutez les autres étapes dans votre logique d’application :  
  ![](./media/connectors-create-api-onedrive/onedrive-5.png)
