<properties
    pageTitle="Apprentissage de l’ordinateur pour préparer des données pour l’amélioration des tâches | Microsoft Azure"
    description="Données de prétraitement et propres afin de le préparer pour l’apprentissage de l’ordinateur."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Apprentissage de l’ordinateur pour préparer des données pour l’amélioration des tâches

De pré-traitement et de nettoyage des données sont des tâches importantes qui en général doivent être effectuées avant que le groupe de données peut être utilisé efficacement pour l’apprentissage de l’ordinateur. Données brutes souvent bruyants et peu fiables et peuvent être des valeurs manquantes. Pour la modélisation à l’aide de ces données peut produire des résultats trompeurs. Ces tâches font partie de la procédure de Science données (TDSP) équipe et suivent généralement une exploration initiale d’un dataset permet de découvrir et de planifier le traitement préalable requis. Pour obtenir des instructions plus détaillées sur le processus TDSP, consultez les étapes décrites dans le [Processus d’équipe données scientifiques](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Traitement préalable et le nettoyage des tâches, telles que la tâche d’exploration de données, peuvent être exécutées dans une grande variété d’environnements, tels que SQL ou d’une ruche ou d’Azure Machine Learning Studio et avec divers outils et langages, tels que R ou Python, selon l’endroit où vos données sont stockées et la mise en forme. Puisque TDSP est répétitive par nature, ces tâches possibles à diverses étapes dans le workflow du processus.

Cet article présente les différents concepts de traitement de données et les tâches qui peuvent être entreprises avant ou après l’ingestion des données dans la formation de Machine Azure.

Pour obtenir un exemple d’exploration de données et de traitement préalable fait à l’intérieur du studio de formation de Machine d’Azure, consultez la vidéo [de pré-traitement des données dans un Studio de formation de Machine Azure](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .


## <a name="why-pre-process-and-clean-data"></a>Pourquoi pré-traiter et nettoyer les données ?

Données du monde réel sont rassemblées à partir de différentes sources, et processus et il peuvent contenir des irrégularités ou des données corrompues transiger sur la qualité du groupe de données. Les problèmes de qualité de données standard qui surviennent sont les suivantes :

* **Incomplet**: données ne dispose pas d’attributs ou qui contiennent des valeurs manquantes.
* **Noisy**: les données contiennent des enregistrements erronés ou aberrantes.
* **Incohérente**: données contiennent des enregistrements en conflit ou des différences.

Données de qualité sont une condition préalable pour les modèles de prévision de qualité. Pour éviter des « garbage in, garbage out » et améliorer la qualité des données et par conséquent les performances de modèle, il est impératif d’effectuer un écran de santé données pour détecter les problèmes de données très tôt et décider sur le traitement de données correspondante et des étapes de nettoyage.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quels sont certains écrans de santé données standard qui sont employés ?

Nous pouvons vérifier la qualité générale des données en vérifiant :

* Le nombre **d’enregistrements**.
* Le nombre **d’attributs** (ou **fonctionnalités**).
* Les attributs **des types de données** (nominal, ordinale ou en continu).
* Le nombre de **valeurs manquantes**.
* **Conformité de la construction** des données.
    * Si les données sont TSV ou CSV, vérifiez que les séparateurs de colonne et les séparateurs de ligne toujours correctement séparent les colonnes et lignes.
    * Si les données sont au format HTML ou XML, vérifiez si les données sont bien formées en fonction de leurs normes respectives.
    * L’analyse peut également être nécessaire afin d’extraire des informations structurées à partir de données structurées ou semi-structurées.
* **Les enregistrements des données incohérentes**. Vérifier la plage de valeurs sont autorisés. par exemple, si les données contiennent le stagiaire amp, vérifiez si l’amp est dans la plage déterminée, par exemple 0 ~ 4.

Lorsque vous trouvez des problèmes liés aux données, **étapes de traitement** sont nécessaires qui implique souvent de nettoyage des valeurs manquantes, normalisation des données, discrétisation, traitement pour supprimer ou remplacer des caractères incorporés qui peuvent affecter l’alignement des données de texte, les types de données mixtes en commun et autres champs.

**Formation de Machine azure consomme bien formé des données tabulaires**.  Si les données sont déjà sous forme de tableau, le prétraitement de données peut être effectuée directement avec formation de Machine Azure dans le Studio de formation de Machine.  Si les données ne sont pas sous forme de tableau, par exemple il est au format XML, l’analyse peut être nécessaire pour convertir les données sous forme de tableau.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quelles sont les principales tâches de prétraitement de données ?

* **Nettoyage des données**: renseignez ou valeurs manquantes, détecter et supprimer les données bruyantes et des valeurs extrêmes.
* **Transformation des données**: normaliser les données pour réduire le bruit et les dimensions.
* **Réduction des données**: exemples d’enregistrements de données ou les attributs pour la gestion de données plus facilement.
* **Discrétisation des données**: convertir les attributs continus en attributs par catégorie pour une facilité d’utilisation avec certaines méthodes d’apprentissage machine.
* **Nettoyage du texte**: supprimer des caractères, qui peuvent entraîner un mauvais alignement des données, pour, par exemple, les onglets incorporés dans un fichier séparé par des tabulations, embedded nouvelles lignes qui peuvent rompre des enregistrements, etc..

Les sections suivantes détaillent certaines de ces étapes de traitement des données.

## <a name="how-to-deal-with-missing-values"></a>Comment traiter les valeurs manquantes ?

Pour gérer les valeurs manquantes, il est préférable de tout d’abord identifier la raison pour laquelle les valeurs manquantes à mieux gérer le problème. Type manquant des méthodes de gestion de valeur sont les suivantes :

* **Suppression**: supprimer les enregistrements avec des valeurs manquantes
* **Substitution du mannequin**: remplacer les valeurs manquantes avec une valeur factice : par exemple, _inconnue_ par catégorie ou 0 pour les valeurs numériques.
* **On entend par la substitution**: si les données manquantes sont numériques, remplacez les valeurs manquantes par la moyenne.
* **Substitution de fréquentes**: si les données manquantes sont catégoriques, remplacez les valeurs manquantes par l’élément plus fréquente
* **Substitution de régression**: une méthode de régression permet de remplacer les valeurs manquantes par des valeurs de régressions.  

## <a name="how-to-normalize-data"></a>Comment normaliser les données ?

Normalisation des données nouveau met à l’échelle des valeurs numériques à une plage spécifiée. Méthodes de normalisation des données les plus courants sont les suivants :

* **Normalisation de Min-Max**: linéairement transformer les données dans une plage, dites entre 0 et 1, où la valeur min est mis à l’échelle 0 et max valeur à 1.
* **Normalisation du Z-score**: mise à l’échelle en fonction de la moyenne et l’écart-type de données : répartir la différence entre les données et la moyenne par l’écart.
* **Mise à l’échelle décimale**: mise à l’échelle les données en déplaçant la virgule décimale de la valeur de l’attribut.  

## <a name="how-to-discretize-data"></a>Comment faire pour discrétiser les données ?

Données peuvent être discrétisées en convertissant attributs nominales ou les intervalles de valeurs continues. Certains moyens de procéder sont les suivantes :

* **Groupement de largeur égale**: diviser la plage de toutes les valeurs possibles d’un attribut en groupes de N de la même taille et affecter les valeurs qui se trouvent dans un emplacement avec le numéro d’emplacement.
* **Groupement d’égal-hauteur**: diviser la plage de toutes les valeurs possibles d’un attribut en groupes de N, chacune contenant le même nombre d’instances, puis d’affecter les valeurs qui se trouvent dans un emplacement avec le numéro d’emplacement.  

## <a name="how-to-reduce-data"></a>Comment faire pour réduire les données ?

Il existe différentes méthodes pour réduire la taille des données pour la gestion des données plus faciles. En fonction de la taille des données et le domaine, vous pouvez appliquer les méthodes suivantes :

* **Échantillonnage d’enregistrement**: exemples d’enregistrements de données et cliquez sur le sous-ensemble représentatif uniquement à partir des données.
* **Attribut échantillonnage**: sélectionnez uniquement un sous-ensemble d’attributs plus importants à partir des données.  
* **Agrégation**: diviser les données en groupes et de stocker les numéros pour chaque groupe. Par exemple, les numéros de chiffre d’affaires quotidien d’une chaîne de restaurant au cours des 20 dernières années peuvent être agrégées revenu mensuel pour réduire la taille des données.  

## <a name="how-to-clean-text-data"></a>Comment faire pour nettoyer des données de texte ?

**Les champs de texte dans les données tabulaires** peut comporter des caractères qui affectent des limites d’alignement et/ou d’enregistrement de colonnes. Par exemple, incorporé des onglets dans un alignement de colonne séparés par des tabulations de fichier cause et incorporé caractères de nouvelle ligne enregistrements lignes de cassure. Gestion de codage de texte incorrecte lors de l’écriture/lecture de texte entraîne la perte accidentelle d’introduction des caractères illisibles, par exemple, les valeurs NULL, et peut également affecter l’analyse d’informations. Faites attention lors de l’analyse et la modification peuvent être nécessaires pour nettoyer les champs de texte pour un alignement correct et/ou aux données extrait structuré de données textuelles non structurées ou semi-structurées.

**Exploration de données** offre un premier aperçu des données. Un certain nombre de problèmes de données soient détecté au cours de cette étape, et méthodes correspondantes peuvent être appliquées pour résoudre ces problèmes.  Il est important de se poser des questions telles que l’origine du problème et comment le problème peut avoir été introduit. Cela vous permet également de déterminer les étapes de traitement de données qui doivent être prises pour les résoudre. Le type d’un a l’intention de dériver à partir des données des analyses permet également de hiérarchiser l’effort de traitement de données.

## <a name="references"></a>Références

>*L’exploration de données : Concepts et Techniques*, troisième édition, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber et Jian Pei
