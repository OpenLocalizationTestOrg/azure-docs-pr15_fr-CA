Maintenant que vous avez ajouté une condition, il est temps de faire quelque chose intéressante avec les données qui sont générées par le déclencheur. Suivez ces étapes pour ajouter l’action de la **force de vente - obtention de l’objet** . Cette action obtiendra les données chaque fois qu’un nouveau prospect est créé. Vous allez également ajouter une deuxième action qui utilisera les données de la force de vente - obtenir une action de l’objet pour envoyer un e-mail à l’aide du connecteur Office 365.  

Pour configurer cette action, vous devez fournir les informations suivantes. Vous remarquerez qu’il est générées par le déclencheur en tant qu’entrée pour certaines des propriétés du nouveau fichier de données facile à utiliser :

|Créer des propriétés de fichier|Description|
|---|---|
|Type d’objet|Il s’agit du type d’objet de force de vente que vous intéressez. Prospect, compte, etc. sont des exemples.|
|ID de l’objet|Cela représente un identificateur pour l’objet.|


1. Sélectionnez le lien **Ajouter une action** . Ouvre la boîte de recherche dans lequel vous pouvez rechercher n’importe quelle action vous souhaitez prendre. Pour cet exemple, les actions de la force de vente sont d’intérêt.      
![Image d’action de force de vente 1](./media/connectors-create-api-salesforce/action-1.png)  
- Permet d’entrer la *force de vente* pour effectuer une recherche pour des actions liées à la force de vente.
- Sélectionnez l’action à entreprendre **force de vente - obtention de l’objet** .   **Remarque**: vous serez invité à autoriser votre application logique pour accéder à votre force de vente compte si vous le n'avez pas fait précédemment.    
![Image d’action de force de vente 2](./media/connectors-create-api-salesforce/action-2.png)    
- Le contrôle **d’obtenir l’objet** s’ouvre.  
- Comme le type d’objet, sélectionnez *prospect* .
- Sélectionnez le contrôle de **l’ID de l’objet** .
- Pour développer la liste de jetons qui peut être utilisé comme entrée pour les actions, sélectionnez **...** .       
![Image d’action de force de vente 3](./media/connectors-create-api-salesforce/action-3.png)    
- Sélectionnez contrôle **d’ID prospect** s’ouvre.   
![Image d’action de force de vente 4](./media/connectors-create-api-salesforce/action-4.png)     
- Notez que le jeton d’ID prospect est présent dans le contrôle de l’ID de l’objet, indiquant que l’action d’objet Get recherchera un prospect avec un ID égal à l’ID du responsable du prospect qui a déclenché cette application logique.  
![Image d’action de force de vente 5](./media/connectors-create-api-salesforce/action-5.png)  
- Enregistrez votre travail. Par conséquent, vous avez ajouté l’action Get de l’objet à votre application de logique. Votre contrôle d’objet Get doit ressembler à ceci :    
![Image d’action de force de vente 6](./media/connectors-create-api-salesforce/action-6.png)  

Maintenant que vous avez ajouté une action pour obtenir un prospect, vous souhaiterez peut-être faire quelque chose d’intéressant avec le prospect qui vient d’être créé. Dans une entreprise, vous souhaiterez envoyer un courrier électronique pour notifier une liste de distribution qu’un prospect a été créé. Nous allons utiliser le connecteur Office 365 pour envoyer un courrier électronique avec des informations pertinentes à partir du nouvel objet de prospect dans la force de vente.  

1. Sélectionnez **Ajouter une action** , puis saisir *l’adresse e-mail* dans le contrôle de recherche. Cela permet de filtrer les actions à ceux qui sont liés à l’envoi et la réception de courrier électronique.  
- Sélectionnez l’élément de liste **Office 365 Outlook - envoyer un e-mail** . Si vous ne l’avez pas déjà créé une *connexion* à votre compte Office 365, vous êtes invité à entrer vos informations d’identification d’Office 365 pour créer maintenant. Une fois que vous avez terminé, le contrôle **d’Envoyer un message électronique** s’ouvre.        
![Image d’action de force de vente 7](./media/connectors-create-api-salesforce/action-7.png)  
- Entrez l’adresse e-mail que vous souhaitez dans le contrôle **pour** envoyer un e-mail à.
-  Dans le contrôle de **l’objet** , entrez le *nouveau prospect créé* -, puis sélectionnez le jeton de la *société* . Le champ de la *société* à partir du nouveau prospect créé dans la force de vente s’affiche.  
-  Dans le contrôle de **corps** , vous pouvez sélectionner un des jetons dans le nouvel objet de prospect, et vous pouvez également entrer le texte que vous souhaitez afficher dans le corps de l’e-mail. Voici un exemple :  
![Image d’action de force de vente 8](./media/connectors-create-api-salesforce/action-8.png)   
- Enregistrez votre flux de travail.  

Voilà. Votre application logique est maintenant terminée.  

Vous pouvez à présent tester votre application logique : force de vente, de créer un nouveau prospect qui répond à la condition que vous avez créé.  Si vous avez suivi cette procédure entièrement, créez simplement un prospect avec une adresse de messagerie qui contient *amazon.com* qu’il contient. Votre application logique doit être déclenchée après quelques secondes, et les résultats peuvent ressembler à ceci :  
![Image d’action de force de vente 9](./media/connectors-create-api-salesforce/action-9.png)  

