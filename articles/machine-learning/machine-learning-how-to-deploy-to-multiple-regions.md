<properties
    pageTitle="Comment faire pour déployer un Service Web à plusieurs régions | Microsoft Azure"
    description="Étapes pour déployer les régions (copie), un Service Web à l’autre."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Comment faire pour déployer un Service Web à plusieurs régions

Les nouveaux Services Web Azure permettent de déployer facilement un service web à plusieurs régions, sans avoir besoin de plusieurs abonnements ou les espaces de travail. 

Le prix est la région spécifique, par conséquent, vous devez définir un plan de facturation pour chaque région dans laquelle vous allez déployer le service web.

## <a name="to-create-a-plan-in-another-region"></a>Pour créer un plan dans une autre région

1. Connexion à [l’apprentissage des Services Web de l’ordinateur Microsoft Azure](https://services.azureml.net/).
2. Cliquez sur l’option de menu de **Plans** .
3. Dans les Plans sur Afficher la page, cliquez sur **Nouveau**.
4. Dans la liste déroulante **abonnement** , sélectionnez l’abonnement dans lequel résidera le nouveau plan.
5. Dans la liste de **zone** déroulante, sélectionnez une région pour le nouveau plan. Les Options du Plan pour la zone sélectionnée s’affiche dans la section **Options du Plan** de la page.
6. Dans la liste déroulante **Groupe de ressources** , sélectionnez un groupe de ressources pour le plan. Plus d’informations sur les groupes de ressources, consultez [ressources de Azure de gérer via le portail](../azure-portal/resource-group-portal.md).
7. Dans le **Plan de nom** tapez le nom du plan.
8. Sous **Options de Plan**, cliquez sur le niveau de facturation pour le nouveau plan.
9. Cliquez sur **créer**.


## <a name="deploying-the-web-service-to-another-region"></a>Déploiement du service web dans une autre région

1. Cliquez sur l’option de menu **Web Services** .
2. Sélectionnez le Service Web que vous déployez sur une nouvelle région.
3. Cliquez sur **Copier**.
4. Dans la zone **Nom du Service Web**, tapez un nouveau nom pour le service web.
5. Dans la zone **description du service Web**, tapez une description pour le service web.
6. Dans la liste déroulante **abonnement** , sélectionnez l’abonnement dans lequel résidera le nouveau service web.
7. Dans la liste déroulante **Groupe de ressources** , sélectionnez un groupe de ressources pour le service web. Plus d’informations sur les groupes de ressources, consultez [ressources de Azure de gérer via le portail](../azure-portal/resource-group-portal.md).
8. Dans la liste de **zone** déroulante, sélectionnez la zone dans laquelle vous voulez déployer le service web.
9. Dans la liste déroulante **compte de stockage** , sélectionnez un compte de stockage dans lequel stocker le service web.
10. Dans la liste déroulante **Plan de prix** , sélectionnez un plan dans la région que vous avez sélectionné à l’étape 8.
11. Cliquez sur **Copier**.

