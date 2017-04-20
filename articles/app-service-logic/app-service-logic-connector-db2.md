<properties
   pageTitle="À l’aide du connecteur de DB2 dans le Service d’application Microsoft Azure | Microsoft Azure"
   description="Comment faire pour utiliser le connecteur de DB2 avec des actions et des déclencheurs d’application logique"
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

# <a name="db2-connector"></a>Connecteur de DB2
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2014-12-01-aperçu.

Le connecteur Microsoft pour DB2 est une application API pour connecter des applications via le Service d’application Azure aux ressources stockées dans une base de données IBM DB2. Connecteur inclut un Client Microsoft pour se connecter aux ordinateurs de serveur DB2 distants via une connexion réseau TCP/IP, y compris les hybrides Azure des connexions à des serveurs DB2 sur site à l’aide du relais de Bus de Service Azure connecteur prend en charge les opérations de base de données suivantes :

- Lecture de lignes à l’aide de SELECT
- Sondage pour lire des lignes à l’aide de sélectionnez nombre, puis sélectionnez
- Ajouter une ligne ou plusieurs lignes (en masse) à l’aide de INSERT
- Modifier une ligne ou plusieurs lignes (en masse) à l’aide de la mise à jour
- Supprimer une ligne ou plusieurs lignes (en masse) à l’aide de DELETE
- Lecture de modifier des lignes à l’aide du curseur, sélectionnez suivies à UPDATE WHERE CURRENT OF CURSOR
- Lecture pour supprimer des lignes à l’aide du curseur, sélectionnez suivies à UPDATE WHERE CURRENT OF CURSOR
- Exécuter une procédure avec des paramètres d’entrée et de sortie, la valeur de retour, jeu de résultats, à l’aide d’appel
- Des commandes personnalisées et des opérations composites à l’aide de SELECT, INSERT, UPDATE, DELETE

## <a name="triggers-and-actions"></a>Déclencheurs et Actions
Connecteur prend en charge les actions et les déclencheurs d’application logique suivants :

Déclencheurs | Actions
--- | ---
<ul><li>Données de sondage</li></ul> | <ul><li>Insertion en bloc</li><li>Insérer</li><li>Mise à jour globale</li><li>Mise à jour</li><li>Appel</li><li>Supprimer en bloc</li><li>Supprimer</li><li>Sélectionnez</li><li>Mise à jour conditionnelle</li><li>Comptabiliser dans EntitySet</li><li>Suppression conditionnelle</li><li>Sélectionnez une entité unique</li><li>Supprimer</li><li>Upsert à EntitySet</li><li>Commandes personnalisées</li><li>Opérations de composite</li></ul>


## <a name="create-the-db2-connector"></a>Créer le connecteur de DB2
Vous pouvez définir un connecteur au sein d’une application logique ou depuis le site Marketplace Azure, comme dans l’exemple suivant :  

1. Dans la startboard d’Azure, sélectionnez **Marketplace**.
2. Dans la lame **tout** , tapez **db2** dans la zone de **Recherche tous les éléments** , puis cliquez sur la touche ENTRÉE.
3. Dans la recherche tout le volet résultats, sélectionnez le **connecteur de DB2**.
4. De la lame de description du connecteur DB2, sélectionnez **créer**.
5. Dans la lame de package DB2 connecteur, entrez le nom (par exemple, « Db2ConnectorNewOrders »), Plan de Service d’application et d’autres propriétés.
6. Sélectionnez les **paramètres de Package**et entrez les paramètres de package suivants :  

    Nom | Obligatoire |  Description
--- | --- | ---
ConnectionString | Oui | Chaîne de connexion cliente de DB2 (par exemple, « adresse réseau = NomServeur ; Port réseau = 50000 ; ID utilisateur = nom d’utilisateur ; Mot de passe = mot de passe ; Initial Catalog = exemple ; Collection de package = les COMPTOIRS ; par défaut du schéma = les COMPTOIRS »).
Tables | Oui | Liste séparée par des virgules de noms de table, vue et alias requis pour les opérations d’OData et à générer de la documentation de swagger avec des exemples (par exemple, «*NEWORDERS*»).
Procédures | Oui | Liste séparée par des virgules des noms de procédure et de fonction (par exemple, « SPORDERID »).
Locales | N° | Déployer à l’aide d’Azure relais de Bus de Service sur site.
ServiceBusConnectionString | N° | Chaîne de connexion de relais de Bus de Service Azure.
PollToCheckData | N° | Instruction de sélectionner le compte à utiliser avec un déclencheur d’application logique (par exemple, « SELECT COUNT (\*) à partir de NEWORDERS WHERE SHIPDATE IS NULL »).
PollToReadData | N° | Instruction SELECT à utiliser avec un déclencheur d’application logique (par exemple, « sélectionnez \* de NEWORDERS où SHIPDATE est mise à jour de NULL pour »).
PollToAlterData | N° | Mise à jour ou supprimer un relevé à utiliser avec un déclencheur d’application logique (par exemple « SHIPDATE de la valeur mise à jour NEWORDERS = en cours DATE WHERE CURRENT OF &lt;curseur&gt;»).

