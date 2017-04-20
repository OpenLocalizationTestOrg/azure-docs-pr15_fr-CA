<properties
    pageTitle="Connecter le Gestionnaire de Configuration de journal Analytique | Microsoft Azure"
    description="Cet article explique les étapes pour connecter le Gestionnaire de Configuration à Analytique du journal et commencer à analyser des données."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="banders"/>

# <a name="connect-configuration-manager-to-log-analytics"></a>Connecter le Gestionnaire de Configuration de journal Analytique

Vous pouvez vous connecter System Center Configuration Manager pour l’Analytique de journal dans l’OMS de données de collection de dispositif de synchronisation. Cela rend les données de votre déploiement de Configuration Manager disponible dans l’OMS.

Il existe un certain nombre d’étapes nécessaires pour connecter le Gestionnaire de Configuration de l’OMS, Voici un récapitulatif rapide de l’ensemble du processus :

1. Dans le portail de gestion Azure, inscrire le Gestionnaire de Configuration sous la forme d’une application Web Application et/ou des API Web et assurez-vous d’avoir les ID client et la clé secrète du client à partir de l’enregistrement d’Azure Active Directory. Voir la rubrique [portail utilisé pour créer l’application d’Active Directory et le service principal qui peuvent accéder aux ressources](../resource-group-create-service-principal-portal.md) pour plus d’informations sur la façon d’effectuer cette étape.
2. Dans le portail de gestion Azure, [fournissent la Configuration Manager (l’application web inscrit) avec l’autorisation d’accéder aux OMS](#provide-configuration-manager-with-permissions-to-oms).
3. Dans Gestionnaire de Configuration, [Ajoutez une connexion à l’aide de l’Assistant Ajout d’un connexion OMS](#add-an-oms-connection-to-configuration-manager).
4. Dans le Gestionnaire de Configuration, vous pouvez [mettre à jour les propriétés de connexion](#update-oms-connection-properties) si jamais, la clé secrète du client ou du mot de passe arrive à expiration ou est perdue.
5. Des informations à partir du portail de l’OMS, [télécharger et installer l’Agent de surveillance de Microsoft](#download-and-install-the-agent) sur l’ordinateur qui exécute la connexion au service Gestionnaire de Configuration point de rôle système de site. L’agent envoie les données du Gestionnaire de Configuration de l’OMS.
6. De l’OMS, [Importer des collections à partir du Gestionnaire de Configuration](#import-collections) en tant que groupes de l’ordinateur.
7. OMS, affiche les données à partir du Gestionnaire de Configuration en tant que [groupes de l’ordinateur](log-analytics-computer-groups.md).

Vous pouvez en savoir plus sur la connexion de Configuration Manager pour OMS sur les [données de synchronisation à partir du Gestionnaire de Configuration de la Suite de gestion des opérations de Microsoft](https://technet.microsoft.com/library/mt757374.aspx).



## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Fournissez le Gestionnaire de Configuration avec des autorisations pour OMS

La procédure suivante fournit le portail de gestion Azure avec des autorisations d’accès aux OMS. En particulier, vous devez accorder le *rôle de collaborateur* pour les utilisateurs dans le groupe de ressources. À son tour, qui permet le portail de gestion Azure connecter le Gestionnaire de Configuration d’OMS.

>[AZURE.NOTE] Vous devez spécifier des autorisations d’OMS pour Configuration Manager. Dans le cas contraire, vous recevez un message d’erreur lorsque vous utilisez l’Assistant de configuration dans le Gestionnaire de Configuration.


1. Ouvrir le [portail Azure](https://portal.azure.com/) et cliquez sur **Parcourir** > **Journal Analytique (OMS)** pour ouvrir la lame journal Analytique (OMS).  
2. Sur la lame de **Journal Analytique (OMS)** , cliquez sur **Ajouter** pour ouvrir la lame de **l’Espace de travail de l’OMS** .  
  ![Lame de l’OMS](./media/log-analytics-sccm/sccm-azure01.png)
3. Sur la lame de **l’Espace de travail de l’OMS** , fournissez les informations suivantes et puis cliquez sur **OK**.
  - **Espace de travail de l’OMS**
  - **Abonnement**
  - **Groupe de ressources**
  - **Emplacement**
  - **Niveau de tarification**  
    ![Lame de l’OMS](./media/log-analytics-sccm/sccm-azure02.png)  

    >[AZURE.NOTE] L’exemple ci-dessus crée un nouveau groupe de ressources. Le groupe de ressources est utilisé uniquement pour fournir un gestionnaire de Configuration avec les autorisations pour l’espace de travail OMS dans cet exemple.

4. Cliquez sur **Parcourir** > **groupes de ressources** pour ouvrir la lame de **groupes de ressources** .
5. De la lame de **groupes de ressources** , cliquez sur le groupe de ressources que vous avez créé ci-dessus pour ouvrir la &lt;nom de la ressource groupe&gt; lame de paramètres.  
  ![lame de paramètres de groupe de ressources](./media/log-analytics-sccm/sccm-azure03.png)
6. Dans le &lt;nom de groupe de ressources&gt; lame de paramètres, cliquez sur contrôle d’accès (IAM) pour ouvrir la &lt;nom de groupe de ressource&gt; lame d’utilisateurs.  
  ![lame de ressource groupe utilisateurs](./media/log-analytics-sccm/sccm-azure04.png)  
7. Dans le &lt;nom de la ressource groupe&gt; lame d’utilisateurs, cliquez sur **Ajouter** pour ouvrir la blade **d’Ajouter l’accès** .
8. Dans la blade **d’Ajouter l’accès** , cliquez sur **Sélectionner un rôle**et puis sélectionnez le rôle de **contributeur** .  
  ![Sélectionnez un rôle](./media/log-analytics-sccm/sccm-azure05.png)  
9. Cliquez sur **Ajouter des utilisateurs**, sélectionnez l’utilisateur du Gestionnaire de Configuration, cliquez sur **Sélectionner**, puis cliquez sur **OK**.  
  ![ajouter des utilisateurs](./media/log-analytics-sccm/sccm-azure06.png)  


## <a name="add-an-oms-connection-to-configuration-manager"></a>Ajouter une connexion de l’OMS pour le Gestionnaire de Configuration

Pour ajouter une connexion de l’OMS, votre environnement Configuration Manager doit avoir un [point de connexion de service](https://technet.microsoft.com/library/mt627781.aspx) configuré pour le mode en ligne.

1. Dans l’espace de travail **Administration** du Gestionnaire de Configuration, sélectionnez le **Connecteur de l’OMS**. L' **Assistant Ajout d’un connexion OMS**s’ouvre. Cliquez sur **suivant**.

2. Dans l’écran **Général** , vérifiez que vous avez terminé les actions suivantes que vous avec des informations pour chaque élément, puis cliquez sur **suivant**.
  1. Dans le portail de gestion Azure, vous êtes enregistré le Gestionnaire de Configuration sous la forme d’une application Web Application et/ou des API Web et que vous disposez de l' [ID de client à partir de l’enregistrement](../active-directory/active-directory-integrating-applications.md).
  2. Dans le portail de gestion Azure, vous avez créé une clé secrète d’application pour l’application enregistrée dans Azure Active Directory.  
  3. Dans le portail de gestion Azure, vous avez fourni l’application web inscrit avec autorisation d’accès OMS.  
  ![Connexion à la page de l’OMS généraux de l’Assistant](./media/log-analytics-sccm/sccm-console-general01.png)

3. Sur l’écran **Azure Active Directory** , configurez vos paramètres de connexion d’OMS en fournissant vos **clients** , **l’ID de Client** et **Clé secrète du Client** , puis cliquez sur **suivant**.  
  ![Connexion à la page Active Directory OMS Assistant Azure](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)

4. Si vous avez effectué toutes les autres procédures avec succès, les informations sur l’écran de **Configuration de la connexion OMS** seront affiche automatiquement sur cette page. Informations pour les paramètres de connexion doivent s’afficher pour votre **abonnement Azure** , **groupe de ressources Azure** et **Espace de travail Suite de gestion des opérations**.  
  ![Connexion à la page de connexion d’OMS OMS Assistant](./media/log-analytics-sccm/sccm-wizard-configure04.png)

5. L’Assistant se connecte au service OMS en utilisant les informations que vous avez entrée. Sélectionnez les collections de périphérique que vous souhaitez synchroniser avec OMS et puis cliquez sur **Ajouter**.  
  ![Sélectionnez les Collections](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)

6. Vérifiez vos paramètres de connexion à l’écran **Résumé** , puis cliquez sur **suivant**. L’écran de **progression** affiche l’état de la connexion, puis doit **Terminer**.

>[AZURE.NOTE] Vous devez vous connecter OMS sur le site de niveau supérieur dans la hiérarchie. Si vous OMS de connectez à un site principal autonome, puis ajoutez un site d’administration centrale à votre environnement, vous devrez supprimer et recréer la connexion de l’OMS dans la nouvelle hiérarchie.

Après avoir lié le Gestionnaire de Configuration de l’OMS, vous pouvez ajouter ou supprimer des collections et afficher les propriétés de la connexion de l’OMS.

## <a name="update-oms-connection-properties"></a>Mettre à jour les propriétés de la connexion OMS

Si une clé secrète du client ou du mot de passe expire jamais ou est perdue, vous devez mettre à jour manuellement les propriétés de connexion OMS.

1. Dans le Gestionnaire de Configuration, accédez à des **Services en nuage** , puis sélectionnez le **Connecteur de l’OMS** pour ouvrir la page **Propriétés de connexion d’OMS** .
2. Sur cette page, cliquez sur l’onglet **Azure Active Directory** pour afficher vos **clients**, **L’ID Client**, **expiration de clé secrète de Client**. **Vérifiez** votre **clé secrète du Client** s’il a expiré.


## <a name="download-and-install-the-agent"></a>Télécharger et installer l’agent

1. Dans le portail de l’OMS, [Téléchargez le fichier d’installation de l’agent à partir de l’OMS](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms).
2. Pour installer et configurer l’agent sur l’ordinateur qui exécute le rôle de système de site Configuration Manager service connexion point, utilisez une des méthodes suivantes :
  - [Installation de l’agent à l’aide du programme d’installation](log-analytics-windows-agents.md#install-the-agent-using-setup)
  - [Installation de l’agent à l’aide de la ligne de commande](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
  - [Installation de l’agent à l’aide de DSC dans Azure Automation](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)


## <a name="import-collections"></a>Relances d’importation

Après avoir ajouté une connexion de l’OMS pour le Gestionnaire de Configuration et d’installer l’agent sur l’ordinateur qui exécute la connexion au service Gestionnaire de Configuration point le rôle système de site, l’étape suivante consiste à importer des collections à partir du Gestionnaire de Configuration de l’OMS en tant que groupes de l’ordinateur.

Une fois que l’importation est activée, les informations d’appartenance de collection sont récupérées toutes les 3 heures pour maintenir à jour les appartenances de la collection. Vous pouvez choisir de désactiver l’importation à tout moment.

1. Dans le portail de l’OMS, cliquez sur **paramètres**.
2. Cliquez sur l’onglet **Groupes d’ordinateurs** , puis sur l’onglet **SCCM** .
3. Sélectionnez des **appartenances aux collections de gestionnaire de Configuration importation** et puis cliquez sur **Enregistrer**.  
  ![Groupes d’ordinateurs - onglet SCCM](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Afficher les données à partir du Gestionnaire de Configuration

Après avoir ajouté une connexion de l’OMS pour le Gestionnaire de Configuration et d’installer l’agent sur l’ordinateur qui exécute le rôle de système de site Configuration Manager service connexion point, les données de l’agent sont envoyées à l’OMS. De l’OMS, vos collections de gestionnaire de Configuration apparaissent en tant que [groupes de l’ordinateur](log-analytics-computer-groups.md). Vous pouvez afficher les groupes à partir de la page du **Gestionnaire de Configuration** dans les **Groupes d’ordinateurs** dans les **paramètres**.

Une fois que les collections sont importées, vous pouvez voir le nombre d’ordinateurs avec des appartenances aux collections qui ont été détecté. Vous pouvez également voir le nombre de collections qui ont été importés.

![Groupes d’ordinateurs - onglet SCCM](./media/log-analytics-sccm/sccm-computer-groups02.png)

Lorsque vous cliquez sur deux, recherche s’ouvre et affiche tous les groupes importés ou tous les ordinateurs qui appartiennent à chaque groupe. À l’aide de la [Recherche dans un journal](log-analytics-log-searches.md), vous pouvez démarrer une analyse approfondie de données du Gestionnaire de Configuration.

## <a name="next-steps"></a>Étapes suivantes

- [Recherche des journaux de](log-analytics-log-searches.md) permet d’afficher des informations détaillées sur vos données du Gestionnaire de Configuration.
