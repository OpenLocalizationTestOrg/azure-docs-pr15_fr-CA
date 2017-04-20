<properties
    pageTitle="Gestion des identités Azure AD privilégié | Microsoft Azure"
    description="Une rubrique qui explique comment utiliser PIM pour améliorer la sécurité de votre nuage et nouveautés de la gestion des identités Azure AD privilégié."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management"></a>Gestion des identités Azure AD privilégié

Avec la gestion d’identité privilégié Azure Active Directory (AD), vous pouvez gérer, de contrôler et surveiller l’accès au sein de votre organisation. Cela inclut l’accès aux ressources dans Active Directory Azure et d’autres services en ligne de Microsoft Office 365 ou Microsoft Intune.  

> [AZURE.NOTE] La gestion des identités privilégié est uniquement disponible avec l’édition Premium P2 d’Azure Active Directory. Pour plus d’informations, consultez [éditions d’Azure Active Directory](active-directory-editions.md).

Les organisations veulent que de réduire le nombre de personnes qui ont accès à des informations sécurisées ou des ressources, car les qui réduit le risque d’un utilisateur malveillant l’obtention de cet accès. Toutefois, les utilisateurs doivent toujours effectuer des opérations privilégiées dans les applications Azure, Office 365 ou SaaS. Entreprises donnent aux utilisateurs un accès privilégié dans Azure annonce sans surveillance ce que font les utilisateurs avec les privilèges d’administrateur. La gestion des identités AD privilégié Azure vous aide à résoudre ce risque.  

La gestion des identités AD privilégié Azure vous aide à :  

- Voir quels utilisateurs sont administrateurs AD Azure
- Activer à la demande, « juste à temps » d’un accès administratif à de Microsoft Online Services comme Office 365 et Intune
- Obtenir des rapports sur l’historique de l’accès administrateur et les modifications dans les affectations de l’administrateur
- Recevoir des alertes sur l’accès à un rôle de privilège

Azure annonce la gestion des identités privilégié peut gérer intégrés rôles d’organisation AD Azure, y compris :  

- Administrateur global
- Administrateur de facturation
- Administrateur de service  
- Administrateur de l’utilisateur
- Mot de passe administrateur

## <a name="just-in-time-administrator-access"></a>Juste-à-accès administrateur de temps

Jusqu’ici, vous pouvez affecter un utilisateur à un rôle d’administrateur à l’aide Azure portal classique ou Windows PowerShell. Par conséquent, cet utilisateur devient une **administration permanente**, toujours active dans le rôle a été attribué. La gestion des identités AD privilégié Azure introduit le concept d’une **administration éligible**. Administrateurs admissibles doivent être des utilisateurs qui ont besoin d’un accès privilégié et, mais pas tous les jours. Le rôle est inactif jusqu'à ce que l’utilisateur a besoin d’accéder, puis terminer un processus d’activation et de devenir un administrateur actif pour une durée prédéterminée.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Activer la gestion des identités privilégié pour votre répertoire

Vous pouvez démarrer à l’aide de la gestion des identités Azure AD privilégié dans [Azure portal](https://portal.azure.com/).

>[AZURE.NOTE] Vous devez être un administrateur global avec un compte d’organisation (par exemple, @yourdomain.com), pas un compte Microsoft (par exemple, @outlook.com), pour activer la gestion des identités Azure AD privilégié pour un répertoire.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’un administrateur global de votre répertoire.
2. Si votre organisation dispose de plus d’un répertoire, sélectionnez votre nom d’utilisateur dans l’angle supérieur droit du portail Azure. Sélectionnez le répertoire où vous allez utiliser la gestion des identités Azure AD privilégié.
3. Sélectionnez **plus de services** et d’utiliser la zone de texte de filtre pour rechercher **la gestion des identités Azure AD privilégié**.
4. Vérifiez le **code confidentiel pour le tableau de bord** , puis cliquez sur **créer**. L’application de gestion des identités privilégié s’ouvre.

Si vous êtes la première personne à utiliser la gestion des identités Azure AD privilégié dans votre répertoire, puis l' [Assistant de sécurité](active-directory-privileged-identity-management-security-wizard.md) vous guide dans l’expérience de l’attribution initiale. Une fois que vous en devenez automatiquement le premier **administrateur de la sécurité** et l' **administrateur de rôle de privilège** de l’annuaire.

Uniquement un rôle de privilège administrateur peut gérer l’accès des autres administrateurs. Vous pouvez [donner aux autres utilisateurs la possibilité de gérer de PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="privileged-identity-management-dashboard"></a>Tableau de bord de gestion des identités privilégié

Le Gestionnaire d’identités AD privilégié Azure fournit un tableau de bord qui vous fournit des informations importantes telles que :

- Alertes qui désignent des opportunités pour améliorer la sécurité
- Le nombre d’utilisateurs qui sont attribués à chaque rôle de privilège  
- Le nombre d’administrateurs admissibles et permanent
- En cours d’accès révisions

![Tableau de bord PIM - capture d’écran][2]

## <a name="privileged-role-management"></a>Gestion des privilèges des rôles

Avec la gestion des identités Azure AD privilégié, vous pouvez gérer les administrateurs en ajoutant ou supprimant des administrateurs permanentes ou éligibles à chaque rôle.

![Administrateurs d’Ajout/Suppression de PIM - capture d’écran][3]

## <a name="configure-the-role-activation-settings"></a>Configurer les paramètres de l’activation du rôle

En utilisant les [paramètres de rôle](active-directory-privileged-identity-management-how-to-change-default-settings.md) , vous pouvez configurer les propriétés activation de rôle éligibles, y compris :

- La durée de la période d’activation de rôle
- La notification de l’activation du rôle
- Les informations de qu'un utilisateur doit fournir au cours du processus d’activation de rôle  

![Capture d’écran de paramètres - activation de l’administrateur - PIM][4]

Notez que, dans l’image, les boutons pour **Plusieurs facteurs d’authentification** sont désactivées. Pour certains, hautement privilégié des rôles, nous avons besoin AMF pour une protection renforcée.

## <a name="role-activation"></a>Activation du rôle  

Pour [Activer un rôle](active-directory-privileged-identity-management-how-to-activate-role.md), un administrateur éligible demande un temps lié aux « activation » pour le rôle. L’activation peut être demandée à l’aide de l’option **Activer mon rôle** dans la gestion des identités Azure AD privilégié.

Un administrateur qui souhaite activer un rôle doit initialiser la gestion des identités Azure AD privilégié dans le portail Azure.

L’activation du rôle est personnalisable. Dans les paramètres PIM, vous pouvez déterminer la longueur de l’activation et les informations que l’administrateur doit fournir pour activer le rôle.

![Activation de rôle PIM administrateur demande - capture d’écran][5]

## <a name="review-role-activity"></a>Vérifier l’activité de rôle

Il existe deux moyens de suivre comment vos employés et les administrateurs utilisent des rôles privilégiés. La première option est l’utilisation de [l’historique d’audit](active-directory-privileged-identity-management-how-to-use-audit-log.md). L’historique d’audit consigne le suivi des modifications dans les attributions de rôle de privilège et l’historique de l’activation de rôle.

![Historique de l’activation de PIM - capture d’écran][6]

La deuxième option consiste à configurer régulièrement [accès révisions](active-directory-privileged-identity-management-how-to-start-security-review.md). Ces révisions d’accès peuvent être effectuées par et affectées réviseur (par exemple, un responsable de l’équipe) ou les employés peuvent consulter eux-mêmes. Il s’agit de la meilleure méthode pour surveiller qui doit toujours avoir accès, et qui n’est plus le cas.


## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
