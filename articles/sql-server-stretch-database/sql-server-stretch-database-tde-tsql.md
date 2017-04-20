<properties
   pageTitle="Activer le chiffrement Transparent des données (TDE) pour la base de données SQL Server étirement sur Azure TSQL | Microsoft Azure"
   description="Activer le chiffrement Transparent des données (TDE) pour la base de données SQL Server étirement sur Azure TSQL"
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
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Activer le chiffrement Transparent des données (TDE) pour la base de données étendu sur Azure (Transact-SQL)
> [AZURE.SELECTOR]
- [Azure portal](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Chiffrement de données transparent (TDE) protège contre la menace des activités malveillantes en effectuant le cryptage en temps réel et décryptage de la base de données, les sauvegardes associées et fichiers journaux des transactions inactives sans nécessiter de modifications à l’application.

TDE crypte le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de cryptage de la base de données. La clé de chiffrement de base de données est protégée par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur Azure. Microsoft fait automatiquement pivote ces certificats au moins tous les 90 jours. Pour une description générale du TDE, consultez [Chiffrement Transparent des données (TDE)].

##<a name="enabling-encryption"></a>Activation du cryptage

Pour activer un Azure TDE base de données qui stocke les données migrées à partir d’une base de données de SQL Server prenant en charge l’étirement, effectuer les opérations suivantes :

1. Se connecter à la base de données *master* sur le serveur Azure qui héberge la base de données à l’aide d’une connexion d’accès qui est un administrateur ou un membre du rôle dans la base de données master **dbmanager**
2. Exécutez l’instruction suivante pour crypter la base de données.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##<a name="disabling-encryption"></a>La désactivation du cryptage

Pour désactiver les TDE pour un Azure base de données qui stocke les données migrées à partir d’une base de données de SQL Server prenant en charge l’étirement, effectuer les opérations suivantes :

1. Se connecter à la base de données *master* à l’aide d’une connexion d’accès qui est un administrateur ou un membre du rôle dans la base de données master **dbmanager**
2. Exécutez l’instruction suivante pour crypter la base de données.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##<a name="verifying-encryption"></a>Vérification du cryptage

Pour vérifier l’état de cryptage d’une base de données Azure qui stocke les données migrées à partir d’une base de données de SQL Server prenant en charge l’étirement, effectuez les opérations suivantes :

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


<!--Anchors-->
[Chiffrement transparent des données (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
