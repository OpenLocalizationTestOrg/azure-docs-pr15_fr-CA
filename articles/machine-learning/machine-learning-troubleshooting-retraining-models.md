<properties
    pageTitle="Résolution des problèmes liés à la Retraining d’un service Web classique de formation de Machine Azure | Microsoft Azure"
    description="Identifier et corriger les rencontré de problèmes courants lorsque vous sont former le modèle pour un Service Web de Azure Machine Learning."
    services="machine-learning"
    documentationCenter=""
    authors="VDonGlover"
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

# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Résolution des problèmes liés à la reconversion d’un service Web classique de formation de Machine Azure

## <a name="retraining-overview"></a>Vue d’ensemble de recyclage

Lorsque vous déployez une expérience prédictive sous la forme d’un service web de score, il est un modèle statique. Lorsque de nouvelles données deviennent disponibles, ou lorsque le consommateur de l’API a ses propres données, le modèle doit être formés. 

Pour une procédure pas à pas complète du processus de reconversion d’un service Web classique, consultez [Former Machine Learning modèles par programme](machine-learning-retrain-models-programmatically.md).

## <a name="retraining-process"></a>Recyclage de processus

Lorsque vous avez besoin former le service Web, vous devez ajouter quelques éléments supplémentaires :

* Un service Web déployé à partir de l’expérience de formation. L’expérience doit être un module de **Sortie du Service Web** associé à la sortie du module **Modèle de Train** .  

    ![Joindre la sortie du service web pour le modèle de train.][image1]

* Un nouveau point de terminaison ajouté à votre service Web score.  Vous pouvez ajouter le point de terminaison par programme à l’aide de l’exemple de code référencé par programmation dans les modèles de dispenser une formation de Machine rubrique ou via le portail classique Azure.

Vous pouvez ensuite utiliser l’exemple C# de code à partir de la page d’aide du Service Web Formation API pour former le modèle. Une fois que vous avez évalué les résultats et que vous conviennent, vous mettez à jour le modèle formé de score de service web en utilisant le nouveau point de terminaison que vous avez ajouté.

Avec tous les éléments en place, les principales étapes à suivre pour former le modèle sont les suivants :

1.  Appeler le Service Web de formation : l’appel est pour le lot d’exécution Service (BES), pas la demande de réponse Service (RR). Vous pouvez utiliser l’exemple C# de code sur la page d’aide de l’API pour effectuer l’appel. 
2.  Rechercher les valeurs de *BaseLocation*, *RelativeLocation*et *SasBlobToken*: ces valeurs sont retournées dans la sortie de votre appel au Service Web formation. 
      ![affichage de la sortie de l’exemple de reconversion et des valeurs BaseLocation, RelativeLocation et SasBlobToken.][image6]
3.  Mettre à jour le point de terminaison ajouté à partir du service web de score avec le nouveau modèle formé : à l’aide de l’exemple de code fourni dans les modèles de dispenser une formation de Machine par programme, de mettre à jour le nouveau point de terminaison que vous avez ajouté au modèle de score avec le modèle nouvellement formé à partir du Service Web de formation.

## <a name="common-obstacles"></a>Obstacles courants

### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Vérifiez si vous avez l’URL correcte des correctifs

L’URL des correctifs que vous utilisez doit être celui associé à la nouvelle extrémité de score que vous avez ajouté au service Web score. Il existe un certain nombre de méthodes pour obtenir l’URL des correctifs :

**Option 1 : par programmation**

Pour obtenir l’URL correcte des correctifs :

1.  Exécutez l’exemple de code [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2.  À partir de la sortie de AddEndpoint, trouve la valeur *HelpLocation* et copiez l’URL.

    ![HelpLocation dans la sortie de l’exemple addEndpoint.][image2]

3.  Collez l’URL dans un navigateur pour accéder à une page qui fournit des liens d’aide du service Web.
4.  Cliquez sur le lien de **Mise à jour de ressources** pour ouvrir la page d’aide du correctif.

**Option 2 : Utiliser le portail classique Azure**

1.  Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com).
2.  Ouvrez l’onglet formation de la Machine. 
     ![Onglet incliné de machine.][image4]
3.  Cliquez sur le nom de votre espace de travail, puis les **Services Web**.
4.  Cliquez sur le service Web score que vous utilisez. (Si vous n’avez pas modifié le nom par défaut du service web, il se termine par [notation Exp.].)
5.  Cliquez sur **Ajouter un point de terminaison**.
6.  Après que le point de terminaison est ajouté, cliquez sur le nom de point de terminaison. Puis, cliquez sur la **Ressource de mise à jour** pour ouvrir la page d’aide de gestion des correctifs.

>[AZURE.NOTE] Si vous avez ajouté le point de terminaison pour le Service Web de formation au lieu du Service Web prédictive, vous recevrez l’erreur suivante lorsque vous cliquez sur le lien de la **Ressource de mise à jour** : nous sommes désolés, mais cette fonctionnalité n’est pas pris en charge ou disponible dans ce contexte. Ce Service Web n’a pas de ressources mis à jour. Nous nous excusons de la gêne occasionnée et que vous travaillez sur l’amélioration de ce flux de travail.

![Nouveau tableau de bord de point de terminaison.][image3]

La page d’aide de correctif contient l’URL des correctifs, vous devez utiliser et fournit des exemples de code que vous permet de l’appeler.

![URL du correctif.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Vérifiez que vous mettez à jour le point de terminaison correct score

* Des correctifs pas le Service Web de formation : l’opération de patch doit être effectuée sur le service Web de score.
* Des correctifs pas le point de terminaison par défaut dans le service Web : l’opération de patch doit être effectuée sur le nouveau score Web service point de terminaison que vous avez ajouté.

Vous pouvez vérifier le service Web auquel le point de terminaison est sur en visitant le portail classique Azure. 

>[AZURE.NOTE] Assurez-vous que vous ajoutez le point de terminaison pour le Service Web prédictive, pas le Service Web de formation. Si vous avez correctement déployé à la fois une formation et un Service Web prédictive, vous devriez voir deux services Web distincts sont répertoriés. Le Service Web prédictive doit se terminer par « [prédictive exp.] ».

1.  Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com).
2.  Ouvrez l’onglet formation de la Machine. 
     ![Espace de travail formation l’interface utilisateur de l’ordinateur.][image4]
3.  Sélectionnez votre espace de travail.
4.  Cliquez sur **les Services Web**.
5.  Sélectionnez votre Service Web prédictive.
6.  Vérifiez que votre nouveau point de terminaison a été ajouté au service Web.

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Vérifiez l’espace de travail figurant dans votre service web pour s’assurer qu’il est dans votre région

1.  Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com).
2.  Sélectionnez apprentissage automatique dans le menu.
      ![Région d’apprentissage machine l’interface utilisateur.][image4]
3.  Vérifiez l’emplacement de votre espace de travail.

<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/machine-learning-troubleshooting-retraining-a-model/web-services-tab.png