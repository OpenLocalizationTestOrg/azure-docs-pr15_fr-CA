<properties
   pageTitle="Utilisez SQL Server Management Studio pour compatibilité de déterminer la base de données SQL avant la migration de la base de données de SQL Azure | Microsoft Azure"
   description="Microsoft Azure SQL base de données, migration de base de données, la compatibilité de la base de données SQL, Assistant Exportation d ' Application de couche données"
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
   ms.date="08/29/2016"
   ms.author="carlrab"/>

# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Utilisez SQL Server Management Studio, à la compatibilité de déterminer la base de données SQL avant la migration de la base de données de SQL Azure

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Conseiller de mise à niveau](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
 
Dans cet article, que vous apprendrez à déterminer si une base de données SQL Server est compatible pour migrer vers une base de données SQL à l’aide de l’Assistant Exportation d’une Application de couche données dans SQL Server Management Studio.

## <a name="using-sql-server-management-studio"></a>À l’aide de SQL Server Management Studio

1. Vérifiez que vous disposez de la dernière version de SQL Server Management Studio. Nouvelles versions de Management Studio sont mises à jour mensuelles de rester synchronisé avec les mises à jour sur le portail Azure.

     > [AZURE.IMPORTANT] Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Ouvrez Management Studio et connectez-vous à votre base de données source dans l’Explorateur d’objets.
3. Avec le bouton droit de la base de données source dans l’Explorateur d’objets, pointez sur **tâches**et cliquez sur **Exporter les applications de couche données**

    ![Exporter une application de couche données dans le menu tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Dans l’Assistant Exportation, cliquez sur **suivant**, puis sur l’onglet **paramètres** , configurez l’exportation pour enregistrer le fichier de type sac à DOS pour un emplacement de disque local ou un blob Azure. Enregistrement d’un fichier de type sac à DOS si vous n’avez aucun problème de compatibilité de base de données. S’il existe des problèmes de compatibilité, ils sont affichés sur la console.

    ![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Pour ignorer l’exportation de données, cliquez sur l' **onglet Avancé** et désactivez la case à cocher **Sélectionner tout** . À ce stade, notre objectif est seulement pour tester la compatibilité.

    ![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Cliquez sur **suivant** , puis cliquez sur **Terminer**. Problèmes de compatibilité de base de données, le cas échéant, s’affiche une fois que l’Assistant valide le schéma.

    ![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Si aucune erreur ne s’affiche, votre base de données est compatible et vous êtes prêt à migrer. Si vous avez des erreurs, vous devez les corriger. Pour afficher les erreurs, cliquez sur **l’erreur** pour le **schéma de validation**. 
    ![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.  Si le *. Fichier de type sac à DOS est générée, puis votre base de données est compatible avec la base de données de SQL, et vous êtes prêt à migrer.

## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Résoudre des problèmes de compatibilité de migration de base de données](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrer une base de données SQL Server compatible à la base de données de SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migration des bases de données SQL Server non - à l’aide de l’Assistant Migration de SQL Server](http://blogs.msdn.com/b/ssma/)
