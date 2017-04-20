<properties
   pageTitle="Chiffrement transparent des données SQL Data Warehouse (portail) | Microsoft Azure"
   description="Chiffrement transparent des données (TDE) dans le magasin de données SQL"
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

# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Mise en route avec des Transparent données cryptage (TDE) dans le magasin de données SQL

> [AZURE.SELECTOR]
- [Vue d’ensemble de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Cryptage (portail)](sql-data-warehouse-encryption-tde.md)
- [Cryptage (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Autorisations requises

Pour activer le chiffrement Transparent des données (TDE), vous devez être un administrateur ou un membre du rôle dbmanager.

## <a name="enabling-encryption"></a>Activation du cryptage

Pour activer TDE pour un entrepôt de données SQL, suivez les étapes ci-dessous :

1. Ouvrez la base de données dans le [portail Azure](https://portal.azure.com)
2. La lame de la base de données, cliquez sur le bouton **paramètres**
3. Sélectionnez l’option de **chiffrement de données Transparent**![][1]
4. Sélectionnez **le paramètre**![][2]
5. Sélectionnez **Enregistrer**
![][3]  

## <a name="disabling-encryption"></a>La désactivation du cryptage

Pour désactiver TDE pour un entrepôt de données SQL, suivez les étapes ci-dessous :

1. Ouvrez la base de données dans le [portail Azure](https://portal.azure.com)
2. La lame de la base de données, cliquez sur le bouton **paramètres**
3. Sélectionnez l’option de **chiffrement de données Transparent**![][1]
4. Sélectionnez le paramètre **désactivé**![][4]
5. Sélectionnez **Enregistrer**
![][5]  

## <a name="encryption-dmvs"></a>Cryptage DMV

Le cryptage peut être confirmé par les DMV suivantes :

- [Sys.Databases]
- [Sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Sys.Databases]: http://msdn.microsoft.com/library/ms178534.aspx
[Sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
