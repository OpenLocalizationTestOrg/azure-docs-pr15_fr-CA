<properties
    pageTitle="Comment choisir les algorithmes d’apprentissage machine | Microsoft Azure"
    description="Le choix des algorithmes de Azure Machine d’apprentissage pour l’apprentissage de mise sous contrôle et qui fonctionne sans surveillance dans les expériences de clustering, de classification ou de régression."
    services="machine-learning"
    documentationCenter=""
    authors="brohrer"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>
    
<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="08/09/2016"
    ms.author="brohrer;garye" />

# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Comment choisir les algorithmes pour la formation de Machine Microsoft Azure

La réponse à la question « Quelle machine algorithme d’apprentissage dois-je utiliser ? » est toujours « Ça dépend. » Il dépend de la taille, la qualité et la nature des données. Elle dépend de ce que vous voulez faire avec la réponse. Il dépend de comment la mathématique de l’algorithme a été traduite dans les instructions de l’ordinateur que vous utilisez. Et il dépend de combien de temps vous avez. Même les plus expérimentés scientifiques de données ne peut pas savoir quel algorithme effectuera le meilleur avant d’essayer de les.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Fiche récapitulative de l’algorithme d’apprentissage de l’ordinateur

Le **Microsoft Azure Machine d’apprentissage algorithme Fiche récapitulative** vous permet de choisir la machine adaptée algorithme pour vos solutions d’analytique prédictive à partir de la bibliothèque Microsoft Azure Machine d’apprentissage des algorithmes d’apprentissage.
Cet article vous explique comment l’utiliser.

> [AZURE.NOTE] Pour télécharger la Fiche récapitulative et suivre cet article, passez à [l’apprentissage un mémento algorithme Microsoft Azure Machine d’apprentissage Studio de l’ordinateur](machine-learning-algorithm-cheat-sheet.md).

Cette fiche récapitulative a un public très spécifique à l’esprit : début données scientifique avec l’apprentissage de l’étudiant du premier cycle de niveau ordinateur, essaie de choisir un algorithme de commence dans le Studio d’apprentissage Machine Azure. Cela signifie qu’il rend des généralisations et des oversimplifications, mais il pointera dans un sens de sécurité. Cela signifie également qu’il existe beaucoup d’algorithmes non répertoriées ici. Apprentissage automatique de Azure fur pour englober un ensemble plus complet de méthodes disponibles, nous allons ajouter les.

Ces recommandations sont compilés commentaires et conseils d’un grand nombre de chercheurs de données et d’experts de formation de machine. Nous n’a pas d’accord sur tout, mais j’ai essayé d’harmoniser ses opinions dans un consensus approximatif. La plupart des instructions de désaccord commence avec « Il dépend... »

### <a name="how-to-use-the-cheat-sheet"></a>L’utilisation de la Fiche récapitulative

Lire les étiquettes sur le graphique en tant que chemin d’accès et de l’algorithme « pour * &lt;étiquette de chemin d’accès&gt; * utiliser * &lt;algorithme de&gt;*. » Par exemple, « *vitesse* d’utiliser *deux régression logistique de classe*. » Parfois plus d’une succursale s’appliquent.
Parfois, aucun d'entre eux sera parfaitement. Ils sont conçus pour être la règle d’or recommandations, donc ne vous inquiétez est exacte.
Plusieurs scientifiques de données que j’ai parlé à ladite qui l’uniquement que pour trouver le meilleur algorithme consiste à essayer toutes les.

