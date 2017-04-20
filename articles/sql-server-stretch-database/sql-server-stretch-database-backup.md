<properties
    pageTitle="Sauvegarde des bases de données compatibles avec Stretch | Microsoft Azure"
    description="Découvrez comment sauvegarder Stretch\-activé des bases de données."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="douglasl"/>

# <a name="backup-stretch-enabled-databases"></a>Bases de données prenant en charge l’étirement de la sauvegarde

Les sauvegardes de base de données vous aident à récupérer à partir de nombreux types de défaillances, les erreurs et les catastrophes naturelles.  

-   Vous devez sauvegarder votre Stretch\-activé des bases de données SQL Server.  

-   Microsoft Azure sauvegarde automatiquement les données à distance, base de données d’étirement a migré à partir de SQL Server vers Azure.  

>    [AZURE.NOTE] La sauvegarde n'est qu’une partie d’une solution de continuité et d’une haute disponibilité totale. Pour plus d’informations sur la disponibilité élevée, consultez [Solutions de haute disponibilité](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="back-up-your-sql-server-data"></a>Sauvegarder vos données SQL Server  

Pour sauvegarder votre Stretch\-activé des bases de données SQL Server, vous pouvez continuer à utiliser les méthodes de sauvegarde de SQL Server que vous utilisez actuellement. Pour plus d’informations, voir [sauvegarde et restauration de SQL Server bases de données](https://msdn.microsoft.com/library/ms187048.aspx).

Les sauvegardes d’une base de données de SQL Server prenant en charge l’étirement contiennent uniquement les données locales et éligibles de données pour la migration au point dans le temps lorsque la sauvegarde s’exécute. \(Données concernées sont des données qui n’a pas encore été migrées, mais seront migrées vers Azure basé sur les paramètres de la migration des tables.\) Cela s’appelle une sauvegarde **partielle** et n’inclut pas les données déjà migré vers Azure.  

## <a name="back-up-your-remote-azure-data"></a>Sauvegardez vos données Azure à distance   

Microsoft Azure sauvegarde automatiquement les données à distance, base de données d’étirement a migré à partir de SQL Server vers Azure.  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure permet de réduire le risque de perte de données avec la sauvegarde automatique  
Le service de base de données de SQL Server étirer sur Azure protège vos bases de données distantes avec des snapshots de stockage automatique au moins toutes les 8 heures. Il conserve chaque instantané pendant 7 jours pour vous fournir une plage de points de restauration possible.  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure permet de réduire le risque de perte de données avec geo\-redondance  
Les sauvegardes de base de données Azure sont stockés sur geo\-le stockage Azure redondants (RA\-GRS) et sont par conséquent geo\-redondante par défaut. Geo\-stockage redondant réplique vos données pour une zone secondaire des centaines de miles en dehors de la zone primaire. Dans les régions principales et secondaires, vos données sont répliquées trois fois chaque entre les domaines distincts des pannes et mise à niveau. Ceci garantit que vos données sont durables, même dans le cas d’une panne de régionaux complète ou d’une catastrophe qui rend l’une des régions Azure indisponible.

### <a name="stretchRPO"></a>Étirement de base de données permet de réduire le risque de perte de données pour vos données Azure en conservant temporairement les lignes ayant migrés
Une fois la base de données de l’échelle migre les lignes éligibles à partir de SQL Server vers Azure, il conserve les lignes dans la table intermédiaire pour un minimum de 8 heures. Si vous restaurez une sauvegarde de votre base de données Azure, base de données de l’étirement utilise les lignes enregistrées dans la table intermédiaire permet de rapprocher le SQL Server et les bases de données Azure.

Après avoir restauré une sauvegarde de vos données Azure, vous devez exécuter la procédure stockée [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) pour reconnecter l’étirement de la\-activée de la base de données SQL Server pour la base de données Azure à distance. Lorsque vous exécutez **sys.sp_rda_reauthorize_db**, base de données de l’étirement rapproche automatiquement le SQL Server et les bases de données Azure.

Pour augmenter le nombre d’heures de données migrées étirement de la base de données conserve temporairement dans la table intermédiaire, exécutez la procédure stockée [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) et spécifiez un nombre d’heures supérieurs à 8. Pour déterminer la quantité de données à conserver, tenez compte des facteurs suivants :
-   La fréquence des sauvegardes Azure (au moins toutes les 8 heures).
-   Le temps requis après un problème de reconnaître le problème et de décider de restaurer une sauvegarde.
-   La durée de l’opération de restauration Azure.

> [AZURE.NOTE] Augmentation de la quantité de données que l’étirement de la base de données conserve temporairement dans la table intermédiaire augmente la quantité d’espace requise sur le SQL Server.

Pour vérifier le nombre d’heures de données de la base de données de l’étirement conserve actuellement temporairement dans la table intermédiaire, exécutez la procédure stockée [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx).

## <a name="see-also"></a>Voir aussi

[Gérer et dépanner l’étirement de base de données](sql-server-stretch-database-manage.md)

[Sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Sauvegarder et restaurer des bases de données SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
