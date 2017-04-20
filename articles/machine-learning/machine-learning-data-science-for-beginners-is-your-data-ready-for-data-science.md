<properties
   pageTitle="Vos données n’est prêt pour la science de données ? L’évaluation des données | Microsoft Azure"
   description="Découvrez les 4 critères de données être prêt pour la science des données. Science de données pour les débutants vidéo 2 a des exemples concrets pour aider à l’évaluation des données de base."
   keywords="les données pertinentes, évaluer des données de, préparer les données, les critères de données, les données prêtes"
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


# <a name="is-your-data-ready-for-data-science"></a>Vos données n’est prêt pour la science de données ?

## <a name="video-2-data-science-for-beginners-series"></a>Vidéo 2 : Science de données de la série des débutants

Apprenez à évaluer vos données pour vous assurer qu’il répond aux critères de base pour être prêt pour la science des données.

Pour obtenir le meilleur parti de la série, vous pouvez surveiller tous les. [Accédez à la liste des vidéos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-is-your-data-ready-for-data-science]

## <a name="other-videos-in-this-series"></a>Autres vidéos dans cette série de

*Science de données pour les débutants* est une brève introduction aux sciences des données dans cinq courtes vidéos.

  * Vidéo 1 : [Les réponses de la science de données 5 questions](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 s)*
  * Vidéo 2 : Donnée de votre prêt pour les sciences de données ?
  * Vidéo 3 : [Poser une question, vous pouvez répondre avec des données](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
  * Vidéo 4 : [Prévoir une réponse avec un modèle simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 s 42 min)*
  * Vidéo 5 : [Copie de travail d’autres personnes pour faire la science de données](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcription : Donnée de votre prêt pour les sciences de données ?

Bienvenue dans « Est vos données prêtes pour les sciences de données ? » la deuxième vidéo dans les séries *Sciences de données pour les débutants*.  

Science de données peut vous donner les réponses souhaitées, vous devez lui donner certaines matières premières de haute qualité pour travailler avec. À l’instar d’une pizza, plus les ingrédients que vous démarrez avec, plus le produit final.

## <a name="criteria-for-data"></a>Critères pour les données

Dans le cas de science de données, il y a certains ingrédients dont nous avons besoin pour rassembler.

Nous avons besoin des données :

  * Pertinentes
  * Connecté
  * Précise
  * Suffisants pour travailler avec

## <a name="is-your-data-relevant"></a>Concerne vos données ?

Afin que l’ingrédient premier - nous avons besoin de données qui sont pertinentes.

![Les données pertinentes et les données inappropriées - évaluation des données](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-relevant-and-irrelevant-data.png)

Examinez le tableau sur la gauche. Nous remplies de sept personnes à l’extérieur des barres de Boston, mesurée de leur niveau d’ALCOOLÉMIE, la moyenne de la batte Red Sox dans leur dernière partie et le prix du lait dans le magasin le plus proche de la commodité.

Il s’agit de toutes les données parfaitement légitimes. Sa seule défaillance est qu’il n’est pas pertinente. Il n’y a pas de relation évidente entre ces nombres. Si je vous a donné le prix actuel de lait et de la moyenne de la batte Red Sox, il n’existe aucun moyen de vous capables de deviner la teneur en alcool mon sang.

Examinez maintenant le tableau sur la droite. Cette fois, nous avons mesuré de chaque personne masse du corps et le nombre de boissons qui ont été pris en compte.  Les nombres de chaque ligne sont maintenant pertinentes à l’autre. Si j’avais mon corps masse et le nombre de Margaritas, j’ai dû, vous pouvez faire une estimation à mon sang alcool contenu.

## <a name="do-you-have-connected-data"></a>Vous avez connecté des données ?

La substance suivante est connecté.

![Les données et les données déconnectées - critères de données, données prêtes connectées](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-connected-vs-disconnected-data.png)

Voici certaines données sur la qualité des hamburgers : grille de température, le poids de GALETTES et contrôle d’accès dans le locaux de denrées alimentaires magazine. Mais, notez les espaces vides dans le tableau sur la gauche.

La plupart des jeux de données, certaines valeurs sont manquantes. Il est courant d’avoir des trous comme suit, et il existe des moyens de les contourner. Mais s’il existe trop manquante, vos données commencent à ressembler au fromage de Suisse.

Si vous examinez le tableau sur la gauche, est donc de la quantité de données manquant, il est difficile de trouver n’importe quel type de relation entre le poids de température et de GALETTES de GRIL. Il s’agit d’un exemple de données déconnectées.

Le tableau sur la droite, cependant, est plein et terminé - un exemple de données connectées.

## <a name="is-your-data-accurate"></a>Est la précision de vos données ?

La substance suivante, que nous avons besoin est la précision. Voici quatre cibles que nous souhaiterions atteint avec des flèches.

![Données précises et des données inexactes - critères de données](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-inaccurate-vs-accurate-data.png)

Examinez la cible dans le coin supérieur droit. Nous avons un regroupement étroit droite autour des deux cercles concentriques. Qui, bien sûr, est précis. Curieusement, dans la langue de la science des données, nos performances sur la droite de la cible en dessous est également considéré comme précis.

Si vous devez mapper le centre de ces flèches, vous verrez qu’il est très proche de la deux cercles concentriques. Les flèches sont réparties tout autour de la cible, ils sont considérés comme imprécises, mais qu’elles sont centrées sur les deux cercles concentriques, elles sont considérées comme exactes.

Examinez maintenant la cible supérieur gauche. Ici notre flèches d’accès très proches, un regroupement serré. Elles sont précises, mais elles sont incorrectes parce que le centre est la façon de désactiver les deux cercles concentriques. Et, bien sûr, les flèches dans la cible inférieur gauche sont inexactes et imprécise. Cette archer doit plus pratique.

## <a name="do-you-have-enough-data-to-work-with"></a>Vous disposez de suffisamment de données pour travailler avec ?

Enfin, l’ingrédient #4 - nous devons avoir suffisamment de données.

![Vous disposez de suffisamment de données pour l’analyse ? Évaluation des données](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-barely-enough-data.png)

Pensez à chaque point de données de la table comme étant un trait de pinceau dans un dessin. Si vous avez uniquement certains d'entre eux, la peinture peut être assez floue, c’est difficile de dire qu’il est.

Si vous ajoutez quelques coups de pinceau plus, votre peinture commence ensuite à obtenir un peu plus nette.

Lorsque vous avez à peine suffisante tracés, vous pouvez voir juste assez pour prendre certaines décisions de large. Est un emplacement que j’aime visiter ? Il recherche lumineux, qui ressemble à l’eau potable – Oui, qui est où je vais en vacances.

Lorsque vous ajoutez davantage de données, le tableau devient plus clair et vous pouvez prendre des décisions plus détaillées. Maintenant je peux examiner les trois hôtels sur la banque de gauche. Vous savez, j’aime vraiment les caractéristiques de l’architecture de l’objet au premier plan. Je vais rester, au troisième étage.

Avec des données pertinentes, connectés, précis et suffisamment, nous faire tous les ingrédients que nous devons faire la science de certaines données de haute qualité.

Veillez à vérifier les autres vidéos quatre en *Science de données pour les débutants* de la formation de Machine Microsoft Azure.




## <a name="next-steps"></a>Étapes suivantes

  * [Essayez une première expérience de science de données avec Studio de formation de Machine](machine-learning-create-experiment.md)
  * [Obtenir une introduction à la formation de Machine sur Microsoft Azure](machine-learning-what-is-machine-learning.md)
