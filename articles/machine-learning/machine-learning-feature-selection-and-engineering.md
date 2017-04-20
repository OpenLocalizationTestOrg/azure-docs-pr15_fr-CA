<properties
    pageTitle="Fonction d’ingénierie et la sélection dans l’apprentissage automatique de Azure | Microsoft Azure"
    description="Explique le cadre de sélection de fonctionnalité et de l’ingénierie de la fonctionnalité et fournit des exemples de leur rôle dans le processus d’amélioration des données d’apprentissage de l’ordinateur."
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
    ms.author="zhangya;bradsev" />


# <a name="feature-engineering-and-selection-in-azure-machine-learning"></a>Ingénierie de fonctionnalité et de sélection dans l’apprentissage automatique de Azure

Cette rubrique explique les besoins de l’ingénierie de la fonctionnalité et la sélection des fonctionnalités dans le processus d’amélioration des données d’apprentissage de l’ordinateur. Il montre ce qu’impliquent ces processus en utilisant les exemples fournis par Azure Machine Learning Studio.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Les données de formation utilisées dans l’apprentissage automatique peuvent souvent être améliorées par la sélection ou l’extraction de fonctionnalités à partir des données brutes collectées. Un exemple d’une fonction d’ingénierie dans le cadre de l’apprentissage de classer les images de caractères manuscrits est un mappage de densité construit à partir de données de distribution bits brutes. Cette carte peut aider à localiser les bords des caractères plus efficacement que la distribution du brute.

Les fonctionnalités de rétroconception et sélectionnées accroître l’efficacité de la formation, qui tente d’extraire les informations de clés contenues dans les données. Ils améliorent en outre la puissance de ces modèles pour classer les données d’entrée avec précision et prédire les résultats d’intérêt plus stricte. Ingénierie de fonctionnalité et de sélection peuvent également combiner pour rendre l’apprentissage plus de calculs souple. Il le fait en améliorant et en puis réduisant le nombre de fonctionnalités nécessaires pour étalonner ou de former un modèle. Du point de vue mathématique, les composants sélectionnés pour l’apprentissage du modèle sont un ensemble minimal de variables indépendantes qui expliquent les modèles de données et puis prédire les résultats avec succès.

L’ingénierie et la sélection des fonctionnalités est une partie d’un processus plus large, ce qui est généralement constitué de quatre étapes :

* Collecte de données
* Amélioration des données
* Construction du modèle
* Post-traitement

Ingénierie et sélection constituent l’étape d’amélioration de données d’apprentissage de l’ordinateur. Trois aspects de ce processus peuvent être distingués dans notre cas :

* **Prétraitement de données**: ce processus tente de s’assurer que les données collectées soient propre et cohérente. Il inclut des tâches telles que l’intégration de plusieurs ensembles de données, gestion des données manquantes, gestion des données incohérentes et conversion des types de données.
* **Fonction d’ingénierie**: ce processus tente de créer des fonctionnalités supplémentaires utiles dans les fonctionnalités de brutes existantes dans les données et pour augmenter la puissance prédictive pour l’algorithme d’apprentissage.
* **Sélection de fonctions**: ce processus sélectionne le sous-ensemble de clés d’origine de fonctionnalités de données pour réduire la dimensionnalité du problème de la formation.

Cette rubrique traite uniquement de l’ingénierie de la fonctionnalité et les aspects de sélection de fonctionnalité du processus d’amélioration de données. Pour plus d’informations sur l’étape de prétraitement de données, consultez [données dans Azure Machine Learning Studio de pré-traitement](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/).


## <a name="creating-features-from-your-data--feature-engineering"></a>Création de fonctions à partir de vos données : fonctionnalité ingénierie

Les données d’apprentissage se composent d’une matrice composée d’exemples (enregistrements ou observations stockées dans les lignes), dont chacun possède un ensemble de fonctionnalités (variables ou stockées dans des colonnes de champs). Les fonctionnalités spécifiées dans le protocole expérimental sont attendues pour caractériser les modèles dans les données. Bien que la plupart des champs de données brutes peuvent être directement incluses dans l’ensemble de la fonction sélectionnée utilisée pour la formation d’un modèle, les fonctionnalités de rétroconception supplémentaires doivent souvent être construit à partir de fonctionnalités dans les données brutes pour générer un jeu de données améliorées.

