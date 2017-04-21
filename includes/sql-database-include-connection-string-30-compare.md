
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Comparer la chaîne de connexion


Le tableau suivant compare les chaînes de connexion, votre programme C# doit se connecter à votre de SQL Server sur site par rapport à votre base de données de SQL Azure dans le nuage. Les différences sont en gras.


| Chaîne de connexion pour<br/>Base de données SQL Azure | Chaîne de connexion pour<br/>Microsoft SQL Server |
| :-- | :-- |
| Server =**tcp :**{your_serverName_here}**. database.windows.net,1433**;<br/>ID utilisateur = {your_loginName_here}**@{your_serverName_here}**;<br/>Mot de passe = {your_password_here} ;<br/>**Base de données = {your_databaseName_here} ;**<br/>**Connection Timeout = 30**;<br/>**Crypter = True**;<br/>**TrustServerCertificate = False**; | Serveur = {your_serverName_here} ;<br/>ID utilisateur = {your_loginName_here} ;<br/>Mot de passe = {your_password_here} ; |


Le **base de données =** est facultatif pour SQL Server, mais qui est requise pour la base de données de SQL.


[Propriétés de SqlConnectionStringBuilder ADO .NET](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) - aborde en détail tous les paramètres.


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