Voici un exemple de la [Galerie d’Intelligence de Cortana](http://gallery.cortanaintelligence.com/) d’une expérience qui essaie plusieurs algorithmes sur les mêmes données et compare les résultats : [comparer les classifieurs multiples : reconnaissance de lettre](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

>[AZURE.TIP] Pour télécharger et imprimer un diagramme qui donne une vue d’ensemble des fonctionnalités de Studio d’apprentissage Machine, voir [diagramme de vue d’ensemble des fonctionnalités de Studio de formation de Machine Azure](machine-learning-studio-overview-diagram.md).

## <a name="flavors-of-machine-learning"></a>Types d’apprentissage automatique

### <a name="supervised"></a>Surveillés

Algorithmes d’apprentissage contrôlés effectuer des prévisions basées sur un ensemble d’exemples. Par exemple, les historiques des cours peuvent servir à risque des estimations à prix futurs. Chaque exemple utilisée pour la formation est étiquetée avec la valeur des intérêts, dans ce cas, le prix de stock. Un algorithme d’apprentissage contrôlés recherche des modèles dans les étiquettes de valeur. Il peut utiliser toutes les informations qui peuvent être pertinentes, le jour de la semaine, la saison, données financières de la société, du type d’industrie, la présence d’événements d’interruption de l’activité geopolicitical, et chaque algorithme de recherche de différents types de modèles. Après l’algorithme a détecté le meilleur modèle possible, il utilise ce modèle pour effectuer des prévisions pour les données de tests sans étiquette : prix de demain.

Il s’agit d’un type utile et apprécié d’apprentissage de l’ordinateur. À une exception près, tous les modules de formation de Machine Azure sont contrôlés d’algorithmes d’apprentissage. Il existe plusieurs types d’apprentissage contrôlé spécifiques qui sont représentés dans l’apprentissage de Machine Azure : détection des anomalies, de classification et de régression.

* **Classification**. Lorsque les données sont utilisées pour prévoir une catégorie, formation contrôlée est également appelée classification. C’est le cas lors de l’affectation d’une image en tant qu’image d’un « chat » ou un « chien ». Lorsqu’il n’y a que deux options, il s’agit de **deux classes** ou **classification binomiale**. Lorsqu’il existe plusieurs catégories, comme lors de prédire le gagnant du tournoi NCAA du printemps, ce problème est connu en tant que **multi-classe classification**.

* **Régression**. Lorsqu’une valeur est est prévue, à l’instar de bourse, formation contrôlée est appelée régression.

* **Détection des anomalies**. Parfois, l’objectif est d’identifier les points de données qui sont simplement inhabituels. Par exemple, dans la détection des fraudes, les modèles de dépenses très inhabituelle de carte de crédit sont suspects. Les variations possibles sont si nombreuses et les exemples de formation si faible qu’il n’est pas possible de savoir quelle activité frauduleuse ressemble à. L’approche qui accepte de détection des anomalies consiste à simplement savoir quelle activité normale ressemble (à l’aide d’un historique des non-de transactions frauduleuses) et identifier tout ce qui est très différente.

### <a name="unsupervised"></a>Qui fonctionne sans surveillance

Dans apprentissage, des points de données ne contiennent aucune étiquette associée. Au lieu de cela, l’objectif d’un algorithme d’apprentissage consiste à organiser les données d’une certaine façon ou pour décrire sa structure. Cela peut signifier le regroupant en clusters ou la recherche de différentes manières de visualiser des données complexes, afin qu’il apparaisse plus simple ou plus organisée.

### <a name="reinforcement-learning"></a>Formation de renforcement

Dans le renforcement de formation, l’algorithme Obtient de choisir une action en réponse à chaque point de données. L’algorithme d’apprentissage reçoit également un signal de récompenser quelques instants plus tard, indiquant la qualité a de la décision.
Sur cette base, l’algorithme modifie sa stratégie pour atteindre la récompense la plus élevée. Actuellement il n’y a aucun renforcement de modules d’algorithme dans Azure Machine de formation de formation. Formation de renforcement est courante dans robotics, où l’ensemble des lectures des capteurs à un moment donné dans le temps est un point de données, et que l’algorithme doit choisir l’action suivante du robot. Il est également qu'un complément naturel pour l’Internet des objets applications.

## <a name="considerations-when-choosing-an-algorithm"></a>Considérations lors du choix d’un algorithme

### <a name="accuracy"></a>Précision

L’obtention de la réponse la plus précise possible n’est pas toujours nécessaire.
Parfois, un rapprochement est adéquat, en fonction de ce que vous voulez utiliser pour. Si tel est le cas, vous ne pourrez pas couper de votre temps de traitement considérablement par égorgement avec plusieurs méthodes approximatives. Un autre avantage de plusieurs méthodes approximatives est qu’ils ont tendance naturellement pour éviter les [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Temps de formation

Le nombre de minutes ou d’heures nécessaires pour former un modèle varie beaucoup entre les algorithmes. Temps de formation est souvent étroitement liée à la précision — une accompagne généralement de l’autre. En outre, certains algorithmes sont plus sensibles au nombre de points de données que d’autres.
Lorsque le temps est limité il peut piloter le choix de l’algorithme, en particulier lorsque le jeu de données est volumineux.

### <a name="linearity"></a>Linéarité

Beaucoup d’algorithmes d’apprentissage machine de rendre l’utilisation de la linéarité. Algorithmes de classification linéaire supposent que les classes peuvent être séparés par une ligne droite (ou son analogique supérieure dimension). Des régression logistique et prise en charge de machines vectorielles (tel qu’il est implémenté dans l’apprentissage automatique de Azure).
Algorithmes de régression linéaire supposent que les tendances des données suivent une ligne droite. Ces hypothèses ne sont pas défectueux pour certains problèmes, mais dans d’autres ils apportent précision vers le bas.

![Classe non linéaire bounday][1]

***Limite de classe non linéaire*** *-s’appuyer sur un algorithme de classification linéaire aurait pour résultat une précision faible*

![Données avec une tendance linéaire][2]

***Données avec une tendance linéaire*** *-à l’aide d’une méthode de régression linéaire génère les erreurs beaucoup plus grandes que nécessaire*

En dépit de leurs dangers, algorithmes linéaires sont très populaires comme une première ligne d’attaque. Ils sont généralement par algorithme simple et rapide pour l’apprentissage.

### <a name="number-of-parameters"></a>Nombre de paramètres

Les paramètres sont les boutons que scientifique données obtient à activer lorsque vous configurez un algorithme. Ils sont des nombres qui affectent le comportement de l’algorithme, telles que la tolérance d’erreur ou le nombre d’itérations, ou options entre deux variantes du comporte de l’algorithme. Le temps de formation et de la précision de l’algorithme peuvent parfois être assez sensibles pour obtenir uniquement les paramètres de droit. En règle générale, les algorithmes avec des paramètres de nombreux nécessitent la plupart des essais et d’erreurs pour trouver une bonne combinaison.

Il est également un bloc module [paramètre balayage](machine-learning-algorithm-parameters-optimize.md) dans l’apprentissage automatique Azure qui tente automatiquement de toutes les combinaisons de paramètres à la granularité que vous choisissez. Alors que c’est un excellent moyen de vous assurer que vous avez fractionné de l’espace du paramètre, le temps requis pour former un modèle augmente de façon exponentielle avec le nombre de paramètres.

L’avantage est que de nombreux paramètres généralement indique qu’un algorithme a une plus grande flexibilité. Elle permet souvent d’obtenir précision très bonne. Condition que vous pouvez trouver la bonne combinaison de valeurs de paramètre.

### <a name="number-of-features"></a>Nombre de fonctions

Pour certains types de données, le nombre de fonctionnalités peut être très grand par rapport au nombre de points de données. C’est souvent le cas avec génétique ou des données textuelles. Le grand nombre de fonctionnalités risque de ralentir certains algorithmes d’apprentissage, fabrication formation temps unfeasibly long. Machines à vecteurs de support sont particulièrement bien adaptés à ce cas (voir ci-dessous).

### <a name="special-cases"></a>Cas particuliers

Certains algorithmes de formation faire des hypothèses notamment sur la structure des données ou les résultats escomptés. Si vous pouvez en trouver un qui corresponde à vos besoins, elle peut vous donner des résultats plus pertinents, des prévisions plus précises ou les durées d’apprentissage plus rapides.

|**Algorithme**|**Précision**|**Temps de formation**|**Linéarité**|**Paramètres**|**Notes**|
|---|:---:|:---:|:---:|:---:|---|
|**Classification de la classe 2**| | | | | |
|[régression logistique](https://msdn.microsoft.com/library/azure/dn905994.aspx)                    | |●|●|5| |
|[forêt de décision](https://msdn.microsoft.com/library/azure/dn906008.aspx)|●|○| |6| |
|[jungle de décision](https://msdn.microsoft.com/library/azure/dn905976.aspx)|●|○| |6|Encombrement faible mémoire|
|[arbre de décision amplifié](https://msdn.microsoft.com/library/azure/dn906025.aspx)|●|○| |6|Grande quantité de mémoire|
|[réseau neuronal](https://msdn.microsoft.com/library/azure/dn905947.aspx)|●| | |9|[Personnalisation supplémentaire est possible](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[perceptron moyenne](https://msdn.microsoft.com/library/azure/dn906036.aspx)|○|○|●|4| |
|[machine à vecteur support](https://msdn.microsoft.com/library/azure/dn905835.aspx)| |○|●|5|Idéal pour les ensembles de fonctionnalités de grande taille|
|[machine à vecteur support deep localement](https://msdn.microsoft.com/library/azure/dn913070.aspx)|○| | |8|Idéal pour les ensembles de fonctionnalités de grande taille|
|[Machine de point des Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx)| |○|●|3| |
|**Classification de multiples**| | | | | |
|[régression logistique](https://msdn.microsoft.com/library/azure/dn905853.aspx)| |●|●|5| |
|[forêt de décision](https://msdn.microsoft.com/library/azure/dn906015.aspx)|●|○| |6| |
|[jungle de décision](https://msdn.microsoft.com/library/azure/dn905963.aspx)|●|○| |6|Encombrement faible mémoire|
|[réseau neuronal](https://msdn.microsoft.com/library/azure/dn906030.aspx)|●| | |9|[Personnalisation supplémentaire est possible](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[1-v-tous](https://msdn.microsoft.com/library/azure/dn905887.aspx)|-|-|-|-|Consultez les propriétés de la méthode de classe de deux sélectionné|
|**Régression**| | | | | |
|[linéaire](https://msdn.microsoft.com/library/azure/dn905978.aspx)| |●|●|4| |
|[Bayésien linéaire](https://msdn.microsoft.com/library/azure/dn906022.aspx)| |○|●|2| |
|[forêt de décision](https://msdn.microsoft.com/library/azure/dn905862.aspx)|●|○| |6| |
|[arbre de décision amplifié](https://msdn.microsoft.com/library/azure/dn905801.aspx)|●|○| |5|Grande quantité de mémoire|
|[quantile de forêt rapide](https://msdn.microsoft.com/library/azure/dn913093.aspx)|●|○| |9|Distributions plutôt que des prévisions de point|
|[réseau neuronal](https://msdn.microsoft.com/library/azure/dn905924.aspx)|●| | |9|[Personnalisation supplémentaire est possible](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx)| | |●|5|Techniquement journal linéaire. Pour la prédiction de nombres|
|[ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx)| | | |0|Pour prévoir l’ordre de classement|
|**Détection d’anomalie**| | | | | |
|[machine à vecteur support](https://msdn.microsoft.com/library/azure/dn913103.aspx)|○|○| |2|Particulièrement aux ensembles de fonctionnalités de grande taille|
|[Détection d’anomalie de basée sur l’Assistant Compatibilité des programmes](https://msdn.microsoft.com/library/azure/dn913102.aspx)| |○|●|3| |
|[K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)| |○|●|4|Un algorithme de mise en cluster|


**Propriétés de l’algorithme :**

**●** - affiche une excellent précision, les durées d’apprentissage rapide et l’utilisation de linéarité

**○** - affiche plus de précision et d’heures de formation modéré

## <a name="algorithm-notes"></a>Notes de l’algorithme

### <a name="linear-regression"></a>Régression linéaire

Comme mentionné précédemment, [régression linéaire](https://msdn.microsoft.com/library/azure/dn905978.aspx) correspond à une ligne (ou plan ou hyperplane) au jeu de données. Il s’agit d’un produit phare, simple et rapide, mais il peut être trop simpliste pour certains problèmes.
Cochez cette case pour afficher un [didacticiel de régression linéaire](machine-learning-linear-regression-in-azure.md).

![Données avec une courbe de tendance linéaire][3]

***Données avec une courbe de tendance linéaire***

### <a name="logistic-regression"></a>Régression logistique

Bien qu’il inclut confusion avec 'régression' dans le nom, régression logistique est en fait un outil puissant pour la classification de [classe 2](https://msdn.microsoft.com/library/azure/dn905994.aspx) et [multiclass](https://msdn.microsoft.com/library/azure/dn905853.aspx) . Il est rapide et simple. Le fait qu’il utilise une '-forme courbe au lieu d’une ligne droite, il est naturellement pour diviser les données en groupes. Limites de classe linéaire régression logistique permet, lorsque vous l’utilisez, assurez-vous donc qu'une approximation linéaire est quelque chose que vous conviennent.

![Les données de deux-classes avec une fonctionnalité régression logistique][4]

***Une régression logistique deux-classe des données avec une fonctionnalité*** *-la limite de classe est le point où la courbe logistique est aussi près les deux classes*

### <a name="trees-forests-and-jungles"></a>Arborescences, forêts et jungles

Décision ([régression](https://msdn.microsoft.com/library/azure/dn905862.aspx), [deux-classe](https://msdn.microsoft.com/library/azure/dn906008.aspx)et [multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)), jungles de décision ([deux-classe](https://msdn.microsoft.com/library/azure/dn905976.aspx) et [multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)), arborescences et des forêts décision amplifiée ([régression](https://msdn.microsoft.com/library/azure/dn905801.aspx) et [deux classes](https://msdn.microsoft.com/library/azure/dn906025.aspx)) reposent toutes sur les arbres de décision, une machine aidant concept d’apprentissage. Il existe de nombreuses variantes d’arbres de décision, mais ils font tous la même chose : subdiviser l’espace fonctionnalité en régions avec essentiellement la même étiquette. Il peut s’agir de catégorie cohérente ou de valeur de constante, en fonction de si vous effectuez une classification ou une régression de régions.

![Arbre de décision subdivise un espace de fonctionnalité][5]

***Un arbre de décision subdivise un espace de fonctionnalité dans les régions de valeurs à peu près uniformes.***

Un espace de fonction peut être subdivisé en zones arbitrairement petites, il est facile d’imaginer divisant finement assez d’avoir un point de données par région : un exemple extrême de dépassement de l’ajustement. Pour éviter cela, un vaste ensemble d’arborescences sont construits avec un soin particulier mathématique pris que les arborescences ne sont pas mis en corrélation. La moyenne de cette forêt « décision » est une arborescence afin d’éviter le dépassement de l’ajustement. Forêts de décision peuvent utiliser beaucoup de mémoire. Jungles de décision sont une variante qui consomme moins de mémoire aux dépens d’un temps de formation légèrement plus long.

Arborescences de décision amplifiée éviter le dépassement de l’ajustement en limitant le nombre de fois où ils peuvent subdiviser et quelques points de données sont autorisées dans chaque région. L’algorithme construit une séquence d’arbres, chacun d’eux est capable d’assimiler pour compenser l’erreur de gauche par l’arborescence avant. Le résultat est un étudiant très précis qui ont tendance à utiliser beaucoup de mémoire. Pour la description complète, consultez [papier d’origine de Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Régression de quantile forêt rapide](https://msdn.microsoft.com/library/azure/dn913093.aspx) est une variante d’arborescences de décision pour le cas particulier où vous souhaitez connaître non seulement la valeur par défaut (médiane) des données au sein d’une région, mais également sa distribution sous la forme d’une étendue.

### <a name="neural-networks-and-perceptrons"></a>PERCEPTRONS et réseaux neuronaux

Les réseaux neuronaux sont inspirés de cerveau algorithmes portant sur les problèmes de [régression](https://msdn.microsoft.com/library/azure/dn905924.aspx) [multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx)et [deux-classe](https://msdn.microsoft.com/library/azure/dn905947.aspx)de formation. Ils sont fournis dans une variété infinie, mais les réseaux neuronaux dans Azure Machine apprentissage sont tous de la forme de graphiques acycliques dirigés. Cela signifie que les fonctions d’entrée sont passées vers l’avant (jamais vers l’arrière) via une série de couches avant d’être activée en sorties. Dans chaque couche, les entrées sont affectées de la pondération dans diverses combinaisons, additionnées et transmises à la couche suivante. Cette combinaison de calculs simples entraîne d’apprendre de classe sophistiquée des tendances des limites et des données, apparemment par magie. La « formation approfondie » qui combustibles tellement de reporting de la technique et de science-fiction d’effectuer plusieurs couches de réseaux de ce type.

Cette haute performance n’est pas fourni gratuitement, cependant. Les réseaux neuronaux peuvent prendre beaucoup de temps à former, particulièrement pour les grands ensembles de données avec un grand nombre de fonctionnalités. Ils ont également plus de paramètres que la plupart des algorithmes, ce qui signifie que balayage du paramètre développe beaucoup le temps de formation.
Et, pour ces overachievers qui souhaitent [spécifier leur propre structure de réseau](http://go.microsoft.com/fwlink/?LinkId=402867), les possibilités sont inexhaustible.

<a name="boundaries-learned-by-neural-networks6"></a>![Limites de la preuve dans les réseaux neuronaux][6]
---------------------------

***Les limites appris par les réseaux neuronaux peuvent être complexes et irrégulières***

Les [deux-classe moyenne perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) la réponse des réseaux neuronaux est à la fois de formation explosion. Il utilise une structure de réseau qui fournit les limites entre les classes linéaire. Il est presque primitif par les normes actuelles, mais il a un long historique de l’utilisation de manière robuste et est suffisamment petite pour en savoir plus rapidement.

### <a name="svms"></a>SVMs

Machines à vecteurs de support (SVMs) trouve la frontière qui sépare les classes par large une marge que possible. Lorsque les deux classes ne peuvent pas être clairement séparés, les algorithmes de trouver la meilleure limite qu’ils peuvent. Lors de l’écriture dans l’apprentissage automatique de Azure, les [deux-classe SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx) cela, avec une ligne droite uniquement. (Dans le jargon SVM, il utilise un noyau linéaire). Car elle fait de cette approximation linéaire, il est en mesure de s’exécuter assez rapidement. Où il s’avère vraiment utile est gourmandes en fonction de données, tels que le texte ou génomique. Dans ces cas SVMs sont en mesure de séparer les classes plus rapidement et avec moins de dépassement que la plupart des autres algorithmes, en plus des uniquement une petite quantité de mémoire.

![Limite de classe de prise en charge du vecteur machine][7]

***Une limite de classe de prise en charge classique vecteur machine optimise la marge séparant deux classes***

Un autre produit de Microsoft Research, le [SVM localement approfondie de deux-classe](https://msdn.microsoft.com/library/azure/dn913070.aspx) est un variant non linéaire de SVM qui conserve la plupart de l’efficacité de mémoire et de vitesse de la version linéaire. Il est idéal pour les cas où l’approche linéaire ne donne pas les réponses suffisamment précises. Les développeurs conservé rapide par décomposer le problème en une série de petits problèmes SVM linéaires. Lisez la [description complète](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf) pour plus de détails sur comment ils extrait de cette astuce.

À l’aide d’une extension intelligente de SVMs non linéaires, la [classe un SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx) Dessine une frontière étroitement présente l’ensemble des données. Il est utile pour la détection des anomalies. Les nouveaux points de données qui se trouvent bien à l’extérieur de cette limite sont assez inhabituels être dignes d’attention.

### <a name="bayesian-methods"></a>Méthodes bayésien

Les méthodes bayésien ont une qualité très intéressante : ils évitent le dépassement de l’ajustement. Pour ce faire, ils suppose au préalable sur la distribution probable de la réponse. Un autre sous-produit de cette approche est qu’ils ont très peu de paramètres. Formation de Machine Azure a les deux algorithmes bayésien de classification ([ordinateur de point de deux classes de Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx)) et de régression ([régression linéaire bayésienne](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Notez que ces supposent que les données peuvent être fractionnées ou ajuster avec une ligne droite.

Sur une note d’historique, machines de point des Bayes ont été développés de Microsoft Research. Ils ont certains travaux théoriques exceptionnellement beau derrière eux. L’étudiant intéressée est dirigé vers l' [article d’origine dans JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) et un [blog de Chris Bishop ingénieuses](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algorithmes spécialisés

Si vous avez un objectif très spécifique vous est peut-être de la chance. Les algorithmes spécialisés dans la détection des anomalies (une basée sur [l’analyse des principaux composants](https://msdn.microsoft.com/library/azure/dn913102.aspx) et une basée sur la [prise en charge de la machine à vecteur](https://msdn.microsoft.com/library/azure/dn913103.aspx)de s), nombre prévision ([régression de Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx)) et prediction de rang ([régression ordinale](https://msdn.microsoft.com/library/azure/dn906029.aspx)) sont dans la collection de formation de Machine Azure.
Et il existe un seul cluster algorithme ainsi ([K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)).

![Détection d’anomalie de basée sur l’Assistant Compatibilité des programmes][8]

***Détection d’anomalie de basée sur l’Assistant Compatibilité des programmes*** *-la grande majorité des données se situe dans une distribution stereotypical ; points qui s’écartent considérablement de que distribution sont suspects*

![Jeu de données groupé à l’aide des moyens K][9]

***Un jeu de données sont regroupé dans des 5 clusters K-moyen***

Il existe également un ensemble [classifieur de multiclass un-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx), qui interrompt le problème de classification N-classe des problèmes de classification de la classe de deux N-1. La précision, les temps de formation et les propriétés de linéarité sont déterminées par classe de deux classifieurs utilisés.

![Classe de deux classifieurs combinés pour former un classifieur de classe 3][10]

***Une paire de classe deux classifieurs combiner pour former un classifieur de classe 3***

D’apprentissage Machine Azure comprend également un accès à une infrastructure d’apprentissage ordinateur puissant sous le titre de [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW définit la catégorisation ici, dans la mesure où il peut savoir problèmes de classification et de régression et même en savoir à partir de données partiellement non labellisées. Vous pouvez le configurer pour utiliser l’un des différents algorithmes, les fonctions de la perte et les algorithmes d’optimisation de la formation. Il a été conçu dès le départ extrêmement rapide, efficace et parallèle. Il gère les jeux de fonctionnalité élevée avec peu d’effort apparent.
Démarrage et dirigée par Microsoft Research propre John Langford, VW est une entrée d’une formule dans un champ d’algorithmes de voiture de stock. Tous les problèmes tient VW, mais si le vôtre est le cas, il peut être intéressant de vous en avalant de la courbe d’apprentissage sur son interface. Il est également disponible en tant que [code source d’ouvrir autonome](https://github.com/JohnLangford/vowpal_wabbit) dans plusieurs langues.


<!-- Media -->

[1]: ./media/machine-learning-algorithm-choice/image1.png
[2]: ./media/machine-learning-algorithm-choice/image2.png
[3]: ./media/machine-learning-algorithm-choice/image3.png
[4]: ./media/machine-learning-algorithm-choice/image4.png
[5]: ./media/machine-learning-algorithm-choice/image5.png
[6]: ./media/machine-learning-algorithm-choice/image6.png
[7]: ./media/machine-learning-algorithm-choice/image7.png
[8]: ./media/machine-learning-algorithm-choice/image8.png
[9]: ./media/machine-learning-algorithm-choice/image9.png
[10]: ./media/machine-learning-algorithm-choice/image10.png
