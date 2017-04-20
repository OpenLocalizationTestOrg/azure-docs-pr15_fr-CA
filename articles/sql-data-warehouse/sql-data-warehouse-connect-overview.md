<properties
   pageTitle="Se connecter à SQL Azure Data Warehouse | Microsoft Azure"
   description="Comment faire pour rechercher le serveur de connexion et le nom de chaîne pour votre à l’entrepôt de données SQL Azure"
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
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# <a name="connect-to-azure-sql-data-warehouse"></a>Se connecter au magasin de données SQL Azure

Cet article vous aide à vous connecter à l’entrepôt de données SQL pour la première fois.

## <a name="find-your-server-name"></a>Trouver le nom de votre serveur

La première étape à la connexion au magasin de données SQL est de savoir comment trouver le nom de votre serveur.  Par exemple, le nom du serveur dans l’exemple suivant est sample.database.windows.net. Pour trouver le nom du serveur complet :

1. Accédez au [portail Azure][].
2. Cliquez sur **bases de données SQL** 
3. Cliquez sur la base de données à laquelle que vous souhaitez vous connecter.
4. Recherchez le nom de serveur complet.

    ![Nom de serveur complet][1]

## <a name="supported-drivers-and-connection-strings"></a>Pilotes pris en charge et les chaînes de connexion

Entrepôt de données SQL Azure prend en charge [ADO.NET][], [ODBC][], [PHP][]et [JDBC][]. Cliquez sur l’un des pilotes précédents pour trouver la dernière version et documentation. Pour générer automatiquement la chaîne de connexion pour le pilote que vous utilisez à partir du portail Azure, vous pouvez cliquer sur **Afficher les chaînes de connexion de base de données** à partir de l’exemple précédent.  Voici également quelques exemples de quoi ressemble une chaîne de connexion pour chaque pilote.

> [AZURE.NOTE] Envisagez de définir le délai d’expiration de la connexion à 300 secondes pour permettre à votre connexion de résister à courtes périodes d’indisponibilité.

### <a name="adonet-connection-string-example"></a>Exemple de chaîne de connexion ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exemple de chaîne de connexion ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exemple de chaîne de connexion PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exemple de chaîne de connexion JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Paramètres de connexion

SQL Data Warehouse standardise certains paramètres lors de la connexion et de création de l’objet. Ces paramètres ne peuvent pas être substituées et comprennent :

| Paramètre de base de données       | Valeur                        |
| :--------------------- | :--------------------------- |
| [ANSI_NULLS][]         | ON                           |
| [QUOTED_IDENTIFIER][] | ON                           |
| [DATEFORMAT][]         | MJA                          |
| [DATEFIRST][]          | 7                            |

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter et interroger avec Visual Studio, consultez [requêtes avec Visual Studio][]. Pour en savoir plus sur les options d’authentification, consultez [authentification au magasin de données SQL Azure][].

<!--Articles-->
[Requête avec Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentification à l’entrepôt de données SQL Azure]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIER]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png


