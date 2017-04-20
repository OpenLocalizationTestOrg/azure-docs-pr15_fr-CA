<properties
   pageTitle="À l’aide du connecteur Informix dans le Service d’application Microsoft Azure | Microsoft Azure"
   description="Comment faire pour utiliser le connecteur Informix avec des actions et des déclencheurs d’application logique"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# <a name="informix-connector"></a>Connecteur d’Informix
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2014-12-01-aperçu.

Le connecteur Microsoft pour Informix est une application API pour connecter des applications via le Service d’application Azure aux ressources stockées dans une base de données IBM Informix. Connecteur inclut un Client Microsoft à se connecter à des serveurs Informix à distance via une connexion réseau TCP/IP, y compris les connexions Azure hybride pour des serveurs Informix sur site à l’aide du relais de Bus de Service Azure. Connecteur prend en charge les opérations de base de données suivantes :

- Lecture de lignes à l’aide de SELECT
- Sondage pour lire des lignes à l’aide de sélectionnez nombre, puis sélectionnez
- Ajouter une ligne ou plusieurs lignes (en masse) à l’aide de INSERT
- Modifier une ligne ou plusieurs lignes (en masse) à l’aide de la mise à jour
- Supprimer une ligne ou plusieurs lignes (en masse) à l’aide de DELETE
- Lecture de modifier des lignes à l’aide du curseur, sélectionnez suivies à UPDATE WHERE CURRENT OF CURSOR
- Lecture pour supprimer des lignes à l’aide du curseur, sélectionnez suivies à UPDATE WHERE CURRENT OF CURSOR
- Exécuter une procédure avec des paramètres d’entrée et de sortie, la valeur de retour, jeu de résultats, à l’aide d’appel
- Des commandes personnalisées et des opérations composites à l’aide de SELECT, INSERT, UPDATE, DELETE

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Connecteur prend en charge les actions et les déclencheurs d’application logique suivants :

Déclencheurs | Actions
--- | ---
<ul><li>Données de sondage</li></ul> | <ul><li>Insertion en bloc</li><li>Insérer</li><li>Mise à jour globale</li><li>Mise à jour</li><li>Appel</li><li>Supprimer en bloc</li><li>Supprimer</li><li>Sélectionnez</li><li>Mise à jour conditionnelle</li><li>Comptabiliser dans EntitySet</li><li>Suppression conditionnelle</li><li>Sélectionnez une entité unique</li><li>Supprimer</li><li>Upsert à EntitySet</li><li>Commandes personnalisées</li><li>Opérations de composite</li></ul>


## <a name="create-the-informix-connector"></a>Créer le connecteur Informix
Vous pouvez définir un connecteur au sein d’une application logique ou depuis le site Marketplace Azure, comme dans l’exemple suivant :  

1. Dans la startboard d’Azure, sélectionnez **Marketplace**.
2. Dans la lame **tout** , tapez **informix** dans la zone de **Recherche tous les éléments** , puis cliquez sur la touche ENTRÉE.
3. Dans la recherche tout le volet résultats, sélectionnez le **connecteur de Informix**.
4. De la lame de description du connecteur Informix, sélectionnez **créer**.
5. Dans la lame de package du connecteur Informix, entrez le nom (par exemple, « InformixConnectorNewOrders »), Plan de Service d’application et d’autres propriétés.
6. Sélectionnez les **paramètres de Package**et entrez les paramètres du lot suivant.

    Nom | Obligatoire |  Description
