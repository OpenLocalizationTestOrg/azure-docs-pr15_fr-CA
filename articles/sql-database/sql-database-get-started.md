<properties
    pageTitle="Didacticiel de la base de données de SQL : créer une base de données SQL | Microsoft Azure"
    description="Apprenez à configurer un serveur de logique de la base de données SQL, règle de pare-feu de serveur, de base de données SQL et exemples de données. En outre, comment se connecter avec les outils clients, configurer des utilisateurs et définir une règle de pare-feu de base de données."
    keywords="didacticiel de base de données SQL, créer une base de données sql"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/07/2016"
    ms.author="carlrab"/>


# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>Didacticiel de la base de données de SQL : création d’une base de données SQL en quelques minutes à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Azure portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Dans ce didacticiel, vous allez apprendre à utiliser le portail Azure pour :

- Créer une base de données Azure SQL avec les exemples de données.
- Créer une règle de pare-feu au niveau du serveur pour une seule adresse IP ou une plage d’adresses IP.

Vous pouvez effectuer les mêmes tâches à l’aide de [C#](sql-database-get-started-csharp.md) ou [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>Créer votre première base de données SQL d’Azure 

1. Si vous n’êtes pas actuellement connecté, connectez-vous au [portail Azure](http://portal.azure.com).
2. Cliquez sur **Nouveau**et cliquez sur **données + de stockage**, puis recherchez **De la base de données SQL**.

    ![Nouvelle base de données sql 1](./media/sql-database-get-started/sql-database-new-database-1.png)

3. Cliquez sur **De base de données SQL** pour ouvrir la lame de la base de données SQL. Le contenu de cette lame varie en fonction du nombre de vos abonnements et vos objets existants (tels que les serveurs existants).

    ![Nouvelle base de données sql 2](./media/sql-database-get-started/sql-database-new-database-2.png)

4. Dans la zone de texte **nom de la base de données** , fournissez un nom pour votre base de données premier - comme « ma base de données ». Une coche verte indique que vous avez fourni un nom valide.

    ![Nouvelle base de données sql 3](./media/sql-database-get-started/sql-database-new-database-3.png)

5. Si vous disposez de plusieurs abonnements, sélectionnez un abonnement.
6. Sous le **groupe de ressources**, cliquez sur **Créer nouveau** et fournir un nom pour votre premier groupe de ressources - tels que « mes--groupe de ressources ». Une coche verte indique que vous avez fourni un nom valide.

    ![Nouvelle base de données sql 4](./media/sql-database-get-started/sql-database-new-database-4.png)

7. Sous **Sélectionner la source**, cliquez sur **exemple** , puis sous **Sélectionnez exemple** **AdventureWorksLT [V12]**.

    ![Nouvelle base de données sql 5](./media/sql-database-get-started/sql-database-new-database-5.png)

8. Sous **serveur**, cliquez sur **configurer les paramètres requis**.

    ![Nouvelle base de données sql 6](./media/sql-database-get-started/sql-database-new-database-6.png)

9. Sur le serveur lame, cliquez sur **créer un nouveau serveur**. Une base de données Azure SQL est créé au sein d’un objet de serveur, qui peut être soit un serveur existant ou un nouveau serveur.

    ![Nouvelle base de données sql 7](./media/sql-database-get-started/sql-database-new-database-7.png)

10. Passez en revue le **nouveau serveur** lame pour comprendre les informations que vous devez fournir pour votre nouveau serveur.

    ![Nouvelle base de données sql 8](./media/sql-database-get-started/sql-database-new-database-8.png)

11. Dans la zone de texte **nom du serveur** , fournissez un nom pour votre premier serveur - par exemple « mon-nouveau-server-object ». Une coche verte indique que vous avez fourni un nom valide.

    ![Nouvelle base de données sql 9](./media/sql-database-get-started/sql-database-new-database-9.png)
 
12. Sous **ouverture de session serveur admin**, fournir un nom d’utilisateur pour la connexion de l’administrateur pour ce serveur - par exemple «-admin-« mon compte ». Cette connexion est appelée la connexion de serveur principal. Une coche verte indique que vous avez fourni un nom valide.

    ![Nouvelle base de données sql 10](./media/sql-database-get-started/sql-database-new-database-10.png)

13. Sous **mot de passe** et **Confirmer le mot de passe**, fournir un mot de passe de compte de l’entité de sécurité de connexion - telles que "p@ssw0rd1". Une coche verte indique que vous avez fourni un mot de passe valide.

    ![Nouvelle base de données sql 11](./media/sql-database-get-started/sql-database-new-database-11.png)
 
14. Sous **emplacement**, sélectionnez un centre de données approprié à votre emplacement - par exemple « Est de l’Australie ».

    ![Nouvelle base de données sql 12](./media/sql-database-get-started/sql-database-new-database-12.png)

15. Sous ** V12 de créer serveur (dernière mise à jour), notez que vous avez uniquement la possibilité de créer une version de Azure SQL server.

    ![Nouvelle base de données sql 13](./media/sql-database-get-started/sql-database-new-database-13.png)

16. Notez que, par défaut, la case à cocher pour **Autoriser azure les services pour accéder à server** est sélectionnée et ne peut pas être modifié ici. Il s’agit d’une option avancée. Vous pouvez modifier ce paramètre dans les paramètres de pare-feu de serveur pour cet objet de serveur, bien que la plupart des scénarios, il n’est pas nécessaire.

    ![Nouvelle base de données sql 14](./media/sql-database-get-started/sql-database-new-database-14.png)

17. Sur le nouveau serveur lame, vérifiez vos choix, puis cliquez sur **Sélectionner** pour sélectionner ce nouveau serveur pour votre nouvelle base de données.

    ![Nouvelle base de données sql 15](./media/sql-database-get-started/sql-database-new-database-15.png)

18. Sur la lame de la base de données SQL, sous **niveau de tarification**, cliquez sur **Standard S2** et puis cliquez sur de **base** pour choisir le niveau de tarification moins coûteux de votre première base de données. Vous pouvez toujours modifier le niveau de tarification ultérieurement.

    ![Nouvelle base de données sql 16](./media/sql-database-get-started/sql-database-new-database-16.png)

19. Sur la lame de la base de données SQL, vérifiez vos choix, puis cliquez sur **créer** pour créer votre premier serveur et base de données. Les valeurs que vous avez fournies sont validées et le déploiement commence.

    ![Nouvelle base de données sql 17](./media/sql-database-get-started/sql-database-new-database-17.png)

20. Dans la barre d’outils de portail, cliquez sur les éléments de **Notifications** pour vérifier l’état de votre déploiement.

    ![Nouvelle base de données sql 18](./media/sql-database-get-started/sql-database-new-database-18.png)

>[AZURE.IMPORTANT]Lorsque le déploiement est terminé, votre nouveau serveur Azure SQL et la base de données sont créées dans Azure. Vous ne pourrez pas vous connecter à votre nouveau serveur ou la base de données à l’aide des outils de SQL Server jusqu'à ce que vous créez une règle de pare-feu de serveur pour ouvrir le pare-feu de base de données SQL pour les connexions à partir d’en dehors d’Azure.

[AZURE.INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce didacticiel de la base de données SQL et créé une base de données avec des exemples de données, vous êtes prêt à Explorer à l’aide de vos outils préférés.

- Si vous êtes familiarisé avec Transact-SQL et SQL Server Management Studio (SSMS), découvrez comment [se connecter et requête d’une base de données SQL avec SSMS](sql-database-connect-query-ssms.md).

- Si vous connaissez Excel, découvrez comment [se connecter à une base de données SQL Azure avec Excel](sql-database-connect-excel.md).

- Si vous êtes prêt à commencer à coder, choisissez votre langage de programmation à [des bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md).

- Si vous souhaitez déplacer vos bases de données SQL Server sur site vers Azure, consultez [migration d’une base de données de la base de données SQL](sql-database-cloud-migrate.md) pour en savoir plus.

- Si vous souhaitez charger des données dans une nouvelle table à partir d’un fichier CSV à l’aide de l’outil de ligne de commande BCP, consultez [chargement de données dans la base de données SQL à partir d’un fichier CSV à l’aide de BCP](sql-database-load-from-csv-with-bcp.md).

- Si vous voulez commencer à Explorer la sécurité de la base de données de SQL Azure, consultez [mise en route avec sécurité](sql-database-get-started-security.md)


## <a name="additional-resources"></a>Ressources supplémentaires

[Quelle est la base de données de SQL ?](sql-database-technical-overview.md)
