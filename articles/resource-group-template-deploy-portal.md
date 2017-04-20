<properties 
    pageTitle="Permet de déployer des ressources Azure portal Azure | Microsoft Azure" 
    description="Utilisez Azure portal et gérer des ressources d’Azure pour déployer vos ressources." 
    services="azure-resource-manager,azure-portal" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Déployer les ressources avec le Gestionnaire de ressources modèles et Azure portal

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cette rubrique montre comment utiliser le [portail Azure](https://portal.azure.com) avec [Le Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) pour déployer vos ressources Azure. Pour en savoir plus sur la gestion de vos ressources, consultez [ressources de gérer l’Azure via le portail](./azure-portal/resource-group-portal.md).

Actuellement, pas tous les services prend en charge le portail ou le Gestionnaire de ressources. Pour ces services, vous devez utiliser le [portail classique](https://manage.windowsazure.com). L’état de chaque service, reportez-vous à la section [graphique de la disponibilité de portail Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="create-resource-group"></a>Créer le groupe de ressources

1. Pour créer un groupe de ressources vide, sélectionnez **Nouveau** > **gestion** > **Groupe de ressources**.

    ![créer le groupe de ressources vide](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. Donnez-lui un nom et un emplacement et, si nécessaire, sélectionnez un abonnement. Vous devez fournir un emplacement pour le groupe de ressources, car le groupe de ressources stocke des métadonnées concernant les ressources. Pour des raisons de conformité, vous souhaiterez spécifier où les métadonnées sont stockées. En général, nous recommandons que vous spécifiez un emplacement dans lequel se trouve la plupart de vos ressources. À partir du même emplacement permet de simplifier votre modèle.

    ![regroupe les valeurs de jeu](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>Déployer des ressources à partir du Marketplace

Après avoir créé un groupe de ressources, vous pouvez déployer les ressources lui depuis le site Marketplace. Sur le marché fournit des solutions prédéfinies pour les scénarios courants.

1. Pour démarrer un déploiement, sélectionnez **Nouveau** et le type de ressource que vous souhaitez déployer. Ensuite, recherchez la version de la ressource que vous souhaitez déployer.

    ![déployer des ressources](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. Si vous ne voyez pas la solution particulière que vous souhaitez déployer, vous pouvez le rechercher sur le marché.

    ![Recherchez sur marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

3. Selon le type de ressource sélectionné, vous disposez d’une collection de propriétés à définir avant le déploiement. Ces options ne sont pas affichées ici, comme ils varient selon le type de ressource. Pour tous les types, vous devez sélectionner un groupe de ressources de destination. L’image suivante montre comment créer une application web et le déployer vers le groupe de ressources que vous avez créé.

    ![créer le groupe de ressources](./media/resource-group-template-deploy-portal/select-existing-group.png)

    Sinon, vous pouvez décider de créer un groupe de ressources lors du déploiement de vos ressources. Sélectionnez **Créer nouveau** et nommez le groupe de ressources.

    ![créer le nouveau groupe de ressources](./media/resource-group-template-deploy-portal/select-new-group.png)

4. Votre déploiement commence. Le déploiement peut prendre quelques minutes. Lorsque le déploiement est terminé, vous voyez une notification.

    ![afficher la notification](./media/resource-group-template-deploy-portal/view-notification.png)

5. Après le déploiement de vos ressources, vous pouvez ajouter davantage de ressources pour le groupe de ressources à l’aide de la commande **Ajouter** sur la lame de groupe de ressources.

    ![ajouter des ressources](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Déployer les ressources de modèle personnalisé

Si vous voulez effectuer un déploiement mais pas utiliser tous les modèles sur le marché, vous pouvez créer un modèle personnalisé qui définit l’infrastructure de votre solution. Pour en savoir plus sur la création de modèles, consultez [modèles de création d’un gestionnaire de ressources Azure](resource-group-authoring-templates.md).

1. Pour déployer un modèle personnalisé via le portail, sélectionnez **Nouveau**et lancez la recherche pour le **Modèle de déploiement** jusqu'à ce que vous pouvez le sélectionner à partir des options.

    ![déploiement de modèle de recherche](./media/resource-group-template-deploy-portal/search-template.png)

2. Sélectionnez le **Modèle de déploiement** à partir des ressources disponibles.

    ![Sélectionnez modèle déploiement](./media/resource-group-template-deploy-portal/select-template.png)

3. Après avoir lancé le déploiement du modèle, ouvrez le modèle vide qui est disponible pour la personnalisation.

    ![créer le modèle](./media/resource-group-template-deploy-portal/show-custom-template.png)

    Dans l’éditeur, ajoutez la syntaxe JSON qui définit les ressources que vous souhaitez déployer. Cliquez sur **Enregistrer** lorsque vous avez terminé. Pour obtenir des conseils sur l’écriture de la syntaxe JSON, consultez [procédure pas à pas de gestionnaire de ressources du modèle](resource-manager-template-walkthrough.md).

    ![modifier le modèle](./media/resource-group-template-deploy-portal/edit-template.png)

4. Ou bien, vous pouvez sélectionner un modèle existant à partir des [modèles de quickstart Azure](https://azure.microsoft.com/documentation/templates/). Ces modèles sont fournis par la Communauté. Ils couvrent les nombreux scénarios courants et une personne peut avoir ajouté un modèle semblable à ce que vous essayez de déployer. Vous pouvez rechercher les modèles pour trouver quelque chose qui correspond à votre scénario.

    ![Sélectionnez le modèle de démarrage rapide](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    Vous pouvez afficher le modèle sélectionné dans l’éditeur.

5. Après avoir fourni toutes les autres valeurs, sélectionnez **créer** le modèle de déploiement. 

    ![déployer le modèle](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Déployer des ressources à partir d’un modèle enregistré dans votre compte

Le portail vous permet de vous permet d’enregistrer un modèle pour votre compte Azure et redéployer ultérieurement. Pour plus d’informations sur l’utilisation de ces enregistré des modèles, [mise en route des modèles privés sur le portail Azure](./marketplace-consumer/mytemplates-getstarted.md).

1. Pour rechercher vos modèles enregistrés, sélectionnez **Parcourir** > **modèles**.

    ![Parcourir les modèles](./media/resource-group-template-deploy-portal/browse-templates.png)

2. Dans la liste des modèles enregistrés dans votre compte, sélectionnez celui que vous souhaitez travailler.

    ![modèles enregistrés](./media/resource-group-template-deploy-portal/saved-templates.png)

3. Sélectionnez **déploiement** de redéployer ce modèle enregistré.

    ![déployer le modèle enregistré](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour afficher les journaux d’audit, voir [Auditer les opérations effectuées avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour résoudre les erreurs de déploiement, consultez [déploiements de groupe de ressources de résolution des problèmes avec Azure portal](resource-manager-troubleshoot-deployments-portal.md).
- Pour récupérer un modèle à partir d’un déploiement ou d’un groupe de ressources, consultez [modèle de gestionnaire de ressources Azure exporter à partir de ressources existantes](resource-manager-export-template.md).





