Voici comment utiliser le déclencheur de **Bus de Service - lors de la réception d’un message dans une file d’attente** pour initier un flux de travail logique app lorsqu’un nouvel élément est envoyé à une file d’attente de Bus de Service.  

>[AZURE.NOTE]Vous devez vous connecter avec votre chaîne de connexion du Bus de Service si vous n’avez pas encore créé une connexion à un Bus de Service.  

1. Dans la zone de recherche dans le Concepteur d’applications logique, entrez **bus de service**. Sélectionnez ensuite le **Service Bus - lors de la réception d’un message dans une file d’attente** de déclencheur.  
![Image de déclenchement de Bus de service 1](./media/connectors-create-api-servicebus/trigger-1.png)   
- La boîte de dialogue **lors de la réception d’un message dans une file d’attente** s’affiche.  
![Image de déclenchement de Bus de service 2](./media/connectors-create-api-servicebus/trigger-2.png)   
- Entrez le nom de la file d’attente de Bus de Service que vous souhaitez que le déclencheur à analyser.   
![Image de déclenchement de Bus de service 3](./media/connectors-create-api-servicebus/trigger-3.png)   

À ce stade, votre application logique a été configurée avec un déclencheur. Lorsqu’un nouvel élément est reçu dans la file d’attente que vous avez sélectionné, le déclencheur commence une série d’autres déclencheurs et actions dans le flux de travail.    
