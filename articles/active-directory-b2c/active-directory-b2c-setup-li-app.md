<properties
    pageTitle="Azure B2C de répertoire actif : Configuration de LinkedIn | Microsoft Azure"
    description="Fournir d’inscription et de connexion aux clients avec les comptes LinkedIn dans vos applications qui sont sécurisées par Azure Active Directory B2C"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure B2C de répertoire actif : Fournir d’inscription et de connexion aux consommateurs avec les comptes LinkedIn

## <a name="create-a-linkedin-application"></a>Créer une application LinkedIn

Pour utiliser LinkedIn comme fournisseur d’identité dans Azure Active Directory (AD Azure) B2C, vous devez créer une application LinkedIn et fournir les paramètres de droit. Vous avez besoin d’un compte LinkedIn pour cela. Si vous n’en avez pas, vous pouvez l’obtenir à [https://www.linkedin.com/](https://www.linkedin.com/).

1. Rendez-vous sur le [site Web des développeurs de LinkedIn](https://www.developer.linkedin.com/) et connectez-vous avec vos informations d’identification du compte LinkedIn.
2. Cliquez sur **Mes applications** dans la barre de menu, puis sur **Créer une Application**.

    ![LinkedIn - nouvelle appli](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. Dans l’écran **créer une nouvelle Application** , entrez les informations appropriées (**Nom de la société**, **nom**, **Description**, **URL de Logo de l’Application**, **Utilisation de l’Application**, **URL de site Web**, **E-mails** et **Téléphone professionnel**).
4. Accepter les **Conditions d’utilisation de LinkedIn API** et cliquez sur **Envoyer**.

    ![LinkedIn - Registre app](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Copier les valeurs **d’ID de Client** et le **Secret du Client**. (Vous trouverez les sous **Clés d’authentification**.) Vous devez les deux pour configurer LinkedIn comme fournisseur d’identité de vos clients.

    >[AZURE.NOTE] **Secret des clients** est une information d’identification de sécurité important.

6. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URL de redirection autorisé** (sous **OAuth 2.0**). Remplacez **{client}** avec le nom de votre client (par exemple, contoso.onmicrosoft.com). Cliquez sur **Ajouter**, puis cliquez sur **mise à jour**. La valeur de **{client}** est sensible à la casse.

    ![LinkedIn - application de configuration](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Configurer LinkedIn comme fournisseur d’identité de vos clients

1. Procédez comme suit pour [accéder à la lame de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Sur la lame de fonctionnalités B2C, cliquez sur **fournisseurs d’identité**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Fournir le **nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « LI ».
5. Cliquez sur le **type de fournisseur d’identité**, sélectionnez **LinkedIn**et cliquez sur **OK**.
6. Cliquez sur **configurer ce fournisseur d’identité** , entrez l’ID de client et le secret de client de l’application LinkedIn que vous avez créé précédemment.
7. Cliquez sur **OK** , puis sur **créer** pour enregistrer votre configuration LinkedIn.
