<properties
    pageTitle="Supprimer une affectation de l’utilisateur ou le groupe à partir d’une application d’entreprise dans l’aperçu d’Azure Active Directory | Microsoft Azure"
    description="Comment faire pour supprimer l’affectation de l’accès d’un utilisateur ou un groupe à partir d’une application d’entreprise dans Active Directory de Azure"
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
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Supprimer un utilisateur ou une affectation de groupe à partir d’une application d’entreprise dans l’aperçu d’Azure Active Directory

Il est facile de supprimer un utilisateur ou un groupe d’accès assignée à l’une de vos applications d’entreprise dans l’aperçu d’Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Vous devez disposer des autorisations appropriées pour gérer l’application d’entreprise. Dans l’aperçu, vous devez être un administrateur global pour le répertoire.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Comment supprimer un utilisateur ou une affectation de groupe ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2. Sélectionnez des **services supplémentaires**, entrez **Azure Active Directory** dans la zone de texte et puis sélectionnez **entrée**.

3. Sur le *nom du répertoire *Azure Active Directory - ** ** blade (autrement dit, la lame Azure AD pour le répertoire que vous gérez), sélectionnez **Enterprise applications **.

    ![Applications d’entreprise ouverture](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)

4. Sur la blade **d’applications d’entreprise** , sélectionnez **toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.

5. Sur la blade **d’applications d’entreprise - toutes les applications** , sélectionnez une application.

6. Sur la lame ***appname*** (autrement dit, la lame avec le nom de l’application sélectionnée dans le titre), sélectionnez **utilisateurs et groupes**.

    ![Sélectionner des utilisateurs ou des groupes](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)

7. Sur ***appname*** **-utilisateur & affectation de groupe de** lame, sélectionnez une des autres utilisateurs ou groupes et puis sélectionnez la commande **Supprimer** . Confirmer votre décision à l’invite de commandes.

    ![La commande Supprimer la sélection](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Étapes suivantes

- [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
- [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
- [Désactivez les connexions utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)
- [Modifier le nom ou le logo d’une application d’entreprise](active-directory-coreapps-change-app-logo-user-azure-portal.md)
