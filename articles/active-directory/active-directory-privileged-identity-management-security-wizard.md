<properties
   pageTitle="L’Assistant de sécurité de la gestion des identités Azure AD privilégié"
   description="La première fois que vous utilisez l’extension Azure privilégié identité gestion d’Active Directory, s’affiche avec l’Assistant sécurité. Cet article décrit les étapes d’utilisation de l’Assistant."
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

# <a name="the-azure-ad-privileged-identity-management-security-wizard"></a>L’Assistant de sécurité de la gestion des identités Azure AD privilégié

Si vous êtes la première personne à exécuter la gestion d’identité privilégié Azure (GIP) pour votre organisation, s’affiche avec un Assistant. L’Assistant vous aide à comprendre les risques de sécurité des identités privilégiées et comment utiliser PIM de réduire ces risques. Vous n’avez pas besoin d’apporter des modifications à des affectations de rôle existantes dans l’Assistant, si vous préférez le faire ultérieurement.

## <a name="what-to-expect"></a>À quoi s’attendre

Avant le démarrage de votre organisation à l’aide de PIM, toutes les affectations de rôle sont permanentes : les utilisateurs sont toujours ces rôles même si elles ne doivent pas actuellement leurs privilèges.  La première étape de l’Assistant affiche une liste de rôles de privilèges élevés et combien d’utilisateurs est actuellement dans ces rôles. , Vous pouvez accéder à un rôle particulier pour plus d’informations sur les utilisateurs si une ou plusieurs d'entre eux sont inconnus.

La deuxième étape de l’Assistant vous donne la possibilité de modifier les attributions de rôle de l’administrateur.  

> [AZURE.WARNING]Il est important d’avoir au moins un administrateur global et plus d’un rôle de privilège administrateur, avec un compte d’organisation (et non un compte Microsoft). S’il existe un seul administrateur de rôle de privilège, l’organisation ne sera pas en mesure de gérer PIM si ce compte est supprimé.
> Conservez également des affectations de rôle permanent si un utilisateur possède un compte Microsoft (compte qu’ils utilisent pour se connecter à des services de Microsoft telles que Skype et Outlook.com). Si vous souhaitez exiger AMF pour l’activation de ce rôle, cet utilisateur sera verrouillé.


Une fois que vous avez apporté des modifications, l’Assistant n’affichent plus. La prochaine fois que vous ou un autre rôle de privilège administrateur utiliser PIM, vous verrez le tableau de bord PIM.  

- Si vous souhaitez ajouter ou supprimer des utilisateurs dans des rôles ou modifier les affectations de permanent afin de bénéficier, pour en savoir plus sur [la façon d’ajouter ou de supprimer le rôle d’un utilisateur](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
- Si vous souhaitez autoriser plusieurs utilisateurs à gérer PIM, en savoir plus sur [comment donner accès à gérer dans PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).



## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
