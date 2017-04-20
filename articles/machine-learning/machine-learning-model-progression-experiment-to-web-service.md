<properties
    pageTitle="Comment progresse d’un modèle d’apprentissage de l’ordinateur à partir d’une expérience à un service Web operationalized | Microsoft Azure"
    description="Vue d’ensemble des mécanismes de comment votre avance de modèle Azure Machine Learning à partir d’un développement essayer à un service Web operationalized."
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
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Comment progresse d’un modèle d’apprentissage de l’ordinateur à partir d’une expérience à un service Web operationalized

Studio d’apprentissage Machine Azure fournit une zone interactive qui permet de développer, d’exécuter, de tester et d’itérer au sein d’un ***tester*** représentant un modèle d’analyse prédictive. Il existe un large éventail de modules qui peuvent :

* Données d’entrée dans votre expérience
* Manipulation des données
* Former un modèle à l’aide d’algorithmes d’apprentissage machine
* Score du modèle
* Évaluer les résultats
* Valeurs finales de sortie

Une fois que vous êtes satisfait de votre expérience, vous pouvez le déployer comme un ***service Web de formation classique Azure Machine*** ou d’un ***service Web de formation nouvelles Azure Machine*** afin que les utilisateurs peuvent envoyer de nouvelles données et recevoir les résultats.

Dans cet article, nous donne une vue d’ensemble des mécanismes de comment votre avance de modèle d’apprentissage automatique à partir d’un développement essayer à un service Web operationalized.

>[AZURE.NOTE] Il existe autres façons pour développer et déployer des modèles de formation des machines, mais cet article se concentre sur l’utilisation de Studio de formation de Machine. Pour savoir comment créer un service Web de prédictive classique avec R, consultez le blog [Build & déployer prédictive Web Apps à l’aide de RStudio et Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).

Tandis que Azure Machine Learning Studio est conçu pour vous aider à développer et à déployer un *modèle d’analyse prédictive*, il est possible de Studio permet de développer une expérience qui n’inclut pas un modèle d’analyse prédictive. Par exemple, une expérience peut simplement d’entrée de données, manipuler et puis générer les résultats. À l’instar d’une expérience d’analyse prédictive, vous pouvez déployer cette expérience non prédictive sous la forme d’un service Web, mais il est plus simple car l’expérience n’est pas de formation ou évaluation d’un modèle d’apprentissage de l’ordinateur. S’il n’est pas classique à l’utilisation de Studio de cette manière, nous allons l’inclure dans la discussion afin que nous pouvons donner une explication complète du fonctionne de Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Développement et déploiement d’un service Web prédictif

Voici les étapes une solution classique suit lorsque vous développez et déployez à l’aide de Studio de formation de Machine :

