<properties
    pageTitle="Une solution prédictive pour les risques de crédit avec apprentissage automatique | Microsoft Azure"
    description="Une procédure pas à pas détaillé montrant comment créer une solution analytique prédictive pour l’évaluation des risques de crédit dans un Studio de formation de Machine Azure."
    keywords="risque de crédit, d’une solution de prévision analytique, d’évaluation des risques"
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
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Procédure pas à pas : Développement d’une solution d’analytique prédictive pour l’évaluation des risques de crédit dans l’apprentissage automatique de Azure

Supposons que vous devez prévoir le risque de crédit d’un individu basé sur les informations qu’ils donnent à une application de crédit.  

Évaluation des risques de crédit est un problème complexe, bien sûr, mais nous allons simplifier un peu les paramètres de la question. Ensuite, nous pouvons l’utiliser comme un exemple de comment vous pouvez utiliser Microsoft Azure Machine Learning avec Studio de formation de Machine et le service web d’apprentissage automatique pour créer une telle solution analytique prédictive.  

Dans cette procédure pas à pas détaillées, nous allons suivre le processus de développement d’un modèle analytique prédictive dans un Studio de formation de Machine et son déploiement sous la forme d’un service web de formation de Machine Azure. Nous commencer avec des données de risque de crédit disponible publiquement, développer et former un modèle de prévision basé sur ces données et puis de déployer le modèle sous la forme d’un service web qui peut être utilisé par d’autres utilisateurs pour l’évaluation des risques de crédit.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] Pour télécharger et imprimer un diagramme qui donne une vue d’ensemble des fonctionnalités de Studio d’apprentissage Machine, voir [diagramme de vue d’ensemble des fonctionnalités de Studio de formation de Machine Azure](machine-learning-studio-overview-diagram.md).

Pour créer une solution d’évaluation des risques crédit, nous allons suivre ces étapes :  

1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Charger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.  [Créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Former et d’évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

Cette procédure pas à pas repose sur une version simplifiée de la [le classement binaire : prévision du risque de crédit](http://go.microsoft.com/fwlink/?LinkID=525270) expérience d’exemple dans la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com/).
