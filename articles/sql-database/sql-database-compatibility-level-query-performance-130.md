<properties
    pageTitle="Le niveau de compatibilité, comment évaluer | Microsoft Azure"
    description="Étapes et les outils permettant de déterminer le niveau de compatibilité est la mieux adapté à votre base de données sur la base de données de SQL Azure ou Microsoft SQL Server"
    services="sql-database"
    documentationCenter=""
    authors="alainlissoir"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.devlang="NA"
    ms.tgt_pltfrm="NA"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="alainl"/>


# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>Amélioration des performances de la requête avec 130 du niveau de compatibilité de base de données de SQL Azure


Une base de données SQL Azure est en cours d’exécution transparente des centaines de milliers de bases de données à de nombreux niveaux de compatibilité, en conservant et en garantir la compatibilité ascendante vers la version correspondante de Microsoft SQL Server pour tous ses clients !

Par conséquent, rien n’empêche les utilisateurs de modifient les bases de données existantes vers le dernier niveau de compatibilité de profiter de l’optimiseur de requêtes nouvelles et fonctionnalités de processeur de requête. Comme un rappel de l’historique, l’alignement des versions SQL pour les niveaux de compatibilité par défaut sont les suivantes :

- 100 : dans SQL Server 2008 et SQL Azure V11 de base de données.
- 110 : dans SQL Server 2012 et SQL Azure V11 de base de données.
- 120 : dans SQL Server 2014 et SQL Azure V12 de base de données.
- 130 : dans SQL Server 2016 et SQL Azure V12 de base de données.


> [AZURE.IMPORTANT] À partir de **mi-juin 2016**, dans la base de données de SQL Azure, le niveau de compatibilité par défaut sera 130 au lieu de 120 pour les bases de données **nouvellement créées** .
> 
> Bases de données créées avant la mi-juin 2016 seront *pas* affectés et de conserver leur niveau de compatibilité actuel (110, 100 ou 120). Migrer à partir de la version de base de données de SQL Azure que V11 à V12 n’auront pas leur niveau de compatibilité de bases de données modifiée.


Dans cet article, nous explorons les avantages de niveau de compatibilité de 130 et comment tirer parti de ces avantages. Corriger les éventuels effets secondaires sur les performances des requêtes pour les applications SQL existantes.


## <a name="about-compatibility-level-130"></a>À propos du niveau de compatibilité de 130


Tout d’abord, si vous souhaitez connaître le niveau de compatibilité actuel de votre base de données, exécutez l’instruction Transact-SQL suivante.


