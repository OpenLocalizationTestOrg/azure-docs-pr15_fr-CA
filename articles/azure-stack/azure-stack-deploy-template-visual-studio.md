<properties
    pageTitle="Déployer des modèles de Visual Studio dans la pile d’Azure | Microsoft Azure"
    description="Découvrez comment déployer des modèles de Visual Studio dans la pile d’Azure."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Déployer des modèles de pile Azure à l’aide de Visual Studio

Utilisez Visual Studio pour déployer des modèles de Azure le Gestionnaire de ressources pour le contrôle du concept pile Azure.

Modèles du Gestionnaire de ressources déploiement et configurer toutes les ressources de votre application dans une opération unique et coordonnée.

1.  Ouvrez la mise à jour de Visual Studio 2015 1.

2.  Cliquez sur **fichier**, cliquez sur **Nouveau**et dans la boîte de dialogue **Nouveau projet** , cliquez sur **Groupe de ressources Azure**.

3.  Entrez un **nom** pour le nouveau projet, puis cliquez sur **OK**.

4.  Dans la boîte de dialogue **Sélectionner un modèle Azure** , cliquez sur **Machine virtuelle Windows**, puis cliquez sur **OK**.

  Dans le nouveau projet, vous pouvez voir une liste des modèles disponibles en développant le nœud des **modèles** dans le volet de **L’Explorateur de solutions** .

5.  Dans le volet de **L’Explorateur de solutions** , double-cliquez sur le nom de votre projet, cliquez sur **déploiement**, puis cliquez sur **New Deployment**.

6.  Dans la boîte de dialogue **déployer au groupe de ressources** , dans le menu déroulant **abonnement** , sélectionnez votre abonnement Microsoft Azure pile.

7.  Dans la liste **Groupe de ressources** , choisissez un groupe de ressources existant ou créez-en un nouveau.

8.  Dans la liste **emplacement de groupe de ressources** , choisissez un emplacement, puis cliquez sur **déployer**.

9.  Dans la boîte de dialogue **Modifier les paramètres** , entrez des valeurs pour les paramètres (qui varient par modèle), puis cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

[Déployer des modèles de la ligne de commande](azure-stack-deploy-template-command-line.md)
