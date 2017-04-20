<properties
    pageTitle="Gérer les membres d’un groupe dans l’aperçu d’Azure Active Directory | Microsoft Azure"
    description="Comment les utilisateurs et les périphériques qui sont membres d’un groupe dans Active Directory de Azure"
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


# <a name="manage-the-members-for-a-group-in-azure-active-directory-preview"></a>Gérer les membres d’un groupe dans l’aperçu d’Azure Active Directory

Cet article explique comment gérer les membres d’un groupe dans l’aperçu d’Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

## <a name="how-do-i-find-the-members-and-manage-them"></a>Comment trouver les membres et les gérer ?

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2.  Sélectionnez des **services supplémentaires**, entrez les **utilisateurs et les groupes** dans la zone de texte et puis sélectionnez **entrée**.

  ![Gestion des utilisateurs ouverture](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  Sur la blade **d’utilisateurs et des groupes** , sélectionnez **tous les groupes**.

  ![Ouverture de la lame de groupes](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. Sur la lame de **utilisateurs et groupes : tous les groupes** , sélectionnez un groupe.

5. Sur la *propriété groupname *groupe - ** ** lame, sélectionnez **membres **.

  ![Ouverture de la lame de membres](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. Pour ajouter des membres au groupe, sur la lame **- groupe de membres** , sélectionnez **Ajouter des membres**.

  ![Ajouter des commandes de membres](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. Sur la lame de **membres** , sélectionnez un ou plusieurs utilisateurs ou périphériques à ajouter au groupe, puis sélectionnez le bouton **Sélectionner** en bas de la lame afin de les ajouter au groupe. La zone **utilisateur** filtre l’affichage selon la concordance de votre entrée à n’importe quelle partie d’un nom d’utilisateur ou de périphérique. Sans caractères génériques sont acceptés dans cette zone.

8. Pour supprimer des membres du groupe, sur la lame **- groupe de membres** , sélectionnez un membre.

9. Sur la lame ***membername*** , sélectionnez la commande **Supprimer** et confirmer votre choix, à l’invite de commandes.

  ![supprimer la commande de membres](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. Lorsque vous avez terminé la modification de membres pour le groupe, cliquez sur **Enregistrer**.


## <a name="additional-information"></a>Informations supplémentaires

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Consultez les groupes existants](active-directory-groups-view-azure-portal.md)
* [Créer un nouveau groupe et ajout de membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les appartenances d’un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs d’un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