```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


Avant cette modification au niveau de 130 se produit pour les bases de données **nouvellement** créé, nous allons vérifier que cette modification est par le biais de quelques exemples élémentaires de requête et voir comment n’importe qui peut en bénéficier.

Traitement des requêtes dans des bases de données relationnelles peut être très complexe et peut entraîner beaucoup des mathématiques et des sciences de l’informatique à comprendre les choix de conception inhérentes et les comportements. Dans ce document, le contenu a été volontairement simplifié pour vous assurer que toute personne disposant d’un contexte technique minimal peut comprendre l’impact de la modification du niveau de compatibilité et de déterminer comment il peut bénéficier des applications.

Nous allons avoir un bref aperçu de ce que le niveau de compatibilité 130 apporte à la table.  Vous trouverez plus de détails à [Modifier le niveau de compatibilité de base de données (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx), mais voici un bref récapitulatif :

- L’opération d’insertion d’une instruction Insert-select peut être multi-thread ou peut avoir un plan parallèle, tandis qu’avant cette opération mono-thread.
- Mémoire optimisée table et requêtes des variables de table peuvent désormais avoir des plans en parallèle, tandis qu’avant que cette opération a été également mono-thread.
- Statistiques de la mémoire optimisée de table peuvent désormais être échantillonnés et mise à jour automatique. Consultez [nouvelles fonctionnalités de moteur de base de données : OLTP de mémoire](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) pour plus de détails.
- Mode de traitement par lots v/s Mode ligne change avec l’index de colonne magasin
  - Effectue un tri sur une table avec un index de colonne magasin est à présent en mode batch.
  - Agrégats de fenêtrage fonctionnent désormais en mode batch telles que les instructions de TSQL retard/prospect.
  - Requêtes sur les tables du magasin de colonne contenant plusieurs clauses distinctes fonctionnent en mode de traitement par lots.
  - Requêtes en cours d’exécution sous DOP = 1 ou avec un plan en série également exécuter en Mode de traitement par lots.
- Enfin, améliorations de l’Estimation de cardinalité sont proviennent en fait avec le niveau de compatibilité de 120, mais pour ceux en cours d’exécution à un niveau de compatibilité inférieur (c'est-à-dire 100 ou 110), le déplacement à la compatibilité au niveau 130 apportera également ces améliorations et ces peuvent également tirer profit de vos applications, les performances de requête.


## <a name="practicing-compatibility-level-130"></a>Pratiquer le niveau de compatibilité de 130


Premier, nous allons apprendre certaines tables, les index et les données aléatoires créées pour mettre en pratique certaines de ces nouvelles fonctionnalités. Les exemples de script TSQL peuvent être exécutés sous SQL Server 2016, ou base de données de SQL Azure. Toutefois, lors de la création d’une base de données SQL d’Azure, assurez-vous que vous choisissez au minimum une base de données P2 car vous avez besoin d’au moins deux cœurs pour permettre le multi-threading et donc bénéficier de ces fonctionnalités.


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


Maintenant, nous allons avoir un coup de œil à certaines des fonctionnalités de traitement de la requête avec le niveau de compatibilité de 130.


## <a name="parallel-insert"></a>INSERTION parallèle


L’exécution des instructions TSQL ci-dessous exécute l’opération d’insertion sous le niveau de compatibilité de 120 et 130, qui exécute respectivement l’opération d’insertion dans un seul modèle de thread (120) et un modèle multithread (130).


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


En demandant la réelle du plan de requête, en examinant sa représentation sous forme de graphique ou de son contenu XML, vous pouvez déterminer quels Estimation de cardinalité sert à lire. En examinant le plans côte à côte sur la figure 1, nous pouvons voir clairement que l’exécution de l’insertion de banque d’informations de colonne passe de la série de 120 à parallèle dans 130. En outre, notez que la modification de l’icône de l’itérateur dans le plan de 130 affichant deux flèches parallèles, illustrant le fait que maintenant l’exécution de l’itérateur est en effet parallèle. Si vous avez de grandes pour terminer les opérations d’insertion, l’exécution en parallèle, liée à la référence de base que vous avez à votre disposition pour la base de données, sera plus performante ; jusqu'à un 100 fois plus rapide en fonction de votre situation !


*Figure 1 : Opération d’insertion change de série parallèle avec compatibilité de niveau 130.*


![Figure 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## <a name="serial-batch-mode"></a>Mode de traitement par lots de série


De la même façon, le déplacement vers le niveau de compatibilité de 130 lors du traitement des lignes de données permet en mode de traitement par lots. Tout d’abord, les opérations en mode de traitement par lots ne sont disponibles lorsque vous avez un index de magasin de colonne en place. En second lieu, un lot représente généralement environ 900 lignes et utilise une logique de code optimisé pour le processeur multicœur, débit de mémoire et directement exploite les données compressées de la banque de colonne chaque fois que possible. Dans ces conditions, SQL Server 2016 peut traiter environ 900 lignes en une seule fois, au lieu de 1 ligne à la fois, et par conséquent, le coût global de frais généraux de l’opération est maintenant partagé par l’ensemble du lot, réduisant le coût par ligne général. Ce montant partagé d’opérations combinées avec la compression de banque d’informations de colonne fondamentalement réduit la latence impliqués dans une opération de mode de sélection de traitement par lots. Vous pouvez trouver plus d’informations sur le magasin de colonne et au [Guide des index Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)le mode de traitement par lots.


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Comme visibles en-dessous, en observant la requête plans côte-à-côte sur la figure 2, nous pouvons voir que le mode de traitement a été modifiée au niveau de compatibilité, et par conséquent, lorsque vous exécutez les requêtes dans le niveau de compatibilité à la fois complètement, nous pouvons voir que la majeure partie du temps de traitement est passé en mode de ligne (86 %) par rapport au mode de traitement par lots (14 %), où 2 lots ont été traités. Augmenter le groupe de données, les avantages augmentent.


*Figure 2 : Sélection des modifications de l’opération de la série en mode de traitement par lots avec le niveau de compatibilité de 130.*


![Figure 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## <a name="batch-mode-on-sort-execution"></a>Mode de traitement par lots sur l’exécution du tri


Similaire à ce qui précède, mais appliquée à une opération de tri, la transition de mode de ligne (niveau de compatibilité de 120) en mode de traitement par lots (niveau de compatibilité de 130) améliore les performances de l’opération de tri pour les mêmes raisons.


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Côte à côte visible sur la figure 3, nous pouvons voir que l’opération de tri en mode de ligne représente 81 % du coût, tandis que le mode de traitement par lots ne représente que 19 % du coût (respectivement 81 et 56 % sur l’ordre de tri).


*Figure 3 : Opération de tri change de ligne en mode de traitement par lots avec le niveau de compatibilité de 130.*


![Figure 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


Bien évidemment, ces exemples ne contient que des dizaines de milliers de lignes, qui a la valeur nothing lorsque vous examinez les données disponibles dans la plupart des serveurs SQL de ces jours. Tout projet ces contre des millions de lignes au lieu de cela, et cela peut se traduire en quelques minutes d’exécution neutralisé tous les jours en attente de la nature de votre charge de travail.


## <a name="cardinality-estimation-ce-improvements"></a>Améliorations de cardinalité Estimation (CE)


Introduits avec SQL Server 2014, toute base de données en cours d’exécution à un niveau de compatibilité 120 ou supérieur rend l’utilisation de la nouvelle Estimation de cardinalité des fonctionnalités. Estimation de cardinalité est essentiellement la logique utilisée pour déterminer la façon dont SQL server exécute une requête en fonction de son coût estimé. L’estimation est calculée à l’aide de la saisie de statistiques associées aux objets impliqués dans cette requête. En pratique, à un niveau élevé, fonctions d’Estimation de cardinalité sont des estimations de nombre de ligne ainsi que des informations sur la distribution des valeurs, les nombres de valeur distincts, et les nombres en double contient dans les tables et les objets référencés dans la requête. Obtenez ces estimations incorrect, peut entraîner des e/s de disque inutiles en raison des aides à l’insuffisance de mémoire (c'est-à-dire les projections de liquide de TempDB), ou à une sélection de l’exécution d’un plan en série sur l’exécution d’une plan parallèle, pour ne citer. Conclusion, estimations incorrectes peuvent entraîner une dégradation des performances globales de l’exécution de la requête. De l’autre côté, une meilleure estimation, des estimations plus précises, entraîne une meilleure exécutions de la requête !

Comme mentionné précédemment, les estimations et les optimisations de requête sont une question complexe, mais si vous souhaitez en savoir plus sur les plans de requête et dispositif d’estimation de cardinalité, vous pouvez faire référence au document à [l’Optimisation de vos Plans de requête avec l’estimation de cardinalité 2014 de SQL Server](https://msdn.microsoft.com/library/dn673537.aspx) pour obtenir de plus amples.


## <a name="which-cardinality-estimation-do-you-currently-use"></a>Estimation de la cardinalité utilisez-vous actuellement ?


Pour déterminer sous quel Estimation de cardinalité vos requêtes sont en cours d’exécution, nous allons simplement utiliser les exemples de requête ci-dessous. Notez que ce premier exemple s’exécuteront sous le niveau de compatibilité 110, ce qui implique d’utiliser les anciennes fonctions d’Estimation de cardinalité.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Une fois l’exécution terminée, cliquez sur le lien XML et consulter les propriétés de l’itérateur de première comme indiqué ci-dessous. Notez le nom de propriété appelé CardinalityEstimationModelVersion définie sur 70. Cela ne signifie pas que le niveau de compatibilité de base de données est défini à la version 7.0 de SQL Server (elle est définie sur 110 visible dans les instructions de TSQL ci-dessus), mais la valeur 70 représente simplement la fonctionnalité d’Estimation de cardinalité héritée disponible depuis SQL Server 7.0, qui n’avait aucune révision principale jusqu'à 2014 de SQL Server (qui est fourni avec un niveau de compatibilité de 120).


*Figure 4 : La CardinalityEstimationModelVersion est définie à 70 lors de l’utilisation d’un niveau de compatibilité de 110 ou en dessous.*


![Figure 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


Vous pouvez également modifier le niveau de compatibilité à 130 et désactiver l’utilisation de la nouvelle fonction d’Estimation de cardinalité à l’aide de la LEGACY_CARDINALITY_ESTIMATION la valeur ON à [ALTER DATABASE CONFIGURATION étendue](https://msdn.microsoft.com/library/mt629158.aspx). Ce sera exactement la même que l’utilisation de 110 à partir d’une Estimation de cardinalité fonction point de vue, lors de l’utilisation de la dernière requête de traitement de niveau de compatibilité. Ce faisant, vous pouvez bénéficier de la nouvelle requête de traitement des fonctionnalités annoncées au dernier niveau de compatibilité (c'est-à-dire en mode batch), mais s’appuient toujours sur l’ancienne fonctionnalité d’Estimation de cardinalité si nécessaire.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Permet à la nouvelle fonctionnalité d’Estimation de cardinalité simplement déplacer vers le niveau de compatibilité de 120 ou 130. Dans ce cas, la valeur par défaut CardinalityEstimationModelVersion va être définie en conséquence à 120 ou 130 comme visibles en-dessous.


```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figure 5 : La CardinalityEstimationModelVersion est définie à 130 lors de l’utilisation d’un niveau de compatibilité de 130.*


