<properties
    pageTitle="Ajouter de nouveaux utilisateurs à la visualisation d’Azure Active Directory | Microsoft Azure"
    description="Explique comment ajouter de nouveaux utilisateurs ou modifier des informations utilisateur dans Active Directory de Azure."
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


# <a name="add-new-users-to-azure-active-directory-preview"></a>Ajouter de nouveaux utilisateurs à la visualisation d’Azure Active Directory

> [AZURE.SELECTOR]
- [Azure portal](active-directory-users-create-azure-portal.md)
- [Azure portal classique](active-directory-create-users.md)

Cet article explique comment ajouter de nouveaux utilisateurs dans votre organisation dans l’aperçu affichait Active d’Azure (Azure AD). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2.  Sélectionnez des **services supplémentaires**, entrez les **utilisateurs et les groupes** dans la zone de texte et puis sélectionnez **entrée**.

    ![Gestion des utilisateurs ouverture](./media/active-directory-users-create-azure-portal/create-users-user-management.png)

3.  Sur la blade **d’utilisateurs et des groupes** , sélectionnez **tous les utilisateurs**et sélectionnez **Ajouter**.

    ![La commande Ajouter la sélection](./media/active-directory-users-create-azure-portal/create-users-add-command.png)

4.  Entrez les détails de l’utilisateur, comme le **nom** et le **nom de l’utilisateur**. La partie nom de domaine du nom d’utilisateur doit être le nom de domaine de nom de domaine « foo.onmicrosoft.com » de valeur par défaut initiale, ou un nom de domaine vérifié, non fédéré comme « contoso.com ».

5. Copiez ou notez sinon le mot de passe généré de sorte que vous pouvez le fournir à l’utilisateur une fois ce processus terminé.

6. Si vous le souhaitez, vous pouvez ouvrir et remplir les informations de la lame de **profil** , la lame de **groupes** ou de la lame de **rôle d’annuaire** de l’utilisateur. Pour plus d’informations sur les rôles utilisateur et administrateur, voir [affectation les rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md).

7.  Sur la lame de **l’utilisateur** , sélectionnez **créer**.

8. Distribuer de manière sécurisée au nouvel utilisateur, le mot de passe généré afin que l’utilisateur peut se connecter.

## <a name="whats-next"></a>Quel est l’avenir

- [Ajouter un utilisateur externe](active-directory-users-create-external-azure-portal.md)
- [Réinitialiser le mot de passe d’un utilisateur dans le nouveau portail Azure](active-directory-users-reset-password-azure-portal.md)
- [Modifier les informations de travail d’un utilisateur](active-directory-users-work-info-azure-portal.md)
- [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
- [Supprimer un utilisateur dans votre annonce Azure](active-directory-users-delete-user-azure-portal.md)
- [Affecter un utilisateur à un rôle dans votre annonce Azure](active-directory-users-assign-role-azure-portal.md)