--- | --- | ---
ConnectionString | Oui | Chaîne de connexion de Client d’Informix (par exemple, « adresse réseau = NomServeur ; Port réseau = 9089 ; ID utilisateur = nom d’utilisateur ; Mot de passe = mot de passe ; Initial Catalog = Les Comptoirs ; schéma par défaut = informix »).
Tables | Oui | Liste séparée par des virgules de noms de table, vue et alias requis pour les opérations d’OData et à générer de la documentation de swagger avec des exemples (par exemple, « NEWORDERS »).
Procédures | Oui | Liste séparée par des virgules des noms de procédure et de fonction (par exemple, « SPORDERID »).
Locales | N° | Déployer à l’aide d’Azure relais de Bus de Service sur site.
ServiceBusConnectionString | N° | Chaîne de connexion de relais de Bus de Service Azure.
PollToCheckData | N° | Instruction de sélectionner le compte à utiliser avec un déclencheur d’application logique (par exemple, « SELECT COUNT (\*) à partir de NEWORDERS WHERE SHIPDATE IS NULL »).
PollToReadData | N° | Instruction SELECT à utiliser avec un déclencheur d’application logique (par exemple, « sélectionnez \* de NEWORDERS où SHIPDATE est mise à jour de NULL pour »).
PollToAlterData | N° | Mise à jour ou supprimer un relevé à utiliser avec un déclencheur d’application logique (par exemple « SHIPDATE de la valeur mise à jour NEWORDERS = en cours DATE WHERE CURRENT OF &lt;curseur&gt;»).

7. Sélectionnez **OK**et sélectionnez **créer**.
8. Lorsque vous avez terminé, les paramètres de Package ressembler à ce qui suit :  
![][1]


## <a name="logic-app-with-informix-connector-action-to-add-data"></a>Application logique avec action de connecteur Informix pour ajouter des données ##
Vous pouvez définir une action d’application logique pour ajouter des données à une table Informix à l’aide d’un Insert de l’API ou le Post pour opération d’entité OData. Par exemple, vous pouvez insérer un nouvel enregistrement de commande client, par traitement d’une instruction SQL INSERT sur une table avec une colonne d’identité, retournant la valeur d’identité ou les lignes affectées à l’application de la logique (sélectionnez le ORDID de TABLE finale (valeurs d’insérer dans NEWORDERS (CUSTID, NOMDESTINATAIRE, SHIPADDR, VILLEDESTINATION, SHIPREG, SHIPZIP) ( ?, ?, ?, ?, ?, ?))).

> [AZURE.TIP] Informix connexion «*Post à EntitySet*» retourne la valeur de colonne d’identité et «*API Insert*» lignes affectées

1. Dans la startboard d’Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **application de la logique**.
2. Entrez le nom (par exemple, « NewOrdersInformix »), Plan de Service d’application, les autres propriétés et sélectionnez **créer**.
3. Dans la startboard d’Azure, sélectionnez la logique application que vous venez de créer, **paramètres**, puis **les déclencheurs et les actions**.
4. De la lame de déclencheurs et actions, sélectionnez **créer de toutes pièces** dans l’application logique modèles.
5. Dans le panneau applications d’API, sélectionnez la **périodicité**, définissez une fréquence et intervalle, puis **activé**.
6. Dans le panneau applications d’API, sélectionnez **lien de Informix**, développez la liste d’opérations pour sélectionner **Insérer dans NEWORDER**.
7. Développez la liste de paramètres pour entrer les valeurs suivantes :  

    Nom | Valeur
--- | --- 
CUSTID | 10042
N° MESSAGER | 10000
DESTINATAIRE | Banque de Kountry K paresseux 
SHIPADDR | 12 orchestre terrasses
VILLE LIVRAISON | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Sélectionnez la **coche** pour enregistrer les paramètres d’action, puis **Enregistrer**.
9. Les paramètres doivent se présenter comme suit :  
![][3]  
10. Dans la liste de **tous les s’exécute** sous **opérations**, sélectionnez l’élément premiers dans la liste (dernière exécution). 
11. Dans la lame **application logique fonctionnent** , sélectionnez l' élément **ACTION** **informixconnectorneworders**.
12. De la lame **d’action d’application logique** , sélectionnez le **Lien d’entrées**. Connecteur d’Informix utilise les entrées à traiter une instruction INSERT paramétrée.
13. De la lame **d’action d’application logique** , sélectionnez le **Lien de sorties**. Les entrées se présente comme suit :  
![][4]

