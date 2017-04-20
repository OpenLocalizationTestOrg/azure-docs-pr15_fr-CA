<properties 
    pageTitle="Quel est le Studio d’apprentissage Azure Machine ? | Microsoft Azure"
    description="Vue d’ensemble de Azure ML Studio, un outil glisser-déplacer pour créer rapidement des modèles à partir d’une bibliothèque de prêt à utiliser des algorithmes et des modules."
    keywords="Azure machine d’apprentissage, azure ml, studio de ml"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/09/2016"
    ms.author="garye"/>

# <a name="what-is-azure-machine-learning-studio"></a>Quel est le Studio d’apprentissage Azure Machine ?

Microsoft Azure Machine Learning Studio est un outil de collaboration, de glisser-déplacer que vous permet de créer, tester et déployer des solutions d’analytique prédictive sur vos données. Studio d’apprentissage machine publie des modèles en tant que services web qui peuvent facilement être consommées par les applications personnalisées ou Outils BI comme Excel.

Studio de formation de machine est l’intersection de science de données, analytique prédictive, ressources de cloud et vos données.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>L’espace de travail interactive Studio de formation de Machine

Pour développer un modèle d’analyse prédictive, vous en général utilisez les données d’une ou plusieurs sources, transformez et analysez ces données par l’intermédiaire de différents de manipulation des données et de fonctions statistiques et générez un jeu de résultats. Développement d’un modèle de ce type est un processus itératif. Lorsque vous modifiez les différentes fonctions et leurs paramètres, les résultats de convergent jusqu'à ce que vous êtes satisfait du résultat que vous avez un modèle formé et efficace.

**Azure Machine Learning Studio** vous donne un espace de travail interactif et visuel pour facilement créer, tester et effectuer une itération sur un modèle d’analyse prédictive. Vous faites glisser-déplacer ***des groupes de données*** et analyse de ***modules*** sur un canevas interactive, en les reliant entre eux pour former un ***tester***, que vous lancez dans un Studio de formation de Machine. Pour effectuer une itération sur la conception de votre modèle, que vous modifiez l’expérimentation, enregistrez une copie si vous le souhaitez et exécutez-le à nouveau. Lorsque vous êtes prêt, vous pouvez convertir votre ***formation essayer*** une ***expérience prédictive***et publier en tant que ***service web*** afin que votre modèle est accessible par d’autres utilisateurs.

>[AZURE.TIP] Pour télécharger et imprimer un diagramme qui donne une vue d’ensemble des fonctionnalités de Studio d’apprentissage Machine, voir [diagramme de vue d’ensemble des fonctionnalités de Studio de formation de Machine Azure](machine-learning-studio-overview-diagram.md).

Il n’y a aucune programmation, connexion simplement visuellement des datasets et des modules pour construire votre modèle d’analyse prédictive.

