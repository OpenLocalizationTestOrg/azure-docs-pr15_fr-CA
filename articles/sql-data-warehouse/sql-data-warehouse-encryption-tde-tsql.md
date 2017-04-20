<properties
   pageTitle="Chiffrement transparent des données SQL Data Warehouse (T-SQL) | Microsoft Azure"
   description="Chiffrement transparent des données (TDE) dans le magasin de données SQL (T-SQL)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="get-started-with-transparent-data-encryption-tde"></a>Mise en route avec le chiffrement de données Transparent (TDE)


> [AZURE.SELECTOR]
- [Vue d’ensemble de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Cryptage (portail)](sql-data-warehouse-encryption-tde.md)
- [Cryptage (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Autorisations requises

Pour activer le chiffrement Transparent des données (TDE), vous devez être un administrateur ou un membre du rôle dbmanager.

## <a name="enabling-encryption"></a>Activation du cryptage

Suivez ces étapes pour activer TDE pour un entrepôt de données SQL :

1. Se connecter à la base de données *master* sur le serveur qui héberge la base de données à l’aide d’une connexion d’accès qui est un administrateur ou un membre du rôle dans la base de données master **dbmanager**
2. Exécutez l’instruction suivante pour crypter la base de données.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>La désactivation du cryptage

Suivez ces étapes pour désactiver TDE pour un entrepôt de données SQL :

1. Se connecter à la base de données *master* à l’aide d’une connexion d’accès qui est un administrateur ou un membre du rôle dans la base de données master **dbmanager**
2. Exécutez l’instruction suivante pour crypter la base de données.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] Un entrepôt de données SQL suspendu doit être relancé avant d’apporter des modifications aux paramètres de TDE.

## <a name="verifying-encryption"></a>Vérification du cryptage

Pour vérifier l’état de chiffrement pour un entrepôt de données SQL, suivez les étapes ci-dessous :

1. Se connecter à la base de données *master* ou une instance à l’aide d’une connexion d’accès qui est un administrateur ou un membre du rôle dans la base de données master **dbmanager**
2. Exécutez l’instruction suivante pour crypter la base de données.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Un résultat de ```1``` indique une base de données cryptée, ```0``` indique une base de données non cryptées.

## <a name="encryption-dmvs"></a>Cryptage DMV  

- [Sys.Databases][] 
- [Sys.dm_pdw_nodes_database_encryption_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Sys.Databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[Sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
