<properties
    pageTitle="Ajouter le connecteur DB2 dans vos applications de logique | Microsoft Azure"
    description="Vue d’ensemble du connecteur DB2 avec des paramètres de l’API REST"
    services=""
    documentationCenter="" 
    authors="gplarsen"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="09/26/2016"
   ms.author="plarsen"/>


# <a name="get-started-with-the-db2-connector"></a>Mise en route avec le connecteur de DB2
Le connecteur Microsoft pour DB2 connecte à des applications de logique aux ressources stockées dans une base de données IBM DB2. Ce connecteur inclut un client Microsoft pour communiquer avec les ordinateurs de serveur DB2 distants sur un réseau TCP/IP. Cela inclut le nuage bases de données, dashDB de Bluemix d’IBM ou IBM DB2 pour Windows en cours d’exécution en matière de virtualisation Azure et des bases de données à l’aide de la passerelle de données sur site local. Voir la [liste de prise en charge](connectors-create-api-db2.md#supported-db2-platforms-and-versions) de plates-formes d’IBM DB2 et versions (dans cette rubrique).

>[AZURE.NOTE] Cette version de l’article s’applique à la disponibilité des applications de logique (GA). 

Le connecteur de DB2 prend en charge les opérations de base de données suivantes :

- Liste des tables de base de données
- Lecture d’une ligne à l’aide de SELECT
- Lire toutes les lignes à l’aide de SELECT
- Ajouter une ligne à l’aide de INSERT
- Modifier une ligne à l’aide de la mise à jour
- Supprimer une ligne à l’aide de DELETE

Cette rubrique vous indique comment utiliser le connecteur dans une logique d’application pour les opérations de base de données de processus.

Pour en savoir plus sur les applications de logique, reportez-vous à la section [Création d’une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="available-actions"></a>Actions disponibles
Le connecteur de DB2 prend en charge les actions d’application logique suivant :

- GetTables
- GetRow
- GetRows
- InsertRow
- UpdateRow
- DeleteRow


## <a name="list-tables"></a>Liste des tables
Création d’une application de logique pour toute opération se compose de nombreuses étapes effectuées via le portail Microsoft Azure.

Dans la logique d’application, vous pouvez ajouter une action à la liste des tables dans une base de données DB2. L’action indique le connecteur pour traiter une instruction schema DB2, tel que `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Créer une application de logique
1.  Dans **Azure démarrer la carte**, sélectionnez **+** (signe plus), **Web + Mobile**, puis **Logique d’application**.
2.  Entrez le **nom**, comme `Db2getTables`, **abonnement**, **groupe de ressources**, **l’emplacement**et **Plan de Service d’application**. Sélectionnez **Ajouter au tableau de bord**et sélectionnez **créer**.

### <a name="add-a-trigger-and-action"></a>Ajout d’un déclencheur et une action
1.  Dans le **Concepteur d’applications logique**, sélectionnez **LogicApp vide** dans la liste **modèles** .
2.  Dans la liste de **déclencheurs** , sélectionnez **récurrence**. 
3.  Dans le déclencheur de la **périodicité** , sélectionnez **Modifier**, sélectionnez la **fréquence de** liste déroulante pour sélectionner le **jour**et puis définissez l' **intervalle** à tapez **7**.  
4.  Activez la case **+ nouvelle étape** et sélectionnez **Ajouter une action**.
5.  Dans la liste **actions** , tapez `db2` dans la **recherche pour plus d’actions** de la zone d’édition et puis sélectionnez **DB2 - obtenir des tables (aperçu)**.

    ![](./media/connectors-create-api-db2/Db2connectorActions.png)  

6.  Dans le volet de configuration **DB2 - obtenir des tables** , sélectionnez la **case à cocher** pour activer la **connexion via une passerelle de données sur site**. Remarquez que les paramètres de nuage local.
    - Tapez la valeur pour le **serveur**, sous la forme d’un numéro de port de deux-points adresse ou alias. Par exemple, tapez `ibmserver01:50000`.
    - Tapez la valeur de **base de données**. Par exemple, tapez `nwind`.
    - Sélectionner une valeur pour **l’authentification**. Par exemple, sélectionnez de **base**.
    - Tapez la valeur pour le **nom d’utilisateur**. Par exemple, tapez `db2admin`.
    - Tapez la valeur pour le **mot de passe**. Par exemple, tapez `Password1`.
    - Sélectionner une valeur pour la **passerelle**. Par exemple, sélectionnez **datagateway01**.
7. Sélectionnez **créer**, puis cliquez sur **Enregistrer**. 

    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

8.  Dans la lame de **Db2getTables** , dans la liste de **tous les s’exécute** sous **Résumé**, sélectionnez l’élément premiers dans la liste (dernière exécution).
9.  Dans la lame **application logique fonctionnent** , sélectionnez **Détails de l’exécution**. Dans la liste **Action** , sélectionnez **Get_tables**. Voir la valeur de l' **état**, qui doit être **terminé**. Cliquez sur le **lien des entrées** pour afficher les entrées. Sélectionnez le **lien de sorties**et d’afficher les sorties ; qui doit inclure une liste de tables.

    ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Créez les connexions
Ce connecteur prend en charge les connexions à des bases de données hébergées sur site et dans le cloud à l’aide des propriétés de connexion suivantes. 

Propriété | Description
--- | ---
serveur | Obligatoire. Accepte une valeur de chaîne qui représente une adresse TCP/IP ou un alias au format IPv4 ou IPv6, suivi (délimité par des deux-points) par un numéro de port TCP/IP. 
base de données | Obligatoire. Accepte une valeur de chaîne représentant un nom de base de données relationnelle (RDBNAM) de DRDA. DB2 pour z/OS accepte une chaîne de 16 octets (base de données est appelée un IBM DB2 pour emplacement de z/OS). DB2 pour i5/OS accepte une chaîne de 18 octets (base de données est appelé un IBM DB2 pour i relationnelle base de données). DB2 pour LUW accepte une chaîne de 8 octets.
authentification | Facultatif. Accepte une valeur d’élément de liste, Basic ou Windows (kerberos). 
nom d’utilisateur | Obligatoire. Accepte une valeur de chaîne. DB2 pour z/OS accepte une chaîne de 8 octets. DB2 pour i accepte une chaîne de 10 octets. DB2 pour Linux ou UNIX accepte une chaîne de 8 octets. DB2 pour Windows accepte une chaîne de 30 octets.
mot de passe | Obligatoire. Accepte une valeur de chaîne.
passerelle | Obligatoire. Accepte une valeur d’élément de liste, représentant la passerelle de données local définie pour les applications de la logique au sein du groupe de stockage.  

## <a name="create-the-on-premises-gateway-connection"></a>Créer des connexion passerelle sur site
Ce connecteur peut accéder à une base de données de DB2 sur site à l’aide de la passerelle locale. Consultez les rubriques de passerelle pour plus d’informations. 

1. Dans le volet configuration de **passerelles** , activez la **case à cocher** pour activer la **connexion via une passerelle**. Remarquez que les paramètres de nuage local.
2. Tapez la valeur pour le **serveur**, sous la forme d’un numéro de port de deux-points adresse ou alias. Par exemple, tapez `ibmserver01:50000`.
3. Tapez la valeur de **base de données**. Par exemple, tapez `nwind`.
4. Sélectionner une valeur pour **l’authentification**. Par exemple, sélectionnez de **base**.
5. Tapez la valeur pour le **nom d’utilisateur**. Par exemple, tapez `db2admin`.
6. Tapez la valeur pour le **mot de passe**. Par exemple, tapez `Password1`.
7. Sélectionner une valeur pour la **passerelle**. Par exemple, sélectionnez **datagateway01**.
8. Sélectionnez **créer** pour continuer. 

    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Créer la connexion de nuage
Ce connecteur peut accéder à une base de données DB2 de nuage. 

1. Dans le volet configuration de **passerelles** , laissez la **case à cocher** désactivée (sur) de **se connecter via la passerelle**. 
2. Tapez la valeur pour le **nom de la connexion**. Par exemple, tapez `hisdemo2`.
3. Tapez la valeur pour le **nom du serveur DB2**, sous la forme d’un numéro de port de deux-points adresse ou alias. Par exemple, tapez `hisdemo2.cloudapp.net:50000`.
3. Tapez la valeur pour le **nom de base de données DB2**. Par exemple, tapez `nwind`.
4. Tapez la valeur pour le **nom d’utilisateur**. Par exemple, tapez `db2admin`.
5. Tapez la valeur pour le **mot de passe**. Par exemple, tapez `Password1`.
6. Sélectionnez **créer** pour continuer. 

    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Extraire toutes les lignes à l’aide de SELECT
Vous pouvez définir une action d’application logique pour extraire toutes les lignes d’une table DB2. Cela indique le connecteur pour traiter une instruction SELECT de DB2, tel que `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Créer une application de logique
1.  Dans **Azure démarrer la carte**, sélectionnez **+** (signe plus), **Web + Mobile**, puis **Logique d’application**.
2.  Entrez le **nom**, comme `Db2getRows`, **abonnement**, **groupe de ressources**, **l’emplacement**et **Plan de Service d’application**. Sélectionnez **Ajouter au tableau de bord**et sélectionnez **créer**.

### <a name="add-a-trigger-and-action"></a>Ajout d’un déclencheur et une action
1.  Dans le **Concepteur d’applications logique**, sélectionnez **LogicApp vide** dans la liste **modèles** .
2.  Dans la liste de **déclencheurs** , sélectionnez **récurrence**. 
3.  Dans le déclencheur de la **périodicité** , sélectionnez **Modifier** **fréquence** vers le bas pour sélectionner le **jour**et sélectionnez ensuite **intervalle** , tapez **7**. 
4.  Activez la case **+ nouvelle étape** et sélectionnez **Ajouter une action**.
5.  Dans la liste **actions** , tapez `db2` dans la **recherche pour plus d’actions** de la zone d’édition et puis sélectionnez **DB2 - obtenir des lignes (aperçu)**.
6. Dans l’action **obtenir des lignes (aperçu)** , sélectionnez **Modifier la connexion**.
7. Dans le volet de configuration de **connexions** , sélectionnez **Créer nouveau**. 

    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
  
8. Dans le volet configuration de **passerelles** , laissez la **case à cocher** désactivée (sur) de **se connecter via la passerelle**.
    - Tapez la valeur pour le **nom de la connexion**. Par exemple, tapez `HISDEMO2`.
    - Tapez la valeur pour le **nom du serveur DB2**, sous la forme d’un numéro de port de deux-points adresse ou alias. Par exemple, tapez `HISDEMO2.cloudapp.net:50000`.
    - Tapez la valeur pour le **nom de base de données DB2**. Par exemple, tapez `NWIND`.
    - Tapez la valeur pour le **nom d’utilisateur**. Par exemple, tapez `db2admin`.
    - Tapez la valeur pour le **mot de passe**. Par exemple, tapez `Password1`.
9. Sélectionnez **créer** pour continuer.

    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

10. Dans la liste **nom de la Table** , sélectionnez **flèche vers le bas**et sélectionnez **la zone**.
11. Si vous le souhaitez, sélectionnez **Afficher les options avancées** pour spécifier les options de requête.
12. Cliquez sur **Enregistrer**. 

    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)

13. Dans la lame de **Db2getRows** , dans la liste de **tous les s’exécute** sous **Résumé**, sélectionnez l’élément premiers dans la liste (dernière exécution).
14. Dans la lame **application logique fonctionnent** , sélectionnez **Détails de l’exécution**. Dans la liste **Action** , sélectionnez **Get_rows**. Voir la valeur de l' **état**, qui doit être **terminé**. Cliquez sur le **lien des entrées** pour afficher les entrées. Sélectionnez le **lien de sorties**et d’afficher les sorties ; qui doit inclure une liste de lignes.

    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Ajouter une ligne à l’aide de INSERT
Vous pouvez définir une action d’application logique pour ajouter une ligne d’une table DB2. Cette action indique à traiter une instruction INSERT de DB2, tels que le connecteur `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Créer une application de logique
1.  Dans **Azure démarrer la carte**, sélectionnez **+** (signe plus), **Web + Mobile**, puis **Logique d’application**.
2.  Entrez le **nom**, comme `Db2insertRow`, **abonnement**, **groupe de ressources**, **l’emplacement**et **Plan de Service d’application**. Sélectionnez **Ajouter au tableau de bord**et sélectionnez **créer**.

### <a name="add-a-trigger-and-action"></a>Ajout d’un déclencheur et une action
1.  Dans le **Concepteur d’applications logique**, sélectionnez **LogicApp vide** dans la liste **modèles** .
2.  Dans la liste de **déclencheurs** , sélectionnez **récurrence**. 
3.  Dans le déclencheur de la **périodicité** , sélectionnez **Modifier** **fréquence** vers le bas pour sélectionner le **jour**et sélectionnez ensuite **intervalle** , tapez **7**. 
4.  Activez la case **+ nouvelle étape** et sélectionnez **Ajouter une action**.
5.  Dans la liste **actions** , tapez **db2** dans la zone d’édition **Rechercher plus d’actions** et puis sélectionnez **DB2 - insérer une ligne (aperçu)**.
6. Dans l’action **obtenir des lignes (aperçu)** , sélectionnez **Modifier la connexion**. 
7. Dans le volet de configuration de **connexions** , sélectionnez une connexion. Par exemple, sélectionnez **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **nom de la Table** , sélectionnez **flèche vers le bas**et sélectionnez **la zone**.
9. Entrez des valeurs pour toutes les colonnes (voir l’astérisque rouge). Par exemple, tapez `99999` pour **AREAID**, tapez `Area 99999`et le type `102` pour **REGIONID**. 
10. Cliquez sur **Enregistrer**.

    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
 
11. Dans la lame de **Db2insertRow** , dans la liste de **tous les s’exécute** sous **Résumé**, sélectionnez l’élément premiers dans la liste (dernière exécution).
12. Dans la lame **application logique fonctionnent** , sélectionnez **Détails de l’exécution**. Dans la liste **Action** , sélectionnez **Get_rows**. Voir la valeur de l' **état**, qui doit être **terminé**. Cliquez sur le **lien des entrées** pour afficher les entrées. Sélectionnez le **lien de sorties**et d’afficher les sorties ; qui doit inclure la nouvelle ligne.

    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Extraire une ligne à l’aide de SELECT
Vous pouvez définir une action d’application logique pour extraire une ligne d’une table DB2. Cette action indique à traiter une instruction DB2, sélectionnez où, tels que le connecteur `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Créer une application de logique
1.  Dans **Azure démarrer la carte**, sélectionnez **+** (signe plus), **Web + Mobile**, puis **Logique d’application**.
2.  Entrez le **nom** (par exemple, «**Db2getRow**»), **abonnement**, **groupe de ressources**, **emplacement**et le **Plan de Service d’application**. Sélectionnez **Ajouter au tableau de bord**et sélectionnez **créer**.

### <a name="add-a-trigger-and-action"></a>Ajout d’un déclencheur et une action
1.  Dans le **Concepteur d’applications logique**, sélectionnez **LogicApp vide** dans la liste **modèles** . 
2.  Dans la liste de **déclencheurs** , sélectionnez **récurrence**. 
3.  Dans le déclencheur de la **périodicité** , sélectionnez **Modifier** **fréquence** vers le bas pour sélectionner le **jour**et sélectionnez ensuite **intervalle** , tapez **7**. 
4.  Activez la case **+ nouvelle étape** et sélectionnez **Ajouter une action**.
5.  Dans la liste **actions** , tapez **db2** dans la zone d’édition **Rechercher plus d’actions** et puis sélectionnez **DB2 - obtenir des lignes (aperçu)**.
6. Dans l’action **obtenir des lignes (aperçu)** , sélectionnez **Modifier la connexion**. 
7. Dans le volet de configuration de **connexions** , sélectionnez une connexion existante. Par exemple, sélectionnez **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **nom de la Table** , sélectionnez **flèche vers le bas**et sélectionnez **la zone**.
9. Entrez des valeurs pour toutes les colonnes (voir l’astérisque rouge). Par exemple, tapez `99999` pour **AREAID**. 
10. Si vous le souhaitez, sélectionnez **Afficher les options avancées** pour spécifier les options de requête.
11. Cliquez sur **Enregistrer**. 

    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)

