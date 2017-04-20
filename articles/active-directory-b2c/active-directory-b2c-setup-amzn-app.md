<properties
    pageTitle="Azure B2C de répertoire actif : Configuration d’Amazon | Microsoft Azure"
    description="Fournir d’inscription et de connexion aux clients avec les comptes d’Amazon dans vos applications qui sont sécurisées par Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure B2C de répertoire actif : Fournir d’inscription et de connexion aux consommateurs avec des comptes d’Amazon

## <a name="create-an-amazon-application"></a>Création d’une application d’Amazon

Pour utiliser Amazon comme fournisseur d’identité dans Azure Active Directory (AD Azure) B2C, vous devez créer une application Amazon et fournir les paramètres de droit. Vous avez besoin d’un compte d’Amazon pour cela. Si vous n’en avez pas, vous pouvez l’obtenir à [http://www.amazon.com/](http://www.amazon.com/).

1. Accédez au [Centre de développement d’Amazon](https://login.amazon.com/) et vous connecter avec vos informations d’identification de compte Amazon.
2. Si vous ne le n'avez pas déjà fait, cliquez sur **Souscrire un abonnement**, suivez les étapes de l’inscription de développeur et accepter la stratégie.
3. Cliquez sur **Enregistrer la nouvelle application**.

    ![Inscription d’une nouvelle application sur le site Web d’Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Fournir des informations sur l’application (**nom**, **Description**et **URL de notification confidentialité**) et cliquez sur **Enregistrer**.

    ![Fournir des informations d’application pour l’enregistrement d’une nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Dans la section **Paramètres Web** , copier les valeurs **d’ID de Client** et le **Secret du Client**. (Vous devez cliquer sur le bouton **Afficher le Secret** pour voir cela). Vous devez les deux pour configurer Amazon comme fournisseur d’identité de vos clients. Cliquez sur **Modifier** au bas de la section. **Secret des clients** est une information d’identification de sécurité important.

    ![Fournir des ID de Client et le Secret de Client pour votre nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Entrez `https://login.microsoftonline.com` dans le champ **Autorisés les origines de JavaScript** et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URL de retour autorisé** . Remplacez **{client}** avec le nom de votre client (par exemple, contoso.onmicrosoft.com). Cliquez sur **Enregistrer**. La valeur de **{client}** est sensible à la casse.

    ![Fournissant des origines de JavaScript et de renvoyer une URL pour votre nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Configurer Amazon comme fournisseur d’identité de vos clients

1. Procédez comme suit pour [accéder à la lame de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Sur la lame de fonctionnalités B2C, cliquez sur **fournisseurs d’identité**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Fournir le **nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « Amzn ».
5. Cliquez sur le **type de fournisseur d’identité**, sélectionnez **Amazon**et cliquez sur **OK**.
6. Cliquez sur **configurer ce fournisseur d’identité** , entrez l’ID de client et le secret de client de l’application d’Amazon que vous avez créé précédemment.
7. Cliquez sur **OK** , puis sur **créer** pour enregistrer votre configuration Amazon.
