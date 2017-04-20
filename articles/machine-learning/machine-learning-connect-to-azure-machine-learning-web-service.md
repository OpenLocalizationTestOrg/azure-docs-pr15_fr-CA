<properties
    pageTitle="Se connecter à un Service Web de formation ordinateur | Microsoft Azure"
    description="C# ou Python, vous connecter à un service Web de formation Azure Machine à l’aide d’une clé d’autorisation."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016" 
    ms.author="garye" />


# <a name="connect-to-an-azure-machine-learning-web-service"></a>Se connecter à un Service Web de formation Machine Azure

L’apprentissage de développeur Azure Machine est une API de service Web pour effectuer des prévisions à partir de données d’entrée en temps réel ou en mode batch. Studio de formation de Machine Azure permet de créer des prévisions et le déploiement d’un service Web de formation de Machine Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Pour en savoir plus sur la façon de créer et de déployer un service Web de formation de Machine à l’aide de Studio de formation de Machine :

- Pour voir un didacticiel sur la création d’une expérience dans le Studio d’apprentissage Machine, [créer votre première expérience](machine-learning-create-experiment.md).
- Pour plus d’informations sur la façon de déployer un service Web, voir [déploiement d’un service Web de formation de Machine](machine-learning-publish-a-machine-learning-web-service.md).
- Pour plus d’informations sur l’apprentissage automatique, en général, visitez le [Centre de Documentation de formation](https://azure.microsoft.com/documentation/services/machine-learning/).

## <a name="azure-machine-learning-web-service"></a>Services Web de formation de Machine Azure ##

Avec le service Web de formation Azure Machine, une application externe communique avec un modèle de workflow score apprentissage automatique en temps réel. Un appel de service Web de formation de Machine renvoie les résultats des prévisions à une application externe. Pour appeler un service Web de formation de Machine, vous passez une clé d’API qui est créée lorsque vous déployez une prévision. Le service Web de formation de Machine est basé sur d’autres, un choix d’architecture les plus courants pour les projets de programmation web.

Formation de Machine Azure comprend deux types de services :

- Le Service de requête-réponse (RR) – une latence faible, le service hautement évolutive qui fournit une interface pour les modèles sans état créé et déployé depuis le Studio d’apprentissage Machine.
- Lot Service d’exécution (BES) – asynchrone de service que les scores un traitement par lots pour les enregistrements de données.

Pour plus d’informations sur les services Web d’apprentissage Machine, voir [déploiement d’un service Web de formation de Machine](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Obtenir une clé d’autorisation de formation de Machine Azure ##

Lorsque vous déployez votre expérience, les clés de l’API sont générées pour le service Web. Vous pouvez récupérer les clés à partir de plusieurs emplacements.

## <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>À partir du portail de Services Web de Microsoft Azure Machine Learning

Se connecter au portail [Microsoft Azure Machine Learning Web Services](https://services.azureml.net) .

Pour récupérer la clé d’API pour un service Web de formation nouvelle Machine :

1. Dans le portail Azure Machine Learning Web Services, cliquez sur **Services Web** du menu supérieur.
2. Cliquez sur le service Web pour lequel vous souhaitez récupérer la clé.
3. Dans le menu supérieur, cliquez sur **consommer**.
4. Copier et enregistrer la **Clé primaire**.


Pour récupérer la clé d’API pour un service Web de formation classique Machine :

1. Dans le portail Azure Machine Learning Web Services, cliquez sur **Les Services Web classique** du menu supérieur.
2. Cliquez sur le service Web avec laquelle vous travaillez.
3. Cliquez sur le point de terminaison pour lequel vous souhaitez récupérer la clé.
3. Dans le menu supérieur, cliquez sur **consommer**.
4. Copier et enregistrer la **Clé primaire**.

## <a name="classic-web-service"></a>Services Web classique ##

 Vous pouvez également récupérer une clé pour un service Web classique de Studio de formation de Machine ou le portail Azure.

### <a name="machine-learning-studio"></a>Studio d’apprentissage machine ###

1. Dans un Studio de formation de Machine, cliquez sur **SERVICES WEB** sur la gauche.
2. Cliquez sur un service Web. La **clé de l’API** est sous l’onglet **tableau de bord** .

### <a name="azure-portal"></a>Azure portal ###

1. Sur la gauche, cliquez sur **Apprentissage automatique** .
2. Cliquez sur l’espace de travail dans lequel se trouve votre service Web.
3. Cliquez sur **les SERVICES WEB**.
4. Cliquez sur un service Web.
5. Cliquez sur un point de terminaison. La clé « API » est vers le bas dans l’angle inférieur droit.

## <a id="connect"></a>Se connecter à un service Web de formation de Machine

Vous pouvez vous connecter à un service Web de formation de Machine à l’aide de n’importe quel langage de programmation qui prend en charge une réponse et une demande HTTP. Vous pouvez consulter des exemples en C#, Python et R à partir d’une page d’aide de service Web de formation de Machine.

**Aide de l’API d’apprentissage machine** Aide de API d’apprentissage d’ordinateur est créé lorsque vous déployez un service Web. Consultez la [procédure pas à pas d’apprentissage automatique Azure - déployer le Service Web](machine-learning-walkthrough-5-publish-web-service.md).
L’aide de l’API d’apprentissage Machine contient des détails sur une service Web de prévision.

2. Cliquez sur le service Web avec laquelle vous travaillez.
3. Cliquez sur le point de terminaison pour lequel vous souhaitez afficher la Page d’aide de l’API.
3. Dans le menu supérieur, cliquez sur **consommer**.
3. Cliquez sur **page d’aide API** sous la requête-réponse ou points de terminaison de l’exécution par lots.

**En mode apprentissage automatique API aide pour un service Web de nouvelles**

Dans la Machine Azure Web Services portail d’apprentissage :

1. Dans le menu supérieur, cliquez sur **Les SERVICES WEB** .
2. Cliquez sur le service Web pour lequel vous souhaitez récupérer la clé.

Cliquez sur **consommer** pour obtenir l’URI de la demande-Reposonse et les exemples de code et de Services d’exécution de traitement par lots dans C#, R et les Python.

Cliquez sur **Swagger les API** pour obtenir la documentation de base de Swagger pour les API appelées à partir de l’URI fourni.

### <a name="c-sample"></a>Exemple C# ###

Pour vous connecter à un service Web d’apprentissage Machine, utilisez un **HttpClient** , passant de ScoreData. ScoreData contient un FeatureVector, un vecteur à n dimensions des fonctions numériques qui représente le ScoreData. Vous authentifiez le service formation de l’ordinateur avec une clé d’API.

Pour vous connecter à un service Web de formation de Machine, le package NuGet **Microsoft.AspNet.WebApi.Client** doit être installé.

**Installer Microsoft.AspNet.WebApi.Client NuGet dans Visual Studio**

1. Publier le dataset de téléchargement à partir de UCI : 2 adultes classe dataset Service Web.
2. Cliquez sur **Outils** > **du Gestionnaire de package de NuGet** > **Console du Gestionnaire de package**.
2. Choisissez le **Package d’installation Microsoft.AspNet.WebApi.Client**.

**Pour exécuter l’exemple de code**

1. Publier « exemple 1 : télécharger un dataset à partir de UCI : adulte 2 classe dataset « expérimentation, la partie de la collecte d’échantillons apprentissage automatique.
2. Affecter des apiKey la clé à partir d’un service Web. Reportez-vous à **obtenir une clé d’autorisation de formation de Machine Azure** ci-dessus.
3. Affecter serviceUri doté de l’URI de la demande.


### <a name="python-sample"></a>Exemple Python ###

Pour vous connecter à un service Web d’apprentissage Machine, utilisez la bibliothèque de **urllib2** en passant ScoreData. ScoreData contient un FeatureVector, un vecteur à n dimensions des fonctions numériques qui représente le ScoreData. Vous authentifiez le service formation de l’ordinateur avec une clé d’API.


**Pour exécuter l’exemple de code**

1. Déployer « exemple 1 : télécharger un dataset à partir de UCI : adulte 2 classe dataset « expérimentation, la partie de la collecte d’échantillons apprentissage automatique.
2. Affecter des apiKey la clé à partir d’un service Web. Reportez-vous à la section **obtenir une clé d’autorisation de formation de Machine Azure** au début de cet article.
3. Affecter serviceUri doté de l’URI de la demande.
