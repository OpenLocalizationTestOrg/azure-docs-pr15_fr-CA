<properties
    pageTitle="Fonctionnalité de sélection dans le processus de Science de données équipe | Microsoft Azure" 
    description="Décrit la fonction de sélection de la fonctionnalité et fournit des exemples de leur rôle dans le processus d’amélioration de données d’apprentissage de l’ordinateur."
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
    ms.date="09/19/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Sélection des fonctionnalités dans le processus de Science du données Team (TDSP)

Cet article explique le cadre de sélection de la fonctionnalité et fournit des exemples de son rôle dans le processus d’amélioration de données d’apprentissage de l’ordinateur. Ces exemples sont tirés de Studio de formation de Machine Azure. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Cette rubrique décrit la fonction de sélection de la fonctionnalité et fournit des exemples de son rôle dans le processus d’amélioration de données d’apprentissage de l’ordinateur. Ces exemples sont tirés de Studio de formation de Machine Azure. 

L’ingénierie et la sélection des fonctionnalités est une partie de la TDSP décrite dans le [Quel est le processus de Science de données équipe ?](data-science-process-overview.md). Ingénierie de fonctionnalité et de sélection sont des parties de l’étape de **fonctionnalités de développement** de la TDSP.

* **ingénierie de fonctionnalité**: ce processus tente de créer des fonctionnalités supplémentaires utiles dans les fonctionnalités de brutes existantes dans les données et augmenter la puissance prédictive pour l’algorithme d’apprentissage.

* **sélection de fonctions**: ce processus sélectionne le sous-ensemble de clés d’origine de fonctionnalités de données lors d’une tentative de réduire la dimensionnalité du problème de la formation.

Normalement **ingénierie de fonctionnalité** est appliqué en premier pour générer des fonctionnalités supplémentaires, et que l’étape de **sélection de la fonction** est alors effectuée pour éliminer les fonctionnalités inutiles, redondantes ou fortement corrélées.


## <a name="filtering-features-from-your-data---feature-selection"></a>Fonctionnalités de filtrage à partir de vos données : sélection des fonctionnalités 

Sélection de fonctions est un processus qui est couramment appliqué pour la construction de jeux de données de formation pour les tâches de modélisation prédictive par exemple les tâches de classification ou de régression. L’objectif est de sélectionner un sous-ensemble des fonctionnalités du dataset d’origine permettant de réduire ses dimensions à l’aide d’un ensemble minimal de fonctionnalités pour représenter la quantité maximale de variation dans les données. Ensuite, ce sous-ensemble de fonctionnalités sont les seules fonctionnalités à inclure à l’apprentissage du modèle. Sélection des fonctionnalités sert deux objectifs principaux.

* Tout d’abord, sélection des fonctionnalités souvent augmente la précision de la classification en éliminant redondants et non pertinents, ou fortement corrélée des fonctionnalités.
* En second lieu, il réduit le nombre de fonctions qui optimise le processus de formation de modèle. Ceci est particulièrement important pour les stagiaires qui sont coûteuses à former comme des machines à vecteurs de support.

Bien que la sélection de la fonctionnalité vise à réduire le nombre de fonctionnalités dans le groupe de données utilisées pour exercer le modèle, il n'est pas généralement désigné par le terme « réduction de la dimensionnalité ». Méthodes de sélection des fonctions extraire un sous-ensemble de fonctionnalités d’origine dans les données sans les modifier.  Méthodes de réduction de dimensionnalité utilisent les fonctionnalités de rétroconception qui peuvent transformer les fonctionnalités d’origine et donc de les modifier. Analyse par composantes principales, l’analyse de corrélation canonique et décomposition en valeurs singulières constituent des exemples de méthodes de réduction de dimensionnalité.

