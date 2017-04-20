<properties 
    pageTitle="Analytique de flux : Faire pivoter le journal des informations d’identification pour les entrées et les sorties | Microsoft Azure" 
    description="Découvrez comment mettre à jour les informations d’identification pour l’Analytique des flux entrées et de sorties."
    keywords="informations d’identification"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Faire pivoter les informations d’identification pour les entrées et les sorties dans les tâches de flux de données Analytique

##<a name="abstract"></a>Résumé
Analytique de flux Azure aujourd'hui ne permet pas remplacer les informations d’identification sur une entrée/sortie pendant l’exécution de la tâche.

Analytique de flux Azure ne prend pas en charge la reprise d’une tâche à partir de la dernière sortie, nous voulons partager l’ensemble du processus pour minimiser le retard entre l’arrêt et démarrage de la tâche et faire pivoter les informations d’identification d’ouverture de session.

##<a name="part-1---prepare-the-new-set-of-credentials"></a>Partie 1 - préparer le nouveau jeu d’informations d’identification :
Cette partie est applicable pour les entrées/sorties suivantes :

* Stockage des objets BLOB
* Concentrateurs d’événement
* Base de données SQL
* Stockage de table

Pour les autres entrées/sorties, poursuivre la partie 2.

###<a name="blob-storagetable-storage"></a>Stockage/Table de stockage des objets BLOB
1.  Accédez à l’extension de stockage sur le portail de gestion d’Azure :  
![graphique1][graphic1]
2.  Recherchez le stockage utilisé par votre travail et allez dans celui-ci :  
![alors que le graphique2][graphic2]
3.  Cliquez sur la commande gérer les touches d’accès rapide :  
![graphic3][graphic3]
4.  Entre la clé d’accès primaire et secondaire touche d’accès rapide, **Choisissez le ne pas utilisés par votre projet**.
5.  Cliquez sur Régénérer (Regenerate) :  
![graphic4][graphic4]
6.  Copiez la clé qui vient d’être générée :  
![graphic5][graphic5]
7.  Passez à la partie 2.

###<a name="event-hubs"></a>Concentrateurs d’événement
1.  Accédez à l’extension de Bus de Service sur le portail de gestion d’Azure :  
![graphic6][graphic6]
2.  Recherchez le Namespace de Bus de Service utilisé par votre tâche et allez dedans :  
![graphic7][graphic7]
3.  Si votre projet utilise une stratégie d’accès partagé sur le Namespace de Bus de Service, passez à l’étape 6  
4.  Accédez à l’onglet de concentrateurs de l’événement :  
![graphic8][graphic8]
5.  Recherchez le concentrateur d’événements utilisées par votre projet et allez dans celui-ci :  
![graphic9][graphic9]
6.  Accédez à l’onglet Configurer :  
![graphic10][graphic10]
7.  Dans la liste déroulante Nom de la stratégie, recherchez la stratégie d’accès partagé utilisée par votre projet :  
![graphic11][graphic11]
8.  Entre la clé primaire et la clé secondaire, **Choisissez le ne pas utilisés par votre projet**.  
9.  Cliquez sur Régénérer (Regenerate) :  
![graphic12][graphic12]
10. Copiez la clé qui vient d’être générée :  
![graphic13][graphic13]
11. Passez à la partie 2.  

###<a name="sql-database"></a>Base de données SQL

>[AZURE.NOTE] Remarque : vous devez vous connecter au Service de base de données SQL. Nous allons montrer comment effectuer cette opération à l’aide de l’environnement de gestion sur le portail de gestion d’Azure, mais vous pouvez choisir d’utiliser un outil côté client, tels que SQL Server Management Studio.

