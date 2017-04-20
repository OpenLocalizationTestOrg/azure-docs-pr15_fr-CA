<properties
    pageTitle="Étape 6 : Accède au service web apprentissage automatique | Microsoft Azure"
    description="Étape 6 de la développer une procédure pas à pas solution prédictive : accéder à un service web de formation de Machine Azure actif."
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


# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Procédure pas à pas, étape 6 : Accède au service web formation de Machine Azure

Il s’agit de la dernière étape de la procédure pas à pas, [développer une solution analytique prédictive dans l’apprentissage automatique de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Charger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.  [Créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Former et d’évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.  **Accéder au service web**

----------

Dans l’étape précédente de cette procédure pas à pas, nous avons déployé un service web qui utilise notre modèle de prévision de risque de crédit. Les utilisateurs doivent maintenant être en mesure d’envoyer des données et de recevoir des résultats. 

Le service web est un service web Azure qui peut recevoir et retourner des données à l’aide des API de reste de deux manières :  

-   **Demande/réponse** - l’utilisateur a envoie une ou plusieurs lignes de données de crédit au service à l’aide d’un protocole HTTP et le service répond avec un ou plusieurs jeux de résultats.
-   **L’exécution de lot** - l’utilisateur stocke une ou plusieurs lignes de données de crédit dans un blob Azure, puis envoie l’emplacement de l’objet blob au service. Le service évalue toutes les lignes de données dans le blob d’entrée stocke les résultats dans un autre blob et renvoie l’URL de ce conteneur.  

Il est le moyen le plus rapide et plus simple pour accéder au service web via les modèles d’application Web disponible dans l' [Application de Web Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).
Ces modèles d’application web peuvent créer une application web personnalisée qui connaît les données d’entrée de votre service web et qu’elle retourne. Tout ce que vous devez faire est permettent d’accéder à votre service web et les données et le modèle s’occupe du reste.

Pour plus d’informations sur les modèles d’application web, consultez [consommer un service web de formation de Machine Azure avec un modèle d’application web](machine-learning-consume-web-service-with-web-app-template.md).

Vous pouvez également développer une application personnalisée pour accéder au service web à l’aide d’un code de démarrage fourni par R, C# et les langages de programmation Python.
Vous trouverez plus de détails dans [comment consommer un service web de formation de Machine Azure qui a été publié à partir d’une Machine d’essai de formation](machine-learning-consume-web-services.md).