![Flux de déploiement](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Figure 1 : les étapes d’un modèle de type d’analyse prédictive*

### <a name="the-training-experiment"></a>L’expérience de formation

Les ***essais de formation*** est la phase initiale de développement de votre service Web dans le Studio de formation de Machine. L’expérience de formation vise à vous permettre de développer, de tester, de parcourir et de finalement former une machine d’apprentissage du modèle. Vous pouvez même former plusieurs modèles simultanément que vous recherchez la meilleure solution, mais une fois que vous avez terminé l’expérimentation vous sélectionnez un seul formé de modèle et d’éliminer le reste de l’expérimentation. Pour obtenir un exemple de développement d’une analyse prédictive essayer, voir [Création d’une solution de prévision analytique pour l’évaluation des risques de crédit dans l’apprentissage automatique de Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>L’expérience prédictive

Une fois que vous avez un modèle formé dans votre expérience de formation, cliquez sur **La valeur d’un Service Web** et sélectionnez **Prédictive Web Service** dans un Studio de formation de Machine pour lancer le processus de conversion de votre expérience de formation pour une ***expérimentation prédictive***. L’objectif de l’expérimentation prédictive est d’utiliser votre modèle formé pour évaluer de nouvelles données, avec l’objectif de finalement devenir operationalized sous la forme d’un service Web d’Azure.

Cette conversion est effectuée pour les étapes suivantes :

-   Convertir l’ensemble des modules utilisés pour la formation dans un seul module et l’enregistrer comme un modèle formé

-   Éliminer tous les modules superflus non liées à l’attribution de scores

-   Ajouter des ports d’entrée et de sortie qui utilise le service Web éventuelle

Il peut y avoir plusieurs modifications que vous souhaitez parvenir votre expérience prédictive prêt à le déployer comme un service Web. Par exemple, si vous souhaitez que le service Web uniquement un sous-ensemble des résultats de sortie, vous pouvez ajouter un module de filtrage avant le port de sortie.

Dans ce processus de conversion, l’expérience de formation n’est pas supprimé. Lorsque le processus est terminé, vous avez deux onglets dans Studio : un pour l’expérimentation de la formation et l’autre pour l’expérience prédictive. Ainsi que vous pouvez apporter des modifications à l’expérience de formation avant de déployer votre service Web et de reconstruire l’expérience prédictive. Ou bien, vous pouvez enregistrer une copie de l’expérience de formation pour démarrer une autre ligne de l’expérimentation.

>[AZURE.NOTE] Lorsque vous cliquez sur **Prévision Web Service** vous démarrez un processus automatique pour convertir votre expérience de formation pour une expérimentation prédictive, et cela fonctionne bien dans la plupart des cas. Si votre expérience de formation est complexe (par exemple, si vous avez plusieurs chemins d’accès pour la formation qui vous joignez), vous pouvez exécuter cette opération manuellement. Pour plus d’informations, voir [convertir une expérience de formation d’apprentissage automatique pour une expérimentation prédictive](machine-learning-convert-training-experiment-to-scoring-experiment.md).

### <a name="the-web-service"></a>Le service Web

Une fois que vous vous êtes assuré que votre expérience prédictive est prêt, vous pouvez déployer votre service en tant que service Web classique ou un service d’un site Web basé sur le Gestionnaire de ressources Azure. Pour mettre votre modèle en le déployant en tant que *service Web d’apprentissage Machine classique*, cliquez sur **Déployer le Service Web** et sélectionnez le **Service Web de déploiement [standard]**. Pour déployer en tant que *service Web d’apprentissage Machine nouveau*, cliquez sur **Déployer le Service Web** et sélectionnez **Web Service de déploiement [nouveau]**. Les utilisateurs peuvent désormais envoyer des données à votre modèle en utilisant le service Web API REST et obtenir les résultats en retour. Pour plus d’informations, consultez [comment consommer un service Web de formation Azure Machine qui a été déployé à partir d’une Machine d’essai de formation](machine-learning-consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Le cas non classique : création d’un service Web non prédictive

Si votre expérience ne pas former un modèle d’analyse prédictive, puis que vous n’avez pas besoin de créer une expérience de formation et une expérience d’évaluation - il y a juste une seule expérience, et vous pouvez le déployer comme un service Web. Studio d’apprentissage machine détecte si votre expérience contient un modèle de prévision en analysant les modules que vous avez utilisé.

Une fois que vous avez parcourue sur votre expérience et il convient :

1.  Cliquez sur **La valeur d’un Service Web** et sélectionnez le **Recyclage des services Web** - nœuds d’entrée et de sortie sont automatiquement ajoutés.

2.  Cliquez sur **exécuter**

3. Cliquez sur **Déployer le Service Web** et sélectionnez le **Service Web de déploiement [standard]** ou de **Déploiement du Service Web [nouveau]** en fonction de l’environnement dans lequel vous souhaitez déployer.

Votre service Web est désormais déployé, et vous pouvez accéder et le gérer comme un service Web prédictif.

## <a name="updating-your-web-service"></a>Mise à jour de votre service Web

Maintenant que vous avez déployé votre expérience en tant qu’un service Web, que se passe-t-il si vous devez mettre à jour ?

Cela dépend de ce que vous devez mettre à jour :

**Vous souhaitez modifier l’entrée ou sortie, ou que vous souhaitez modifier la manière dont le service Web manipule les données**

Si vous ne changez pas le modèle, mais que vous changez simplement comment le service Web traite les données, vous pouvez modifier l’expérience prédictive et cliquez sur **Déployer le Service Web** et sélectionner **Déployer du Service Web [standard]** ou **Déployer le Service Web [nouveau]** à nouveau. Le service Web est arrêté, l’expérimentation prédictive mis à jour est déployée et le service Web est redémarré.

Voici un exemple : supposons que votre expérience prédictive renvoie toute la ligne de données d’entrée avec le résultat prévu. Vous pouvez décider que vous souhaitez que le service Web simplement retourner le résultat. Ainsi, vous pouvez ajouter un module de **Projet de colonnes** à l’expérimentation prédictive, juste avant le port de sortie pour les exclure des colonnes de résultat. Lorsque vous cliquez sur **Déployer le Service Web** et sélectionnez le **Service Web de déploiement [standard]** ou de **Déploiement du Service Web [nouveau]** à nouveau, le service Web est mis à jour.

**Vous souhaitez former le modèle avec de nouvelles données**

Si vous souhaitez conserver votre machine d’apprentissage du modèle, mais vous souhaitez qu’il former avec de nouvelles données, vous avez deux possibilités :

1.  **Former le modèle pendant l’exécution du service Web** - si vous souhaitez former votre modèle lors de la prévision de service Web est en cours d’exécution, cela en apportant quelques modifications à l’expérience de formation afin de faciliter une ***expérience de réadaptation***, puis vous pouvez le déployer en tant que **service *web de reconversion* **. Pour obtenir des instructions sur la procédure à suivre, consultez [modèles de dispenser une formation de Machine par programme](machine-learning-retrain-models-programmatically.md).

2.  **Revenez à la première expérimentation de formation et utiliser les données de formation différentes pour développer votre modèle** - votre expérience prédictive est lié au service Web, mais l’expérience de formation n’est pas lié directement de cette façon. Si vous modifiez l’expérience de formation initiale et que vous cliquez sur **La valeur d’un Service Web**, il crée une *nouvelle* prévision essayer qui, lors du déploiement, crée un *Nouveau* service Web. Il ne simplement à jour le service Web d’origine.

    Si vous devez modifier l’expérience de formation, ouvrez-le et cliquez sur **Enregistrer sous** pour faire une copie. Cela laisse intact l’expérience de formation initiale, expérience prédictive et service Web. Vous pouvez maintenant créer un nouveau service Web avec vos modifications. Une fois que vous avez déployé le nouveau service Web, que vous pouvez alors décider d’arrêter le service Web précédent afin qu’il fonctionne à côté de l’autre.

**Vous voulez exercer un autre modèle**

Si vous souhaitez apporter des modifications à votre expérience de prévision d’origine, par exemple la sélection d’un algorithme d’apprentissage machine différente, essayez de procédé de formation de différents, etc., alors que vous devez suivre la deuxième procédure décrite ci-dessus pour le recyclage de votre modèle : l’expérience de formation, cliquez sur **Enregistrer sous** pour faire une copie, puis démarrez le nouveau chemin d’accès de votre modèle de développement vers le bas , l’expérimentation prédictive de création et déploiement du service web. Ceci créera un nouveau site Web Services, sans rapport avec l’original - vous pouvez choisir parmi, ou les deux, continue de s’exécuter.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le processus de développement et l’expérimentation, consultez les articles suivants :

-   conversion de l’expérimentation - [convertir une expérience de formation d’apprentissage automatique pour une expérimentation prédictive](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   déploiement du service Web - [déployer un service web de formation de Machine Azure](machine-learning-publish-a-machine-learning-web-service.md)

-   recyclage du modèle - [modèles de dispenser une formation de Machine par programme](machine-learning-retrain-models-programmatically.md)

Pour obtenir des exemples de l’ensemble du processus, voir :

-   [Didacticiel de formation de machine : créer votre première expérience dans un Studio d’apprentissage Machine Azure](machine-learning-create-experiment.md)

-   [Procédure pas à pas : Développement d’une solution d’analytique prédictive pour l’évaluation des risques de crédit dans l’apprentissage automatique de Azure](machine-learning-walkthrough-develop-predictive-solution.md)