![Figure 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## <a name="witnessing-the-cardinality-estimation-differences"></a>Contrôleur les différences d’Estimation de cardinalité


Maintenant, nous allons exécuter un peu plus complexe impliquant une jointure interne avec une clause WHERE avec des prédicats de la requête et jetons un œil à l’estimation de nombre de lignes à partir de l’ancienne fonction de l’Estimation de cardinalité tout d’abord.


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


L’exécution de cette requête efficacement renvoie les 200,704 lignes, alors que l’estimation de la ligne avec l’ancienne fonctionnalité d’Estimation de cardinalité prétend 194,284 lignes. Bien évidemment, comme indiqué précédemment, ces résultats d’inventaire ligne dépendront également la fréquence à laquelle vous avez exécuté les exemples précédents, qui remplit les tables d’exemple à de nombreuses reprises à chaque exécution. Bien évidemment, les prédicats dans votre requête a également une influence sur l’estimation réelle en dehors de la forme de table, les données de contenu, et comment ces données réellement mettre en corrélation les uns avec les autres.


*Figure 6 : L’estimation de nombre de lignes est 194,284 ou 6 000 lignes désactivée à partir des 200,704 lignes attendus.*


![Figure 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


De la même façon, nous allons maintenant s’exécuter la même requête avec la nouvelle fonctionnalité d’Estimation de cardinalité.


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Examinez la ci-dessous, vous pouvez désormais voir que l’estimation de la ligne est 202,877, ou beaucoup plus étroite et plus élevé que l’Estimation de cardinalité ancien.

*Figure 7 : L’estimation de nombre de lignes est désormais 202,877, au lieu de 194,284.*


![Figure 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


En réalité, le jeu de résultats est 200,704 lignes (mais tout dépend de la fréquence à laquelle vous n’avez exécuté les requêtes des exemples précédents, mais plus important encore, car le code TSQL utilise l’instruction ALEA (), les valeurs retournées peuvent varier d’une exécution à l’autre). Par conséquent, dans cet exemple particulier, la nouvelle Estimation de cardinalité est le meilleur à estimer le nombre de lignes, car 202,877 est beaucoup plus proche 200,704, que 194,284 ! Enfin, si vous modifiez des prédicats de la clause WHERE à l’égalité (au lieu de « > » par exemple), cela pourrait rendre les estimations entre l’ancienne et nouvelle fonction de cardinalité encore plus différente selon correspond à combien vous pouvez obtenir.

Bien évidemment, dans ce cas, est de 6000 ~ lignes éteint à partir du nombre réel ne représente pas un grand nombre de données dans certaines situations. À présent, la fonction transpose pour des millions de lignes entre plusieurs tables et des requêtes plus complexes et parfois l’estimation peut être désactivée par des millions de lignes et par conséquent, les risques de prise en charge du plan d’exécution incorrect, ou demander les aides mémoire insuffisante conduisant à TempDB de projections de liquide et donc d’e/s, sont plus élevées.

Si vous avez la possibilité, pratique cette comparaison avec vos requêtes plus courantes et les groupes de données et voyez par vous-même en combien parmi les estimations de l’anciennes et le nouveau sont affectés, tandis que certains pourraient simplement devenir plus hors de la réalité, ou certains autres tout simplement plus près de la ligne compte réellement renvoyé dans les jeux de résultats. Tout dépend de la forme de vos requêtes, les caractéristiques de base de données SQL d’Azure, la nature et la taille de vos groupes de données et les statistiques disponibles à leur sujet. Si vous venez de créer votre instance de base de données de SQL Azure, l’optimiseur de requête devra créer ses connaissances à partir de zéro au lieu de réutiliser les statistiques de l’exécution de requête précédente. Par conséquent, les estimations sont très contextuels et presque spécifiques à chaque situation de serveur et d’application. Il est important de garder à l’esprit !


## <a name="some-considerations-to-take-into-account"></a>Certaines considérations à prendre en compte


Bien que la plupart des charges de travail seraient de bénéficier du niveau de compatibilité 130, avant d’adopter le niveau de compatibilité de votre environnement de production, vous avez essentiellement 3 options :

1. Vous déplacez vers le niveau de compatibilité de 130 et voir comment les choses. Dans le cas où vous notez des régressions vous suffit de définissez le niveau de la compatibilité à son niveau d’origine, ou conservez 130 et sauvegarder de l’Estimation de cardinalité inverse uniquement pour le mode hérité (comme expliqué ci-dessus, ce seul peut résoudre le problème).
2. Tester vos applications existantes sous la charge de production similaires, régler et valider les performances avant de passer à la production. En cas de problèmes, même en tant que ci-dessus, vous pouvez toujours revenir en arrière pour le niveau de compatibilité d’origine, ou simplement inverser l’Estimation de cardinalité vers le mode hérité.
3. Comme une option finale et la plus récente permet de répondre à ces questions, consiste à exploiter le magasin de la requête. C’est option recommandée d’aujourd'hui ! Pour aider à l’analyse de vos requêtes sous compatibilité niveau 120 ou ci-dessous et 130, nous ne pouvons pas encourager lorsque vous utilisez le magasin de la requête. Banque de requête est disponible avec la version la plus récente de V12 de base de données SQL Azure, et il est conçu pour vous aider dans la résolution des problèmes de performances de requête. Considérez la banque de la requête comme un enregistreur de données de vol de votre base de données, collecter et présenter des informations historiques détaillées sur toutes les requêtes. Ceci simplifie grandement l’analyse post-mortem de performances en réduisant le temps à diagnostiquer et à résoudre les problèmes. Vous trouverez plus d’informations à [magasin de requête : un enregistreur de données de vol de votre base de données](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).


Au haut niveau, si vous déjà disposez d’un ensemble de bases de données en cours d’exécution au niveau de compatibilité 120 ou en dessous et que vous envisagez de déplacer certaines d'entre elles à 130, ou parce que votre charge de travail pour le provisionnement automatique des bases de données qui seront bientôt être défini par défaut à 130, veuillez prendre en compte les suivantes :

- Avant de modifier le nouveau niveau de compatibilité dans la production, activer le magasin de la requête. Vous pouvez faire pour [Modifier le Mode de compatibilité de base de données et le magasin de requête](https://msdn.microsoft.com/library/bb895281.aspx) pour plus d’informations.
- Ensuite, testez les charges de travail critiques à l’aide de données représentatives et les requêtes d’un environnement de type production et de comparer les performances rencontré et comme signalé par la banque de la requête. Si vous rencontrez des régressions, vous pouvez identifier les requêtes de régressions avec le magasin de requête et utilisez le plan de forcer l’option à partir de la banque de la requête (également appelé plan épinglage). Dans ce cas, vous définitivement Restez avec le niveau de compatibilité 130 et utiliser le plan de requête précédent comme suggéré par le magasin de la requête.
- Si vous souhaitez tirer parti des nouvelles fonctionnalités et fonctions de base de données SQL Azure (qui exécute SQL Server 2016), mais sont sensibles aux modifications apportées par le niveau de compatibilité 130, en dernier recours, vous pouvez envisager de forcer le niveau de compatibilité au niveau adapté à votre charge de travail à l’aide d’une instruction ALTER DATABASE. Mais tout d’abord, sachez que le plan de requête banque épinglage option est la meilleure option car vous n’utilisez ne pas 130 séjourne en fait au niveau de la fonctionnalité d’une ancienne version de SQL Server.
- Si vous avez des applications mutualisées recouvrant plusieurs bases de données, il peut être nécessaire de mettre à jour la configuration logique de vos bases de données afin de garantir un niveau de compatibilité cohérente sur toutes les bases de données ; plus anciens et qui vient d’être mis en service. Performances de charge de travail de votre application peut être sensibles au fait que certaines bases de données sont en cours d’exécution à des niveaux de compatibilité, et par conséquent, la cohérence au niveau de compatibilité sur une base de données peut être requise afin de fournir la même expérience à vos clients par le Conseil. Notez qu’il n’est pas un mandat, il dépend vraiment comment votre application est concernée par le niveau de compatibilité.
- Dernier, en ce qui concerne l’Estimation de cardinalité et comme la modification du niveau de compatibilité, avant de passer en production, il est recommandé de tester votre charge de travail de production dans les nouvelles conditions afin de déterminer si votre application tire parti de l’amélioration de l’Estimation de cardinalité.


## <a name="conclusion"></a>Conclusion


À l’aide de la base de données de SQL Azure pour bénéficier de toutes les améliorations de SQL Server 2016 peut améliorer nettement les exécutions de la requête. Tout comme-est ! Bien sûr, comme toute nouvelle fonctionnalité, une évaluation appropriée doit être effectuée pour déterminer les conditions précises sous lesquelles votre charge de travail de base de données fonctionne le mieux. L’expérience montre que la plupart des charges de travail doivent au moins exécutés de façon transparente sous le niveau de compatibilité de 130, tout en profitant des nouvelles fonctions et nouvelle Estimation de cardinalité de traitement des requêtes. Qui dit, dans la pratique, il existe toujours des exceptions échéance approprié de faire diligence consiste en une évaluation importante pour déterminer combien vous pouvez bénéficier de ces améliorations. Et, là encore, la banque de requête peut être d’une grande aide dans ce travail !

Évolution de SQL Azure, attendez-vous à un niveau de compatibilité de 140 à l’avenir. Lorsque le moment est approprié, nous allons commencer à parler de ce que ce niveau de compatibilité future 140 apportera, tout comme nous avons brièvement abordée ici le niveau de compatibilité 130 est mise à jour.

Pour le moment, n’oublions pas, à partir de juin 2016, base de données de SQL Azure change le niveau de compatibilité par défaut de 120 à 130 pour les bases de données nouvellement créées. N’oubliez pas !


## <a name="references"></a>Références


- [Quelles sont les nouveautés dans le moteur de base de données](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [Blog : Requête banque : un enregistreur de DONNEES de vol de votre base de données, par Borko Novakovic, 2016 de 8 juin](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [MODIFIER le niveau de compatibilité de base de données (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [MODIFIER LA BASE DE DONNÉES DE CONFIGURATION D’ÉTENDUES](https://msdn.microsoft.com/library/mt629158.aspx)

- [Niveau de compatibilité de 130 pour V12 de base de données SQL Azure](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)

- [Optimisation de votre requête Plans avec le SQL Server estimation de cardinalité 2014](https://msdn.microsoft.com/library/dn673537.aspx)

- [Guide d’index ColumnStore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Blog : Meilleures performances des requêtes avec un niveau de compatibilité de 130 dans la base de données SQL Azure par Alain Lissoir, 2016 de 6 mai](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->
