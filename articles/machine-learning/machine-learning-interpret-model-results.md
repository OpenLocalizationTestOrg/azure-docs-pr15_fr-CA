<properties
    pageTitle="Interpréter les résultats du modèle dans l’apprentissage automatique | Microsoft Azure"
    description="Comment choisir le paramètre optimal pour un algorithme de l’aide et la visualisation des sorties de modèle de score."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="bradsev" />


# <a name="interpret-model-results-in-azure-machine-learning"></a>Interpréter les résultats du modèle dans l’apprentissage automatique de Azure

Cette rubrique explique comment visualiser et interpréter les résultats des prévisions dans le Studio de formation de Machine Azure. Après avoir formé un modèle et faire des prévisions dessus (« le modèle de score »), vous devez comprendre et interpréter le résultat de la prédiction.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Il existe quatre types principaux d’apprentissage des modèles dans Azure Machine d’apprentissage de l’ordinateur :

* Classification
* Gestion de clusters
* Régression
* Systèmes de conseiller

Les modules utilisés pour la prédiction en plus de ces modèles sont :

* [Modèle de score] [ score-model] module de classification et de régression
* [Affecter à des Clusters] [ assign-to-clusters] module de gestion de clusters
* [Score de conseils sur les allumettes] [ score-matchbox-recommender] pour les systèmes de recommandation

Ce document explique comment interpréter les résultats des prévisions pour chacun de ces modules. Pour une vue d’ensemble de ces modules, voir [comment choisir les paramètres pour optimiser vos algorithmes dans l’apprentissage automatique de Azure](machine-learning-algorithm-parameters-optimize.md).

Cette rubrique concerne l’interprétation de la prévision, mais pas l’évaluation modèle. Pour plus d’informations sur l’évaluation de votre modèle, reportez-vous à [l’évaluation des performances du modèle dans l’apprentissage automatique de Azure](machine-learning-evaluate-model-performance.md).

Si vous sont à nouveau à formation de Machine Azure et avez besoin d’aide pour créer une expérience simple pour commencer, consultez [créer une expérience simple dans Azure Machine Learning Studio](machine-learning-create-experiment.md) dans Azure Machine Learning Studio.

## <a name="classification"></a>Classification ##
Il existe deux sous-catégories des problèmes de classification :

* Problèmes avec les deux seules classes (classification deux classes ou binaire)
* Problèmes avec plus de deux classes (classification multiples)

Formation de Machine Azure a différents modules de traiter chacun de ces types de classification, mais les méthodes pour l’interprétation de leurs résultats de prévision sont similaires.

### <a name="two-class-classification"></a>Classification de la classe 2###
**Expérience de l’exemple**

Un exemple d’un problème de classification de deux-classe correspond à la classification de fleurs d’iris. La tâche consiste à classer des fleurs iris en fonction de leurs caractéristiques. Le jeu de données Iris fourni dans l’apprentissage automatique de Azure est un sous-ensemble de l' [ensemble de données Iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) contenant les occurrences d’uniquement deux espèces de fleur (classes 0 et 1). Il existe quatre fonctionnalités pour chaque fleur (longueur des sépales, largeur des sépales, longueur des pétales et la largeur des pétales).

![Capture d’écran de l’expérience de l’iris](./media/machine-learning-interpret-model-results/1.png)

La figure 1. Expérience de problème de classification de deux-classe IRIS

Une expérimentation a été effectuée pour résoudre ce problème, comme illustré dans la Figure 1. Un modèle d’arborescence de décision amplifiée de deux-classe a été formé et noté. Maintenant vous pouvez visualiser les résultats de la prévision à partir du [Modèle de Score] [ score-model] module en cliquant sur le port de sortie du [Modèle de Score] [ score-model] module et puis en cliquant sur **visualiser**.

![Module de modèle de score](./media/machine-learning-interpret-model-results/1_1.png)

Ceci affiche les résultats du score comme illustré à la Figure 2.

![Résultats d’essai de deux-classe classification iris](./media/machine-learning-interpret-model-results/2.png)