#### <a name="what-you-need-to-know"></a>Ce que vous devez savoir

- Connecteur tronque les noms de table Informix lors de la formation des noms d’action logique app. Par exemple, l’opération **d’insertion dans NEWORDERS** est tronquée à **Insérer dans NEWORDER**.
- Après avoir enregistré l’application logique, **les déclencheurs et les actions**, les application logique traite l’opération. Il peut y avoir un délai d’un nombre de secondes (par exemple, 3 à 5 secondes) avant la logique d’application traite l’opération. Si vous le souhaitez, vous pouvez cliquer sur **Exécuter maintenant** pour l’opération.
- Connecteur d’Informix définit EntitySet membres avec les attributs, y compris si le membre correspond à une colonne avec une valeur par défaut d’Informix ou généré des colonnes (par exemple, identité). Application de la logique affiche un astérisque rouge en regard du nom de code de membre EntitySet, pour indiquer les colonnes Informix nécessitant des valeurs. Vous devez entrer une valeur pour le membre ORDID, ce qui correspond à la colonne d’identité Informix. Vous pouvez entrer des valeurs pour d’autres membres facultatifs (éléments, ORDDATE, REQDATE, n° messager, frais de transport, SHIPCTRY), qui correspondent aux colonnes Informix avec les valeurs par défaut. 
- Connecteur d’Informix renvoie la réponse sur le billet à EntitySet qui inclut les valeurs des colonnes d’identité, qui est dérivée de la SQLDARD DRDA (données de la réponse de zone de données SQL) de l’instruction SQL INSERT préparée à l’application de la logique. Serveur Informix ne renvoie pas les valeurs insérées pour les colonnes avec les valeurs par défaut.  


## <a name="logic-app-with-informix-connector-action-to-add-bulk-data"></a>Application logique avec action de connecteur Informix pour ajouter des données en bloc ##
Vous pouvez définir une action d’application logique pour ajouter des données à une table Informix à l’aide d’une opération d’API Bulk Insert. Par exemple, vous pouvez insérer deux enregistrements de commande client nouvelle, par traitement d’une instruction SQL INSERT à l’aide d’un tableau de valeurs de ligne sur une table avec une colonne d’identité, renvoyant les lignes affectées à l’application de la logique (sélectionnez le ORDID de TABLE finale (valeurs d’insérer dans NEWORDERS (CUSTID, NOMDESTINATAIRE, SHIPADDR, VILLEDESTINATION, SHIPREG, SHIPZIP) ( ?, ?, ?, ?, ?, ?))).

1. Dans la startboard d’Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **application de la logique**.
2. Entrez le nom (par exemple, « NewOrdersBulkInformix »), Plan de Service d’application, les autres propriétés et sélectionnez **créer**.
3. Dans la startboard d’Azure, sélectionnez la logique application que vous venez de créer, **paramètres**, puis **les déclencheurs et les actions**.
4. De la lame de déclencheurs et actions, sélectionnez **créer de toutes pièces** dans l’application logique modèles.
5. Dans le panneau applications d’API, sélectionnez la **périodicité**, définissez une fréquence et intervalle, puis **activé**.
6. Dans le panneau applications d’API, sélectionnez **lien de Informix**, développez la liste d’opérations pour sélectionner **Bulk Insert dans le nouveau**.
7. Entrez la valeur de **lignes** sous forme de tableau. Par exemple, copiez et collez le texte suivant :  

    ```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
    ```
        
8. Sélectionnez la **coche** pour enregistrer les paramètres d’action, puis **Enregistrer**. Les paramètres doivent se présenter comme suit :  
![][6]

