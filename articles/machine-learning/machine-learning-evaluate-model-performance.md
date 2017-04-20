<properties 
    pageTitle="Évaluation des performances du modèle dans l’apprentissage automatique | Microsoft Azure" 
    description="Explique comment évaluer les performances du modèle dans l’apprentissage automatique de Azure." 
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
    ms.date="08/19/2016" 
    ms.author="bradsev;garye" />


# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>L’évaluation des performances du modèle dans l’apprentissage automatique de Azure

Cette rubrique montre comment évaluer les performances d’un modèle dans un Studio de formation de Machine Azure et fournit une brève explication sur les mesures disponibles pour cette tâche. Trois scénarios courants de formation contrôlés sont présentées : 

* régression
* classement binaire 
* classement multiclass

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Évaluation des performances d’un modèle est une des étapes principales du processus de données scientifiques. Il indique les score (prévisions) d’un groupe de données a réussi par un modèle formé. 

Formation de Machine Azure prend en charge les évaluation de modèle par le biais de deux de ses modules de formation de machine principale : [Évaluer le modèle] [ evaluate-model] et [Cross-valider le modèle][cross-validate-model]. Ces modules permettent de voir comment votre modèle s’exécute en fonction d’un certain nombre de mesures qui sont couramment utilisés dans les statistiques et apprentissage automatique.

##<a name="evaluation-vs-cross-validation"></a>Validation de l’évaluation et les##
D’évaluation et de validation croisée sont des méthodes standard permettant de mesurer les performances de votre modèle. Les deux génèrent des mesures d’évaluation que vous pouvez inspecter ou comparer par rapport à ceux des autres modèles.

[Évaluation du modèle] [ evaluate-model] attend un groupe de données évaluée comme entrée (ou 2 dans les cas que vous souhaitez comparer les performances des différents modèles de 2). Cela signifie que vous devez former votre modèle en utilisant le [Modèle de formation] [ train-model] module et effectuer des prévisions sur certains dataset à l’aide du [Modèle de Score] [ score-model] module, avant de pouvoir évaluer les résultats. L’évaluation est fonction d’étiquettes/probabilités évaluées et les étiquettes, trues, qui sont tous des sorties par le [Modèle de Score] [ score-model] module.

Vous pouvez également utiliser automatiquement des validation croisée à effectuer un certain nombre d’opérations d’évaluer train-score (10 plis) sur différents sous-ensembles de données d’entrée. Les données d’entrée sont divisées en 10 pièces, où un est réservé pour le test, et les autres 9 pour la formation. Ce processus se répète 10 fois, et les mesures d’évaluation moyenne sont calculées. Cela permet de déterminer comment un modèle serait généraliser à nouveaux ensembles de données. Le [Cross-valider le modèle] [ cross-validate-model] module utilise un modèle non formé et certains dataset étiquetée et renvoie les résultats de l’évaluation de chaque les 10 plis, en plus des résultats moyennes.

Dans les sections suivantes, nous construire des modèles de classification et de régression simples et évaluer leurs performances, en utilisant à la fois le [Modèle d’évaluation] [ evaluate-model] et le [Cross-valider le modèle] [ cross-validate-model] modules.

##<a name="evaluating-a-regression-model"></a>Évaluation d’un modèle de régression##
Supposons que nous voulons prédire le prix d’une voiture à l’aide de certaines fonctionnalités telles que les dimensions, puissance, caractéristiques du moteur et ainsi de suite. Il s’agit d’un problème classique de régression, où la variable cible (*prix*) est une valeur numérique en continu. Nous pouvons ajuster un modèle de régression linéaire simple qui, d’après les valeurs de la fonctionnalité d’une voiture, certains peut prévoir le prix de cette voiture. Ce modèle de régression peut être utilisé pour évaluer le score du même groupe de données sur que nous avons formés. Une fois les prix prévus pour toutes les voitures, nous pouvons évaluer les performances du modèle en regardant combien les prévisions s’écartent les prix réels en moyenne. Pour illustrer cela, nous utilisons le *dataset de données (Raw) Automobile prix* disponibles dans la section **Des groupes de données enregistrées** dans le Studio de formation de Machine Azure.
 
