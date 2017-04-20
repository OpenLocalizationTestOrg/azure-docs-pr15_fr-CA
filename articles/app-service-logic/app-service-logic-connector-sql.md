<properties
   pageTitle="À l’aide du connecteur SQL dans les applications de logique | Service d’application Microsoft Azure"
   description="Comment créer et configurer l’application Connecteur de SQL ou l’API et l’utiliser dans une logique d’application dans le Service d’application Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="sameerch"/>


# <a name="get-started-with-the-microsoft-sql-connector-and-add-it-to-your-logic-app"></a>Mise en route avec le connecteur de SQL de Microsoft et l’ajouter à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2014-12-01-aperçu. Pour la version de schéma Azure SQL 2015-08-01-aperçu, cliquez sur [SQL Azure API](../connectors/connectors-create-api-sqlazure.md).

Se connecter à un site sur SQL Server ou une base de données de SQL Azure pour créer et modifier vos informations ou données. Connecteurs peuvent être utilisés dans les applications de la logique pour récupérer, processus, ou de distribuer des données dans le cadre d’un « workflow ». Lorsque vous utilisez le connecteur SQL dans votre flux de travail, vous pouvez obtenir une variété de scénarios. Par exemple, vous pouvez :

- Exposer une section des données résidant dans votre base de données SQL à l’aide d’un site ou une application mobile.
- Insérer des données dans une table de base de données SQL pour le stockage. Par exemple, vous pouvez entrer des enregistrements de l’employé, mettre à jour les commandes client, etc..
- Obtenir des données à partir de SQL et l’utiliser dans un processus d’entreprise. Par exemple, vous pouvez obtenir des enregistrements de client et mettre les enregistrements client de la force de vente.

Vous pouvez ajouter le connecteur SQL à vos données de workflow et des processus métier dans le cadre de ce flux de travail au sein d’une application de logique. 

## <a name="triggers-and-actions"></a>Déclencheurs et Actions
*Les déclencheurs* sont des événements qui se produisent. Par exemple, lorsqu’une commande est mis à jour ou lorsqu’un nouveau client est ajouté. Une *Action* est le résultat d’un déclencheur. Par exemple, lorsqu’une commande est mis à jour, envoyer une alerte pour le vendeur. Ou bien, lors de l’ajout d’un nouveau client, envoyez un e-mail de bienvenue pour le nouveau client.

Le connecteur de SQL peut être utilisé comme un déclencheur ou d’une action dans une logique app et prend en charge les données au format JSON et XML. Pour chaque table incluse dans votre package de paramètres (plus plus loin dans cette rubrique), il existe un ensemble d’actions de JSON et un ensemble d’actions de XML.

Le connecteur SQL a les déclencheurs et les Actions disponibles suivants :

Déclencheurs | Actions
--- | ---
Données de sondage | <ul><li>Insérer dans la Table</li><li>Mettre à jour la Table</li><li>Sélectionnez à partir de la Table</li><li>Supprimer la Table</li><li>Appelez la procédure stockée</li></ul>

## <a name="create-the-sql-connector"></a>Créer le connecteur SQL

Un connecteur peut être créé dans une application de logique ou être créé directement à partir de l’Azure Marketplace. Pour créer un lien à partir du marché :  

1. Dans la startboard d’Azure, sélectionnez **Marketplace**.
2. Recherchez « SQL connecteur », sélectionnez-le et sélectionnez **créer**.
3. Entrez le nom, Plan de Service d’application et d’autres propriétés.
4. Entrez les paramètres de package suivants :

    Nom | Obligatoire |  Description
