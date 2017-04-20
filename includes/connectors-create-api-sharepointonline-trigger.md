Dans cet exemple, je montrerai comment utiliser le déclencheur de **SharePoint Online - lors de la création d’un nouvel élément** pour lancer un workflow d’application logique lors de la création d’un nouvel élément dans une liste SharePoint Online.

>[AZURE.NOTE]Vous sera invité à vous connecter à votre compte SharePoint si vous n’avez pas encore créé une *connexion* à SharePoint Online.  

1. Saisissez *sharepoint* dans la zone de recherche dans le Concepteur d’applications logique, puis sélectionnez le déclencheur **SharePoint Online - lorsqu’un nouvel élément est créé** .  
![Image du déclencheur en ligne SharePoint](./media/connectors-create-api-sharepointonline/trigger-1.png)  
- Le contrôle **lors de la création d’un nouvel élément** s’affiche.  
![Image du déclencheur en ligne SharePoint 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
- Sélectionnez une **URL de Site**. Il s’agit du site en ligne SharePoint que vous souhaitez surveiller pour les nouveaux articles déclencher votre flux de travail.  
![Image du déclencheur en ligne SharePoint 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
- Sélectionnez un **nom de la liste**. Il s’agit de la liste sur le site SharePoint en ligne que vous souhaitez surveiller pour les nouveaux éléments qui déclencheront votre flux de travail.  
![Image du déclencheur en ligne SharePoint 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

À ce stade, votre application logique a été configurée avec un déclencheur qui commence une série d’autres déclencheurs et actions dans le flux de travail. Cela aura lieu chaque fois qu’un nouvel élément est créé dans la liste SharePoint Online que vous sélectionné.  