###<a name="creating-the-experiment"></a>Création de l’expérience###
Ajouter les modules suivants à votre espace de travail dans un Studio de formation de Machine Azure :

- Données de prix automobile (Raw)
- [Régression linéaire][linear-regression]
- [Modèle de train][train-model]
- [Modèle de score][score-model]
- [Évaluation du modèle][evaluate-model]


Connectez les ports comme indiqué ci-dessous à la Figure 1 et la valeur de la colonne libellé du [Modèle de Train] [ train-model] module au *prix*.
 
![Évaluation d’un modèle de régression](media/machine-learning-evaluate-model-performance/1.png)

La figure 1. Évaluation d’un modèle de régression.

###<a name="inspecting-the-evaluation-results"></a>Examen des résultats de l’évaluation###
Après l’exécution de l’essai, vous pouvez cliquer sur le port de sortie du [Modèle d’évaluation] [ evaluate-model] module et sélectionnez *visualiser* pour voir les résultats de l’évaluation. Les mesures d’évaluation disponibles pour les modèles de régression sont : *Signifie que l’erreur absolue*, *Racine signifie une erreur absolue*, *Erreur absolue Relative*, *Des erreurs relatives au carré*et le *Coefficient de détermination*.

Le terme « erreur » représente la différence entre la valeur prévue et de la valeur true. La valeur absolue ou le carré de cette différence sont généralement calculées pour capturer l’amplitude totale d’erreur pour toutes les instances, comme la différence entre la valeur prévue et true peut être négative dans certains cas. Les métriques d’erreur mesurent les performances de prévision d’un modèle de régression en fonction de l’écart de la moyenne de ses prévisions à partir des valeurs trues. Valeurs d’erreur inférieures signifient que le modèle est plus précis des prévisions. Une mesure d’erreur globale de 0 signifie que le modèle de données est parfaitement adaptée aux.

Le coefficient de détermination, qui est également appelé R au carré, est également un moyen standard de mesure de la façon dont le modèle adapté aux données. Il peut être interprété comme la proportion de variation expliquée par le modèle. Une proportion est préférable dans ce cas, où 1 indique une solution idéale.
 
![Mesures d’évaluation de régression linéaire](media/machine-learning-evaluate-model-performance/2.png)

La figure 2. Mesures d’évaluation de régression linéaire.

###<a name="using-cross-validation"></a>À l’aide de croisée de Validation###
Comme mentionné précédemment, vous pouvez effectuer la formation répétée, évaluation et les évaluations automatiquement à l’aide du [Cross-valider le modèle] [ cross-validate-model] module. Il vous suffit dans ce cas est un groupe de données et un modèle non formé un [Cross-valider le modèle] [ cross-validate-model] module (voir figure ci-dessous). Notez que vous devez définir la colonne étiquette de *prix* dans le [Cross-valider le modèle] [ cross-validate-model] les propriétés du module.

![Cross-validation d’un modèle de régression](media/machine-learning-evaluate-model-performance/3.png)

La figure 3. Cross-validation d’un modèle de régression.

Après l’exécution de l’essai, vous pouvez vérifier les résultats de l’évaluation en cliquant sur le port de sortie droit du [Cross-valider le modèle] [ cross-validate-model] module. Ceci fournira une vue détaillée des mesures pour chaque itération (pliage) et les moyenne des résultats de chacun des indicateurs (Figure 4).
 
![Résultats de Validation croisée d’un modèle de régression](media/machine-learning-evaluate-model-performance/4.png)

La figure 4. Résultats de Validation croisée d’un modèle de régression.

