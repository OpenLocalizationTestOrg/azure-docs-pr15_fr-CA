<properties
   pageTitle="Les solutions de la Suite de gestion des opérations (OMS) | Microsoft Azure"
   description="Solutions étendent les fonctionnalités de la Suite Gestion des opérations (OMS) en fournissant des scénarios de gestion de package que les clients peuvent ajouter à leur espace de travail de l’OMS.  Cet article fournit des détails sur les solutions personnalisées créées par les clients et partenaires."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="management-solutions-in-operations-management-suite-oms-preview"></a>Solutions de gestion des opérations de gestion Suite (OMS) (aperçu)

>[AZURE.NOTE]Il s’agit d’une documentation préliminaire des solutions de gestion de l’OMS qui sont actuellement dans l’aperçu.    

Solutions de gestion étendent les fonctionnalités de la Suite Gestion des opérations (OMS) en fournissant des scénarios de gestion de package que les clients peuvent ajouter à leur environnement.  En plus des [solutions fournies par Microsoft](../log-analytics/log-analytics-add-solutions.md), les partenaires et les clients peuvent créer des solutions de gestion à utiliser dans leur propre environnement ou accessibles aux clients par le biais de la Communauté.

## <a name="finding-and-installing-management-solutions"></a>Recherche et installation des solutions de gestion
Il existe plusieurs méthodes pour situer et installer des solutions de gestion, comme décrit dans les sections suivantes.

### <a name="azure-marketplace"></a>Azure Marketplace
Solutions de gestion fournis par Microsoft et partenaires approuvés peuvent être installés à partir du marché Azure dans le portail Azure.

1. Connectez-vous au portail Azure.
2. Dans le volet gauche, sélectionnez **davantage de services**.
3. Faites défiler jusqu'à **Solutions** ou tapez des *solutions* dans la boîte de dialogue **filtre** .
4. Cliquez sur le bouton **+ Ajouter** .
5. Recherche de solutions qui vous intéressent en navigation, cliquez sur le bouton **filtre** ou tapant dans la zone de **Recherche Everthing** .
6. Cliquez sur un élément de marché pour afficher ses informations détaillées.
4. Cliquez sur **créer** pour ouvrir le volet **Ajouter la Solution** .
5. Vous devrez les informations nécessaires comme l' [espace de travail de l’OMS et compte d’Automation](#oms-workspace-and-automation-account) en plus des valeurs pour les paramètres de la solution.
6. Cliquez sur **créer** pour installer la solution.

### <a name="oms-portal"></a>Portail de l’OMS
Solutions de gestion proposées par Microsoft peuvent être installées à partir de la galerie de Solutions dans le portail de l’OMS.

1. Ouvrez une session sur le portail de l’OMS.
2. Cliquez sur la vignette de la **Galerie de Solutions** .
2. Dans la page Galerie de Solutions OMS, obtenir des informations sur chaque solution disponible. Cliquez sur le nom de la solution que vous souhaitez ajouter pour OMS.
3. Sur la page pour la solution que vous avez choisi, des informations détaillées sur la solution s’affiche. Cliquez sur **Ajouter**.
4. Une nouvelle vignette pour la solution que vous avez ajouté s’affiche dans l’aperçu de page OMS et vous pouvez commencer à l’utiliser une fois que le service OMS traite les données.

