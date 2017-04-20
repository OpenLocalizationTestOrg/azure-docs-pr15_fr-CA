<properties
    pageTitle="Création d’un atelier dans Azure DevTest Labs | Microsoft Azure"
    description="Création d’un atelier dans Azure DevTest Labs pour les machines virtuelles"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/12/2016"
    ms.author="tarcher"/>

# <a name="create-a-lab-in-azure-devtest-labs"></a>Création d’un atelier dans Azure DevTest Labs

## <a name="prerequisites"></a>Conditions préalables

Pour créer un atelier, vous devez :

- Un abonnement Azure. Pour en savoir plus sur les options d’achat d’Azure, voir [Comment acheter Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [version d’évaluation gratuite d’un mois](https://azure.microsoft.com/pricing/free-trial/). Vous devez être le propriétaire de l’abonnement pour créer les travaux pratiques.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Étapes de création d’un laboratoire dans Azure DevTest Labs

Les étapes suivantes illustrent comment utiliser le portail Azure pour créer un atelier dans Azure DevTest Labs. 

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez les **autres services**et sélectionnez **DevTest Labs** à partir de la liste.

1. Sur la lame **DevTest Labs** , sélectionnez **Ajouter**.

    ![Ajouter un laboratoire](./media/devtest-lab-create-lab/add-lab-button.png)

1. Sur la lame de **créer un laboratoire de DevTest** :

    1. Entrez le **Nom du laboratoire** pour le nouveau laboratoire.
    
    1. Sélectionnez l' **abonnement** à associer à l’atelier.
    
    1. Sélectionnez un **emplacement** dans lequel stocker le laboratoire.
    
    1. Sélectionnez **arrêt automatique** pour spécifier si vous souhaitez activer - et de définir les paramètres pour - la fermeture automatique de tous les travaux pratiques 's des ordinateurs virtuels.
    
    1. Sélectionnez le **type de stockage** pour indiquer le type de disque de stockage pour les machines virtuelles de l’atelier. 
    
    1. Sélectionnez **créer**.

    ![Créer une lame de laboratoire](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez créé votre laboratoire, voici quelques étapes à prendre en compte :

- [Sécuriser l’accès à un laboratoire](devtest-lab-add-devtest-user.md).

- [Définir des stratégies de l’atelier](devtest-lab-set-lab-policy.md).

- [Créer un modèle de laboratoire](devtest-lab-create-template.md).

- [Créer des artefacts personnalisés pour vos ordinateurs virtuels](devtest-lab-artifact-author.md).

- [Ajouter un ordinateur virtuel avec des artefacts à un laboratoire](devtest-lab-add-vm-with-artifacts.md).