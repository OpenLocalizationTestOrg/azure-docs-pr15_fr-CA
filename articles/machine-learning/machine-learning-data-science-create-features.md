<properties
    pageTitle="Fonction d’ingénierie dans le processus Analytique Cortana | Microsoft Azure" 
    description="Explique les besoins de l’ingénierie de la fonctionnalité et fournit des exemples de son rôle dans le processus d’amélioration de données d’apprentissage de l’ordinateur."
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


# <a name="feature-engineering-in-the-cortana-analytics-process"></a>Ingénierie de fonctionnalité dans le processus Analytique de Cortana 

Cette rubrique explique les besoins de l’ingénierie de la fonctionnalité et fournit des exemples de son rôle dans le processus d’amélioration de données d’apprentissage de l’ordinateur. Les exemples pour illustrer ce processus sont tirés de Studio de formation de Machine Azure. 

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

Ce **menu** liens vers des rubriques qui décrivent comment créer des fonctions pour les données dans différents environnements. Cette tâche est une étape dans le [Processus de Science données Team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Fonctionnalité d’ingénierie essaie d’augmenter la puissance prédictive des algorithmes de formation en créant des fonctions à partir de données brutes qui permettent de faciliter le processus d’apprentissage. L’ingénierie et la sélection des fonctionnalités est une partie de la TDSP décrite dans le [Quel est le processus de Science de données équipe ?](data-science-process-overview.md) Ingénierie de fonctionnalité et de sélection sont des parties de l’étape de **fonctionnalités de développement** de la TDSP. 

* **ingénierie de fonctionnalité**: ce processus tente de créer des fonctionnalités supplémentaires utiles dans les fonctionnalités de brutes existantes dans les données et augmenter la puissance prédictive de l’algorithme d’apprentissage.

* **sélection de fonctions**: ce processus sélectionne le sous-ensemble de clés d’origine de fonctionnalités de données lors d’une tentative de réduire la dimensionnalité du problème de la formation.

Normalement **ingénierie de fonctionnalité** est appliqué en premier pour générer des fonctionnalités supplémentaires, et que l’étape de **sélection de la fonction** est alors effectuée pour éliminer les fonctionnalités inutiles, redondantes ou fortement corrélées.

Les données de formation utilisées dans l’apprentissage automatique peuvent souvent être améliorées par l’extraction de fonctionnalités à partir des données brutes collectées. Un exemple d’une fonction d’ingénierie dans le cadre de l’apprentissage de classer les images de caractères manuscrits est la création du mappage de densité un peu construit à partir de données de distribution bits brutes. Cette carte peut aider à localiser les bords des caractères plus efficacement qu’une simple utilisation directement la distribution brute.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="creating-features-from-your-data---feature-engineering"></a>Création de fonctions à partir de données - fonctionnalités ingénierie

Les données d’apprentissage se composent d’une matrice composée d’exemples (enregistrements ou observations stockées dans les lignes), dont chacun possède un ensemble de fonctionnalités (variables ou stockées dans des colonnes de champs). Les fonctionnalités spécifiées dans le protocole expérimental sont attendues pour caractériser les modèles dans les données. Bien que la plupart des champs de données brutes peuvent être directement incluses dans l’ensemble de la fonction sélectionnée utilisée pour la formation d’un modèle, il arrive souvent que des fonctionnalités supplémentaires (ingénieries) besoin d’être construite dans les fonctionnalités dans les données brutes pour générer un dataset de formation améliorées.

Quel type de fonctionnalités doit être créé pour améliorer le groupe de données lors de l’apprentissage d’un modèle ? Rétroconception des fonctionnalités qui améliorent la formation fournissent des informations qui différencie les mieux les modèles dans les données. Nous pensons que les nouvelles fonctionnalités pour fournir des informations supplémentaires qui n’est pas clairement capturées ou facilement apparente dans l’ensemble des fonctionnalités existantes ou d’origine. Mais ce processus est un art. Les décisions de son et de la productivité nécessitent souvent un domaine d’expertise.

Lors du démarrage avec formation de Machine d’Azure, il est plus facile à comprendre ce processus concrètement l’utilisation d’exemples fournis dans le Studio. Voici deux exemples :

* Un exemple de régression [prévision du nombre de mensualités de vélo](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) dans une expérience de mise sous contrôle dans lequel les valeurs cibles sont connus
* Un exemple de classification d’exploration de données texte à l’aide de la [Fonction de hachage](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

### <a name="example-1-adding-temporal-features-for-regression-model"></a>Exemple 1 : Ajout de fonctionnalités temporelles pour un modèle de régression ###

Nous allons utiliser l’expérience « prévision de la demande de bicyclettes » dans Azure Machine Learning Studio pour montrer la manière de concevoir des fonctionnalités pour une tâche de régression. L’objectif de cette expérimentation est de prédire la demande pour les vélos, autrement dit, le nombre de mensualités de vélo dans une mois/jour/heure spécifique. Le groupe de données « location de vélo UCI dataset » est utilisé en tant que données d’entrée brutes. Ce groupe de données est basé sur des données réelles à partir de la société Bikeshare de capitaux qui conserve un réseau de location de vélo à Washington DC, aux États-Unis. Le groupe de données représente le nombre de mensualités de vélo dans une heure spécifique de la journée au cours des années 2011 et année 2012 et contient 17379 lignes et 17 colonnes. Le jeu de fonctionnalités brutes contient des conditions météorologiques (température/humidité/vent) et le type de la journée (vacances/jours de la semaine). Le champ de prédire est « cnt », un nombre qui représente les loyers de bicyclette en une heure spécifique et qui est comprise entre 1 et 977 les plages.

Dans le but de construire des fonctions efficaces dans les données d’apprentissage, quatre régression modèles reposent sur le même algorithme, mais avec quatre ensembles de données de formation différentes. Les quatre groupes de données représentent les mêmes données d’entrée brutes, mais avec un nombre croissant de fonctionnalités définies. Ces fonctionnalités sont regroupées en quatre catégories :

1. A = météo + vacances de jour de la semaine + week-end de fonctionnalités pour le jour prévu
2. B = nombre de bicyclettes qui ont été louées dans chacun des 12 dernières heures
3. C = nombre de bicyclettes qui ont été louées dans chacun des 12 jours à la même heure précédentes
4. D = le nombre de bicyclettes qui ont été louées dans chacun des 12 semaines précédentes à la même heure et le jour même

En plus d’un jeu de fonctionnalité, qui existent déjà dans les données brutes d’origine, les autres trois ensembles de fonctionnalités sont créés par le biais de la fonctionnalité de processus d’ingénierie. Ensemble de fonctionnalités B captures à la demande très récente pour les vélos. Ensemble de fonctionnalités C captures à la demande de bicyclettes à une heure particulière. Ensemble de fonctionnalités à la demande de capture D pour vélos à une heure particulière et un jour particulier de la semaine. Formation de quatre jeux de données chaque inclut un ensemble de fonctionnalités, A + B, A + B + C et A + B + C + D, respectivement.

Dans l’expérience d’apprentissage automatique de Azure, ces quatre ensembles de données de formation sont formés par quatre branches du groupe de données prétraitées d’entrée. À l’exception de la gauche de la plupart des succursales, chacune de ces branches contient un module [d’exécuter le Script de R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) , dans lequel un ensemble de dérivés de fonctionnalités (fonction de la valeur B, C et D) sont respectivement construit et ajouté au groupe de données importée. La figure suivante illustre le script R permet de créer un ensemble de fonctionnalités B dans la deuxième branche de gauche.

![créer des fonctions](./media/machine-learning-data-science-create-features/addFeature-Rscripts.png)

La comparaison des résultats de performances des quatre modèles sont résumées dans le tableau suivant. Les meilleurs résultats sont affichés par les fonctionnalités A + B + C. Notez que le taux d’erreur diminue lorsque des fonctionnalités supplémentaires sont inclus dans les données d’apprentissage. Il vérifie notre présomption que l’ensemble des fonctionnalités B, C fournit des informations complémentaires la tâche de régression. Mais l’ajout de la fonctionnalité D ne semble pas fournir toute réduction supplémentaire dans le taux d’erreur.

![comparaison des résultats](./media/machine-learning-data-science-create-features/result1.png)

### <a name="example2"></a>Exemple 2 : Création de fonctions dans l’exploration de données de texte  

Ingénierie de fonctionnalité est largement appliquée dans les tâches liées à l’exploration de texte, telles que l’analyse de la classification et le sentiment de document. Par exemple, lorsque vous souhaitez classer des documents en plusieurs catégories, une hypothèse classique est que les mot/expressions incluses dans une catégorie de document sont moins susceptibles de se produire dans une autre catégorie de doc. Autrement dit, la fréquence de la distribution de mots ou d’expressions est caractériser les catégories de documents différents. Dans les applications d’exploration de données de texte, des éléments de contenu de texte servent généralement les données d’entrée, la fonction de processus d’ingénierie est nécessaire pour créer les fonctions impliquant des fréquences de mot/expression.

Pour ce faire, une technique appelée **fonction de hachage** est appliquée afin de transformer efficacement les fonctionnalités de texte arbitraire en indices. Au lieu d’associer chaque fonctionnalité de texte (mots ou d’expressions) à un index particulier, cette méthode fonctionne par application d’une fonction de hachage pour les fonctionnalités et directement à l’aide de leurs valeurs de hachage en tant qu’indices.

Dans apprentissage de Machine d’Azure, il existe un module de [Fonction de hachage](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) qui crée ces mot/expression fonctionnalités facilement. La figure suivante montre un exemple d’utilisation de ce module. Le groupe de données d’entrée contient deux colonnes : l’indice de livre allant de 1 à 5 et le contenu de révision proprement dite. L’objectif de ce module de [Fonction de hachage](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) est de récupérer beaucoup de nouvelles fonctionnalités qui affichent la fréquence de l’occurrence du ou les mots correspondant et de révision de phrases contenues dans le livre particulier. Pour utiliser ce module, nous devons suivez les étapes ci-dessous :

* Tout d’abord, sélectionnez la colonne qui contient le texte d’entrée (« Col2 » dans cet exemple).
* Ensuite, définissez le « bitsize de hachage » à 8, qui signifie 2 ^ 8 = 256 fonctionnalités seront créées. La phase/word dans tout le texte sera hachée à 256 indices. Le paramètre « Bitsize de hachage » varie de 1 à 31. L’ou les mots ou les phrases sont moins susceptibles d’être haché dans le même index si qu’il s’agit d’un plus grand nombre.
* Enfin, affectez au paramètre « N-grammes » 2. Cet exemple obtient la fréquence d’occurrence d’unigrams (il s’agit d’une fonctionnalité pour tous les mots) et bigrams (il s’agit d’une fonctionnalité pour chaque paire de mots adjacents) du texte d’entrée. Le paramètre « N-grammes » comprise entre 0 et 10, qui indique le nombre maximal de mots séquentiels pour être inclus dans une fonctionnalité.  

![Module de « Fonction de hachage »](./media/machine-learning-data-science-create-features/feature-Hashing1.png)

La figure suivante illustre ce que ces fonctionnalités Rechercher comme.

![Exemple de « Fonction de hachage »](./media/machine-learning-data-science-create-features/feature-Hashing2.png)


## <a name="conclusion"></a>Conclusion

Les fonctionnalités de rétroconception et sélectionnées accroître l’efficacité de la formation qui tente d’extraire les informations de clés contenues dans les données. Ils améliorent en outre la puissance de ces modèles pour classer les données d’entrée avec précision et prédire les résultats d’intérêt plus stricte. Ingénierie de fonctionnalité et de sélection peuvent également combiner pour rendre l’apprentissage plus de calculs souple. Il le fait en améliorant et en puis réduisant le nombre de fonctionnalités nécessaires pour étalonner ou de former un modèle. Du point de vue mathématique, les composants sélectionnés pour l’apprentissage du modèle sont un ensemble minimal de variables indépendantes qui expliquent les modèles de données et puis prédire les résultats avec succès.

Notez qu’il n’est pas toujours nécessairement effectuer d’ingénierie ou la fonction de sélection des fonctionnalités. S’il est nécessaire ou non dépend de nous ont ou collecter des données, l’algorithme que nous le prélèvement et l’objectif de l’expérimentation.
 