La figure 2. Visualiser un résultat de modèle de score dans la classification de la classe de deux

**Interprétation des résultats**

Il existe six colonnes de la table de résultats. Les quatre colonnes à gauche sont les quatre fonctionnalités. Les colonnes vers la droite à deux, un score des étiquettes et des probabilités d’un score, sont les résultats de la prévision. La colonne de score de probabilités indique la probabilité qu’une fleur appartient à la classe positive (classe 1). Par exemple, le premier nombre dans les moyens de la colonne (0.028571) il est 0.028571 de probabilité que le premier fleur appartient à la classe 1. La colonne d’étiquettes d’un score indique la classe prévue pour chaque fleur. Ceci est basé sur la colonne de score des probabilités. Si la probabilité évaluée d’une fleur est supérieure à 0,5, il est prévu en tant que classe 1. Dans le cas contraire, il est prévu en tant que classe 0.

**Publication du service Web**

Une fois les résultats de prévision ont été compris et jugées audio, l’expérimentation peut être publiée sous la forme d’un service web afin que vous pouvez déployer dans diverses applications et l’appeler pour obtenir les prévisions de classe sur n’importe quel nouveau fleur d’iris. Pour savoir comment modifier une expérience de formation dans une expérience de score et le publier sous la forme d’un service web, consultez [Publier le service web de formation de Machine Azure](machine-learning-walkthrough-5-publish-web-service.md). Cette procédure vous offre une expérience d’évaluation comme illustré à la Figure 3.

![Capture d’écran de l’expérimentation de score](./media/machine-learning-interpret-model-results/3.png)

La figure 3. Score de l’expérimentation de problème de classification de deux-classe iris

Vous devez maintenant définir l’entrée et la sortie du service web. L’entrée est le port d’entrée droit du [Modèle de Score][score-model], qui est la fleur Iris de fonctionnalités d’entrée. Le choix de la sortie dépend de si vous êtes intéressé par la classe prévue (étiquette évaluée), la probabilité évaluée ou les deux. Dans cet exemple, il est supposé que vous êtes intéressé par les deux. Pour sélectionner les colonnes de sortie de votre choix, utilisez une [Sélection des colonnes dans le jeu de données] [ select-columns] module. Cliquez sur [Sélectionner les colonnes dans le jeu de données][select-columns]et cliquez sur **lancer le sélecteur de colonne**, sélectionnez les **Étiquettes d’un score** et **Un score de probabilités**. Après avoir défini le port de sortie de [Sélectionner les colonnes dans le jeu de données] [ select-columns] et exécuter à nouveau, vous devez être prêt à publier l’expérience d’évaluation sous la forme d’un service web en cliquant sur **Publier le SERVICE WEB**. L’essai final ressemble à la Figure 4.

![L’expérimentation de la classification de la classe de deux iris](./media/machine-learning-interpret-model-results/4.png)

La figure 4. Expérience de score final d’un problème de classification de deux-classe iris

Après avoir exécuté le service web et que vous entrez des valeurs de fonction d’une instance de test, le résultat renvoie deux nombres. Le premier nombre correspond à l’étiquette évaluée, et la seconde est la probabilité évaluée. Cette fleur est prévue en tant que classe 1, avec une probabilité de 0.9655.

![Test d’interprétation modèle de score](./media/machine-learning-interpret-model-results/4_1.png)

![Évaluation des résultats des tests](./media/machine-learning-interpret-model-results/5.png)

La figure 5. Résultats du service Web de classification de deux classes de diaphragme

### <a name="multi-class-classification"></a>Classification de multiples
**Expérience de l’exemple**

Dans cette expérience, vous effectuez une reconnaissance lettre sous la forme d’un exemple de classification multiclass. Le classifieur tente de prédire une certaine lettre (classe), basée sur des valeurs d’attribut de manuscrite extraites à partir des images écrites à la main.

![Exemple de reconnaissance de lettre](./media/machine-learning-interpret-model-results/5_1.png)

