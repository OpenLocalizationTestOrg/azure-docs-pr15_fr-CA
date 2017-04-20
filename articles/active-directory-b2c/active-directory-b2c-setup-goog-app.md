<properties
    pageTitle="Azure B2C de répertoire actif : Google + configuration | Microsoft Azure"
    description="Fournir d’inscription et de connexion aux clients avec les comptes Google + dans les applications qui sont sécurisées par Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure B2C de répertoire actif : Fournir d’inscription et de connexion aux consommateurs avec Google + comptes

## <a name="create-a-google-application"></a>Créer une application Google +

Pour utiliser Google + en tant que fournisseur d’identité dans Azure Active Directory (AD Azure) B2C, vous devez créer une application Google + et lui fournit les paramètres de droit. Vous avez besoin d’un compte Google + pour cela. Si vous n’en avez pas, vous pouvez l’obtenir à [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Accédez à la [Console de développeurs de Google](https://console.developers.google.com/) et vous connecter avec vos informations d’identification compte Google +.
2. Cliquez sur **créer un projet**, entrez un **nom de projet**, puis cliquez sur **créer**.

    ![Google + - mise en route](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google + - nouveau projet](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Cliquez sur **Gestionnaire d’API** et puis cliquez sur **informations d’identification** dans la navigation de gauche.
4. Cliquez sur l’onglet **écran de consentement OAuth** en haut.

    ![Google + - informations d’identification](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Sélectionnez ou spécifiez l' **adresse de messagerie**valide, fournir un **nom de produit**et cliquez sur **Enregistrer**.

    ![Google + - écran de consentement OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Cliquez sur **nouvelles informations d’identification** , puis choisissez **l’ID client OAuth**.

    ![Google + - écran de consentement OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. Sous **type d’Application**, sélectionnez **application Web**.

    ![Google + - écran de consentement OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Fournir un **nom** pour votre application, entrez `https://login.microsoftonline.com` dans le champ **les origines autorisé de JavaScript** , et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **autorisés rediriger URI** . Remplacez **{client}** avec le nom de votre client (par exemple, contosob2c.onmicrosoft.com). La valeur de **{client}** est sensible à la casse. Cliquez sur **créer**.

    ![Google + - création d’ID de client](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copier les valeurs **d’ID de Client** et le **secret du Client**. Vous devez les deux pour configurer Google + en tant que fournisseur d’identité de vos clients. **Secret des clients** est une information d’identification de sécurité important.

    ![Google + - secret Client](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Configurer Google + en tant que fournisseur d’identité de vos clients

1. Procédez comme suit pour [accéder à la lame de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Sur la lame de fonctionnalités B2C, cliquez sur **fournisseurs d’identité**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Fournir le **nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « G + ».
5. Cliquez sur le **type de fournisseur d’identité**, sélectionnez **Google**et cliquez sur **OK**.
6. Cliquez sur **configurer ce fournisseur d’identité** , entrez l’ID de client et le secret de client de l’application Google + que vous avez créé précédemment.
7. Cliquez sur **OK** , puis sur **créer** pour enregistrer votre configuration Google +.
