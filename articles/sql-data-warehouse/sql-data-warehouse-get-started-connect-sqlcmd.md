<properties
   pageTitle="Interroger le magasin de données SQL Azure (sqlcmd) | Microsoft Azure"
   description="Interrogation d’entrepôt de données de SQL Azure avec l’utilitaire de ligne de commande de sqlcmd."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/06/2016"
   ms.author="barbkess;sonyama"/>

# <a name="query-azure-sql-data-warehouse-sqlcmd"></a>Interroger le magasin de données SQL Azure (sqlcmd)

> [AZURE.SELECTOR]
- [Alimentation BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprentissage automatique Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Cette procédure pas à pas utilise l’utilitaire de ligne de commande [sqlcmd][] pour interroger un magasin de données SQL Azure.  

## <a name="1-connect"></a>1. Connectez-vous

Mise en route de [sqlcmd][], ouvrez l’invite de commande et entrez **sqlcmd** , suivie de la chaîne de connexion pour votre base de données SQL Data Warehouse. La chaîne de connexion requiert les paramètres suivants :

+ **Serveur (-S) :** Serveur sous la forme `<`nom du serveur`>`. database.windows.net
+ **Base de données (-d) :** Nom de la base de données.
+ **Activer les identificateurs entre guillemets (-I) :** Les identificateurs entre guillemets doivent être activés pour vous connecter à une instance SQL Data Warehouse.

Pour utiliser l’authentification SQL Server, vous devez ajouter les paramètres de nom d’utilisateur/mot de passe :

+ **Utilisateur (-U) :** Utilisateur de serveur sous la forme `<`utilisateur`>`
+ **Mot de passe (-P) :** Mot de passe associé à l’utilisateur.

Par exemple, votre chaîne de connexion peut se présenter comme suit :

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Pour utiliser l’authentification intégrée à Active Directory de Azure, vous devez ajouter les paramètres Azure Active Directory :

+ **L’authentification Active Directory azure (-G) :** utiliser Azure Active Directory pour l’authentification

Par exemple, votre chaîne de connexion peut se présenter comme suit :

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [AZURE.NOTE] Vous devez [Activer l’authentification Active Directory Azure](sql-data-warehouse-authentication.md) pour s’authentifier à l’aide d’Active Directory.

## <a name="2-query"></a>2. requête

Après la connexion, vous pouvez émettre des instructions Transact-SQL prises en charge par rapport à l’instance.  Dans cet exemple, les requêtes sont envoyées en mode interactif.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Ces exemples suivant montrent comment vous pouvez exécuter vos requêtes en mode batch à l’aide de l’option -Q ou tuyauterie votre SQL à sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Étapes suivantes

Consultez la [documentation de sqlcmd][sqlcmd] pour plus de détails sur les options disponibles dans sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[Sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