![Diagramme de ML Studio Azure : créer des expériences, lire les données de nombreuses sources, écrire des données évaluées, écrire des modèles.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Mise en route de Studio de formation de Machine

Lorsque vous entrez tout d’abord la [Machine Learning Studio](https://studio.azureml.net) vous consultez la page **d’accueil** . À partir de là, vous pouvez afficher la documentation, des vidéos, des séminaires en ligne et trouver des ressources précieuses.

Il existe trois onglets dans la partie supérieure : **accueil** (où vous démarrez) **Studio**et **Galerie**.

### <a name="studio"></a>Studio

Cliquez sur l’onglet de **Studio** et vous devrez vous connecter en utilisant votre compte Microsoft ou votre compte de travail ou l’école. Une fois connecté, vous verrez les onglets suivants sur la gauche :

- **Projets** - Collections des expériences, des groupes de données, ordinateurs portables et autres ressources représentant un projet unique
- **Essais** - essais qui ont été créés, exécutez et enregistré en tant que brouillons
- **SERVICES WEB** - services Web que vous avez déployé à partir de vos expériences
- **Ordinateurs portables** - ordinateurs portables Jupyter que vous avez créé
- **Groupes de données** - des groupes de données que vous avez téléchargé en Studio
- **Formé de modèles** - les modèles que vous avez formé dans des expériences et enregistrés dans un Studio
- **Paramètres** - une collection de paramètres que vous pouvez utiliser pour configurer votre compte et ressources.

### <a name="gallery"></a>Galerie

Cliquez sur l’onglet **Galerie** et vous serez dirigé vers la galerie d’Intelligence Cortana. La bibliothèque est un emplacement où une Communauté de chercheurs de données et les développeurs peut partager les solutions créées à l’aide de composants de la Suite d’Intelligence Cortana.

Pour plus d’informations sur la galerie, consultez [partager et découvrir les solutions de la galerie d’Intelligence Cortana](machine-learning-gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Composants d’une expérience

Une expérience se compose de groupes de données qui fournissent des données pour les modules analyse vous connectez ensemble pour construire un modèle d’analyse prédictive. Plus précisément, une expérience valide possède ces caractéristiques :

- L’expérience a au moins un groupe de données et d’un module
- Des groupes de données peut être connecté uniquement à des modules
- Les modules peuvent être connectés à des groupes de données ou d’autres modules
- Tous les ports d’entrée pour les modules doivent avoir une connexion pour le flux de données
- Tous les requis vous devez configurer les paramètres de chaque module

Vous pouvez créer une expérience de toutes pièces, ou vous pouvez utiliser une expérience exemple existant comme modèle. Pour plus d’informations, consultez les [expériences sur des échantillons utilisé pour créer de nouvelles expériences](machine-learning-sample-experiments.md).

Pour obtenir un exemple de création d’une expérience simple, voir [Création d’une expérience simple dans Azure Machine Learning Studio](machine-learning-create-experiment.md).

Pour une procédure pas à pas plus complète de la création d’une solution analytique prédictive, voir [Création d’une solution prédictive avec formation de Machine Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="datasets"></a>Groupes de données

Un groupe de données est des données qui a été téléchargées pour Machine Learning Studio afin qu’il peut être utilisé dans le processus de modélisation. Un certain nombre de groupes de données exemple est inclus dans un Studio de formation de Machine pour expérimenter, et vous pouvez télécharger plusieurs groupes de données dont vous avez besoin. Voici quelques exemples de groupes de données inclus :

- **Données MPG pour automobiles divers** - Miles par gallon (MPG) des valeurs pour les automobiles identifiés par le nombre de cylindres, puissance, etc..
- **Données sur le cancer du sein** - données de diagnostic du cancer du sein.
- **Données des incendies de forêt** - tailles d’incendie de forêt au Portugal nord-est.

Lorsque vous créez une expérience, que vous pouvez choisir dans la liste des groupes de données disponible à gauche de la zone de dessin.

Pour obtenir la liste des groupes de données exemple inclus dans le Studio d’apprentissage Machine, voir [utiliser les jeux de données d’exemple dans un Studio de formation de Machine Azure](machine-learning-use-sample-datasets.md).

### <a name="modules"></a>Modules

Un module est un algorithme que vous pouvez effectuer sur vos données. Studio de formation de machine a un certain nombre de modules allant de fonctions d’entrée de données pour la formation, la notation et les processus de validation. Voici quelques exemples de modules inclus :

- [Convertir en ARFF] [ convert-to-arff] -convertit un groupe de données sérialisé .NET à Relation d’attribut de fichier de Format (ARFF).
- [Calcul des statistiques élémentaires] [ elementary-statistics] -calcule les statistiques élémentaires (moyenne, écart type, etc.).
- [Régression linéaire] [ linear-regression] -crée un modèle en ligne dégradé basée sur une profondeur droite de régression linéaire.
- [Modèle de score] [ score-model] -obtenant un modèle formé de classification ou de régression.

Lorsque vous créez une expérience, que vous pouvez choisir dans la liste des modules disponibles à gauche de la zone de dessin.  

Un module peut avoir un jeu de paramètres que vous pouvez utiliser pour configurer les algorithmes internes du module. Lorsque vous sélectionnez un module dans la zone de travail, les paramètres du module sont affichés dans le volet de **Propriétés** à droite de la zone de dessin. Vous pouvez modifier les paramètres de ce volet pour analyser votre modèle.

Pour une aide pour la navigation via la grande bibliothèque d’algorithmes d’apprentissage machine disponibles, reportez-vous à la section [comment choisir les algorithmes d’apprentissage de Microsoft Azure Machine](machine-learning-algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Déploiement d’un service web d’analytique prédictive

Une fois votre modèle prédictif analytique est prêt, vous pouvez le déployer comme un service web à partir de Studio de formation de Machine. Pour plus d’informations sur ce processus, voir [déploiement d’un service web de formation de Machine Azure](machine-learning-publish-a-machine-learning-web-service.md).

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
