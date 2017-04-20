<properties
    pageTitle="OLTP de mémoire améliore la performance de txn SQL | Microsoft Azure"
    description="Adopter les OLTP en mémoire afin d’améliorer les performances transactionnelles dans une base de données SQL existante."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor="MightyPen"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="use-in-memory-oltp-preview-to-improve-your-application-performance-in-sql-database"></a>Utilisation en mémoire OLTP (aperçu) pour améliorer les performances de votre application de base de données de SQL

[OLTP de mémoire](sql-database-in-memory.md) permet d’améliorer les performances de la charge de travail OLTP dans les bases de données SQL [Premium](sql-database-service-tiers.md) Azure sans augmenter le niveau de performance.

Suivez ces étapes pour adopter en mémoire OLTP dans votre base de données existante.

## <a name="step-1-ensure-your-premium-database-supports-in-memory-oltp"></a>Étape 1 : Vérifiez que votre base de données de prime prend en charge en mémoire OLTP

Bases de données de prime créés en novembre 2015 ou ultérieure prennent en charge la fonctionnalité en mémoire. Vous pouvez vérifier si votre base de données de prime prend en charge la fonctionnalité en mémoire en exécutant l’instruction Transact-SQL suivante. En mémoire est pris en charge si le résultat retourné est 1 (pas 0) :

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* est l’acronyme de *Traitement des transactions Extreme*

Si votre base de données existante doit être déplacé vers une nouvelle base de données V12 Premium, vous pouvez utiliser les techniques suivantes pour exporter, puis importer vos données.

#### <a name="export-steps"></a>Étapes d’exportation

Exporter votre base de données de production à un type sac à DOS à l’aide :

