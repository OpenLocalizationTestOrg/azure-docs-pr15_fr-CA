<properties
    pageTitle="Affecter un utilisateur à des rôles d’administrateur dans Aperçu d’Azure Active Directory | Microsoft Azure"
    description="Explique comment modifier les informations d’administration utilisateur dans Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory-preview"></a>Affecter un utilisateur à des rôles d’administrateur dans Aperçu d’Azure Active Directory

Cet article explique comment affecter un rôle d’administrateur à un utilisateur d’aperçu d’Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Pour plus d’informations sur l’ajout de nouveaux utilisateurs dans votre organisation, consultez [Ajout de nouveaux utilisateurs à Azure Active Directory](active-directory-users-create-azure-portal.md). Utilisateurs n’ont pas les autorisations d’administrateur par défaut, mais vous pouvez attribuer des rôles à eux à tout moment.

## <a name="assign-a-role-to-a-user"></a>Attribuer un rôle à un utilisateur

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2.  Sélectionnez des **services supplémentaires**, entrez les **utilisateurs et les groupes** dans la zone de texte et puis sélectionnez **entrée**.

    ![Gestion des utilisateurs ouverture](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3.  Sur la blade **d’utilisateurs et des groupes** , sélectionnez **tous les utilisateurs**.

    ![Ouverture de la lame tous les utilisateurs](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)

4. Sur la lame de **utilisateurs et groupes : tous les utilisateurs** , sélectionnez un utilisateur dans la liste.

5. Sur la lame pour l’utilisateur sélectionné, sélectionnez le **rôle d’annuaire**et ensuite affecter l’utilisateur à un rôle dans la liste **rôle du répertoire** . Pour plus d’informations sur les rôles utilisateur et administrateur, voir [affectation les rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md).

      ![Affectation d’un utilisateur à un rôle](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

6. Cliquez sur **Enregistrer**.


## <a name="whats-next"></a>Quel est l’avenir

- [Ajout d’un utilisateur](active-directory-users-create-azure-portal.md)
- [Réinitialiser le mot de passe d’un utilisateur dans le nouveau portail Azure](active-directory-users-reset-password-azure-portal.md)
- [Modifier les informations de travail d’un utilisateur](active-directory-users-work-info-azure-portal.md)
- [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
- [Supprimer un utilisateur dans votre annonce Azure](active-directory-users-delete-user-azure-portal.md)