7. Sélectionnez **OK**et sélectionnez **créer**.
8. Lorsque vous avez terminé, les paramètres de Package ressembler à ce qui suit :  
![][1]


## <a name="logic-app-with-db2-connector-action-to-add-data"></a>Application logique avec action de connecteur de DB2 pour ajouter des données ##
Vous pouvez définir une action d’application logique pour ajouter des données à une table DB2 à l’aide d’un Insert de l’API ou le Post pour opération d’entité OData. Par exemple, vous pouvez insérer un nouvel enregistrement de commande client, par traitement d’une instruction SQL INSERT sur une table avec une colonne d’identité, retournant la valeur d’identité ou les lignes affectées à l’application logique (sélectionnez ORDID à partir de TABLE finale (insérer dans NWIND. LES VALEURS NEWORDERS (CUSTID, NOMDESTINATAIRE, SHIPADDR, VILLEDESTINATION, SHIPREG, SHIPZIP) ( ?, ?, ?, ?, ?, ?))).

> [AZURE.TIP] DB2 connexion «*Post à EntitySet*» retourne la valeur de colonne d’identité et «*API Insert*» lignes affectées

1. Dans la startboard d’Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **application de la logique**.
2. Entrez le nom (par exemple, « NewOrdersDb2 »), Plan de Service d’application, les autres propriétés et sélectionnez **créer**.
3. Dans la startboard d’Azure, sélectionnez la logique application que vous venez de créer, **paramètres**, puis **les déclencheurs et les actions**.
4. De la lame de déclencheurs et actions, sélectionnez **créer de toutes pièces** dans l’application logique modèles.
5. Dans le panneau applications d’API, sélectionnez la **périodicité**, définissez une fréquence et intervalle, puis **activé**.
6. Dans le panneau applications d’API, sélectionnez **lien de DB2**, développez la liste d’opérations pour sélectionner **Insérer dans NEWORDER**.
7. Développez la liste de paramètres pour entrer les valeurs suivantes :  

    Nom | Valeur
--- | --- 
CUSTID | 10042
DESTINATAIRE | Banque de Kountry K paresseux 
SHIPADDR | 12 orchestre terrasses
VILLE LIVRAISON | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Sélectionnez la **coche** pour enregistrer les paramètres d’action, puis **Enregistrer**.
9. Les paramètres doivent se présenter comme suit :  
![][3]

10. Dans la liste de **tous les s’exécute** sous **opérations**, sélectionnez l’élément premiers dans la liste (dernière exécution). 
11. Dans la lame **application logique fonctionnent** , sélectionnez l' élément **ACTION** **db2connectorneworders**.
12. De la lame **d’action d’application logique** , sélectionnez le **Lien d’entrées**. Connecteur de DB2 utilise les entrées à traiter une instruction INSERT paramétrée.
13. De la lame **d’action d’application logique** , sélectionnez le **Lien de sorties**. Les entrées se présente comme suit :  
![][4]

#### <a name="what-you-need-to-know"></a>Ce que vous devez savoir

- Connecteur tronque les noms de tables DB2 lors de la formation des noms d’action logique app. Par exemple, l’opération **d’insertion dans NEWORDERS** est tronquée à **Insérer dans NEWORDER**.
- Après avoir enregistré l’application logique, **les déclencheurs et les actions**, les application logique traite l’opération. Il peut y avoir un délai d’un nombre de secondes (par exemple, 3 à 5 secondes) avant la logique d’application traite l’opération. Si vous le souhaitez, vous pouvez cliquer sur **Exécuter maintenant** pour l’opération.
- Connecteur de DB2 définit les membres EntitySet avec des attributs, notamment si le membre correspond à une colonne avec une valeur par défaut de DB2 ou de colonnes (par exemple, identité) générées. Application de la logique affiche un astérisque rouge en regard du nom de code de membre EntitySet, pour indiquer les colonnes DB2 qui requièrent des valeurs. Vous devez entrer une valeur pour le membre ORDID, ce qui correspond à la colonne d’identité de DB2. Vous pouvez entrer des valeurs pour d’autres membres facultatifs (éléments, ORDDATE, REQDATE, n° messager, frais de transport, SHIPCTRY), qui correspondent aux colonnes de DB2 avec les valeurs par défaut. 
- Connecteur de DB2 renvoie la réponse à l’application de la logique de la publication à l’EntitySet qui inclut les valeurs des colonnes d’identité, qui est dérivée de la SQLDARD DRDA (données de la réponse de zone de données SQL) de l’instruction SQL INSERT préparée. Serveur DB2 ne renvoie pas les valeurs insérées pour les colonnes avec les valeurs par défaut.  