##<a name="evaluating-a-binary-classification-model"></a>Évaluation d’un modèle de Classification binaire##
Dans un scénario de classification binaire, la variable cible a seulement deux résultats possibles, par exemple : {0, 1} ou {false, true}, {négatif et positif}. Supposent que vous disposez d’un groupe de données d’employés adultes avec certains démographiques et variables d’emploi, et que vous êtes invité à prévoir le niveau de revenu, un binaire variable avec les valeurs {"< = 50K », « > 50K »}. En d’autres termes, la classe négative représente les employés qui effectuent inférieure ou égale à 50K par an, et la classe positive représente tous les autres employés. Comme dans le scénario de la régression, nous exercer un modèle, certaines données de score et évaluer les résultats. La principale différence ici est le choix des mesures de que formation de Machine Azure calcule et des sorties. Pour illustrer le scénario de prévision au niveau de revenu, nous allons utiliser le dataset [pour adultes](http://archive.ics.uci.edu/ml/datasets/Adult) à créer une expérience d’apprentissage automatique de Azure et d’évaluer les performances d’un modèle de régression logistique de deux classes, un classifieur binaire couramment utilisé.

###<a name="creating-the-experiment"></a>Création de l’expérience###
Ajouter les modules suivants à votre espace de travail dans un Studio de formation de Machine Azure :

- Dataset de Classification binaire de recensement revenu adulte
- [Régression logistique de classe 2][two-class-logistic-regression]
- [Modèle de train][train-model]
- [Modèle de score][score-model]
- [Évaluation du modèle][evaluate-model]

Connecter les ports comme indiqué dans la Figure 5 ci-dessous et définissez la colonne étiquette du [Modèle de Train] [ train-model] module de *revenu*.

![Évaluation d’un modèle de Classification binaire](media/machine-learning-evaluate-model-performance/5.png)

La figure 5. Évaluation d’un modèle de Classification binaire.

###<a name="inspecting-the-evaluation-results"></a>Examen des résultats de l’évaluation###
Après l’exécution de l’essai, vous pouvez cliquer sur le port de sortie du [Modèle d’évaluation] [ evaluate-model] module et sélectionnez *visualiser* pour voir les résultats de l’évaluation (Figure 7). Les mesures d’évaluation disponibles pour les modèles de classement binaire sont : *exactitude*, *précision*, *rappeler*, un *Score de F1*et *AUC*. En outre, le module renvoie une matrice de confusion indiquant le nombre de true positifs, faux négatifs, faux positifs et trues négatifs, ainsi que des courbes *ROC*, de *Précision et de rappel*et *soulevez* .

La précision est simplement la proportion d’instances correctement classées. Il est généralement la première mesure qu'examiner lors de l’évaluation d’un classifieur. Toutefois, lorsque les données de test sont asymétrique (dont la plupart des instances appartient à une des classes), ou si vous êtes plus intéressé par les performances sur l’une des classes, précision ne capture vraiment pas l’efficacité d’un classifieur. Dans le scénario de la classification des niveaux de revenu, supposons que vous testez des données où 99 % des instances représentent des personnes qui sont inférieures ou égales à 50K par an. Il est possible d’atteindre une précision 0,99 prédisant la classe « < = 50K » pour toutes les instances. Le classifieur s’affiche dans ce cas pour faire un bon travail global, mais en réalité, il ne peut pas classer les personnes à revenus élevés (1 %) correctement.

Pour cette raison, il est utile calculer des statistiques supplémentaires qui capture des aspects plus spécifiques de l’évaluation. Avant de rentrer dans les détails de ces mesures, il est important de comprendre la matrice de confusion d’une évaluation de la classification binaire. Les étiquettes de classe dans le jeu de configuration peuvent prendre seulement 2 valeurs possibles, que nous appelons généralement comme positive ou négative. Un classifieur prédit correctement les instances positives et négatives sont appelés positifs trues (TP) et trues négatifs (TN), respectivement. De même, les instances incorrectement classifiés sont appelés (FP) de faux positifs et faux négatifs (FN). La matrice de confusion est simplement un tableau indiquant le nombre d’instances qui tombent sous chacune de ces catégories de 4. Formation de Machine Azure décide automatiquement parmi les deux classes, dans le groupe de données est la classe positive. Si les étiquettes de la classe sont booléennes ou des entiers, puis les instances intitulés 'true' ou '1' sont affectés à la classe positive. Si les étiquettes sont des chaînes, comme dans le cas du groupe de données sur le revenu, les étiquettes sont triés par ordre alphabétique et le premier niveau est choisi pour être la classe négative, tandis que le deuxième niveau est la classe positive.

![Matrice de Confusion de Classification binaire](media/machine-learning-evaluate-model-performance/6a.png)

La figure 6. Matrice de Confusion de Classification binaire.

Pour revenir au problème de classification de revenu, nous voulons poser plusieurs questions d’évaluation qui nous aident à comprennent les performances du classifieur utilisé. Une question très naturelle est : « de toutes les personnes auxquelles le modèle prédit à gagner > 50 K (TP + FP), combien ont été classés correctement (TP) ?' Peut répondre à cette question en examinant la **précision** du modèle, qui est la proportion de positifs qui sont classés correctement : TP/(TP+FP). Une autre question commune est « de toutes les hautes engranger des employés disposant d’un revenu > 50 k (TP + FN), combien le classifieur classer correctement (TP) ». C’est en fait le **rappel**ou le taux positif true : TP/(TP+FN) du classifieur. Vous remarquerez qu’il existe un compromis évidente entre la précision et de rappel. Par exemple, étant donné un groupe de données relativement équilibrée, un classifieur qui prévoit principalement des cas positifs, aurait un rappel élevé, mais une précision de plutôt faible autant d’instances négatifs serait mal catégorisée résultant dans un grand nombre de faux positifs. Pour visualiser un tracé de la manière dont ces deux mesures peuvent varier, vous pouvez cliquer sur la courbe de la « Précision et rappel » dans la page sortie de résultat d’évaluation (haut à gauche de la Figure 7).

![Résultats de l’évaluation Classification binaire](media/machine-learning-evaluate-model-performance/7.png) Figure 7. Résultats de l’évaluation Classification binaire.

Une autre mesure connexe qui est souvent utilisé est le **Score de F1**, qui prend la précision et rappel en considération. Elle est la moyenne harmonique de ces 2 métriques et est calculée en tant que tel : F1 = 2 (rappel de précision x) / (précision + rappel). Le score de F1 est un bon moyen pour récapituler l’évaluation dans un nombre unique, mais il est toujours judicieux d’examiner à la fois de précision et de rappel afin de mieux comprendre le comporte d’un classifieur.

En outre, un peut inspecter le taux vrai positif et le taux de faux positifs dans la courbe **Caractéristique de fonctionnement de récepteur (ROC)** et la valeur correspondante de la **Zone sous la courbe (AUC)** . Cette courbe est proche du coin supérieur gauche, plus les performances du classifieur (qui est maximiser le taux positif true tout en réduisant le taux de faux positifs). Courbes sont proches de la diagonale de la placette, résultat de classifieurs qui ont tendance à faire des prévisions qui sont proches d’estimation aléatoire.

###<a name="using-cross-validation"></a>À l’aide de croisée de Validation###
Comme dans l’exemple de la régression, nous pouvons effectuer une validation croisée pour former, score et évaluer automatiquement les différents sous-ensembles de données à plusieurs reprises. De la même façon, nous pouvons utiliser le [Cross-valider le modèle] [ cross-validate-model] module, un modèle non formé de régression logistique et un groupe de données. La colonne de l’étiquette doit indiquer *sur le revenu* dans le [Cross-valider le modèle] [ cross-validate-model] les propriétés du module. Après l’expérimentation en cours d’exécution, puis cliquez sur le port de sortie droit du [Cross-valider le modèle] [ cross-validate-model] module, nous pouvons voir les valeurs de métrique de classification binaire pour chaque pli, en outre à la moyenne et l’écart-type de chacun. 
 
![Un modèle de Classification binaire Cross-validation](media/machine-learning-evaluate-model-performance/8.png)

La figure 8. Validation-entre un modèle de Classification binaire.

![Résultats de Validation croisée d’un classifieur binaire](media/machine-learning-evaluate-model-performance/9.png)

La figure 9. Résultats de Validation croisée d’un classifieur binaire.

##<a name="evaluating-a-multiclass-classification-model"></a>Évaluation d’un modèle de Classification Multiclass##
Dans cette expérience, nous allons utiliser le groupe de données [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") populaire qui contient des instances de 3 différents types (classes) de la plante d’iris. Il existe 4 valeurs de fonction (sépales longueur et la largeur et la longueur/largeur des pétales) pour chaque instance. Dans les expériences précédentes nous formés et testé les modèles en utilisant les mêmes jeux de données. Ici, nous allons utiliser les [Données fractionnées] [ split] module créer 2 sous-ensembles de données, former sur la première et score et l’évaluation de la deuxième. Le dataset Iris est disponible publiquement sur le [Référentiel de formation UCI Machine](http://archive.ics.uci.edu/ml/index.html)et peut être téléchargé à l’aide d’une [Importation de données] [ import-data] module.

###<a name="creating-the-experiment"></a>Création de l’expérience###
Ajouter les modules suivants à votre espace de travail dans un Studio de formation de Machine Azure :

- [Importation de données][import-data]
- [Forêt de décision multiclass][multiclass-decision-forest]
- [Données fractionnées][split]
- [Modèle de train][train-model]
- [Modèle de score][score-model]
- [Évaluation du modèle][evaluate-model]

Connecter les ports comme indiqué dans la Figure 10 ci-dessous.

Définir l’index de la colonne étiquette du [Modèle de Train] [ train-model] module 5. Le groupe de données ne possède aucune ligne d’en-tête, mais nous savons que les étiquettes de classe se trouvent dans la cinquième colonne.

Cliquez sur [Importer des données] [ import-data] module et définissez la propriété de *source de données* à *l’URL du site Web via HTTP*et l' *URL* http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

La fraction d’instances à utiliser pour la formation dans les [Données] de la valeur[ split] module (0,7 par exemple).
 
![Évaluation d’un classifieur Multiclass](media/machine-learning-evaluate-model-performance/10.png)

La figure 10. Évaluation d’un classifieur Multiclass

###<a name="inspecting-the-evaluation-results"></a>Examen des résultats de l’évaluation###
Exécuter l’essai et cliquez sur le port de sortie du [Modèle d’évaluation][evaluate-model]. Les résultats de cette évaluation sont présentés sous la forme d’une matrice de confusion, dans ce cas. La matrice affiche le chiffre réel par rapport à des instances prévues pour toutes les classes de 3.
 
![Résultats de l’évaluation Classification multiclass](media/machine-learning-evaluate-model-performance/11.png)

La figure 11. Multiclass Classification des résultats d’évaluation.

###<a name="using-cross-validation"></a>À l’aide de croisée de Validation###
Comme mentionné précédemment, vous pouvez effectuer la formation répétée, évaluation et les évaluations automatiquement à l’aide du [Cross-valider le modèle] [ cross-validate-model] module. Vous devez un [Cross-valider le modèle] , un groupe de données et un modèle non formé[ cross-validate-model] module (voir figure ci-dessous). Vous devez à nouveau la valeur de la colonne libellé du [Cross-valider le modèle] [ cross-validate-model] module (index de colonne 5 dans le cas présent). Une fois que le port du [Cross-valider le modèle]de sortie de l’expérimentation en cours d’exécution et en cliquant sur la droite[cross-validate-model], vous pouvez inspecter les valeurs métriques pour chaque pliage, ainsi que l’écart de la moyenne et standard. Les mesures affichées ici sont similaires à celles mentionnées dans le cas de classement binaire. Toutefois, notez que dans la classification multiclass, informatique les positifs/négatifs vrai et faux positifs/négatifs est effectuée en comptant sur une base par classe, car il n’existe aucune classe globalement positive ou négative. Par exemple, lors du calcul de la précision ou le rappel de la classe « Iris-setosa », il est supposé que c’est la classe positive et tous les autres comme négatifs.
 
![Cross-validation d’un modèle de Classification Multiclass](media/machine-learning-evaluate-model-performance/12.png)

La figure 12. Cross-validation d’un modèle de Classification Multiclass.


![Résultats de Validation croisée d’un modèle de classement Multiclass](media/machine-learning-evaluate-model-performance/13.png)

La figure 13. Résultats de Validation croisée d’un modèle de Classification Multiclass.


<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/
 
