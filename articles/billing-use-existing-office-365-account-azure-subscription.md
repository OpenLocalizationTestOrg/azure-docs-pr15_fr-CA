<properties
    pageTitle="Partager un seul client AD Azure via abonnements Office 365 et Azure | Microsoft Azure"
    description="Découvrez comment partager vos clients de publicité Azure Office 365 et de ses utilisateurs avec votre abonnement Azure, ou vice versa."
    services=""
    documentationCenter=""
    authors="JiangChen79"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="cjiang"/>

# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>Utilisez un compte Office 365 avec votre abonnement Azure, ou vice versa
Scénario : Vous avez déjà un abonnement à Office 365 et souhaitez destinés à un abonnement Azure, mais que vous souhaitez utiliser les comptes utilisateur Office 365 pour votre abonnement Azure. Également, vous êtes un abonné Azure et pour obtenir un abonnement à Office 365 pour les utilisateurs dans Azure Active Directory existant. Cet article vous montre combien il est facile d’atteindre deux.

> [AZURE.NOTE] Cet article ne s’applique pas aux clients de l’accord entreprise (EA). Si vous avez besoin d’obtenir de l’aide à tout moment dans cet article, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) à résoudre rapidement votre problème.


## <a name="quick-guidance"></a>Guide rapide

- Si vous avez déjà possédez un abonnement à Office 365 et que vous souhaitez inscrire pour Azure, utilisez l’option de **vous connecter avec votre compte d’organisation** . Poursuivez le processus d’abonnement Azure avec votre compte Office 365. Consultez [la procédure détaillée plus loin dans cet article](#s1).

- Si vous avez déjà un avez Azure et que vous souhaitez obtenir un abonnement à Office 365, vous connecter à Office 365 avec votre compte Azure. Passez ensuite à la procédure d’inscription. Après avoir terminé la procédure d’abonnement, l’abonnement à Office 365 est ajoutée à la même instance d’Azure Active Directory auquel appartient votre abonnement Azure. Pour plus d’informations, reportez-vous à section [procédure détaillée plus loin dans cet article](#s2).

>[AZURE.NOTE] Pour obtenir un abonnement à Office 365, le compte que vous utilisez pour d’abonnement doit être un membre du rôle de répertoire administrateur Global ou administrateur de facturation dans votre client Azure Active Directory. [Apprenez à déterminer le rôle dans Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

Pour comprendre ce qui se passe lorsque vous ajoutez un abonnement à un compte, consultez les [informations d’arrière-plan plus loin dans l’article](#background-information).

## <a name="detailed-steps"></a>Procédure détaillée
<a id="s1"></a>
### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>Scénario 1 : Office 365, les utilisateurs qui envisagent d’acheter Azure
Dans ce scénario, nous supposons que Kelley Wall est un utilisateur qui dispose d’un abonnement à Office 365 et a l’intention de s’abonner à Azure. Il existe deux autres utilisateurs actifs, Jane et Tricia. Compte de Kelley est admin@contoso.onmicrosoft.com.

![Centre d’administration utilisateur Office 365](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Pour vous inscrire pour Azure, procédez comme suit :

1. Inscrivez-vous pour Azure à [Azure.com](https://azure.microsoft.com/). Cliquez sur **essayer gratuitement**. Sur la page suivante, cliquez sur **Démarrer maintenant**.

    ![Essayez gratuitement d’Azure.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Cliquez sur **Connectez-vous à votre compte d’organisation**.

    ![Se connecter à Azure.](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Connectez-vous avec votre compte Office 365. Dans ce cas, il est le compte d’Office 365 de Kelley.

    ![Connectez-vous avec votre compte Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Renseignez les informations et terminez le processus d’inscription.

    ![Renseignez les informations et terminer l’inscription.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![Cliquez sur Démarrer, gestion de mon service.](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Maintenant vous êtes prêt. Dans le portail Azure, vous devriez voir les mêmes utilisateurs qui apparaissent. Pour vérifier cela, procédez comme suit :

1. Dans l’écran affiché précédemment, cliquez sur **commencer la gestion de mon service** .
2. Cliquez sur **Parcourir**, puis cliquez sur **Active Directory**.

    ![Cliquez sur Parcourir, puis cliquez sur Active Directory.](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Cliquez sur **utilisateurs**.

    ![L’onglet utilisateurs](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Tous les utilisateurs, y compris Kelley, sont répertoriées comme prévu.

    ![Liste des utilisateurs](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>Scénario 2 : Azure, les utilisateurs qui envisagent d’acheter Office 365

Dans ce scénario, Kelley Wall est un utilisateur qui possède un abonnement Azure sous le compte admin@contoso.onmicrosoft.com. Kelley souhaite s’abonner à Office 365 et utilisent le même répertoire qu’elle a déjà avec Azure.

>[AZURE.NOTE] Pour obtenir un abonnement à Office 365, le compte que vous utilisez pour la connexion doit être un membre du rôle de répertoire administrateur Global ou administrateur de facturation dans votre client Azure Active Directory. [Apprenez à connaître le rôle dans Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

![Paramètres d’abonnement de portail Azure](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Utilisateurs Active Directory du portail Azure](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Pour vous abonner à Office 365, procédez comme suit :

1. Accédez à la [page du produit Office 365](https://products.office.com/business)et sélectionnez un plan qui vous convient.
2. Après avoir sélectionné le plan, la page suivante s’affiche. Ne remplissez pas le formulaire. Dans le coin supérieur droit de la page, cliquez sur **connexion** .

    ![Page d’évaluation d’Office 365](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Connectez-vous en utilisant vos informations d’identification du compte. Dans cet exemple, il est compte de Kelley.

    ![Connexion à Office 365](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Cliquez sur **Essayer maintenant**.

    ![Confirmez votre commande d’Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. Dans la page de reçu de commande, cliquez sur **Continuer**.

    ![Réception de commandes d’Office 365](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Maintenant vous êtes prêt. Dans le centre d’administration Office 365, vous devez voir les utilisateurs à partir du répertoire de Contoso s’affiche en tant qu’utilisateurs actives. Pour vérifier cela, procédez comme suit :

1. Ouvrez le centre d’administration Office 365.
2. Développez **utilisateurs**, puis cliquez sur **Utilisateurs actifs**.

    ![Utilisateurs centre d’administration d’Office 365](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Comment faire pour connaître votre rôle dans Azure Active Directory

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir**, puis cliquez sur **Active Directory**.

    ![Active Directory dans le portail Azure](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Cliquez sur **utilisateurs**.

    ![Utilisateurs de Active Directory par défaut de portail Azure](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Cliquez sur l’utilisateur. Dans cet exemple, l’utilisateur est Kelley Wall.

    Notez que le champ du **Rôle d’organisation**.

    ![Identité de l’utilisateur du portail Azure](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Informations générales sur les abonnements d’Azure et Office 365
Office 365 et Azure utilisent le service Azure Active Directory pour gérer les utilisateurs et les abonnements. Considérer un répertoire Azure comme un conteneur dans lequel vous pouvez regrouper les utilisateurs et les abonnements. Pour utiliser le même compte d’utilisateur pour vos abonnements Azure et Office 365, vous devez vous assurer que les abonnements sont créés dans le même répertoire. Gardez à l’esprit les points suivants :

- Un abonnement est créé dans un répertoire, pas l’inverse.
- Les utilisateurs appartiennent à des répertoires, pas l’inverse.
- Un abonnement arrive dans le répertoire de l’utilisateur qui crée l’abonnement. Par conséquent, votre abonnement à Office 365 est lié sur le même compte que votre abonnement Azure lorsque vous utilisez ce compte pour créer l’abonnement d’Office 365.

![Informations générales](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Pour plus d’informations, consultez [comment Azure abonnements sont associés à Active Directory de Azure](./active-directory/active-directory-how-subscriptions-associated-directory.md).

>[AZURE.NOTE] Abonnements Azure appartiennent à des utilisateurs individuels dans le répertoire.

>[AZURE.NOTE] Les abonnements Office 365 sont détenues par le répertoire lui-même. Si les utilisateurs dans l’annuaire ont les autorisations requises, ils peuvent fonctionner sur ces abonnements.

## <a name="next-steps"></a>Étapes suivantes
Si vous avez acquis séparément votre Azure et Office 365 les abonnements dans le passé et que vous voulez être en mesure d’accéder à Office 365 locataire depuis l’abonnement Azure, voir [associer un client Office 365 avec un abonnement Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [AZURE.NOTE] Si vous avez encore des questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) à résoudre rapidement votre problème.
