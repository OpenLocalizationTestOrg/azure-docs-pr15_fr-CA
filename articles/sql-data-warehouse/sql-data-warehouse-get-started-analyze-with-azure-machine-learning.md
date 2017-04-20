<properties
   pageTitle="Analyser des données avec Azure Machine Learning | Microsoft Azure"
   description="Pour créer une machine prédictive apprentissage du modèle basé sur les données stockées dans l’entrepôt de données de SQL Azure, utilisez Azure Machine d’apprentissage."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/14/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="analyze-data-with-azure-machine-learning"></a>Analyser des données avec Azure Machine Learning

> [AZURE.SELECTOR]
- [Alimentation BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprentissage automatique Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Ce didacticiel utilise une Machine d’apprentissage Azure pour créer une machine prédictive apprentissage du modèle basé sur les données stockées dans l’entrepôt de données SQL Azure. Plus précisément, cela génère une campagne marketing ciblée pour Adventure Works, le magasin de vélo, à prévoir si un client est susceptible d’acheter un vélo ou non.

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]


## <a name="prerequisites"></a>Conditions préalables
Pour exécuter ce didacticiel, vous devez :

- Un entrepôt de données SQL préchargé avec des exemples de données AdventureWorksDW. Pour configurer cela, voir [créer un entrepôt de données SQL][] et choisissez de charger les données d’exemple. Si vous déjà avez un data warehouse mais n’avez pas d’exemples de données, vous pouvez [charger manuellement les données de l’exemple][].

## <a name="1-get-data"></a>1. obtenir des données
Les données sont dans l’affichage de la dbo.vTargetMail dans la base de données AdventureWorksDW. Pour lire ces données :

1. Dans un [studio de formation de Machine d’Azure][] , cliquez sur Mes expériences.
2. Cliquez sur **+ Nouveau** et sélectionnez **l’Essai à blanc**.
3. Entrez un nom pour votre expérience : ciblé Marketing.
4. Faites glisser le module de **lecteur** à partir du volet de modules dans la zone de dessin.
5. Dans le volet Propriétés, spécifiez les détails de votre base de données SQL Data Warehouse.
6. Spécifiez la **requête** de base de données pour lire les données d’intérêt.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Exécutez l’expérimentation en cliquant sur **exécuter** dans la zone d’essai.
![Exécution de l’essai][1]


Après que l’essai est terminée avec succès, cliquez sur le port de sortie en bas du module lecteur et sélectionnez **visualiser** pour voir les données importées.
![Afficher les données importées][3]


## <a name="2-clean-the-data"></a>2. nettoyage des données
Pour nettoyer les données, supprimer des colonnes qui ne sont pas pertinentes pour le modèle. Pour ce faire :

1. Dans la zone de dessin, faites glisser le module de **Colonnes du projet** .
2. Dans le volet Propriétés pour spécifier les colonnes que vous souhaitez supprimer, cliquez sur **sélecteur de colonne de lancement** .
![Colonnes de projet][4]

3. Exclure les deux colonnes : CustomerAlternateKey et GeographyKey.
![Supprimez les colonnes inutiles][5]


## <a name="3-build-the-model"></a>3. construire le modèle
Il fractionne les données 80-20 : 80 % pour la formation d’une machine de formation de modèle et 20 % pour tester le modèle. Nous nous engageons à utiliser des algorithmes de « Deux-Class » pour ce problème de classification binaire.

1. Dans la zone de dessin, faites glisser le module de **fractionnement** .
2. Entrez 0,8 pour la Fraction de lignes dans le premier groupe de données de sortie dans le volet Propriétés.
![Fractionner les données en jeu de formation et de test][6]
3. Dans la zone de dessin, faites glisser le module de **Classe de deux amplifiée un arbre de décision** .
4. Faites glisser le module du **Modèle de Train** dans la zone de dessin et de spécifier les entrées. Puis, cliquez sur **lancer le sélecteur de colonne** dans le volet Propriétés.
      - Première entrée : algorithme de ML.
      - Deuxième entrée : données pour l’apprentissage de l’algorithme sur.
![Connectez le module du modèle de Train][7]
5. Sélectionnez la colonne pour prévoir la colonne **BikeBuyer** .
![Sélectionnez la colonne à prévoir][8]


## <a name="4-score-the-model"></a>4. le modèle de score
Maintenant, nous testerons comment le modèle s’exécute sur les données de test. Nous allons comparer l’algorithme de notre choix avec un autre algorithme pour voir laquelle fonctionne le mieux.

1. Dans la zone de dessin, faites glisser **Le modèle de Score de** module.
    Première entrée : formé de la deuxième entrée du modèle : données de Test ![le modèle de Score][9]
2. Faites glisser le **Point de Bayes de deux-classe Machine** dans le canevas de l’expérimentation. Nous allons comparer le fonctionnement de cet algorithme par rapport à l’arbre de décision deux-classe amplifiée.
3. Copiez et collez les modules Train modèle et le modèle de Score dans la zone de dessin.
4. Faites glisser le module à **Évaluer le modèle** dans la zone de dessin pour comparer les deux algorithmes.
5. **Exécution de** l’expérience.
![Exécution de l’essai][10]
6. Cliquez sur le port de sortie en bas du module évaluer le modèle, puis cliquez sur visualiser.
![Visualiser les résultats de l’évaluation][11]

Les métriques fournies sont la courbe ROC, la courbe de diagramme et ascenseur de rappel de la précision. En examinant ces métriques, nous pouvons voir que le premier modèle obtenu des résultats supérieurs à la deuxième. Pour consulter le premier modèle prédit, sur le port de sortie du modèle de Score puis cliquez sur visualiser.
![Visualiser les résultats du score][12]

Vous verrez deux colonnes supplémentaires ajoutés à votre groupe de données de test.

- Les probabilités évaluées : la probabilité qu’un client est un acheteur de vélo.
- Étiquettes évaluées : la classification effectuée par le modèle – l’acheteur de vélo (1) ou non (0). Ce seuil de probabilité pour l’étiquetage est défini à 50 % et peut être ajusté.

Comparaison de la colonne BikeBuyer (réel) et les étiquettes d’un score (prévision), vous pouvez voir la façon dont le modèle a effectué. Dans les étapes suivantes, vous pouvez utiliser ce modèle pour faire des prévisions pour les nouveaux clients et publier ce modèle sous la forme d’un service web ou écrire les résultats à l’entrepôt de données SQL.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la création d’apprentissage des modèles de l’ordinateur prédictive, consultez [Introduction à la Machine d’apprentissage sur Azure][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure studio d’apprentissage automatique]:https://studio.azureml.net/
[Introduction à la formation sur Azure de l’ordinateur]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[charger les données d’exemple manuellement]: sql-data-warehouse-load-sample-databases.md
[Créez un entrepôt de données SQL]: sql-data-warehouse-get-started-provision.md
