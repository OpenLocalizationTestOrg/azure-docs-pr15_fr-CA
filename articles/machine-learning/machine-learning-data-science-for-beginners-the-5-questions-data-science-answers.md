<properties
   pageTitle="Les questions science de 5 données - Science de données pour les débutants | Microsoft Azure"
   description="Obtenir une brève introduction à la science des données à partir de données scientifiques pour les débutants, cinq courtes vidéos qui commencent par les réponses de Science de données 5 Questions."
   keywords="faire la science de données, les débutants de données science, sciences de données pour les débutants, les types de questions, les questions science de données, les données vidéo de science"
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

# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Science de données pour les débutants vidéo 1 : les 5 questions réponses science de données

Obtenir une brève introduction aux sciences de données à partir de la *Science des données pour les débutants* dans cinq courtes vidéos à partir d’un chercheur de données supérieur. Ces vidéos sont fondamentales mais utile, que vous soyez à faire science de données ou vous travaillez avec des scientifiques de données.

Cette première vidéo est sur les types de questions science de données capable de répondre. Pour obtenir le meilleur parti de la série, vous pouvez surveiller tous les. [Accédez à la liste des vidéos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-the-5-questions-data-science-answers]

## <a name="other-videos-in-this-series"></a>Autres vidéos dans cette série de

