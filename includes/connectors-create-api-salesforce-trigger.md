Dans cette procédure, vous allez apprendre à utiliser le déclencheur de la **force de vente - lorsqu’un objet est créé** pour initier un flux de travail application logique lors de la création d’un nouveau prospect dans votre force de vente.

>[AZURE.NOTE]Vous sera invité à vous connecter à votre compte de la force de vente, si vous n’avez pas encore créé une *connexion* à la force de vente.  

1. Entrez la *force de vente* dans la zone de recherche dans le Concepteur d’applications logique, puis sélectionnez le déclencheur de la **force de vente - lorsqu’un objet est créé** .  
![Image de déclenchement de force de vente 1](./media/connectors-create-api-salesforce/trigger-1.png)   
- Le contrôle **lorsqu’un objet est créé** est affiché.  
![Image de déclenchement de force de vente 2](./media/connectors-create-api-salesforce/trigger-2.png)   
- Sélectionnez le **Type d’objet** , puis sélectionnez le *prospect* à partir de la liste d’objets. Dans cette étape vous indiquez que vous créez un déclencheur qui avertit votre application logique lors de la création d’un nouveau prospect dans la force de vente.   
![Image de déclenchement de force de vente 3](./media/connectors-create-api-salesforce/trigger-3.png)   
- Voilà. Vous avez créé le déclencheur. Toutefois, vous devez créer au moins une action pour permettre cela une application logique valide.    
![Image de déclenchement de force de vente 4](./media/connectors-create-api-salesforce/trigger-4.png)   

À ce stade, votre application logique a été configurée avec un déclencheur qui commence une série d’autres déclencheurs et actions dans le flux de travail lorsqu’un nouvel élément est créé dans votre force de vente.  