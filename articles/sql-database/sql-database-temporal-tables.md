<properties
   pageTitle="Mise en route avec les Tables de la base de données SQL Azure temporelles | Microsoft Azure"
   description="Découvrez comment mettre en route à l’aide de Tables temporelles dans la base de données de SQL Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="08/29/2016"
   ms.author="carlrab"/>

#<a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Mise en route avec les Tables temporelles dans la base de données SQL Azure

Tables temporelles sont une nouvelle fonctionnalité de programmation de base de données SQL Azure qui vous permet de suivre et d’analyser l’historique complet des modifications dans vos données, sans nécessiter de codage personnalisé. Tables temporelles conservent les données étroitement liées au contexte de l’heure afin que les faits stockés peuvent être interprétées comme étant valide uniquement dans la période spécifique. Cette propriété de Tables temporelles permet une analyse efficace en temps et lors de l’obtention des perspectives d’évolution des données.

##<a name="temporal-scenario"></a>Scénario temporelle

Cet article illustre la procédure pour utiliser les Tables temporelles dans un scénario d’application. Supposons que vous souhaitez effectuer le suivi de l’activité des utilisateurs sur un nouveau site Web qui est en cours de développement à partir de rien ou sur un site Web existant que vous souhaitez étendre avec analytique d’activité utilisateur. Dans cet exemple simplifié, nous supposons que le nombre de pages de web visités au cours d’une période de temps est un indicateur qui doive être capturés et analysés dans la base de données de site Web qui est hébergé sur la base de données de SQL Azure. L’objectif de l’analyse de l’historique de l’activité de l’utilisateur est pour obtenir des entrées à recréer le site Web et de fournir la meilleure expérience pour les visiteurs.

Le modèle de base de données de ce scénario est très simple : mesure d’activité utilisateur est représentée par un champ d’entier unique, **PageVisited**et est capturée en même temps que les informations de base sur le profil de l’utilisateur. En outre, pour l’analyse de la base de temps, vous conservez une série de lignes pour chaque utilisateur, où chaque ligne représente le nombre de pages visité par un utilisateur donné dans un délai spécifique.

![Schéma](./media/sql-database-temporal-tables/AzureTemporal1.png)