Dans les données d’apprentissage, il y a des 16 fonctionnalités extraites à partir d’images de manuscrits de lettre. Les 26 lettres de formulaire nos 26 classes. La figure 6 montre une expérience qui va former un modèle de classification multiclass pour la reconnaissance de lettre et de prévoir la même fonctionnalité définie sur un ensemble de données de test.

![Expérience de classification multiclass de reconnaissance de lettre](./media/machine-learning-interpret-model-results/6.png)

La figure 6. Expérience de problème de lettre reconnaissance classification multiclass

Visualiser les résultats à partir du [Modèle de Score] [ score-model] module en cliquant sur le port de sortie du [Modèle de Score] [ score-model] module, puis en cliquant sur **visualiser**, vous devez voir le contenu, comme illustré à la Figure 7.

![Résultats de modèle de score](./media/machine-learning-interpret-model-results/7.png)

La figure 7. Visualiser les résultats de modèle de score dans une classification multiples

**Interprétation des résultats**

Les 16 colonnes de gauche représentent les valeurs de fonction de l’ensemble des tests. Les colonnes avec des noms comme un score de probabilités de classe « XX » sont simplement comme la colonne de score de probabilités dans le cas de deux classes. Ils montrent la probabilité que l’entrée correspondante est compris dans une certaine catégorie. Par exemple, à la première entrée, il est 0.003571 de probabilité qu’il s’agit d’un « A », 0.000451 probabilité qu’il s’agit d’un « B » et ainsi de suite. La dernière colonne (obtenu étiquettes) est identique à celui obtenu des étiquettes dans le cas de deux classes. Il sélectionne la classe avec la plus grande probabilité évaluée comme la classe prévue de l’entrée correspondante. Par exemple, pour la première entrée, l’évaluées est « F » dans la mesure où elle est la plus grande probabilité soit un « F » (0.916995).

**Publication du service Web**

Vous pouvez également obtenir l’étiquette évaluée pour chaque écriture et la probabilité de l’étiquette évaluée. La logique de base est de trouver la plus grande probabilité parmi tous les probabilités évaluées. Pour ce faire, vous devez utiliser le [Script de R exécuter] [ execute-r-script] module. Le code R est illustré à la Figure 8, et le résultat de l’essai est illustré à la Figure 9.

![Exemple de code R](./media/machine-learning-interpret-model-results/8.png)

La figure 8. Code R d’extraction obtenu les étiquettes et les probabilités associées des étiquettes

![Résultat de l’essai](./media/machine-learning-interpret-model-results/9.png)

La figure 9. Expérience de score final du problème de classification multiclass de reconnaissance de lettre

Après avoir publier et exécuter le service web et entrez des valeurs de fonction d’entrée, la résultat retourné ressemble à la Figure 10. Cette lettre manuscrite, avec ses fonctionnalités de 16 extraites, est prévue pour être un « T » avec une probabilité de 0.9715.

![Module de score interprétation de test](./media/machine-learning-interpret-model-results/9_1.png)

![Résultat de test](./media/machine-learning-interpret-model-results/10.png)

La figure 10. Résultats du service Web de classification multiclass

## <a name="regression"></a>Régression

Problèmes de régression sont différentes des problèmes de classification. Un problème de classification, que vous essayez de prévoir des classes discrètes, telles que les classe une fleur iris appartient à. Mais, comme vous pouvez le voir dans l’exemple suivant d’un problème de régression, que vous essayez de prévoir une variable continue, comme le prix d’une voiture.

**Expérience de l’exemple**

Utiliser la prévision de prix automobile votre exemple de régression. Vous essayez de prévoir le prix d’une voiture en fonction de ses fonctionnalités, y compris la marque, le type de carburant, type de corps et roue motrice. L’expérience est illustrée à la Figure 11.

![Expérience de régression prix automobile](./media/machine-learning-interpret-model-results/11.png)

La figure 11. Expérience de problème de régression prix automobile

Visualiser le [Modèle de Score] [ score-model] module, le résultat ressemble à la Figure 12.

