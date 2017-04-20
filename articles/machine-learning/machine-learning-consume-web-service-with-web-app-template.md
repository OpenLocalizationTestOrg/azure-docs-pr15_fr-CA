<properties
    pageTitle="Consommer un service web de formation de la Machine avec un modèle d’application web | Microsoft Azure"
    description="Pour consommer un service web prédictive dans Azure Machine Learning, utilisez un modèle d’application web dans Azure Marketplace."
    keywords="service Web, operationalization, l’API REST, machine d’apprentissage"
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
    ms.date="10/10/2016"
    ms.author="garye;raymondl"/>

# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Utilisation d’un service web de formation de Machine Azure avec un modèle d’application web

>[AZURE.NOTE] Cette rubrique décrit les techniques applicables à un service web classique. 

Une fois que vous avez développé votre modèle prédictif et déployé sous la forme d’un service web Azure Studio d’apprentissage Machine, ou à l’aide d’outils tels que R ou les Python, vous pouvez accéder au modèle d’operationalized à l’aide d’une API REST.

Il existe un certain nombre de façons d’utiliser l’API REST et accéder au service web. Par exemple, vous pouvez écrire une application C#, R ou les Python à l’aide de l’exemple de code généré lorsque vous avez déployé le service web (disponible sur la Page d’aide de API dans le tableau de bord de service web dans le Studio de formation de Machine). Ou vous pouvez utiliser l’exemple de classeur Microsoft Excel créé à votre intention (également disponible dans le tableau de bord de service web dans Studio).

Mais les modèles d’application Web disponible dans l' [Application de Web Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/)est le moyen le plus rapide et plus simple pour accéder à votre service web.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>L’ordinateur Azure apprentissage des modèles d’application Web

Modèles application web disponibles sur le marché d’Azure peuvent créer une application web personnalisée qui connaît les données d’entrée et les résultats attendus de votre service web. Il vous suffit de faire est de donner l’accès de l’application web à votre service web et les données et le modèle fait le reste.

Deux modèles sont disponibles :