1.  Accédez à l’extension des bases de données SQL sur le portail de gestion d’Azure :  
![graphic14][graphic14]
2.  Recherchez la base de données SQL utilisée par votre lien du projet et **cliquez sur le serveur** sur la même ligne :  
![graphic15][graphic15]
3.  Cliquez sur la commande gérer :  
![graphic16][graphic16]
4.  Type de base de données Master :  
![graphic17][graphic17]
5.  Tapez votre nom d’utilisateur, mot de passe et cliquez sur journal dans :  
![graphic18][graphic18]
6.  Cliquez sur Nouvelle requête :  
![graphic19][graphic19]
7.  Type de la requête suivante en remplaçant < login_name > avec votre nom d’utilisateur et le remplacement des <enterStrongPasswordHere> avec votre nouveau mot de passe :  
`CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Cliquez sur Exécuter :  
![graphic20][graphic20]
9.  Revenez à l’étape 2 et cette fois, cliquez sur la base de données :  
![graphic21][graphic21]
10. Cliquez sur la commande gérer :  
![graphic22][graphic22]
11. Tapez votre nom d’utilisateur, mot de passe et cliquez sur ouverture de session :  
![graphic23][graphic23]
12. Cliquez sur Nouvelle requête :  
![graphic24][graphic24]
13. Tapez la requête suivante en remplaçant < nom_utilisateur > avec un nom par lequel vous souhaitez identifier cette connexion dans le contexte de cette base de données (vous pouvez fournir la même valeur que vous avez donné pour < login_name >, par exemple) et le remplacement des < login_name > avec votre nouveau nom d’utilisateur :  
`CREATE USER <user_name> FROM LOGIN <login_name>`
14. Cliquez sur Exécuter :  
![graphic25][graphic25]
15. Vous devez maintenant fournir votre nouvel utilisateur avec les rôles et les privilèges de l’utilisateur d’origine avait même.
16. Passez à la partie 2.

##<a name="part-2-stopping-the-stream-analytics-job"></a>Partie 2 : Arrêt de la tâche d’Analytique de flux
1.  Accédez à l’extension Analytique de flux sur le portail de gestion d’Azure :  
![graphic26][graphic26]
2.  Localisez votre projet et allez dans celui-ci :  
![graphic27][graphic27]
3.  Accédez à l’onglet entrées ou les sorties en fonction de si vous faites pivoter les informations d’identification sur une entrée ou une sortie.  
![graphic28][graphic28]
4.  Cliquez sur la commande d’arrêt et de confirmer que le travail s’est arrêté :  
![graphic29][graphic29] d’attente de travail à arrêter.
5.  Recherchez l’entrée/la sortie que vous souhaitez faire pivoter des informations d’identification sur Composition à emporter dans ce :  
![graphic30][graphic30]
6.  Passez à la partie 3.

##<a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>Partie 3 : Modifier les informations d’identification sur le flux de travail d’Analytique

###<a name="blob-storagetable-storage"></a>Stockage/Table de stockage des objets BLOB
1.  Recherchez le champ de clé de compte de stockage et coller votre nouvelle clé :  
![graphic31][graphic31]
2.  Cliquez sur la commande Enregistrer et confirmer l’enregistrement de vos modifications :  
![graphic32][graphic32]
3.  Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications, assurez-vous que c'est-à-dire a réussi.
4.  Passez à la partie 4.

###<a name="event-hubs"></a>Concentrateurs d’événement
1.  Recherche le champ clé de la stratégie événement concentrateur et coller votre nouvelle clé :  
![graphic33][graphic33]
2.  Cliquez sur la commande Enregistrer et confirmer l’enregistrement de vos modifications :  
![graphic34][graphic34]
3.  Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications, assurez-vous qu’il a passé avec succès.
4.  Passez à la partie 4.

###<a name="power-bi"></a>Alimentation BI
1.  Cliquez sur l’autorisation de renouvellement :  
* ![graphic35][graphic35]
* Vous obtiendrez la confirmation suivante :  
* ![graphic36][graphic36]
2.  Cliquez sur la commande Enregistrer et confirmer l’enregistrement de vos modifications :  
![graphic37][graphic37]
3.  Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications, assurez-vous qu’il a réussi.
4.  Passez à la partie 4.

###<a name="sql-database"></a>Base de données SQL
1.  Rechercher les champs nom d’utilisateur et mot de passe et coller votre jeu nouvellement créé des informations d’identification :  
![graphic38][graphic38]
2.  Cliquez sur la commande Enregistrer et confirmer l’enregistrement de vos modifications :  
![graphic39][graphic39]
3.  Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications, assurez-vous qu’il a passé avec succès.  
4.  Passez à la partie 4.

##<a name="part-4-starting-your-job-from-last-stopped-time"></a>Partie 4 : Démarrage de votre travail à partir de la dernière heure d’arrêt
1.  Accédez à l’entrée et la sortie :  
![graphic40][graphic40]
2.  Cliquez sur la commande de démarrage :  
![graphic41][graphic41]
3.  Sélectionnez la dernière heure d’arrêt, puis cliquez sur OK :  
 ![graphic42][graphic42]
4.  Passez à la partie 5.  

##<a name="part-5-removing-the-old-set-of-credentials"></a>Partie 5 : Suppression de l’ancien ensemble d’informations d’identification
Cette partie est applicable pour les entrées/sorties suivantes :
* Stockage des objets BLOB
* Concentrateurs d’événement
* Base de données SQL
* Stockage de table

###<a name="blob-storagetable-storage"></a>Stockage/Table de stockage des objets BLOB
Répétez partie 1 pour la touche d’accès rapide qui était utilisé précédemment par votre travail de renouveler la clé d’accès maintenant inutilisés.

###<a name="event-hubs"></a>Concentrateurs d’événement
Répétez 1 de la partie de la clé qui a été précédemment utilisée par votre travail pour renouveler la clé maintenant inutilisée.

###<a name="sql-database"></a>Base de données SQL
1.  Revenir à la fenêtre de requête à partir de la partie 1 étape 7 et tapez la requête suivante, remplacez < previous_login_name > avec le nom de l’utilisateur qui a été utilisé précédemment par votre travail :  
`DROP LOGIN <previous_login_name>`  
2.  Cliquez sur Exécuter :  
    ![graphic43][graphic43]  

Vous devez obtenir la confirmation suivante : 

    Command(s) completed successfully.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 