9. Dans la liste de **tous les s’exécute** sous **opérations**, cliquez sur l’élément premiers dans la liste (dernière exécution).
10. Dans la lame **application logique exécutée** , cliquez sur l’élément **d’ACTION** .
11. La lame de **cette action logique app** , cliquez sur le **Lien d’entrées**. Les sorties doivent se présenter comme suit :  
[][7]
12. La lame de **cette action logique app** , cliquez sur le **Lien de sorties**. Les sorties doivent se présenter comme suit :  
![][8]

#### <a name="what-you-need-to-know"></a>Ce que vous devez savoir

- Connecteur tronque les noms de table Informix lors de la formation des noms d’action logique app. Par exemple, l’opération **d’Insertion en bloc dans NEWORDERS** est tronquée à **l’Insertion en bloc dans le nouveau**.
- Base de données d’Informix peut-être respectant la casse pour les noms de table et de colonne. Par exemple, les noms de colonne de tableau opération Bulk Insert devez peuvent être spécifié en minuscules (« custid ») au lieu de majuscules (« CUSTID »).
- En omettant les colonnes d’identité (par exemple, ORDID), des colonnes nullables (par exemple SHIPDATE) et colonnes avec les valeurs par défaut (par exemple ORDDATE, REQDATE, n° messager, frais de transport, SHIPCTRY), base de données d’Informix génère des valeurs.
- En spécifiant « aujourd'hui » et « demain », Informix connecteur génère « DATE actuelle » et « DATE actuelle + 1 jour » fonctionne (par exemple, REQDATE). 


## <a name="logic-app-with-informix-connector-trigger-to-read-alter-or-delete-data"></a>Application logique avec déclencheur de connecteur Informix à lire, modifier ou supprimer des données ##
Vous pouvez définir un déclencheur d’application logique pour interroger et lire les données d’une table Informix à l’aide d’une opération de composite de données de sondage d’API. Par exemple, vous pouvez lire l’un ou plusieurs enregistrements de commande client nouvelle, renvoyer les enregistrements dans l’application de la logique. Les paramètres de package/app Informix connexion doivent se présenter comme suit :

    Paramètre d’application | Valeur
--- | --- | ---
PollToCheckData | SELECT COUNT (\*) à partir de NEWORDERS où SHIPDATE est NULL
PollToReadData | Sélectionnez \* de NEWORDERS où SHIPDATE est NULL pour la mise à jour
PollToAlterData | <no value specified>


Vous pouvez également définir un déclencheur d’application logique pour interroger, de lire et de modifier des données dans une table Informix à l’aide d’une opération de composite de données de sondage d’API. Par exemple, vous pouvez lire l’un ou plusieurs enregistrements de commande client nouvelle, mettre à jour les valeurs de la ligne, retourner les enregistrements sélectionnés (avant la mise à jour) à l’application de la logique. Les paramètres de package/app Informix connexion doivent se présenter comme suit :

    Paramètre d’application | Valeur
--- | --- | ---
PollToCheckData | SELECT COUNT (\*) à partir de NEWORDERS où SHIPDATE est NULL
PollToReadData | Sélectionnez \* de NEWORDERS où SHIPDATE est NULL pour la mise à jour
PollToAlterData | Mise à jour NEWORDERS ensemble SHIPDATE = DATE du jour où actuel des &lt;curseur&gt;


En outre, vous pouvez définir un déclencheur d’application logique pour interroger, de lire et de supprimer des données d’une table Informix à l’aide d’une opération de composite de données de sondage d’API. Par exemple, vous pouvez lire l’un ou plusieurs enregistrements de commande client nouvelle, supprimer les lignes, retourner les enregistrements sélectionnés (avant suppression) à l’application de la logique. Les paramètres de package/app Informix connexion doivent se présenter comme suit :

    Paramètre d’application | Valeur
--- | --- | ---
PollToCheckData | SELECT COUNT (\*) à partir de NEWORDERS où SHIPDATE est NULL
PollToReadData | Sélectionnez \* de NEWORDERS où SHIPDATE est NULL pour la mise à jour
PollToAlterData | SUPPRIMER NEWORDERS où actuel des &lt;curseur&gt;

