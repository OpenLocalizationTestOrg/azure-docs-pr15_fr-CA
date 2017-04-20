<properties
   pageTitle="Comment faire pour donner accès à PIM | Microsoft Azure"
   description="Apprenez à ajouter des rôles aux utilisateurs dotés de l’extension Azure privilégié identité gestion d’Active Directory pour gérer PIM."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2016"
   ms.author="kgremban"/>

# <a name="how-to-give-access-to-manage-azure-ad-privileged-identity-management"></a>Comment faire pour accorder l’accès pour gérer la gestion des identités Azure AD privilégié

L’administrateur global qui permet la gestion des identités (GIP) Azure AD privilégié pour une organisation automatiquement obtenir les attributions de rôles et l’accès aux PIM. Personne d’autre n’Obtient un accès en écriture par défaut, cependant, d’autres administrateurs globaux. Autres administrateurs globaux, les administrateurs de la sécurité et les lecteurs de sécurité ont un accès en lecture seule pour Azure AD PIM. Pour donner accès au GIP, le premier utilisateur peut affecter d’autres utilisateurs au rôle **administrateur de rôle de privilège** . Cette affectation doit être effectuée à partir de PIM lui-même et ne peut pas être modifiée via PowerShell ou autres portails.

> [AZURE.NOTE] Gestion d’Azure AD PIM nécessite Azure AMF. Dans la mesure où les comptes Microsoft ne peut pas s’inscrire Azure AMF, un utilisateur qui se connecte avec un compte Microsoft ne peut pas accéder à Azure AD PIM.

Assurez-vous qu’il y a toujours au moins deux utilisateurs dans un rôle d’administrateur de rôle privilégié, dans le cas où un utilisateur est verrouillé ou son compte est supprimé.

## <a name="give-another-user-access-to-manage-pim"></a>Autoriser un autre utilisateur à gérer PIM

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez l’application de **Gestion des identités Azure AD privilégié** sur le tableau de bord.
2. Sélectionnez **Gérer les rôles de privilèges** > **administrateur du rôle de privilège** > **Ajouter**.

    ![Ajouter des administrateurs de rôle de privilège - capture d’écran][1]

4. Sur la lame ajouter utilisateurs gérés, l’étape 1 est déjà terminée. Sélectionnez l’étape 2, **Sélectionnez les utilisateurs** et recherche de l’utilisateur que vous souhaitez ajouter.

    ![Sélectionnez les utilisateurs - capture d’écran][2]

6. Sélectionnez l’utilisateur à partir des résultats de la recherche, puis cliquez sur **terminé**.
7. Cliquez sur **OK** pour enregistrer votre sélection. L’utilisateur que vous avez sélectionné s’affiche dans la liste des administrateurs de rôle de privilège.

    - Quand vous attribuez un rôle à une personne, ils sont automatiquement définies comme éligibles pour activer le rôle. Si vous souhaitez les rendre permanentes dans le rôle, cliquez sur l’utilisateur dans la liste. Dans le menu utilisateur, sélectionnez **rendre permanente** .

8. Envoyer à l’utilisateur un lien vers la [mise en route avec la gestion des identités Azure AD privilégié](active-directory-privileged-identity-management-getting-started.md).


## <a name="remove-another-users-access-rights-for-managing-pim"></a>Supprimer les droits d’accès d’un autre utilisateur pour la gestion de PIM

Avant de supprimer un utilisateur du rôle administrateur de rôle privilégié, assurez-vous toujours il y aura toujours deux utilisateurs qui lui est assignées.

1. Dans le tableau de bord PIM, cliquez sur le **rôle de privilège**administrateur.  La liste des utilisateurs actuels de ce rôle s’affichera.
2. Cliquez sur l’utilisateur dans la liste des utilisateurs.
3. Cliquez sur **Supprimer**.  Un message de confirmation s’affiche.
4. Cliquez sur **Oui** pour supprimer l’utilisateur du rôle.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
