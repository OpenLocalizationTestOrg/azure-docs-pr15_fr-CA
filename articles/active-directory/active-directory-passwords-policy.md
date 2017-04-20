<properties
    pageTitle="Les stratégies de mot de passe et les restrictions dans Azure Active Directory | Microsoft Azure"
    description="Décrit les stratégies qui s’appliquent dans Azure Active Directory, y compris les caractères autorisés, longueur et l’expiration des mots de passe"
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
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Les stratégies de mot de passe et les restrictions dans Azure Active Directory

Cet article décrit les stratégies de mot de passe et la complexité associées aux comptes d’utilisateur stockés dans votre répertoire Azure AD.

> [AZURE.IMPORTANT] **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName les stratégies qui s’appliquent à tous les comptes d’utilisateur

Chaque compte d’utilisateur qui a besoin de se connecter au système d’authentification AD Azure doit avoir une valeur d’attribut de nom principal (UPN) utilisateur unique associée à ce compte. Dans le tableau suivant les stratégies qui s’appliquent à ces deux comptes d’utilisateurs de provenant d’Active Directory sur site (synchronisées vers le nuage) et aux comptes d’utilisateurs de nuage uniquement.

|   Propriété           |     Exigences de UserPrincipalName  |
|   ----------------------- |   ----------------------- |
|  Caractères autorisés    |  <ul> <li>À A Z.</li> <li>d’a - z </li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
|  Caractères non autorisés  | <ul> <li>Les '@' les caractères qui ne sont pas séparer le nom d’utilisateur du domaine.</li> <li>Ne peut pas contenir un point «. » qui précède immédiatement la '@' symbole</li></ul> |
| Contraintes de longueur  |       <ul> <li>Longueur totale ne doit pas dépasser 113 caractères</li><li>64 caractères avant de la ‘@’ symbole</li><li>48 caractères après le ‘@’ symbole</li></ul>

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Stratégies de mot de passe qui s’appliquent uniquement aux comptes d’utilisateurs de nuage

Le tableau suivant décrit les paramètres de stratégie de mot de passe disponibles qui peuvent être appliquées aux comptes d’utilisateur qui sont créés et gérés dans Active Directory Azure.

|  Propriété       |    Configuration requise          |
|   ----------------------- |   ----------------------- |
|  Caractères autorisés   |   <ul><li>À A Z.</li><li>d’a - z </li><li>0 – 9</li> <li>@# $ % ^ & * - _ ! + = [] {} & #124 ; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
|  Caractères non autorisés   |       <ul><li>Caractères Unicode</li><li>Espaces</li><li> **Mots de passe forts uniquement**: ne peut pas contenir de point '.' qui précède immédiatement la '@' symbole</li></ul> |
|   Restrictions de mot de passe | <ul><li>8 caractères au minimum et 16 caractères maximum</li><li>**Mots de passe forts uniquement**: nécessite un 3 sur 4 des éléments suivants :<ul><li>Caractères minuscules</li><li>Caractères majuscules</li><li>Chiffres (0-9)</li><li>Symboles (voir les restrictions de mot de passe ci-dessus)</li></ul></li></ul> |
| Durée d’expiration du mot de passe      | <ul><li>Valeur par défaut : **90** jours </li><li>La valeur est configurable à l’aide de l’applet de commande Set-MsolPasswordPolicy à partir du Module Azure pour annuaire Active pour Windows PowerShell.</li></ul> |
| Notification d’expiration de mot de passe |  <ul><li>Valeur par défaut : **14** jours (avant l’expiration du mot de passe)</li><li>La valeur est configurable à l’aide de l’applet de commande Set-MsolPasswordPolicy.</li></ul> |
| Expiration du mot de passe |  <ul><li>Valeur par défaut : **false** jours (indique que l’expiration mot de passe est activée) </li><li>Valeur peut être configurée pour les comptes d’utilisateurs individuels à l’aide de l’applet de commande Set-MsolUser. </li></ul> |
|  Historique du mot de passe  | Dernier mot de passe ne peut pas être utilisé à nouveau. |
|  Durée de l’historique de mot de passe | Pour toujours |
|  Verrouillage de compte | Après 10 tentatives connexion autorisées (mot de passe incorrect), l’utilisateur est verrouillé pendant une minute. Autres tentatives de connexion incorrectes va verrouiller l’utilisateur d’augmentation de la durée. |


## <a name="next-steps"></a>Étapes suivantes

* **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Gérer vos mots de passe de n’importe où](active-directory-passwords.md)
* [Comment fonctionne la gestion de mot de passe](active-directory-passwords-how-it-works.md)
* [Mise en route de la gestion de mot de passe](active-directory-passwords-getting-started.md)
* [Personnaliser la gestion de mot de passe](active-directory-passwords-customize.md)
* [Meilleures pratiques de gestion de mot de passe](active-directory-passwords-best-practices.md)
* [Comment obtenir des perspectives opérationnelles avec des rapports de gestion de mot de passe](active-directory-passwords-get-insights.md)
* [FAQ sur la gestion mot de passe](active-directory-passwords-faq.md)
* [Résoudre les problèmes de gestion de mot de passe](active-directory-passwords-troubleshoot.md)
* [Pour en savoir plus](active-directory-passwords-learn-more.md)