Quel type de fonctionnalités doit être créé pour améliorer l’ensemble de données lors de l’apprentissage d’un modèle ? Rétroconception des fonctionnalités qui améliorent la formation fournissent des informations qui différencie les mieux les modèles dans les données. Vous prévoyez que les nouvelles fonctionnalités pour fournir des informations supplémentaires qui ne sont pas clairement capturées ou facilement apparente dans l’ensemble des fonctionnalités existantes ou d’origine, mais ce processus est un art. Les décisions de son et de la productivité nécessitent souvent un domaine d’expertise.

Lors du démarrage avec formation de Machine d’Azure, il est plus facile à comprendre concrètement de ce processus à l’aide des exemples fournis dans le Studio de formation de Machine. Voici deux exemples :

* Un exemple de régression ([prévision du nombre de mensualités de bicyclette](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)) dans une expérience de mise sous contrôle dans lequel les valeurs cibles sont connus
* Un exemple de classification de texte à l’aide de la [Fonction de hachage][feature-hashing]

### <a name="example-1-adding-temporal-features-for-a-regression-model"></a>Exemple 1 : Ajout de fonctionnalités temporelles pour un modèle de régression ###

Pour montrer comment concevoir des fonctionnalités pour une tâche de régression, nous allons utiliser l’expérience « prévision de la demande de bicyclettes » dans Azure Machine Learning Studio. L’objectif de cette expérimentation est de prédire la demande pour les vélos, autrement dit, le nombre de mensualités de vélo dans un mois, jour et heure. Le jeu de données **UCI location de vélo de jeu de données** est utilisé en tant que données d’entrée brutes.

Cet ensemble de données est basé sur des données réelles à partir de la société Bikeshare de capitaux qui conserve un réseau de location de vélo à Washington DC, aux États-Unis. Le jeu de données représente le nombre de mensualités de vélo dans une heure spécifique de la journée, à partir de 2011 à 2012, et il contient 17379 lignes et 17 colonnes. Le jeu de fonctionnalités brutes contient les conditions météorologiques (température, humidité, vitesse du vent) et le type de la journée (vacances ou jour de la semaine). Le champ de prédire est **cnt**, un nombre qui représente les loyers de bicyclette en une heure spécifique et qui varie de 1 à 977.

Pour construire des fonctionnalités efficaces dans les données d’apprentissage, quatre modèles de régression sont créés à l’aide du même algorithme, mais avec quatre ensembles de données de formation différentes. Les quatre ensembles de données représentent les mêmes données d’entrée brutes, mais avec un nombre croissant de fonctionnalités. Ces fonctionnalités sont regroupées en quatre catégories :

1. A = météo + vacances de jour de la semaine + week-end de fonctionnalités pour le jour prévu
2. B = nombre de bicyclettes qui ont été louées dans chacun des 12 dernières heures
3. C = nombre de bicyclettes qui ont été louées dans chacun des 12 jours à la même heure précédentes
4. D = le nombre de bicyclettes qui ont été louées dans chacun des 12 semaines précédentes à la même heure et le jour même

En plus d’un jeu de fonctionnalité, qui existe déjà dans les données brutes d’origine, les autres trois ensembles de fonctionnalités sont créés par le biais de la fonctionnalité de processus d’ingénierie. Ensemble de fonctionnalités B capture la demande récente pour les vélos. Ensemble de fonctionnalités C captures à la demande de bicyclettes à une heure particulière. Ensemble de fonctionnalités à la demande de capture D pour vélos à une heure particulière et un jour particulier de la semaine. Chacun des quatre ensembles de données de formation inclut des ensembles de fonctionnalités A, A + B, A + B + C et A + B + C + D, respectivement.

Dans l’expérience d’apprentissage automatique de Azure, ces quatre ensembles de données de formation sont formés par quatre branches à partir de l’ensemble de données prétraitée d’entrée. À l’exception de la branche à l’extrême gauche, chacune de ces branches contient une [Exécution de Script de R] [ execute-r-script] dans lequel un jeu de dérivés (ensembles de fonctionnalités B, C et D) des fonctions de module est respectivement construit et ajouté au jeu de données importé. La figure suivante illustre le script R permet de créer un ensemble de fonctionnalités B dans la deuxième branche de gauche.

