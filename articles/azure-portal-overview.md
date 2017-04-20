<properties
    pageTitle="Vue d’ensemble du portail de Microsoft Azure"
    description="Apprenez à utiliser le portail Microsoft Azure."
    services=""
    documentationCenter=""
    authors="davidwrede"
    manager="dwrede"
    editor="jimbe"/>

<tags
    ms.service="na"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/16/2015"
    ms.author="dwrede"/>

# <a name="microsoft-azure-portal-overview"></a>Vue d’ensemble du portail de Microsoft Azure

Le portail de Microsoft Azure est un emplacement central où vous pouvez configurer et gérer vos ressources Azure.  Ce didacticiel va vous familiariser avec le portail et vous montrer comment utiliser certaines de ces fonctionnalités clées :
- **Marketplace complète** qui vous permet de parcourir des milliers d’éléments à partir de Microsoft et d’autres fournisseurs qui peuvent être achetés et/ou mis en service.
- Une **expérience de navigation évolutive et unifiée** qui permet de facilement trouver les ressources qui vous intéressent et effectuez diverses opérations de gestion.
- **Pages de gestion cohérente** (ou lames) qui vous permettent de gérer large éventail d’Azure de services de manière cohérente d’exposition des paramètres, des actions, facturation informations, données de surveillance et d’utilisation de la santé et beaucoup plus.
- Une **expérience personnelle** qui vous permet de créer un écran de démarrage personnalisé qui affiche les informations que vous voulez voir à chaque fois que vous vous connectez.  Vous pouvez également personnaliser tous les blades de gestion qui contiennent des mosaïques.

 ![Orientation de l’interface utilisateur du portail Azure][UIOrientation]

## <a name="before-you-get-started"></a>Avant de commencer

Vous aurez besoin d’un abonnement Azure valide pour parcourir le didacticiel.  Si vous n’en avez pas, puis [Inscrivez-vous pour une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/) dès aujourd'hui.  Une fois que vous avez un abonnement, vous pouvez accéder au portail à [https://portal.azure.com].

## <a name="how-to-create-a-resource"></a>Comment faire pour créer une ressource

Azure a un marché avec des milliers d’éléments que vous pouvez créer à partir d’un seul endroit.  Supposons que vous souhaitez créer un nouvel ordinateur virtuel Windows Server 2012.  Le + nouveau concentrateur est votre point d’entrée dans un ensemble curated de catégories disponibles depuis le site marketplace.  Chaque catégorie comporte un petit jeu d’objets a l’une ainsi qu’un lien vers le marché complet qui affiche toutes les catégories et recherche. Pour créer ce nouvel ordinateur virtuel Windows Server 2012, effectuez les actions suivantes :  

1.  Windows Server 2012 est proposé, et vous pouvez le sélectionner à partir de la catégorie de calcul.  
2.  Remplir certaines entrées base sur un formulaire.
3.  Cliquez sur 'Créer' et commence à fournir immédiatement votre machine virtuelle.

Le concentrateur de notifications vous avertit lorsque la ressource a été créée et une blade de gestion s’ouvre (vous pouvez toujours parcourir aux ressources plus loin).

![Catégories de portail][PortalCategories]


## <a name="how-to-find-your-resources"></a>Comment trouver vos ressources

Vous pouvez épingler les ressources fréquemment sollicitées pour votre startboard, mais vous devrez peut-être accéder à quelque chose qui vous n’accédez fréquemment.  Le concentrateur de parcourir ci-dessous est un moyen pour accéder à l’ensemble de vos ressources.  Vous pouvez filtrer par abonnement, choisissez ou redimensionner des colonnes et naviguer jusqu'à les blades de gestion en cliquant sur des éléments individuels.

![Parcourir le concentrateur][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Comment gérer et déléguer l’accès à une ressource

À partir de cette lame, que vous pouvez vous connecter à l’ordinateur virtuel à l’aide du Bureau à distance, surveiller les mesures de performances clés, contrôler l’accès à cet ordinateur virtuel à l’aide de l’accès basée sur les rôles (RBAC), configurer l’ordinateur virtuel et effectuer d’autres tâches de gestion important.  Délégation de l’accès en fonction du rôle est essentielle pour la gestion à l’échelle.  Cliquez [ici](./active-directory/role-based-access-control-configure.md) pour en savoir plus à ce sujet. Pour déléguer l’accès à une ressource, effectuez les actions suivantes :

1.  Accédez à votre ressource.
2.  Cliquez sur tous les paramètres dans la section Essentials.
3.  Dans la liste Paramètres, cliquez sur « Utilisateurs ».
4.  Dans la barre de commandes, cliquez sur 'Ajouter'.
5.  Sélectionnez un utilisateur et un rôle.

![Gestion d’une ressource][ManageResource]

## <a name="how-to-customize-a-resource-blade"></a>Comment personnaliser une lame de ressource

Azure préconfigure les lames pour vos ressources, mais les mosaïques sur ces lames sont vôtre au contrôle.  Vous pouvez facilement accéder en personnaliser le mode pour ajouter, supprimer, redimensionner ou réorganiser les mosaïques. Pour personnaliser une lame, effectuez les actions suivantes :

1.  Accédez à votre ressource.
2.  Cliquez sur le '...' en haut de la lame, vous souhaitez personnaliser.
3.  Cliquez sur « Ajouter parties ».
4.  Démarrer le glisser-déplacer des éléments.  

![Personnalisation des lames][CustomizeBlades]

## <a name="how-to-get-help"></a>Comment obtenir de l’aide

Si vous avez toujours un problème, nous sommes là pour vous.  Le portail a une page d’aide et de support qui peut vous pointez dans la bonne direction.  En fonction de votre [plan de prise en charge](https://azure.microsoft.com/support/plans/), vous pouvez également créer des tickets de support directement dans le portail.  Après la création d’un ticket de support, vous pouvez gérer le cycle de vie du ticket à partir du portail. Vous pouvez obtenir de l’aide et page de support en accédant à parcourir -> aide + prise en charge.  

![Aide et support][HelpSupport]

## <a name="summary"></a>Résumé

Passons en revue ce que vous l’avez appris dans ce didacticiel :
- Vous avez appris comment vous inscrire et obtenir un abonnement accédez au portail
- Vous avez orienté avec l’interface utilisateur de portail et que vous avez appris à créer et parcourir des ressources
- Vous avez appris comment créer une ressource et parcourir les ressources
- Vous avez appris sur les blades de gestion ou de la structure et comment vous pouvez gérer de façon cohérente différents types de ressources
- Vous avez appris comment personnaliser le portail pour afficher les informations vous intéressent à l’avant et le centre
- Vous avez appris comment contrôler l’accès aux ressources à l’aide de l’accès basée sur les rôles (RBAC)
- Vous avez appris à Obtention d’aide et support

Le portail Microsoft Azure simplifie radicalement la création et la gestion de vos applications dans le nuage.  Examinons le [blog de gestion](https://azure.microsoft.com/blog/topics/management/) pour maintenir à jour que nous sommes constamment [l’écoute](https://feedback.azure.com/forums/223579-azure-preview-portal/) et l’améliorer.  [Blog du ScottGu](http://weblogs.asp.net/scottgu) est un autre endroit idéal pour rechercher les mises à jour Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
