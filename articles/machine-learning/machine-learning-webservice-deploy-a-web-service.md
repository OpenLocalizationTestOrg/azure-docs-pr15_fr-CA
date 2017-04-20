<properties
   pageTitle="Déploiement d’un nouveau Service Web"
   description="Le flux de travail de déploiement d’un ARM en fonction de service web"
   services="machine-learning"
   documentationCenter=""
   authors="vDonGlover"
   manager="raymondl"
   editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>

# <a name="deploy-a-new-web-service"></a>Déployer un nouveau service web

Microsoft Azure Machine maintenant de formation fournit des services web qui reposent sur [Le Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md) permettant de nouvelles options de plan de facturation et le déploiement de votre service web dans plusieurs régions.

Le flux de travail général pour déployer un service web à l’aide des Services Web de Microsoft Azure Machine Learning est la suivante :

* Créer une expérience prédictive
* déployer
* configurer son nom
* plan de facturation
* tester
* le consommer.

Le graphique suivant illustre le flux de travail.

![Workflow de déploiement du service Web][1]
 
## <a name="deploy-web-service-from-studio"></a>Déployer le service web à partir de Studio 

Pour déployer une expérience en tant qu’un nouveau service web. Signer dans le Studio de formation de Machine et créer un nouveau service web prédictive. 

**Remarque**: Si vous avez déjà déployé une expérience sous la forme d’un service web classique vous ne pouvez pas le déployer comme un nouveau service web.
 
Cliquez sur **exécuter** en bas de la zone d’essai et puis cliquez sur **Déployer le Service Web** et **Déploiement du Service Web [nouveau]**. La page de déploiement du Gestionnaire de Service Web de formation Machine s’ouvre.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Le Gestionnaire de Service de machine formation Web déployer Page d’expérience
Dans la page déployer l’expérimentation, entrez un nom pour le service web.
Sélectionnez un plan de tarification. Si vous avez un plan de tarification existant, que vous pouvez le sélectionner, sinon vous devez créer un nouveau plan de prix pour le service. 

1.  Dans le **Plan du prix** de la liste déroulante, sélectionnez un plan existant ou l’option **Sélectionner le nouveau plan** .
2.  Dans **Nom de Plan**, entrez un nom qui identifiera le plan sur votre facture.
3.  Sélectionnez un des **Niveaux de Plan mensuel**. Notez que la valeur par défaut des niveaux de plan pour les plans pour votre région par défaut et votre service web est déployée dans cette zone.

Cliquez sur **déployer** et la page de démarrage rapide pour votre service de web s’ouvre.

## <a name="quickstart-page"></a>Page de démarrage rapide
La page de démarrage rapide de service web vous donne accès et conseils sur les tâches les plus courantes que vous aurez à effectuer après la création d’un nouveau service web. À partir de là, vous pouvez facilement accéder à la fois la page de **consommer** et page de **Test** .

## <a name="testing-your-web-service"></a>Test de votre service web

À partir de la page de démarrage rapide, cliquez sur service de Test web sous tâches courantes.   

Pour tester le service web sous la forme d’un Service de requête-réponse (RR) :

* Sur la barre de menus, cliquez sur **Test** .
* Cliquez sur **Requête-réponse**.
* Entrez les valeurs appropriées pour les colonnes d’entrée de votre expérience.
* Cliquez sur Test **Requête-réponse**.

Vous, les résultats s’affiche sur le côté droit de la page.

Pour tester un service web de Service d’exécution de lot (BES), vous utiliserez un fichier CSV :

* Sur la barre de menus, cliquez sur **Test** .
* Cliquez sur **traitement par lots**.
* Sous votre entrée, cliquez sur Parcourir et naviguez vers votre fichier de données d’exemple.
* Cliquez sur **Test**.

L’état de votre test s’affiche sous **Test des traitements par lots**.

## <a name="consuming-your-web-service"></a>Utilisation de votre Service Web

Lors du déploiement sous la forme d’un service web, expériences d’apprentissage automatique de Azure fournissent une API de reste pouvant être consommée par un large éventail de plates-formes et de périphériques. C’est parce que l’API REST de simple accepte et répond avec JSON des messages au format. Le portail de formation de Machine Azure fournit un code qui peut être utilisé pour appeler le service web de R, C# et Python.
 
Sur la page de la consommation, vous trouverez :

* La clé d’API et de l’URI pour la consommation du service web dans les applications.
* Modèles d’app Excel et le web pour démarrer votre processus de consommation.
* Exemple de code C#, python et R pour vous aider à démarrer.

Pour plus d’informations sur la consommation des services web, reportez-vous à la section [comment consommer un service web de formation de Machine Azure qui a été déployé à partir d’une Machine d’expérience d’apprentissage](machine-learning-consume-web-services.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la consommation des services web, consultez :

[Comment consommer un service web de formation de Machine Azure qui a été déployé à partir d’une Machine d’essai de formation](machine-learning-consume-web-services.md)

[Services Web de formation Azure Machine : Déploiement et la consommation](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->
