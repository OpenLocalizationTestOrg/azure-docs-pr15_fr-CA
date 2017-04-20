<properties
   pageTitle="Utiliser l’apprentissage automatique Azure avec SQL Data Warehouse | Microsoft Azure"
   description="Didacticiel pour l’utilisation de formation de Machine Azure avec l’entrepôt de données SQL Azure pour le développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Utiliser l’apprentissage automatique Azure avec SQL Data Warehouse

L’apprentissage Machine Azure est un service entièrement géré analytique prédictive que vous pouvez utiliser pour créer des modèles prédictifs sur vos données dans l’entrepôt de données SQL et puis publier en tant que services web de prêt-à-consommer. Vous pouvez apprendre les principes fondamentaux d’analytique prédictive et machine d’apprentissage en lisant [l’Introduction à la Machine d’apprentissage sur Azure][].  Vous pouvez ensuite apprendre à créer, former, score et test d’un modèle d’apprentissage automatique à l’aide de l' [expérimenter le didacticiel de création][].

Dans cet article, vous allez apprendre à effectuer les opérations suivantes à l’aide du [Studio de formation de Machine Azure][]:

- Lire les données de votre base de données de créer, de former et de score d’un modèle de prévision
- Écrire des données dans votre base de données


## <a name="read-data-from-sql-data-warehouse"></a>Lecture de données à partir de l’entrepôt de données SQL

Données seront lues à partir de la table Product dans la base de données AdventureWorksDW.

### <a name="step-1"></a>Étape 1

Démarrer une nouvelle expérience en cliquant sur + Nouveau en bas de la fenêtre de la Machine Learning Studio, sélectionnez expérience, puis tester vide. Sélectionnez le nom d’expérimentation par défaut en haut de la zone de dessin et renommez-le en quelque chose de significatif, par exemple, les prévision de prix de bicyclette.

### <a name="step-2"></a>Étape 2

Recherchez le module de lecteur de la palette de jeux de données et les modules sur la gauche de la zone d’essai. Faites glisser le module pour la toile de l’expérimentation.
![][drag_reader]

### <a name="step-3"></a>Étape 3

Sélectionnez le module de lecteur et remplissez le volet des propriétés.

1. Sélectionnez la Source de données de la base de données SQL Azure.
2. Nom du serveur de base de données : tapez le nom du serveur. Vous pouvez utiliser le [portail Azure][] pour trouver ceci.

![][server_name]

3. Nom de la base de données : tapez le nom d’une base de données sur le serveur que vous venez de spécifier.
4. Nom du compte utilisateur serveur : tapez le nom d’utilisateur d’un compte qui dispose des autorisations d’accès pour la base de données.
5. Mot de passe utilisateur serveur : fournir le mot de passe pour le compte d’utilisateur spécifié.
6. Accepter un certificat de serveur : utilisez cette option (moins sûre) si vous souhaitez ignorer la vérification du certificat du site avant de lire vos données.
7. Requête de base de données : entrez une instruction SQL qui décrit les données que vous souhaitez lire. Dans ce cas, nous lit des données à partir de la table de produit à l’aide de la requête suivante.


```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Étape 4

1. Exécutez l’expérimentation en cliquant sur Exécuter dans la zone d’essai.
2. Lorsque l’essai est terminée, le module de lecteur aura une coche verte pour indiquer qu’il a terminé avec succès. Notez également le terminé, état d’exécution dans le coin supérieur droit.

![][run]

3. Pour afficher les données importées, cliquez sur le port de sortie en bas du groupe de données automobile et sélectionnez visualiser.


## <a name="create-train-and-score-a-model"></a>Créer, de former et d’un modèle de score

Vous pouvez désormais utiliser ce dataset pour :

- Créer un modèle : données de processus et de définir des fonctionnalités
- Apprentissage du modèle : choisir et appliquer un algorithme d’apprentissage
- Score et tester le modèle : prévoir le nouveau prix de bicyclette


![][model]

Pour en savoir plus sur la création, du train, score et un apprentissage utilisation du modèle de l’ordinateur de test [créer tester le didacticiel][].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Écrire des données dans l’entrepôt de données SQL Azure

Nous avons écrit les résultats au tableau de ProductPriceForecast dans la base de données AdventureWorksDW.

### <a name="step-1"></a>Étape 1

Recherchez le module Writer dans la palette de jeux de données et les modules sur la gauche de la zone d’essai. Faites glisser le module pour la toile de l’expérimentation.

![][drag_writer]

### <a name="step-2"></a>Étape 2

Sélectionnez le module de Writer et remplissez le volet des propriétés.

1. Sélectionnez la Destination des données de la base de données SQL Azure.
2. Nom du serveur de base de données : tapez le nom du serveur. Vous pouvez utiliser le [portail Azure][] pour trouver ceci.
3. Nom de la base de données : tapez le nom d’une base de données sur le serveur que vous venez de spécifier.
4. Nom du compte utilisateur serveur : tapez le nom d’utilisateur d’un compte qui dispose des autorisations d’écriture pour la base de données.
5. Mot de passe utilisateur serveur : fournir le mot de passe pour le compte d’utilisateur spécifié.
6. Accepter n’importe quel certificat du serveur (non sécurisé) : sélectionnez cette option si vous ne souhaitez pas afficher le certificat.
7. Séparées par des virgules de liste de colonnes à être enregistré : fournissent une liste de colonnes dataset ou les résultats que vous souhaitez copier.
8. Nom de table de données : spécifiez le nom de la table de données.
9. Séparées par des virgules de liste de colonnes de la table de données : spécifiez les noms de colonne à utiliser dans la nouvelle table. Les noms de colonne peuvent être différentes de celles du groupe de données source, mais vous devez répertorier le même nombre de colonnes ici que vous définissez pour la table de sortie.
10. Nombre de lignes écrites par opération de SQL Azure : vous pouvez configurer le nombre de lignes qui sont écrits dans une base de données SQL en une seule opération.

![][writer_properties]

### <a name="step-3"></a>Étape 3

1. Exécutez l’expérimentation en cliquant sur Exécuter dans la zone d’essai.
2. Lorsque la fin de l’essai, tous les modules aura une coche verte pour indiquer qu’elles sont terminées.

## <a name="next-steps"></a>Étapes suivantes

Pour plus de conseils de développement, consultez [vue d’ensemble du développement de SQL Data Warehouse][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[Vue d’ensemble du développement SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Didacticiel de l’expérience de création]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduction à la formation sur Azure de l’ordinateur]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Studio d’apprentissage Machine Azure]: https://studio.azureml.net/Home
[Azure portal]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