12. Dans la lame de **Db2getRow** , dans la liste de **tous les s’exécute** sous **Résumé**, sélectionnez l’élément premiers dans la liste (dernière exécution).
13. Dans la lame **application logique fonctionnent** , sélectionnez **Détails de l’exécution**. Dans la liste **Action** , sélectionnez **Get_rows**. Voir la valeur de l' **état**, qui doit être **terminé**. Cliquez sur le **lien des entrées** pour afficher les entrées. Sélectionnez le **lien de sorties**et d’afficher les sorties ; qui doit inclure la ligne.

    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Modifier une ligne à l’aide de la mise à jour
Vous pouvez définir une action d’application logique pour modifier une ligne d’une table DB2. Cette action indique à traiter une instruction de mise à jour de DB2, tels que le connecteur `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Créer une application de logique
1.  Dans **Azure démarrer la carte**, sélectionnez **+** (signe plus), **Web + Mobile**, puis **Logique d’application**.
2.  Entrez le **nom**, comme `Db2updateRow`, **abonnement**, **groupe de ressources**, **l’emplacement**et **Plan de Service d’application**. Sélectionnez **Ajouter au tableau de bord**et sélectionnez **créer**.

### <a name="add-a-trigger-and-action"></a>Ajout d’un déclencheur et une action
1.  Dans le **Concepteur d’applications logique**, sélectionnez **LogicApp vide** dans la liste **modèles** .
2.  Dans la liste de **déclencheurs** , sélectionnez **récurrence**. 
3.  Dans le déclencheur de la **périodicité** , sélectionnez **Modifier** **fréquence** vers le bas pour sélectionner le **jour**et sélectionnez ensuite **intervalle** , tapez **7**. 
4.  Activez la case **+ nouvelle étape** et sélectionnez **Ajouter une action**.
5.  Dans la liste **actions** , tapez **db2** dans la zone d’édition **Rechercher plus d’actions** et puis sélectionnez **DB2 - ligne de mise à jour (aperçu)**.
6. Dans l’action **obtenir des lignes (aperçu)** , sélectionnez **Modifier la connexion**. 
7. Dans le volet de configuration de **connexions** , permet de sélectionner une connexion existante. Par exemple, sélectionnez **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **nom de la Table** , sélectionnez **flèche vers le bas**et sélectionnez **la zone**.
9. Entrez des valeurs pour toutes les colonnes (voir l’astérisque rouge). Par exemple, tapez `99999` pour **AREAID**, tapez `Updated 99999`et le type `102` pour **REGIONID**. 
10. Cliquez sur **Enregistrer**. 

    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)

11. Dans la lame de **Db2updateRow** , dans la liste de **tous les s’exécute** sous **Résumé**, sélectionnez l’élément premiers dans la liste (dernière exécution).
12. Dans la lame **application logique fonctionnent** , sélectionnez **Détails de l’exécution**. Dans la liste **Action** , sélectionnez **Get_rows**. Voir la valeur de l' **état**, qui doit être **terminé**. Cliquez sur le **lien des entrées** pour afficher les entrées. Sélectionnez le **lien de sorties**et d’afficher les sorties ; qui doit inclure la nouvelle ligne.

    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Supprimer une ligne à l’aide de DELETE
Vous pouvez définir une action d’application logique pour supprimer une ligne d’une table DB2. Cette action indique à traiter une instruction DELETE de DB2, tels que le connecteur `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Créer une application de logique
1.  Dans **Azure démarrer la carte**, sélectionnez **+** (signe plus), **Web + Mobile**, puis **Logique d’application**.
2.  Entrez le **nom**, comme `Db2deleteRow`, **abonnement**, **groupe de ressources**, **l’emplacement**et **Plan de Service d’application**. Sélectionnez **Ajouter au tableau de bord**et sélectionnez **créer**.