### <a name="azure-quickstart-templates"></a>Modèles d’Azure Quickstart
Membres de la Communauté peuvent soumettre les solutions de gestion des modèles de démarrage rapide d’Azure.  Vous pouvez télécharger ces modèles pour une installation ultérieure ou les inspecter pour savoir comment [créer vos propres solutions](#creating-a-solution).

1. Suivez la procédure décrite dans l' [espace de travail de l’OMS et le compte de l’automatisation](#oms-workspace-and-automation-account) pour lier un compte et un espace de travail.
2. Atteindre [les modèles Quickstart Azure](https://azure.microsoft.com/documentation/templates/).  
3. Recherche d’une solution qui vous intéresse.
4. Sélectionnez la solution à partir des résultats pour afficher ses détails.
5. Cliquez sur le bouton **déployer vers Azure** .
6. Vous devrez fournir des informations telles que le groupe de ressources et l’emplacement en plus des valeurs pour les paramètres de la solution.
7. Cliquez sur **achat** pour installer la solution.

### <a name="deploy-azure-resource-manager-template"></a>Déployer le modèle de gestionnaire de ressources Azure
Solutions que vous obtenez à partir de la Communauté ou que vous [créez vous-même](#creating-a-solution) sont implémentées en tant que gestionnaire de ressources du modèle, et vous pouvez utiliser une des méthodes standard pour le [déploiement d’un modèle](../resource-group-template-deploy-portal.md).  Notez qu’avant d’installer la solution, vous devez créer et lier l' [espace de travail de l’OMS et le compte de l’Automation](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>Espace de travail de l’OMS et le compte de l’Automation
La plupart des solutions de gestion nécessitent un [espace de travail de l’OMS](../log-analytics/log-analytics-manage-access.md) pour contenir des vues et un [compte d’Automation](../automation/automation-security-overview.md#automation-account-overview) pour contenir des procédures opérationnelles et les ressources associées. L’espace de travail et le compte doivent remplir les conditions suivantes.

- Une solution peut utiliser uniquement un espace de travail OMS et un seul compte d’Automation.  
- L’espace de travail de l’OMS et compte d’Automation utilisé par une solution doivent être liés à un autre. Un espace de travail OMS peut être lié uniquement à un seul compte d’automatisation et un automatisation compte peut uniquement être lié à un espace de travail OMS.
- Pour être lié, l’espace de travail de l’OMS et le compte d’Automation doivent être dans le même groupe de ressources et la région.  L’exception est un espace de travail dans la région des États-Unis OMS et et compte d’Automation dans la version américaine est 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Création d’un lien entre un espace de travail de l’OMS et le compte de l’Automation
Comment vous spécifiez l’espace de travail OMS et compte d’Automation dépend de la méthode d’installation de votre solution.

- Lorsque vous installez une solution Microsoft via le portail de l’OMS, il est installé dans l’espace de travail actuel de l’OMS, et aucun compte d’Automation n’est requis.

- Lorsque vous installez une solution via le Marketplace Azure, vous êtes invité pour un espace de travail de l’OMS et le compte de l’automatisation et le lien entre les deux est créé pour vous.  

- Pour les solutions hors du marché d’Azure, vous devez lier le compte de l’Automation et un espace de travail de l’OMS avant d’installer la solution.  Pour cela, en sélectionnant toutes les solutions sur le marché d’Azure, l’espace de travail de l’OMS et le compte de l’Automation.  Vous n’êtes pas obligé d’installer la solution, car le lien sera créé dès que l’espace de travail de l’OMS et compte d’Automation sont sélectionnées.  Une fois le lien créé, vous pouvez utiliser cet espace de travail de l’OMS et le compte d’Automation pour n’importe quelle solution. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Vérification de la liaison entre un espace de travail de l’OMS et le compte de l’Automation
Vous pouvez vérifier le lien entre un espace de travail de l’OMS et un compte d’Automation à l’aide de la procédure suivante.

1. Sélectionnez le compte de l’Automation dans le portail Azure.
2. Faites défiler vers le bas du volet **paramètres** .
3. S’il existe une section appelée **OMS ressources** dans le volet **paramètres** , ce compte est associé à un espace de travail de l’OMS.
4. Sélectionnez **espace de travail** pour afficher les détails de l’espace de travail OMS liée à ce compte d’Automation.


## <a name="listing-management-solutions"></a>Solutions de gestion de liste
Utilisez la procédure suivante pour afficher les solutions de gestion dans les espaces de travail liés à votre abonnement Azure.

1. Connectez-vous au portail Azure.
2. Dans le volet gauche, sélectionnez **davantage de services**.
3. Faites défiler jusqu'à **Solutions** ou tapez des *solutions* dans la boîte de dialogue **filtre** .
4. Solutions installées dans tous vos espaces de travail apparaît.

Notez que vous pouvez afficher uniquement les solutions Microsoft installées dans l’espace de travail actuel à l’aide du portail de l’OMS.

## <a name="removing-a-management-solution"></a>Suppression d’une solution de gestion
Lorsqu’une solution de gestion est supprimée, toutes les ressources de la solution sont également supprimés.  

1. Recherchez la solution dans le portail Azure à l’aide de la procédure dans la [liste des solutions](#listing-solutions).
2. Sélectionnez la solution que vous souhaitez supprimer.
3. Cliquez sur le bouton **Supprimer** .

## <a name="creating-a-management-solution"></a>Création d’une solution de gestion
Guide pas à pas sur la création de solutions de gestion sont disponibles à la [Création de solutions dans Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 


## <a name="next-steps"></a>Étapes suivantes

- Rechercher des [Modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates) des exemples des différents modèles de gestionnaire de ressources.
- Créer vos propres [solutions de gestion](operations-management-suite-solutions-creating.md).
 