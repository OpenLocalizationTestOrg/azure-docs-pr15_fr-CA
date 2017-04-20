<properties
    pageTitle="Ajouter de nouveaux utilisateurs à Active Directory de Azure | Microsoft Azure"
    description="Explique comment ajouter de nouveaux utilisateurs ou modifier des informations utilisateur dans Active Directory de Azure."
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
    ms.topic="get-started-article"
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="add-new-users--or-users-with-microsoft-accounts-to-azure-active-directory"></a>Ajouter de nouveaux utilisateurs ou utilisateurs avec des comptes de Microsoft Azure Active Directory

Ajouter des utilisateurs pour peupler votre répertoire. Cet article explique comment ajouter de nouveaux utilisateurs dans votre organisation et ajouter les utilisateurs qui disposent de comptes Microsoft. Pour plus d’informations sur l’ajout d’utilisateurs à partir d’autres annuaires dans Azure Active Directory ou l’ajout d’utilisateurs à partir de sociétés partenaires, voir [Ajout d’utilisateurs à partir d’autres répertoires ou les sociétés partenaires dans Azure Active Directory](active-directory-create-users-external.md). Utilisateurs n’ont pas les autorisations d’administrateur par défaut, mais vous pouvez attribuer des rôles à eux à tout moment.

## <a name="add-a-user"></a>Ajout d’un utilisateur

1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com) avec un compte qui est un administrateur global pour le répertoire.
2. Sélectionnez **Active Directory**et sélectionnez le nom de l’annuaire de votre organisation.
3. Sélectionnez l’onglet **utilisateurs** , puis, dans la barre de commandes, sélectionnez **Ajouter un utilisateur**.
4. Sur la page de **nous dire sur cet utilisateur** , sous **Type d’utilisateur**, sélectionnez une ou l’autre :

    - **Nouvel utilisateur de votre organisation** – ajoute un nouveau compte d’utilisateur dans votre répertoire.
    - **Utilisateur avec un compte existant de Microsoft** – ajoute un compte de consommation de Microsoft existant dans votre répertoire (par exemple, un compte Outlook)

5. Selon le **Type d’utilisateur**, entrez un nom d’utilisateur (pour le nouvel utilisateur) ou une adresse de messagerie (pour un utilisateur avec un compte Microsoft).
6. Sur la page de **profil** utilisateur, fournir un nom et prénom, un nom convivial et un rôle d’utilisateur à partir de la liste de **rôles** . Pour plus d’informations sur les rôles utilisateur et administrateur, voir [affectation les rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md). Spécifiez s’il faut **Activer l’authentification multifactorielle** pour l’utilisateur.
7. Sur la page **obtenir le mot de passe temporaire** , sélectionnez **créer**.

> [AZURE.IMPORTANT] Si votre organisation utilise plus d’un domaine, vous devez connaître les problèmes suivants lorsque vous ajoutez un compte d’utilisateur :
>
> - Pour ajouter des comptes d’utilisateurs avec le même nom utilisateur principal (UPN) sur plusieurs domaines, **premier** ajouter, par exemple, geoffgrisso@contoso.onmicrosoft.com, **suivie par** geoffgrisso@contoso.com.
> - **Ne pas** ajouter de geoffgrisso@contoso.com avant d’ajouter des geoffgrisso@contoso.onmicrosoft.com. Cet ordre est important et peut être difficile à annuler.

## <a name="change-user-information"></a>Modifier les informations de l’utilisateur

Vous pouvez modifier n’importe quel attribut de l’utilisateur, à l’exception de l’ID d’objet.

1. Ouvrez votre répertoire.
2. Sélectionnez l’onglet **utilisateurs** , puis sélectionnez le nom complet de l’utilisateur que vous souhaitez modifier.
3. Effectuez les modifications souhaitées, puis cliquez sur **Enregistrer**.

Si l’utilisateur que vous modifiez est synchronisée avec votre service d’Active Directory sur site, vous ne pouvez pas modifier les informations utilisateur à l’aide de cette procédure. Pour modifier l’utilisateur, utilisez vos outils de gestion Active Directory local.

## <a name="guest-user-management-and-limitations"></a>Limitations et la gestion des utilisateurs invités

Les comptes invités sont des utilisateurs à partir d’autres annuaires qui ont été invitées à votre répertoire d’accéder aux documents SharePoint, des applications ou autres ressources Azure. Un compte invité dans votre répertoire a son attribut UserType sous-jacente la valeur « Invité ». Les utilisateurs normaux (en particulier, les membres de votre répertoire) ont l’attribut UserType « Member ».

Invités ont un ensemble limité de droits dans le répertoire. Ces droits de limitent la possibilité pour les visiteurs pour découvrir des informations sur les autres utilisateurs dans l’annuaire. Toutefois, les utilisateurs invités peuvent toujours interagir avec les utilisateurs et les groupes associés aux ressources sur que lesquels ils travaillent. Les utilisateurs invités peuvent :

- Voir les autres utilisateurs et des groupes associés à un abonnement Azure auxquelles elles sont affectées
- Consultez les membres des groupes auxquels ils appartiennent
- Rechercher d’autres utilisateurs dans l’annuaire, s’ils connaissent l’adresse électronique complète de l’utilisateur
- Voir uniquement un ensemble limité d’attributs des utilisateurs qu’ils recherchent--limitées pour afficher une photo miniature, nom d’utilisateur principal (UPN), nom et adresse de courriel
- Obtenir la liste des domaines vérifiés dans l’annuaire
- Pour les applications, en leur accordant le même accès que les membres ont dans votre répertoire de consentement

## <a name="set-guest-user-access-policies"></a>Définir les stratégies d’accès utilisateur invité

L’onglet de **configuration** d’un répertoire inclut des options pour contrôler l’accès pour les utilisateurs invités. Ces options peuvent être modifiées uniquement dans Azure portal classique par un administrateur global du répertoire. Il n’existe actuellement aucune méthode PowerShell ou API.

Pour ouvrir l’onglet **configurer** dans Azure portal classique, sélectionnez **Active Directory**, puis sélectionnez le nom du répertoire.

![Onglet Configurer dans Azure Active Directory][1]

Vous pouvez modifier les options pour contrôler l’accès pour les utilisateurs invités.

![options de contrôle d’accès pour les utilisateurs invités][2]


## <a name="whats-next"></a>Quel est l’avenir

- [Ajouter des utilisateurs à partir d’autres répertoires ou les sociétés partenaires dans Azure Active Directory](active-directory-create-users-external.md)
- [Administration AD Azure](active-directory-administer.md)
- [Gérer les mots de passe dans Active Directory Azure](active-directory-manage-passwords.md)
- [Gérer des groupes dans AD Azure](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png
