<properties
    pageTitle="Quelles sont les nouveautés dans SQL de base de données V12 | Microsoft Azure"
    description="Explique pourquoi des systèmes métier qui sont à l’aide de base de données de SQL Azure dans le nuage pourra bénéficier d’une mise à niveau vers la version V12."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="genemi"/>


# <a name="whats-new-in-sql-database-v12"></a>Quelles sont les nouveautés dans V12 de base de données SQL


Cette rubrique décrit les nombreux avantages dont la nouvelle version V12 de base de données de SQL Azure par rapport à la version 11.


Nous continuons à ajouter des fonctionnalités à V12. Par conséquent, nous vous encourageons à visiter notre page de mises à jour de Service Web pour Azure et d’utiliser ses filtres :


- Filtré au [service de base de données de SQL](https://azure.microsoft.com/updates/?service=sql-database).
- Filtré à disponibilité générale [les annonces (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) pour les fonctionnalités de base de données SQL.


Les dernières informations sur les limites des ressources de base de données de SQL sont documentées dans :<br/>[Les limites des ressources de base de données SQL azure](sql-database-resource-limits.md).


## <a name="increased-application-compatibility-with-sql-server"></a>Compatibilité accrue des applications avec SQL Server


Un objectif clé de V12 de base de données SQL était d’améliorer la compatibilité avec Microsoft SQL Server 2014 et à maintenir la compatibilité que de la commercialisation des nouvelles versions de SQL Server. Parmi les autres zones, V12 garantit la parité avec SQL Server dans la zone importante de programmabilité. Par exemple :

- [Prise en charge intégrée de JSON](https://msdn.microsoft.com/library/dn921897.aspx)

- [Les fonctions de fenêtre](http://msdn.microsoft.com/library/ms189798.aspx), [sur](http://msdn.microsoft.com/library/ms189461.aspx)

- [Index XML](http://msdn.microsoft.com/library/bb934097.aspx) et [des index XML sélectives](http://msdn.microsoft.com/library/jj670104.aspx)

- [Suivi des modifications](http://msdn.microsoft.com/library/bb933875.aspx)

- [SÉLECTIONNEZ... DANS](http://msdn.microsoft.com/library/ms188029.aspx)

- [Recherche de texte intégral](http://msdn.microsoft.com/library/ms142571.aspx)

- [MODIFIER CONFIGURATION inclus dans l’étendue de base de données (Transact-SQL)](http://msdn.microsoft.com/library/mt629158.aspx)

Consultez [ici](sql-database-transact-sql-information.md) pour l’ensemble des fonctionnalités non prises en charge dans la base de données de SQL.


### <a name="compatibility-level-130"></a>Niveau de compatibilité de 130


> [AZURE.IMPORTANT] À compter de **juin 2016**, *nouvellement* créé les bases de données sur V12 de base de données SQL Azure que leur niveau de compatibilité démarrent en 130, qui correspond à Microsoft SQL Server 2016 GA.
> 
> Vous pouvez utiliser `ALTER DATABASE YourDatabase SET COMPATIBILITY_LEVEL = 120` si vous le souhaitez.
> 
> Bases de données créées avant juin 2016 n’ont pas leur niveau de compatibilité modifié par ce changement de valeur par défaut. Ni le niveau d’une base de données modifiée par la mise à niveau à partir de 11 à V12.



Pour obtenir une explication de la façon dont vous pouvez comparer vos requêtes plus importantes entre plus tard par rapport au niveau précédent de compatibilité, voir :

- [Meilleures performances des requêtes avec un niveau de compatibilité de 130 dans la base de données SQL Azure](sql-database-compatibility-level-query-performance-130.md)



## <a name="more-premium-performance-new-performance-levels"></a>Plus performances de nouveaux niveaux de performances, premium


Dans V12, nous avons augmenté les unités de Transaction de base de données (DTUs) allouée à tous les niveaux de performances de prime de 25 %, sans coût supplémentaire. Des gains de performances encore Pluss peuvent être obtenus avec les nouvelles fonctionnalités telles que :


- Prise en charge pour les [index de columnstore](http://msdn.microsoft.com/library/gg492153.aspx)en mémoire.
- [Le partitionnement de table par des lignes](http://msdn.microsoft.com/library/ms187802.aspx) avec des améliorations liées à [l’Instruction TRUNCATE TABLE](http://msdn.microsoft.com/library/ms177570.aspx).
- La disponibilité de la gestion dynamique des vues [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx) pour vous aider à surveiller et optimiser les performances.


### <a name="reliable-performance"></a>Performances fiables


Si votre programme client se connecte à SQL de base de données V12 alors que votre client s’exécute sur une machine virtuelle Azure (VM), vous devez ouvrir les plages de ports suivants sur l’ordinateur virtuel :

- 11000-11999
- 14000-14999


Cliquez [ici](sql-database-develop-direct-route-ports-adonet-v12.md) pour plus d’informations sur les ports pour V12 de base de données SQL. Les ports sont requis par les améliorations des performances dans V12 de base de données SQL.


## <a name="better-support-for-cloud-saas-vendors"></a>Meilleur support des cloud SaaS fournisseurs


Uniquement dans V12, nous avons publié le nouveau niveau de performances Standard S3 et la présentation publique de [pools d’élastique de la base de données](sql-database-elastic-pool.md). Les pools élastique de la base de données est une solution conçue pour le cloud SaaS fournisseurs.  Avec les pools élastique de la base de données, vous pouvez :


- Partage des DTUs entre les bases de données pour réduire les coûts pour un grand nombre de bases de données.
- Exécuter [les tâches de base de données élastique](sql-database-elastic-jobs-overview.md) pour gérer des bases de données à grande échelle.


## <a name="security-enhancements"></a>Améliorations de la sécurité


La sécurité est une préoccupation pour quiconque exécute leur entreprise dans le nuage. Les dernières fonctionnalités de sécurité publiées dans V12 sont les suivantes :


- [Sécurité de niveau ligne](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [Masquage de données dynamiques](sql-database-dynamic-data-masking-get-started.md)
- [Des bases de données](http://msdn.microsoft.com/library/ff929188.aspx)
- [Les rôles d’application](http://msdn.microsoft.com/library/ms190998.aspx) gérés avec GRANT, DENY, REVOKE
- [Chiffrement de données transparent](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [Connexion à la base de données SQL à l’aide de l’authentification Azure Active Directory](sql-database-aad-authentication.md)
 - Base de données de SQL prend désormais en charge l’authentification Azure Active Directory, un mécanisme de connexion à la base de données de SQL à l’aide des identités dans Azure Active Directory (AD Azure). Avec l’authentification Azure Active Directory vous pouvez gérer de façon centralisée l’identité des utilisateurs de la base de données et d’autres services de Microsoft dans un emplacement central.
- [Toujours cryptées](https://msdn.microsoft.com/library/mt163865.aspx) (en aperçu) rend le cryptage transparent pour les applications et permet aux clients de crypter les données sensibles à l’intérieur des applications clientes sans partager les clés de cryptage avec la base de données de SQL.


## <a name="increased-business-continuity-when-recovery-is-needed"></a>A augmenté la continuité d’activité lors de la récupération est nécessaire.


V12 améliorée point de récupération (RPO) et reprise estimé fois (ERTs) :


| Fonctionnalité de continuité d’activité d’entreprise | Version antérieure | V12 |
| :-- | :-- | :-- |
| Geo-restauration | • RPO < 24 heures.<br/>• Insérer < 12 heures. | • RPO < 1 heure.<br/>• Insérer < 12 heures. |
| Geo-réplication Active | • RPO < 5 minutes.<br/>• Insérer < 1 heure. | • Secondes de < 5 RPO.<br/>• Secondes de 30 < Insérer. |


Pour plus d’informations, reportez-vous à la section [continuité d’activité de la base de données SQL](sql-database-business-continuity.md) .


## <a name="more-reasons-to-upgrade-now"></a>Pourquoi mettre à niveau maintenant


Il existe de nombreuses bonnes raisons de pourquoi les clients doivent mettre à jour maintenant V12 de base de données SQL Azure à partir de 11 :


- V12 de base de données SQL possède une longue liste de fonctionnalités au-delà de 11.
- Nous continuons à ajouter de nouvelles fonctionnalités à V12, mais aucuns nouvelles fonctionnalités ne seront ajoutés à la V11.
- La plupart des nouvelles fonctionnalités V12 de base de données SQL sont publiées avant leur publication pour Microsoft SQL Server.


## <a name="are-you-using-v12-already"></a>Si vous utilisez V12 déjà ?


Une méthode simple pour voir si vous avez une base de données ou le serveur logique en cours d’exécution sur une version antérieure du service de base de données SQL consiste à effectuer les opérations suivantes :


1. Accédez au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir**.
3. Cliquez sur **les serveurs SQL**.
4. L’icône en regard de votre serveur ou de base de données indique le récit :
 - ![Icône pour un serveur v12](./media/sql-database-v12-whats-new/v12_icon.png) **serveur logique de V12**
 - ![Icône de serveur de version antérieure](./media/sql-database-v12-whats-new/earlier_icon.png) **serveur logique des version antérieur**


Une autre technique pour déterminer la version consiste à exécuter la `SELECT @@version;` instruction dans votre base de données et afficher les résultats similaires à :


- **12**.0.2000.10 &nbsp; *(version V12)*
- **11**.0.9228.18 &nbsp; *(version 11)*


Une base de données V12 peut être hébergé sur un serveur logique V12 uniquement. Et un serveur V12 peut héberger des bases de données V12 uniquement.


Si vous n’exécutez pas de V12, vous pouvez mettre à niveau votre serveur logique en suivant les étapes de [mise à niveau vers V12 de base de données SQL en place](sql-database-v12-plan-prepare-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a>Régions de disponibilité générales


- Par le 31 juillet 2015, toutes les régions avaient été promues à disponibilité générale.
- V12 a été publiée en décembre 2014, mais uniquement au niveau de l’état de l’aperçu.

[Les conditions supplémentaires d’utilisation pour les prévisualisations de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
