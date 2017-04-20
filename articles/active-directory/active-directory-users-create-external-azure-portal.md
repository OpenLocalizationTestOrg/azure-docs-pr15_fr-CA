<properties
    pageTitle="Ajouter des utilisateurs à partir d’autres annuaires ou partenaires dans l’aperçu d’Azure Active Directory | Microsoft Azure"
    description="Explique comment ajouter des utilisateurs ou modifier des informations utilisateur dans Active Directory Azure, y compris les utilisateurs externes et l’invité."
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

# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory-preview"></a>Ajouter des utilisateurs à partir d’autres répertoires ou les sociétés partenaires dans l’aperçu d’Azure Active Directory

> [AZURE.SELECTOR]
- [Azure portal](active-directory-users-create-external-azure-portal.md)
- [Azure portal classique](active-directory-create-users-external.md)

Cet article explique comment ajouter des utilisateurs à partir d’autres annuaires dans l’aperçu d’Azure Active Directory (AD Azure) ou de sociétés partenaires. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Pour plus d’informations sur l’ajout de nouveaux utilisateurs dans votre organisation et l’ajout d’utilisateurs qui ont des comptes de Microsoft, consultez [Ajout de nouveaux utilisateurs à Azure Active Directory](active-directory-users-create-azure-portal.md). Utilisateurs n’ont pas les autorisations d’administrateur par défaut, mais vous pouvez attribuer des rôles à eux à tout moment.

## <a name="add-a-user"></a>Ajout d’un utilisateur

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2.  Sélectionnez des **services supplémentaires**, entrez les **utilisateurs et les groupes** dans la zone de texte et puis sélectionnez **entrée**.

    ![Gestion des utilisateurs ouverture](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)

3.  Sur la blade **d’utilisateurs et des groupes** , sélectionnez **utilisateurs**et sélectionnez **Ajouter**.

    ![La commande Ajouter la sélection](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)

4. Sur la lame de **l’utilisateur** , fournir un nom complet dans la zone **nom** et nom de l’utilisateur de connexion nom **d’utilisateur**.

5. Copiez ou notez sinon le mot de passe généré de sorte que vous pouvez le fournir à l’utilisateur une fois ce processus terminé.

6. Le cas échéant, sélectionnez le **profil** à ajouter d’abord les utilisateurs et les nom, prénom, un titre et un nom de département.
    
    ![Ouvrir le profil de l’utilisateur](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

    - Sélectionnez les **groupes** à ajouter l’utilisateur à un ou plusieurs groupes.

        ![Ajout d’un utilisateur à des groupes](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)

    - Sélectionnez les **rôles organisationnels** pour affecter l’utilisateur à un rôle dans la liste des **rôles** . Pour plus d’informations sur les rôles utilisateur et administrateur, voir [affectation les rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md).

        ![Affectation d’un utilisateur à un rôle](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)

7. Sélectionnez **créer**.

8. Distribuer de manière sécurisée au nouvel utilisateur, le mot de passe généré afin que l’utilisateur peut se connecter.

> [AZURE.IMPORTANT] Si votre organisation utilise plus d’un domaine, vous devez connaître les problèmes suivants lorsque vous ajoutez un compte d’utilisateur :
>
> - Pour ajouter des comptes d’utilisateurs avec le même nom utilisateur principal (UPN) sur plusieurs domaines, **premier** ajouter, par exemple, geoffgrisso@contoso.onmicrosoft.com, **suivie par** geoffgrisso@contoso.com.
> - **Ne pas** ajouter de geoffgrisso@contoso.com avant d’ajouter des geoffgrisso@contoso.onmicrosoft.com. Cet ordre est important et peut être difficile à annuler.

Si vous modifiez les informations pour un utilisateur dont l’identité est synchronisée avec votre service d’Active Directory sur site, vous ne pouvez pas modifier les informations utilisateur dans le portail classique Azure. Pour modifier les informations de l’utilisateur, utilisez vos outils de gestion Active Directory local.


## <a name="whats-next"></a>Quel est l’avenir

- [Ajout d’un utilisateur](active-directory-users-create-azure-portal.md)
- [Réinitialiser le mot de passe d’un utilisateur dans le nouveau portail Azure](active-directory-users-reset-password-azure-portal.md)
- [Affecter un utilisateur à un rôle dans votre annonce Azure](active-directory-users-assign-role-azure-portal.md)
- [Modifier les informations de travail d’un utilisateur](active-directory-users-work-info-azure-portal.md)
- [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
- [Supprimer un utilisateur dans votre annonce Azure](active-directory-users-delete-user-azure-portal.md)
