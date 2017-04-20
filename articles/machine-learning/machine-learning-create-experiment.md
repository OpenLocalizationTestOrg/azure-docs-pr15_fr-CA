<properties
    pageTitle="Une expérience simple dans le Studio d’apprentissage Machine | Microsoft Azure"
    description="Ce didacticiel de formation de machine vous guide tout au long une expérience de science simple des données. Nous allons prévoir le prix d’une voiture à l’aide d’un algorithme de régression."
    keywords="Faites des essais, régression linéaire, les algorithmes d’apprentissage machine, didacticiel d’apprentissage machine, des techniques de modélisation prédictive, expérience de science de données"
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
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="garye"/>

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Didacticiel de formation de machine : créer votre première expérience de science de données dans le Studio d’apprentissage Machine Azure

Ce didacticiel de formation de machine vous guide tout au long une expérience de science simple des données. Nous allons créer un modèle de régression linéaire qui prévoit le prix d’une automobile en fonction de différentes variables telles que rendre et les spécifications techniques. Pour ce faire, nous allons utiliser Azure Machine Learning Studio afin de développer et d’effectuer une itération sur une simple prédictive analytique essayer.

*Analytique prédictive* est une sorte de science de données qui utilise les données actuelles pour prédire les futurs résultats. Pour obtenir un exemple très simple d’analytique prédictive, regarder la Science des données pour les débutants vidéo 4 : [prévoir une réponse avec un modèle simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (runtime : 7:42).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>Comment le Studio d’apprentissage Machine aide-t-il ?

Studio d’apprentissage machine rend facile à configurer une expérience à l’aide de modules de glisser-déplacer préprogrammées avec les techniques de modélisation prédictive. Pour exécuter votre expérience et prévoir une réponse, vous utiliserez le Studio d’apprentissage Machine pour *créer un modèle*, *train, le modèle*et *score et le modèle de test*.

Entrez le Studio d’apprentissage Machine : [https://studio.azureml.net](https://studio.azureml.net). Si vous êtes abonné en Studio d’apprentissage Machine avant, cliquez sur **vous connecter ici**. Dans le cas contraire, cliquez sur **Souscrire un abonnement** et le choix entre les options gratuites et payantes.

Pour obtenir des informations plus générales sur le Studio d’apprentissage Machine, consultez [Quel est le Studio d’apprentissage Machine ?](machine-learning-what-is-ml-studio.md)

## <a name="five-steps-to-create-an-experiment"></a>Cinq étapes pour créer une expérience

Dans cette machine didacticiel de formation, vous suivrez les cinq étapes de base pour générer une expérience dans une Machine de formation de Studio afin de créer, de former et de votre modèle de score :

- Créer un modèle
    - [Étape 1 : Obtenir les données]
    - [Étape 2 : Données de prétraitement]
    - [Étape 3 : Définir des fonctionnalités]
- Apprentissage du modèle
    - [Étape 4 : Choisir et appliquer un algorithme d’apprentissage]
- Score et tester le modèle
    - [Étape 5 : Prévoir de nouveaux prix pour automobile]

[Étape 1 : Obtenir les données]: #step-1-get-data
[Étape 2 : Données de prétraitement]: #step-2-preprocess-data
[Étape 3 : Définir des fonctionnalités]: #step-3-define-features
[Étape 4 : Choisir et appliquer un algorithme d’apprentissage]: #step-4-choose-and-apply-a-learning-algorithm
[Étape 5 : Prévoir de nouveaux prix pour automobile]: #step-5-predict-new-automobile-prices


## <a name="step-1-get-data"></a>Étape 1 : Obtenir les données

Il existe un certain nombre d’exemples inclus avec Studio de formation de Machine que vous pouvez choisir des groupes de données, et vous pouvez importer des données provenant de nombreuses sources. Pour cet exemple, nous allons utiliser l’exemple inclus dataset, **les données de prix Automobile (Raw)**.
Ce groupe de données inclut des entrées pour un certain nombre d’automobiles individuels, y compris des informations telles que la marque, modèle, spécifications techniques et le prix.

1. Démarrer une nouvelle expérience en cliquant sur le **+ Nouveau** en bas de la fenêtre de la Machine Learning Studio et sélectionnez **tester**puis sélectionnez **Essayer vide**. Sélectionnez le nom d’expérimentation par défaut en haut de la zone de dessin et renommez-le en quelque chose de significatif, par exemple, la **prévision de prix Automobile**.

2. À gauche de l’expérimentation canvas est une palette de groupes de données et les modules. Type **automobile** dans la zone de recherche en haut de cette palette pour trouver le groupe de données nommé **données de prix Automobile (Raw)**.

    ![Recherche de palette][screen1a]

3. Faites glisser le groupe de données pour la toile de l’expérimentation.

    ![Groupe de données][screen1]

Pour afficher un aperçu de ces données, cliquez sur le port de sortie en bas du groupe de données automobile et sélectionnez **visualiser**.

![Port de sortie de module][screen1c]

Les variables contenues dans le groupe de données apparaissent comme des colonnes, et chaque instance d’une voiture apparaît sous la forme d’une ligne. La colonne d’extrême droite (colonne 26 et intitulé « prix ») est la variable cible, nous allons tenter de prédire.

![Visualisation du groupe de données][screen1b]

Fermez la fenêtre de visualisation en cliquant sur le «**x**» dans le coin supérieur droit.

## <a name="step-2-preprocess-data"></a>Étape 2 : Données de prétraitement

Un groupe de données requiert généralement un prétraitement avant des analyser. Vous avez peut-être remarqué les valeurs manquantes présentes dans les colonnes des lignes différentes. Ces valeurs manquantes doivent être nettoyées afin que le modèle peut analyser les données correctement. Dans notre cas, nous allons supprimer toutes les lignes qui ont des valeurs manquantes. La colonne de **pertes normalisé** a également, une proportion importante des valeurs manquantes, donc nous allons exclure complètement cette colonne à partir du modèle.

> [AZURE.TIP] Les valeurs manquantes à partir des données d’entrée de nettoyage est une condition préalable à l’aide de la plupart des modules.

Tout d’abord, nous supprimerons la colonne **pertes normalisé** et puis nous allons supprimer n’importe quelle ligne qui a des données manquantes.

1. Tapez **Sélectionner des colonnes** dans la zone de recherche en haut de la palette de module pour rechercher la [Sélection des colonnes dans un groupe de données] [ select-columns] module, puis faites glisser à l’expérimentation canevas et connectez-le au port de sortie du groupe de données **les données de prix Automobile (Raw)** . Ce module permet de sélectionner les colonnes de données que vous souhaitez inclure ou exclure dans le modèle.

2. Sélectionnez [Sélectionner des colonnes dans un groupe de données] [ select-columns] module et cliquez sur **lancer le sélecteur de colonne** dans le volet **Propriétés** .

    - Sur la gauche, cliquez sur **règles**
    - Dans la zone **Commencer à**, cliquez sur **toutes les colonnes**. Ceci indique à [Sélectionner des colonnes dans un groupe de données] [ select-columns] à traverser toutes les colonnes (à l’exception de ceux que nous sommes sur le point d’exclure).
    - Dans les listes déroulantes, sélectionnez **Exclure** et **noms de colonne**, puis cliquez sur à l’intérieur de la zone de texte. Une liste de colonnes s’affiche. Sélectionnez **pertes normalisées**et il seront ajouté à la zone de texte.
    - Cliquez sur le bouton de coche (OK) pour fermer le sélecteur de colonne.

    ![Sélection des colonnes][screen3]

    Le volet des propriétés pour **Sélectionner les colonnes dans le jeu de données** indique qu’il va passer par toutes les colonnes du groupe de données, à l’exception des **pertes normalisé**.

    ![Sélectionnez les colonnes dans les propriétés du groupe de données][screen4]

    > [AZURE.TIP] Vous pouvez ajouter un commentaire à un module en double-cliquant sur le module et de saisie de texte. Vous pouvez voir en un coup de œil sur ce que fait le module dans votre expérience. Dans ce cas, double-cliquez sur la [Sélection des colonnes dans un groupe de données] [ select-columns] module et le commentaire de type « excluent pertes normalisées. »

3. Faites glisser les [Données manquantes en propre] [ clean-missing-data] module à l’expérience de canevas et le connecter à [Sélectionner des colonnes dans un groupe de données] [ select-columns] module. Dans le volet des **Propriétés** , sélectionnez **Supprimer la ligne entière** sous le **mode de nettoyage** pour nettoyer les données en supprimant les lignes qui ont des valeurs manquantes. Double-cliquez sur le module et tapez le commentaire « Supprimer les lignes de valeur manquantes ».

    ![Propriétés données manquantes en mode minimal][screen4a]

4. Exécutez l’expérimentation en cliquant sur **exécuter** dans la zone d’essai.

Lorsque l’essai est terminé, tous les modules ont une coche verte pour indiquer qu’il s’est terminée correctement. Notez également le statut de **terminé en cours d’exécution** dans le coin supérieur droit.

![Expérience de première exécution][screen5]

Tout ce que nous avons fait dans l’expérience à ce stade est Nettoyer les données. Si vous souhaitez afficher le groupe de données nettoyée, cliquez sur le port de sortie gauche des [Données manquantes en propre] [ clean-missing-data] module (« nettoyé dataset ») et sélectionnez **visualiser**. Notez que la colonne de **pertes normalisé** n’est plus incluse, et aucune valeur manquante.

Maintenant que les données sont propres, nous pouvons spécifier les fonctionnalités que nous allons utiliser dans le modèle de prévision.

## <a name="step-3-define-features"></a>Étape 3 : Définir des fonctionnalités

Dans l’apprentissage de l’ordinateur, les *fonctionnalités* sont des propriétés mesurables de quelque chose qui vous intéressent. Dans notre groupe de données, chaque ligne représente une automobile, et chaque colonne est une fonctionnalité de ce véhicule.

Recherche d’un bon jeu de fonctionnalités pour la création d’un modèle de prévision nécessite expérimentation et connaissances sur le problème que vous souhaitez résoudre. Certaines fonctionnalités sont préférables pour la prédiction de la cible que d’autres. En outre, certaines fonctionnalités ont une forte corrélation avec d’autres fonctionnalités (par exemple, ville-mpg et bus-mpg), afin qu’ils n’ajoutera pas les nouvelles informations au modèle, et ils peuvent être supprimés.

Commençons par créer un modèle qui utilise un sous-ensemble des fonctionnalités dans notre groupe de données. Vous pouvez y revenir différentes fonctionnalités, exécutez de nouveau l’expérience et sélectionnez voir si vous obtenez de meilleurs résultats. Mais pour commencer, nous allons essayer les fonctionnalités suivantes :

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Faites glisser une autre [Sélection des colonnes dans un groupe de données] [ select-columns] module à l’expérience de canevas et le connecter au port de sortie gauche des [Données manquantes en mode minimal] [ clean-missing-data] module. Double-cliquez sur le module et tapez « Fonctionnalités de sélection de prédiction ».

2. Dans le volet **Propriétés** , cliquez sur **lancer le sélecteur de colonne** .

3. Cliquez sur **règles**.

4. Sous **Commencer par** cliquez sur **pas de colonnes**et sélectionnez **Include** et les **noms de colonne** dans la ligne de filtre. Entrez la liste des noms de colonne. Il dirige le module passer uniquement les colonnes qui nous indiquer.

    > [AZURE.TIP] En exécutant cette expérience, nous avons veillé à ce que les définitions de colonne pour nos données passent du groupe de données les [Données manquantes en propre] [ clean-missing-data] module. Cela signifie que les autres modules que vous vous connectez aura également des informations à partir de l’ensemble de données.

5. Cliquez sur le bouton coche (OK).

![Sélection des colonnes][screen6]

Cela produit le jeu de données qui sera utilisé dans l’algorithme d’apprentissage dans les étapes suivantes. Ultérieurement, vous pouvez retourner et essayez à nouveau avec une autre sélection de fonctionnalités.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Étape 4 : Choisir et appliquer un algorithme d’apprentissage

Maintenant que les données sont prêtes, la construction d’un modèle de prévision se compose de formation et de test. Nous allons utiliser nos données pour former le modèle puis testez le modèle pour voir comment fermer, il est en mesure de prévoir des prix. Pour le moment, ne vous inquiétez pourquoi nous devons former et test d’un modèle.

*Classification* et *régression* sont les deux types d’apprentissage de techniques de l’ordinateur contrôlé. Classification prévoit une réponse à partir d’un ensemble défini de catégories, par exemple une couleur (rouge, bleue ou verte). Régression est utilisée pour déterminer un nombre.

Étant donné que nous voulons prévoir des prix, qui est un nombre, nous allons utiliser un modèle de régression. Pour cet exemple, nous allons former un modèle simple *régression linéaire* et à l’étape suivante, nous allons tester.

1. Nous utilisons nos données de formation et de test en le fractionnant en formation distincte et de test des jeux. Sélectionnez et faites glisser les [Données fractionnées] [ split] module à l’expérience de canevas et connectez-le à la sortie de la dernière [Sélection des colonnes dans un groupe de données] [ select-columns] module. **Fraction des lignes dans le dataset de sortie premier** la valeur 0,75. De cette façon, nous allons 75 pour cent des données permet de former le modèle et suspendre 25 pour cent pour le test.

    > [AZURE.TIP] En modifiant le paramètre de **valeur de départ aléatoire** , vous pouvez produire des échantillons aléatoires différents de formation et de test. Ce paramètre contrôle l’amorçage du Générateur de nombres pseudo-aléatoires.

2. Exécution de l’essai. Cela permet de [Sélectionner les colonnes dans le Dataset] [ select-columns] et des [Données fractionnées] [ split] modules à passer des définitions de colonne pour les modules mais nous ajouterons ensuite.  

3. Pour sélectionner l’algorithme d’apprentissage, puis développez la catégorie **Machine d’apprentissage** dans la palette de module à gauche de la zone de dessin et **Modèle d’initialisation**. Cela affiche les différentes catégories de modules qui peuvent être utilisés pour initialiser les algorithmes d’apprentissage machine.

    Pour cette étude, sélectionnez la [Régression linéaire] [ linear-regression] module sous la catégorie de **régression** (vous pouvez également trouver le module en tapant « régression linéaire » dans la zone de recherche de palette) et faites-le glisser vers la zone d’essai.

4. Rechercher et faites glisser le [Modèle de Train] [ train-model] module pour la toile de l’expérimentation. Connectez le port d’entrée gauche à la sortie de la [Régression linéaire] [ linear-regression] module. Connectez le port d’entrée droit à la sortie de données formation (port gauche) des [Données fractionnées] [ split] module.

5. Sélectionnez le [Modèle de Train] [ train-model] module, cliquez sur **lancer le sélecteur de colonne** dans le volet **Propriétés** et sélectionnez ensuite la colonne de **prix** . Il s’agit de la valeur que notre modèle est à prévoir.

    ![Sélectionnez la colonne « price »][screen7]

6. Exécution de l’essai.

Le résultat est un modèle de régression formés qui peut être utilisé pour évaluer de nouveaux exemples pour faire des prévisions.

![Application de l’algorithme d’apprentissage machine][screen8]

## <a name="step-5-predict-new-automobile-prices"></a>Étape 5 : Prévoir de nouveaux prix pour automobile

Maintenant que nous avons formé du modèle à l’aide de 75 pour cent de nos données, nous pouvons l’utiliser pour évaluer les 25 % restants des données pour voir comment nos fonctions de modèle.

1. Rechercher et faites glisser le [Modèle de Score] [ score-model] module à l’expérience de canevas et connectez le port d’entrée gauche à la sortie du [Modèle de Train] [ train-model] module. Connectez le port d’entrée droit à la sortie des données test (port de droite) des [Données fractionnées] [ split] module.  

    ![Module de modèle de score][screen8a]

2. Pour exécuter l’essai et afficher la sortie à partir du [Modèle de Score] [ score-model] module, cliquez sur le port de sortie et sélectionnez **visualiser**. La sortie affiche les valeurs prévues pour les prix et les valeurs connues à partir des données de test.  

3. Enfin, pour tester la qualité des résultats, sélectionnez et faites glisser le [Modèle d’évaluation] [ evaluate-model] module à l’expérience de canevas et connectez le port d’entrée gauche à la sortie du [Modèle de Score] [ score-model] module. (Il existe deux ports d’entrée parce que le [Modèle d’évaluation] [ evaluate-model] module peut être utilisé pour comparer deux modèles.)

4. Exécution de l’essai.

Pour afficher la sortie à partir du [Modèle d’évaluation] [ evaluate-model] module, cliquez sur le port de sortie et sélectionnez **visualiser**. Les statistiques suivantes sont affichées pour notre modèle :

- **Erreur absolue moyenne** (Affecter) : la moyenne des erreurs absolues (une *erreur* est la différence entre la valeur réelle et de la valeur prévue).
- **Erreur au carré moyen de racine** (RMSE) : la racine carrée de la moyenne des erreurs au carré des prévisions effectuées sur le groupe de données de test.
- **Erreur absolue relative**: la moyenne des erreurs absolues par rapport à la différence absolue entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
- **Erreur au carré relative**: la moyenne des carrés erreurs par rapport à la différence des carrés entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
- **Coefficient de détermination**: également connue comme la **valeur du coefficient de détermination**, il s’agit d’une mesure statistique indiquant la façon dont un modèle adapté aux données.

Pour chacun de l’erreur, des statistiques, plus petite sont préférable. Une valeur inférieure indique que les prévisions plus proche des valeurs réelles. Pour le **Coefficient de détermination**, sa valeur est proche de 1 (1.0), plus les prévisions.

![Résultats de l’évaluation][screen9]

L’essai final doit ressembler à ceci :

![Didacticiel de formation de machine : effectuer les essais de régression linéaire qui utilise des techniques de modélisation prédictive.][screen10]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez terminé un premier didacticiel de formation de machine et que vous avez votre expérience de paramétrage, vous pouvez parcourir pour essayer d’améliorer le modèle. Par exemple, vous pouvez modifier les fonctionnalités que vous utilisez dans votre prévision. Ou vous pouvez modifier les propriétés de la [Droite de régression linéaire] [ linear-regression] algorithme ou essayez un autre algorithme complètement. Vous pouvez même ajouter une machine plusieurs algorithmes à votre expérience de formation en une seule fois et comparer deux en utilisant le [Modèle d’évaluation] [ evaluate-model] module.

> [AZURE.TIP] Utilisez le bouton **Enregistrer sous** sous la zone d’essai pour copier une itération de votre expérience. Vous pouvez voir toutes les itérations de votre expérience en cliquant sur **Afficher l’historique de s’exécuter** sous la zone de dessin. Voir [Gérer expérimenter des itérations dans Azure Machine Learning Studio] [ runhistory] pour plus de détails.

[runhistory]: machine-learning-manage-experiment-iterations.md

Lorsque vous êtes satisfait de votre modèle, vous pouvez le déployer comme un service web à utiliser pour prévoir des prix automobile à l’aide de nouvelles données. Voir [déployer un service web de formation de Machine Azure] [ publish] pour plus de détails.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Pour une procédure pas à pas plus complète et détaillée des techniques de modélisation prédictive pour la création, la formation, évaluation et le déploiement d’un modèle, voir [développer une solution prédictive par apprentissage automatique de Azure][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
