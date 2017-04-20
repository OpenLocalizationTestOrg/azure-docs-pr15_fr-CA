<properties
    pageTitle="Déployer un service web de formation de l’ordinateur | Microsoft Azure"
    description="Comment faire pour convertir une expérience de formation pour une expérimentation prédictive, préparer pour le déploiement, puis le déployer en tant que formation de Machine Azure web service."
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

# <a name="deploy-an-azure-machine-learning-web-service"></a>Déploiement d’un service web de formation de Machine Azure

L’apprentissage Machine Azure permet vous permet de créer, tester et déployer des solutions d’analyse prédictives.

À partir d’un point-de-vue, cela s’effectue en trois étapes :

- **[Créer une expérience de formation]** - Azure Machine Learning Studio est un environnement collaboratif de développement visuel qui vous permet de former et de test d’un modèle de prévision analytique à l’aide de données d’apprentissage que vous fournissez.
- **[Convertir en une expérience prédictive]** - une fois votre modèle a été formé avec les données existantes et vous êtes prêt à utiliser pour évaluer les nouvelles données, vous préparez et Rationalisez votre expérience pour les prévisions.
- **Déployer comme un service web** - vous pouvez déployer votre expérience prédictive sous la forme d’un service web Azure [Nouveau] ou [classique] . Les utilisateurs peuvent envoyer des données à votre modèle et recevoir les prévisions de votre modèle.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Créer une expérience de formation

Pour former un modèle analytique prédictive, vous utilisez Studio d’apprentissage Machine Azure pour créer une expérience de formation où vous incluez différents modules pour charger les données de formation, préparer les données en tant que de besoin, appliquer des algorithmes d’apprentissage machine et évaluer les résultats. Vous pouvez effectuer une itération sur une expérience et essayez d’algorithmes d’apprentissage automatique différentes pour comparer et évaluer les résultats.

Le processus de création et de gestion des expériences de formation est couvert plus en détail ailleurs. Pour plus d’informations, voir les articles suivants :

