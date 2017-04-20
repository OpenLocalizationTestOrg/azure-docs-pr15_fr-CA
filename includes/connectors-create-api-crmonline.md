#### <a name="prerequisites"></a>Conditions préalables
- Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)
- Un compte de [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) 

Avant d’utiliser votre compte Dynamics dans une logique d’application, autoriser l’application de la logique pour se connecter à votre compte de CRM en ligne. Vous pouvez le faire facilement dans votre application logique sur le portail Azure. 

Autoriser votre application logique pour se connecter à votre compte de CRM en ligne à l’aide des étapes suivantes :

1. Créer une application de logique. Dans le Concepteur d’applications de logique, sélectionnez **afficher de Microsoft d’API gérées** dans la liste déroulante et entrez « dynamics » dans la zone de recherche. Sélectionnez un des déclencheurs ou des actions :  
  ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Si vous n’avez pas créé les connexions de Dynamics, vous êtes invité à vous connecter en utilisant vos informations d’identification de Dynamics :  
  ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Sélectionnez **se connecter**et entrez votre nom d’utilisateur et le mot de passe. Sélectionnez **se connecter**. 

    Ces informations sont utilisées pour autoriser votre application logique pour se connecter à et accéder aux données de votre compte de Dynamics. 
4. Notez que la connexion a été créée. Maintenant, exécutez les autres étapes dans votre logique d’application :  
  ![](./media/connectors-create-api-crmonline/dynamics-properties.png)
