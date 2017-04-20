<properties
   pageTitle="Rôles dans PIM | Microsoft Azure"
   description="Découvrez quels rôles sont utilisés pour les identités privilégiées avec l’extension de la gestion des identités Azure privilégié."
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
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="roles-in-azure-ad-privileged-identity-management"></a>Rôles dans Azure AD privilégié de gestion des identités

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Vous pouvez affecter des utilisateurs de votre organisation à des rôles administratifs différents dans Azure AD. Ces attributions de rôles contrôlent quelles tâches, telles que l’ajout ou de suppression d’utilisateurs ou de modification des paramètres de service, les utilisateurs sont autorisés à effectuer sur l’annonce Azure, Office 365 de Microsoft Online Services et d’autres applications connectées.  

Un administrateur global peut mettre à jour les utilisateurs qui sont **définitivement** affectés à des rôles dans Azure annonce, à l’aide des applets de commande PowerShell tel que `Add-MsolRoleMember` et `Remove-MsolRoleMember`, ou via le portail classique comme indiqué dans [attribution des rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

Gestion des identités AD privilégié Azure (GIP) gère les stratégies d’accès privilégié pour les utilisateurs dans Azure AD. PIM affecte des utilisateurs à un ou plusieurs rôles dans Azure AD, et vous pouvez affecter une personne pour être définitivement dans le rôle, ou éligibles pour le rôle. Lorsqu’un utilisateur est attribué de manière permanente à un rôle, ou Active une affectation de rôle éligibles, puis ils peuvent gérer Active Directory de Azure, Office 365 et d’autres applications avec les autorisations attribuées à leurs rôles.

Il n’y a aucune différence entre l’accès accordé à un utilisateur avec une permanente par rapport à une affectation de rôle éligibles. La seule différence est que certaines personnes n’est pas nécessaire que l’accès tout le temps. Ils deviennent éligibles pour le rôle et peuvent l’activer et désactiver chaque fois qu’ils souhaitent.

## <a name="roles-managed-in-pim"></a>Rôles gérés dans PIM

Privilégié la gestion des identités vous permet d’affecter des utilisateurs aux rôles administrateur, y compris :


- **Administrateur global** (également connu sous le nom administrateur d’entreprise) a accès à toutes les fonctionnalités d’administration. Vous pouvez avoir plus d’un administrateur global dans votre organisation. La personne qui se connecte à acheter Office 365 automatiquement devient un administrateur global
- **Rôle avec privilèges administrateur** gère Azure AD PIM et met à jour les affectations de rôles pour d’autres utilisateurs.  
- **Administrateur de facturation** fait des achats, gère les abonnements, gère des tickets de support et surveille le fonctionnement du service.
- **Mot de passe l’administrateur** réinitialise les mots de passe, gère les demandes de service et surveille le fonctionnement du service. Administrateurs de mot de passe sont limités à la réinitialisation des mots de passe des utilisateurs.
- **Administrateur de service** gère les demandes de service et surveille la santé du service.

  > [AZURE.NOTE] Si vous utilisez Office 365, puis avant d’attribuer le rôle d’administrateur de service à un utilisateur, d’abord affecter l’utilisateur des autorisations d’administration à un service, tel qu’Exchange Online.

- **Administrateur de gestion utilisateur** réinitialise les mots de passe, surveille le fonctionnement du service et gère les comptes d’utilisateurs, des groupes d’utilisateurs et des demandes de service. L’administrateur de gestion d’utilisateur ne peut pas supprimer un administrateur global, créer d’autres rôles d’administrateur ou réinitialiser des mots de passe pour la facturation, globale et les administrateurs du service.
- **Administrateur Exchange** dispose d’un accès administratif à Exchange Online via le centre d’administration Exchange (FAC) et permet d’effectuer pratiquement n’importe quel dans Exchange en ligne.
- **L’administrateur SharePoint** dispose d’un accès administratif à SharePoint Online via le centre d’administration SharePoint Online et permet d’effectuer pratiquement n’importe quel dans SharePoint Online.
- **Skype pour l’administrateur de l’entreprise** dispose d’un accès administratif à Skype pour les entreprises à travers le Skype pour le centre d’administration de Business et permet d’effectuer pratiquement n’importe quel dans Skype pour l’activité en ligne.

Lisez les articles suivants pour plus d’informations sur [les rôles d’administrateur affectation dans Azure AD](active-directory-assign-admin-roles.md) et [l’affectation des rôles d’administrateur dans Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


À partir de PIM, vous pouvez [affecter ces rôles à un utilisateur](active-directory-privileged-identity-management-how-to-add-role-to-user.md) afin que l’utilisateur peut [Activer le rôle lorsque cela est nécessaire](active-directory-privileged-identity-management-how-to-activate-role.md).

Si vous souhaitez autoriser un autre utilisateur à gérer dans PIM lui-même, les rôles PIM nécessite à l’utilisateur sont décrites dans [comment donner accès aux PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Rôles non gérés dans PIM

Rôles dans Exchange Online ou SharePoint Online, à l’exception de ceux mentionnés ci-dessus, ne sont pas représentés dans Active Directory Azure et ne sont donc pas visibles dans PIM. Pour plus d’informations sur la modification des affectations de rôle précis dans ces services Office 365, consultez [autorisations dans Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Abonnements Azure et les groupes de ressources ne sont également pas représentés dans Active Directory Azure. Pour gérer les abonnements d’Azure, voir [Comment faire pour ajouter ou modifier des rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md) et pour plus d’informations sur Azure RBAC [Azure_Role-Based le contrôle d’accès](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Rôles de l’utilisateur et l’ouverture de session
Pour certains services de Microsoft et les applications, affectation d’un utilisateur à un rôle peut ne pas suffire activer cet utilisateur soit un administrateur.

Accès au portail classique Azure requiert de que l’utilisateur est un administrateur de service ou d’un administrateur de collaboration sur un abonnement Azure, même si l’utilisateur n’a pas besoin de gérer les abonnements Azure.  Par exemple, pour gérer les paramètres de configuration pour Azure AD dans le portail classique, un utilisateur doit être à la fois un administrateur global dans Azure AD et un administrateur de collaboration d’abonnement sur un abonnement Azure.  Pour savoir comment ajouter des utilisateurs à des abonnements Azure, voir [Comment faire pour ajouter ou modifier des rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md).

Accès à de Microsoft Online Services peuvent exiger l’utilisateur également reçoivent une licence avant de pouvoir ouvrir le portail du service ou effectuer des tâches administratives.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Attribuer une licence à un utilisateur dans AD Azure

1. Vous connecter à [portal classique Azure] (http://manage.windowsazure.com) avec un compte d’administrateur général ou un compte d’administrateur de collaboration.
2. Sélectionner **Tous les éléments** dans le menu principal.
3. Sélectionnez le répertoire que vous souhaitez utiliser et qui a des licences qui lui est associés.
4. Sélectionner des **licences**. La liste de licences disponibles s’affiche.
5. Sélectionnez le plan de licences contenant les licences que vous voulez distribuer.
6. Sélectionnez **affecter des utilisateurs**.
7. Sélectionnez l’utilisateur que vous souhaitez attribuer une licence à.
8. Cliquez sur le bouton **affecter** .  L’utilisateur peut désormais se connecter à Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
