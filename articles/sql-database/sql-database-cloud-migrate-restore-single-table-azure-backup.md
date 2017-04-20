<properties
    pageTitle="Restaurer la table unique à partir de la sauvegarde de la base de données de SQL Azure | Microsoft Azure"
    description="Découvrez comment restaurer la table unique à partir de la sauvegarde de la base de données de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>


# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Comment faire pour restaurer une table unique à partir d’une sauvegarde de base de données de SQL Azure

Vous pouvez rencontrer une situation dans laquelle vous avez modifié accidentellement des données dans une base de données SQL et maintenant vous souhaitez récupérer la table affectée unique. Cet article décrit comment restaurer une table unique dans une base de données à partir d’une des [sauvegardes automatiques](sql-database-automated-backups.md)de la base de données SQL.

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>Étapes de préparation : Renommez la table et de restaurer une copie de la base de données
1. Identifier la table dans votre base de données Azure SQL que vous souhaitez remplacer par la copie restaurée. Microsoft SQL Management Studio permet de renommer la table. Par exemple, renommez la table en tant que &lt;nom de la table&gt;_old.

    **Remarque** Pour éviter le blocage, assurez-vous qu’il n’y a aucune activité en cours d’exécution sur la table que vous renommez. Si vous rencontrez des problèmes, assurez-vous que cette procédure au cours d’une période de maintenance.

2. Restaurer une sauvegarde de votre base de données à un point dans le temps que vous souhaitez récupérer à l’aide de la procédure de [Restauration de Point-In_Time](sql-database-recovery-using-backups.md#point-in-time-restore) .

    **Notes**:
    - Le nom de la base de données restaurée doit être dans le format de DBName + TimeStamp ; par exemple, **Adventureworks2012_2016-01-01T22-12Z**. Cette étape ne remplacer le nom de la base de données existant sur le serveur. Il s’agit d’une mesure de sécurité, et il a conçu pour vous permettre de vérifier la base de données restaurée avant de supprimer leur base de données en cours et renommez la base de données restaurée pour la production.
    - Tous les niveaux de performances de base pour la prime sont automatiquement sauvegardés par le service, avec des variables des mesures de conservation, selon le niveau :

| Restauration de la base de données | Couche de base | Niveaux standard | Niveaux de prime |
| :-- | :-- | :-- | :-- |
|  Restauration du point dans le temps |  Aucun point de restauration dans les 7 jours|Aucun point de restauration au sein de 35 jours| Aucun point de restauration au sein de 35 jours|

## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>Copie de la table à partir de la base de données restaurée à l’aide de l’outil de Migration de base de données SQL
1. Téléchargez et installez l' [Assistant de Migration de base de données SQL](https://sqlazuremw.codeplex.com).

2. Ouvrez l’Assistant de Migration de base de données SQL, dans la page **Sélectionner un processus** , sélectionnez **la base de données sous analyser/migration**et puis cliquez sur **suivant**.
![Assistant Migration de base de données SQL - Select Process](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Dans la boîte de dialogue **se connecter au serveur** , appliquez les paramètres suivants :
 - **Nom du serveur**: instance de votre SQL Azure
 - **Authentification**: **l’authentification SQL Server**. Entrez vos informations d’identification d’ouverture de session.
 - **Base de données**: **base de données Master (toutes les bases de données de la liste)**.
 - **Remarque** Par défaut, l’Assistant enregistre vos informations de connexion. Si vous ne souhaitez pas à, sélectionnez **Oublier les informations de connexion**.
![Étape de Migration de base de données SQL de l’Assistant - Sélectionner la Source - 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Dans la boîte de dialogue **Sélectionner la Source** , sélectionnez le nom de la base de données restaurée à partir de la section **étapes de préparation** comme source d’et puis cliquez sur **suivant**.

    ![Étape de Migration de base de données SQL de l’Assistant - Sélectionner la Source - 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. Dans la boîte de dialogue **Sélectionner des objets** , sélectionnez l’option **Sélectionner les objets de base de données spécifique** , puis sélectionnez le table(or tables) que vous souhaitez migrer vers le serveur cible.
![Assistant Migration de base de données SQL - choisissez les objets](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. Dans la page **Résumé de l’Assistant de Script** , cliquez sur **Oui** lorsque vous êtes invité indiquant si vous êtes prêt à générer un script SQL. Vous avez également la possibilité d’enregistrer le Script TSQL pour une utilisation ultérieure.
![Assistant Migration de base de données SQL - Résumé de l’Assistant Script](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. Sur la page de **Résumé des résultats** , cliquez sur **suivant**.
![Assistant Migration de base de données SQL - Résumé des résultats](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. Sur la page de **Connexion au serveur cible d’installation** , cliquez sur **se connecter au serveur**et puis entrez les détails comme suit :
    - **Nom du serveur**: instance du serveur cible
    - **Authentification**: **l’authentification SQL Server**. Entrez vos informations d’identification d’ouverture de session.
    - **Base de données**: **base de données Master (toutes les bases de données de la liste)**. Cette option répertorie toutes les bases de données sur le serveur cible.

    ![Assistant Migration de base de données SQL - connexion au serveur cible d’installation](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Cliquez sur **se connecter**, sélectionnez la base de données cible que vous souhaitez déplacer le tableau à, puis cliquez sur **suivant**. Ceci doit terminer l’exécution du script généré précédemment, et vous devriez voir la table nouvellement déplacée, copiée dans la base de données cible.

## <a name="verification-step"></a>Étape de vérification
1. Interroger et tester la table nouvellement copiée pour vous assurer que les données sont intactes. Après confirmation, vous pouvez supprimer la section **étapes de préparation** du formulaire table renommée. (par exemple, &lt;nom de la table&gt;_old).

## <a name="next-steps"></a>Étapes suivantes

[Sauvegardes automatiques de la base de données SQL](sql-database-automated-backups.md)