*Science de données pour les débutants* est une brève introduction aux données science prend environ 25 minutes au total. Découvrez les quatre autres vidéos :

  * Vidéo 1 : Les 5 questions réponses science de données
  * Vidéo 2 : [vos données n’est prêt pour la science données ?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
  * Vidéo 3 : [Poser une question, vous pouvez répondre avec des données](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
  * Vidéo 4 : [Prévoir une réponse avec un modèle simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 s 42 min)*
  * Vidéo 5 : [Copie de travail d’autres personnes pour faire la science de données](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transcription : Les 5 questions réponses science de données

Salut ! Bienvenue dans la série vidéo *Science de données pour les débutants*.

Science de données peut être intimidant, je vais présenter ici les notions de base des équations ou un jargon de programmation informatique.

Dans cette première vidéo, nous allons parler « les 5 questions réponses de science données. »

Science de données utilise des numéros et noms (également connu sous le nom des catégories ou des étiquettes) pour prédire les réponses aux questions.

Il peut vous surprendre, mais *il y a seulement cinq questions que réponses science de données*:

  * Soit ce A B ?
  * C’est étrange ?
  * Combien – ou – combien ?
  * Comment est organisé ?
  * Que dois-je faire ensuite ?

  Chacun d’eux de ces questions est reçu par une famille distincte de méthodes d’apprentissage machine, appelés algorithmes.


Il est utile de réfléchir à un algorithme à une recette et vos données en tant qu’ingrédients. Un algorithme indique comment combiner et associer les données afin d’obtenir une réponse. Les ordinateurs sont comme un mélangeur. Ils effectuent la plus grande partie du travail de l’algorithme pour vous, et ce assez rapidement.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Question 1 : Soit la ce A B ? utilise des algorithmes de classification

Commençons par la question : est ce A ou le B ?

![Les algorithmes de classement : soit la ce A B ?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-classification-algorithms.png)

Cette famille d’algorithmes est appelée classement de deux classes.

Il est utile pour toute question qui a deux réponses possibles.

Par exemple :

  * Ce PNEU échoue dans les miles 1000 suivant : Oui ou non ?
  * Qui apporte davantage de clients : un coupon de 5 euros ou une remise de 25 % ?

Cette question peut également être rephrased pour inclure plus de deux options : est ce A B ou C ou D, etc.. ?  On parle de classification multiclass et ses utile lorsque vous en avez plusieurs, ou plusieurs milliers : réponses possibles. Multiclass classification choisit celle la plus probable.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Question 2 : C’est étrange ? utilise des algorithmes de détection des anomalies

La prochaine question scientifique des données peut répondre est : est cette étrange ? Cette question est traitée par une famille d’algorithmes appelée détection des anomalies.

![Les algorithmes de détection des anomalies : est cette étrange ?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-anomaly-detection-algorithms.png)


Si vous avez une carte de crédit, vous avez déjà bénéficié de détection des anomalies. Votre société de carte de crédit analyse vos habitudes d’achat, afin qu’ils vous signaler une fraude possible. Les frais sont « bizarres » peuvent être un achat à un magasin où vous n’effectuez normalement des achats ou l’achat d’un article anormalement inutile.

Cette question peut être utile dans un grand nombre de façons. Par exemple :

  * Si vous avez une voiture avec manomètres, vous pouvez souhaiter savoir : est ce manomètre lecture normale ?
  * Si vous êtes surveillance internet, vous avez besoin : est ce message à partir d’internet classique ?

Détection d’anomalie indicateurs des événements inattendus ou inhabituelles ou des comportements. Il donne des indices où rechercher les problèmes.



## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Question 3 : Quelle quantité ? et la manière dont de nombreux ? utilise des algorithmes de régression

Apprentissage automatique permet également de prédire la réponse à une procédure bien ? et la manière dont de nombreux ? La famille d’algorithmes qui répond à cette question est appelée régression.

![Algorithmes de régression : combien ? et la manière dont de nombreux ?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-regression-algorithms.png)


Algorithmes de régression rendre les prévisions numériques, tels que :

  * Quel sera la température mardi prochain ?  
  * Quels seront mes ventes du quatrième trimestre ?

Elles permettent de répondre à toute question qui demande un nombre.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Question 4 : Comment cette organisation ? utilise des algorithmes de gestion de clusters

Les deux dernières questions sont maintenant un peu plus avancé.

Parfois vous souhaitez comprendre la structure d’un ensemble de données - comment cette organisation ? Pour cette question, vous n’avez pas les exemples que vous connaissez déjà les résultats pour.

Il existe de nombreuses façons vous taquinent la structure de données. La mise en clusters est une approche. Il sépare les données en naturel « rassemble, » pour simplifier l’interprétation. Avec le clustering, il n’est pas un droit de réponse.

![Algorithmes de groupement : comment cette organisation ?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-clustering-algorithms.png)

Des exemples courants de clustering de questions sont :

  * Les visionneuses comme les mêmes types de films ?
  * Les modèles d’imprimante échouent de la même façon ?

En comprenant comment les données sont organisées, vous pouvez mieux comprendre - et prévoir - comportements et événements.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Question 5 : Que dois-je faire maintenant ? utilise des algorithmes de formation de renforcement

La dernière question, que dois-je faire maintenant ? – utilise une famille d’algorithmes appelé apprentissage de renforcement.

Formation de renforcement a été inspirée par comment le cerveau de l’homme et de rats répond à la peine de mort et de primes. Ces algorithmes apprendre à partir des résultats et décident de l’action suivante.

Formation de renforcement est généralement une bonne solution pour les systèmes automatisés qui doivent effectuer beaucoup de petites décisions sans aide humaine.

![Renforcement des algorithmes d’apprentissage : que dois-je faire ensuite ?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-reinforcement-learning-algorithms.png)

Elle répond à des questions sont toujours sur les mesures devraient être prises - généralement par un ordinateur ou d’un robot. Des exemples sont :

  * Si je suis un système de contrôle de température d’une maison : réglage de la température ou le laisser où il est ?  
  * Si je suis une voiture de pilotage automatique : À un feu jaune, frein ou accélérer ?  
  * Pour un aspirateur robot : conserver la fonction de ramasse-miettes, ou revenir en arrière pour le poste de charge ?

Algorithmes d’apprentissage de renfort collectent des données qu’ils aller, apprentissage à partir d’essais et d’erreurs.

C’est elle - science de la 5 questions données peut répondre.



## <a name="next-steps"></a>Étapes suivantes

  * [Essayez une première expérience de science de données avec Studio de formation de Machine](machine-learning-create-experiment.md)
  * [Obtenir une introduction à la formation de Machine sur Microsoft Azure](machine-learning-what-is-machine-learning.md)