![Évaluation des résultats de problème de prévision de prix automobile](./media/machine-learning-interpret-model-results/12.png)

La figure 12. Résultat de score pour le problème de la prévision des prix automobile

**Interprétation des résultats**

Étiquettes évaluées est la colonne de résultat dans le résultat de cette évaluation. Les nombres sont le prix prévisible pour chaque véhicule.

**Publication du service Web**

Vous pouvez publier l’expérience de régression dans un service web et appeler de la même manière, comme dans le cas d’utilisation de deux-classe classification pour la prédiction de prix automobile.

![Expérience de score pour le problème de régression prix automobile](./media/machine-learning-interpret-model-results/13.png)

La figure 13. Score de l’expérience d’un problème de régression prix automobile

Exécution du service web, le résultat retourné ressemble à la Figure 14. Le prix prévisible pour ce véhicule est de $15,085.52.

![Module de notation interprétation de test](./media/machine-learning-interpret-model-results/13_1.png)

![Évaluation des résultats du module](./media/machine-learning-interpret-model-results/14.png)

La figure 14. Résultats du service Web d’un problème de régression prix automobile

## <a name="clustering"></a>Gestion de clusters

**Expérience de l’exemple**

Nous allons utiliser le jeu de données d’Iris à nouveau pour créer une expérience de gestion de clusters. Ici, vous pouvez filtrer les étiquettes de classe dans le jeu de données afin qu’elle dispose de fonctionnalités et peut être utilisé pour la gestion de clusters d’uniquement. Cas d’utilisation dans cette iris, spécifiez le nombre de clusters à deux pendant le processus de formation, ce qui signifie que vous aurait regrouper les fleurs en deux classes. L’expérience est illustrée à la Figure 15.

![Problème de mise en cluster de Iris essayer](./media/machine-learning-interpret-model-results/15.png)

La figure 15. Problème de mise en cluster de Iris essayer

Mise en cluster diffère de la classification dans la mesure où le jeu de données n’a pas les étiquettes de sol-vérité par lui-même. Groupes de clusters les instances du jeu de données de formation en clusters distincts. Pendant le processus de formation, le modèle d’étiquettes les entrées par apprendre les différences entre leurs fonctions. Après cela, le modèle formé peut servir à classer les entrées futures. Il existe deux parties du résultat que nous intéresse au sein d’un problème de gestion de clusters. La première partie est libellé le jeu de données et la seconde est classer un nouveau jeu de données avec le modèle formé.

La première partie du résultat peut être visualisée en cliquant sur le port de sortie gauche du [Modèle de Clustering de Train] de[ train-clustering-model] , puis cliquez sur **visualiser**. La visualisation est illustrée dans la Figure 16.

![Résultat de la mise en cluster](./media/machine-learning-interpret-model-results/16.png)

La figure 16. Visualiser le résultat pour le jeu de données de gestion de clusters

Le résultat de la deuxième partie, nouvelles entrées avec le modèle de cluster formé, le clustering est illustré à la Figure 17.

![Visualiser les résultats de clustering](./media/machine-learning-interpret-model-results/17.png)

La figure 17. Visualiser la mise en cluster des résultats sur une nouvelle série de données

**Interprétation des résultats**

Bien que les résultats des deux parties proviennent d’expérience différents stades, ils ont le même aspect et sont interprétées de la même manière. Les quatre premières colonnes sont des fonctionnalités. La dernière colonne, les affectations, est le résultat de la prévision. Les entrées qui reçoivent le même numéro sont prévues pour être dans le même cluster, c'est-à-dire, qu’ils partagent des points communs d’une certaine façon (cette expérience utilise la mesure de la distance EUCLIDIENNE par défaut). Étant donné que vous avez spécifié le nombre de clusters à 2, les entrées dans les affectations sont étiquetées 0 ou 1.

**Publication du service Web**

Vous pouvez publier l’expérience de mise en cluster dans un service web et appeler pour la mise en cluster des prévisions de la même manière que dans la classification de la classe de deux cas d’utilisation.

