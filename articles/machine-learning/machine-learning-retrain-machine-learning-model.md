<properties
    pageTitle="Recyclage d’une Machine d’apprentissage du modèle | Microsoft Azure"
    description="Découvrez comment former un modèle et le service Web pour utiliser le modèle nouvellement formé dans Azure Machine formation de mise à jour."
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
    ms.date="10/10/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-machine-learning-model"></a>Recyclage d’une Machine d’apprentissage du modèle

Dans le cadre du processus d’operationalization des modèles d’apprentissage machine dans Azure d’apprentissage Machine, votre modèle est formé et enregistré. Vous l’utilisez ensuite pour créer un service Web predicative. Le service Web peut ensuite être consommé dans les sites web, des tableaux de bord et des applications mobiles. 

Les modèles que vous créez à l’aide d’apprentissage automatique ne sont généralement pas statiques. Lorsque de nouvelles données deviennent disponibles, ou lorsque le consommateur de l’API a leurs propres données le modèle doit être formés. 

Recyclage peut se produire fréquemment. Avec la fonctionnalité de programmation API de recyclage, vous pouvez par programmation former le modèle à l’aide de l’API de recyclage et mettre à jour le service Web avec le modèle nouvellement formé. 

Ce document décrit le processus de reconversion et vous montre comment utiliser les API de recyclage.

## <a name="why-retrain-defining-the-problem"></a>Pourquoi former : définition du problème  

Dans le cadre de la machine, processus de formation d’apprentissage, un modèle est formé à l’aide d’un ensemble de données. Les modèles que vous créez à l’aide d’apprentissage automatique ne sont généralement pas statiques. Lorsque de nouvelles données deviennent disponibles, ou lorsque le consommateur de l’API a leurs propres données le modèle doit être formés.

Dans ces scénarios, une API de programmation fournit un moyen pratique de permettre ou le consommateur de votre API pour créer un client qui peut, à titre exceptionnel ou régulier, former le modèle à l’aide de leurs propres données. Ils peuvent ensuite évaluer les résultats de recyclage et mise à jour de l’API du service Web pour utiliser le modèle nouvellement formé.

>[AZURE.NOTE] Si vous avez une expérience de formation existants et le service du nouveau site Web, vous souhaiterez peut-être extraire honorées d’un service Web prédictive existant au lieu de suivre la procédure pas à pas mentionnées dans la section suivante.

## <a name="end-to-end-workflow"></a>Workflow de bout en bout 

Le processus implique les éléments suivants : une formation expérience et une expérience prédictive est publié sous la forme d’un service Web. Pour permettre le recyclage d’un modèle de formation, l’expérience de formation doivent être publié sous la forme d’un service Web avec la sortie d’un modèle formé. Cela permet un accès API au modèle de recyclage. 

Les étapes suivantes s’appliquent aux services à la fois nouvelle et Web classique :

Créer le service Web prédictive initial :

* Créer une expérience de formation
* Créer une expérience web prédictive
* Déployer un service web prédictive

Former le service Web :

* Expérience de formation de mise à jour pour permettre au recyclage professionnels
* Déployer le service web de réadaptation
* Le code de Service d’exécution de traitement par lots permet de former le modèle

Pour une procédure pas à pas les étapes précédentes, consultez [modèles de dispenser une formation de Machine par programme](machine-learning-retrain-models-programmatically.md).

Si vous avez déployé un Service Web classique :

* Créer un nouveau point de terminaison du service Web prédictive
* Obtenir le correctif de l’URL et le code
* Utiliser l’URL des correctifs pour le nouveau point de terminaison sur le modèle retrained 

Pour une procédure pas à pas les étapes précédentes, reportez-vous à [dispenser un service Web classique](machine-learning-retrain-a-classic-web-service.md).

Si vous rencontrez des difficultés de la reconversion d’un service Web classique, consultez [résolution des problèmes liés à la reconversion d’un service Web classique de formation de Machine Azure](machine-learning-troubleshooting-retraining-models.md).

Si vous avez déployé un service du nouveau site Web :

* Connectez-vous à votre compte Azure le Gestionnaire de ressources
* Obtenir la définition de service Web
* Exporter la définition du Service Web en tant que JSON
* Mettre à jour la référence à la `ilearner` objet blob dans le JSON
* Importer le JSON dans une définition de Service Web
* Mise à jour du service Web avec la nouvelle définition de Service Web

Pour une procédure pas à pas les étapes précédentes, reportez-vous à [dispenser un service d’un site Web à l’aide des applets de commande PowerShell de gestion d’apprentissage Machine](machine-learning-retrain-new-web-service-using-powershell.md).

Le processus pour configurer le recyclage d’un service Web classique implique les étapes suivantes :

![Vue d’ensemble du processus de recyclage][1]

Le processus pour configurer le recyclage d’un service Web nouvelle implique les étapes suivantes :

![Vue d’ensemble du processus de recyclage][7]

## <a name="other-resources"></a>Autres ressources

- [Modèles de recyclage et de mise à jour d’une formation de Machine Azure avec Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
- [Créer les points de terminaison de service web et modèles d’apprentissage automatique de nombreux à partir d’une expérience à l’aide de PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md)
- La vidéo [AML recyclage des API à l’aide de modèles](https://www.youtube.com/watch?v=wwjglA8xllg) vous montre comment former des modèles d’apprentissage automatique créés dans l’apprentissage automatique de Azure à l’aide de l’API de recyclage et de PowerShell.

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

