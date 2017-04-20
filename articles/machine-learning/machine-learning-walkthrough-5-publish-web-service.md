<properties
    pageTitle="Étape 5 : Déployer le service Web de formation de Machine | Microsoft Azure"
    description="Étape 5 de la développer une procédure pas à pas solution prédictive : déployer une expérience prédictive dans le Studio d’apprentissage Machine comme un service Web."
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
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Procédure pas à pas, étape 5 : Déployer le service Web de formation de Machine Azure

Il s’agit de la cinquième étape de la procédure pas à pas, [développer une solution analytique prédictive dans l’apprentissage automatique de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Charger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.  [Créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Former et d’évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  **Déployer le service Web**
6.  [Accéder au service Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Pour permettre d’autres personnes d’utiliser le modèle prédictif, que nous avons développé dans cette procédure pas à pas, nous le déployer comme un service Web sur Azure.

À ce stade nous avons fait des tests avec notre modèle de formation. Mais le service déployé va n’est plus à faire de formation - il génère des prévisions à l’entrée d’utilisateur en fonction de notre modèle d’attribution de scores. Nous allons effectuer certaines tâches de préparation pour convertir cette expérience à partir d’une expérience de ***formation*** à une ***prévision*** essayer. 

Il s’agit d’un processus en deux étapes :  

1. Convertir *formation essais* que nous avons créé une *expérience prédictive*
2. Déployer l’expérience prédictive sous la forme d’un service Web

Mais tout d’abord, nous devons d’affiner un peu cette expérience. Nous avons actuellement deux modèles différents de l’expérience, mais nous ne voulons être un modèle lors du déploiement de cela sous la forme d’un service Web.  

Supposons que nous avons décidé que le modèle d’arborescence amplifiée était le meilleur modèle à utiliser. Afin que la première chose à faire est de supprimer les [Deux-classe Machine à vecteur Support] [ two-class-support-vector-machine] module et les modules qui ont été utilisés pour la formation. Voulez-vous faire une copie de l’expérience de tout d’abord en cliquant sur **Enregistrer sous** au bas de la zone d’essai.

Il faut supprimer les modules suivants :  

- [Machine à vecteur Support de classe de deux][two-class-support-vector-machine]
- [Former le modèle] [ train-model] et le [Modèle de Score] [ score-model] modules qui ont été connectés
- [Normaliser les données] [ normalize-data] (les deux)
- [Évaluation du modèle][evaluate-model]

Sélectionnez le module et appuyez sur la touche SUPPR, ou cliquez sur le module et sélectionnez **Supprimer**.

Maintenant, nous sommes prêts à déployer ce modèle à l’aide de l' [Arbre de décision amplifiée deux-classe][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir l’expérience de formation pour une expérimentation prédictive

Conversion d’une expérimentation prédictive implique trois étapes :

1. Enregistrer le modèle de nous avoir formé, puis la remplacer nos modules de formation
2. Supprimer l’expérience pour supprimer les modules qui ont été uniquement nécessaires pour la formation
3. Définir où le service Web accepte les entrées et dans lequel il génère la sortie

Heureusement, les trois étapes est possible en cliquant sur **définir des Web Services** au bas de la zone d’essai (sélectionnez l’option de **service Web prédictives** ).

Lorsque vous cliquez sur **définir des Web Services**, plusieurs choses se produisent :

- Le modèle formé est enregistré sous la forme d’un seul module de **Modèle formé** dans la palette de module à gauche de la zone d’expérience (vous le trouverez sous **Modèles formés**).
- Les modules qui ont été utilisés pour la formation sont supprimés. En particulier :
  - [Arbre de décision amplifiée de deux-classe][two-class-boosted-decision-tree]
  - [Modèle de train][train-model]
  - [Données fractionnées][split]
  - la deuxième [Exécution de Script de R] [ execute-r-script] module qui a été utilisé pour les données de test
- Le modèle formé enregistré est ajouté dans l’expérience.
- Modules **d’entrée du service Web** et de la **sortie du service Web** sont ajoutés.

> [AZURE.NOTE] L’expérience a été enregistrée dans les deux parties sous les onglets qui ont été ajoutés dans la partie supérieure de la zone d’essai : l’expérience de formation d’origine est sous l’onglet **essayer de formation**et l’expérience prédictive nouvellement créé est sous **une prédictive essayer**.

Nous devons effectuer une étape supplémentaire à cette expérience donnée.
Nous avons ajouté deux [Exécuter un Script R] [ execute-r-script] modules pour fournir une fonction de pondération pour les données de formation et de test. Nous n’avez pas besoin de le faire dans le modèle final.

Studio d’apprentissage machine supprimé une [Exécution de Script de R] [ execute-r-script] module lorsque vous supprimer le [fractionnement] [ split] module. Maintenant nous pouvons maintenant supprimer l’autre et connecter [Éditeur de métadonnées] [ metadata-editor] directement sur le [Modèle de Score][score-model].    

Notre expérience doit maintenant ressembler à ceci :  

![Le modèle formé d’attribution de scores][4]  

> [AZURE.NOTE] Vous vous demandez peut-être pourquoi nous avons laissé le dataset de données de carte de crédit UCI allemand à l’expérimentation prédictive. Le service va utiliser les données de l’utilisateur, pas le jeu de données d’origine, alors pourquoi laisser le dataset d’origine dans le modèle ?
>
>Il est vrai que le service n’a pas besoin des données de carte de crédit d’origine. Mais il n’a besoin du schéma pour ces données, qui inclut des informations telles que le nombre de colonnes il y a et les colonnes sont numériques. Ces informations de schéma sont nécessaires pour interpréter les données de l’utilisateur. Nous laisser ces composants connectés afin que le module de score a le schéma d’un dataset lorsque le service est en cours d’exécution. Les données n’est pas utilisées, uniquement le schéma.  

Exécutez l’expérience une dernière fois (cliquez sur **exécuter**). Si vous souhaitez vérifier que le modèle fonctionne encore, cliquez sur la sortie du [Modèle de Score] [ score-model] module et sélectionnez **Afficher les résultats**. Vous verrez que les données d’origine sont affichées, ainsi que la valeur de risque de crédit (« obtenu étiquettes ») et la valeur de probabilité score (« probabilités un score ».) 

## <a name="deploy-the-web-service"></a>Déployer le service Web

Vous pouvez déployer l’expérience sous la forme d’un service Web classique ou un service Web basé sur le Gestionnaire de ressources Azure.

### <a name="deploy-as-a-classic-web-service"></a>Déployer comme un service Web classique ###

Pour déployer un service Web classique dérivé de notre expérience, cliquez sur **Déployer le Service Web** au-dessous de la zone de dessin, puis sélectionnez le **Service Web de déploiement [standard]**. Studio d’apprentissage machine déploie l’expérience sous la forme d’un service Web et vous amène dans le tableau de bord pour ce service Web. À partir d’ici, vous pouvez revenir à l’expérimentation (**Afficher l’instantané** ou **Afficher plus tard**) et exécuter un test simple du service Web (voir **le Test du service Web** ci-dessous). Il existe également des informations pour la création d’applications qui peuvent accéder au service Web (plus dans l’étape suivante de cette procédure pas à pas).

![Tableau de bord de service Web][6]

Vous pouvez configurer le service en cliquant sur l’onglet **CONFIGURATION** . Ici, vous pouvez modifier le nom du service (il a donné le nom de l’expérience par défaut) et lui donner une description. Vous pouvez également donner plusieurs étiquettes convivial pour les colonnes d’entrée et de sortie.  

![Configurer le service Web][5]  

### <a name="deploy-as-a-new-web-service"></a>Déployer en tant que nouveau Web service

Pour déployer un service Web nouveau dérivé de notre expérience, cliquez sur **Service Web de déploiement** sous la zone de dessin et le **Déploiement du Service Web [nouveau]**. Studio d’apprentissage machine vous transfère à la page d’expérience de déploiement de services Web de formation de Machine Azure.

Entrez un nom pour le service Web et sélectionnez un plan de tarification. Si vous avez un plan de tarification existant, que vous pouvez le sélectionner, sinon vous devez créer un nouveau plan de prix pour le service. 

1.  Dans la liste déroulante **Plan de prix** , sélectionnez un plan existant ou l’option **Sélectionner le nouveau plan** .
2.  Dans **Nom de Plan**, entrez un nom qui identifie le plan sur votre facture.
3.  Sélectionnez un des **Niveaux de Plan mensuel**. La valeur par défaut des niveaux de plan pour les plans pour votre région par défaut et votre service Web est déployé dans cette région.

Cliquez sur **déployer** et la page de **démarrage rapide** de vos ouvertures de service Web.

Vous pouvez configurer le service en cliquant sur l’option de menu de **configuration** . Ici, vous pouvez modifier le nom du service (il a donné le nom de l’expérience par défaut) et lui donner une description. 

Pour tester la sélection de service Web, cliquez sur l’option de menu **de Test** (voir le **Test du service Web** ci-dessous). Pour plus d’informations sur la création d’applications qui peuvent accéder au service Web, cliquez sur l’option de menu de **consommer** (plus dans l’étape suivante de cette procédure pas à pas).

> [AZURE.TIP] Une fois que vous avez déployé, vous pouvez mettre à jour le service Web. Par exemple, si vous souhaitez modifier votre modèle, modifier l’expérience de formation, modifier les paramètres de modèle, cliquez sur **Déployer le Service Web**. Sélectionnez ensuite le **Service Web de déploiement [standard]** ou de **Déploiement du Service Web [nouveau]**. Lorsque vous déployez de nouveau l’expérimentation, il remplace le service Web, en maintenant à l’aide de votre modèle mis à jour.  

## <a name="test-the-web-service"></a>Tester le service Web

### <a name="test-a-classic-web-service"></a>Tester un service Web classique

Vous pouvez tester le service dans le Studio d’apprentissage Machine ou dans le portail Azure Machine Learning Web Services. Test du portail Azure Machine Learning Web Services a l’avantage de vous permettre d’activer 

**Tester dans un Studio de formation de Machine**

Sur la page de **tableau de bord** , cliquez sur le bouton de **Test** sous **Le point de terminaison par défaut**. Une boîte de dialogue s’affiche et vous demande pour les données d’entrée pour le service. Ce sont les mêmes colonnes apparaissant dans le dataset de risque de crédit allemand d’origine.  

Permet d’entrer un jeu de données et puis cliquez sur **OK**. 

**Test du portail Azure Machine Learning Web Services**

Sur la page de **tableau de bord** , cliquez sur le lien aperçu de **Test** sous **Le point de terminaison par défaut**. Dans le portail Azure Machine Learning Web Services pour le point de terminaison de service Web, la page de test s’ouvre et vous demande les données d’entrée pour le service. Ce sont les mêmes colonnes apparaissant dans le dataset de risque de crédit allemand d’origine.

Cliquez sur **Test de requête-réponse**. 

Dans le service Web, les données entrent via le module **d’entrée du service Web** , par le biais de l' [Éditeur de métadonnées] [ metadata-editor] module et le [Modèle de Score] [ score-model] module où il est évalué. Les résultats sont ensuite de sortie à partir du service Web par le biais de la **sortie du service Web**.

**Tester un service Web**

Dans le portail de services Web de formation Azure Machine, cliquez sur **Test** en haut de la page. La page de **Test** s’ouvre et vous pouvez entrer des données pour le service. Les champs d’entrée affichés correspondent aux colonnes qui apparaissent dans le dataset de risque de crédit allemand d’origine. 

Permet d’entrer un jeu de données et puis cliquez sur **Test de requête-réponse**.

Les résultats du test seront affiche sur le côté droit de la page dans la colonne de sortie. 

Lorsque vous testez dans le portail Azure Machine formation Web Services, vous pouvez activer des exemples de données que vous pouvez utiliser pour tester le service de requête-réponse. Si vous avez créé le service Web dans le Studio d’apprentissage Machine, les exemples de données provient des données votre utilisé pour exercer votre modèle.

> [AZURE.TIP] La façon dont nous disposons de l’expérience prédictive configuré, l’ensemble de résultats à partir du [Modèle de Score] [ score-model] module sont retournés. Cela inclut toutes les données d’entrée ainsi que la valeur de risque de crédit et la probabilité de score. Si vous souhaitez renvoyer quelque chose de différent - par exemple, seulement le crédit des risques valeur - puis vous pouvez insérer un [Projet colonnes] [ project-columns] module entre le [Modèle de Score] [ score-model] et éliminer les colonnes que vous ne souhaitez pas que le service Web renvoie la **sortie du service Web** . 

## <a name="manage-the-web-service"></a>Gérer le service Web

**Gestion d’un service Web classique**

Une fois que vous avez déployé votre service Web classique, vous pouvez le gérer à partir du [Azure portal classique](https://manage.windowsazure.com).

1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com).
2. Dans le panneau de services Microsoft Azure, cliquez sur **Apprentissage automatique**.
3. Cliquez sur votre espace de travail.
4. Cliquez sur l’onglet de **serviceS Web** .
5. Cliquez sur le service Web, que nous avons créé.
6. Cliquez sur le point de terminaison « par défaut ».

À ce stade, vous pouvez faire surveiller le faire du service Web et effectuer des ajustements de performances en modifiant le nombre simultané appelle le service peuvent gérer.
Vous pouvez même publier votre service Web sur le marché d’Azure.

Pour plus d’informations, consultez :

- [Création de points de terminaison](machine-learning-create-endpoint.md)
- [Service Web de mise à l’échelle](machine-learning-scaling-webservice.md)
- [Publier le service Web de formation Azure Machine vers Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

**Gestion d’un service Web dans le portail Azure Machine formation Web Services**

Une fois que vous avez déployé votre service Web, standard ou nouveau, vous pouvez le gérer à partir du [portail de services Web de formation de Machine Azure](https://servics.azureml.net).

Pour surveiller les performances de votre service Web :

1. Connectez-vous au [portail de services Web de formation de Machine Azure](https://servics.azureml.net).
2. Cliquez sur **les services Web**.
3. Cliquez sur votre service Web.
4. Cliquez sur le **tableau de bord**.

----------

**Suivant : L' [accès au service Web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/