### <a name="prerequisites"></a>Conditions préalables

- Un compte [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)  


Avant de pouvoir utiliser votre compte SMTP dans une logique d’application, vous devez autoriser l’application de la logique pour se connecter à votre compte SMTP. Heureusement, cela peut facilement à partir de dans votre application logique sur le portail d’Azure.  

Voici les étapes pour autoriser votre application logique pour se connecter à votre compte SMTP :  
1. Pour créer une connexion au service SMTP, dans le Concepteur d’application logique, sélectionnez **Afficher les Microsoft d’API gérées** dans la liste déroulante, puis entrez *SMTP* dans la zone de recherche. Sélectionnez l’action que vous allez utiliser ou un déclencheur :  
![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Si vous n’avez pas créé les connexions SMTP avant, vous allez obtenir invité à fournir vos informations d’identification SMTP. Ces informations d’identification seront utilisées pour autoriser votre application logique pour se connecter à et accéder aux données de votre compte SMTP :  
![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Notez la connexion a été créée et vous pouvez maintenant poursuivre les autres étapes dans votre logique d’application :  
 ![](./media/connectors-create-api-smtp/smtp-3.png)  

