<properties
   pageTitle="Dépannage de SQL Azure Data Warehouse | Microsoft Azure"
   description="Dépannage de SQL Azure Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# <a name="troubleshooting-azure-sql-data-warehouse"></a>Dépannage de SQL Azure Data Warehouse

Cette rubrique répertorie certaines des questions de dépannage plus courantes que nous attendons de nos clients.

## <a name="connecting"></a>Connexion

| Problème                              | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Échec de la connexion pour l’utilisateur 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, erreur : 18456) | Cette erreur se produit lorsqu’un utilisateur DAS essaie de se connecter à la base de données master, mais ne dispose pas d’un utilisateur dans master.  Pour corriger ce problème soit spécifier l’entrepôt de données SQL que vous souhaitez vous connecter au moment de la connexion ou ajouter l’utilisateur à la base de données master.  Consultez l’article de [Présentation de la sécurité][] pour plus de détails.|
|Le serveur principal « MyUserName » n’est pas en mesure d’accéder à la base de données « master » dans le contexte de sécurité actuel. Ne peut pas ouvrir la base de données utilisateur par défaut. Échec de la connexion. Échec de la connexion pour l’utilisateur 'MyUserName'. (Microsoft SQL Server, erreur : 916) | Cette erreur se produit lorsqu’un utilisateur DAS essaie de se connecter à la base de données master, mais ne dispose pas d’un utilisateur dans master.  Pour corriger ce problème soit spécifier l’entrepôt de données SQL que vous souhaitez vous connecter au moment de la connexion ou ajouter l’utilisateur à la base de données master.  Consultez l’article de [Présentation de la sécurité][] pour plus de détails.|
| Erreur CTAIP                        | Cette erreur peut se produire lorsqu’une connexion a été créée sur la base de données master du serveur SQL, mais pas dans la base de données SQL Data Warehouse.  Si vous rencontrez cette erreur, examinez l’article de [Présentation de la sécurité][] .  Cet article explique comment créer créer une connexion et un utilisateur sur le masque, puis comment créer un utilisateur dans la base de données SQL Data Warehouse.|
| Bloqué par le pare-feu                |Bases de données SQL Azure sont protégés par un pare-feu au niveau serveur et de base de données pour garantir connue uniquement des adresses IP ont accès à une base de données. Les pare-feu sont sécurisées par défaut, ce qui signifie que vous devez activer explicitement et adresse IP ou plage d’adresses avant de vous connecter.  Pour configurer votre pare-feu pour l’accès, suivez les étapes [configurer l’accès serveur pare-feu pour votre adresse IP du client][] dans les [instructions de provisionnement][].|
| Impossible de se connecter avec un outil ou d’un pilote | SQL Data Warehouse recommande l’utilisation de [SSMS][], [SSDT pour Visual Studio 2015][]ou [sqlcmd][] pour interroger les données. Pour plus d’informations sur les pilotes et la connexion au magasin de données SQL, consultez les articles de [pilotes pour l’entrepôt de données SQL Azure][] et [se connecter au magasin de données SQL Azure][] .|


## <a name="tools"></a>Outils

| Problème                              | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Explorateur d’objets Visual Studio manque aux utilisateurs du DAS | Il s’agit d’un problème connu.  Pour résoudre ce problème, afficher les utilisateurs dans [sys.database_principals][].  Consultez [l’authentification à l’entrepôt de données SQL Azure][] pour en savoir plus sur l’utilisation de Active Directory de Azure avec SQL Data Warehouse.|

## <a name="performance"></a>Performances

|  Problème                             | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Résolution des problèmes de performances de requête  | Si vous tentez de résoudre une requête, commencez par [apprendre à surveiller vos requêtes][].|
| Les plans et les faibles performances de requête est souvent un résultat de statistiques manquantes   | La cause la plus courante de performances médiocres est le manque de statistiques sur les tables.  Afficher les [statistiques de table maintenance] [ Statistics] pour plus d’informations sur la création de statistiques et pourquoi elles sont essentielles à votre performance.|
| Accès concurrentiel faible / en file d’attente des requêtes   | Présentation de la [gestion de la charge de travail][] est importante pour comprendre comment équilibrer l’allocation de mémoire à accès concurrentiel.|
| La mise en œuvre des meilleures pratiques    | Le mieux place pour commencer à apprendre comment améliorer les performances de la requête est [SQL Data Warehouse][] de bonnes pratiques.|
| Comment faire pour améliorer les performances de la mise à l’échelle  | La solution à l’amélioration des performances est parfois simplement à ajouter que plus de calcul puissance à vos requêtes en [mise à l’échelle de votre magasin de données SQL][].|
| Médiocrité des performances en raison de la qualité d’index faibles | Les requêtes parfois peuvent ralentissement en raison de la [qualité d’index columnstore médiocre][].  Consultez cet article pour plus d’informations et la [reconstruction des index pour améliorer la qualité du segment][].|

## <a name="system-management"></a>Gestion du système

