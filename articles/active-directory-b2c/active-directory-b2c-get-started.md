<properties
    pageTitle="Azure B2C de répertoire actif : Créer un locataire Azure Active Directory B2C | Microsoft Azure"
    description="Une rubrique sur la création d’un locataire Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.topic="article"
    ms.devlang="na"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure B2C de répertoire actif : Créer un locataire Azure AD B2C

Pour commencer à utiliser Microsoft Azure Active Directory (AD Azure) B2C, suivez les trois étapes décrites dans cet article.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Étape 1 : Inscrivez-vous à un abonnement Azure

Si vous disposez déjà d’un abonnement Azure, ignorez cette étape. Si ce n’est pas le cas, souscrire à un [abonnement Azure](../active-directory/sign-up-organization.md) et obtenir l’accès à Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Étape 2 : Créer un locataire Azure AD B2C

Utilisez les étapes suivantes pour créer un nouveau locataire Azure AD B2C. B2C fonctionnalités ne peuvent pas être activées dans votre locataires existants.

1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com/) comme l’administrateur d’abonnement. C’est le même travail ou le compte de l’établissement ou le même compte Microsoft que vous avez utilisé pour vous inscrire pour Azure.
2. Cliquez sur **Nouveau** > **application Services** > **Active Directory** > **répertoire** > **créer des personnalisées**.

    ![Capture d’écran de démarrage créer un client](./media/active-directory-b2c-get-started/new-directory.png)

3. Cliquez sur le **nom**, le **Nom de domaine** et le **pays ou la région** pour vos clients.
4. Activez l’option indiquant **qu’il s’agit d’un répertoire B2C**.
5. Cliquez sur la case à cocher pour terminer l’action.

    ![Capture d’écran de la case à cocher pour créer un répertoire B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Votre client est maintenant créé et s’affiche dans l’extension de l’Active Directory. Un administrateur Global du locataire sont également apportées. Vous pouvez ajouter d’autres administrateurs globaux selon vos besoins.

    > [AZURE.IMPORTANT]
    Si vous envisagez d’utiliser un client B2C pour une application de production, lisez l’article de [production à l’échelle et les locataires aperçu B2C](active-directory-b2c-reference-tenant-type.md). Notez qu’il existe des connus des problèmes lorsque vous supprimez un locataire B2C existant et créez de nouveau avec le même nom de domaine. Vous devez créer un locataire B2C avec un nom de domaine différent.

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Étape 3 : Accédez à la lame de fonctionnalités B2C sur le portail Azure

1. Accédez à l’extension de Active Directory sur la barre de navigation sur le côté gauche.
2. Rechercher votre client sous l’onglet **répertoire** , puis cliquez dessus.
3. Cliquez sur l’onglet **configurer** .
4. Cliquez sur le lien dans la section **administration de B2C** **B2C de gérer les paramètres** .

    ![Capture d’écran de configuration de l’annuaire pour B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. Le portail Azure avec la lame de fonctionnalités B2C affichage s’ouvre dans une fenêtre ou un nouvel onglet du navigateur.

    > [AZURE.IMPORTANT]
    Il peut prendre jusqu'à 2-3 minutes pour que vos clients soient accessibles sur le portail Azure. Recommencer ces étapes après que quelque temps corrigera ce problème. Si ce n’est pas le cas, contactez le support technique.

6. Ajouter cette blade à votre Startboard pour faciliter l’accès. (L’outil coin est marqué en rouge dans le coin supérieur droit de la lame de fonctionnalités).

    ![Capture d’écran de la lame de fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Vous pouvez gérer les utilisateurs et groupes de configuration de réinitialisation de mot de passe et les fonctionnalités de personnalisation de société de votre client sur [Azure portal classique](https://manage.windowsazure.com/).

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Accès facile à la lame de fonctionnalités B2C sur le portail Azure

Pour améliorer la détectabilité, nous avons ajouté un raccourci à la blade de fonctionnalités B2C sur le portail Azure.

1. Connectez-vous au portail Azure en tant qu’administrateur Global de vos clients B2C. Si vous êtes déjà connecté à un autre client, passer les locataires (dans le coin supérieur droit).
2. Dans la barre de navigation gauche, cliquez sur **Parcourir** .
3. Cliquez sur **Azure AD B2C** pour accéder à la lame de fonctionnalités B2C.

    ![Capture d’écran de parcourir à lame de fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>Étapes suivantes

Apprenez à inscrire une application avec Azure AD B2C et pour générer une application de démarrage rapide en lisant [Azure Active Directory B2C : enregistrer votre application](active-directory-b2c-app-registration.md).
