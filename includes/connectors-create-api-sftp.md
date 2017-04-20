### <a name="prerequisites"></a>Conditions préalables

- Un compte [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)  


Avant de pouvoir utiliser votre compte SFTP dans une logique d’application, vous devez autoriser l’application de la logique pour se connecter à votre compte SFTP. Heureusement, cela peut facilement à partir de dans votre application logique sur le portail d’Azure.  

Voici les étapes pour autoriser votre application logique pour se connecter à votre compte SFTP :  
1. Pour créer une connexion SFTP, dans le Concepteur d’application logique, sélectionnez **Afficher les Microsoft d’API gérées** dans la liste déroulante, puis entrez *SFTP* dans la zone de recherche. Sélectionnez le déclencheur **SFTP - lorsqu’un fichier est ajouté ou modifié** :  
![Image de connexion SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Si vous n’avez pas créé les connexions SFTP avant, vous devrez obtenir pour fournir vos informations d’identification SFTP. Ces informations d’identification seront utilisées pour autoriser votre application logique pour se connecter à et accéder aux données de votre compte SFTP :  
![Image de connexion SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Notez la connexion a été créée et vous pouvez maintenant poursuivre les autres étapes dans votre logique d’application :   
 ![Image de connexion SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 
