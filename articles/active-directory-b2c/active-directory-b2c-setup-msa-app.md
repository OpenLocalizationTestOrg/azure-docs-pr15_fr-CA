<properties
    pageTitle="Azure B2C de répertoire actif : Configuration du compte Microsoft | Microsoft Azure"
    description="Fournir d’inscription et de connexion aux clients avec des comptes de Microsoft dans vos applications qui sont sécurisées par Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure B2C de répertoire actif : Fournir d’inscription et de connexion aux consommateurs avec des comptes de Microsoft

## <a name="create-a-microsoft-account-application"></a>Créer une application de compte Microsoft

Pour utiliser le compte de Microsoft en tant que fournisseur d’identité dans Azure Active Directory (AD Azure) B2C, vous devez créer une application de compte Microsoft et fournir les paramètres de droit. Vous avez besoin d’un compte Microsoft pour cela. Si vous n’en avez pas, vous pouvez l’obtenir à [https://www.live.com/](https://www.live.com/).

1. Accédez au [Portail de l’enregistrement d’applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) et vous connecter avec vos informations d’identification du compte Microsoft.
2. Cliquez sur **Ajouter une application**.

    ![Microsoft compte - Ajouter une nouvelle app](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Fournir un **nom** pour votre application, puis cliquez sur **créer une application**.

    ![Compte Microsoft - nom de l’application](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Copiez la valeur de **l’Id de l’Application**. Vous aurez besoin pour configurer le compte de Microsoft en tant que fournisseur d’identité de vos clients.

    ![Compte Microsoft - Id de l’Application](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)

5. Cliquez sur **Ajouter plate-forme** et choisissez **Web**.

    ![Microsoft compte - ajouter des plates-formes](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)

    ![Compte Microsoft - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)

6. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URI de redirection** . Remplacez **{client}** avec le nom de votre client (par exemple, contosob2c.onmicrosoft.com).

    ![Compte Microsoft - URL de redirection](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

7. Cliquez sur **Générer un nouveau mot de passe** sous la section **Secrets de l’Application** . Copiez le nouveau mot de passe s’affichée à l’écran. Vous aurez besoin pour configurer le compte de Microsoft en tant que fournisseur d’identité de vos clients. Ce mot de passe est une information d’identification de sécurité important.

    ![Microsoft compte - générer un nouveau mot de passe](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)

    ![Compte Microsoft - nouveau mot de passe](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)

8. Cochez la case indiquant que **la prise en charge de Live SDK** sous la section **Options avancées** . Cliquez sur **Enregistrer**.

    ![Compte Microsoft - prise en charge de Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Configurer le compte de Microsoft en tant que fournisseur d’identité de vos clients

1. Procédez comme suit pour [accéder à la lame de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Sur la lame de fonctionnalités B2C, cliquez sur **fournisseurs d’identité**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Fournir le **nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « MSA ».
5. Cliquez sur le **type de fournisseur d’identité**, sélectionnez le **compte Microsoft**et cliquez sur **OK**.
6. Cliquez sur **configurer ce fournisseur d’identité** , entrez l’Id d’Application et d’un mot de passe de l’application de compte Microsoft que vous avez créé précédemment.
7. Cliquez sur **OK** , puis sur **créer** pour enregistrer la configuration de votre compte Microsoft.