- [Modèle d’application Azure ML de requête-réponse Service Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Modèle application Web Azure ML lot l’exécution du Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Chaque modèle crée un exemple d’application ASP.NET, à l’aide de l’URI de l’API et la clé de votre service web et il déploie comme un site web sur Azure. Le modèle de Service de requête-réponse (RR) crée une application web qui vous permet d’envoyer une ligne unique de données au service web pour obtenir un résultat unique. Le modèle de Service d’exécution de lot (BES) crée une application web qui vous permet d’envoyer de nombreuses lignes de données pour obtenir plusieurs résultats.

Aucun code n’est nécessaire pour utiliser ces modèles. Vous fournissez simplement la clé et l’URI de l’API et le modèle génère l’application pour vous.

## <a name="how-to-use-the-request-response-service-rrs-template"></a>Comment faire pour utiliser le modèle de Service de requête-réponse (RR)

Une fois que vous avez déployé votre service web, vous pouvez suivre les étapes ci-dessous pour utiliser le modèle application de web RR, comme illustré dans le diagramme suivant.

![Procédure pour utiliser le modèle de web de RR][image1]

1. Dans le Studio d’apprentissage Machine, ouvrez l’onglet **Web Services** , puis le service web que vous souhaitez accéder. Copiez la clé répertoriée sous la **clé d’API** et enregistrez-le.

    ![Clé de l’API][image3]

2. Ouvrez la Page d’aide API de **Demande/réponse** . En haut de la page d’aide, sous **requête**, copiez la valeur de **l’URI de la demande** et l’enregistrer. Cette valeur se présente comme suit :

        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

    ![URI de la demande][image4]

3. Accédez au [portail Azure](https://portal.azure.com), **connexion**, cliquez sur **Nouveau**, recherchez et sélectionnez **Azure ML réponse à la demande de Service Web App**, puis cliquez sur **créer**. 

    - Donner un nom unique à votre application web. L’URL de l’application web sera ce nom, suivi de `.azurewebsites.net.` , par exemple,`http://carprediction.azurewebsites.net.`

    - Sélectionnez l’abonnement Azure et les services sous lequel votre service web est en cours d’exécution.

    - Cliquez sur **créer**.

    ![Créer l’application web][image5]

4. Lorsque Azure a terminé le déploiement de l’application web, cliquez sur l' **URL** de la page de paramètres d’application web dans Azure, ou saisissez l’URL dans un navigateur web. Par exemple,`http://carprediction.azurewebsites.net.`

5. Lors de la première exécution de l’application web il vous demandera **l’API poster une URL** et la **Clé d’API**.
Entrez les valeurs que vous avez enregistré précédemment :
    - **URI de la demande** à partir de la Page d’aide API **API poster une URL**
    - **Clé de l’API** à partir du tableau de bord de service web pour la **Clé de l’API**.

    Cliquez sur **Envoyer**.

    ![Entrez l’URI de valider et de la clé d’API][image6]

6. L’application web affiche sa page de **Configuration d’application Web** avec les paramètres de service web en cours. Ici vous pouvez modifier les paramètres utilisés par l’application web.

    > [AZURE.NOTE] Modification des paramètres d’ici les modifications uniquement pour cette application web. Il ne change pas les paramètres par défaut de votre service web. Par exemple, si vous modifiez la **Description** ici qu’il ne change pas la description qui s’affichée sur le tableau de bord de service web dans le Studio de formation de Machine.

    Lorsque vous avez terminé, cliquez sur **Enregistrer les modifications**, puis cliquez sur **Atteindre la Page d’accueil**.

7. À partir de la page d’accueil, vous pouvez saisir les valeurs à envoyer à votre service web, cliquez sur **Envoyer**, et le résultat est renvoyé.

Si vous souhaitez revenir à la page de **Configuration** , passez à le `setting.aspx` page de l’application web. Par exemple : `http://carprediction.azurewebsites.net/setting.aspx.` vous serez invité à entrer la clé de l’API à nouveau, vous avez besoin pour accéder à la page et de mettre à jour les paramètres.

Vous pouvez arrêter, redémarrer ou supprimer l’application web du portail Azure comme n’importe quelle autre application web. Dans la mesure où il est en cours d’exécution, vous pouvez accédez à l’adresse web de base et entrez de nouvelles valeurs.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Comment faire pour utiliser le modèle de Service d’exécution de lot (BES)

Vous pouvez utiliser le modèle d’application de web BES de la même manière que le modèle de RR, sauf que l’application web qui est créée vous permettra d’envoyer plusieurs lignes de données et de recevoir plusieurs résultats.

Les résultats d’un service web de l’exécution du traitement par lots sont stockées dans un conteneur de stockage Azure ; les valeurs d’entrée peuvent provenir d’un fichier local ou de stockage Azure.
Par conséquent, vous aurez besoin d’un conteneur de stockage Azure pour contenir les résultats renvoyés par l’application web, et vous devez préparer vos données d’entrée.

![Procédure pour utiliser le modèle du web BES][image2]

1. Suivez la même procédure pour créer l’application web de BES que pour le modèle de RR, à l’exception de :
    - Pour obtenir l' **URI de la demande** à partir de la Page d’aide des API pour **l’Exécution du lot** pour le service web.
    - Accédez au [Modèle App de Azure ML lot l’exécution du Service Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) pour ouvrir le modèle BES sur Azure Marketplace et cliquez sur **Créer Web App**.

2. Pour spécifier où vous souhaitez que les résultats stockés, entrez les informations de conteneur de destination sur la page d’accueil web app. Également spécifier où l’application web peut obtenir les valeurs d’entrée, dans un fichier local ou dans un conteneur de stockage Azure.
Cliquez sur **Envoyer**.

    ![Informations sur le stockage][image7]

L’application web affichera une page ayant le statut de la tâche.
Lorsque le travail est terminé, vous aurez l’emplacement des résultats dans le stockage blob Azure. Vous avez également la possibilité de télécharger les résultats dans un fichier local.

## <a name="for-more-information"></a>Pour plus d’informations

Pour en savoir plus sur...

- Création d’une expérience d’apprentissage automatique avec le Studio d’apprentissage Machine, consultez [créer votre première expérience dans un Studio d’apprentissage Machine Azure](machine-learning-create-experiment.md)

- comment déployer votre apprentissage machine essayer sous la forme d’un service web, voir [déploiement d’un service web d’apprentissage Machine Azure](machine-learning-publish-a-machine-learning-web-service.md)

- autres façons d’accéder à votre service web, consultez [comment consommer un service web de formation de Machine Azure](machine-learning-consume-web-services.md)


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png