## <a name="logic-app-with-db2-connector-action-to-add-bulk-data"></a>Application logique avec action de connecteur de DB2 pour ajouter des données en bloc ##
Vous pouvez définir une action d’application logique pour ajouter des données à une table DB2 à l’aide d’une opération d’API Bulk Insert. Par exemple, vous pouvez insérer deux enregistrements de commande client nouvelle, par traitement d’une instruction SQL INSERT à l’aide d’un tableau de valeurs de ligne sur une table avec une colonne d’identité, renvoyant les lignes affectées à l’application logique (sélectionnez ORDID à partir de TABLE finale (insérer dans NWIND. LES VALEURS NEWORDERS (CUSTID, NOMDESTINATAIRE, SHIPADDR, VILLEDESTINATION, SHIPREG, SHIPZIP) ( ?, ?, ?, ?, ?, ?))).

1. Dans la startboard d’Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **application de la logique**.
2. Entrez le nom (par exemple, « NewOrdersBulkDb2 »), Plan de Service d’application, les autres propriétés et sélectionnez **créer**.
3. Dans la startboard d’Azure, sélectionnez la logique application que vous venez de créer, **paramètres**, puis **les déclencheurs et les actions**.
4. De la lame de déclencheurs et actions, sélectionnez **créer de toutes pièces** dans l’application logique modèles.
5. Dans le panneau applications d’API, sélectionnez la **périodicité**, définissez une fréquence et intervalle, puis **activé**.
6. Dans le panneau applications d’API, sélectionnez **lien de DB2**, développez la liste d’opérations pour sélectionner **Bulk Insert dans le nouveau**.
7. Entrez la valeur de **lignes** sous forme de tableau. Par exemple, copiez et collez le texte suivant :

    ```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
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

- Connecteur tronque les noms de tables DB2 lors de la formation des noms d’action logique app. Par exemple, l’opération **d’Insertion en bloc dans NEWORDERS** est tronquée à **l’Insertion en bloc dans le nouveau**.
- En omettant les colonnes d’identité (par exemple, ORDID), des colonnes nullables (par exemple SHIPDATE) et colonnes avec les valeurs par défaut (par exemple ORDDATE, REQDATE, n° messager, frais de transport, SHIPCTRY), la base de données DB2 génère des valeurs.
- En spécifiant « aujourd'hui » et « demain », connecteur de DB2 génère « DATE actuelle » et « DATE actuelle + 1 jour » fonctionne (par exemple, REQDATE). 


## <a name="logic-app-with-db2-connector-trigger-to-read-alter-or-delete-data"></a>Application logique avec déclencheur de connecteur de DB2 pour lire, modifier ou supprimer des données ##
Vous pouvez définir un déclencheur d’application logique pour interroger et lire les données d’une table DB2 à l’aide d’une opération de composite de données de sondage d’API. Par exemple, vous pouvez lire l’un ou plusieurs enregistrements de commande client nouvelle, renvoyer les enregistrements dans l’application de la logique. Les paramètres de package/app DB2 connexion doivent se présenter comme suit :

    Paramètre d’application | Valeur
--- | --- | ---
PollToCheckData | SELECT COUNT (\*) à partir de NEWORDERS où SHIPDATE est NULL
PollToReadData | Sélectionnez \* de NEWORDERS où SHIPDATE est NULL pour la mise à jour
PollToAlterData | <no value specified>


Vous pouvez également définir un déclencheur d’application logique pour interroger, de lire et de modifier les données d’une table DB2 à l’aide d’une opération de composite de données de sondage d’API. Par exemple, vous pouvez lire l’un ou plusieurs enregistrements de commande client nouvelle, mettre à jour les valeurs de la ligne, retourner les enregistrements sélectionnés (avant la mise à jour) à l’application de la logique. Les paramètres de package/app DB2 connexion doivent se présenter comme suit :

    Paramètre d’application | Valeur
--- | --- | ---
PollToCheckData | SELECT COUNT (\*) à partir de NEWORDERS où SHIPDATE est NULL
PollToReadData | Sélectionnez \* de NEWORDERS où SHIPDATE est NULL pour la mise à jour
PollToAlterData | Mise à jour NEWORDERS ensemble SHIPDATE = DATE du jour où actuel des &lt;curseur&gt;


En outre, vous pouvez définir un déclencheur d’application logique pour interroger, de lire et de supprimer des données d’une table DB2 à l’aide d’une opération de composite de données de sondage d’API. Par exemple, vous pouvez lire l’un ou plusieurs enregistrements de commande client nouvelle, supprimer les lignes, retourner les enregistrements sélectionnés (avant suppression) à l’application de la logique. Les paramètres de package/app DB2 connexion doivent se présenter comme suit :

    Paramètre d’application | Valeur
--- | --- | ---
PollToCheckData | SELECT COUNT (\*) à partir de NEWORDERS où SHIPDATE est NULL
PollToReadData | Sélectionnez \* de NEWORDERS où SHIPDATE est NULL pour la mise à jour
PollToAlterData | SUPPRIMER NEWORDERS où actuel des &lt;curseur&gt;

Dans cet exemple, logique application interroge, lire, mettre à jour et lire de nouveau les données dans la table de DB2.

1. Dans la startboard d’Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **application de la logique**.
2. Entrez le nom (par exemple, « ShipOrdersDb2 »), Plan de Service d’application, les autres propriétés et sélectionnez **créer**.
3. Dans la startboard d’Azure, sélectionnez la logique application que vous venez de créer, **paramètres**, puis **les déclencheurs et les actions**.
4. De la lame de déclencheurs et actions, sélectionnez **créer de toutes pièces** dans l’application logique modèles.
5. Dans le panneau applications d’API, sélectionnez **lien de DB2**, définissez une fréquence et intervalle, puis **activé**.
6. Dans le panneau applications d’API, sélectionnez **lien de DB2**, développez la liste d’opérations pour sélectionner **à partir de NEWORDERS**.
7. Sélectionnez la **coche** pour enregistrer les paramètres d’action, puis **Enregistrer**. Les paramètres doivent se présenter comme suit :  
![][10]  
8. Cliquez sur pour fermer la lame **déclencheurs et actions** , puis cliquez sur pour fermer la lame de **paramètres** .
9. Dans la liste de **tous les s’exécute** sous **opérations**, cliquez sur l’élément premiers dans la liste (dernière exécution).
10. Dans la lame **application logique exécutée** , cliquez sur l’élément **d’ACTION** .
11. La lame de **cette action logique app** , cliquez sur le **Lien de sorties**. Les sorties doivent se présenter comme suit :  
![][11]


## <a name="logic-app-with-db2-connector-action-to-remove-data"></a>Application logique avec action de connecteur de DB2 pour supprimer des données ##
Vous pouvez définir une action d’application logique pour supprimer des données d’une table DB2 à l’aide d’une API de suppression ou un Post à OData d’entité opération. Par exemple, vous pouvez insérer un nouvel enregistrement de commande client, par traitement d’une instruction SQL INSERT sur une table avec une colonne d’identité, retournant la valeur d’identité ou les lignes affectées à l’application logique (sélectionnez ORDID à partir de TABLE finale (insérer dans NWIND. LES VALEURS NEWORDERS (CUSTID, NOMDESTINATAIRE, SHIPADDR, VILLEDESTINATION, SHIPREG, SHIPZIP) ( ?, ?, ?, ?, ?, ?))).

## <a name="create-logic-app-using-db2-connector-to-remove-data"></a>Créer l’application logique à l’aide du connecteur de DB2 pour supprimer des données ##
Vous pouvez créer une nouvelle application logique à partir de l’Azure Marketplace et ensuite d’utiliser le connecteur DB2 en tant qu’action pour supprimer les commandes client. Par exemple, vous pouvez utiliser l’opération de suppression conditionnelle du connecteur de DB2 pour traiter une instruction SQL DELETE (supprimer à partir de NEWORDERS où ORDID > = 10000).

1. Dans le menu hub du Conseil Azure **Démarrer** , cliquez sur **+** (signe plus), cliquez sur **Web + Mobile**, puis cliquez sur **application de logique**. 
2. De la lame **d’application de la logique de créer** , tapez un **nom**, par exemple **RemoveOrdersDb2**.
3. Sélectionnez ou définissez les valeurs des autres paramètres (par exemple, le plan de service, le groupe de ressources).
4. Les paramètres doivent se présenter comme suit. Cliquez sur **créer**:  
![][12]  
5. De la lame de **paramètres** , cliquez sur **déclencheurs et actions**.
6. Dans le **déclencheurs et les actions** lame, dans la liste de **l’application logique modèles** , cliquez sur **créer à partir de zéro**.
7. De la lame de **déclencheurs et actions** , dans le panneau **Applications de l’API** , dans le groupe de ressources, cliquez sur **périodicité**.
8. Sur l’aire de conception logique app, cliquez sur l’élément de **périodicité** , définir la **fréquence** et l' **intervalle**, par exemple les **jours** et **1**et puis cliquez sur la **coche** pour enregistrer les paramètres d’article de périodicité.
9. Dans la lame **déclencheurs et actions** , dans le panneau **Applications de l’API** , dans le groupe de ressources, cliquez sur **connecteur de DB2**.
10. Sur l’aire de conception logique app, cliquez sur l’élément d’action de **connecteur de DB2** , cliquez sur les ellipses (**.**) pour développer la liste des opérations, puis cliquez sur **conditionnel supprimer de N**.
11. Sur l’élément d’action connecteur DB2, tapez une **expression qui identifie un sous-ensemble d’entrées** **ORDID ge 10000** .
12. Cliquez sur la **coche** pour enregistrer les paramètres de l’action, puis cliquez sur **Enregistrer**. Les paramètres doivent se présenter comme suit :  
![][13]  
13. Cliquez sur pour fermer la lame **déclencheurs et actions** , puis cliquez sur pour fermer la lame de **paramètres** .
14. Dans la liste de **tous les s’exécute** sous **opérations**, cliquez sur l’élément premiers dans la liste (dernière exécution).
15. Dans la lame **application logique exécutée** , cliquez sur l’élément **d’ACTION** .
16. La lame de **cette action logique app** , cliquez sur le **Lien de sorties**. Les sorties doivent se présenter comme suit :  
![][14]

**Remarque :** Concepteur d’application logique tronque le nom de la table. Par exemple, l’opération **conditionnelle supprimer NEWORDERS** est tronquée **conditionnel**supprimer N.


> [AZURE.TIP] Utilisez les instructions SQL suivantes pour créer la table et des procédures stockées. 

Vous pouvez créer l’exemple de table NEWORDERS à l’aide des instructions DDL SQL de DB2 suivantes :
 
    CREATE TABLE ORDERS (  
        ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
        CUSTID INT NOT NULL ,  
        EMPID INT NOT NULL DEFAULT 10000 ,  
        ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
        REQDATE DATE DEFAULT CURRENT DATE ,  
        SHIPDATE DATE ,  
        SHIPID INT NOT NULL DEFAULT 10000,  
        FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
        SHIPNAME CHAR (40) NOT NULL ,  
        SHIPADDR CHAR (60) NOT NULL ,  
        SHIPCITY CHAR (20) NOT NULL ,  
        SHIPREG CHAR (15) NOT NULL ,  
        SHIPZIP CHAR (10) NOT NULL ,  
        SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
        PRIMARY KEY(ORDID)  
        )  
 
    CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



Vous pouvez créer l’exemple de procédure SPOERID stockée à l’aide de l’instruction DDL de DB2 suivante :
 
    CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
        DYNAMIC RESULT SETS 1  
    P1: BEGIN  
        DECLARE CURSOR1 CURSOR WITH RETURN FOR  
            SELECT * FROM NWIND.NEWORDERS  
                WHERE ORDID = ORDERID;  
        OPEN CURSOR1;  
    END P1  
    ') 


## <a name="hybrid-configuration-optional"></a>Configuration hybride (facultative)

> [AZURE.NOTE] Cette étape est requise uniquement si vous utilisez DB2 le connecteur locaux derrière votre pare-feu.

Service d’application utilise le Gestionnaire de Configuration hybride pour se connecter en toute sécurité sur votre système local. Si le connecteur utilise un serveur local IBM DB2 pour Windows, le Gestionnaire de connexion hybride est requis.

Consultez [l’aide du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l’ajouter à un workflow d’entreprise à l’aide d’une application de logique. Consultez [Quelles sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

Créer des applications API à l’aide des API de reste. Consultez les [connecteurs et les applications de référence sur l’API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les performances statistiques et contrôle de sécurité sur le connecteur. Reportez-vous à la section [gérer et surveiller les connecteurs et les applications d’API intégrées](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

