### <a name="prerequisites"></a>Conditions préalables
- Un compte Twilio
- Un numéro de téléphone Twilio vérifié qui peut recevoir des SMS
- Un numéro de téléphone Twilio vérifié que vous pouvez envoyer un SMS

>[AZURE.NOTE] Si vous utilisez un compte d’évaluation Twilio, vous pouvez uniquement envoyer des SMS à **Vérifier** les numéros de téléphone.  

Avant de pouvoir utiliser votre compte de Twilio dans une logique d’application, vous devez autoriser l’application logique pour se connecter à votre compte de Twilio. Heureusement, cela peut facilement à partir de dans votre application logique sur le portail d’Azure. 

Voici les étapes pour autoriser votre application logique pour se connecter à votre compte de Twilio :

1. Pour créer une connexion à Twilio, dans le Concepteur d’application logique, sélectionnez **Afficher les Microsoft d’API gérées** dans la liste déroulante, puis entrez *Twilio* dans la zone de recherche. Sélectionnez l’action que vous allez utiliser ou un déclencheur :  
  ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Si vous n’avez pas créé les connexions de Twilio avant, vous devrez obtenir pour fournir vos informations d’identification de Twilio. Ces informations d’identification seront utilisées pour autoriser votre application logique pour se connecter à et accéder aux données de votre compte Twilio :  
  ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Vous aurez besoin de **l’id de compte Twilio** et le **jeton d’accès Twilio** du tableau de bord dans Twilio, donc se connecter à votre compte de Twilio désormais récupérer ces deux informations :  
  ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio et la logique des applications utilisent des noms différents pour identifier ces deux types d’informations. Voici comment vous devez les mapper à la boîte de dialogue applications logique :![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Cliquez sur le bouton **créer une connexion** :  
  ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Notez la connexion a été créée et vous pouvez maintenant poursuivre les autres étapes dans votre logique d’application :  
  ![](./media/connectors-create-api-twilio/twilio-5.png)