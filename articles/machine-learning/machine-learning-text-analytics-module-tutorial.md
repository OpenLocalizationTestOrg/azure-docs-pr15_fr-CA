<properties
    pageTitle="Créer des modèles d’analytique de texte dans un Studio de formation de Machine Azure | Microsoft Azure"
    description="Comment créer des modèles d’analytique de texte dans un Studio de formation de Machine Azure à l’aide de modules de texte de prétraitement, de N-grammes ou de fonction de hachage"
    services="machine-learning"
    documentationCenter=""
    authors="rastala"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="roastala" />


#<a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Créer des modèles d’analytique de texte dans un Studio de formation de Machine Azure

Vous pouvez utiliser la formation de Machine Azure pour générer et mettre des modèles de texte analytique. Ces modèles peuvent vous aider à résoudre, par exemple, problèmes d’analyse classification ou sentiment de document.

Dans une expérience analytique du texte, vous devez en général :

 1. Nettoyer et prétraiter le groupe de données de texte
 2. Extraire des vecteurs de fonction numérique de texte traitement préalable
 3. Modèle de classification ou de régression du train
 4. Score et valider le modèle
 5. Déployer le modèle à la production

Dans ce didacticiel, vous Découvrez ces étapes que nous guider dans un modèle d’analyse sentiment à l’aide du groupe de données critiques d’Amazon (reportez-vous à cet article recherche « Biographies, films indiens, boîtes de bras et mélangeurs : Adaptation de domaine pour la Classification de Sentiment » par John Blitzer, Mark Dredze et les Fernando Pereira ; Association de calcul linguistique (ACL), 2007.) Ce groupe de données est constitué des notes de révision (1-2 ou 4 et 5) et un texte de forme libre. L’objectif est de prédire le score de révision : faible (1 - 2) ou haute (4-5).

Vous pouvez trouver des expériences visées dans le présent didacticiel dans la bibliothèque d’Intelligence de Cortana :

[Prévoir critiques] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Prévoir critiques - prédictive expérience] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Étape 1 : Nettoyer et prétraiter le groupe de données de texte

