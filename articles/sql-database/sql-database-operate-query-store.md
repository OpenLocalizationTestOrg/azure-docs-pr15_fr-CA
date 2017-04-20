<properties
   pageTitle="Fonctionnement du magasin de requête de base de données SQL Azure"
   description="Découvrez comment utiliser le magasin de requête de base de données de SQL Azure"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="operating-the-query-store-in-azure-sql-database"></a>Fonctionnement de la banque de requête de base de données SQL Azure 

Magasin de requête dans Azure est une fonctionnalité de base de données entièrement gérée qui collecte et présente des informations historiques détaillées sur toutes les requêtes. Vous pouvez considérer sur la banque de requête comme semblable à un enregistreur de DONNEES de vol d’un avion considérablement simplifie la résolution des problèmes liés à la fois pour le cloud les performances des requêtes et sur les sites clients. Cet article décrit des aspects spécifiques du fonctionnement du magasin de requête dans Azure. À l’aide de ces données collectées des requêtes, vous pouvez rapidement diagnostiquer et résoudre les problèmes de performances et ainsi consacrer plus de temps à se concentrer sur leurs activités. 

Banque de la requête a été [globalement disponibles](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) dans la base de données de SQL Azure depuis novembre 2015. Magasin de requête est la base pour l’analyse des performances et l’optimisation des fonctionnalités, telles que le [Gestionnaire de base de données SQL et du tableau de bord performances](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Au moment de la publication de cet article, magasin de requête est en cours d’exécution dans plus de 200 000 bases de données utilisateur dans Azure, collecte des informations de requête liées pendant plusieurs mois, sans interruption.

> [AZURE.IMPORTANT] Microsoft est en cours de l’activation de magasin de requête pour toutes les bases de données SQL d’Azure (nouveaux et existants). 

## <a name="optimal-query-store-configuration"></a>Configuration de la banque de requête optimales

Cette section décrit les paramètres par défaut de la configuration optimale qui sont conçus pour garantir un fonctionnement fiable de la banque de requête et fonctionnalités dépendantes, tels que le [Gestionnaire de base de données SQL et du tableau de bord performances](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Configuration par défaut est optimisée pour collection de données en continu, un minimum de temps passé dans les États OFF/READ_ONLY.

| Configuration de | Description | Par défaut | Commentaire |
| ------------- | ----------- | ------- | ------- |
| MAX_STORAGE_SIZE_MB | Spécifie la limite de l’espace de données magasin de requête peut prendre au sein de la base de données de clients z | 100 | Mise en œuvre de nouvelles bases de données |
| INTERVAL_LENGTH_MINUTES | Définit la taille de fenêtre de temps pendant laquelle les statistiques d’exécution collectés pour les plans de requête sont agrégées et rendues persistantes. Chaque plan de requête active a au plus une ligne pour une période de temps définie avec cette configuration | 60   | Mise en œuvre de nouvelles bases de données |
| STALE_QUERY_THRESHOLD_DAYS | Stratégie basée sur le temps de nettoyage qui contrôle la période de rétention de statistiques d’exécution persistantes et des requêtes inactives | 30 | Mise en œuvre de nouvelles bases de données et de bases de données avec la valeur par défaut précédente (367) |
| SIZE_BASED_CLEANUP_MODE | Spécifie si le nettoyage de données automatique a lieu lorsque la taille des données de magasin de requête est proche de la limite | Auto | Mise en œuvre des bases de données |
| QUERY_CAPTURE_MODE | Spécifie si toutes les requêtes ou uniquement un sous-ensemble de requêtes sont suivies. | Auto | Mise en œuvre des bases de données |
| FLUSH_INTERVAL_SECONDS | Spécifie le délai maximal pendant lequel capturées runtime statistiques sont conservées dans la mémoire avant de vider sur le disque | 900 | Mise en œuvre de nouvelles bases de données |
||||||

> [AZURE.IMPORTANT] Ces valeurs par défaut sont automatiquement appliquées dans la dernière étape de l’activation de la banque de requête dans toutes les bases de données SQL d’Azure (voir précédente Remarque importante). Après cette lumière haut, base de données de SQL Azure ne modifier les valeurs de configuration définies par les clients, sauf si elles un impact négatif sur la charge de travail principal ou des opérations fiables de la banque de la requête.

Si vous souhaitez conserver vos paramètres personnalisés, utilisez [Modifier la base de données avec les options de la banque de la requête](https://msdn.microsoft.com/library/bb522682.aspx) pour rétablir la configuration à l’état précédent. Extraire les [Meilleures pratiques avec le magasin de requête](https://msdn.microsoft.com/library/mt604821.aspx) afin d’apprendre comment haut a choisi des paramètres de configuration optimale.

## <a name="next-steps"></a>Étapes suivantes

[Analyse de performances de base de données SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations consultez les articles suivants :

- [Un enregistreur de données de vol de votre base de données](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Analyse des performances à l’aide de la banque de la requête](https://msdn.microsoft.com/library/dn817826.aspx)

- [Scénarios d’utilisation de banque de la requête](https://msdn.microsoft.com/library/mt614796.aspx)

- [Analyse des performances à l’aide de la banque de la requête](https://msdn.microsoft.com/library/dn817826.aspx) 
