<properties 
    pageTitle="Mise en route avec les outils de base de données élastique" 
    description="Principes fondamentaux de la fonctionnalité des outils élastique de la base de données de base de données SQL Azure, y compris facile pour exécuter l’exemple d’application." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor="CarlRabeler"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="get-started-with-elastic-database-tools"></a>Mise en route avec les outils de base de données élastique

Ce document présente l’expérience du développeur par l’exemple d’application en cours d’exécution. L’exemple crée une application simple de sharded et explore les fonctionnalités clés des outils de base de données élastique. L’exemple montre les fonctions de la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md)

Pour installer la bibliothèque, accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Notez que la bibliothèque est installée avec l’exemple d’application décrit ci-dessous.

## <a name="prerequisites"></a>Conditions préalables

1. Visual Studio 2012 ou supérieur avec C# est requis. Téléchargez une version gratuite à [Visual Studio les téléchargements](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. NuGet 2.7 ou ultérieure. Pour obtenir la version la plus récente, reportez-vous à la section [Installation de NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>Télécharger et exécuter l’exemple d’application

Le **élastique la base de données avec Azure SQL : mise en route** exemple d’application illustre les aspects les plus importants de l’expérience de développement pour les applications sharded à l’aide des outils de base de données élastique Azure SQL. Il se concentre sur les cas d’utilisation de clé pour [Éclater mapper gestion](sql-database-elastic-scale-shard-map-management.md), [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) et [l’interrogation de plusieurs partagé](sql-database-elastic-scale-multishard-querying.md). Pour télécharger et exécuter l’exemple, procédez comme suit : 

1. Ouvrez Visual Studio et sélectionnez **fichier -> Nouveau -> projet**.
2. Dans la boîte de dialogue, cliquez sur **en ligne**.

    ![Nouveau projet > en ligne][2]
3. Cliquez ensuite sur **Visual C#** sous **exemples**.

    ![Cliquez sur Visual C##][3]
4. Dans la zone Rechercher, tapez **db élastique** à rechercher pour l’exemple. Le titre **Élastique outils de base de données pour SQL Azure - mise en route** s’affiche.

    ![Zone de recherche][1]
 
5. Sélectionnez l’échantillon, choisissez un nom et un emplacement pour le nouveau projet et cliquez sur **OK** pour créer le projet.
6. Ouvrez le fichier **app.config** dans la solution pour le projet d’exemple et suivez les instructions dans le fichier pour ajouter le nom de votre serveur de base de données SQL d’Azure et vos informations de connexion (nom d’utilisateur et mot de passe).
7. Générez et exécutez l’application. Lorsque vous êtes invité, Veuillez permettre à Visual Studio restaurer les packages NuGet de la solution. Il télécharge la dernière version de la bibliothèque de client de base de données élastique de NuGet.
8. Jouez avec les différentes options pour en savoir plus sur les fonctionnalités de la bibliothèque client. Remarque Les étapes de l’application dans la console de sortie et n’hésitez pas à Explorer le code dans les coulisses.

    ![état d’avancement][4]

Félicitations, vous avez correctement construit et exécuté votre première application sharded à l’aide des outils de base de données élastique sur la base de données de SQL Azure. Examinons rapidement au milieu des fragments créés par l’exemple en vous connectant avec Visual Studio ou SQL Server Management Studio pour votre serveur de base de données Azure. Vous remarquerez les nouvelles bases de données exemple partagé et une base de données du Gestionnaire de table partagé l’échantillon a créé.

> [AZURE.IMPORTANT] Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="key-pieces-of-the-code-sample"></a>Éléments clés de l’exemple de code

1. **Gestion de milieu des fragments et partagé**: le code montre comment travailler avec le milieu des fragments, des plages, et les mappages dans le fichier **ShardMapManagerSample.cs**. Vous trouverez plus d’informations sur ce sujet : [Gestion de mappage partagé](http://go.microsoft.com/?linkid=9862595).  
2. **Routage dépendant des données**: routage de transactions à la droite partagé est indiqué dans **DataDependentRoutingSample.cs**. Pour plus d’informations, consultez [Routage dépendant des données](http://go.microsoft.com/?linkid=9862596). 
3. **Requête sur plusieurs milieu des fragments**: interrogation sur le milieu des fragments est illustrée dans le fichier **MultiShardQuerySample.cs**. Pour plus d’informations, consultez [Partagé plusieurs requêtes](http://go.microsoft.com/?linkid=9862597).
4. **Milieu des fragments Ajout vides**: l’ajout itératif de nouveau milieu des fragments vides est effectuée par le code dans le fichier **AddNewShardsSample.cs**. Les informations de cette rubrique sont couverts ici : [Gestion de mappage partagé](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Autres opérations évolutivité élastique

1. **Fractionnement d’un partagé existant**: la possibilité de fractionner le milieu des fragments est fournie par le biais de l' **outil de fusion et fractionnement**. Vous trouverez plus d’informations sur cet outil ici : [Présentation de l’outil de fusion et fractionnement](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Fusion du milieu des fragments existants**: les fusions partagé sont également effectuées à l’aide de l' **outil de fusion et fractionnement**. Pour plus d’informations, reportez-vous à : [Présentation de l’outil de fusion et fractionnement](sql-database-elastic-scale-overview-split-and-merge.md).   


## <a name="cost"></a>Coût

Les outils de base de données élastique sont gratuits. Outils de base de données élastique n’impose pas de frais supplémentaires sur le coût de l’utilisation d’Azure. 

Par exemple, l’exemple d’application crée de nouvelles bases de données. Le coût dépend de l’édition de base de données de base de données SQL Azure choisie et l’utilisation d’Azure de votre application.

Informations de tarification, consultez [Détails de prix de base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les outils de base de données élastique, voir :

* [Table de documentation outils élastique de la base de données](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
-    Exemples de code : 
    -    [DB élastique avec SQL Azure - mise en route](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [DB élastique avec SQL Azure - intégration avec Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Élasticité partagé sur le centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blog : [annonce d’évolutivité élastique](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Channel 9 : [évolutivité élastique présentation vidéo](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    Forum de discussion : [forum d’une base de données SQL Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
-    Pour mesurer les performances : [les compteurs de Performance pour le Gestionnaire de mappage partagé](sql-database-elastic-database-client-library.md)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 
