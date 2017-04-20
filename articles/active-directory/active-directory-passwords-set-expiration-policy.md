<properties
    pageTitle="Définir des stratégies d’expiration de mot de passe dans Active Directory de Azure | Microsoft Azure"
    description="Découvrez comment vérifier les stratégies d’expiration et de modifier l’expiration de mot de passe d’utilisateur individuellement ou en bloc pour les mots de passe Azure Active directory"
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


# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Définir des stratégies d’expiration de mot de passe dans Azure Active Directory

> [AZURE.IMPORTANT] **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

En tant qu’un administrateur global pour un service de cloud de Microsoft, vous pouvez utiliser le Microsoft Azure Active Directory Module pour Windows PowerShell pour configurer les mots de passe utilisateur ne pas le point d’expirer. Vous pouvez également utiliser les applets de commande Windows PowerShell pour supprimer le-n’expire jamais configuration, ou quel utilisateur mots de passe sont paramétrés ne pas expirer. Cet article fournit une aide pour les services en nuage, telles que Microsoft Intune et Office 365, qui s’appuient sur Microsoft Azure Active Directory pour les services d’annuaire et d’identité.

  > [AZURE.NOTE] Uniquement les mots de passe des comptes d’utilisateurs qui ne sont pas synchronisés par le biais de la synchronisation d’annuaire peuvent être configurés pour ne pas pour expirer. Pour plus d’informations sur la synchronisation d’annuaire, consultez la liste des rubriques [d’introduction de synchronisation d’annuaire](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Pour utiliser les applets de commande Windows PowerShell, vous devez d’abord installer les.

## <a name="what-do-you-want-to-do"></a>Tu veux faire quoi ?

- [Comment faire pour vérifier la stratégie d’expiration pour un mot de passe](#how-to-check-expiration-policy-for-a-password)

- [Définir un mot de passe expire](#set-a-password-to-expire)

- [Définir un mot de passe afin qu’il ne va pas expirer](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>Comment faire pour vérifier la stratégie d’expiration pour un mot de passe

1.  Se connecter à Windows PowerShell à l’aide de vos informations d’identification d’administrateur de la société.

2.  Effectuez l’une des opérations suivantes :

    - Pour voir si un seul mot de passe est défini pour ne jamais expirer, exécutez l’applet de commande suivante à l’aide du nom d’utilisateur principal (UPN) (par exemple, aprilr@contoso.onmicrosoft.com) ou l’ID utilisateur de l’utilisateur que vous souhaitez vérifier :`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

    - Pour voir le paramètre « Le mot de passe n’expire jamais » pour tous les utilisateurs, exécutez l’applet de commande suivante :`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>Définir un mot de passe expire

1.  Se connecter à Windows PowerShell à l’aide de vos informations d’identification d’administrateur de la société.

2.  Effectuez l’une des opérations suivantes :

    - Pour définir le mot de passe d’un utilisateur afin que le mot de passe expire, exécutez l’applet de commande suivante à l’aide du nom d’utilisateur principal (UPN) ou l’ID utilisateur de l’utilisateur :`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`

    - Pour définir les mots de passe de tous les utilisateurs de l’organisation afin qu’il expire, utilisez l’applet de commande suivante :`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>Définir un mot de passe n’expire jamais

1. Se connecter à Windows PowerShell à l’aide de vos informations d’identification d’administrateur de la société.

2.  Effectuez l’une des opérations suivantes :

    - Pour définir le mot de passe d’un utilisateur n’expire jamais, exécutez l’applet de commande suivante à l’aide du nom d’utilisateur principal (UPN) ou l’ID utilisateur de l’utilisateur :`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`

    - Pour définir les mots de passe de tous les utilisateurs d’une organisation n’expire jamais, exécutez l’applet de commande suivante :`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Étapes suivantes

* **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
