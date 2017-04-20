<properties
    pageTitle="Déployer l’ordinateur virtuel l’échelle définie à l’aide de Visual Studio | Microsoft Azure"
    description="Déployer des jeux d’échelle de Machine virtuelle à l’aide de Visual Studio et un modèle de gestionnaire de ressources"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="guybo"/>

# <a name="deploy-virtual-machine-scale-set-using-visual-studio"></a>Déployer l’ordinateur virtuel l’échelle définie à l’aide de Visual Studio

Cet article vous explique comment déployer un Azure Virtual Machine échelle définie à l’aide d’un déploiement de groupe de ressource Visual Studio.


[Jeux d’échelle Azure Machine virtuelle](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) sont une ressource Azure Compute pour déployer et gérer une collection des machines virtuelles similaires avec des options facilement intégrées pour ajuster automatiquement l’équilibrage de charge. Vous pouvez configurer et déployer des jeux d’échelle de machine virtuelle à l’aide de [modèles d’Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates). Modèles ARM peuvent être déployés à l’aide de la CLI d’Azure, PowerShell, reste ainsi directement à partir de Visual Studio. Visual Studio fournit un ensemble de modèles qui peuvent être déployés dans le cadre d’un projet de déploiement de groupe de ressources Azure d’exemple.

Déploiement de groupe de ressources Azure est un moyen de regrouper et de publier un ensemble de ressources Azure liées dans une opération de déploiement unique. Vous pouvez en savoir plus sur les ici : [Création et déploiement de groupes de ressources Azure via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Conditions préalables

Pour commencer à déployer des jeux d’échelle VM dans Visual Studio, vous devez les éléments suivants :

- Visual Studio 2013 ou 2015
- Azure SDK 2.7, 2.8 et 2.9

Remarque : Ces instructions supposent que vous utilisez Visual Studio 2015 avec [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Création d’un projet

1. Créez un nouveau projet dans Visual Studio 2015 en choisissant **fichier | Nouveau | Projet**.

    ![Nouveau fichier][file_new]

2. Sous **Visual C# | Nuage**, choisissez **Gestionnaire de ressources Azure** pour créer un projet de déploiement d’un modèle de ARM.

    ![Créer le projet][create_project]

3.  À partir de la liste des modèles, sélectionnez modèle de définir d’échelle d’ordinateur virtuel Windows ou Linux.

    ![Sélectionnez le modèle][select_Template]

4. Une fois votre projet créé vous verrez PowerShell scripts de déploiement, un modèle du Gestionnaire de ressources Azure et un fichier de paramètres pour l’ensemble de l’échelle de Machine virtuelle.

    ![L’Explorateur de solutions][solution_explorer]

## <a name="customize-your-project"></a>Personnaliser votre projet.

Maintenant, vous pouvez modifier le modèle afin de le personnaliser pour répondre aux besoins de votre application, telles que l’ajout de propriétés d’extension de mémoire virtuelle ou de la modification des règles d’équilibrage de charge. Par défaut, les modèles de valeur échelle machine virtuelle sont configurés pour déployer l’extension AzureDiagnostics qui permet de facilement ajouter des règles de l’échelle. Il déploie également un équilibreur de charge doté d’une adresse IP publique, configuré avec les règles de trafic entrant NAT qui vous permettent de vous connectez aux instances de machine virtuelle avec SSH (Linux) ou RDP (Windows), la plage de ports front-end commence à 50000, c'est-à-dire dans le cas de Linux, si vous SSH au port 50000 de l’adresse IP publique (ou le nom de domaine) vous seront acheminées vers le port 22 de première VM dans l’ensemble de l’échelle. Connexion au port 50001 doit être routé vers le port 22 de la machine virtuelle deuxième et ainsi de suite.

 Un bon moyen de modifier les modèles avec Visual Studio est le contour de JSON permet d’organiser les paramètres, les variables et les ressources. Avec une bonne compréhension du schéma Visual Studio peut pointer les erreurs dans votre modèle avant de le déployer.

![Explorateur JSON][json_explorer]

## <a name="deploy-the-project"></a>Déployer le projet

6. Déployer le modèle ARM sur Azure pour créer la ressource d’ordinateur virtuel échelle définie. Cliquez avec le bouton droit sur le nœud du projet, cliquez sur déploiement de **| Nouveau déploiement**.

    ![Déployer le modèle][5deploy_Template]

7. Sélectionnez votre abonnement dans la boîte de dialogue « Déployer au groupe de ressources ».

    ![Déployer le modèle][6deploy_Template]

8. À partir de là, vous pouvez également créer un nouveau groupe de ressources Azure pour déployer votre modèle.

    ![Nouveau groupe de ressources][new_resource]

9. Sélectionnez ensuite le bouton **Modifier les paramètres** à entrer des paramètres qui sont transmises à votre modèle, certaines valeurs telles que le nom d’utilisateur et mot de passe pour le système d’exploitation nécessaires à la création du déploiement. Si vous ne disposez d’outils de PowerShell pour Visual Studio installé, il est recommandé de vérifier « enregistrer les mots de passe » pour éviter une invite de ligne de commande PowerShell masquée ou utilisez [keyvault prend en charge](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).

    ![Modifier les paramètres][edit_parameters]

10. Maintenant, cliquez sur **déployer**. La fenêtre **sortie** affiche la progression du déploiement. Notez que le l’action s’exécute le script de **Déploiement-AzureResourceGroup.ps1** .

    ![Fenêtre Sortie][output_window]

## <a name="exploring-your-vm-scale-set"></a>Exploration de votre jeu d’échelle de machine virtuelle

Une fois le déploiement terminé, vous pouvez afficher la nouvelle machine virtuelle échelle définie dans le **Cloud Explorer** de Visual Studio (actualisation de la liste). Cloud Explorer vous permet de gérer les ressources Azure dans Visual Studio lors du développement d’applications. Vous pouvez également afficher votre échelle de machine virtuelle définie dans le [Portail Azure](https://portal.azure.com) et [Azure Explorateur de ressources](https://resources.azure.com/).

![Explorateur de nuage][cloud_explorer]

 Le portail fournit la meilleure façon de gérer votre infrastructure Azure avec un navigateur web, tandis que l’Explorateur de ressources Azure offre un moyen facile pour explorer visuellement et le débogage des ressources Azure, donnant une fenêtre dans la vue « instance » et également en affichant les commandes PowerShell pour les ressources que vous examinez. Jeux d’échelle de VM sont dans l’aperçu, l’Explorateur de ressources affiche le niveau le plus élevé pour vos jeux d’échelle de machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez déployé avec succès des jeux d’échelle machine virtuelle par le biais de Visual Studio, vous pouvez personnaliser davantage votre projet en fonction des besoins de votre application. Par exemple, paramètre d’échelle par l’ajout d’une ressource de perspectives, l’ajout d’infrastructure à votre modèle comme autonome d’ordinateurs virtuels ou déployer des applications à l’aide de l’extension de script personnalisé. Vous trouverez une bonne source d’exemples de modèles dans le référentiel de GitHub de [Modèles de démarrage rapide d’Azure](https://github.com/Azure/azure-quickstart-templates) (recherche de « vmss »).

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
