## <a name="connect-to-outlookcom"></a>Se connecter à Outlook.com

### <a name="prerequisites"></a>Conditions préalables
- Un compte Outlook.com

Avant de pouvoir utiliser votre compte de Outlook.com dans une logique d’application, vous devez autoriser l’application de la logique pour se connecter à votre compte de Outlook.com. Heureusement, cela peut facilement à partir de dans votre application logique sur le portail d’Azure. 

Voici les étapes pour autoriser votre application logique pour se connecter à votre compte de Outlook.com :

1. Toutes les applications de la logique doivent être démarré par un déclencheur pour une fois que vous créez votre logique d’application, le concepteur ouvre et affiche une liste des déclencheurs qui vous permet de démarrer votre application logique :

  ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. Dans la zone Rechercher, entrez « outlook ». Notez que la liste est filtrée pour répertorier tous les déclencheurs avec « Outlook » dans le nom :![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Sélectionnez **Office 365 Outlook - de nouveaux messages électroniques**.   
  Si vous n’avez pas créé les connexions Outlook avant, vous devrez obtenir pour fournir vos informations d’identification de Outlook.com. Ces informations d’identification seront utilisées pour autoriser votre application logique pour se connecter à et accéder aux données de votre compte Outlook.com :![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Fournir vos informations d’identification pour Outlook et vous connecter :![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
  Voilà. Vous avez maintenant créé une connexion à Outlook. Cette connexion sera disponible pour une utilisation dans n’importe quelle autre application logique que vous créez.


