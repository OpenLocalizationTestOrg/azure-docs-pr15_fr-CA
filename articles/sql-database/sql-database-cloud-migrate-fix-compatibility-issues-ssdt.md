<properties
   pageTitle="Résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers SQL de base de données | Microsoft Azure"
   description="Microsoft Azure SQL base de données, migration de base de données, la compatibilité, l’Assistant de Migration SQL Azure, SSDT"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>Migrer une base de données de SQL Server à l’aide des outils de données de SQL Server pour Visual Studio de la base de données SQL Azure 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Conseiller de mise à niveau](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Dans cet article, vous apprenez à détecter et à résoudre les problèmes de compatibilité de base de données SQL Server à l’aide des outils de données SQL Server de Visual Studio avant la migration de la base de données de SQL Azure.

## <a name="using-sql-server-data-tools-for-visual-studio"></a>À l’aide des outils de données de SQL Server pour Visual Studio

Utilisez les outils de données de SQL Server de Visual Studio (« SSDT ») pour importer le schéma de base de données dans un projet de base de données de Visual Studio pour l’analyse. Pour analyser, vous spécifiez la plate-forme cible pour le projet en tant que V12 de base de données SQL et puis générez le projet. Si la génération est réussie, la base de données est compatible. Si la génération échoue, vous pouvez résoudre les erreurs dans SSDT (ou un des autres outils décrits dans cette rubrique). Une fois le projet généré avec succès, vous pouvez le publier comme une copie de la base de données source. Vous pouvez ensuite utiliser la fonction de comparaison de données de SSDT pour copier les données à partir de la base de données source vers la base de données compatible V12 de SQL Azure. Vous pouvez alors migrer cette base de données mis à jour. Pour utiliser cette option, téléchargez la [version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagramme de migration VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Si la migration de schéma est requise, le schéma peut être publié directement à partir de Visual Studio directement à la base de données de SQL Azure. Utilisez cette méthode lorsque le schéma de base de données nécessite plus de modifications peut être gérée par l’Assistant de migration uniquement.

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Détection des problèmes de compatibilité à l’aide des outils de données de SQL Server pour Visual Studio
   
1.  Ouvrez l' **Explorateur d’objets SQL Server** dans Visual Studio. Utilisez **Ajouter un SQL Server** pour se connecter à l’instance de SQL Server contenant la base de données en cours de migration. Localiser la base de données dans l’Explorateur d’objets, avec le bouton droit de la base de données et sélectionnez **Créer un nouveau projet...**     
    
    ![Nouveau projet](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
   
2.  Configurer les paramètres d’importation pour **Importer des objets de portée application**. Désélectionnez les options d’importation suivantes : référencé des connexions d’accès, les autorisations et les paramètres de la base de données.    

    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    

3.  Cliquez sur **Démarrer** pour importer la base de données et de créer le projet contenant un fichier de script T-SQL pour chaque objet dans la base de données. Les fichiers de script sont imbriquées dans des dossiers au sein du projet.    

    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    

4.  Dans l’Explorateur de solutions Visual Studio, cliquez sur le projet de base de données et sélectionnez Propriétés. Dans la page **Paramètres du projet** , configurez la plateforme cible de V12 de base de données SQL Microsoft Azure.    
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
    
5.  Droit sur le projet et sélectionnez **Générer** pour générer le projet.    
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
    
6.  La **Liste d’erreurs** affiche chaque incompatibilité. Dans ce cas, le nom d’utilisateur NT AUTHORITY\NETWORK SERVICE n’est pas compatible. Dans la mesure où il n’est pas compatible, vous pouvez transformer en commentaire ou supprimer (et adresse implications de la suppression de cette connexion et le rôle de la solution de base de données).     
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    
    
## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Résolution des problèmes de compatibilité à l’aide des outils de données de SQL Server pour Visual Studio

1.  Double-cliquez sur le script premier pour ouvrir le script dans une fenêtre de requête et de commenter le script et puis exécuter le script.     
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.  Répétez ce processus pour chaque script contenant les incompatibilités jusqu'à ce qu’aucune erreur ne reste.    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
    
3.  Lorsque la base de données est exempt d’erreurs, cliquez sur le projet et sélectionnez **Publier**. Une copie de la base de données source est créée et publiée (il est fortement recommandé d’utiliser une copie, au moins au début).     
 - Avant de publier, selon la version de SQL Server source (antérieure à SQL Server 2014), vous devrez peut-être réinitialiser la plateforme du projet cible pour permettre le déploiement.     
 - Si vous migrez une ancienne base de données SQL Server, n’introduisent pas de toutes les fonctionnalités dans le projet qui ne sont pas pris en charge dans la code source de SQL Server jusqu'à ce que faire migrer la base de données vers une version plus récente de SQL Server.     

        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
        
4.  Dans l’Explorateur d’objets SQL Server, avec le bouton droit de votre base de données source, puis cliquez sur **Comparaison de données**. Comparaison du projet à la base de données d’origine vous aide à comprendre quelles modifications ont été apportées par l’Assistant. Sélectionnez votre version V12 de SQL Azure de la base de données et puis cliquez sur **Terminer**.    
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    

5.  Passez en revue les différences détectées et puis cliquez sur **Cible de la mise à jour** pour migrer des données à partir de la base de données source dans la base de données V12 de SQL Azure.     
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
    
6.  Choisissez une méthode de déploiement. Consultez [migrer une base de données SQL Server compatible à la base de données de SQL.](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migration des bases de données SQL Server non - à l’aide de l’Assistant Migration de SQL Server](http://blogs.msdn.com/b/ssma/)
