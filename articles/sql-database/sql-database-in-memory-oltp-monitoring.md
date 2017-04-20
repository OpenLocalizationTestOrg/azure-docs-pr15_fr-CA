<properties
    pageTitle="Surveiller le stockage de mémoire XTP | Microsoft Azure"
    description="Estimation et moniteur de stockage en mémoire XTP utilisent, capacité ; résoudre l’erreur de capacité 41823"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="monitor-in-memory-oltp-storage"></a>Stockage en mémoire OLTP de moniteur

Lors de l’utilisation [De mémoire OLTP](sql-database-in-memory.md), les données dans les tables de mémoire optimisée et des variables de table résident dans le stockage en mémoire OLTP. Chaque niveau de service Premium a une taille de stockage maximale en mémoire OLTP, qui est documentée dans l' [article de niveaux de Service de base de données SQL](sql-database-service-tiers.md#service-tiers-for-single-databases). Une fois cette limite dépassée, insérer et mettre à jour les opérations peuvent démarrer échoue (avec l’erreur 41823). À ce stade seront soit supprimer des données pour récupérer de la mémoire, ou que vous le niveau de performances de votre base de données de mise à niveau.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Déterminer si les données se tiendront dans l’embout de stockage en mémoire

Déterminer l’embout du stockage : consultez l' [article de niveaux de Service de base de données SQL](sql-database-service-tiers.md#service-tiers-for-single-databases) pour les embouts de stockage des différents niveaux service Premium.

Estimation des besoins de mémoire pour un works mémoire optimisée de table identique pour SQL Server comme il le fait dans la base de données de SQL Azure. Prenez quelques minutes pour consulter cette rubrique sur le [site MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Notez que le tableau et les lignes de la variable table, ainsi que les index, sont pris en compte la taille des données utilisateur max. En outre, ALTER TABLE a besoin de suffisamment d’espace pour créer une nouvelle version de toute la table et ses index.

## <a name="monitoring-and-alerting"></a>Surveillance et alerte

Vous pouvez surveiller l’utilisation de stockage en mémoire sous forme de pourcentage de la [cap de stockage pour votre niveau de performances](sql-database-service-tiers.md#service-tiers-for-single-databases) dans Azure [portal](https://portal.azure.com/): 

- Sur la lame de la base de données, recherchez la zone de l’utilisation des ressources, puis cliquez sur Modifier.
- Puis sélectionnez la mesure de `In-Memory OLTP Storage percentage`.
- Pour ajouter une alerte, cliquez sur la zone de l’utilisation des ressources pour ouvrir la lame de mesure, puis cliquez sur Ajouter une alerte.

Ou, utilisez la requête suivante pour afficher l’utilisation du stockage en mémoire :

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Corriger des situations de mémoire insuffisante - erreur 41823

Résultats d’insuffisance de mémoire en cours d’exécution dans les opérations INSERT, UPDATE et CREATE échoue avec le message d’erreur 41823.

41823 message d’erreur indique que les tables de mémoire optimisée et les variables de table ont dépassé la taille maximale.

Pour résoudre cette erreur, soit :


- Supprimer des données dans les tables de mémoire optimisée, potentiellement en déchargeant les données dans des tables traditionnels sur disque ; ou,
- Mise à niveau de la couche de service vers un espace de stockage suffisant en mémoire pour les données que vous souhaitez conserver dans les tables de mémoire optimisée.

## <a name="next-steps"></a>Étapes suivantes
Ressources supplémentaires sur à propos de la [surveillance de base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md)