### <a name="add-a-trigger-and-action"></a>Ajout d’un déclencheur et une action
1.  Dans le **Concepteur d’applications logique**, sélectionnez **LogicApp vide** dans la liste **modèles** . 
2.  Dans la liste de **déclencheurs** , sélectionnez **récurrence**. 
3.  Dans le déclencheur de la **périodicité** , sélectionnez **Modifier** **fréquence** vers le bas pour sélectionner le **jour**et sélectionnez ensuite **intervalle** , tapez **7**. 
4.  Activez la case **+ nouvelle étape** et sélectionnez **Ajouter une action**.
5.  Dans la liste **actions** , sélectionnez **db2** dans la zone d’édition **Rechercher plus d’actions** et puis sélectionnez **DB2 - Supprimer ligne (aperçu)**.
6. Dans l’action **obtenir des lignes (aperçu)** , sélectionnez **Modifier la connexion**. 
7. Dans le volet de configuration de **connexions** , sélectionnez une connexion existante. Par exemple, sélectionnez **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **nom de la Table** , sélectionnez **flèche vers le bas**et sélectionnez **la zone**.
9. Entrez des valeurs pour toutes les colonnes (voir l’astérisque rouge). Par exemple, tapez `99999` pour **AREAID**. 
10. Cliquez sur **Enregistrer**. 

    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)

