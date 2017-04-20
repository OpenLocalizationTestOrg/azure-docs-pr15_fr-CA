<properties
    pageTitle="Affecter un utilisateur ou un groupe à une application d’entreprise dans l’aperçu d’Azure Active Directory | Microsoft Azure"
    description="La sélection d’une application d’entreprise à un groupe ou un utilisateur lui assigner dans Azure Active Directory"
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
    ms.date="10/03/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory-preview"></a>Affecter un utilisateur ou un groupe à une application d’entreprise dans l’aperçu d’Azure Active Directory

Il est facile d’affecter un utilisateur ou un groupe à vos applications d’entreprise dans l’aperçu d’Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Vous devez disposer des autorisations appropriées pour gérer l’application d’entreprise. Dans l’aperçu, vous devez être un administrateur global pour le répertoire.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Comment affecter des accès des utilisateurs à une application d’entreprise ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2. Sélectionnez des **services supplémentaires**, entrez Azure Active Directory dans la zone de texte et puis sélectionnez **entrée**.

3. Sur le *nom du répertoire *Azure Active Directory - ** ** blade (autrement dit, la lame Azure AD pour le répertoire que vous gérez), sélectionnez **Enterprise applications **.

    ![Applications d’entreprise ouverture](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)

4. Sur la blade **d’applications d’entreprise** , sélectionnez **toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.

5. Sur la blade **d’applications d’entreprise - toutes les applications** , sélectionnez une application.

6. Sur la lame ***appname*** (autrement dit, la lame avec le nom de l’application sélectionnée dans le titre), sélectionnez **utilisateurs et groupes**.

    ![Sélection de la commande toutes les applications](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)

7. Sur ***appname*** **-utilisateur & affectation de groupe de** lame, sélectionnez la commande **Ajouter** .

8. Sur la lame de **Ajouter une affectation** , sélectionnez **utilisateurs et groupes**.

    ![Affecter un utilisateur ou un groupe à l’application](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)

9. Sur la blade **d’utilisateurs et des groupes** , sélectionnez un ou plusieurs utilisateurs ou groupes dans la liste et sélectionnez le bouton **Sélectionner** en bas de la lame.

10. Sur la lame de **Ajouter une affectation** , sélectionnez **rôle**. Puis, sur la lame de **Sélectionner un rôle** , sélectionnez un rôle à appliquer à l’utilisateur ou le groupe et cliquez sur le bouton **OK** en bas de la lame.

11. Sur la lame de **Ajouter une affectation** , cliquez sur le bouton **affecter** au bas de la lame. Les utilisateurs ou les groupes auront les autorisations définies par le rôle sélectionné pour cette application d’entreprise.

## <a name="next-steps"></a>Étapes suivantes

- [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
- [Supprimer une affectation de l’utilisateur ou le groupe à partir d’une application d’entreprise](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Désactivez les connexions utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)
- [Modifier le nom ou le logo d’une application d’entreprise](active-directory-coreapps-change-app-logo-user-azure-portal.md)