- [Créer une expérience simple dans Azure Machine Learning Studio](machine-learning-create-experiment.md)
- [Développer une solution prédictive avec formation de Machine Azure](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importer vos données de formation dans Azure Machine Learning Studio](machine-learning-data-science-import-data.md)
- [Gérer l’expérience des itérations dans Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir l’expérience de formation pour une expérimentation prédictive

Une fois que vous avez formé votre modèle, vous êtes prêt à convertir votre expérience de formation en une expérience prédictive pour évaluer de nouvelles données.

En convertissant une expérience prédictive, vous obtenez votre modèle formé prêt à être déployé comme un service web de score. Les utilisateurs du service web peuvent envoyer des données d’entrée à votre modèle et votre modèle de renvoyer les résultats de la prévision. Lorsque vous convertissez une expérience prédictive, gardez à l’esprit comment vous prévoyez votre modèle pour être utilisé par d’autres utilisateurs.

Pour convertir votre expérience de formation en une expérience prédictive, cliquez sur **exécuter** en bas de la zone de l’expérience, **La valeur d’un Service Web**, puis sur **Prédictive Web Service**.

![Convertir en notation d’expérience](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Pour plus d’informations sur la façon d’effectuer cette conversion, consultez [convertir une expérience de formation d’apprentissage automatique pour une expérimentation prédictive](machine-learning-convert-training-experiment-to-scoring-experiment.md).

Les étapes suivantes décrivent le déploiement d’une expérimentation prédictive comme un nouveau service web. Vous pouvez également déployer l’expérience en tant que service de web classique.

## <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Déployer l’expérience prédictive sous la forme d’un service web

Maintenant que l’expérimentation prédictive a été préparée, vous pouvez le déployer comme un service web Azure. À l’aide du service web, les utilisateurs peuvent envoyer des données à votre modèle et le modèle renvoie ses prévisions.

Pour déployer votre expérience prédictive, cliquez sur **exécuter** en bas de la zone d’essai. Une fois que l’expérience a fini de s’exécuter, cliquez sur **Déployer le Service Web** et sélectionnez **déployer le Service Web [Nouveau]**.  La page déploiement de Machine formation Web Service portal s’ouvre.

### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Portail de formation Web Service machine Page d’expérience de déploiement

Dans la page déployer l’expérimentation, entrez un nom pour le service web.
Sélectionnez un plan de tarification. Si vous avez un plan de tarification existant, que vous pouvez le sélectionner, sinon vous devez créer un nouveau plan de prix pour le service.

1.  Dans le **Plan du prix** de la liste déroulante, sélectionnez un plan existant ou l’option **Sélectionner le nouveau plan** .
2.  Dans **Nom de Plan**, entrez un nom qui identifiera le plan sur votre facture.
3.  Sélectionnez un des **Niveaux de Plan mensuel**. La valeur par défaut des niveaux de plan pour les plans pour votre région par défaut et votre service web est déployé dans cette région.

Cliquez sur **déployer** et la page de **démarrage rapide** pour votre service de web s’ouvre.

La page de démarrage rapide de service web vous donne accès et conseils sur les tâches les plus courantes que vous aurez à effectuer après la création d’un service web. À partir de là, vous pouvez facilement accéder à la fois la page de Test et la page de consommer.

<!-- ![Deploy the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-web-service"></a>Tester votre service web

Pour tester votre nouveau service web, cliquez sur **tester le service web** sous tâches courantes. Sur la page de Test, vous pouvez tester votre service web sous la forme d’un Service de requête-réponse (RR) ou un service d’exécution du lot (BES).

La page de test de RR affiche les entrées, sorties et tous les paramètres globaux que vous avez définis pour l’expérience. Pour tester le service web, vous pouvez manuellement entrer des valeurs appropriées pour les entrées ou fournir un fichier mis en forme séparées par des virgules (CSV) de valeur contenant les valeurs de test.

Pour tester à l’aide de RR, le mode d’affichage liste, entrez les valeurs appropriées pour les entrées, puis cliquez sur **Test de requête-réponse**. Les résultats de la prévision s’affiche dans la colonne de sortie vers la gauche.

![Déployer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Pour tester votre BES, cliquez sur **traitement par lots**. Sur la page de test de traitement par lots, cliquez sur Parcourir sous votre entrée et sélectionnez un fichier CSV contenant les valeurs d’échantillon approprié. Si vous n’avez pas un fichier CSV et que vous avez créé votre expérience prédictive à l’aide de Studio d’apprentissage Machine, vous pouvez télécharger le jeu de données pour votre expérience prédictive et l’utiliser.

Pour télécharger le jeu de données, ouvrez Studio de formation de Machine. Ouvrez votre expérience prédictive et cliquez avec le bouton droit sur l’entrée de votre expérience. Dans le menu contextuel, sélectionnez **dataset** , puis **Télécharger**.

![Déployer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

Cliquez sur **Test**. Le statut de votre travail d’exécution du lot s’affiche à droite sous **Traitements par lots de Test**.

![Déployer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

Sur la page **CONFIGURATION** , vous pouvez modifier la description, le titre, mettre à jour la clé de compte de stockage et activer des exemples de données pour votre service web.

![Configurer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Une fois que vous avez déployé le service web, vous pouvez :

- **Accès** via l’API de service web.
- **Gérer** via le portail de formation de Machine Azure web services ou l’Azure portal classique.
- **Mise à jour** si votre modèle est modifié.

### <a name="access-the-web-service"></a>Accéder au service web

Une fois que vous déployez votre service web à partir de Studio d’apprentissage Machine, vous pouvez envoyer des données vers le service et recevoir les réponses par programme.

La page de **consommer** fournit toutes les informations dont vous avez besoin pour accéder à votre service web. Par exemple, la clé de l’API est fournie pour permettre un accès autorisé au service.

Pour plus d’informations sur l’accès à un service web d’apprentissage automatique, voir [comment consommer un service web de formation de Machine Azure déployé](machine-learning-consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Gérer votre nouveau service web

Vous pouvez gérer votre portail de Machine, Learning Services Web services web classique. À partir de la [page principale du portail](https://services.azureml-test.net/), cliquez sur **Les Services Web**. À partir de la page des services web, vous pouvez supprimer ou copie d’un service. Pour analyser un service spécifique, cliquez sur le service et puis cliquez sur **tableau de bord**. Pour surveiller les traitements par lots associés au service web, cliquez sur **Journal des demandes de traitement par lots**.

## <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Déployer l’expérience prédictive comme service web classique

Maintenant que l’expérimentation prédictive a été suffisamment préparée, vous pouvez le déployer comme un service web Azure. À l’aide du service web, les utilisateurs peuvent envoyer des données à votre modèle et le modèle renvoie ses prévisions.

Pour déployer votre expérience prédictive, cliquez sur **exécuter** en bas de la zone d’essai et puis cliquez sur **Déployer le Service Web**. Le service web est configuré et vous sont placés dans le tableau de bord de service web.

![Déployer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Vous pouvez tester le service web dans le portail de Services Web de formation Machine ou Machine Learning Studio.

Pour tester le service web de réponse à la demande, cliquez sur le bouton de **Test** dans le tableau de bord de service web. Une boîte de dialogue s’affiche pour vous demander des données d’entrée pour le service. Ce sont les colonnes attendus par l’expérience d’évaluation. Permet d’entrer un jeu de données et puis cliquez sur **OK**. Les résultats générés par le service web sont affichent en bas du tableau de bord.

Vous pouvez cliquer sur le lien aperçu **de Test** pour tester votre service dans le portail Azure Machine formation Web Services, comme indiqué précédemment dans la nouvelle section de service web.

Pour tester le Service d’exécution de lot, cliquez sur **tester** le lien aperçu. Sur la page de test de traitement par lots, cliquez sur Parcourir sous votre entrée et sélectionnez un fichier CSV contenant les valeurs d’échantillon approprié. Si vous n’avez pas un fichier CSV et que vous avez créé votre expérience prédictive à l’aide de Studio d’apprentissage Machine, vous pouvez télécharger le jeu de données pour votre expérience prédictive et l’utiliser.

![Tester le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

Sur la page **CONFIGURATION** , vous pouvez modifier le nom complet du service et lui donner une description. Le nom et la description s’affiche dans [Azure portal classique](http://manage.windowsazure.com/) où vous gérez vos services web.

Vous pouvez fournir une description de vos données d’entrée, les données de sortie et paramètres de service web en entrant une chaîne pour chaque colonne dans le **Schéma d’entrée**, les **Schémas de sortie**et les **Paramètre du SERVICE Web**. Ces descriptions sont utilisées dans l’exemple de documentation de code fourni pour le service web.

Vous pouvez activer l’enregistrement diagnostiquer les problèmes que vous voyez lorsque votre service web est accessible. Pour plus d’informations, consultez [activation de la journalisation pour les services web de formation de l’ordinateur](machine-learning-web-services-logging.md).

![Configurer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

Vous pouvez également configurer les points de terminaison pour le service web dans le portail Azure Machine Learning Web Services similaire à la procédure décrite précédemment dans la nouvelle section de service web. Les options sont différentes, vous pouvez ajouter ou modifier la description de service, activer la journalisation et activer les données d’exemple pour le test.

### <a name="access-the-web-service"></a>Accéder au service web

Une fois que vous déployez votre service web à partir de Studio d’apprentissage Machine, vous pouvez envoyer des données vers le service et recevoir les réponses par programme.

Le tableau de bord fournit toutes les informations dont vous avez besoin pour accéder à votre service web. Par exemple, la clé de l’API est fournie pour permettre un accès autorisé au service, et les pages d’aide API sont fournis afin de commencer à écrire votre code.

Pour plus d’informations sur l’accès à un service web d’apprentissage automatique, voir [comment consommer un service web de formation de Machine Azure déployé](machine-learning-consume-web-services.md).

### <a name="manage-the-web-service"></a>Gérer le service web

Il existe diverses actions vous pouvez effectuer pour surveiller un service web. Vous pouvez mettre à jour et le supprimer. Vous pouvez également ajouter des points de terminaison supplémentaires à un service web de classique en plus le point de terminaison par défaut qui est créé lorsque vous le déployez.

Pour plus d’informations, consultez [gérer un espace de travail formation de Machine Azure](machine-learning-manage-workspace.md) et [gérer un service web en utilisant le portail Azure Machine Learning Web Services](machine-learning-manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Mise à jour du service web

Vous pouvez apporter des modifications à votre service web, telles que la mise à jour du modèle avec les données de formation supplémentaires et le déployer à nouveau, remplaçant le service web d’origine.

Pour mettre à jour le service web, ouvrez l’expérience prédictive d’origine, que vous avez utilisé pour déployer le service web et de créer une copie modifiable en cliquant sur **Enregistrer sous**. Apportez les modifications souhaitées, puis cliquez sur **Déployer le Service Web**.

Étant donné que vous avez déployé cette expérience avant, vous êtes invité si vous souhaitez remplacer (classique Web Service) ou de mettre à jour (nouveau service web) le service existant. En cliquant sur **Oui** ou **mise à jour** arrête le service web existants et déploie la nouvelle expérience prédictive est déployé à sa place.

> [AZURE.NOTE] Si vous avez apporté des modifications de configuration dans le service web d’origine, par exemple, entrer un nouveau nom ou une description, vous devez entrer de nouveau ces valeurs.

Une option de mise à jour de votre service web consiste à former le modèle par programmation. Pour plus d’informations, consultez [modèles de dispenser une formation de Machine par programme](machine-learning-retrain-models-programmatically.md).


<!-- internal links -->
[Créer une expérience de formation]: #create-a-training-experiment
[Convertir en une expérience prédictive]: #convert-the-training-experiment-to-a-predictive-experiment
[Nouveau]: #deploy-the-predictive-experiment-as-a-new-Web-service
[classique]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