|  Problème                             | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Msg 40847 : N’a pas pu effectuer l’opération car le serveur dépasse le quota autorisé unité de Transaction de base de données de 45000. | Soit réduire la [DWU][] de la base de données que vous essayez de créer ou de [demander une augmentation des quotas][].|
| Étudier l’utilisation de l’espace    | Voir les [tailles de tables][] afin de comprendre l’utilisation de l’espace de votre système.|
| Aide concernant la gestion des tables          | Consultez la [vue d’ensemble de la Table] [ Overview] l’article de l’aide à la gestion de vos tables.  Cet article contient également des liens à des rubriques plus détaillées comme des [types de données de Table][Data types], [distribuer une table][Distribute], [l’indexation d’un tableau][Index], [partitionnement d’une table][Partition], [statistiques de la table maintenance] [ Statistics] et [tables temporaires][Temporary].|

## <a name="polybase"></a>Polybase

|  Problème                             | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Erreur UTF-8                        |  PolyBase seulement prend en charge le chargement de fichiers de données qui ont été codés en UTF-8.  Pour obtenir des instructions sur la façon de contourner cette limitation, consultez [utilisation autour de la spécification PolyBase UTF-8][] .|
| Chargement échoue en raison de lignes de grande taille   | Actuellement prise en charge de la ligne de grande taille n’est pas disponible pour Polybase.  Cela signifie que si votre table contient varchar (max), nvarchar (max) ou varbinary (max), tables externes ne peut pas être utilisés pour charger vos données.  Pour les grandes lignes des charges est actuellement pris en charge uniquement par le biais de Azure Data Factory (avec BCP), Azure flux Analytique, SSIS, BCP ou la classe SQLBulkCopy de .NET. Prise en charge de PolyBase de lignes de grande taille sera ajouté dans une version ultérieure.|
| Échec de chargement de bcp de table avec le type de données MAX | Il existe un problème connu qui nécessite que varchar (max), nvarchar (max) ou varbinary (max) est placé à la fin de la table dans certains scénarios.  Essayez de déplacer votre nombre maximal de colonnes à la fin de la table.|

## <a name="differences-from-sql-database"></a>Différences à partir de la base de données SQL

|  Problème                             | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Fonctionnalités de base de données SQL non pris en charge  | Reportez-vous à la section [fonctions de tableau non pris en charge][].|
| Types de données de la base de données SQL non pris en charge  | Voir [types de données non pris en charge][].|
| Limitations de la mise à jour et de suppression      | Reportez-vous à la section [solutions de contournement mise à jour][], de [Supprimer des solutions de contournement][] et de [SACT de l’aide pour contourner la syntaxe non prise en charge de la mise à jour et de suppression][].  |
| Instruction MERGE n’est pas pris en charge.   | Reportez-vous à la section [solutions de contournement de la fusion][].|
| Limitations de la procédure stockée       | Consultez les [limitations de procédure stockée][] pour comprendre certaines des limitations des procédures stockées.|
| UDF ne prennent pas en charge les instructions SELECT | Il s’agit d’une limitation actuelle de notre UDF.  Pour la syntaxe que nous prenons en charge, reportez-vous à la section [CREATE FUNCTION][] .   |

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes ont été impossible de trouver une solution à votre problème ci-dessus, voici quelques autres ressources que vous pouvez essayer.

- [Blogs]
- [Demandes de fonctionnalités]
- [Vidéos]
- [Blogs de l’équipe CAT]
- [Créer le ticket de support]
- [Forum MSDN]
- [Forum de débordement de pile]
- [Twitter]

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la sécurité]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT pour Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Pilotes pour l’entrepôt de données SQL Azure]: ./sql-data-warehouse-connection-strings.md
[Se connecter au magasin de données SQL Azure]: ./sql-data-warehouse-connect-overview.md
[Créer le ticket de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Mise à l’échelle de votre magasin de données SQL]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[demander une augmentation des quotas]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[Apprendre à surveiller vos requêtes]: ./sql-data-warehouse-manage-monitor.md
[Instructions de mise en service]: ./sql-data-warehouse-get-started-provision.md
[Configurer l’accès au serveur pare-feu pour votre adresse IP du client]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Meilleures pratiques de SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Tailles de tableau]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Fonctions de tableau non pris en charge]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Types de données non pris en charge]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Qualité des index columnstore faible]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Reconstruction des index pour améliorer la qualité de segment]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Gestion de la charge de travail]: ./sql-data-warehouse-develop-concurrency.md
[Pour contourner la syntaxe non prise en charge de la mise à jour et de suppression à l’aide de SACT]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[Solutions de contournement mise à jour]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[SUPPRIMER des solutions de contournement]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[FUSIONNER des solutions de contournement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Limitations de la procédure stockée]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentification à l’entrepôt de données SQL Azure]: ./sql-data-warehouse-authentication.md
[Travail autour de la spécification PolyBase UTF-8]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[Sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CRÉER FONCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[Sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Forum de débordement de pile]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

