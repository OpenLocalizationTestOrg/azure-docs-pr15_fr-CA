< propriétés
    
    pageTitle="Upgrade to the latest elastic database client library | Microsoft Azure" 
    description="Upgrade apps and libraries using Nuget" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Mise à niveau d’une application pour utiliser la bibliothèque de client plus récente élastique de la base de données

Nouvelles versions de la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md) sont disponibles par le biais de NuGetand de l’interface du Gestionnaire de NuGetPackage dans Visual Studio. Mises à jour contient des correctifs de bogues et prise en charge de nouvelles fonctionnalités de la bibliothèque cliente.

**Pour la version la plus récente :** Accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Régénérez votre application avec la nouvelle bibliothèque, ainsi que modifier votre gestionnaire de mappage Éclater les métadonnées existantes stockées dans les bases de données SQL Azure pour prendre en charge les nouvelles fonctionnalités.

Effectuez ces étapes dans l’ordre permet de s’assurer que les versions anciennes de la bibliothèque de client ne sont plus présentes dans votre environnement lors de la mise à jour des objets de métadonnées, qui signifie que les objets de métadonnées de l’ancienne version ne sont pas créés après la mise à niveau.   

## <a name="upgrade-steps"></a>Étapes de mise à niveau

**1. mise à niveau de vos applications.** Dans Visual Studio, téléchargez et faire référence à la dernière version de la bibliothèque client dans tous vos projets de développement qui utilisent la bibliothèque ; Ensuite, régénérez et déployer. 

 * Dans votre solution Visual Studio, sélectionnez **Outils** --> **Du Gestionnaire de package NuGet** -->  **Manage NuGet Packages de Solution**. 
 * (2013 de Visual Studio) Dans le panneau de gauche, sélectionnez **mises à jour**et cliquez sur le bouton de **mise à jour** dans le package de la **Bibliothèque cliente élastique échelle d’Azure SQL de base de données** qui s’affiche dans la fenêtre.
 * (2015 de Visual Studio) Mise à **niveau disponible**, définissez la zone de filtre. Sélectionnez le package à mettre à jour, puis cliquez sur le bouton **mettre à jour** .
    
 
 * Générer et déployer. 

**2. mise à niveau de vos scripts.** Si vous utilisez des scripts **PowerShell** pour gérer le milieu des fragments, [Téléchargez la nouvelle version de la bibliothèque](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) et le copier dans le répertoire à partir duquel vous exécutez les scripts. 

**3. mise à niveau de votre service de fusion et fractionnement.** Si vous utilisez l’outil de fusion et fractionnement de base de données élastique à réorganiser les données sharded, [télécharger et déployer la dernière version de l’outil](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Pour le Service peut être trouvé une mise à niveau procédure détaillée [ici](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. mise à niveau de vos bases de données du Gestionnaire de table partagé**. Mettre à niveau les métadonnées de vos cartes partagé de prise en charge dans la base de données de SQL Azure.  Il existe deux méthodes que vous pouvez y parvenir à l’aide de PowerShell ou C#. Les deux options sont présentées ci-dessous.

***Option 1 : Mettre à niveau les métadonnées à l’aide de PowerShell***

1. Télécharger l’utilitaire de ligne de commande les plus récentes de NuGet [ici](http://nuget.org/nuget.exe) et les enregistrer dans un dossier. 

2. Ouvrez une invite de commande, naviguez dans le même dossier et émettre la commande :`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Recherchez le sous-dossier contenant la nouvelle version DLL de client que vous avez simplement téléchargé, par exemple :`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. Télécharger le scriptlet de mise à niveau du client élastique de la base de données à partir du [Centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)et enregistrez-le dans le même dossier que celui contenant la DLL.

5. À partir de ce dossier, exécutez « PowerShell.\upgrade.ps1 » à partir de l’invite de commande et suivez les invites.
 
***Option 2 : Mettre à niveau les métadonnées à l’aide de C#***

Vous pouvez également créer une application Visual Studio qui ouvre votre ShardMapManager, effectue une itération sur tous les milieu des fragments et effectue la mise à niveau des métadonnées en appelant les méthodes [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) et [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) , comme dans cet exemple : 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 
    
    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Ces techniques de mises à niveau de métadonnées peuvent être appliqués plusieurs fois sans endommager. Par exemple, si une version antérieure de client crée par inadvertance un partagé une fois que vous avez déjà mis à jour, vous pouvez exécuter à nouveau mise à niveau sur tous les milieu des fragments pour s’assurer que la dernière version de métadonnées est présente dans l’ensemble de votre infrastructure. 

**Remarque :**  Nouvelles versions de la bibliothèque cliente publiés à ce jour de continuer à travailler avec les versions antérieures des métadonnées du Gestionnaire de table partagé sur la base de données SQL Azure et vice versa.   Cependant, pour tirer parti de certaines des nouvelles fonctionnalités dans la dernière version du client, métadonnées doivent être mis à niveau.   Notez que mises à niveau des métadonnées n’affectent pas les données de l’utilisateur ni de données spécifiques à l’application, seuls les objets créés et utilisés par le Gestionnaire de table partagé.  Et les applications continuent à fonctionner à travers la séquence de mise à niveau décrite ci-dessus. 

## <a name="elastic-database-client-version-history"></a>Historique de version de client de base de données élastique 

Pour l’historique de version, rendez-vous sur [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 