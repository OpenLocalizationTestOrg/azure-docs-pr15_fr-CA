<properties
   pageTitle="Dépannage de Microsoft d’alimentation BI incorporée"
   description="Dépannage de Microsoft d’alimentation BI incorporée"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Dépannage de Microsoft d’alimentation BI incorporée
Cet article fournit des réponses pour savoir comment résoudre les problèmes **D’alimentation BI incorporé**.

<a name="connection-string"/>
## <a name="setting-sql-server-connection-strings"></a>Définition des chaînes de connexion SQL Server
Pour définir un SQL Server chaîne de connexion, vous devez suivre un format spécifique. Vous trouverez ci-dessous un exemple de chaîne de connexion pour SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Pour en savoir plus sur les chaînes de connexion de SQL Server, consultez les articles suivants :

-   [Chaînes de connexion SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
-   [SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## <a name="setting-credentials"></a>Définition des informations d’identification
Dans le cas où vous disposez d’informations d’identification pour un développement ou un environnement de test, nom d’utilisateur et mot de passe, vous devrez peut-être mettre à jour les informations d’identification qui correspondent à une solution de production.

## <a name="see-also"></a>Voir aussi
- [Mise en route de l’échantillon](power-bi-embedded-get-started-sample.md)
- [Quelle est la puissance BI incorporé](power-bi-embedded-what-is-power-bi-embedded.md)
