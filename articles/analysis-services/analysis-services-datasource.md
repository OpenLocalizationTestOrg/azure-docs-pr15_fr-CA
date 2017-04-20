<properties
   pageTitle="Les connexions de source de données | Microsoft Azure"
   description="Décrit les connexions de source de données pour les modèles de données dans Azure Analysis Services."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="datasource-connections"></a>Connexions de source de données

Modèles de données dans Azure Analysis Services peuvent nécessiter des fournisseurs de données différents lors de la connexion à certaines sources de données. Dans certains cas, les modèles sous forme de tableau de la connexion aux sources de données à l’aide des fournisseurs natifs tels que SQL Server Native Client (SQLNCLI11) peuvent renvoyer une erreur.

Par exemple ; Si vous avez en mémoire ou requête directe du modèle de données qui se connecte à une source de données de nuage comme base de données de SQL Azure, si vous utilisez des fournisseurs natifs que SQLOLEDB, vous pouvez voir le message d’erreur : **« Le fournisseur 'SQLNCLI11.1' n’est pas inscrit »**.

Ou, si vous disposez d’un modèle de DirectQuery la connexion aux sources de données sur site, si vous utilisez des fournisseurs natifs vous pouvez voir le message d’erreur : **« Erreur lors de la création du jeu de lignes OLE DB. Syntaxe incorrecte près de 'Limite' »**.

## <a name="data-source-providers"></a>Fournisseurs de source de données

Les fournisseurs de source de données suivants sont pris en charge en mémoire ou directement des modèles de requête lors de la connexion à des locaux ou des sources de données en nuage :

|               | **Source de données**                     | **En mémoire**                            |  **Requête directe**                                           |
|---------------------------|-------------------------------|---------------------------------------------|---------------------------------------------|
| **Nuage**                     | Entrepôt de données SQL Azure      | Fournisseur de données.NET Framework pour SQL Server | Fournisseur de données.NET Framework pour SQL Server |
|                           | Base de données SQL Azure            | Fournisseur de données.NET Framework pour SQL Server | Fournisseur de données.NET Framework pour SQL Server |
| **Sur site** (via une passerelle) | SQL Server                    | SQL Server Native Client 11.0               | Fournisseur de données.NET Framework pour SQL Server |
|                           |  SQL Server                             | Fournisseur Microsoft OLE DB pour SQL Server    |   Fournisseur de données.NET Framework pour SQL Server                                          |
|                           |  SQL Server                             | Fournisseur de données.NET Framework pour SQL Server |  Fournisseur de données.NET Framework pour SQL Server                                           |
|                           | Oracle                        | Fournisseur Microsoft OLE DB pour Oracle        | Fournisseur de données Oracle pour .NET               |
|                           |  Oracle                             | Fournisseur de données Oracle pour .NET               | Fournisseur de données Oracle pour .NET                                            |
|                           | Teradata                      | Fournisseur OLE DB pour Teradata                | Fournisseur de données de Teradata pour .NET             |
|                           |  Teradata                             | Fournisseur de données de Teradata pour .NET             |  Fournisseur de données de Teradata pour .NET                                            |
|                           | Système de plate-forme Analytique | Fournisseur de données.NET Framework pour SQL Server | Fournisseur de données.NET Framework pour SQL Server |


> [AZURE.NOTE] Assurez-vous que les fournisseurs de 64 bits sont installés lors de l’utilisation sur site passerelle.

Lorsque vous migrez un modèle tabulaire de SQL Server Analysis Services sur site pour Azure Analysis Services, il peut être nécessaire de modifier le fournisseur.

**Pour spécifier un fournisseur de source de données**

1. Dans SSDT > **Explorateur de modèle sous forme de tableau** > **Des Sources de données**, cliquez sur une connexion de source de données, puis cliquez sur **Modifier la Source de données**.

2. Dans **Modifier la connexion**, cliquez sur **Avancé** pour ouvrir la fenêtre de propriétés d’avance.

3. Dans la **Définition des propriétés avancées** > **fournisseurs**, puis sélectionnez le fournisseur approprié.

## <a name="impersonation"></a>Emprunt d’identité
Dans certains cas, il peut être nécessaire de spécifier un compte d’emprunt d’identité différent. Compte d’emprunt d’identité peut être spécifiée dans SSDT ou SSMS.

Pour les sources de données sur site :

- Si vous utilisez l’authentification SQL, l’emprunt d’identité doit être le compte de Service.
- Si vous utilisez l’authentification Windows, la valeur utilisateur/mot de passe Windows. Pour SQL Server, l’authentification Windows avec un compte d’emprunt d’identité spécifique est pris en charge uniquement pour les modèles de données en mémoire.

Pour les sources de données de nuage :

- Si vous utilisez l’authentification SQL, l’emprunt d’identité doit être le compte de Service.


## <a name="next-steps"></a>Étapes suivantes

Si vous avez des sources de données sur site, veillez à installer la [passerelle de locaux](analysis-services-gateway.md). Pour en savoir plus sur la gestion de votre serveur dans SSDT ou SSMS, voir [Gérer votre serveur](analysis-services-manage.md).
