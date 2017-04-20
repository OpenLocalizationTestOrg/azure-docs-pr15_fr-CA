<properties
   pageTitle="Prévoir une réponse avec un simple modèle - modèle de régression | Microsoft Azure"
   description="Comment créer un modèle de régression simple afin de prévoir un prix dans la Science des données pour les débutants vidéo 4. Inclut une régression linéaire avec les données de la cible."                                  
   keywords="Créez un modèle, modèle simple, prévision de prix, un modèle de régression simple"
   services="machine-learning"
   documentationCenter="na"
   authors="cjgronlund"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="cgronlun;garye"/>

# <a name="predict-an-answer-with-a-simple-model"></a>Prévoir une réponse avec un modèle simple

## <a name="video-4-data-science-for-beginners-series"></a>Vidéo 4 : Science de données pour les séries débutants

Apprenez à créer un modèle de régression simple afin de prévoir le prix d’un losange dans la Science des données pour les débutants vidéo 4. Nous allons dessiner un modèle de régression avec les données de la cible.

Pour obtenir le meilleur parti de la série, vous pouvez surveiller tous les. [Accédez à la liste des vidéos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-predict-an-answer-with-a-simple-model]

## <a name="other-videos-in-this-series"></a>Autres vidéos dans cette série de

*Science de données pour les débutants* est une brève introduction aux sciences des données dans cinq courtes vidéos.

  * Vidéo 1 : [Les réponses de la science de données 5 questions](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 s)*
  * Vidéo 2 : [vos données n’est prêt pour la science données ?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
  * Vidéo 3 : [Poser une question, vous pouvez répondre avec des données](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
  * Vidéo 4 : Prévoir une réponse avec un modèle simple
  * Vidéo 5 : [Copie de travail d’autres personnes pour faire la science de données](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transcription : Prévoir une réponse avec un modèle simple

Bienvenue dans la quatrième vidéo dans les « données scientifiques pour débutants » série. Dans celui-ci, nous construire un modèle simple et établir une prévision.

Un *modèle* est un récit simplifié sur nos données. Je vais vous montrer ce que je veux dire.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Collect pertinente, précise, connecté, suffisamment de données

Imaginons que je veuille usine pour un losange. J’ai un anneau qui appartenait à ma grand-mère avec un paramètre pour un losange 1,35 carats, et je souhaite avoir une idée de quel sera le coût. Prendre un bloc-notes et du stylet dans le magasin de bijoux et écrire vers le bas le prix de tous les losanges dans le cas d’et combien ils Peser dans nombre de carats. En commençant par le premier losange - nombre de 1,01 carats son et $7,366.

Maintenant je passez en revue et cela pour tous les autres carreaux dans la banque.

![Colonnes de données de diamant](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Notez que notre liste a deux colonnes. Chaque colonne a un attribut différent - poids dans le nombre de carats et prix - et chaque ligne est un point de données qui représente un losange unique.

En fait, nous avons créé un petit ensemble de données ici - une table. Notez qu’il répond à nos critères de qualité :

* Les données sont **pertinentes** - poids est sans aucun doute lié au prix
* C’est **exact** - nous à double contrôle les prix qui nous écrire
* Il est **connecté** - il n’existe aucun espace vide dans une de ces colonnes
* Et, comme nous allons le voir, il est **suffisamment** de données pour répondre à notre question

## <a name="ask-a-sharp-question"></a>Poser une question sharp

Maintenant nous allons poser notre question d’une manière forte : « combien est le coût pour acheter un losange carats 1,35 ? »

Notre liste n’a pas un losange 1,35 carats, et nous devrons donc pour le reste des données permet d’obtenir une réponse à la question.

## <a name="plot-the-existing-data"></a>Tracer les données existantes

La première chose que nous allons faire est de dessiner une ligne horizontale du numéro, appelée un axe, pour les poids de graphique. La gamme des poids est de 0 à 2, commençons par dessiner une ligne qui traite de plage et placer les graduations de chaque moitié carats.

Ensuite, nous allons dessiner un axe vertical pour enregistrer le prix et le connecter à l’axe horizontal de poids. Il s’agit dans les unités de dollars. Nous avons un ensemble d’axes de coordonnées.

![Axes de poids et de prix](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Nous allons prendre ces données maintenant et le transformer en un *tracé en nuages de points*. Il s’agit d’un excellent moyen pour visualiser des ensembles de données numériques.

Le premier point de données, nous se rendre compte une ligne verticale au nombre de carats 1,01. Ensuite, nous se rendre compte une ligne horizontale à $7,366. Dans le cas où ils remplissent, nous dessinons un point. Il s’agit de notre premier losange.

Maintenant, nous passer par chaque losange sur cette liste et faire la même chose. Lorsque nous sommes via, c’est ce que nous obtenons : un certain nombre de points, un pour chaque losange.

![Nuage de points](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Dessiner le modèle à travers les points de données

Si vous examinez les points et les squint, la collection ressemble maintenant à une ligne floue fat. Nous pouvons prendre notre marque et dessiner une ligne droite.

En dessinant une ligne, nous avons créé un *modèle*. Considérez ceci comme en tenant le monde réel et de rendre une version dessin animé simpliste. L’animation est incorrecte, la ligne ne passe pas tous les points de données. Mais, il s’agit d’une simplification utile.

![Ligne de régression linéaire](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Le fait que tous les points ne passent pas exactement par la ligne est OK. Scientifiques de données expliquent cela en disant que le modèle - ce qui est de la ligne - est et ensuite chaque point certains *bruit* ou la *variation* associé. Le parfait de la relation sous-jacente, et il y a aussi le monde PIERREUSES, réel qui ajoute du bruit et l’incertitude.

Parce que nous essayons de répondre à la question *combien ?* il s’agit d’une *régression*. Et bien, car nous utilisons une ligne droite, il s’agit d’une *régression linéaire*.

## <a name="use-the-model-to-find-the-answer"></a>Utiliser le modèle pour trouver la réponse

Maintenant nous avons un modèle et nous demander à notre question : Combien coûtera un losange carats 1,35 ?

Pour répondre à votre question, nous se rendre compte du nombre de carats 1,35 et dessiner une ligne verticale. Où il croise la ligne modèle, nous se rendre compte d’une ligne horizontale à l’axe du dollar. Il accède à droite à 10 000. BRAS ! C’est la réponse : un losange 1,35 carat coûte environ 10 000 dollars.

![Trouver la réponse sur le modèle](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Créer un intervalle de confiance

Il est naturel de vous demander quel degré de précision est de cette prévision. Il est utile de savoir si le losange 1,35 carats est très proche de 10 000 $, ou bien supérieure ou inférieure. Pour cela à la figure, nous allons dessiner la ligne de régression qui inclut la plupart des points d’une enveloppe. Cette enveloppe est appelée notre *intervalle de confiance*: nous sommes très confiants que les prix relèvent de cette enveloppe, car dans le passé plus de leur avoir. Nous pouvons dessiner deux lignes horizontales plus à partir de l’intersection de la ligne de carats 1,35 le haut et le bas de cette enveloppe.

![Intervalle de confiance](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Maintenant nous pouvons dire quelque chose sur l’intervalle de confiance à notre : nous pouvons dire avec certitude que le prix d’un losange carats 1,35 est environ $ 10 000 - mais il peut être aussi bas que 8 000 $, et il peut être aussi élevée que 12 000 dollars.

## <a name="were-done-with-no-math-or-computers"></a>Nous avons terminé, sans mathématiques ou les ordinateurs

Nous avons réussi les scientifiques des données de rémunération à faire, et nous l’avons fait simplement en dessinant :

* Nous avons posé une question que nous pourrions répondre avec des données
* Nous avons créé un *modèle* à l’aide de la *droite de régression linéaire*
* Nous avons effectué une *prévision*, avec un *intervalle de confiance*

Et nous n’avez pas utilisé les mathématiques ou les ordinateurs pour cela.

Maintenant, si nous avions eu plus d’informations, comme...

* la découpe du losange
* variations de couleurs (comment le losange est près d’être blanc)
* le nombre d’inclusions dans le losange

.. .puis nous aurait eu plus de colonnes. Dans ce cas, les mathématiques devient utile. Si vous avez plus de deux colonnes, il est difficile de dessiner les points sur le papier. Math permet de fonctionnent très bien de cette ligne ou ce plan pour vos données.

En outre, si au lieu de quelques-unes des diamants, nous avait deux mille ou deux millions, puis vous pouvez effectuer cette tâche beaucoup plus rapidement avec un ordinateur.

Aujourd'hui, nous avons expliqué comment effectuer une régression linéaire, et nous avons une prévision à l’aide de données.

Veillez à vérifier les autres vidéos dans « Science de données pour débutants » de Microsoft Azure Machine Learning.



## <a name="next-steps"></a>Étapes suivantes

  * [Essayez une première expérience de science de données avec Studio de formation de Machine](machine-learning-create-experiment.md)
  * [Obtenir une introduction à la formation de Machine sur Microsoft Azure](machine-learning-what-is-machine-learning.md)
