<properties
    pageTitle="Impossible de se connecter à un abonnement Azure | Microsoft Azure"
    description="Explique comment résoudre certains problèmes de connexion abonnement Azure."
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="genli"/>

# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>Je ne parviens pas à connecter à gérer mon abonnement Azure

Cet article décrit quelques-unes des méthodes plus courantes pour résoudre les problèmes de connexion.

## <a name="page-hangs-in-the-loading-status"></a>Page se bloque dans l’état de chargement

Si la page de votre navigateur internet se bloque, essayez chacune des étapes suivantes jusqu'à ce que vous pouvez obtenir sur le [portail Azure](https://portal.azure.com).

-   Actualisez la page.
-   Utilisez un autre navigateur Internet.
-   Si vous utilisez Microsoft Internet Explorer, accédez au portail Azure en utilisant le mode de navigation InPrivate. 

    A.  Cliquez sur **Outils** ![bouton outils](./media/billing-cannot-login-subscription/Toolsbutton.png) > **sécurité** > **La navigation InPrivate**.

    B.  Accédez au [portail Azure](https://portal.azure.com)et puis vous connecter au portail.

## <a name="error-message-no-subscriptions-found"></a>Message d’erreur « Aucun abonnement trouvé »

Si votre compte ne dispose pas des autorisations suffisantes, vous pouvez voir un message d’erreur **Aucun abonnement trouvé** . Seul un administrateur de compte peut atteindre le [Centre du compte](https://account.windowsazure.com/), pas le service (administrateur) ou des administrateurs (CA).

**Scénario 1 : Message d’erreur apparaît dans le [portail Azure](https://portal.azure.com)**

Pour résoudre ce problème, [Ajoutez le rôle co-administrateur ou le propriétaire](billing-add-change-azure-subscription-administrator.md) du compte.

**Scénario 2 : Message d’erreur apparaît dans le [Centre de compte Azure](https://account.windowsazure.com/Subscriptions)**

Vérifie si le compte que vous avez utilisé l’administrateur de compte. Pour vérifier le compte administrateur, procédez comme suit :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Dans le menu de concentrateur, sélectionnez **l’abonnement**.
3.  Sélectionner l’abonnement que vous souhaitez vérifier, puis sélectionnez **paramètres**.
4.  Sélectionnez **Propriétés**. L’administrateur du compte de l’abonnement s’affiche dans la zone **Compte Admin** .

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Vous êtes automatiquement connecté en tant qu’autre utilisateur

Ce problème peut se produire si vous utilisez plus d’un compte d’utilisateur dans un navigateur Internet.

Pour résoudre ce problème, essayez l’une des méthodes suivantes :

-   Effacer le cache et supprimer les cookies Internet. Dans Internet Explorer, cliquez sur **Outils** ![bouton outils](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Options Internet** > **Supprimer**. Assurez-vous que les cases à cocher pour les fichiers temporaires, les cookies, mot de passe et l’historique de navigation sont sélectionnées, puis cliquez sur Supprimer.

-   Réinitialiser les paramètres d’Internet Explorer pour rétablir tous les paramètres personnels que vous avez apportées. Cliquez sur **Outils** ![bouton outils](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Options Internet** > **Avancé** > activez la case **Supprimer les paramètres personnels** > **Réinitialiser**.

-   Accédez au portail Azure en mode de navigation InPrivate. Cliquez sur **Outils** ![bouton outils](./media/billing-cannot-login-subscription/Toolsbutton.png) > **sécurité** > **La navigation InPrivate**.

## <a name="need-help-contact-support"></a>Besoin d’aide ? Contactez le support technique. 

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) à résoudre rapidement votre problème. 