Heureusement, vous n’avez pas besoin de placer tout effort dans votre application pour gérer les informations de cette activité. Avec les Tables temporelles, ce processus est automatisé - vous offrant ainsi une flexibilité totale lors de la conception de site Web et plus de temps pour vous concentrer sur l’analyse de données lui-même. La seule chose que vous avez à faire est de s’assurer que la table de **WebSiteInfo** est configuré en tant que [temporal système version](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Les étapes exactes pour utiliser les Tables temporelles dans ce scénario sont décrites ci-dessous.

##<a name="step-1-configure-tables-as-temporal"></a>Étape 1 : Configurer les tables comme temporelle

Selon que vous démarrage du développement en nouvelle ou mise à niveau d’une application existante, vous va créer tables temporelles ou modifier des modèles existants en ajoutant des attributs temporelles. Demande de devis, en général votre scénario peut être une combinaison de ces deux options. Exécuter ces actions à l’aide de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [Outils de données de SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) ou tout autre outil de développement de Transact-SQL.


> [AZURE.IMPORTANT] Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


###<a name="create-new-table"></a>Créer nouvelle table

Utilisez élément de menu contextuel « Nouvelle Table système version » dans l’Explorateur d’objets de SSMS pour ouvrir l’éditeur de requête avec un script de modèle temporel table puis utilisez « Spécifier les valeurs de paramètres de modèle » (Ctrl + Maj + M) pour remplir le modèle :

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Dans SSDT, a choisi le modèle de « Temporelle de Table (système version) » lors de l’ajout de nouveaux éléments au projet de base de données. Qui va ouvrir le Concepteur de table et vous permettent de spécifier facilement la disposition de table :

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Vous pouvez également une table temporelle create en spécifiant les instructions Transact-SQL directement, comme indiqué dans l’exemple ci-dessous. Notez que les éléments obligatoires de chaque table temporelle sont la définition et la clause SYSTEM_VERSIONING avec une référence à un autre utilisateur de table qui stockera les versions de ligne d’historique :

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Lorsque vous créez la version système table temporelle, la table d’historique associé avec la configuration par défaut est créée automatiquement. La table de l’historique par défaut contient un index B-tree ordonné en clusters sur les colonnes des périodes (début, fin) avec la compression de page activée. Cette configuration est optimale pour la majorité des scénarios dans lesquels les tables temporelles sont utilisés, en particulier pour les [données d’audit](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Dans ce cas particulier, nous souhaitons effectuer des tendances en temps sur un historique de données plus long et avec des ensembles de données plus importants, le choix de stockage pour la table de l’historique est un index ordonné en clusters columnstore. Un columnstore en cluster offre très bonne compression et des performances pour les requêtes analytiques. Tables temporelles vous donnent la possibilité de configurer complètement indépendamment des index sur les tables en cours et temporelles. 

**Remarque**: index de Columnstore sont disponibles uniquement dans la couche de service premium.

Le script suivant montre comment les index par défaut dans la table de l’historique peut être modifiée à la columnstore ordonné en clusters :

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Tables temporelles sont représentés dans l’Explorateur d’objets avec l’icône spécifique pour faciliter l’identification, tandis que sa table de l’historique s’affiche comme un nœud enfant.

![ALTER table](./media/sql-database-temporal-tables/AzureTemporal4.png)

###<a name="alter-existing-table-to-temporal"></a>Modifier une table existante à temporelle

Nous couvrirons l’autre scénario dans lequel la table WebsiteUserInfo existe déjà, mais n’a pas été conçue pour conserver un historique des modifications. Dans ce cas, vous pouvez étendre la table existante pour devenir temporelle, comme illustré dans l’exemple suivant :

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

##<a name="step-2-run-your-workload-regularly"></a>Étape 2 : Exécuter votre charge de travail régulièrement

Le principal avantage de Tables temporelles est que vous n’avez pas besoin de modifier ou d’adapter votre site Web en aucune façon pour effectuer le suivi des modifications. Une fois créé, Tables temporelles persistent en toute transparence les précédentes versions de ligne chaque fois que vous effectuez des modifications sur vos données. 

Pour tirer pleinement parti de changement automatique de suivi pour ce scénario particulier, nous allons simplement mettre à jour la colonne **PagesVisited** chaque fois lorsque l’utilisateur termine sa session sur le site Web :

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Il est important de noter que la requête de mise à jour n’a pas besoin de connaître l’heure exacte, lors de l’opération en cours, ni comment les données historiques seront conservées pour les analyser ultérieurement. Ces deux aspects sont gérées automatiquement par la base de données de SQL Azure. Le diagramme suivant illustre comment des données d’historique sont générées sur chaque mise à jour.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

##<a name="step-3-perform-historical-data-analysis"></a>Étape 3 : Analyse des données historiques

Lorsque le système temporelle-contrôle de version est activé, analyse des données historiques est désormais qu’une seule requête à vous. Dans cet article, nous fournirons quelques exemples adresse analyse scénarios courants - pour connaître tous les détails, diverses options sont introduites avec la clause [De SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) .

Pour voir les 10 principaux utilisateurs classés par le nombre des pages web visitées à une heure plus tôt, exécutez cette requête :

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Vous pouvez facilement modifier cette requête pour analyser les visites sur site un jour plus tôt, Voilà un mois, ou à n’importe quel point dans le passé que vous souhaitez.

Pour effectuer l’analyse statistique de base pour la journée précédente, utilisez l’exemple suivant :

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Pour rechercher des activités d’un utilisateur spécifique, dans une période donnée, utilisez la clause IN de contenus :

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Visualisation graphique est particulièrement pratique pour les requêtes temporelles que vous pouvez afficher plus facilement des tendances et des modèles d’utilisation dans un intuitive :

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

##<a name="evolving-table-schema"></a>Schéma de la table en cours d’évolution

En règle générale, vous devez modifier le schéma de table temporaire pendant que vous effectuez le développement d’applications. Pour ce faire, exécutez simplement régulières ALTER TABLE instructions et la base de données de SQL Azure va correctement propager les modifications dans la table d’historique. Le script suivant montre comment vous pouvez ajouter des attributs supplémentaires pour le suivi :

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

De la même façon, vous pouvez modifier la définition de la colonne alors que votre charge de travail est actif :

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Enfin, vous pouvez supprimer une colonne que vous n’avez plus besoin.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````
    
Dernière [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) permet également de modifier le schéma de la table temporaire pendant que vous êtes connecté à la base de données (mode en ligne) ou dans le cadre du projet de base de données (mode hors connexion).

##<a name="controlling-retention-of-historical-data"></a>Contrôler la conservation des données historiques

Avec les tables système version temporelles du tableau Historique peut augmenter la taille de la base de données et plusieurs tableaux ordinaires. Une table d’historique de grande taille et en croissance constante peut devenir un problème à la fois en raison des coûts de stockage pur comme imposant une efficacité de taxe interrogation temporelle. Par conséquent, le développement d’une stratégie de rétention de données pour la gestion des données de la table de l’historique est un aspect important de la planification et la gestion du cycle de vie de chaque table temporelle. La base de données SQL Azure, vous devez les approches suivantes pour la gestion des données d’historique dans la table temporelle :

- [Le partitionnement de table](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
- [Script de nettoyage personnalisé](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

##<a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les Tables temporelles, consultez [la documentation de MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visitez le site Channel 9 pour entendre un [témoignage de client réel temporelle implémentation](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) et regarder une [démonstration temporelle en direct](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).