Nous commençons l’expérimentation en divisant les scores de révision en compartiments par catégorie de bas et hautes de formuler le problème de la classification de la classe de deux. Nous utilisons (modifier les métadonnées) (https://msdn.microsoft.com/library/azure/dn905986.aspx) et les modules de [groupe valeurs catégoriques] (https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Créer des étiquettes](./media/machine-learning-text-analytics-module-tutorial/create-label.png)

Ensuite, nous nettoyer le texte à l’aide du module [prétraiter texte] (https://msdn.microsoft.com/library/azure/mt762915.aspx). Le nettoyage permet de réduire le bruit dans le dataset, vous aident à trouvez des fonctionnalités les plus importantes et améliorez la précision du modèle final. Nous supprimons les mots vides - mots courants tels que « le » ou « a - » et nombres, caractères spéciaux, caractères dupliqués, les adresses e-mail et URL. Nous également convertir le texte en minuscules, et lemmatize les mots et de détecter les limites de phrase puis indiqués par « rouges. » symbole de texte prétraitée.

![Prétraiter le texte](./media/machine-learning-text-analytics-module-tutorial/preprocess-text.png)

Que se passe-t-il si vous souhaitez utiliser une liste de mots vides personnalisée ? Vous pouvez passer dans en tant qu’entrée facultative. Vous pouvez également utiliser expression régulière personnalisée C# syntaxe pour remplacer des sous-chaînes et supprimer des mots en partie du discours : noms, de verbes ou d’adjectifs.

Une fois le prétraitement terminé, nous fractionner les données en train et tester les jeux.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Étape 2 : Extraire des vecteurs de fonction numérique de texte traitement préalable

Pour créer un modèle avec des données texte, vous devrez généralement convertir le texte en forme libre dans des vecteurs de fonction numérique. Dans cet exemple, nous utilisons [extraire les caractéristiques de la N-Gram texte] module (https://msdn.microsoft.com/library/azure/mt762916.aspx) pour transformer les données texte dans ce format. Ce module prend une colonne d’éléments séparés par des espaces blancs et calcule un dictionnaire de mots ou de N-grammes de mots, qui apparaissent dans votre groupe de données. Ensuite, il compte compte combien de fois chaque mot et N-gram, apparaît dans chaque enregistrement et crée des vecteurs de fonction à partir de celles. Dans ce didacticiel, nous la valeur taille de N-gram 2, afin que notre vecteurs de fonction incluent des mots et combinaisons de deux mots suivants.

![Extraire de N-grammes](./media/machine-learning-text-analytics-module-tutorial/extract-ngrams.png)

Nous appliquons TF * compte d’IDF (terme fréquence fréquence Inverse de Document) à N-gram de pondération. Cette approche ajoute le poids des mots qui apparaissent fréquemment dans un seul enregistrement, mais qui sont rares dans l’ensemble du dataset. Autres options incluent binaire, TF et pesée du graphique.

Ces fonctionnalités de texte ont souvent une dimensionnalité élevée. Par exemple, si votre corpus a 100 000 mots uniques, votre espace de fonctionnalité aurait dimensions 100 000 ou plus si N-grammes sont utilisés. Le module de fonctionnalités de N-Gram extraire vous offre un ensemble d’options pour réduire la dimensionnalité. Vous pouvez choisir d’exclure les mots court ou long, ou trop rares ou trop fréquent d’avoir une valeur prédictive significative. Dans ce didacticiel, nous exclure de N-grammes qui apparaissent dans les enregistrements de moins de 5 ou dans plus de 80 % des enregistrements.

Également, vous pouvez utiliser la sélection de fonctions pour sélectionner uniquement les fonctionnalités qui sont plus corrélée avec votre cible de prévision. Sélection des fonctionnalités Khi-deux nous permet de sélectionner les fonctionnalités de 1000. Vous pouvez afficher le vocabulaire de N-grammes ou de mots sélectionnés en cliquant sur le résultat approprié du module de N-grammes d’extrait.

En guise d’alternative à l’utilisation des fonctionnalités de N-Gram extraire, vous pouvez utiliser le module de fonction de hachage. Notez que ce [fonction de hachage] (https://msdn.microsoft.com/library/azure/dn906018.aspx) ne possède pas les fonctionnalités de sélection de fonctionnalité de build ou TF * IDF pesée.

## <a name="step-3-train-classification-or-regression-model"></a>Étape 3 : Modèle de classification ou de régression du Train

Désormais, le texte a été transformé à des colonnes numériques. Le groupe de données contient toujours les colonnes de type chaîne dans les étapes précédentes, nous permet de sélectionner les colonnes dans le Dataset pour les exclure.

Nous utilisons ensuite [classe deux régression logistique] (https://msdn.microsoft.com/library/azure/dn905994.aspx) afin de prédire notre cible : révision élevée ou faible score. À ce stade, le problème d’analytique de texte a été transformé en un problème de classification standard. Vous pouvez utiliser les outils disponibles dans Azure Machine apprentissage pour améliorer le modèle. Par exemple, vous pouvez expérimenter différents classifieurs pour connaître les résultats précises comment ils donnent, ou utiliser des hyperparameter pour améliorer la précision de réglage.

![Train et Score](./media/machine-learning-text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Étape 4 : Évaluer et valider le modèle

Comment vous aurait pour valider le modèle formé ? Nous il score contre le groupe de données de test et d’évaluer l’exactitude. Toutefois, le modèle appris le vocabulaire de N-grammes et leur poids à partir du dataset de la formation. Par conséquent, nous devons utiliser ce vocabulaire et ces pondérations lors de l’extraction des fonctionnalités à partir des données de test, plutôt que de créer de nouveau le vocabulaire. Par conséquent, nous ajouter le module de fonctionnalités de N-Gram extraire à la branche d’évaluation de l’expérience, connectez le vocabulaire de sortie de branche de formation et définir le mode de vocabulaire en lecture seule. Nous également désactiver le filtrage de N-grammes par fréquence en affectant la valeur minimale 1 instance et au maximum sur 100 % et de désactiver la fonctionnalité de sélection.

Une fois la colonne de données de test de texte a été transformée à des colonnes numériques, nous exclure les colonnes de type chaîne à partir des étapes précédentes, comme dans la branche de la formation. Nous utilisons ensuite le module du modèle de Score pour faire des prévisions et module d’évaluer le modèle pour évaluer la précision.

## <a name="step-5-deploy-the-model-to-production"></a>Étape 5 : Déployer le modèle à la production

Le modèle est presque prêt à être déployée en production. Lors du déploiement en tant que service web, il prend la chaîne de texte en forme libre comme entrée et renvoyer une prévision « élevée » ou « faible ». Il utilise le vocabulaire N-gram appris pour transformer le texte aux fonctionnalités et modèle formé de régression logistique pour établir une prévision à partir de ces fonctionnalités. 

Pour configurer l’expérience prédictive, nous enregistrez d’abord le vocabulaire N-gram en tant que groupe de données et le modèle formé de régression logistique de la branche de la formation de l’expérience. Ensuite, nous allons enregistrer l’expérience à l’aide de « Enregistrer sous » pour créer un graphique d’expérience pour une expérience. Nous supprimer le module de données fractionnées et la branche de la formation de l’expérimentation. Nous nous connectons ensuite le vocabulaire de N-gram et le modèle précédemment enregistré pour extraire les fonctionnalités N-Gram et modules de modèle de Score, respectivement. Nous avons également supprimer le module d’évaluer le modèle.

Nous insérer de sélectionner les colonnes dans le module du Dataset avant de module de prétraiter le texte à supprimer de la colonne libellé et désélectionner l’option « Ajouter la colonne de score au groupe de données » dans le Module de Score. De cette manière, le service web ne demande pas l’étiquette, qu'il tente de prédire et ne pas les écho l’entrée présente dans la réponse.

![Expérience prédictive](./media/machine-learning-text-analytics-module-tutorial/predictive-text.png)

Nous avons une expérience qui peut être publiée sous la forme d’un service web et appelée à l’aide de l’exécution de requête-réponse ou un lot d’API.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les modules d’analytique de texte à partir de [documentation MSDN] (https://msdn.microsoft.com/library/azure/dn905886.aspx).
