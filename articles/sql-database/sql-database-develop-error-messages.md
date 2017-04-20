<properties
    pageTitle="Codes d’erreur SQL - erreur de connexion de base de données | Microsoft Azure"
    description="Obtenir des informations sur les codes d’erreur SQL pour les applications clientes de la base de données SQL, comme les erreurs courantes de connexion de base de données, les problèmes de copie de base de données et erreurs générales. "
    keywords="code d’erreur SQL, access sql, erreur de connexion de base de données, les codes d’erreur sql"
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="annemill"/>


# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-error-and-other-issues"></a>Codes d’erreur SQL pour les applications de client de base de données SQL : base de données d’erreur de connexion et d’autres problèmes


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


Cet article répertorie les codes d’erreur SQL pour les applications de client de base de données SQL, y compris les erreurs de connexion de base de données, les erreurs transitoires (également appelés défaillances temporaires), erreurs de gouvernance de ressources, les problèmes de copie de base de données, pool élastique et autres erreurs. La plupart des catégories sont spécifiques à la base de données de SQL Azure et ne s’appliquent pas à Microsoft SQL Server.

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Erreurs de connexion de base de données, les erreurs transitoires et autres erreurs temporaires

Le tableau suivant décrit les codes d’erreur pour les erreurs de perte de connexion et autres erreurs transitoires, que vous pouvez rencontrer lorsque votre application tente d’accéder à SQL de base de données SQL. Pour obtenir des didacticiels sur la façon de se connecter à la base de données de SQL Azure, consultez [connexion à la base de données de SQL Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Erreurs de connexion de base de données courantes et des erreurs transitoires

L’infrastructure Azure a la possibilité de reconfigurer dynamiquement les serveurs cas de charges de travail dans le service de base de données SQL.  Ce comportement dynamique peut entraîner votre programme client de perdre sa connexion à la base de données de SQL. Ce type de condition d’erreur est appelé une *défaillance transitoire*.

Si votre programme client possède une logique, il peut tenter de rétablir une connexion après avoir donné la fois pannes transitoires pour corriger de lui-même.  Nous vous recommandons de vous reporter pendant 5 secondes avant votre première tentative. Nouvelle tentative après un délai plus court que les risques de 5 secondes submerger le service en nuage. Pour chaque tentative ultérieure le délai doit croître de manière exponentielle, avec un maximum de 60 secondes.

Erreurs de défaillance transitoire de manifestent généralement comme l’un des messages d’erreur suivants à partir de votre programme client :

- Base de données < db_name > serveur < Azure_instance > n’est pas disponible actuellement. Recommencez la connexion ultérieurement. Si le problème persiste, contactez le support technique et leur fournir l’ID de suivi de session de < session_id >

- Base de données < db_name > serveur < Azure_instance > n’est pas disponible actuellement. Recommencez la connexion ultérieurement. Si le problème persiste, contactez le support client et leur fournir l’ID de suivi de session de < session_id >. (Microsoft SQL Server, erreur : 40613)

- Une connexion a été fermée par l’hôte distant.

- System.Data.Entity.Core.EntityCommandExecutionException : Une erreur s’est produite lors de l’exécution de la définition de commande. Consultez l’exception interne pour plus de détails. ---> System.Data.SqlClient.SqlException : une erreur de niveau transport s’est produite lors de la réception des résultats à partir du serveur. (fournisseur : fournisseur de Session, erreur : 19 - connexion physique n’est pas utilisable)

Pour obtenir des exemples de code de logique de recherche, voir :

- [Bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md) 
- [Actions pour corriger les erreurs de connexion et les erreurs transitoires dans la base de données de SQL](sql-database-connectivity-issues.md)

Une description de la *période de blocage* pour les clients qui utilisent ADO.NET est disponible dans [Le pool de connexion serveur SQL (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Codes d’erreur de défaillance transitoire

Les erreurs suivantes sont transitoires et devraient être recommencées dans la logique de l’application 

| Code d’erreur | Gravité | Description |
| ---: | ---: | :--- |
| 4060 | 16 | Impossible d’ouvrir la base de données « % & #x2a ; ls » demandée par la connexion. La connexion a échoué. |
|40197|17|Le service a rencontré une erreur lors du traitement de votre demande. Essayez à nouveau. Code d’erreur %d.<br/><br/>Vous recevez cette erreur, lorsque le service est arrêté en raison de logiciels ou mises à niveau matérielles, pannes matérielles ou d’autres problèmes de basculement. Le code d’erreur (%d) incorporé dans le message d’erreur 40197 fournit des informations supplémentaires sur le type de panne ou de basculement qui s’est produite. 40020, 40143, 40166 et 40540 sont des exemples de l’erreur codes sont incorporés dans le message d’erreur 40197.<br/><br/>Reconnexion à votre serveur de base de données SQL se vous connecte automatiquement à une copie saine de votre base de données. Votre application doit intercepter le journal 40197, le code d’erreur incorporé (%d) dans le message pour le dépannage des erreurs et essayez de vous reconnecter à la base de données de SQL jusqu'à ce que les ressources sont disponibles, votre connexion est rétablie.|
|40501|20|Le service est actuellement occupé. Retentez l’opération après 10 secondes. ID d’incident : %ls. Code : %d.<br/><br/>*Remarque :* Pour plus d’informations, voir :<br/>• [Limites de ressource de base de données de SQL azure](sql-database-resource-limits.md).
|40613|17|Base de données '%. & #x2a ; ls' sur le serveur '% & #x2a ; ls' n’est pas disponible actuellement. Recommencez la connexion ultérieurement. Si le problème persiste, contactez le support client et fournir les ID de suivi '% & #x2a ; ls'.|
|49918|16|Impossible de traiter la demande. Ressources insuffisantes pour traiter la demande.<br/><br/>Le service est actuellement occupé. Réessayez la demande ultérieurement. |
|49919|16|Impossible de processus créer ou mettre à jour de la demande. Trop nombreux créer ou mettre à jour les opérations en cours pour l’abonnement « % ld ».<br/><br/>Le service est occupé à traiter plusieurs de créer ou de mettre à jour les demandes de votre abonnement ou votre serveur. Les demandes sont actuellement bloquées pour l’optimisation des ressources. [Sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pour les opérations en attente de la requête. Patientez jusqu'à ce que la création ou mise à jour en attente les demandes sont terminées ou supprimer l’une de vos demandes en attente et renouvelez votre demande ultérieurement. |
|49920|16|Impossible de traiter la demande. Trop d’opérations en cours pour l’abonnement « % ld ».<br/><br/>Le service est occupé à traiter plusieurs demandes pour cet abonnement. Les demandes sont actuellement bloquées pour l’optimisation des ressources. [Sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) , état de l’opération de requête. Attendez jusqu'à ce que demandes en attente sont terminée ou supprimer l’une de vos demandes en attente et renouvelez votre demande ultérieurement. |

## <a name="database-copy-errors"></a>Erreurs de copie de base de données

Les erreurs suivantes peuvent être rencontrées lors de la copie d’une base de données dans la base de données de SQL Azure. Pour plus d’informations, voir [Copier une base de données de SQL Azure](sql-database-copy.md).


|Code d’erreur|Gravité|Description|
|---:|---:|:---|
|40635|16|Client avec l’adresse IP '% & #x2a ; ls' est temporairement désactivé.|
|40637|16|Créez la copie de base de données est actuellement désactivée.|
|40561|16|Échec de la copie de la base de données. Base de données de la source ou la cible n’existe pas.|
|40562|16|Échec de la copie de la base de données. La base de données source a été supprimé.|
|40563|16|Échec de la copie de la base de données. La base de données cible a été supprimé.|
|40564|16|Copie de la base de données a échoué en raison d’une erreur interne. Veuillez supprimer la base de données cible et essayez à nouveau.|
|40565|16|Échec de la copie de la base de données. Pas plus de 1 copie de la base de données simultanées de la même source est autorisé. Veuillez supprimer la base de données cible et réessayez ultérieurement.|
|40566|16|Copie de la base de données a échoué en raison d’une erreur interne. Veuillez supprimer la base de données cible et essayez à nouveau.|
|40567|16|Copie de la base de données a échoué en raison d’une erreur interne. Veuillez supprimer la base de données cible et essayez à nouveau.|
|40568|16|Échec de la copie de la base de données. Base de données source est indisponible. Veuillez supprimer la base de données cible et essayez à nouveau.|
|40569|16|Échec de la copie de la base de données. Base de données cible est indisponible. Veuillez supprimer la base de données cible et essayez à nouveau.|
|40570|16|Copie de la base de données a échoué en raison d’une erreur interne. Veuillez supprimer la base de données cible et réessayez ultérieurement.|
|40571|16|Copie de la base de données a échoué en raison d’une erreur interne. Veuillez supprimer la base de données cible et réessayez ultérieurement.|

## <a name="resource-governance-errors"></a>Erreurs de gestion des ressources

Les erreurs suivantes sont dues à une utilisation excessive des ressources lorsque vous travaillez avec une base de données de SQL Azure. Par exemple :

- Une transaction a été ouverte trop longtemps.
- Une transaction maintient les verrous trop nombreux.
- Une application consomme trop de mémoire.
- Une application consomme trop `TempDb` espace.

Rubriques connexes :

* Informations plus détaillées sont disponibles ici : [limites de ressource de base de données de SQL Azure](sql-database-resource-limits.md)

|Code d’erreur|Gravité|Description|
|---:|---:|:---|
|10928|20|ID de la ressource : %d. La limite de %s pour la base de données est de %d et a été atteint. Pour plus d’informations, consultez [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>L’ID de ressource indique que la ressource qui a atteint la limite. Pour les threads de travail, l’ID de ressource = 1. Pour les sessions, l’ID de ressource = 2.<br/><br/>*Remarque :* Pour plus d’informations sur cette erreur et comment le résoudre, consultez :<br/>• [Limites de ressource de base de données de SQL azure](sql-database-resource-limits.md). |
|10929|20|ID de la ressource : %d. La garantie minimale de %s est %d, limite maximale est de %d et l’utilisation actuelle de la base de données est %d. Toutefois, le serveur est actuellement trop occupé pour prendre en charge les demandes supérieurs à %d pour cette base de données. Pour plus d’informations, consultez [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Dans le cas contraire, veuillez réessayer ultérieurement.<br/><br/>L’ID de ressource indique que la ressource qui a atteint la limite. Pour les threads de travail, l’ID de ressource = 1. Pour les sessions, l’ID de ressource = 2.<br/><br/>*Remarque :* Pour plus d’informations sur cette erreur et comment le résoudre, consultez :<br/>• [Limites de ressource de base de données de SQL azure](sql-database-resource-limits.md).|
|40544|20|La base de données a atteint son quota de taille. Partition ou supprimer des données, drop index, ou consultez la documentation pour les résolutions possibles.|
|40549|16|Session se termine parce que vous avez une transaction longue. Essayez de raccourcir votre transaction.|
|40550|16|La session a été interrompue car elle a acquis le trop grand nombre de verrous. Essayez de lire ou modifier le nombre de lignes dans une transaction unique.|
|40551|16|La session a été interrompue en raison d’excessive `TEMPDB` l’utilisation. Essayez de modifier votre requête afin de réduire l’espace utilisé par une table temporaire.<br/><br/>*Conseil :* Si vous utilisez des objets temporaires, économiser l’espace dans le `TEMPDB` base de données en déplaçant des objets temporaires une fois qu’ils ne sont plus nécessaires par la session.|
|40552|16|La session a été interrompue en raison de l’utilisation de l’espace de journal des transactions excessifs. Essayez de modifier le nombre de lignes dans une transaction unique.<br/><br/>*Conseil :* Si vous effectuez des insertions en bloc à l’aide de la `bcp.exe` utilitaire ou le `System.Data.SqlClient.SqlBulkCopy` classe, essayez d’utiliser la `-b batchsize` ou `BatchSize` options permettant de limiter le nombre de lignes copiées sur le serveur à chaque transaction. Si vous reconstruisez un index avec la `ALTER INDEX` instruction, essayez d’utiliser la `REBUILD WITH ONLINE = ON` option.|
|40553|16|La session a été interrompue en raison de l’utilisation de mémoire excessive. Essayez de modifier votre requête pour traiter moins de lignes.<br/><br/>*Conseil :* Réduction du nombre de `ORDER BY` et `GROUP BY` opérations dans le code Transact-SQL permet de réduire la mémoire de votre requête.|

## <a name="elastic-pool-errors"></a>Erreurs du pool élastique

Les erreurs suivantes sont liées à la création et l’utilisation de Pools d’ELASTIQUES a.

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | Le pool ELASTIQUE a atteint sa limite de stockage. L’utilisation du stockage pour le pool élastique ne doit pas dépasser Mo (%d). | Limite d’espace de pool élastique en Mo. | Essayez d’écrire des données dans une base de données lorsque la limite de stockage du pool ELASTIQUE a été atteint. | Veuillez, envisagez d’augmenter les DTUs du pool élastique si possible afin d’augmenter sa limite de stockage, de réduire le stockage utilisé par les bases de données individuelles au sein du pool élastique, ou supprimer des bases de données à partir du pool élastique. |
| 10929 | EX_USER | La garantie minimale de %s est %d, limite maximale est de %d et l’utilisation actuelle de la base de données est %d. Toutefois, le serveur est actuellement trop occupé pour prendre en charge les demandes supérieurs à %d pour cette base de données. Consultez [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) pour obtenir de l’aide. Dans le cas contraire, veuillez réessayer ultérieurement. | Min DTU par base de données ; Max DTU par base de données | Le nombre total des travailleurs simultanées (demandes) sur toutes les bases de données dans le pool élastique a tenté de dépasser la limite de pool. | Veuillez, envisagez d’augmenter les DTUs du pool élastique si possible afin d’augmenter la limite de son travail, ou supprimer des bases de données à partir du pool élastique. |
| 40844 | EX_USER | Base de données '%ls' serveur '%ls', base de données dans un pool élastique edition '%ls' et ne peut pas avoir une relation continue de copie. | nom de base de données, l’édition de base de données, le nom du serveur | Émission d’une commande de StartDatabaseCopy pour une base de données non premium dans un pool élastique. | À venir |
| 40857 | EX_USER | Pool élastique introuvable pour le serveur : '%ls', le nom du pool élastique : '%ls'. | nom du serveur ; nom du pool élastique | Pool élastique spécifié n’existe pas sur le serveur spécifié. | Veuillez fournir un nom de pool élastique valide. |
| 40858 | EX_USER | Pool élastique '%ls' existe déjà dans le serveur : '%ls' | nom de pool élastique, nom du serveur | Pool élastique spécifié existe déjà sur le serveur logique spécifié. | Fournir le nom du nouveau pool élastique. |
| 40859 | EX_USER | Pool élastique ne gère pas le niveau de service '%ls'. | niveau de service de pool élastique | Niveau de service spécifié n’est pas pris en charge pour la mise en service du pool élastique. | Fournir l’édition appropriée ou rien de niveau de service à utiliser le niveau de service par défaut. |
| 40860 | EX_USER | Combinaison de pool élastique '%ls' et service d’objectif '%ls' n’est pas valide. | nom de pool élastique ; nom objectifs de niveau de service | Élastique objectif de pool et de service peut être spécifié ensemble que si le service est défini en tant que « ElasticPool ». | Spécifiez la combinaison correcte de pool élastique et les objectifs de service. |
| 40861 | EX_USER | L’édition de base de données ' %. *! ne peut pas être différente de la couche de service du pool élastique qui est ' %.* ls'. | edition de la base de données, niveau de service de pool élastique | L’édition de base de données est différente de celui de la couche de service du pool élastique. | Ne spécifiez pas une édition de base de données qui est différente de la couche de service du pool élastique.  Notez que l’édition de base de données n’a pas besoin d’être spécifié. |
| 40862 | EX_USER | Nom du pool élastique doit être spécifié si l’objectif de service du pool élastique. | Aucun | Objectif de service pool élastique n’identifie pas un pool élastique. | Spécifiez le nom du pool élastique à l’aide de l’objectif de service du pool élastique. |
| 40864 | EX_USER | Les DTUs pour le pool d’élasticité doivent être au moins (%d) DTUs de niveau de service ' %. * ls'. | DTUs pour le pool élastique ; niveau de service du pool élastique. | Tentative de définition des DTUs pour le pool élastique au-dessous de la limite minimale. | Veuillez recommencer les DTUs la définition pour l’élastique de pool au moins la limite minimale. |
| 40865 | EX_USER | Les DTUs pour le pool élastique ne doit pas dépasser (%d) DTUs de niveau de service ' %. * ls'. | DTUs pour le pool élastique ; niveau de service du pool élastique. | Tentative de définition des DTUs pour le pool élastique au-dessus de la limite maximale. | Veuillez réessayer la définition des DTUs pour le pool élastique non supérieure à la limite maximale. |
| 40867 | EX_USER | Le DTU maximum par base de données doit être au moins (%d) de niveau de service ' %. * ls'. | Max DTU par base de données ; niveau de service de pool élastique | Tentative de définition de max DTU par base de données inférieures à la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre de votre choix. |
| 40868 | EX_USER | Le DTU maximum par base de données ne doit pas dépasser (%d) de niveau de service ' %. * ls'. | Max DTU par base de données ; niveau de service du pool élastique. | Tentative de définition de max DTU par base de données au-delà de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre de votre choix. |
| 40870 | EX_USER | Minimum DTU par base de données ne doit pas dépasser (%d) de niveau de service ' %. * ls'. | Min DTU par base de données ; niveau de service du pool élastique. | Tentative de définition de DTU minimum par base de données au-delà de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre de votre choix. |
| 40873 | EX_USER | Le nombre de bases de données (%d) et min DTU par base de données (%d) ne peut pas dépasser le DTUs du pool élastique (%d). | Nombre de bases de données dans le pool élastique ; Min DTU par base de données ; DTUs du pool élastique. | Tentative de spécifier min DTU pour les bases de données dans le pool élastique qui dépasse les DTUs du pool élastique. | Envisagez d’augmenter les DTUs du pool élastique, ou diminuer le min DTU par base de données ou diminuer le nombre de bases de données dans le pool élastique. |
| 40877 | EX_USER | Impossible de supprimer un pool élastique à moins qu’il ne contient-elle pas les bases de données. | Aucun | Le pool élastique contient une ou plusieurs bases de données et ne peut donc pas être supprimé. | Veuillez supprimer les bases de données à partir du pool élastique pour pouvoir les pour supprimer. |
| 40881 | EX_USER | Le pool élastique ' %. * ls' a atteint sa limite de nombre de base de données.  La limite de nombre de base de données pour le pool élastique ne doit pas dépasser (%d) pour un pool élastique avec (%d) DTUs. | Nom de pool élastique ; limite de nombre de base de données du pool élastique ; e DTUs pour la liste des ressources. | Essayez de créer ou ajouter la base de données au pool élastique lorsque la limite de nombre de base de données du pool ELASTIQUE a été atteint. | Veuillez, envisagez d’augmenter les DTUs du pool élastique si possible afin d’augmenter la limite de la base de données, ou de supprimer des bases de données à partir du pool élastique. |
| 40889 | EX_USER | La limite de stockage pour le pool élastique ou de DTUs ' %. * ls' ne peut être diminués depuis qui ne fournirait pas d’espace de stockage suffisant pour ses bases de données. | Nom de pool élastique. | Essayez de réduire la limite de stockage du pool élastique sous son utilisation du stockage. | Veuillez, envisagez de réduire l’utilisation du stockage des bases de données dans le pool élastique ou supprimer des bases de données dans le pool afin de réduire son DTUs ou la limite de stockage. |
| 40891 | EX_USER | Minimum DTU par base de données (%d) ne peut pas dépasser le maximum DTU par base de données (%d). | Min DTU par base de données ; DTU par base de données. | Tentative de définition de DTU minimum par supérieure à max DTU par base de données de la base de données. | Vérifiez que DTU minimum par les bases de données ne dépasse pas le maximum DTU par base de données. |
| À DÉFINIR | EX_USER | La taille de stockage pour une base de données individuel dans un pool élastique ne peut pas dépasser la taille maximale autorisée par ' %. * ls' pool élastique de niveau de service. | niveau de service de pool élastique | La taille maximale de la base de données dépasse la taille maximale autorisée par le niveau de service du pool élastique. | Définissez la taille maximale de la base de données dans la limite de la taille maximale autorisée par le niveau de service du pool élastique. |

Rubriques connexes :

* [Créer un pool d’élasticité de la base de données (C#)](sql-database-elastic-pool-create-csharp.md) 
* [Gérer un pool élastique de la base de données (C#)](sql-database-elastic-pool-manage-csharp.md). 
* [Créer un pool de la base de données élastique (PowerShell)](sql-database-elastic-pool-create-powershell.md) 
* [Moniteur et de gérer un pool de la base de données élastique (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Erreurs générales

Les erreurs suivantes ne sont pas dans les catégories précédentes.

|Code d’erreur|Gravité|Description|
|---:|---:|:---|
|15006|16|<AdministratorLogin>n’est pas un nom valide car il contient des caractères non valides.|
|18452|14|Échec de la connexion. La connexion est dans un domaine non approuvé et ne peut pas être utilisée avec Windows authentication.% & x2a de # ; ls (logins Windows ne sont pas pris en charge dans cette version de SQL Server.)|
|18456|14|Échec de la connexion pour l’utilisateur ' % #x2a;ls'.%. & #x2a ; ls %. & #x2a ; ls (Échec de l’ouverture de session de l’utilisateur "% & #x2a ; ls". La modification de mot de passe a échoué. Modification du mot de passe lors de la connexion n'est pas pris en charge dans cette version de SQL Server.)|
|18470|14|Échec de la connexion pour l’utilisateur '% & #x2a ; ls'. Raison : Le compte est disabled.% & x2a de # ; ls|
|40014|16|Plusieurs bases de données ne peut pas être utilisés dans la même transaction.|
|40054|16|Les tables sans un index ordonné en clusters ne sont pas pris en charge dans cette version de SQL Server. Créer un index cluster et essayez à nouveau.|
|40133|15|Cette opération n’est pas pris en charge dans cette version de SQL Server.|
|40506|16|SID spécifié n’est pas valide pour cette version de SQL Server.|
|40507|16|' % & #, x2a ; %.*ls ne peut pas être appelées avec des paramètres dans cette version de SQL Server.|
|40508|16|Instruction d’utilisation n’est pas pris en charge pour basculer entre les bases de données. Utiliser une nouvelle connexion pour vous connecter à une autre base de données.|
|40510|16|Instruction '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server|
|40511|16|Fonction intégrée '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server.|
|40512|16|Fonctionnalités déconseillées '%ls' ne sont pas pris en charge dans cette version de SQL Server.|
|40513|16|Serveur variable '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server.|
|40514|16|'%ls' n’est pas pris en charge dans cette version de SQL Server.|
|40515|16|Référence au nom de la base de données et de serveur en '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server.|
|40516|16|Les objets temporaires globales ne sont pas pris en charge dans cette version de SQL Server.|
|40517|16|Option d’instruction ou mot clé '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server.|
|40518|16|Commande DBCC '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server.|
|40520|16|Classe sécurisable '% S_MSG' est pas pris en charge dans cette version de SQL Server.|
|40521|16|Classe sécurisable '% S_MSG' est pas pris en charge dans l’étendue du serveur dans cette version de SQL Server.|
|40522|16|Type d’entité de sécurité '% & #x2a ; ls' base de données n’est pas pris en charge dans cette version de SQL Server.|
|40523|16|Création de '% & #x2a ; ls' utilisateur implicite n’est pas pris en charge dans cette version de SQL Server. Créer explicitement l’utilisateur avant de l’utiliser.|
|40524|16|Type de données '%. & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server.|
|40525|16|AVEC '%.ls' n’est pas pris en charge dans cette version de SQL Server.|
|40526|16|' % & #x2a ; fournisseur %.*ls non pris en charge dans cette version de SQL Server.|
|40527|16|Les serveurs liés ne sont pas pris en charge dans cette version de SQL Server.|
|40528|16|Impossible de mapper les utilisateurs pour les certificats, les clés asymétriques ou logins Windows dans cette version de SQL Server.|
|40529|16|Fonction intégrée '% & #x2a ; ls' dans l’emprunt d’identité le contexte n’est pas supporté dans cette version de SQL Server.|
|40532|11|Impossible d’ouvrir le serveur « % & #x2a ; ls » demandée par la connexion. La connexion a échoué.|
|40553|16|La session a été interrompue en raison de l’utilisation de mémoire excessive. Essayez de modifier votre requête pour traiter moins de lignes.<br/><br/>*Remarque :* Réduction du nombre de `ORDER BY` et `GROUP BY` opérations dans le code Transact-SQL vous permet de réduire les besoins de mémoire de votre requête.|
|40604|16|Pourrait pas CREATE ou ALTER DATABASE, car il dépasse le quota du serveur.|
|40606|16|Attachement de bases de données n’est pas pris en charge dans cette version de SQL Server.|
|40607|16|Logins Windows ne sont pas pris en charge dans cette version de SQL Server.|
|40611|16|Les serveurs peuvent avoir au maximum 128 les règles de pare-feu définis.|
|40614|16|Adresse IP de début de la règle de pare-feu ne peut pas dépasser l’adresse IP de fin.|
|40615|16|Impossible d’ouvrir le serveur '{0}' demandée par la connexion. Client avec l’adresse IP '{1}' n’est pas autorisé à accéder au serveur.  Pour activer l’accès, utiliser le portail de la base de données SQL ou d’exécuter le sp_set_firewall_rule sur la base de données master pour créer une règle de pare-feu pour cette adresse IP ou une plage d’adresses.  Il peut prendre jusqu'à cinq minutes pour que cette modification prenne effet.|
|40617|16|Le nom de règle de pare-feu qui commence par <rule name> est trop long. La longueur maximale est 128.|
|40618|16|Le nom de la règle de pare-feu ne peut pas être vide.|
|40620|16|Échec de l’ouverture de session de l’utilisateur « % & #x2a ; ls ». La modification de mot de passe a échoué. Modification du mot de passe pendant la connexion n’est pas pris en charge dans cette version de SQL Server.|
|40627|20|Opération sur le serveur '{0}' et la base de données '{1}' est en cours. Veuillez patienter quelques minutes avant de réessayer.|
|40630|16|Échec de la validation du mot de passe. Le mot de passe ne répond pas aux exigences de la stratégie, car il est trop court.|
|40631|16|Le mot de passe que vous avez spécifié est trop long. Le mot de passe doit avoir pas plus de 128 caractères.|
|40632|16|Échec de la validation du mot de passe. Le mot de passe ne répond pas aux exigences de la stratégie, car il n’est pas assez complexe.|
|40636|16|Impossible d’utiliser un nom réservé de la base de données '%. & #x2a ; ls' dans cette opération.|
|40638|16|< Id d’abonnement > id d’abonnement non valide. Abonnement n’existe pas.|
|40639|16|Demande n’est pas conforme au schéma : <schema error>.|
|40640|20|Le serveur a rencontré une exception inattendue.|
|40641|16|L’emplacement spécifié n’est pas valide.|
|40642|17|Le serveur est actuellement trop occupé. Veuillez réessayer ultérieurement.|
|40643|16|La valeur de l’en-tête x-ms-version de spécifié n’est pas valide.|
|40644|14|Impossible d’autoriser l’accès à l’abonnement spécifié.|
|40645|16|Nom_serveur <servername> ne peut pas être null ou vide. Il peut uniquement être composé de lettres minuscules 'a'-'z', les chiffres 0-9 et le trait d’union. Le trait d’union ne peut-être pas entraîner et traçage dans le nom.|
|40646|16|ID de l’abonnement ne peut pas être vide.|
|40647|16|Abonnement < id d’abonnement n’a pas de serveur servername.|
|40648|17|Trop de demandes ont été effectuées. Veuillez réessayer plus tard.|
|40649|16|Content-type non valide est spécifié. Application/xml seul est pris en charge.|
|40650|16|Abonnement < id d’abonnement > n’existe pas ou n’est pas prêt pour l’opération.|
|40651|16|Impossible de créer le serveur car l’abonnement < id d’abonnement > est désactivé.|
|40652|16|Impossible de déplacer ou de créer de serveur. Abonnement < id d’abonnement > va dépasser le quota du serveur.|
|40671|17|Échec de communication entre la passerelle et le service de gestion. Veuillez réessayer plus tard.|
|40852|16|Impossible d’ouvrir la base de données ' %. *! sur le serveur ' %.* ls' demandée par la connexion. Accès à la base de données n’est autorisé qu’à l’aide d’une chaîne de connexion de sécurité activée. Pour accéder à cette base de données, modifier vos chaînes de connexion contiennent « sécurisé » sur le serveur de nom de domaine complet - 'nom_serveur'.database.windows .net doit être modifié pour 'nom_serveur'.database. `secure`. Certificate Revocation Lists.|
|45168|16|Le système de SQL Azure est sous une charge et place une limite supérieure sur les opérations CRUD de base de données simultanées pour un seul serveur (par exemple, créer la base de données). Le serveur spécifié dans le message d’erreur a dépassé le nombre maximal de connexions simultanées. Réessayez ultérieurement.|
|45169|16|Le système SQL azure est sous une charge et place une limite sur le nombre d’opérations de serveur simultané pour un seul abonnement (par exemple, créer un serveur). L’abonnement spécifié dans le message d’erreur a dépassé le nombre maximal de connexions simultanées, et la demande a été refusée. Réessayez ultérieurement.|

## <a name="related-links"></a>Liens connexes

- [Les instructions et les restrictions générales de la base de données SQL Azure](sql-database-general-limitations.md)
- [Limites des ressources de base de données SQL Azure](sql-database-resource-limits.md)
