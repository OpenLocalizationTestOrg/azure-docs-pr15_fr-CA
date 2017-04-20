<properties
    pageTitle="Contrôle d’accès basé sur rôle | Microsoft Azure"
    description="Mise en route dans Gestion de l’accès avec contrôle de l’accès basé sur rôle Azure dans le portail Azure. Attributions de rôles permet d’attribuer des autorisations dans votre répertoire."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="get-started-with-access-management-in-the-azure-portal"></a>Mise en route de la gestion de l’accès dans le portail Azure

Orienté sur la sécurité des entreprises devraient se concentrer sur la possibilité pour les employés les autorisations exactes que dont ils ont besoin. Trop d’autorisations expose un compte aux intrus. Trop peu d’autorisations signifie que les employés ne peuvent pas effectuer leur travail efficacement. Azure contrôle d’accès basée sur les rôles (RBAC) permet de résoudre ce problème en offrant une gestion d’accès à granularité fine pour Azure.

L’utilisation de RBAC, vous pouvez séparer les droits au sein de votre équipe et accorder uniquement la quantité de l’accès aux utilisateurs dont ils ont besoin pour effectuer leur travail. Au lieu de donner à tout le monde des autorisations illimitées dans votre abonnement Azure ou des ressources, vous pouvez autoriser uniquement certaines actions. Par exemple, utiliser le RBAC pour permettre à un employé de gérer des ordinateurs virtuels dans un abonnement, tandis que l’autre peut gérer des bases de données SQL dans l’abonnement même.

## <a name="basics-of-access-management-in-azure"></a>Notions de base de gestion des accès dans Azure
Chaque abonnement Azure est associé à un répertoire Azure Active Directory (AD). Les utilisateurs, les groupes et les applications à partir de ce répertoire peuvent gérer les ressources dans l’abonnement Azure. Attribuer ces droits d’accès à l’aide d’Azure portal, les outils de ligne de commande Azure et Azure des API de gestion.

Accorder l’accès en attribuant le rôle RBAC approprié pour les utilisateurs, les groupes et les applications au niveau d’une portée. La portée d’une affectation de rôle peut être une seule ressource, un groupe de ressources ou un abonnement. Un rôle attribué à une portée parent accorde également l’accès aux enfants qu’il contient. Par exemple, un utilisateur ayant accès à un groupe de ressources peut gérer toutes les ressources qu’il contient, tels que des sites Web, les ordinateurs virtuels et les sous-réseaux.

![Relation entre les éléments d’Azure Active Directory - diagramme](./media/role-based-access-control-what-is/rbac_aad.png)

Le rôle RBAC que vous affectez détermine les ressources auxquelles l’utilisateur, le groupe ou l’application peut gérer dans cette portée.

## <a name="built-in-roles"></a>Rôles intégrés
RBAC Azure a trois rôles de base qui s’appliquent à tous les types de ressources :

- **Propriétaire** dispose d’un accès complet à toutes les ressources, y compris le droit de déléguer l’accès à d’autres personnes.
- **Collaborateur** peut créer et gérer tous les types de ressources Azure, mais ne peut pas accorder l’accès à d’autres personnes.
- **Lecteur** peut afficher les ressources Azure existantes.

Le reste des rôles RBAC dans Azure autoriser la gestion des ressources Azure spécifiques. Par exemple, le rôle de collaborateur de Machine virtuelle permet à l’utilisateur de créer et de gérer des ordinateurs virtuels. Il ne donne pas les accès au réseau virtuel ou le sous-réseau que l’ordinateur virtuel se connecte à.

[Les rôles intégrés RBAC](role-based-access-built-in-roles.md) répertorie les rôles disponibles dans Azure. Il spécifie les opérations et la portée de chaque rôle intégré accorde aux utilisateurs. Si vous avez besoin pour définir vos propres rôles pour encore plus de contrôle, consultez Comment créer des [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Héritage des accès et la hiérarchie des ressources
- Chaque **abonnement** dans Azure appartient à un seul répertoire.
- Chaque **groupe de ressources** appartient à un seul abonnement.
- Chaque **ressource** appartient au groupe de ressources qu’un seul.

L’accès que vous accordez à des étendues de parent est héritée à portées enfants. Par exemple :

- Vous affectez le rôle de lecteur à un groupe d’annonces Azure à la portée de l’abonnement. Les membres de ce groupe peuvent afficher chaque groupe de ressources et les ressources dans l’abonnement.
- Vous affectez le rôle de collaborateur pour une application au niveau de la portée de groupe de ressources. Il peut gérer les ressources de tous les types dans ce groupe de ressources, mais pas les autres groupes ressource dans l’abonnement.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>RBAC Azure et administrateurs d’abonnement classique
Les administrateurs d’abonnement classique et co-administrateurs ont un accès complet à l’abonnement Azure. Ils peuvent gérer des ressources à l’aide du [portail Azure](https://portal.azure.com) avec l’API du Gestionnaire de ressources Azure, ou le modèle de déploiement classique [Azure portal classique](https://manage.windowsazure.com) et Azure. Dans le modèle RBAC, administrateurs classiques sont affectés du rôle de propriétaire à la portée de l’abonnement.

Uniquement le portail Azure et les nouvelles API du Gestionnaire de ressources Azure prend en charge RBAC d’Azure. Les utilisateurs et les applications qui sont affectées des rôles RBAC ne peut pas utiliser le portail de gestion classique et le modèle de déploiement classique d’Azure.

## <a name="authorization-for-management-vs-data-operations"></a>Autorisation pour la gestion et des opérations de données
RBAC Azure prend uniquement en charge les opérations de gestion des ressources Azure dans le portail Azure et l’API du Gestionnaire de ressources Azure. Il ne peut pas autoriser toutes les opérations de niveau de données pour les ressources d’Azure. Par exemple, vous pouvez autoriser une personne à gérer les comptes de stockage, mais pas pour les objets BLOB ou des tables dans un compte de stockage. De même, une base de données SQL peut être gérée, mais pas les tables qu’il contient.

## <a name="next-steps"></a>Étapes suivantes
- Mise en route avec [contrôle d’accès basée sur les rôles dans le portail Azure](role-based-access-control-configure.md).
- Consultez les [rôles intégrés de RBAC](role-based-access-built-in-roles.md)
- Définir vos propres [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md)
