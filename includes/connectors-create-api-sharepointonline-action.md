Maintenant que vous avez ajouté un déclencheur, il est temps de faire quelque chose intéressante avec les données qui sont générées par le déclencheur. Suivez ces étapes pour ajouter l’action **SharePoint Online, créer le fichier** . Cette action crée un fichier dans SharePoint Online chaque fois que le nouveau déclencheur d’élément se déclenche. 

Pour configurer cette action, vous devez fournir les informations suivantes. Vous spécifiez ces informations, vous remarquerez qu’il est générées par le déclencheur en tant qu’entrée pour certaines des propriétés du nouveau fichier de données facile à utiliser :

|Créer des propriétés de fichier|Description|
|---|---|
|URL du site|Il s’agit de l’URL du site SharePoint en ligne où vous souhaitez créer le nouveau fichier. Dans la liste affichée, sélectionnez le site.|
|Chemin d’accès du dossier|Il s’agit du dossier (au niveau de l’URL du Site sélectionné à l’étape précédente) où sera placé le nouveau fichier. Recherchez et sélectionnez le dossier.|
|Nom de fichier|C’est le nom du fichier en cours de création.|
|Contenu du fichier|Le contenu sera écrit dans le fichier.|

1. Sélectionnez **+ nouvelle étape** pour ajouter l’action.  
![Image d’action en ligne SharePoint 1](./media/connectors-create-api-sharepointonline/action-1.png)  
- Cliquez sur le lien **Ajouter une action** . Ouvre la boîte de recherche dans lequel vous pouvez rechercher n’importe quelle action vous souhaitez prendre. Pour cet exemple, les actions de SharePoint sont d’intérêt.    
![Image d’action en ligne SharePoint 2](./media/connectors-create-api-sharepointonline/action-2.png)    
- Saisissez *sharepoint* pour rechercher des actions liées à SharePoint.
- Sélectionnez **SharePoint Online, créer le fichier** en tant que l’action à effectuer.   **Remarque**: vous serez invité à autoriser votre application logique pour accéder à votre compte SharePoint si vous n’avez pas précédemment créé une connexion à SharePoint Online.    
![Image d’action en ligne SharePoint 3](./media/connectors-create-api-sharepointonline/action-3.png)    
- Le contrôle de **créer un fichier** s’ouvre.   
![Image d’action en ligne SharePoint 4](./media/connectors-create-api-sharepointonline/action-4.png)     
- Sélectionnez **l’URL du Site** et Parcourir pour rechercher le site où vous souhaitez créer le fichier.     
![Image d’action en ligne SharePoint 5](./media/connectors-create-api-sharepointonline/action-5.png)  
- Sélectionnez le **chemin d’accès du dossier** et Parcourir pour rechercher le dossier où sera placé le nouveau fichier.  
![Image d’action en ligne SharePoint 6](./media/connectors-create-api-sharepointonline/action-6.png)  
- Sélectionnez le contrôle de **nom de fichier** et entrez le nom du fichier que vous souhaitez créer. Ici, vous pouvez soit entrer directement le nom du fichier, ou vous pouvez utiliser les propriétés à partir du déclencheur que vous avez créé précédemment. Pour cela, en sélectionnant Propriétés à partir de la liste des **sorties à partir de la création d’un nouvel élément**. Cette liste est uniquement afficher une fois que vous sélectionnez le **nom du fichier** de contrôle. Dans cette procédure pas à pas, j’ai sélectionné des ID (ID de l’élément de liste) comme le nom du fichier créé par l’action de **SharePoint Online - Créer fichier** .    
![Image d’action en ligne SharePoint 7](./media/connectors-create-api-sharepointonline/action-7.png)  
- Sélectionnez le contrôle de **contenu du fichier** et entrez le contenu qui sera écrit dans le fichier qui sera créé. Pour le contenu du fichier, notez que vous pouvez utiliser les propriétés à partir du déclencheur que vous avez créé précédemment. Sélectionnez les propriétés à partir de la liste affichée. Ou bien, vous pouvez entrer le **contenu d’un fichier** texte directement dans le contrôle. Dans cet exemple, j’ai sélectionné certaines propriétés et ajouté des espaces et un tiret entre chaque propriété.        
![Image d’action en ligne SharePoint 8](./media/connectors-create-api-sharepointonline/action-8.png)  
- Enregistrer les modifications apportées à votre flux de travail  
- Félicitations, vous avez maintenant une application totalement fonctionnelle logique qui est déclenchée lorsqu’un élément est ajouté à une liste SharePoint Online. L’application crée un fichier, à l’aide de certaines des propriétés du nouvel élément de liste.  Vous pouvez maintenant le tester en créant un élément dans la liste SharePoint. 
