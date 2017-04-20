<properties
    pageTitle="Convertir une expérience de formation d’apprentissage automatique pour une expérimentation prédictive | Microsoft Azure"
    description="Comment convertir une expérience de formation apprentissage automatique, utilisée pour la formation de votre modèle prédictif analytique, pour une expérience prédictive, qui peut être déployée comme un service web."
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
    ms.author="garye"/>

# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>Convertir une expérience de formation d’apprentissage automatique pour une expérimentation prédictive

L’apprentissage Machine Azure permet vous permet de créer, tester et déployer des solutions d’analytique prédictive.

Une fois que vous avez créé et parcourues sur une *formation essayer* pour exercer votre modèle prédictif analytique, et vous êtes prêt à utiliser pour évaluer les nouvelles données, vous devez préparer et optimiser votre expérience d’évaluation. Vous pouvez ensuite déployer cette *expérience prédictive* sous la forme d’un service web Azure afin que les utilisateurs peuvent envoyer des données à votre modèle et recevoir les prévisions de votre modèle.

En convertissant une expérience prédictive, vous obtenez votre modèle formé prêt à être déployé comme un service web. Les utilisateurs du service web envoie des données d’entrée à votre modèle et votre modèle de renvoyer les résultats de la prévision. Ainsi, que vous convertissez en une expérience prédictive vous devez garder à l’esprit comment vous prévoyez votre modèle pour être utilisé par d’autres.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Le processus de conversion d’une expérience de formation pour une expérimentation prédictive implique trois étapes :

1.  Enregistrer le modèle d’apprentissage machine vous avez formé et remplacer la machine d’apprentissage algorithme et [Modèle de Train] [ train-model] modules avec votre modèle formé enregistrée.
2.  Supprimer l’expérience pour que les modules qui sont nécessaires pour l’évaluation. Une expérience de formation comprend un certain nombre de modules qui sont nécessaires pour la formation, mais qui ne sont pas nécessaires une fois que le modèle est formé et prêt à utiliser pour le calcul des points.
3.  Définir où votre expérience vous accepterez les données de l’utilisateur de service web et les données seront renvoyées.

## <a name="set-up-web-service-button"></a>Bouton haut le Service Web

Une fois que vous avez exécuté votre expérience (bouton**exécuter** en bas de la zone d’essai), le bouton **Définir des services Web** (sélectionnez l’option **Prédictive Web Service** ) effectue pour vous les trois étapes de la conversion de votre expérience de formation en une expérience prédictive :

1.  Il enregistre votre modèle formé comme un module dans la section **Modèles formés** de la palette du module (à gauche de la zone d’essai), puis remplace l’ordinateur algorithme et [Modèle de Train] de formation[ train-model] modules avec le modèle formé enregistré.
2.  Il supprime les modules qui ne sont pas clairement nécessaires. Dans notre exemple, cela inclut les [Données fractionnées][split], 2e [Modèle de Score][score-model]et [d’Évaluer le modèle] [ evaluate-model] modules.
3.  Il crée l’entrée du service Web et modules de sortie et les ajoute dans les emplacements par défaut de votre expérience.

Par exemple, l’expérience suivante forme un modèle d’arborescence de décision amplifiée de deux-classe à l’aide d’exemples de données recensement :

![Expérience de formation][figure1]

Les modules de cette expérience des fonctions fondamentalement quatre différentes :

![Fonctions du module][figure2]

Lorsque vous convertissez cette expérience de formation en une expérience prédictive, certains de ces modules ne sont plus nécessaires ou qu’ils ont un objectif différent :

- **Données** - les données de cet exemple de dataset n’est pas utilisé lors de l’évaluation - l’utilisateur du service web fournit les données pour un score. Toutefois, les métadonnées à partir de ce groupe de données, tels que les types de données, sont utilisée par le modèle formé. Vous devez conserver le groupe de données à l’expérimentation prédictive afin qu’il puisse fournir des ces métadonnées.

- **Préparation** - en fonction des données qui seront soumises à l’attribution de scores, ces modules peuvent ou n’est peut-être pas nécessaires de traiter les données entrantes.

    Par exemple, dans cet exemple, l’exemple de dataset peut-être avoir des valeurs manquantes, et elle inclut des colonnes qui ne sont pas nécessaires pour exercer le modèle. Par conséquent, un [Nettoyage de données manquant] [ clean-missing-data] module a été inclus pour la transaction avec les valeurs manquantes et de [Sélectionner des colonnes dans un groupe de données] [ select-columns] module a été inclus pour exclure les colonnes supplémentaires dans le flux de données. Si vous savez que les données qui seront soumises à l’attribution de scores via le service web n’auront pas de valeurs manquantes, puis vous pouvez supprimer les [Données manquantes en propre] [ clean-missing-data] module. Toutefois, comme la [Sélection des colonnes dans un groupe de données] [ select-columns] module vous permet de définir le jeu de fonctionnalités à un score, ce module doit rester.

