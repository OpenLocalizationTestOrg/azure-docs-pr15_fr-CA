<properties 
    pageTitle="Exemples créés avec R des services web de formation de machine | Microsoft Azure" 
    description="Rechercher un jeu d’utile du site web des exemples de services créé avec code de R et à l’apprentissage de l’ordinateur et publié sur le marché d’Azure." 
    keywords="CSharp, code de r, exemples de services web"
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="web-services-examples-using-r-code-on-azure-machine-learning-and-published-to-microsoft-azure-marketplace"></a>Web services les exemples à l’aide du code de R sur Azure Machine Learning et publiés sur Microsoft Azure Marketplace

Dans cet article sont web exemple services ont été créés à l’aide de la formation de Machine Azure et publiés vers Azure Marketplace. Chaque exemple de service web est un document complet associé, l’incorporation des ensembles de données exemple pour tester les services et expliquant comment l’utilisateur peut créer un service similaire eux-mêmes. 

Dans un Studio de formation de Machine Azure, les utilisateurs peuvent écrire du code de R et en quelques clics, le publier sous la forme d’un service web pour les applications et les périphériques à utiliser dans le monde entier. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


À partir de la production des calculatrices simples qui fournissent des fonctionnalités de statistiques création PRÉDICTEUR analyse sentiment texte personnalisé, les utilisateurs de R des débutants et expérimentés peuvent bénéficier de la facilité avec laquelle les utilisateurs d’apprentissage d’ordinateur Azure effectuent des code de R. Lors de ces services peuvent être consommés par les utilisateurs, via une application mobile ou un site Web, l’objectif de ces exemples de services web de web est de montrer le mode apprentissage automatique peut mettre R des scripts à des fins analytiques et être utilisé pour créer des services web sur le code de R.

Chaque exemple inclut un exemple C# pour la consommation du service web.


![Diagramme du code R dans Azure Machine Learning : solutions propriétaires R utilisez ou publiée vers Azure Marketplace.][1]

Examinons les scénarios suivants.

##<a name="scenario-1-generic-model"></a>Scénario 1 : Modèle générique 
Un utilisateur travaille avec un modèle générique qui peut être appliqué à des données d’un nouvel utilisateur, par exemple une prévision de base de données série au moment ou une méthode R personnalisée avec analytique avancée. Cet utilisateur publie le modèle sous la forme d’un service web pour d’autres utilisateurs d’utiliser leurs données.



* [Classifieur binaire](machine-learning-r-csharp-binary-classifier.md)
* [Modèle de cluster](machine-learning-r-csharp-cluster-model.md)
* [MULTIVARIÉE régression linéaire](machine-learning-r-csharp-multivariate-linear-regression.md)
* [Prévisions - lissage exponentiel](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [ETS + prévision de STL](machine-learning-r-csharp-retail-demand-forecasting.md)
* [Prévisions - AUTOREGRESSIVE a intégré la moyenne mobile (ARIMA)](machine-learning-r-csharp-arima.md)
* [Analyse de survie](machine-learning-r-csharp-survival-analysis.md)


##<a name="scenario-2-trained-model--specific-data"></a>Scénario 2 : Formé modèle de données spécifique 
Un utilisateur dispose de données qui fournissent des prévisions utiles à travers le code R, par exemple un échantillon large des questionnaires de personnalité en clusters via un algorithme des moyens k pour prédire l’utilisateur type de personnalité ou données de l’enquête sanitaire qui peuvent être utilisées pour prédire le risque d’un individu au cancer du poumon avec un package d’analyse R de survie. L’utilisateur publie les données via un service web qui prédit les résultats d’un nouvel utilisateur.

##<a name="scenario-3-trained-model--generic-data"></a>Scénario 3 : Formé modèle de données génériques 
Un utilisateur a des données génériques (par exemple, un corpus de texte) qui permettent à un service web doivent être générés et appliqué générique sur différents types de scénarios et cas d’usage.

* [Lexique basé Sentiment analyse](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

##<a name="scenario-4-advanced-calculator"></a>Scénario 4 : Calculatrice avancée 
Un utilisateur fournit des calculs complexes ou des simulations qui ne nécessitent pas un modèle formé ou un raccord d’un modèle de données de l’utilisateur.

* [Différence de Proportions Test](machine-learning-r-csharp-difference-in-two-proportions.md)
* [Suite de la Distribution normale](machine-learning-r-csharp-normal-distribution.md)
* [Distribution binomiale Suite](machine-learning-r-csharp-binomial-distribution.md)

##<a name="faq"></a>FAQ
Pour les questions fréquemment posées sur la consommation du service web ou de publication sur le marché, voir [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png


 