![Créer un ensemble de fonctionnalités](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

Le tableau suivant résume la comparaison des résultats de performances des quatre modèles. Les meilleurs résultats sont affichés par les fonctionnalités A + B + C. Notez que le taux d’erreur diminue lorsque les ensembles de fonctionnalités supplémentaires sont inclus dans les données d’apprentissage. Cela vérifie notre présomption que B et C, les groupes de fonctionnalités fournissent des informations complémentaires la tâche de régression. Ajout de l’ensemble des fonctionnalités D ne semble pas fournir toute réduction supplémentaire dans le taux d’erreur.

![Comparer les résultats de performances](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="example2"></a>Exemple 2 : Création de fonctions dans l’exploration de données de texte  

Ingénierie de fonctionnalité est largement appliquée dans les tâches liées à l’exploration de texte, telles que l’analyse de la classification et le sentiment de document. Par exemple, lorsque vous souhaitez classer des documents en plusieurs catégories, une hypothèse classique est que les mots ou expressions incluses dans une catégorie de document sont moins susceptibles de se produire dans une autre catégorie de document. En d’autres termes, la fréquence de la distribution du mot ou de phrase est caractériser les catégories de documents différents. Dans les applications d’exploration de données de texte, la fonction de processus d’ingénierie est nécessaire pour créer les fonctions impliquant des fréquences de mot ou une expression dans la mesure où les éléments individuels du contenu de texte servent généralement les données d’entrée.

Pour ce faire, une technique appelée *fonction de hachage* est appliquée afin de transformer efficacement les fonctionnalités de texte arbitraire en indices. Au lieu d’associer chaque fonctionnalité de texte (mots ou phrases) à un index particulier, cette méthode fonctionne en appliquant une fonction de hachage pour les fonctionnalités et à l’aide de leurs valeurs de hachage en tant qu’indices directement.

Dans la formation de Machine d’Azure, il est une [Fonction de hachage] [ feature-hashing] module qui crée ces fonctionnalités mot ou une phrase. La figure suivante montre un exemple d’utilisation de ce module. Le jeu de données d’entrée contient deux colonnes : l’indice de livre allant de 1 à 5 et la connaissance du contenu. L’objectif de cette [Fonction de hachage] [ feature-hashing] module est de récupérer de nouvelles fonctionnalités qui indiquent la fréquence de l’occurrence de correspondante de mots ou des expressions dans la révision du livre particulier. Pour utiliser ce module, vous devez effectuer les étapes suivantes :

1. Sélectionnez la colonne qui contient le texte d’entrée (**Col2** dans cet exemple).
2. La valeur *bitsize de hachage* 8, qui signifie 2 ^ 8 = 256 de fonctionnalités sont créées. Le mot ou la phrase dans le texte est ensuite hachée à 256 indices. Le paramètre *bitsize de hachage* comprise entre 1 et 31. Si le paramètre est défini sur un nombre plus important, les mots ou les expressions sont moins susceptibles d’être haché dans le même index.
3. Définissez le paramètre *de N-grammes* à 2. Il récupère la fréquence de l’occurrence d’unigrams (il s’agit d’une fonctionnalité pour tous les mots) et bigrams (il s’agit d’une fonctionnalité pour chaque paire de mots adjacents) du texte d’entrée. Le paramètre *N-grammes* comprise entre 0 et 10, qui indique le nombre maximal de mots séquentiels pour être inclus dans une fonctionnalité.  

![Module de hachage de fonctionnalité](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

La figure suivante montre l’aspect de ces nouvelles fonctionnalités.

![Exemple de hachage de fonctionnalité](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## <a name="filtering-features-from-your-data--feature-selection"></a>Fonctionnalités de filtrage de vos données : sélection des fonctionnalités  ##

*Sélection de fonctions* est un processus qui est couramment appliqué à la construction de jeux de données de formation pour les tâches de modélisation prédictive par exemple les tâches de classification ou de régression. L’objectif est de sélectionner un sous-ensemble des fonctionnalités de l’ensemble de données d’origine qui réduit ses dimensions à l’aide d’un ensemble minimal de fonctionnalités pour représenter la quantité maximale de variation dans les données. Ce sous-ensemble de fonctionnalités contient les seules fonctionnalités à inclure pour former le modèle. Sélection de fonctions a deux objectifs principaux :

* Sélection des fonctionnalités souvent augmente la précision de la classification en éliminant redondants et non pertinents, ou fortement corrélée des fonctionnalités.
* Sélection de fonctionnalité diminue le nombre de fonctions, ce qui rend le processus de formation de modèle plus efficace. Ceci est particulièrement important pour les stagiaires qui sont coûteuses à former comme des machines à vecteurs de support.

Bien que la sélection de fonctionnalité vise à réduire le nombre de fonctionnalités dans le jeu de données utilisé pour exercer le modèle, il n'est pas généralement désigné par le terme *réduction de dimensionnalité.* Méthodes de sélection des fonctions extraire un sous-ensemble de fonctionnalités d’origine dans les données sans les modifier.  Méthodes de réduction de dimensionnalité utilisent les fonctionnalités de rétroconception qui peuvent transformer les fonctionnalités d’origine et donc de les modifier. Analyse du composant principal, l’analyse de corrélation canonique et décomposition de valeur singulière constituent des exemples de méthodes de réduction de dimensionnalité.

Une catégorie de largement appliquée des méthodes de sélection de fonction dans un contexte contrôlé est la sélection de la fonction filtre. En évaluant la corrélation entre chaque fonctionnalité et l’attribut cible, ces méthodes s’appliquent à une mesure statistique pour assigner un score à chaque fonctionnalité. Les fonctionnalités sont ensuite classées par le résultat, ce qui vous permet de définir le seuil de conservation ou suppression d’une fonctionnalité spécifique. Corrélation de Pearson, l’information réciproque et le test du Khi-deux sont des exemples de mesures statistiques utilisés dans ces méthodes.

Studio d’apprentissage Machine Azure fournit des modules pour la sélection des fonctionnalités. Comme illustré dans la figure suivante, ces modules sont [basée sur le filtre de sélection de fonctionnalité] [ filter-based-feature-selection] et [Analyse discriminante LINEAIRE de Fisher][fisher-linear-discriminant-analysis].

![Exemple de sélection de fonctionnalité](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)


Par exemple, utiliser la [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] module avec l’exemple d’exploration de données de texte décrite précédemment. Supposons que vous souhaitez créer un modèle de régression après la création d’un ensemble de 256 fonctionnalités par le biais de la [Fonction de hachage] [ feature-hashing] module et que la variable de réponse est **Col1** et représente un livre revoir évaluation comprise entre 1 et 5. Définir la **méthode de notation de fonction** de **Corrélation d’échantillonnage de Pearson**, **Col1** **colonne cible** et le **nombre de fonctionnalités souhaitées** à **50**. Le module de [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] puis génère un jeu de données contenant 50 fonctions avec l’attribut cible **Col1**. La figure suivante illustre le déroulement de cette expérience et les paramètres d’entrée.

![Exemple de sélection de fonctionnalité](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

La figure suivante montre les jeux de données qui en résulte. Chaque fonctionnalité est un score en fonction de la corrélation de Pearson entre lui-même et l’attribut cible **Col1**. Les fonctionnalités de scores supérieur sont conservées.

![Jeux de données de fonctionnalité basée sur le filtre de sélection](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

La figure suivante montre les résultats correspondants des composants sélectionnés.

![Résultats de la fonction sélectionnée](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

En appliquant cette [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] module, les fonctionnalités sont sélectionnées, car ils ont des fonctionnalités très complètes en corrélation avec la cible variable **Col1** basé sur la méthode de calcul des points **De corrélation de Pearson**50 de 256.

## <a name="conclusion"></a>Conclusion
Ingénierie de fonctionnalité et de sélection de fonctionnalité sont deux étapes fréquemment exécutées pour préparer les données de formation lors de la création d’un modèle d’apprentissage de l’ordinateur. Normalement, ingénierie de fonctionnalité est appliqué en premier pour générer des fonctionnalités supplémentaires, et que l’étape de sélection de fonctionnalité est alors effectuée pour éliminer les fonctionnalités inutiles, redondantes ou fortement corrélées.

Il n’est pas toujours nécessairement effectuer d’ingénierie ou la fonction de sélection des fonctionnalités. Si elle est nécessaire dépend de l’objectif de l’expérience, les données que vous avez ou collectez et l’algorithme que vous sélectionnez.


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