--- | --- | ---
Nom du serveur | Oui | Entrez le nom de SQL Server. Par exemple, entrez *SQLserver/sqlexpress* ou *SQLserver.mydomain.com*.
Port | N° | Valeur par défaut est 1433.
Nom d’utilisateur | Oui | Entrez un nom d’utilisateur peut ouvrir une session dans le SQL Server. Si vous vous connectez à un de SQL Server sur site, entrez les informations d’identification de l’authentification SQL.
Mot de passe | Oui | Entrez le mot de passe utilisateur.
Nom de la base de données | Oui | Entrez la base de données à laquelle que vous vous connectez. Par exemple, vous pouvez saisir des *clients* ou *dbo/commandes*.
Sur site | Oui | Valeur par défaut est False. Si la connexion à une base de données SQL d’Azure, entrez False. Entrez True si la connexion à un SQL Server de locaux.
Chaîne de connexion de Bus de service | N° | Si vous vous connectez sur site, entrez la chaîne de connexion de relais de Bus de Service.<br/><br/>[L’aide du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md)<br/>[Bus tarification service](https://azure.microsoft.com/pricing/details/service-bus/)
Nom de serveur partenaire | N° | Si le serveur principal n’est pas disponible, vous pouvez entrer un serveur partenaire comme serveur secondaire ou de sauvegarde.
Tables | N° | Liste des tables de base de données qui peuvent être mis à jour par le connecteur. Par exemple, entrez *OrdersTable* ou *EmployeeTable*. Si aucune table n’est entrée, toutes les tables peuvent être utilisés. Tables valides ou les procédures stockées sont requises pour utiliser ce connecteur comme une action.
Procédures stockées | N° | Entrer une procédure stockée existante qui peut être appelée par le connecteur. Par exemple, entrez *sp_IsEmployeeEligible* ou *sp_CalculateOrderDiscount*. Tables valides ou les procédures stockées sont requises pour utiliser ce connecteur comme une action.
Requête de données disponibles | Pour la prise en charge de déclencheur | Instruction SQL afin de déterminer si des données sont disponibles pour l’interrogation d’une table de base de données SQL Server. Cela doit retourner une valeur numérique représentant le nombre de lignes de données disponibles. Exemple : SELECT Count de table_name.
Requête d’interrogation de données | Pour la prise en charge de déclencheur | L’instruction SQL pour interroger la table de base de données SQL Server. Vous pouvez entrer n’importe quel nombre d’instructions SQL séparées par un point-virgule. Cette instruction est exécutée en mode transactionnel et uniquement validée lorsque les données sont stockées en toute sécurité dans votre logique d’application. Exemple : SELECT *à partir de la table table_name ; DELETE FROM nom_table. <br/>**Note**<br/>Vous devez fournir une instruction de sondage qui permet d’éviter une boucle infinie en supprimant, déplaçant ou mise à jour de données sélectionnées pour vous assurer que les mêmes données n’est pas interrogées à nouveau.

5. Lorsque vous avez terminé, les paramètres de Package ressembler à ce qui suit :  
![][1]  

6. Sélectionnez **créer**. 


## <a name="use-the-connector-as-a-trigger"></a>Utiliser le connecteur comme un déclencheur.
Examinons une application logique simple qui interroge les données d’une table SQL, ajoute les données dans une autre table et met à jour les données.

Pour utiliser le connecteur SQL comme un déclencheur, entrez les valeurs de la **Requête d’interrogation de données** et de **Requête de données disponibles** . **Requête de données disponibles** est exécutée sur la grille, vous entrez et détermine si les données sont disponibles. Étant donné que cette requête renvoie uniquement un nombre scalaire, peut être réglée et optimisée pour l’exécution fréquente.

**Requête d’interrogation de données** est exécutée uniquement lorsque la requête de données disponible indique que les données sont disponibles. Cette instruction s’exécute dans une transaction et n’est validée lorsque les données extraites sont stockées durablement dans votre flux de travail. Il est important d’éviter l’infini extraire à nouveau les mêmes données. La nature transactionnelle de cette exécution permet de supprimer ou de mettre à jour les données pour vous assurer qu’il n’est pas collectées à la prochaine ouverture de données sont interrogées.

> [AZURE.NOTE] Le schéma retourné par cette instruction identifie les propriétés disponibles dans votre connecteur. Toutes les colonnes doivent être nommés.

#### <a name="data-available-query-example"></a>Exemple de requête de données

    SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### <a name="poll-data-query-example"></a>Exemple de requête de données de sondage

    SELECT *, GetData() as 'PollTime' FROM [Order]
        WHERE OrderStatus = 'ProcessedForCollection'
        ORDER BY Id DESC;
    UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
        WHERE Id =
        (SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### <a name="add-the-trigger"></a>Ajouter le déclencheur
1. Lors de la création ou la modification d’une application de logique, sélectionnez le connecteur SQL que vous avez créé en tant que déclencheur. Cette liste répertorie les déclencheurs disponibles : **Interrogation données (JSON)** et **l’Interrogation de données (XML)**:  
![][5]

2. Sélectionnez le déclencheur de **Données de sondage (JSON)** , entrez la fréquence, cliquez sur le ✓ :  
![][6]

3. Le déclencheur s’affiche maintenant comme configuré dans la logique d’application. Les sorties du déclencheur sont affichés et peuvent être utilisées comme entrées dans toutes les actions suivantes :  
![][7]

## <a name="use-the-connector-as-an-action"></a>Utiliser le connecteur sous forme d’Action
À l’aide de notre scénario d’application logique simple qui interroge les données d’une table SQL, ajoute les données dans une autre table et met à jour les données.

Pour utiliser le connecteur SQL en tant qu’action, entrez le nom des Tables ou procédures stockées que vous avez entré lorsque vous avez créé le connecteur SQL :

1. Après votre déclencheur (ou choisissez « exécuter manuellement cette logique »), ajoutez le connecteur SQL créées à partir de la galerie. Vous avez le choix entre les actions Insert, comme *l’Insertion dans TempEmployeeDetails (JSON)*:  
![][8]

2. Entrez les valeurs d’entrée de l’enregistrement à insérer, puis cliquez sur le ✓ :  
![][9]

3. À partir de la galerie, sélectionnez le même connecteur SQL que vous avez créé. En tant qu’action, sélectionnez l’action de mise à jour sur la même table, comme *EmployeeDetails de mise à jour*:  
![][11]

4. Entrez les valeurs d’entrée pour l’action de mise à jour et cliquez sur le ✓ :  
![][12]

Vous pouvez tester l’application de la logique en ajoutant un nouvel enregistrement dans la table qui est sondée.

## <a name="what-you-can-and-cannot-do"></a>Vous pouvez et ne pouvez pas faire

Requêtes SQL | Prise en charge | Non pris en charge
--- | --- | ---
Où clause | <ul><li>Opérateurs : Et, ou, <>, =, <, < =, >, > = et que</li><li>Plusieurs conditions sub peuvent être combinées par '(' et')'</li><li>Littéraux de chaîne, Datetime (placé entre guillemets simples), des nombres (doit contenir uniquement des caractères numériques)</li><li>Doit être strictement dans un format d’expression binaire, comme ((operand operator operand) ou (opérande d’opérateur opérande)) *</li></ul> | <ul><li>Opérateurs : Between, IN</li><li>Toutes les fonctions intégrées telles que ADD(), MAX() maintenant(), POWER() et ainsi de suite</li><li>Opérateurs mathématiques tels que *, -, +, etc.</li><li>Les concaténations de chaînes à l’aide de +.</li><li>Toutes les jointures</li><li>EST NULL et N'EST pas Null</li><li>Les nombres avec des caractères non numériques, comme les nombres hexadécimaux</li></ul>
Champs (requête Select) | <ul><li>Noms de colonne corrects séparés par des virgules. Aucun préfixe de nom de table n’autorisée (sur une seule table à la fois le fonctionnement du connecteur).</li><li>Les noms peuvent être précédées de ' [' et ']'</li></ul> | <ul><li>Mots clés telles que TOP, DISTINCT, etc.</li><li>Alias, telles que la rue, ville + Zip des adresses en tant que</li><li>Fonctions intégrées de tous, tels que ADD(), MAX() maintenant(), POWER(), etc.</li><li>Les opérateurs mathématiques, tels que *, -, +, etc.</li><li>Les concaténations de chaînes à l’aide de +</li></ul>

#### <a name="tips"></a>Conseils

- Pour les requêtes avancées, nous vous suggérons de création d’une procédure stockée et l’exécuter à l’aide de la procédure stockée d’execute API.
- Lors de l’utilisation de requêtes internes, les utiliser dans des procédures stockées.
- Pour joindre plusieurs conditions, vous pouvez utiliser l’opérateur 'AND' et les opérateurs 'OR'.

## <a name="hybrid-configuration-optional"></a>Configuration hybride (facultative)

> [AZURE.NOTE] Cette étape est requise uniquement si vous utilisez SQL Server sur site derrière votre pare-feu.

Service d’application utilise le Gestionnaire de Configuration hybride pour se connecter en toute sécurité sur votre système local. Si vous êtes le connecteur utilise une de SQL Server sur site, le Gestionnaire de connexion hybride est requis.

> [AZURE.NOTE] Si vous souhaitez démarrer avec les applications Azure logique avant l’ouverture d’un compte Azure, accédez à [Essayer une application de logique](https://tryappservice.azure.com/?appservice=logic), où vous pouvez créer une application de logique de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

Consultez [l’aide du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l’ajouter à un workflow d’entreprise à l’aide d’une application de logique. Consultez [Quelles sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

Permet d’afficher la référence de l’API de REST Swagger à des [connecteurs et des applications de référence sur l’API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les performances statistiques et contrôle de sécurité sur le connecteur. Reportez-vous à la section [gérer et surveiller les connecteurs et les applications d’API intégrées](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png
