<properties
    pageTitle="Gérer les mots de passe dans Active Directory de Azure | Microsoft Azure"
    description="La gestion des mots de passe dans Azure Active Directory."
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
    ms.date="08/23/2016"
    ms.author="curtand"/>

# <a name="manage-passwords-in-azure-active-directory"></a>Gérer les mots de passe dans Azure Active Directory

Si vous êtes un administrateur, vous pouvez réinitialiser un mot de passe utilisateur dans Azure Active Directory (AD Azure) dans Azure portal classique. Cliquez sur le nom de votre répertoire et sur la page utilisateurs, cliquez sur le nom de l’utilisateur et au bas du portail, cliquez sur **Réinitialiser le mot de passe**.

Le reste de cette rubrique couvre l’ensemble des fonctionnalités de gestion de mot de passe Active Directory Azure prend en charge, y compris :

- Modification du **mot de passe libre-service** permet aux utilisateurs finaux et les administrateurs à modifier leur mot de passe a expiré ou non expiré sans appeler l’administrateur ou le support technique pour la prise en charge.
- Réinitialisation du **mot de passe libre-service** permet aux utilisateurs finaux et les administrateurs réinitialiser leurs mots de passe d’automatiquement sans appeler l’administrateur ou le support technique pour la prise en charge. Réinitialisation du mot de passe libre-service requiert Azure AD Premium ou Basic. Pour plus d’informations, consultez [éditions d’Azure Active Directory](active-directory-editions.md).
- **Réinitialisation du mot de passe lancées par l’administrateur** permet à un administrateur de réinitialiser d’un utilisateur final ou d’un autre administrateur mot de passe à partir du portail classique Azure.
- **Rapports d’activité de gestion de mot de passe** permettent aux administrateurs appréhender activité d’enregistrement et de réinitialisation mot de passe dans leur organisation.
- **L’écriture différée de mot de passe** permet la gestion des mots de passe locaux du nuage afin que tous les scénarios ci-dessus peuvent être exécutées par ou pour le compte de, fédérés ou les utilisateurs de synchronisation de mot de passe. L’écriture différée de mot de passe nécessite Azure AD Premium. Pour plus d’informations, consultez [mise en route avec Azure Active Directory prime](active-directory-get-started-premium.md).

> [AZURE.NOTE]
> Azure AD Premium est disponible pour les clients chinois à l’aide de l’instance dans le monde d’Azure AD. Azure AD Premium n’est pas pris en charge dans le service Microsoft Azure exploité par 21Vianet en Chine. Pour plus d’informations, contactez-nous à l' [Azure Active Directory Forum](https://feedback.azure.com/forums/169401-azure-active-directory/).

Utilisez les liens suivants pour accéder à la documentation qui que vous intéressez le plus :

- [La vue d’ensemble : gestion de mots de passe dans Azure AD](active-directory-passwords-how-it-works.md)
- [Mot de passe réinitialisé dans Azure AD : comment faire pour activer, configurer et tester de réinitialisation de mot de passe libre-service](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
- [Mot de passe réinitialisé dans Azure AD : comment personnaliser le mot de passe réinitialisé pour répondre à vos besoins](active-directory-passwords-customize.md)
- [Mot de passe réinitialisé dans AD Azure : déploiement et gestion des meilleures pratiques](active-directory-passwords-best-practices.md)
- [Rapports sur la gestion de mot de passe dans Active Directory Azure : comment afficher l’activité de gestion de mot de passe de vos clients](active-directory-passwords-get-insights.md)
- [L’écriture différée de mot de passe : comment configurer AD Azure pour gérer les mots de passe sur site](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
- [Dépannage de la gestion de mot de passe Active Directory Azure](active-directory-passwords-troubleshoot.md)
- [Forum aux questions sur la gestion des mots de passe Active Directory Azure](active-directory-passwords-faq.md)


## <a name="whats-next"></a>Quel est l’avenir

- [Administration AD Azure](active-directory-administer.md)
- [Créer ou modifier des utilisateurs dans Active Directory Azure](active-directory-create-users.md)
- [Gérer des groupes dans AD Azure](active-directory-manage-groups.md)
