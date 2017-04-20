<properties
    pageTitle="Étape 2 : Charger des données dans une expérience d’apprentissage automatique | Microsoft Azure"
    description="Étape 2 de la développer une procédure pas à pas solution prédictive : stockées de téléchargement sont données publiques dans le Studio de formation de Machine Azure."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Procédure pas à pas, étape 2 : Télécharger les données existantes dans une expérience d’apprentissage automatique de Azure

Il s’agit de la deuxième étape de la procédure pas à pas, [développer une solution analytique prédictive dans l’apprentissage automatique de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  **Charger des données existantes**
3.  [Créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Former et d’évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

----------

Pour développer un modèle de prévision pour le risque de crédit, nous avons besoin que nous pouvons utiliser pour former et ensuite tester le modèle de données. Pour cette procédure pas à pas, nous allons utiliser le « Jeu de données UCI Statlog (données de crédit allemande) » à partir du référentiel d’apprentissage automatique de UCI. Vous le trouverez ici :  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ICS.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Nous allons utiliser le fichier nommé **german.data**. Téléchargez ce fichier sur votre disque dur local.  

Ce groupe de données contient des lignes de 20 variables pour 1000 candidats passées pour le crédit. Ces 20 variables représentent des vecteurs de fonction du groupe de données, qui fournit des caractéristiques d’identification de chaque demandeur de crédit. Une colonne supplémentaire dans chaque ligne représente un risque de crédit calculé du demandeur, avec les 700 candidats identifiés comme un risque de crédit faible et 300 comme un risque élevé.

Le site Web de UCI fournit une description des attributs du vecteur de fonctionnalité, qui incluent des informations financières, l’historique de crédit, le statut d’emploi et des informations personnelles. Pour chacun des postulants, une notation binaire a été donnée indiquant s’ils sont d’une faible ou élevé risque de crédit.  

Nous allons utiliser ces données pour former un modèle analytique prédictive. Lorsque nous avons terminé, notre modèle doit être en mesure d’accepter les informations pour les personnes nouvelles et de prédire si elles sont un risque faible ou élevé.  

Un petit changement intéressant ici Voici. La description du groupe de données explique que classer par erreur d’entrées une personne comme un risque de crédit faible lorsqu’ils sont en fait un risque élevé de crédit est 5 fois plus coûteux à l’établissement financier à un faible risque élevé de classer par erreur entrées. Une méthode simple pour en tenir compte dans notre expérience est la duplication (5 fois) les écritures représentant une personne présentant un risque élevé de crédit. Ensuite, si le modèle de misclassifies un risque élevé de crédit comme faible, il le fera cette mauvaise classification 5 fois, une fois pour chaque double. Cela augmente le coût de cette erreur dans les résultats de la formation.  

##<a name="convert-the-dataset-format"></a>Convertir le format du groupe de données
Le dataset d’origine utilise un format séparé par des vides. Studio d’apprentissage machine fonctionne mieux avec un fichier de valeurs séparées par des virgules (CSV), afin que nous allons convertir le groupe de données en remplaçant les espaces par des virgules.  

Il existe de nombreux moyens de convertir ces données. La première consiste à l’aide de la commande Windows PowerShell suivante :   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Une autre méthode consiste à l’aide de la commande sed Unix :  

    sed 's/ /,/g' german.data > german.csv  

Dans les deux cas, nous avons créé une version séparée par des virgules des données dans un fichier nommé **german.csv** que nous utiliserons dans notre expérience.

##<a name="upload-the-dataset-to-machine-learning-studio"></a>Charger le groupe de données à un Studio de formation de Machine

Une fois que les données ont été converties au format CSV, il faut la télécharger dans un Studio de formation de Machine. Pour plus d’informations sur la mise en route avec le Studio d’apprentissage Machine, voir [Accueil de Studio de formation Microsoft Azure Machine](https://studio.azureml.net/).

1.  Ouvrir l’apprentissage Studio ([https://studio.azureml.net](https://studio.azureml.net)) de l’ordinateur. Lorsque vous êtes invité à vous connecter, utilisez le compte que vous avez spécifié comme propriétaire de l’espace de travail.
1.  Cliquez sur l’onglet **Studio** en haut de la fenêtre.
1.  Cliquez sur **+ Nouveau** en bas de la fenêtre.
1.  Sélectionnez le **groupe de données**.
1.  Sélectionnez **fichier LOCAL de FROM**.
1.  Dans la boîte de dialogue **télécharger un nouveau groupe de données** , cliquez sur **Parcourir** et recherchez le fichier **german.csv** que vous avez créé.
1.  Entrez un nom pour le groupe de données. Pour cette procédure pas à pas, nous appellerons « Données de carte de crédit UCI allemand ».
1.  Type de données, sélectionnez **générique fichier CSV sans en-tête (. nh.csv)**.
1.  Ajoutez une description si vous le souhaitez.
1.  Cliquez sur **OK**.  

![Charger le groupe de données][1]  


Ceci met à jour les données dans un module de dataset que nous pouvons utiliser dans une expérience.

> [AZURE.TIP] Pour gérer des groupes de données que vous avez téléchargée à Studio, cliquez sur l’onglet **groupes de données** vers la gauche de la fenêtre de Studio.

Pour plus d’informations sur l’importation des différents types de données dans une expérience, voir [importation de vos données de formation dans un Studio de formation de Machine Azure](machine-learning-data-science-import-data.md).

**Suivante : [créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
