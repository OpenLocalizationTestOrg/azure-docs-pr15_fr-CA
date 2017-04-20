<properties
   pageTitle="Poser une question que vous pouvez répondre avec des données - formuler des questions | Microsoft Azure"
   description="Découvrez comment formuler une question des données de sciences en sciences de données pour les débutants 3 vidéo. Inclut une comparaison des questions de classification et de régression."
   keywords="les questions science données, formuler des questions, les questions de la régression, les questions de classification, nettes de question"
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

# <a name="ask-a-question-you-can-answer-with-data"></a>Poser une question, que vous pouvez répondre avec des données

## <a name="video-3-data-science-for-beginners-series"></a>Vidéo 3 : Science de données série de débutants

Découvrez comment formuler une question des données de sciences en sciences de données pour les débutants 3 vidéo. Cette vidéo propose une comparaison des questions pour les algorithmes de classification et de régression.

Pour obtenir le meilleur parti de la série, vous pouvez surveiller tous les. [Accédez à la liste des vidéos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## <a name="other-videos-in-this-series"></a>Autres vidéos dans cette série de

*Science de données pour les débutants* est une brève introduction aux sciences des données dans cinq courtes vidéos.

  * Vidéo 1 : [Les réponses de la science de données 5 questions](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 s)*
  * Vidéo 2 : [vos données n’est prêt pour la science données ?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
  * Vidéo 3 : Poser une question que vous pouvez répondre avec des données
  * Vidéo 4 : [Prévoir une réponse avec un modèle simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 s 42 min)*
  * Vidéo 5 : [Copie de travail d’autres personnes pour faire la science de données](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transcription : Poser une question que vous pouvez répondre avec des données

Bienvenue dans la troisième vidéo de la série « Sciences de données pour les débutants ».  

Dans celui-ci, vous obtiendrez des conseils pour la formulation d’une question, que vous pouvez répondre avec des données.

Vous pouvez mieux exploiter cette vidéo, si vous regardez tout d’abord les deux vidéos précédentes dans cette série : « la science de données 5 questions peut répondre » et « Est vos données sont prêtes pour la science de données ? »

## <a name="ask-a-sharp-question"></a>Poser une question sharp

Nous avons parlé de la science des données est le processus d’utilisation des noms (également appelées catégories ou étiquettes) et les numéros de prédire une réponse à une question. Mais il ne peut pas être n’importe quelle question ; Il doit être un *question tranchant.*

Une question de vagues ne doit répondre avec un nom ou un numéro. Doit être un point aigu.

Imaginez que vous avez trouvé une lampe magique avec un genie qui répondra bien toute question à que vous poser. Mais il est un genie partir et il allez essayer à sa réponse vague et prête à confusion car il en sortir. Vous souhaitez lui ajouter vers le bas avec une question donc étanche à l’air qu’il ne peut pas aider mais vous dire ce que vous voulez savoir.

Si vous n’avez pour poser une question de vagues, comme « Que va-t-il se passer avec mon stock ? », le genie peut répondre, « le prix change ». C’est une réponse honnête, mais il n’est pas très utile.

Mais si vous deviez poser une question tranchant, comme « Quel prix de vente de mon stock sera la semaine prochaine ? », le genie ne peut pas vous aider mais vous donnent un spécifique de répondre et prédire un prix de vente.

## <a name="examples-of-your-answer-target-data"></a>Exemples de la réponse : cibler les données

Une fois que vous pouvez formuler votre question, vérifiez si vous avez des exemples de la réponse dans vos données.

Si votre question est « Quel prix de vente de mon stock sera la semaine prochaine ? » Nous avons ensuite pour vous assurer que nos données incluent la cotation boursière.

Si votre question est « la voiture de mon parc va échouer tout d’abord ? » Nous avons ensuite s’assurer que nos données incluent des informations sur les échecs précédents.

![Données cibles - exemples de votre réponse. Formuler une question des données de sciences.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

Ces exemples de réponses sont appelés une cible. Une cible est ce que nous essayons de prévoir sur les points de données futures, qu’il s’agisse d’une catégorie ou un nombre.

Si vous n’avez pas de données cible, vous devez en obtenir. Il se peut que vous ne pourrez pas répondre à votre question sans lui.

## <a name="reformulate-your-question"></a>Reformulez votre question

Parfois, vous pouvez reformuler votre question pour obtenir une réponse plus utile.

La question « Est ce un point de données ou de B ? » prévoit la catégorie (nom ou étiquette) d’un élément. Pour le faire, nous utilisons un *algorithme de classification*.

La question « Combien ? » ou « Nombre » ? prévoit un montant. Pour le faire, nous utilisons un *algorithme de régression*.

Pour voir comment nous pouvons transformer ces, penchons-nous sur la question, « le reportage est la plus intéressante pour ce lecteur ? » Il vous demande d’une prévision d’un choix unique parmi les nombreuses possibilités - en d’autres termes « Est ce A B ou C ou D ? » - et utilisez un algorithme de classification.

Mais, cette question peut être plus facile de répondre si vous reformuler en tant que « comment intéressant est chaque récit sur cette liste pour ce lecteur ? » Maintenant vous pouvez donner à chaque article un score numérique, et il est facile d’identifier l’article au score le plus élevé. Il s’agit d’un reformuler la question classification dans une question de régression ou combien ?

![Reformulez votre question. Question de classement et la question de la régression.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

Comment vous posez qu'une question est un indice sur un algorithme qui peut vous donner une réponse.

Vous constaterez que certaines familles d’algorithmes - comme celles de notre exemple de scénario de news - sont étroitement liées. Vous pouvez reformulez votre question pour utiliser l’algorithme qui vous donne la réponse la plus utile.

Mais, plus important encore, poser cette question tranchant - la question que vous pouvez répondre avec des données. Et vérifiez que vous disposez de données droite à répondre.

Nous avons parlé de quelques principes simples pour poser une question, que vous pouvez répondre avec des données.

Veillez à vérifier les autres vidéos dans « Science de données pour débutants » de Microsoft Azure Machine Learning.


## <a name="next-steps"></a>Étapes suivantes

  * [Essayez une première expérience de science de données avec Studio de formation de Machine](machine-learning-create-experiment.md)
  * [Obtenir une introduction à la formation de Machine sur Microsoft Azure](machine-learning-what-is-machine-learning.md)
