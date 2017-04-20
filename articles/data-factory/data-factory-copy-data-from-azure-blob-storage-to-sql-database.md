<properties
    pageTitle="Copier des données depuis le stockage Blob à SQL de base de données | Microsoft Azure"
    description="Ce didacticiel vous montre comment utiliser les activités de copie dans un pipeline Azure Data Factory pour copier des données depuis le stockage Blob dans la base de données SQL."
    Keywords="BLOB sql, le stockage blob, copie de données"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Copier des données depuis le stockage Blob pour la base de données SQL à l’aide de la fabrique de données 
> [AZURE.SELECTOR]
- [Vue d’ensemble et des conditions préalables](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Assistant copie](data-factory-copy-data-wizard-tutorial.md)
- [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modèle de gestionnaire de ressources Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Dans ce didacticiel, vous créez une fabrique de données avec un pipeline pour copier des données depuis le stockage Blob dans la base de données SQL.

L’activité de copie effectue le déplacement des données dans Azure Data Factory. Il est alimenté par un service global disponible qui peut copier des données entre différentes banques de données de manière sécurisée, fiable et évolutive. Reportez-vous à l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) pour plus d’informations sur l’activité de copie.  

> [AZURE.NOTE] Pour une présentation détaillée du service Factory de données, consultez l’article de [Présentation de Azure Data Factory](data-factory-introduction.md) .

##<a name="prerequisites-for-the-tutorial"></a>Conditions requises pour le didacticiel
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement azure**.  Si vous n’êtes pas abonné, vous pouvez créer un compte d’essai gratuit en quelques minutes. Consultez l’article de la [Version d’évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/) pour plus de détails.
- **Compte de stockage azure**. Vous utilisez le stockage blob sous la forme d’un magasin de données **source** dans ce didacticiel. Si vous n’avez pas un compte de stockage Azure, voir l’article [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) pour les étapes pour en créer un.
- **Base de données SQL azure**. Vous utilisez une base de données Azure SQL sous la forme d’un magasin de données de **destination** dans ce didacticiel. Si vous n’avez pas une base de données Azure SQL que vous pouvez utiliser dans ce didacticiel, consultez [comment créer et configurer une base de données de SQL Azure](../sql-database/sql-database-get-started.md) en créer une.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Vous utilisez SQL Server Management Studio ou Visual Studio pour créer une base de données exemple et à afficher les données de résultat dans la base de données.  

## <a name="collect-blob-storage-account-name-and-key"></a>Collecter la clé et le nom du compte de stockage blob 
Vous avez besoin du nom de compte et clé de compte de votre compte de stockage Azure pour effectuer ce didacticiel. Notez le **nom de compte** et **clé de compte** pour votre compte de stockage Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **plus de services** dans le menu de gauche et sélectionnez les **Comptes de stockage**.

    ![Parcourir - comptes de stockage](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\browse-storage-accounts.png)
3. De la lame de **Comptes de stockage** , sélectionnez le **compte de stockage Azure** que vous souhaitez utiliser dans ce didacticiel.
4. Sélectionnez le lien sous **paramètres**de **touches d’accès rapide** .
5.  Cliquez sur le bouton de **copie** (image) en regard de la zone de texte **nom du compte de stockage** et de sauvegarde/coller il quelque part (par exemple : dans un fichier texte).
6. Répétez l’étape précédente pour copier ou note vers le bas l' **argument key1**.
    
    ![Clé d’accès de stockage](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\storage-access-key.png)
7. Cliquez sur **X**pour fermer toutes les blades.

## <a name="collect-sql-server-database-user-names"></a>Collecter les serveur SQL, de base de données, les noms d’utilisateur
Vous avez besoin des noms de serveur, de base de données et d’utilisateur pour effectuer ce didacticiel Azure SQL. Notez les noms de **serveur**et **base de données** **utilisateur** de votre base de données SQL d’Azure.

1. Dans **Azure portal**, cliquez sur **plus de services** sur la gauche et sélectionnez les **bases de données SQL**.
2. De la **lame de bases de données SQL**, sélectionnez la **base de données** que vous souhaitez utiliser dans ce didacticiel. Notez le **nom de la base de données**.  
3. De la lame de la **base de données SQL** , cliquez sur **Propriétés** sous **paramètres**.
4. Notez les valeurs pour le **Nom du serveur** et de **Connexion d’administrateur de serveur**.
5. Cliquez sur **X**pour fermer toutes les blades.

## <a name="allow-azure-services-to-access-sql-server"></a>Autoriser les services Azure pour accéder à SQL server 
Vérifiez **que **permettre l’accès à des services Azure** paramètre activé pour votre serveur Azure SQL afin que le service Data Factory peut accéder à votre serveur SQL d’Azure** . Pour vérifier et activez ce paramètre, procédez comme suit :

1. Concentrateur de **services plus** à gauche et cliquez sur **les serveurs SQL**.
2. Sélectionnez votre serveur, cliquez sur **pare-feu** sous **paramètres**. 
4. De la lame de **paramètres de pare-feu** , cliquez sur **ON** pour **Autoriser l’accès à des services Azure**.
5. Cliquez sur **X**pour fermer toutes les blades.

## <a name="prepare-blob-storage-and-sql-database"></a>Préparer la base de données SQL et de stockage des objets Blob 
Maintenant, préparer votre stockage blob Azure et la base de données de Azure SQL pour le didacticiel, effectuez les opérations suivantes :  

1. Lancez le bloc-notes, collez le texte suivant et enregistrez-le en tant **emp.txt** **C:\ADFGetStarted** dossier sur votre disque dur.

        John, Doe
        Jane, Doe

2. Utilisez des outils tels que [l’Explorateur de stockage Azure](https://azurestorageexplorer.codeplex.com/) pour créer le conteneur de **adftutorial** et de télécharger le fichier **emp.txt** vers le conteneur.

    ![Explorateur de stockage Azure. Copier des données depuis le stockage Blob dans la base de données SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Utilisez le script SQL suivant pour créer la table **emp** dans votre base de données de SQL Azure.  


        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    **Si vous disposez de SQL Server 2012/2014 installé sur votre ordinateur :** suivez les instructions de [étape 2 : se connecter à la base de données SQL de la gestion d’Azure SQL de base de données à l’aide de SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md#Step2) article pour vous connecter à votre serveur Azure SQL et exécuter le script SQL. Cet article utilise le [portail d’Azure classique](http://manage.windowsazure.com), pas le [nouveau portail Azure](https://portal.azure.com), configurer le pare-feu pour un serveur SQL d’Azure.

    Si votre client n’est pas autorisé à accéder au serveur SQL d’Azure, vous devez configurer le pare-feu pour votre serveur Azure SQL autoriser l’accès à partir de votre ordinateur (adresse IP). Consultez [cet article](../sql-database/sql-database-configure-firewall-settings.md) pour obtenir la procédure configurer le pare-feu pour votre serveur Azure SQL.

Vous avez terminé la configuration requise. Vous pouvez créer une fabrique de données à l’aide d’une des manières suivantes. Cliquez sur les onglets en haut ou sur les liens suivants pour exécuter le didacticiel.     

- [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Assistant copie](data-factory-copy-data-wizard-tutorial.md)
