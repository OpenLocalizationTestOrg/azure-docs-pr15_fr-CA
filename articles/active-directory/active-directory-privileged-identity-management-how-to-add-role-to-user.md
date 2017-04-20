<properties
   pageTitle="Comment faire pour ajouter ou supprimer un rôle d’utilisateur | Microsoft Azure"
   description="Apprenez à ajouter des rôles à des identités privilégiées avec l’application Azure privilégié identité gestion d’Active Directory."
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
   ms.date="10/24/2016"
   ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD privilégié la gestion des identités : Comment ajouter ou supprimer un rôle d’utilisateur

Avec Azure Active Directory (AD), un administrateur global (ou l’administrateur de la société) peut mettre à jour les utilisateurs qui sont **définitivement** affectés à des rôles dans Azure AD. Cela est effectué avec les applets de commande PowerShell comme `Add-MsolRoleMember` et `Remove-MsolRoleMember`. Ou bien, ils peuvent utiliser le portail classique Azure comme décrit dans [l’affectation des rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

L’application de gestion des identités Azure AD privilégié permet aux administrateurs de rôle privilégié effectuer des affectations de rôle permanent, également. En outre, les administrateurs de rôle de privilège peuvent apporter aux utilisateurs **éligibles** pour les rôles d’administrateur. Un administrateur éligible peut activer le rôle lorsqu’ils en ont besoin, et ensuite leurs autorisations expirent une fois qu’ils ont terminé.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Gérer les rôles avec PIM dans le portail Azure

Dans votre organisation, vous pouvez affecter des utilisateurs à des rôles administratifs différents dans AD Azure, Office 365 et d’autres services de Microsoft et les applications.  Vous trouverez plus d’informations sur les rôles disponibles à [des rôles dans Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Pour ajouter ou supprimer un utilisateur d’un rôle à l’aide de la gestion des identités privilégié, afficher le tableau de bord PIM. Cliquez ensuite sur le bouton **des utilisateurs dans des rôles d’administrateur** , ou sélectionnez un rôle spécifique (par exemple, l’administrateur Global) dans le tableau rôles.

> [AZURE.NOTE] Si vous n’avez pas encore activé le PIM dans Azure portal, consultez Mise en [route Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) pour plus de détails.

Si vous voulez donner un autre utilisateur accès à PIM lui-même, les rôles PIM nécessite à l’utilisateur sont décrites dans [comment donner accès aux PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Ajouter un utilisateur à un rôle

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez la mosaïque de **la gestion des identités Azure AD privilégié** sur le tableau de bord.
2. Sélectionnez **Gérer les rôles privilégiés**.
3. Dans le tableau de **Synthèse du rôle** , sélectionnez le rôle que vous souhaitez gérer.
4. De la lame de rôle, sélectionnez **Ajouter**.
5. Cliquez sur **Sélectionner des utilisateurs** et recherche de l’utilisateur sur la blade **Sélectionnez utilisateurs** .  
6. Sélectionnez l’utilisateur dans la liste des résultats de la recherche, puis cliquez sur **terminé**.
4. Cliquez sur **OK** pour enregistrer votre sélection. L’utilisateur que vous avez sélectionné s’affiche dans la liste comme éligibles pour le rôle.

> [AZURE.NOTE]
>Nouveaux utilisateurs d’un rôle ne sont pas éligibles pour le rôle par défaut. Si vous souhaitez conserver le rôle, cliquez sur l’utilisateur dans la liste. Les informations d’utilisateur seront affiche dans une nouvelle lame. Dans le menu utilisateur, sélectionnez **rendre permanente** .  
>Si un utilisateur ne peut pas inscrire pour Azure plusieurs facteurs d’authentification (AMF), ou à l’aide d’un compte Microsoft (généralement @outlook.com), vous avez besoin afin de les rendre permanentes dans tous leurs rôles. Administrateurs admissibles sont posées pour vous inscrire à AMF pendant l’activation.

Maintenant que l’utilisateur est éligible pour un rôle, informez-les qu’ils peuvent l’activer en suivant les instructions de la rubrique [comment activer ou désactiver un rôle](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Supprimer un utilisateur d’un rôle

Vous pouvez supprimer des utilisateurs d’affectations de rôle éligibles, mais assurez-vous qu’il existe toujours au moins un utilisateur est un administrateur global permanent.

Suivez ces étapes pour supprimer un utilisateur d’un rôle :

1. Accédez au rôle dans la liste rôle en sélectionnant un rôle dans le tableau de bord Azure AD PIM ou en cliquant sur le bouton **des utilisateurs dans des rôles d’administrateur** .
2. Cliquez sur l’utilisateur dans la liste des utilisateurs.
3. Cliquez sur **Supprimer**. Un message vous demande de confirmer.
4. Cliquez sur **Oui** pour supprimer le rôle de l’utilisateur.

Si vous ne savez pas quels utilisateurs ont encore besoin de leurs affectations de rôle, vous pouvez [Démarrer une révision d’accès pour le rôle](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
