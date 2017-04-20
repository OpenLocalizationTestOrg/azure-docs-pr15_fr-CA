Maintenant que vous avez ajouté un déclencheur, il est temps de faire quelque chose intéressante avec les données qui sont générées par le déclencheur. Suivez ces étapes pour ajouter une l’action **SFTP - dossier d’extraction** . Cette action extrait le contenu d’un fichier si les conditions définies sont réunies. 

Pour configurer cette action, vous devez fournir les informations suivantes. Vous remarquerez qu’il est générées par le déclencheur en tant qu’entrée pour certaines des propriétés du nouveau fichier de données facile à utiliser :

|SFTP - propriété de dossier d’extraction|Description|
|---|---|
|Chemin d’accès du fichier source archive|C’est le chemin d’accès pour le fichier en cours d’extraction. Vous pouvez sélectionner un des jetons à partir d’une action antérieure ou parcourir le serveur SFTP pour rechercher le chemin d’accès du fichier.|
|Chemin d’accès du dossier de destination|C’est le chemin d’accès où seront placés les fichiers extraits. Vous pouvez sélectionner un des jetons à partir d’une action antérieure comme le chemin d’accès de destination ou parcourir le serveur SFTP et sélectionnez un chemin d’accès.|
|Le remplacer ?|Indique si un fichier portant le même nom que le fichier se trouve dans le chemin d’accès du dossier de destination si le fichier existant doit être remplacé ou pas.|

Nous allons commencer l’ajout de l’action pour extraire les fichiers si la condition définie précédemment a la valeur *True*. 

1. Sélectionnez **Ajouter une action**.        
![Image de condition d’action SFTP 6](./media/connectors-create-api-sftp/condition-6.png)   
- Sélectionnez l’action **SFTP - dossier d’extraction**      
![Image de condition SFTP action 7](./media/connectors-create-api-sftp/condition-7.png)   
- Sélectionnez le **chemin d’accès du fichier Source archive**              
![Image de condition d’action SFTP 9](./media/connectors-create-api-sftp/condition-9.png)   
- Sélectionnez le jeton de **chemin d’accès du fichier** . Cela indique que vous allez utiliser le chemin d’accès de fichier du fichier que le déclencheur est trouvé comme chemin source des fichiers archive.           
![Image de condition d’action SFTP 10](./media/connectors-create-api-sftp/condition-10.png)   
- Sélectionnez le **chemin d’accès du dossier de Destination**           
![Image de condition d’action SFTP 11](./media/connectors-create-api-sftp/condition-11.png)   
- Sélectionnez le jeton de **chemin d’accès du fichier** . Cela indique que vous utiliserez le chemin d’accès de fichier du fichier que le déclencheur est détecté en tant que le chemin d’accès de destination pour les fichiers extraits.   
- Entrez *\ExtractedFile* dans le contrôle de **chemin d’accès du dossier de Destination** . Pour ce faire, juste après le jeton du chemin d’accès de fichier dans le contrôle de chemin d’accès de dossier de Destination.         
![Image de condition d’action SFTP 12](./media/connectors-create-api-sftp/condition-12.png)   
- Saisissez *True* dans le **Remplacer ?* contrôle pour indiquer que les fichiers existants doivent être remplacés s’ils ont le même nom que les fichiers extraits.      
![Image de condition d’action SFTP 13](./media/connectors-create-api-sftp/condition-13.png)   
- Enregistrer les modifications apportées à votre flux de travail  