- **Train** - une fois que le modèle a été correctement formé, vous enregistrez en tant que modèle formé unique module. Puis, vous remplacez ces modules individuels avec le modèle formé enregistré.

- Dans cet exemple, le module de fractionnement **score** - est utilisé pour diviser le flux de données en un jeu de données de test et les données d’apprentissage. Dans l’expérience prédictive, ce n’est pas nécessaire et peut être supprimé. De même, le 2e [Modèle de Score] [ score-model] module et le [Modèle d’évaluation] [ evaluate-model] module permettant de comparer les résultats à partir des données de test, afin que ces modules ne sont également pas nécessaires à l’expérimentation prédictive. Le restant [Des modèle de Score] [ score-model] module, cependant, est nécessaire pour retourner un résultat de score via le service web.

Voici comment notre exemple apparaît après avoir cliqué sur **La valeur d’un Service Web**:

![Expérience de prédictive converti][figure3]

Cela peut être suffisant pour préparer votre expérience pour être déployé comme un service web. Toutefois, vous souhaiterez effectuer un travail supplémentaire spécifique à votre expérience.

### <a name="adjust-input-and-output-modules"></a>Ajuster des modules d’entrée et de sortie

Dans votre expérience de formation, vous utilisez un jeu de données d’apprentissage et ensuite fait un traitement pour obtenir les données dans un formulaire que l’algorithme d’apprentissage de l’ordinateur si nécessaire. Si les données que vous souhaitez recevoir via le service web ne seront pas besoin de ce traitement, vous pouvez déplacer le **module d’entrée du service Web** sur un autre nœud dans votre expérience.

Par exemple, par défaut, **La valeur d’un Service Web** place le module **d’entrée du service Web** en haut de votre flux de données, comme dans la figure ci-dessus. Toutefois, si les données d’entrée ne seront pas besoin de ce traitement, puis vous pouvez positionner manuellement l' **entrée du service Web** après les modules de traitement des données :

![Déplacement de l’entrée du service web][figure4]

Les données d’entrée fournies par le service web seront désormais passer directement dans le module du modèle de Score sans prétraitement.

De même, par défaut, **La valeur d’un Service Web** place le module de sortie du service Web en bas de votre flux de données. Dans cet exemple, le service web renvoie à l’utilisateur la sortie du [Modèle de Score] [ score-model] module qui comprend le vecteur d’entrée complète des données, ainsi que l’évaluation des résultats.

Toutefois, si vous préférez retourner quelque chose de différent - par exemple, uniquement les résultats de la notation et pas le vecteur d’ensemble de données d’entrée -, vous peuvent insérer une [Sélection des colonnes dans un groupe de données] [ select-columns] module pour exclure toutes les colonnes à l’exception de l’évaluation des résultats. Puis, vous déplacez le module de **sortie du service Web** à la sortie de la [Sélection des colonnes dans un groupe de données] [ select-columns] module :

![Déplacement de la sortie du service web][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Ajouter ou supprimer des modules de traitement de données supplémentaire

S’il existe plus de modules dans votre expérience que vous savez ne seront pas nécessaires lors de l’attribution de scores, lesquels peuvent être supprimés. Par exemple, comme nous avons déplacé le module **d’entrée du service Web** à un point après les modules de traitement de données, nous pouvons supprimer le [Nettoyage des données manquantes] [ clean-missing-data] module de l’expérimentation prédictive.

Notre expérience prédictive ressemble maintenant à ceci :

![Suppression du module supplémentaire][figure6]

### <a name="add-optional-web-service-parameters"></a>Ajouter des paramètres de Service Web facultatifs

Dans certains cas, vous souhaiterez peut-être permettre à l’utilisateur de votre service web modifier le comportement des modules, lorsque le service est accessible. *Paramètres de Service Web* vous autorise à effectuer cette opération.

Un exemple courant consiste à configurer l' [Importation de données] [ import-data] module afin que l’utilisateur du service web déployé peut spécifier une autre source de données lors de l’accès au service web. Ou la configuration de l' [Exportation de données] [ export-data] module afin qu’une autre destination peut être spécifiée.

Vous pouvez définir des paramètres de Service Web et les associer à un ou plusieurs paramètres de module, et vous pouvez spécifier si elles sont obligatoires ou facultatives. L’utilisateur du service web peut ensuite fournir des valeurs pour ces paramètres lorsque le service est accessible et que les actions du module seront modifiées en conséquence.

Pour plus d’informations sur les paramètres de Service Web, consultez [L’aide Azure Machine Learning, paramètres de Service Web ] [ webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Déployer l’expérience prédictive sous la forme d’un service web

Maintenant que l’expérimentation prédictive a été suffisamment préparée, vous pouvez le déployer comme un service web Azure. À l’aide du service web, les utilisateurs peuvent envoyer des données à votre modèle et le modèle renvoie ses prévisions.

Pour plus d’informations sur le processus de déploiement complet, voir [déploiement d’un service web de formation de Machine Azure][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
