<properties
    pageTitle="Désactivez les connexions utilisateur pour une application d’entreprise dans l’aperçu d’Azure Active Directory | Microsoft Azure"
    description="Comment faire pour désactiver une application d’entreprise afin qu’aucun utilisateur ne peut se connecter à elle dans Azure Active Directory"
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
    ms.date="10/17/2016"
    ms.author="curtand"/>


# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Désactivez les connexions utilisateur pour une application d’entreprise dans l’aperçu d’Azure Active Directory

Il est facile de désactiver une application d’entreprise afin qu’aucun utilisateur ne peut se connecter à elle dans Aperçu d’Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Vous devez disposer des autorisations appropriées pour gérer l’application d’entreprise. Dans l’aperçu, vous devez être un administrateur global pour le répertoire.

## <a name="how-do-i-disable-user-sign-ins"></a>Comment désactiver les connexions utilisateur ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2. Sélectionnez des **services supplémentaires**, entrez **Azure Active Directory** dans la zone de texte et puis sélectionnez **entrée**.

3. Sur le *nom du répertoire *Azure Active Directory - ** ** blade (autrement dit, la lame Azure AD pour le répertoire que vous gérez), sélectionnez **Enterprise applications **.

    ![Applications d’entreprise ouverture](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)

4. Sur la blade **d’applications d’entreprise** , sélectionnez **toutes les applications**. Vous consultez une liste des applications que vous pouvez gérer.

5. Sur la blade **d’applications d’entreprise - toutes les applications** , sélectionnez une application.

6. Sur la lame ***appname*** (autrement dit, la lame avec le nom de l’application sélectionnée dans le titre), sélectionnez **Propriétés**.

    ![Sélection de la commande toutes les applications](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)

7. Sur ***appname*** **-Propriétés** lame, sélectionnez **non** pour **activée pour les utilisateurs de se connecter ?**.

8. Sélectionnez la commande **Enregistrer** .

## <a name="next-steps"></a>Étapes suivantes

- [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
- [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
- [Supprimer une affectation de l’utilisateur ou le groupe à partir d’une application d’entreprise](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Modifier le nom ou le logo d’une application d’entreprise](active-directory-coreapps-change-app-logo-user-azure-portal.md)
