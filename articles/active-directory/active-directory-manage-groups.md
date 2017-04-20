<properties
    pageTitle="Gestion de l’accès aux ressources avec les groupes Active Directory de Azure | Microsoft Azure"
    description="L’utilisation de groupes dans Azure Active Directory pour gérer l’accès des utilisateurs aux ressources et applications du nuage et locaux."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="managing-access-to-resources-with-azure-active-directory-groups"></a>Gestion de l’accès aux ressources avec groupes Azure Active Directory

Azure Active Directory (AD Azure) est une solution complète de gestion des identités et des accès qui fournit un jeu robuste de fonctions pour gérer l’accès aux locaux et applications en nuage et les ressources, y compris les services en ligne de Microsoft Office 365 et des applications de SaaS non - Microsoft. Cet article fournit une vue d’ensemble, mais si vous souhaitez commencer à utiliser les groupes d’annonces Azure maintenant, suivez les instructions de [Gestion des groupes de sécurité dans Active Directory Azure](active-directory-accessmanagement-manage-groups.md). Si vous voulez voir comment vous pouvez utiliser PowerShell pour gérer des groupes dans Azure Active directory, vous pouvez lire en plus [Azure Active Directory aperçu applets de commande pour la gestion de groupe](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).


> [AZURE.NOTE] Pour utiliser Azure Active Directory, vous avez besoin d’un compte Azure. Si vous n’avez pas un compte, vous pouvez [vous inscrire pour un compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/).


Dans l’annonce Azure, l’une des fonctionnalités principales est la possibilité de gérer l’accès aux ressources. Ces ressources peuvent être la partie de l’annuaire, comme dans le cas des autorisations pour gérer des objets par le biais de rôles dans le répertoire, ou les ressources qui sont externes à l’annuaire, telles que les applications SaaS, Azure, sites et services SharePoint ou sur les ressources de site. Il existe des droits d’accès à une ressource peut être affecté à un utilisateur de quatre façons :


1. Affectation directe

    Les utilisateurs peuvent être affectés directement à une ressource par le propriétaire de la ressource.

2. Appartenance au groupe

    Un groupe peut être affecté à une ressource par le propriétaire de la ressource et donc, l’octroi de membres de ce groupe l’accès à la ressource. L’appartenance au groupe peut alors être géré par le propriétaire du groupe. En effet, propriétaire de la ressource délègue l’autorisation d’affecter des utilisateurs à leurs ressources au propriétaire du groupe.

3. Basée sur des règles

    Le propriétaire de la ressource peut utiliser une règle pour exprimer les utilisateurs qui doivent avoir accès à une ressource. Le résultat de la règle dépend des attributs utilisés dans cette règle et leurs valeurs pour des utilisateurs spécifiques, et dans ce cas, le propriétaire de la ressource délègue efficacement le droit de gérer l’accès à leurs ressources à la source faisant autoritée pour les attributs qui sont utilisés dans la règle. Le propriétaire de la ressource est toujours gère la règle elle-même et détermine les attributs et les valeurs permettent d’accéder à leurs ressources.

4. Autorité externe

    L’accès à une ressource est dérivé d’une source externe. par exemple, un groupe de synchronisation à partir d’une source faisant autorité comme dans un répertoire local ou d’une application SaaS comme une journée de travail. Le propriétaire de la ressource affecte le groupe pour fournir l’accès à la ressource, et la source externe gère les membres du groupe.

  ![Vue d’ensemble du diagramme de gestion des accès](./media/active-directory-access-management-groups/access-management-overview.png)


## <a name="watch-a-video-that-explains-access-management"></a>Regardez une vidéo qui explique la gestion des accès

Vous pouvez regarder une vidéo expliquant plus à ce sujet :

**AD Azure : Présentation des membres dynamiques pour les groupes**

> [AZURE.VIDEO azure-ad--introduction-to-dynamic-memberships-for-groups]

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Comment a accès à gestion des travaux d’Azure Active Directory ?
Au centre de la publicité Azure, solution de gestion d’accès est le groupe de sécurité. À l’aide d’un groupe de sécurité pour gérer l’accès aux ressources est un paradigme connu, permettant ainsi d’un moyen souple et facile à comprendre fournir l’accès à une ressource pour le groupe de destination des utilisateurs. Le propriétaire de la ressource (ou l’administrateur de l’annuaire) peut affecter un groupe de fournir un certains droits d’accès les ressources qu’ils possèdent. Les membres du groupe seront fournies lors de l’accès et le propriétaire de la ressource peut déléguer le droit de gérer la liste des membres d’un groupe à quelqu'un d’autre, par exemple un chef de service ou un administrateur du support technique.

![Diagramme de gestion des accès Active Directory Azure](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

Le propriétaire d’un groupe peut également rendre ce groupe disponible pour les demandes de libre services. Dans ce cas, un utilisateur final peut rechercher et trouver le groupe et une demande de participation, à efficacement la recherche autorisé à accéder aux ressources qui sont gérés par l’intermédiaire du groupe. Le propriétaire du groupe pouvez définir le groupe afin que les requêtes de jointure sont automatiquement acceptées ou exigent l’approbation par le propriétaire du groupe. Lorsqu’un utilisateur effectue une demande à participer à un groupe, la demande d’adhésion est transmise aux propriétaires du groupe. Si un des propriétaires approuve la demande, l’utilisateur est informé et l’utilisateur est joint au groupe. Si un des propriétaires refuse la demande, l’utilisateur est averti, mais pas joint au groupe.


## <a name="getting-started-with-access-management"></a>Mise en route de la gestion des accès
Prêt à commencer ? Vous devez essayer certaines des tâches de base que vous pouvez faire avec les groupes d’annonces Azure. Utilisez ces fonctions pour accéder aux différents groupes de personnes spécialisées pour différentes ressources de votre organisation. Vous trouverez ci-dessous une liste des premières étapes de base.

* [Création d’une règle simple pour configurer l’appartenance dynamique d’un groupe](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)

* [À l’aide d’un groupe pour gérer l’accès aux applications de SaaS](active-directory-accessmanagement-group-saasapps.md)

* [Mise à disposition un groupe pour utilisateur final libre-service](active-directory-accessmanagement-self-service-group-management.md)

* [La synchronisation d’un groupe local sur Azure à l’aide d’Azure Connect d’AD](active-directory-aadconnect.md)

* [Gestion des propriétaires d’un groupe](active-directory-accessmanagement-managing-group-owners.md)


## <a name="next-steps-for-access-management"></a>Étapes suivantes pour la gestion des accès
Maintenant que vous avez compris les principes fondamentaux de la gestion des accès, Voici certaines des fonctionnalités avancées supplémentaires disponibles dans Azure Active Directory pour la gestion de l’accès aux applications et ressources.

* [L’utilisation d’attributs pour créer des règles avancées](active-directory-accessmanagement-groups-with-advanced-rules.md)

* [Gestion des groupes de sécurité dans Active Directory Azure](active-directory-accessmanagement-manage-groups.md)

* [Configuration des groupes dédiés dans Azure AD](active-directory-accessmanagement-dedicated-groups.md)

* [Référence des API de graphique pour les groupes](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)

* [Azure applets de commande de Active Directory pour configurer les paramètres de groupe](active-directory-accessmanagement-groups-settings-cmdlets.md)
