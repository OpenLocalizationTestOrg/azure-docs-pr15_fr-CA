<properties
    pageTitle="Mise en route de la synchronisation de données de bases de données SQL"
    description="Ce didacticiel vous aide à mettre en route la synchronisation de données SQL Azure (aperçu)."
    services="sql-database"
    documentationCenter=""
    authors="jennieHubbard"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/11/2016"
    ms.author="jhubbard"/>


#<a name="getting-started-with-azure-sql-data-sync-preview"></a>Mise en route de la synchronisation de données SQL Azure (aperçu)
Dans ce didacticiel, vous découvrez les principes fondamentaux de la synchronisation de données SQL Azure via le portail classique d’Azure.

Ce didacticiel suppose une connaissance préalable minimale avec SQL Server et la base de données de SQL Azure. Dans ce didacticiel, vous créez un groupe de synchronisation hybride (SQL Server et les instances de base de données SQL), entièrement configuré et synchronisation de la planification définie.

> [AZURE.NOTE] La documentation technique complète pour la synchronisation de données SQL Azure, qui se trouvaient auparavant sur MSDN, est disponible sous la forme d’un fichier .pdf. Téléchargez-le [ici](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

## <a name="step-1-connect-to-the-azure-sql-database"></a>Étape 1 : Se connecter à la base de données SQL Azure

1. Connectez-vous au [portail classique](http://manage.windowsazure.com).

2. Dans le volet gauche, cliquez sur **Bases de données SQL** .

3. Cliquez sur **synchronisation** en bas de la page. Lorsque vous cliquez sur Synchroniser, une liste s’affiche, indiquant les éléments que vous pouvez ajouter - **Nouveau groupe de synchronisation** et le **Nouvel Agent de synchronisation**.

4. Pour lancer l’Assistant nouvel Agent de synchronisation de données SQL, cliquez sur **Nouvel Agent de synchronisation**.

5. Si vous n’avez pas ajouté un agent avant, **cliquez sur Télécharger ici**.

    ![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## <a name="step-2-add-a-client-agent"></a>Étape 2 : Ajouter un Agent de Client
Cette étape est requise uniquement si vous vous apprêtez à une base de données de SQL Server sur site inclus dans votre groupe de synchronisation. Si votre groupe de synchronisation comporte uniquement des instances de base de données SQL, passez à l’étape 4.

<a id="InstallRequiredSoftware"></a>
### <a name="step-2a-install-the-required-software"></a>Étape 2 a : installer le logiciel requis
Assurez-vous que vous disposez de suivantes installée sur l’ordinateur où vous installez l’Agent du Client.

- **.NET Framework 4.0**

 Installez.NET Framework 4.0 à partir [d’ici](http://go.microsoft.com/fwlink/?linkid=205836).

- **Microsoft SQL Server 2008 R2 SP1 Types CLR du système (x86)**

 Installer les Microsoft SQL Server 2008 R2 SP1 Types CLR du système (x86) à partir [d’ici](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Microsoft SQL Server 2008 R2 SP1 partagé des objets de gestion (x86)**

 Installer Microsoft SQL Server 2008 R2 SP1 partagé aux objets de gestion (x86) à partir [d’ici](http://www.microsoft.com/download/en/details.aspx?id=26728)



<a id="InstallClient"></a>
### <a name="step-2b-install-a-new-client-agent"></a>Étape 2 b : installer un nouvel Agent de Client

Suivez les instructions dans [l’installation d’un Agent de Client (SQL Data Sync)](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf) pour installer l’agent.



<a id="RegisterSSDb"></a>
### <a name="step-2c-finish-the-new-sql-data-sync-agent-wizard"></a>Étape 2c : fin de l’Assistant nouvel Agent de synchronisation de données SQL

1.  Revenir à l’Assistant nouvel Agent de synchronisation de données SQL.
2.  Donner un nom significatif à l’agent.
3.  Dans la liste déroulante, sélectionnez la **région** (centre de données) pour héberger cet agent.
4.  Dans la liste déroulante, sélectionnez l' **abonnement** pour héberger cet agent.
5.  Cliquez sur la flèche droite.



## <a name="step-3-register-a-sql-server-database-with-the-client-agent"></a>Étape 3 : Enregistrer une base de données SQL Server avec l’Agent du Client

Après avoir installé l’Agent du Client, enregistrez chaque base de données locale SQL Server que vous souhaitez inclure dans un groupe de synchronisation avec l’agent.
Pour enregistrer une base de données avec l’agent, suivez les instructions à [Enregistrer une base de données de SQL Server avec un Agent Client](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).



## <a name="step-4-create-a-sync-group"></a>Étape 4 : Créer un groupe de synchronisation


<a id="StartNewSGWizard"></a>
### <a name="step-4a-start-the-new-sync-group-wizard"></a>Étape 4 : démarrer l’Assistant Nouveau groupe de synchronisation

1.  Retourner au [portail classique](http://manage.windowsazure.com).
2.  Cliquez sur **bases de données SQL**.
3.  Cliquez sur **Ajouter une synchronisation** en bas de la page, puis sélectionnez Nouveau groupe de synchronisation à partir du bac.

    ![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### <a name="step-4b-enter-the-basic-settings"></a>Étape 4 b : permet d’entrer les paramètres de base


1.  Entrez un nom significatif pour le groupe de synchronisation.
2.  Dans la liste déroulante, sélectionnez la **région** (centre de données) pour héberger ce groupe de synchronisation.
3. Cliquez sur la flèche droite.

    ![Sa stratégie partenaires3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### <a name="step-4c-define-the-sync-hub"></a>Étape c 4 : définir le concentrateur de synchronisation

1. Dans la liste déroulante, sélectionnez l’instance de base de données SQL comme le concentrateur de groupe de synchronisation.
2. Entrez les informations d’identification pour cette instance de base de données SQL - **Nom d’utilisateur du concentrateur** et de **Mot de passe de concentrateur**.
3. Attendre la synchronisation de données SQL vérifier le nom d’utilisateur et le mot de passe. Vous verrez une coche verte s’affiche à droite du mot de passe lorsque les informations d’identification sont confirmées.
4. Dans la liste déroulante, sélectionnez la stratégie de **Résolution de conflit** .

 Le **Concentrateur Wins** - toute modification écrite à l’écriture de base de données de concentrateur pour les bases de données de référence, écrasement des modifications dans le même enregistrement de base de données de référence. Du point de vue fonctionnel, cela signifie que la première modification écrite sur le concentrateur se propage aux autres bases de données.


 **Client Wins** - modifications écrites au concentrateur sont écrasées par les modifications de bases de données de référence. Du point de vue fonctionnel, cela signifie que la dernière modification écrite sur le concentrateur est celle conservée et diffusées aux autres bases de données.

5.  Cliquez sur la flèche droite.

    ![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### <a name="step-4d-add-a-reference-database"></a>Étape d 4 : ajouter une base de données de référence


Répétez cette étape pour chaque base de données supplémentaire que vous souhaitez ajouter au groupe de synchronisation.

1. Dans la liste déroulante, sélectionnez la base de données à ajouter.

    Bases de données dans la liste déroulante incluent les deux bases de données SQL Server qui ont été enregistrés avec l’agent et les instances de base de données SQL.
2.  Entrez les informations d’identification pour cette base de données - **nom d’utilisateur** et le **mot de passe**.
3.  Dans la liste déroulante, sélectionnez la **DIRECTION de la synchronisation** pour cette base de données.

    **Bidirectionnelle** - modifications dans la base de données de référence sont écrites dans la base de données du concentrateur, et les modifications apportées à la base de données de hub sont écrites dans la base de données de référence.

    **Synchronisation du concentrateur** - la base de données reçoit des mises à jour à partir du concentrateur. Il n’envoie pas de modifications au concentrateur.

    **Synchronisation au concentrateur** - la base de données envoie les mises à jour sur le concentrateur. Modifications dans le concentrateur ne sont pas écrites dans cette base de données.

4.  Pour terminer la création du groupe de synchronisation, cliquez sur la case à cocher dans le coin inférieur droit de l’Assistant. Attendez que la synchronisation de données SQL confirmer les informations d’identification. Une coche verte indique que les informations d’identification sont confirmées.

5.  Cliquez sur la case à cocher une deuxième fois. Ceci vous renvoie à la page de **synchronisation** sous bases de données SQL. Ce groupe de synchronisation apparaît maintenant avec d’autres groupes de synchronisation et les agents.

    ![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## <a name="step-5-define-the-data-to-sync"></a>Étape 5 : Définir les données à synchroniser

Synchronisation de données SQL Azure vous permet de sélectionner les tables et les colonnes à synchroniser. Si vous souhaitez également filtrer une colonne afin que seules les lignes avec des valeurs spécifiques (telles que l’âge > = 65 ans) synchroniser, utiliser le portail de la synchronisation de données SQL à Azure et la documentation à sélectionner les Tables, colonnes et lignes à synchroniser pour définir les données à synchroniser.

1.  Retourner au [portail classique](http://manage.windowsazure.com).
2.  Cliquez sur **bases de données SQL**.
3.  Cliquez sur l’onglet **synchroniser** .
4.  Cliquez sur le nom de ce groupe de synchronisation.
5.  Cliquez sur l’onglet **Règles de synchronisation** .
6.  Sélectionnez la base de données à laquelle que vous souhaitez fournir le schéma de groupe de synchronisation.
7.  Cliquez sur la flèche droite.
8.  Cliquez sur **Actualiser le schéma**.
9.  Pour chaque table de la base de données, sélectionnez les colonnes à inclure dans la synchronisation.
    - Colonnes avec des types de données non pris en charge ne peuvent pas être sélectionnés.
    - Si aucune colonne dans une table n’est sélectionnés, la table n’est pas incluse dans le groupe de synchronisation.
    - Pour sélectionner/désélectionner toutes les tables, cliquez sur Sélectionner en bas de l’écran.
10. Cliquez sur **Enregistrer**, puis attendez que le groupe de synchronisation à la fin de mise en service.
11. Pour revenir à la page de lancement de la synchronisation de données, cliquez sur la flèche arrière dans le coin supérieur gauche de l’écran (au-dessus du synchronisation nom du groupe).

    ![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## <a name="step-6-configure-your-sync-group"></a>Étape 6 : Configuration de votre groupe de synchronisation

Vous pouvez toujours synchroniser un groupe de synchronisation en cliquant sur synchroniser au bas de la page de lancement de la synchronisation de données.
Pour synchroniser sur une planification, vous configurez le groupe de synchronisation.

1.  Retourner au [portail classique](http://manage.windowsazure.com).
2.  Cliquez sur **bases de données SQL**.
3.  Cliquez sur l’onglet **synchroniser** .
4.  Cliquez sur le nom de ce groupe de synchronisation.
5.  Cliquez sur l’onglet **configurer** .
6.  **SYNCHRONISATION AUTOMATIQUE**
    - Pour configurer le groupe de synchronisation à synchroniser sur une fréquence définie, cliquez **sur**. Vous pouvez toujours synchroniser à la demande en cliquant sur Synchroniser.
    - Cliquez sur **OFF** pour configurer le groupe de synchronisation pour synchroniser uniquement lorsque vous cliquez sur Synchroniser.
7.  **FRÉQUENCE DE SYNCHRONISATION**
    - Si la synchronisation automatique est activée, définissez la fréquence de synchronisation. La fréquence doit être entre 5 Minutes et 1 mois.
8.  Cliquez sur **Enregistrer**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Félicitations. Vous avez créé un groupe de synchronisation qui inclut à la fois une instance de base de données SQL et une base de données SQL Server.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur SQL de base de données et la synchronisation des données SQL, consultez :

* [Télécharger la documentation complète de techniques de synchronisation de données SQL](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [Vue d’ensemble de la base de données SQL](sql-database-technical-overview.md)
* [Gestion du cycle de vie de base de données](https://msdn.microsoft.com/library/jj907294.aspx)
 

 
