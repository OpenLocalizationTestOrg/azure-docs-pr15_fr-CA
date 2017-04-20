<properties 
    pageTitle="Déboguer votre modèle dans l’apprentissage automatique Azure | Microsoft Azure" 
    description="Explique comment déboguer votre modèle dans l’apprentissage automatique de Azure." 
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
    ms.topic="article" 
    ms.date="09/09/2016" 
    ms.author="bradsev;garye" />

# <a name="debug-your-model-in-azure-machine-learning"></a>Déboguer votre modèle dans l’apprentissage automatique Azure

Cet article explique comment déboguer vos modèles dans la formation de Machine Microsoft Azure. En particulier, il traite les raisons possibles, pourquoi un des deux scénarios de défaillance suivants susceptibles de survenir lors de l’exécution d’un modèle :

* le [Modèle de Train] [ train-model] module lève une erreur 
* le [Modèle de Score] [ score-model] module produit des résultats incorrects 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-throws-an-error"></a>Module de modèle train lève une erreur

![Image1](./media/machine-learning-debug-models/train_model-1.png)

Le [Modèle de Train] [ train-model] Module attend les 2 entrées suivantes :

1. Le type de modèle de Classification/Regression de la collection de modèles fournie par Machine Azure
2. Les données d’apprentissage avec une colonne de l’étiquette spécifiée. La colonne étiquette spécifie la variable à prédire. Le reste des colonnes incluses sont supposées pour être des fonctionnalités.

Ce module génère une erreur dans les cas suivants :

1. La colonne de l’étiquette est incorrect, car plus d’une colonne est sélectionnée comme étiquette ou un index de colonne incorrect est sélectionné. Par exemple, le deuxième cas appliquera si un index de colonne de 30 a été utilisé avec un groupe de données d’entrée qui avait uniquement 25 colonnes.

2. Le groupe de données ne contient-elle pas de colonnes de la fonctionnalité. Par exemple, si le dataset d’entrée possède uniquement 1 colonne, qui est marquée en tant que la colonne libellé, il ne serait aucuns fonctionnalités de génération du modèle. Dans ce cas, le [Modèle de Train] [ train-model] module génère une erreur.

3. Le dataset d’entrée (fonctions ou étiquette) contiennent l’infini comme valeur.


## <a name="score-model-module-does-not-produce-correct-results"></a>Module de modèle de score ne produit pas les résultats corrects

![Image2](./media/machine-learning-debug-models/train_test-2.png)

Dans un graphique classique de formation et les tests pour surveillés de formation, les [Données fractionnées] [ split] module divise le dataset d’origine en deux parties : la partie qui est utilisée pour exercer le modèle et la partie réservée pour évaluer comment le modèle formé effectue sur les données qu’il ne pas former sur. Le modèle formé est ensuite utilisé pour évaluer les données de test après laquelle les résultats sont évalués pour déterminer la précision du modèle.

Le [Modèle de Score] [ score-model] module requiert deux entrées :

1. Une sortie de modèle formé à partir du [Modèle de Train] [ train-model] module
2. Notation d’un groupe de données n’est pas que le modèle n’a pas formé sur

Il peut arriver que, même si l’essai aboutit, le [Modèle de Score] [ score-model] module produit des résultats incorrects. Plusieurs scénarios peuvent provoquer cette situation :

1. Si l’étiquette spécifiée est catégorique et un modèle de régression est formé sur les données, une sortie incorrecte serait produit par le [Modèle de Score] [ score-model] module. C’est pourquoi la régression requiert une variable d’une réponse continue. Dans ce cas, il doit être plus approprié d’utiliser un modèle de classification. 
2. De même, si un modèle de classification est exercé sur un groupe de données comportant des nombres à virgule flottante dans la colonne libellé, il peut produire des résultats indésirables. C’est parce que la classification requiert une variable de réponse discrète qui permet uniquement de cette plage de valeurs sur un jeu de classes fini et généralement assez petit.
3. Si le score du groupe de données ne contient-elle pas toutes les fonctionnalités utilisées pour exercer le modèle, le [Modèle de Score] [ score-model] génère une erreur.
4. Le [Modèle de Score] [ score-model] ne donnent pas de résultat correspondant à une ligne dans le dataset de score qui contient une valeur manquante ou une valeur infinie pour une de ses fonctionnalités.
5. Le [Modèle de Score] [ score-model] peut produire des sorties identiques pour toutes les lignes dans le dataset de score. Cela peut se produire, par exemple, lors de la tentative de classification à l’aide de forêts de décision si le nombre minimal d’échantillons par nœud est choisi pour être plus que le nombre d’exemples de formation disponibles.


<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 