Entre autres, une catégorie largement appliquée des méthodes de sélection de fonction dans un contexte contrôlé est appelée « sélection des fonctionnalités filtre ». En évaluant la corrélation entre chaque fonctionnalité et l’attribut cible, ces méthodes s’appliquent à une mesure statistique pour assigner un score à chaque fonctionnalité. Les fonctionnalités sont ensuite classées par le résultat, ce qui peut être utilisé pour aider à définir le seuil de conservation ou suppression d’une fonctionnalité spécifique. Corrélation, l’information réciproque et le test au carré Chi personne sont des exemples de mesures statistiques utilisés dans ces méthodes.

Dans Azure Machine Learning Studio, sont fournis pour la sélection de la fonctionnalité des modules. Comme illustré dans la figure suivante, ces modules sont [basée sur le filtre de sélection de fonctionnalité] [ filter-based-feature-selection] et [Analyse discriminante LINEAIRE de Fisher][fisher-linear-discriminant-analysis].

![Exemple de sélection de fonctionnalité](./media/machine-learning-data-science-select-features/feature-Selection.png)


Prenons, par exemple, l’utilisation de la [sélection des fonctionnalités basées sur le filtre] [ filter-based-feature-selection] module. Pour plus de commodité, nous continuons utiliser l’exemple de data mining de texte ci-dessus. Supposons que nous voulons créer un modèle de régression après la création d’un ensemble de 256 fonctionnalités par le biais de la [Fonction de hachage] [ feature-hashing] module, et que la variable de réponse est le « Col1 » représente un livre consulter les évaluations allant de 1 à 5. En définissant la « Fonctionnalité de méthode score » à « Corrélation de Pearson », la colonne « cible » à « Col1 » et le « nombre de fonctionnalités souhaitées » à 50. Puis le module de [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] produira un dataset contenant 50 fonctions avec l’attribut cible « Col1 ». La figure suivante illustre le déroulement de cette expérience et les paramètres d’entrée que nous venons de décrire.

![Exemple de sélection de fonctionnalité](./media/machine-learning-data-science-select-features/feature-Selection1.png)

La figure suivante montre les groupes de données qui en résulte. Chaque fonctionnalité est un score en fonction de la corrélation de Pearson entre lui-même et l’attribut cible « Col1 ». Les fonctionnalités de scores supérieur sont conservées.

![Exemple de sélection de fonctionnalité](./media/machine-learning-data-science-select-features/feature-Selection2.png)

Les scores correspondantes des composants sélectionnés sont affichés dans la figure suivante.

![Exemple de sélection de fonctionnalité](./media/machine-learning-data-science-select-features/feature-Selection3.png)

En appliquant cette [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] module, 50 de 256 fonctionnalités sont sélectionnées parce qu’ils ont les fonctionnalités plus en corrélation avec la variable cible « Col1 », basé sur la méthode de notation « Corrélation de Pearson ».

## <a name="conclusion"></a>Conclusion
Ingénierie de fonctionnalité et de sélection de fonctionnalité sont deux généralement conçue et fonctionnalités sélectionnées augmentent l’efficacité de la formation qui tente d’extraire les informations de clés contenues dans les données. Ils améliorent en outre la puissance de ces modèles pour classer les données d’entrée avec précision et prédire les résultats d’intérêt plus stricte. Ingénierie de fonctionnalité et de sélection peuvent également combiner pour rendre l’apprentissage plus de calculs souple. Il le fait en améliorant et en puis réduisant le nombre de fonctionnalités nécessaires pour étalonner ou de former un modèle. Du point de vue mathématique, les composants sélectionnés pour l’apprentissage du modèle sont un ensemble minimal de variables indépendantes qui expliquent les modèles de données et puis prédire les résultats avec succès.

Notez qu’il n’est pas toujours nécessairement effectuer d’ingénierie ou la fonction de sélection des fonctionnalités. S’il est nécessaire ou non dépend de nous ont ou collecter des données, l’algorithme que nous le prélèvement et l’objectif de l’expérimentation.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 
