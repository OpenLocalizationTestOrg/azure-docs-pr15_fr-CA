<properties
    pageTitle="Gérer les groupes de votre groupe est membre dans Aperçu d’Azure Active Directory | Microsoft Azure"
    description="Les groupes peuvent contenir d’autres groupes dans Azure Active Directory. Voici comment gérer ces appartenances."
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


# <a name="manage-the-groups-your-group-is-a-member-of-in-azure-active-directory-preview"></a>Gérer les groupes de que votre groupe est membre dans Aperçu d’Azure Active Directory

Les groupes peuvent contenir d’autres groupes dans l’aperçu d’Azure Active Directory. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Voici comment gérer ces appartenances.

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>Comment trouver mon groupe est membre de groupes ?

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2.  Sélectionnez des **services supplémentaires**, entrez les **utilisateurs et les groupes** dans la zone de texte et puis sélectionnez **entrée**.

  ![Gestion des utilisateurs ouverture](./media/active-directory-groups-membership-azure-portal/search-user-management.png)

3.  Sur la blade **d’utilisateurs et des groupes** , sélectionnez **tous les groupes**.

  ![Ouverture de la lame de groupes](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)

4. Sur la lame de **utilisateurs et groupes : tous les groupes** , sélectionnez un groupe.

5. Sur le * *Group - *nom de* ** lame, sélectionnez **groupe appartenances **.

  ![Ouverture de la lame d’appartenances de groupe](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Pour ajouter votre groupe en tant que membre d’un autre groupe, sur la lame de **groupe - appartenances à un groupe** , sélectionnez la commande **Ajouter** .

7. Sélectionnez un groupe dans la **Sélection d’un groupe** de lame, puis sélectionnez le bouton **Sélectionner** en bas de la lame. Vous pouvez ajouter votre groupe pour qu’un seul groupe à la fois. La zone **utilisateur** filtre l’affichage selon la concordance de votre entrée à n’importe quelle partie d’un nom d’utilisateur ou de périphérique. Sans caractères génériques sont acceptés dans cette zone.

  ![Ajouter une appartenance de groupe](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)

8. Pour supprimer le groupe en tant que membre d’un autre groupe, sur la lame de **groupe - les appartenances aux groupes** , sélectionnez un groupe.

9. Sur la lame ***groupname*** , sélectionnez la commande **Supprimer** et confirmer votre choix, à l’invite de commandes.

  ![supprimer la commande de l’appartenance](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)

9. Lorsque vous avez terminé la modification des appartenances aux groupes de votre groupe, cliquez sur **Enregistrer**.


## <a name="additional-information"></a>Informations supplémentaires

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Consultez les groupes existants](active-directory-groups-view-azure-portal.md)
* [Créer un nouveau groupe et ajout de membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs d’un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
