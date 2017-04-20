<properties
    pageTitle="Afficher les affectations accès Azure | Microsoft Azure"
    description="Afficher et gérer toutes les affectations de contrôle d’accès basée sur les rôles pour un utilisateur ou un groupe dans le portail Azure"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="jeffsta"/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal---public-preview"></a>Afficher les affectations d’accès pour les utilisateurs et les groupes dans le portail Azure - version d’évaluation

> [AZURE.SELECTOR]
- [Gérer l’accès par utilisateur ou par groupe](role-based-access-control-manage-assignments.md)
- [Gérer l’accès à la ressource](role-based-access-control-configure.md)

Avec basée sur le rôle de contrôle d’accès (RBAC) dans l’aperçu Azure Active Directory, vous pouvez gérer l’accès à vos ressources d’Azure. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

Accès avec RBAC est précis, car il existe deux méthodes que vous pouvez restreindre les autorisations :

- **Étendue :** Affectation des rôles RBAC ont une portée limitée à un abonnement spécifique, un groupe de ressources ou une ressource. Un utilisateur autorisé à accéder à une ressource unique ne peut pas accéder à d’autres ressources dans le même abonnement.
- **Rôle :** Dans le cadre de l’affectation, l’accès est restreint encore plus en assignant un rôle. Rôles peuvent être de haut niveau, comme le propriétaire ou spécifique, comme un lecteur de l’ordinateur virtuel.

Rôles peuvent uniquement être affectés à partir de dans l’abonnement, le groupe de ressources ou la ressource qui est la portée de l’affectation. Mais vous pouvez afficher toutes les affectations d’accès pour un utilisateur ou un groupe dans un seul emplacement.

Obtenir plus d’informations sur la façon [d’utiliser les affectations de rôles pour gérer l’accès aux ressources de votre abonnement Azure](role-based-access-control-configure.md).

##  <a name="view-access-assignments"></a>Afficher les affectations

Pour rechercher les affectations d’accès pour un seul utilisateur ou un groupe, démarrer dans Azure Active Directory dans le [portail Azure](http://portal.azure.com).

1. Sélectionnez **Azure Active Directory**. Si cette option n’est pas visible dans votre liste de navigation, sélectionnez **Services plus** et accédez à la section recherche **Azure Active Directory**.
2. Sélectionnez **utilisateurs et groupes**et ensuite soit **tous les utilisateurs** ou **tous les groupes**. Pour cet exemple, nous concentrer sur les utilisateurs individuels.
    ![Gérer les utilisateurs et les groupes dans Active Directory Azure - capture d’écran](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Recherche de l’utilisateur par son nom ou le nom d’utilisateur.
4. Sélectionnez les **ressources Azure** sur la lame de l’utilisateur. Toutes les affectations d’accès de cet utilisateur apparaissent.

### <a name="read-permissions-to-view-assignments"></a>Autorisations de lecture pour afficher les affectations

Cette page n’affiche que les affectations d’accès que vous êtes autorisé à lire. Par exemple, vous avez accès en lecture à abonnement A et atteindre la lame de ressources Azure pour vérifier les affectations d’un utilisateur. Vous pouvez voir ses affectations d’accès de l’abonnement A, mais que vous ne verrez qu’elle a également des affectations d’accès sur abonnement B.

## <a name="delete-access-assignments"></a>Supprimer les affectations d’accès

À partir de cette carte, vous pouvez supprimer les affectations d’accès qui ont été affectées directement à un utilisateur ou un groupe. Si l’affectation de l’accès a été héritée d’un groupe parent, vous avez besoin accéder à la ressource ou l’abonnement et de gérer l’affectation il.

1. Dans la liste de toutes les affectations d’accès pour un utilisateur ou un groupe, sélectionnez celui que vous souhaitez supprimer.
2. Sélectionnez **Supprimer** , puis sur **Oui** pour confirmer.
    ![Supprimer l’affectation de l’accès - capture d’écran](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="related-topics"></a>Rubriques connexes

- Mise en route de contrôle d’accès basé sur le rôle à [utiliser les affectations de rôles pour gérer l’accès aux ressources de votre abonnement Azure](role-based-access-control-configure.md)
- Consultez les [rôles intégrés de RBAC](role-based-access-built-in-roles.md)