- La fonctionnalité [Exporter](sql-database-export.md) dans le [portail](https://portal.azure.com/).

- La fonctionnalité **d’exportation de données de couche Application** dans un [SSMS.exe à jour](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. Dans l' **Explorateur d’objets**, développez le nœud **bases de données** .
 2. Cliquez droit sur le nœud de votre base de données.
 3. Cliquez sur **tâches** > **Exporter les données de la couche Application**.
 4. Utiliser la fenêtre de l’Assistant s’affiche.


#### <a name="import-steps"></a>Étapes d’importation

Importer le type sac à DOS dans une nouvelle base de données de la prime.

1. Dans le [portail](https://portal.azure.com/)d' Azure,
 - Accédez au serveur.
 - Sélectionnez l’option de [Base de données d’importation](sql-database-import.md) .
 - Sélectionnez une niveau de tarification de prime.

2. Utiliser SSMS pour importer le type sac à DOS :
 - Dans l' **Explorateur d’objets**, cliquez sur le nœud de **bases de données** .
 - Cliquez sur **Importer données de couche Application**.
 - Utiliser la fenêtre de l’Assistant s’affiche.


## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Étape 2 : Identifier les objets à migrer vers les OLTP en mémoire

SSMS inclut un rapport de **Présentation de l’analyse Transaction performances** que vous pouvez exécuter sur une base de données avec une charge de travail active. Le rapport identifie les tables et les procédures stockées qui sont candidates pour la migration vers les OLTP en mémoire.

Dans SSMS, pour générer le rapport :
- Dans l' **Explorateur d’objets**, cliquez sur le nœud de votre base de données.
- Cliquez sur **rapports** > **rapports Standard** > **Transaction performances présentation de l’analyse**.

Pour plus d’informations, consultez [Détermination de Table ou stockées procédure doit être porté sur les OLTP en mémoire](http://msdn.microsoft.com/library/dn205133.aspx).


## <a name="step-3-create-a-comparable-test-database"></a>Étape 3 : Créer une base de données de test comparables

Supposons que le rapport indique que votre base de données possède une table qui bénéfice d’être convertie en un tableau de mémoire optimisée. Nous vous recommandons de commencer par tester pour confirmer l’indication par le test.

Vous avez besoin d’une copie de test de votre base de données de production. La base de données de test doit être au même niveau de niveau de service que votre base de données de production.

Pour faciliter le test, modifier votre base de données de test comme suit :

1. Se connecter à la base de données de test à l’aide de SSMS.

2. Pour éviter de l’option WITH (SNAPSHOT) dans les requêtes, définissez l’option de base de données comme indiqué dans l’instruction T-SQL suivante :
```
ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## <a name="step-4-migrate-tables"></a>Étape 4 : Faire migrer des tables

Vous devez créer et remplir une copie mémoire optimisée de la table que vous souhaitez tester. Vous pouvez le créer à l’aide :

- L’Assistant pratique l’optimisation de la mémoire dans SSMS.
- Manuel T-SQL.


#### <a name="memory-optimization-wizard-in-ssms"></a>Assistant d’optimisation de mémoire dans SSMS

Pour utiliser cette option de migration :

1. Se connecter à la base de données de test avec SSMS.

2. Dans l' **Explorateur d’objets**, avec le bouton droit sur la table, puis cliquez sur **Gestionnaire d’optimisation mémoire**.
 - L’Assistant **Table mémoire optimiseur Advisor** s’affiche.

3. Dans l’Assistant, cliquez sur **validation de la Migration** (ou **suivant** ) pour voir si la table contient des fonctionnalités non prises en charge qui sont pris en charge dans les tables de mémoire optimisée. Pour plus d’informations, voir :
 - *liste de vérification de l’optimisation de mémoire* dans le [Gestionnaire d’optimisation mémoire](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Les constructions transact-SQL non pris en charge par en mémoire OLTP](http://msdn.microsoft.com/library/dn246937.aspx).
 - La [migration vers les OLTP en mémoire](http://msdn.microsoft.com/library/dn247639.aspx).

4. Si la table ne possède aucuns fonctionnalités non prises en charge, le conseiller peut effectuer le schéma et la migration des données pour vous.


#### <a name="manual-t-sql"></a>Manuel de T-SQL

Pour utiliser cette option de migration :

1. Se connecter à votre base de données de test à l’aide de SSMS (ou un utilitaire similaire).

2. Obtenir le script T-SQL complet pour votre table et ses index.
 - Dans SSMS, cliquez droit sur le nœud de votre table.
 - Cliquez sur **Table de Script en tant que** > **créer pour** > **nouvelle fenêtre de requête**.

3. Dans la fenêtre de script, ajoutez WITH (MEMORY_OPTIMIZED = ON) à l’instruction CREATE TABLE.

4. S’il existe un index ordonné en clusters, modifiez-la NONCLUSTERED.

5. Renommer la table existante à l’aide de SP_RENAME.

6. Créez la nouvelle copie de mémoire optimisée de la table en exécutant le script CREATE TABLE modifié.

7. Copier les données dans votre table de mémoire optimisée à l’aide de INSERT... SÉLECTIONNEZ * DANS :
    
```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Étape 5 (facultatif) : migration des procédures stockées

La fonction en mémoire peut également modifier une procédure stockée pour améliorer les performances.


### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considérations sur les procédures stockées compilées en mode natif

Une procédure stockée compilée de manière native doit avoir les options suivantes dans sa clause avec T-SQL :

- NATIVE_COMPILATION

- SCHEMABINDING : ce qui signifie que les tables de la procédure stockée ne peut pas avoir de leurs définitions de colonne modifiées en aucune manière qui affecterait la procédure stockée, à moins que vous supprimez la procédure stockée.


Un module natif doit utiliser un grands [blocs atomiques](http://msdn.microsoft.com/library/dn452281.aspx) pour la gestion de la transaction. Il n’existe aucun rôle pour une instruction explicite BEGIN TRANSACTION ou ROLLBACK TRANSACTION. Si votre code détecte une violation d’une règle d’entreprise, il peut terminer le bloc atomique avec une instruction [THROW](http://msdn.microsoft.com/library/ee677615.aspx) .


### <a name="typical-create-procedure-for-natively-compiled"></a>PROCÉDURE standard de création pour compilé en mode natif

En règle générale le T-SQL pour créer une procédure stockée compilée de manière native est semblable au modèle suivant :

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

- Pour le TRANSACTION_ISOLATION_LEVEL, la capture instantanée est la valeur la plus courante pour la procédure stockée compilée de manière native. Toutefois, un sous-ensemble d’autres valeurs sont également prises en charge :
 - LECTURE RÉPÉTABLE
 - SÉRIALISABLE


- La valeur de langue doit être présente dans la vue sys.languages.


### <a name="how-to-migrate-a-stored-procedure"></a>Comment migrer d’une procédure stockée

Les étapes de migration sont les suivantes :


1. Obtenir le script CREATE PROCEDURE pour la procédure stockée interprété régulière.

2. Réécrire son en-tête pour faire correspondre le modèle précédent.

3. Vérifier si la procédure stockée les code T-SQL utilise des fonctionnalités qui ne sont pas pris en charge pour les procédures stockées compilées en mode natif. Mettre en œuvre des solutions de contournement si nécessaire.
 - Pour plus d’informations, consultez [Problèmes de Migration pour le mode natif compilé les procédures stockées](http://msdn.microsoft.com/library/dn296678.aspx).

4. Renommez l’ancienne procédure stockée à l’aide de SP_RENAME. Ou simplement le faire glisser.

5. Exécutez le script modifié de créer procédure T-SQL.


## <a name="step-6-run-your-workload-in-test"></a>Étape 6 : Exécuter votre charge de travail de test

Exécutez une charge de travail dans votre base de données de test est similaire à la charge de travail qui s’exécute dans votre base de données de production. Cela doit faire apparaître le gain de performances obtenu par l’utilisation de la fonction en mémoire pour les tables et procédures stockées.

Les attributs principaux de la charge de travail sont les suivantes :

- Nombre de connexions simultanées.

- Rapport de lecture/écriture.


Pour adapter et exécuter la charge de travail de test, envisagez d’utiliser l’outil pratique ostress.exe, illustré [ici](sql-database-in-memory.md).


Pour réduire la latence du réseau, exécutez votre test dans la même région géographique Azure où se trouve la base de données.


## <a name="step-7-post-implementation-monitoring"></a>Étape 7 : Après mise en œuvre de surveillance

Pensez à contrôler les effets de la performance de vos mises en œuvre en mémoire dans la production :

- [Stockage en mémoire de moniteur](sql-database-in-memory-oltp-monitoring.md).

- [Surveillance de base de données SQL Azure à l’aide des vues de gestion dynamique](sql-database-monitoring-with-dmvs.md)


## <a name="related-links"></a>Liens connexes

- [OLTP (optimisation de la mémoire) en mémoire](http://msdn.microsoft.com/library/dn133186.aspx)

- [Présentation des procédures stockées compilées en mode natif](http://msdn.microsoft.com/library/dn133184.aspx)

- [Gestionnaire d’optimisation mémoire](http://msdn.microsoft.com/library/dn284308.aspx)

