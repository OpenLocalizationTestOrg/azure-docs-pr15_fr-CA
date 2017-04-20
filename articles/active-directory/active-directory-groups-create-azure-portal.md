<properties
    pageTitle="Créer un nouveau groupe dans l’aperçu d’Azure Active Directory | Microsoft Azure"
    description="Comment faire pour créer un groupe dans Azure Active Directory et les ajouter au groupe utilisateurs (membres)"
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


# <a name="create-a-new-group-in-azure-active-directory-preview"></a>Créer un nouveau groupe dans l’aperçu d’Azure Active Directory

> [AZURE.SELECTOR]
- [Azure portal](active-directory-groups-create-azure-portal.md)
- [Azure portal classique](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

Cet article explique comment créer et remplir un nouveau groupe dans l’aperçu Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Un groupe permet d’effectuer des tâches de gestion telles que l’affectation à la fois les licences ou autorisations à un nombre d’utilisateurs ou de périphériques.

## <a name="how-do-i-create-a-group"></a>Comment pour créer un groupe ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2. Sélectionnez des **services supplémentaires**, entrez **l’utilisateur et des groupes** dans la zone de texte et puis sélectionnez **entrée**.

  ![Gestion des utilisateurs ouverture](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. Sur la blade **d’utilisateurs et des groupes** , sélectionnez **tous les groupes**.

  ![Ouverture de la lame de groupes](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. Sur la lame de **utilisateurs et groupes : tous les groupes** , sélectionnez la commande **Ajouter** .

  ![La commande Ajouter la sélection](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. Sur la lame de **groupe** , ajoutez un nom et une description pour le groupe.

6. Pour sélectionner les membres à ajouter au groupe, sélectionnez **assigné** dans la zone **type d’appartenance** et sélectionnez **les membres**. Pour plus d’informations sur la façon de gérer l’appartenance d’un groupe dynamique, reportez-vous [à l’aide des attributs pour créer des règles avancées pour l’appartenance au groupe](active-directory-groups-dynamic-membership-azure-portal.md).

  ![Sélectionner les membres à ajouter](./media/active-directory-groups-create-azure-portal/select-members.png)

5. Sur la lame de **membres** , sélectionnez un ou plusieurs utilisateurs ou périphériques à ajouter au groupe, puis sélectionnez le bouton **Sélectionner** en bas de la lame afin de les ajouter au groupe. La zone **utilisateur** filtre l’affichage selon la concordance de votre entrée à n’importe quelle partie d’un nom d’utilisateur ou de périphérique. Sans caractères génériques sont acceptés dans cette zone.

6. Lorsque vous avez terminé l’ajout de membres au groupe, sélectionnez **créer** sur la lame de **groupe** .    

  ![Créer la confirmation du groupe](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## <a name="additional-information"></a>Informations supplémentaires

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Consultez les groupes existants](active-directory-groups-view-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer les appartenances d’un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs d’un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