Dans cet exemple, logique application interroge, lire, mettre à jour et lire de nouveau les données dans la table Informix.

1. Dans la startboard d’Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **application de la logique**.
2. Entrez le nom (par exemple, « ShipOrdersInformix »), Plan de Service d’application, les autres propriétés et sélectionnez **créer**.
3. Dans la startboard d’Azure, sélectionnez la logique application que vous venez de créer, **paramètres**, puis **les déclencheurs et les actions**.
4. De la lame de déclencheurs et actions, sélectionnez **créer de toutes pièces** dans l’application logique modèles.
5. Dans le panneau applications d’API, sélectionnez **lien de Informix**, définissez une fréquence et intervalle, puis **activé**.
6. Dans le panneau applications d’API, sélectionnez **lien de Informix**, développez la liste d’opérations pour sélectionner **à partir de NEWORDERS**.
7. Sélectionnez la **coche** pour enregistrer les paramètres d’action, puis **Enregistrer**. Les paramètres doivent se présenter comme suit :  
![][10]
8. Cliquez sur pour fermer la lame **déclencheurs et actions** , puis cliquez sur pour fermer la lame de **paramètres** .
9. Dans la liste de **tous les s’exécute** sous **opérations**, cliquez sur l’élément premiers dans la liste (dernière exécution).
10. Dans la lame **application logique exécutée** , cliquez sur l’élément **d’ACTION** .
11. La lame de **cette action logique app** , cliquez sur le **Lien de sorties**. Les sorties doivent se présenter comme suit :  
![][11]


## <a name="logic-app-with-informix-connector-action-to-remove-data"></a>Application logique avec action de connecteur Informix pour supprimer des données ##
Vous pouvez définir une action d’application logique pour supprimer des données d’une table Informix à l’aide d’une API de suppression ou un Post à OData d’entité opération. Par exemple, vous pouvez insérer un nouvel enregistrement de commande client, par traitement d’une instruction SQL INSERT sur une table avec une colonne d’identité, retournant la valeur d’identité ou les lignes affectées à l’application de la logique (sélectionnez le ORDID de TABLE finale (valeurs d’insérer dans NEWORDERS (CUSTID, NOMDESTINATAIRE, SHIPADDR, VILLEDESTINATION, SHIPREG, SHIPZIP) ( ?, ?, ?, ?, ?, ?))).

## <a name="create-logic-app-using-informix-connector-to-remove-data"></a>Créer l’application logique à l’aide du connecteur de Informix pour supprimer des données ##
Vous pouvez créer une nouvelle application logique à partir de l’Azure Marketplace et ensuite d’utiliser le connecteur Informix en tant qu’action pour supprimer les commandes client. Par exemple, vous pouvez utiliser l’opération de suppression conditionnelle Informix connecteur pour traiter une instruction SQL DELETE (supprimer à partir de NEWORDERS où ORDID > = 10000).