![Expérience de score pour le problème de gestion de clusters de diaphragme](./media/machine-learning-interpret-model-results/18.png)

La figure 18. Score de l’expérience d’un problème de gestion de clusters de diaphragme

Après avoir exécuté le service web, le résultat retourné ressemble à la Figure 19. Cette fleur est prévue en cluster 0.

![Test interpréter le module d’évaluation](./media/machine-learning-interpret-model-results/18_1.png)

![Évaluation des résultats du module](./media/machine-learning-interpret-model-results/19.png)

La figure 19. Résultats du service Web de classification de deux classes de diaphragme

## <a name="recommender-system"></a>Système de recommandation
**Expérience de l’exemple**

Pour les systèmes de conseiller, vous pouvez utiliser le problème de la recommandation restaurant comme un exemple : vous pouvez recommander des restaurants pour les clients en fonction de leur historique de contrôle d’accès. Les données d’entrée se composent de trois parties :

* Évaluations de restaurant à partir de clients
* Données de composant de client
* Données de fonctionnalité de restaurant

Il existe plusieurs choses que nous pouvons faire avec le [Conseiller d’allumettes Train] [ train-matchbox-recommender] module dans la formation de Machine Azure :

- Prévoir des contrôles d’accès pour un utilisateur donné et un élément
- Recommander des éléments à un utilisateur donné
- Rechercher des utilisateurs associés à un utilisateur donné
- Rechercher des éléments liés à un élément donné

Vous pouvez choisir ce que vous voulez faire en sélectionnant parmi les quatre options dans le menu **type de prédiction de conseiller** . Ici, vous pouvez étudier les quatre scénarios.

![Conseiller d’allumettes](./media/machine-learning-interpret-model-results/19_1.png)

Une expérience d’apprentissage automatique de Azure typique pour un système de recommandation ressemble à la Figure 20. Pour plus d’informations sur l’utilisation de ces modules de système de garant, voir [conseils sur les allumettes Train] [ train-matchbox-recommender] et des [conseils sur les allumettes Score][score-matchbox-recommender].

![Expérience de système de garant](./media/machine-learning-interpret-model-results/20.png)

La figure 20. Expérience de système de garant

**Interprétation des résultats**

**Prévoir des contrôles d’accès pour un utilisateur donné et un élément**

En **Prévision d’évaluation** sous **type de prédiction de conseils sur**la sélection, vous demandez au système de garant prévoir le contrôle d’accès pour un utilisateur donné et un élément. La visualisation du [Score allumettes garant] de la[ score-matchbox-recommender] sortie ressemble à la Figure 21.

![Score de résultat du système garant--évaluation de prévision](./media/machine-learning-interpret-model-results/21.png)

La figure 21. Visualiser le résultat de score du système garant--évaluation de prévision

Les deux premières colonnes sont les paires élément-utilisateur fournies par les données d’entrée. La troisième colonne est l’évaluation prévue d’un utilisateur pour un article particulier. Par exemple, dans la première ligne, client U1048 est prévue pour le restaurant taux 135026 2.

**Recommander des éléments à un utilisateur donné**

En sélectionnant l' **Élément recommandation** sous **type de prévision garant**, vous demandez le système de recommandation de recommander des éléments à un utilisateur donné. Le dernier paramètre pour choisir dans ce scénario est la *sélection d’élément recommandé*. L’option **de nominale (pour l’évaluation du modèle)** est principalement pour l’évaluation de modèle pendant le processus de formation. Pour cette étape de prévision, nous choisir **à partir de tous les éléments**. La visualisation du [Score allumettes garant] de la[ score-matchbox-recommender] sortie ressemble à la Figure 22.

![Résultat de score de système de recommandation : recommandation de l’élément](./media/machine-learning-interpret-model-results/22.png)

La figure 22. Visualiser les résultats du score du système garant : recommandation de l’élément

La première des six colonnes représente l’utilisateur ID de recommander des éléments, tel que fourni par les données d’entrée. Les cinq autres colonnes représentent les éléments recommandés à l’utilisateur dans l’ordre décroissant de pertinence. Par exemple, dans la première ligne, le restaurant la plus recommandé pour le client U1048 est 134986, suivi de 135018, 134975, 135021 et 132862.

