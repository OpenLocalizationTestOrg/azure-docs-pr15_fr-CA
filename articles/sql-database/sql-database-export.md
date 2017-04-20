<properties
    pageTitle="Archiver une base de données Azure SQL dans un fichier de type sac à DOS à l’aide du portail Azure"
    description="Archiver une base de données Azure SQL dans un fichier de type sac à DOS à l’aide du portail Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Archiver une base de données Azure SQL dans un fichier de type sac à DOS à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Azure portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Cet article fournit des instructions relatives à l’archivage de votre base de données Azure SQL dans un fichier de type sac à DOS (stocké dans le stockage blob Azure) à l’aide du [portail Azure](https://portal.azure.com).

Lorsque vous avez besoin créer une archive d’une base de données Azure SQL, vous pouvez exporter les données et le schéma de base de données dans un fichier de type sac à DOS. Un fichier de type sac à DOS est simplement un fichier ZIP avec une extension de type sac à DOS. Un fichier de type sac à DOS ultérieurement peut être stocké dans le stockage blob Azure ou dans le stockage local à un emplacement local et ultérieurement les principaux importés dans la base de données de SQL Azure ou dans un SQL Server installation sur site. 

***Considérations relatives à la***

- Une archive de façon cohérente, vous ne devez vous assurer qu’aucune écriture activité se produit lors de l’exportation, ou que vous exportez à partir d’une [copie cohérente de façon transactionnelle](sql-database-copy.md) de votre base de données SQL d’Azure.
- La taille maximale d’un fichier de type sac à DOS archivé sur le stockage des objets Blob Azure est de 200 Go. Pour archiver un fichier plus volumineux de type sac à DOS sur le stockage local, utilisez l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Cet utilitaire est fourni avec Visual Studio et SQL Server. Vous pouvez également [Télécharger](https://msdn.microsoft.com/library/mt204009.aspx) la dernière version des outils de données de SQL Server pour obtenir cet utilitaire.
- L’archivage vers un stockage Azure premium à l’aide d’un fichier de type sac à DOS n’est pas pris en charge.
- Si l’opération d’exportation dépasse 20 heures, elle peut être annulée. Pour améliorer les performances lors de l’exportation, vous pouvez :
 - Augmenter provisoirement le niveau de service.
 - Cessation de tous les lire et de l’activité d’écriture lors de l’exportation.
 - Utilisation d’un [index ordonné en clusters](https://msdn.microsoft.com/library/ms190457.aspx) avec des valeurs non null sur toutes les tables de grande taille. Sans index ordonnés en clusters, une exportation peut échouer si elle dure plu de 6 à 12 heures. C’est parce que le service d’exportation doit effectuer une analyse de table pour essayer d’exporter l’ensemble de la table. Un bon moyen pour déterminer si vos tables sont optimisés pour l’exportation est à exécuter **DBCC SHOW_STATISTICS** et assurez-vous que le *RANGE_HI_KEY* n’est pas null et que sa valeur a une bonne distribution. Pour plus d’informations, voir [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).


> [AZURE.NOTE] BACPACs ne sont pas destinés à être utilisés pour la sauvegarde et les opérations de restauration. Une base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données de l’utilisateur. Pour plus d’informations, consultez [Vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md).

Pour terminer cet article, vous devez les éléments suivants :

- Un abonnement Azure.
- Une base de données SQL Azure. 
- Un [compte de stockage Standard d’Azure](../storage/storage-create-storage-account.md) avec un conteneur blob pour stocker le type sac à DOS dans le stockage standard.

## <a name="export-your-database"></a>Exporter votre base de données

Ouvrez la lame de la base de données SQL pour la base de données que vous souhaitez exporter.

> [AZURE.IMPORTANT] Pour garantir un fichier de type sac à DOS cohérent vous devez tout d’abord [créer une copie de votre base de données](sql-database-copy.md) et exportez la copie de la base de données. 

1.  Accédez au [portail Azure](https://portal.azure.com).
2.  Cliquez sur **bases de données SQL**.
3.  Cliquez sur la base de données d’archivage.
4.  De la lame de la base de données SQL, cliquez sur **Exporter** pour ouvrir la blade **d’Exporter la base de données** :

    ![bouton Exporter][1]

5.  Cliquez sur **stockage** et sélectionnez votre conteneur blob et de compte de stockage où sera stocké le type sac à DOS :

    ![Exporter la base de données][2]

6. Sélectionnez le type d’authentification. 
7.  Entrez les informations d’identification d’authentification approprié pour le serveur Azure SQL contenant la base de données que vous exportez.
8.  Cliquez sur **OK** pour archiver la base de données. Cliquer sur **OK** crée une demande de base de données d’exportation et l’envoie au service. La durée de que l’exportation prendra dépend de la taille et la complexité de votre base de données et de votre niveau de service. Vous recevrez une notification.

    ![notification d’exportation][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Surveiller la progression de l’opération d’exportation

1.  Cliquez sur **les serveurs SQL**.
2.  Cliquez sur le serveur contenant la base de données (source) d’origine que vous venez d’archivage.
3.  Faites défiler jusqu'à des opérations.
4.  De la lame de serveur SQL, cliquez sur **historique d’importation/exportation**:

    ![importer, exporter l’historique][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Vérifiez que le type sac à DOS se trouve dans votre conteneur de stockage

1.  Cliquez sur **comptes de stockage**.
2.  Cliquez sur le compte de stockage où vous avez stocké l’archive de type sac à DOS.
3.  Cliquez sur **les conteneurs** et sélectionnez le conteneur que vous avez exporté de la base de données en détails (vous pouvez télécharger et enregistrer le type sac à DOS à partir d’ici).

    ![Détails du fichier .bacpac][5]  

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’importation de sac à DOS une base de données SQL Azure, reportez-vous à [importation d’un BACPCAC à une base de données SQL d’Azure](sql-database-import.md)
- Pour en savoir plus sur l’importation d’un type sac à DOS pour une base de données SQL Server, reportez-vous à [importation d’un BACPCAC à une base de données SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