11. Dans la lame de **Db2deleteRow** , dans la liste de **tous les s’exécute** sous **Résumé**, sélectionnez l’élément premiers dans la liste (dernière exécution).
12. Dans la lame **application logique fonctionnent** , sélectionnez **Détails de l’exécution**. Dans la liste **Action** , sélectionnez **Get_rows**. Voir la valeur de l' **état**, qui doit être **terminé**. Cliquez sur le **lien des entrées** pour afficher les entrées. Sélectionnez le **lien de sorties**et d’afficher les sorties ; qui doit inclure la ligne supprimée.

    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="technical-details"></a>Détails techniques

## <a name="actions"></a>Actions
Une action est une opération effectuée par le flux de travail défini dans une logique d’application. Le connecteur de base de données DB2 inclut les actions suivantes. 

|Action|Description|
|--- | ---|
|[GetRow](connectors-create-api-db2.md#get-row)|Récupère une ligne unique d’une table DB2|
|[GetRows](connectors-create-api-db2.md#get-rows)|Extrait des lignes d’une table DB2|
|[InsertRow](connectors-create-api-db2.md#insert-row)|Insère une nouvelle ligne dans une table DB2|
|[DeleteRow](connectors-create-api-db2.md#delete-row)|Supprime une ligne d’une table DB2|
|[GetTables](connectors-create-api-db2.md#get-tables)|Récupère des tables d’une base de données DB2|
|[UpdateRow](connectors-create-api-db2.md#update-row)|Mises à jour d’une ligne existante dans une table DB2|

### <a name="action-details"></a>Détails de l’action

Dans cette section, voir les détails relatifs à chaque action, y compris les propriétés d’entrée requises ou facultatives et toute associés au connecteur de sortie correspondante.

#### <a name="get-row"></a>Obtenir la ligne 
Récupère une seule ligne d’une table DB2.  

| Nom de la propriété| Nom complet |Description|
| ---|---|---|
|table * | Nom de la table |Nom de la table DB2|
|ID * | Code de ligne |Identificateur unique de la ligne à récupérer|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Élément

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|chaîne|


#### <a name="get-rows"></a>Obtenir des lignes 
Extrait des lignes d’une table DB2.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|table *|Nom de la table|Nom de la table DB2|
|$skip|Skip nombre|Nombre d’entrées à ignorer (par défaut = 0)|
|$top|Nombre de Get maximum|Nombre maximal d’entrées à récupérer (par défaut = 256)|
|$filter|Requête de filtre|Une requête de filtre ODATA pour limiter le nombre d’entrées|
|$orderby|Trier par|Une requête d’orderBy ODATA pour spécifier l’ordre des entrées|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Liste

| Nom de la propriété | Type de données |
|---|---|
|valeur|tableau|


#### <a name="insert-row"></a>Insérer une ligne 
Insère une nouvelle ligne dans une table DB2.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|table *|Nom de la table|Nom de la table DB2|
|article *|Ligne|Ligne à insérer dans la table spécifiée dans DB2|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Élément

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|chaîne|


#### <a name="delete-row"></a>Supprimer ligne 
Supprime une ligne d’une table DB2.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|table *|Nom de la table|Nom de la table DB2|
|ID *|Code de ligne|Identificateur unique de la ligne à supprimer|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Aucun.

#### <a name="get-tables"></a>Obtenir des tables 
Extrait des tables d’une base de données DB2.  

Il n’y a pas de paramètres pour cet appel. 

##### <a name="output-details"></a>Détails de sortie 
TablesList

| Nom de la propriété | Type de données |
|---|---|
|valeur|tableau|

#### <a name="update-row"></a>Mise à jour de ligne 
Met à jour une ligne existante dans une table DB2.  

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|table *|Nom de la table|Nom de la table DB2|
|ID *|Code de ligne|Identificateur unique de la ligne à mettre à jour|
|article *|Ligne|Ligne avec les valeurs mises à jour|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie  
Élément

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|chaîne|


### <a name="http-responses"></a>Réponses HTTP

Lorsque vous appelez les différentes actions, vous pouvez obtenir certaines réponses. Le tableau suivant présente les réponses et leurs descriptions :  

|Nom|Description|
|---|---|
|200|Bien|
|202|Accepté|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="supported-db2-platforms-and-versions"></a>Prise en charge des plates-formes de DB2 et des versions
Ce connecteur prend en charge les plates-formes suivantes de IBM DB2 et versions, ainsi que les produits compatibles IBM DB2 (par exemple, IBM Bluemix dashDB) prenant en charge distribués relationnel de base de données Architecture (DRDA) SQL Access Manager (SQLAM) version 10 et 11 :

- IBM DB2 pour z/OS 11.1
- IBM DB2 pour z/OS 10.1
- IBM DB2 pour i 7.3
- IBM DB2 pour i 7.2
- IBM DB2 pour i 7.1
- IBM DB2 LUW 11
- IBM DB2 pour LUW 10.5


## <a name="next-steps"></a>Étapes suivantes

[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Explorez les autres connecteurs disponibles dans les applications de logique à notre [liste d’API](apis-list.md).

