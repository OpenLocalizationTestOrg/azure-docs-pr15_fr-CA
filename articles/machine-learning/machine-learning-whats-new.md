<properties
    pageTitle="Quelles sont les nouveautés de la formation de l’ordinateur | Microsoft Azure"
    description="Nouvelles fonctionnalités qui sont disponibles dans l’apprentissage automatique de Azure."
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
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="whats-new-in-azure-machine-learning"></a>Quelles sont les nouveautés dans l’apprentissage automatique Azure

###<a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>La version d’août 2016 des mises à jour de Microsoft Azure Machine Learning offrent les fonctionnalités suivantes :

* Les services Web classiques peuvent maintenant être gérés dans le nouveau portail de [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) qui fournit un emplacement unique pour gérer tous les aspects de votre service Web.   
    * Qui fournit le service web de [statistiques d’utilisation](machine-learning-manage-new-webservice.md).
    * Simplifie le test des appels à distance-demande de formation de Machine Azure à l’aide d’exemples de données.
    * Fournit une nouvelle page de test du Service d’exécution de traitement par lots avec l’historique des soumissions exemple données et travail.
    * Fournit la gestion simplifiée de point de terminaison.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>La version de juillet 2016 des mises à jour de Microsoft Azure Machine Learning offrent les fonctionnalités suivantes :

* Les services Web sont maintenant gérées comme ressources Azure gérés via les interfaces [Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md) , ce qui permet les améliorations suivantes :
    * Il existe de nouvelles [API de reste](https://msdn.microsoft.com/library/azure/Dn950030.aspx) à déployer et à gérer votre gestionnaire de ressources en fonction des services Web.
    * Il existe un nouveau portail de [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) qui fournit un emplacement unique pour gérer tous les aspects de votre service Web.
* Intègre un nouveau modèle de déploiement de service web sur abonnement, plusieurs régions à l’aide du Gestionnaire de ressources en fonction des API exploitant le fournisseur de ressources le Gestionnaire de ressources pour les Services Web.
* Présente les nouvelles fonctionnalités de gestion [tarifications](https://azure.microsoft.com/pricing/details/machine-learning/) et plan à l’aide du nouveau gestionnaire de ressources RP pour la facturation.
    * Vous pouvez maintenant [déployer votre service web pour plusieurs régions](machine-learning-how-to-deploy-to-multiple-regions.md) sans avoir à créer un abonnement dans chaque région.
* Fournit le service web de [statistiques d’utilisation](machine-learning-manage-new-webservice.md).
* Simplifie le test des appels à distance-demande de formation de Machine Azure à l’aide d’exemples de données.
* Fournit une nouvelle page de test du Service d’exécution de traitement par lots avec l’historique des soumissions exemple données et travail.

En outre, le Studio d’apprentissage Machine a été mis à jour pour vous permettre de déployer vers le nouveau modèle de service Web ou continuer à déployer sur le modèle de service Web classique. 