**Rechercher des utilisateurs associés à un utilisateur donné**

En sélectionnant **Utilisateurs connexes** sous **type de prévision garant**, vous demandez au système de garant de rechercher des utilisateurs associés à un utilisateur donné. Utilisateurs connexes sont les utilisateurs qui ont des préférences similaires. Le dernier paramètre pour choisir dans ce scénario est la *sélection de l’utilisateur*. L’option **à partir d’utilisateurs que nominale (pour l’évaluation du modèle)** est principalement pour l’évaluation de modèle pendant le processus de formation. Choisissez **à partir de tous les utilisateurs** pour cette étape de prévision. La visualisation du [Score allumettes garant] de la[ score-matchbox-recommender] sortie ressemble à la Figure 23.

![Résultat de score de système de recommandation--utilisateurs connexes](./media/machine-learning-interpret-model-results/23.png)

Figure 23. Visualiser les résultats du score du système garant--utilisateurs connexes

La première des six colonnes indique l’utilisateur Qu'id est nécessaire pour trouver des utilisateurs connexes, tel que fourni par les données d’entrée. Les cinq autres colonnes stockent les utilisateurs prévus connexes de l’utilisateur dans l’ordre de pertinence décroissant. Par exemple, dans la première ligne, le client la plus approprié pour client U1048 est U1051, suivi de U1066, U1044, U1017 et U1072.

**Rechercher des éléments liés à un élément donné**

En sélectionnant **Les éléments associés** , sous **type de prédiction de conseiller**, vous demandez au système de garant rechercher des éléments associés à un élément donné. Les éléments associés sont les éléments susceptibles d’être apprécié par le même utilisateur. Le dernier paramètre pour choisir dans ce scénario est la *sélection de l’élément associé*. L’option **de nominale (pour l’évaluation du modèle)** est principalement pour l’évaluation de modèle pendant le processus de formation. Nous choisir **à partir de tous les éléments** pour cette étape de prévision. La visualisation du [Score allumettes garant] de la[ score-matchbox-recommender] sortie ressemble à la Figure 24.

![Résultat de score de système de recommandation--les éléments associés](./media/machine-learning-interpret-model-results/24.png)

Figure 24. Visualiser les résultats du score du système garant--les éléments associés

La première des six colonnes représente les ID nécessaires pour rechercher des éléments associés, tel que fourni par les données d’entrée d’élément donné. Les cinq autres colonnes stockent les éléments connexes prévues de l’article dans l’ordre, en termes de pertinence décroissant. Par exemple, dans la première ligne, l’élément plus pertinent pour l’élément 135026 est 135074, suivi de 135035, 132875, 135055 et 134992.

**Publication du service Web**

Le processus de publication de ces expériences en tant que services web afin d’obtenir des prévisions est similaire pour chacun des quatre scénarios. Ici, nous prenons le second scénario (éléments de recommander à un utilisateur donné) à titre d’exemple. Vous pouvez suivre la même procédure pour les trois autres.

L’enregistrement du système de garant formé comme un modèle formé et filtrage des données d’entrée à une colonne d’ID utilisateur unique comme demandé, vous pouvez raccorder l’expérience comme dans la Figure 25 et publier sous la forme d’un service web.

![Expérience du problème de recommandation de restaurant de score](./media/machine-learning-interpret-model-results/25.png)

La figure 25. Expérience du problème de recommandation de restaurant de score

Exécution du service web, le résultat retourné ressemble à la Figure 26. Les cinq restaurants recommandés pour utilisateur U1048 sont 134986, 135018, 134975, 135021 et 132862.

![Exemple de service de système de garant](./media/machine-learning-interpret-model-results/25_1.png)

![Exemple de résultats expérience](./media/machine-learning-interpret-model-results/26.png)

La figure 26. Résultats du service Web de problème de recommandation de restaurant


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
