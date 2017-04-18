<properties
    pageTitle="Obtenir démarré Azure AMF dans le nuage | Microsoft Azure"
    description="Il s’agit de la page d’authentification Microsoft Azure plusieurs facteurs qui décrit la procédure de mise en route de l’AMF Azure dans le nuage."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Mise en route avec authentification à plusieurs facteurs Azure dans le nuage
Cet article décrit comment démarrer à l’aide de l’authentification multifactorielle Azure dans le nuage.

> [AZURE.NOTE]  La documentation suivante fournit des informations sur l’activation des utilisateurs à l’aide du **Portail classique d’Azure**. Si vous recherchez des informations sur le paramétrage d’Azure, plusieurs facteurs d’authentification pour les utilisateurs de O365, reportez-vous à [paramétrage d’authentification à plusieurs facteurs pour Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![AMF dans le nuage](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Conditions préalables
Les conditions préalables suivantes sont nécessaires avant de pouvoir activer l’authentification selon plusieurs facteurs Azure pour vos utilisateurs.


1. [S’inscrire pour un abonnement Azure](https://azure.microsoft.com/pricing/free-trial/) - si vous ne disposez pas déjà d’un abonnement Azure, vous devez d’abonnement pour une. Si vous êtes simplement démarrage et à l’aide d’Azure AMF, vous pouvez utiliser un abonnement d’essai
2. [Créer un fournisseur d’authentification selon plusieurs facteurs](multi-factor-authentication-get-started-auth-provider.md) et l’affecter à votre répertoire ou [l’attribution de licences aux utilisateurs](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  Les licences sont disponibles pour les utilisateurs qui ont Azure AMF, Azure AD Premium ou Suite de mobilité d’entreprise (EMS).  AMF est inclus dans Azure AD Premium et l’EMS. Si vous avez assez de licences, vous n’avez pas besoin de créer un fournisseur d’authentification.


## <a name="turn-on-two-step-verification-for-users"></a>Activer la vérification en deux étapes pour les utilisateurs
Pour démarrer nécessitant deux-début vérification sur un utilisateur, modifier l’état de désactivé à activé.  Pour plus d’informations sur les États utilisateur, consultez [États utilisateur dans Azure plusieurs facteurs d’authentification](multi-factor-authentication-get-started-user-states.md)

Utilisez la procédure suivante pour activer l’AMF pour vos utilisateurs.

### <a name="to-turn-on-multi-factor-authentication"></a>Pour activer une authentification multifactorielle

1.  Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com) en tant qu’administrateur.
2.  Sur la gauche, cliquez sur **Active Directory**.
3.  Sous annuaire, sélectionnez le répertoire de l’utilisateur que vous souhaitez activer.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En haut, cliquez sur **utilisateurs**.
5.  En bas de la page, cliquez sur **Gérer les authentification selon plusieurs facteurs**. Un nouvel onglet de navigateur s’ouvre.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Trouver l’utilisateur que vous souhaitez activer pour la vérification des deux étapes. Vous devrez peut-être modifier l’affichage située en haut. Vérifiez que l’état est **désactivé.** 
 ![Activer utilisateur](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Placez une **vérification** dans la case en regard de leur nom.
7.  Sur la droite, cliquez sur **Activer**.
![Activer utilisateur](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Cliquez sur **Activer l’authentification de plusieurs facteur**.
![Activer utilisateur](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Notez que l’état a changé de **désactivé** à **activé**.
![Permettre aux utilisateurs](./media/multi-factor-authentication-get-started-cloud/user.png)

Une fois que vous avez activé vos utilisateurs, vous devez les avertir par e-mail. La prochaine fois qu’ils essaient de se connecter, ils vous demandera d’inscrire leur compte pour la vérification des deux étapes. Une fois qu’ils démarrent à l’aide de la vérification de deux étapes, ils devrez également définir des mots de passe app afin d’éviter les verrouillés sur les applications autres que des navigateurs.


## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Utilisation de PowerShell pour automatiser l’activation de la vérification, en deux étapes

Pour modifier l' [état](multi-factor-authentication-whats-next.md) à l’aide [d’Azure AD PowerShell](../powershell-install-configure.md), vous pouvez utiliser les éléments suivants.  Vous pouvez modifier `$st.State` égale à un des états suivants :

- Activé
- Mise en œuvre
- Désactivé  

> [AZURE.IMPORTANT]  Nous déconseillons contre le déplacement d’utilisateurs à l’état appliqué directement à partir de l’état de désactivation. Applications basées sur le navigateur non fonctionnent plus, car l’utilisateur n’a pas subi d’inscription de l’AMF et obtenu un [mot de passe app](multi-factor-authentication-whats-next.md#app-passwords). Si vous avez des applications non basées sur un navigateur et que vous nécessitent des mots de passe app, nous vous recommandons que vous passez d’un état désactivé à activé. Cela permet aux utilisateurs d’enregistrer et d’obtenir leurs mots de passe app. Après cela, vous pouvez les déplacer appliqué.

À l’aide de PowerShell est une option de bloc permettant aux utilisateurs. Actuellement, il n’existe aucune fonctionnalité d’activer en bloc dans le portail Azure et vous devez sélectionner chaque utilisateur individuellement. Cela peut s’avérer assez si vous avez beaucoup d’utilisateurs. En créant un PowerShell script à l’aide de la commande suivante, vous pouvez exécuter une boucle sur une liste d’utilisateurs et les activer.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Voici un exemple :

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Pour plus d’informations, consultez [États utilisateur dans Azure plusieurs facteurs d’authentification](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez configuré l’authentification selon plusieurs facteurs Azure dans le nuage, vous pouvez configurer et paramétrer votre déploiement. Pour plus d’informations, reportez-vous à la section [Configuration de l’authentification multifactorielle Azure](multi-factor-authentication-whats-next.md) .