1. Dans le menu hub du Conseil Azure **Démarrer** , cliquez sur **+** (signe plus), cliquez sur **Web + Mobile**, puis cliquez sur **application de logique**. 
2. De la lame **d’application de la logique de créer** , tapez un **nom**, par exemple **RemoveOrdersInformix**.
3. Sélectionnez ou définissez les valeurs des autres paramètres (par exemple, le plan de service, le groupe de ressources).
4. Les paramètres doivent se présenter comme suit. Cliquez sur **créer**:  
![][12]
5. De la lame de **paramètres** , cliquez sur **déclencheurs et actions**.
6. Dans le **déclencheurs et les actions** lame, dans la liste de **l’application logique modèles** , cliquez sur **créer à partir de zéro**.
7. De la lame de **déclencheurs et actions** , dans le panneau **Applications de l’API** , dans le groupe de ressources, cliquez sur **périodicité**.
8. Sur l’aire de conception logique app, cliquez sur l’élément de **périodicité** , définir la **fréquence** et l' **intervalle**, par exemple les **jours** et **1**et puis cliquez sur la **coche** pour enregistrer les paramètres d’article de périodicité.
9. Dans la lame **déclencheurs et actions** , dans le panneau **Applications de l’API** , dans le groupe de ressources, cliquez sur **connecteur de Informix**.
10. Sur l’aire de conception logique app, cliquez sur l’élément d’action **Informix connecteur** , cliquez sur les ellipses (**.**) pour développer la liste des opérations, puis cliquez sur **conditionnel supprimer de N**.
11. Dans l’élément d’action connecteur Informix, tapez **ordid ge 10000** pour une **expression qui identifie un sous-ensemble d’entrées**.
12. Cliquez sur la **coche** pour enregistrer les paramètres de l’action, puis cliquez sur **Enregistrer**. Les paramètres doivent se présenter comme suit :  
![][13]
13. Cliquez sur pour fermer la lame **déclencheurs et actions** , puis cliquez sur pour fermer la lame de **paramètres** .
14. Dans la liste de **tous les s’exécute** sous **opérations**, cliquez sur l’élément premiers dans la liste (dernière exécution).
15. Dans la lame **application logique exécutée** , cliquez sur l’élément **d’ACTION** .
16. La lame de **cette action logique app** , cliquez sur le **Lien de sorties**. Les sorties doivent se présenter comme suit :  
![][14]

**Remarque :** Concepteur d’application logique tronque le nom de la table. Par exemple, l’opération **conditionnelle supprimer NEWORDERS** est tronquée **conditionnel**supprimer N.


> [AZURE.TIP] Utilisez les instructions SQL suivantes pour créer la table et des procédures stockées. 

Vous pouvez créer l’exemple de table NEWORDERS avec les instructions Informix SQL DDL suivantes :
 
    create table neworders (  
        ordid serial(10000) unique ,  
        custid int not null ,  
        empid int not null default 10000 ,  
        orddate date not null default today ,  
        reqdate date default today ,  
        shipdate date ,  
        shipid int not null default 10000 ,  
        freight decimal (9,2) not null default 0.00 ,  
        shipname char (40) not null ,  
        shipaddr char (60) not null ,  
        shipcity char (20) not null ,  
        shipreg char (15) not null ,  
        shipzip char (10) not null ,  
        shipctry char (15) not null default ''USA'' 
        )


Vous pouvez créer l’exemple de procédure SPORDERID stockée à l’aide de l’instruction d’Informix DDL suivante :
 
    create procedure sporderid ( ord_id int)  
        returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
        define xordid, xcustid, xempid, xshipid int;  
        define xorddate, xreqdate, xshipdate date;  
        define xfreight decimal (9,2);  
        define xshipname char (40);  
        define xshipaddr char (60);  
        define xshipcity char (20);  
        define xshipreg, xshipctry char (15);  
        define xshipzip char (10);  
        select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
            into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
            from neworders where ordid = ord_id;  
        return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## <a name="hybrid-configuration-optional"></a>Configuration de hybride (facultatif)

> [AZURE.NOTE] Cette étape est requise uniquement si vous utilisez DB2 le connecteur locaux derrière votre pare-feu.

Service d’application utilise le Gestionnaire de Configuration hybride pour se connecter en toute sécurité sur votre système local. Si le connecteur utilise un serveur local IBM DB2 pour Windows, le Gestionnaire de connexion hybride est requis.

Consultez [l’aide du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l’ajouter à un workflow d’entreprise à l’aide d’une application de logique. Consultez [Quelles sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

Créer des applications API à l’aide des API de reste. Consultez les [connecteurs et les applications de référence sur l’API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les performances statistiques et contrôle de sécurité sur le connecteur. Reportez-vous à la section [gérer et surveiller les connecteurs et les applications d’API intégrées](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png


