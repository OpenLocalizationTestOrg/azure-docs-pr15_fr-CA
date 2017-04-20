<properties
    pageTitle="Azure B2C de répertoire actif : Configuration de Facebook | Microsoft Azure"
    description="Fournir d’inscription et de connexion aux clients avec les comptes de Facebook dans vos applications qui sont sécurisées par Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure B2C de répertoire actif : Fournir d’inscription et de connexion aux consommateurs avec les comptes de Facebook

## <a name="create-a-facebook-application"></a>Créer une application Facebook

Pour utiliser Facebook en tant que fournisseur d’identité dans Azure Active Directory (AD Azure) B2C, vous devez créer une application Facebook et fournir les paramètres de droit. Vous avez besoin d’un compte Facebook pour cela. Si vous n’en avez pas, vous pouvez l’obtenir à [https://www.facebook.com/](https://www.facebook.com/).

1. Atteindre le site Web [Facebook pour les développeurs](https://developers.facebook.com/) et vous connecter avec vos informations d’identification de compte Facebook.
2. Si vous ne le n'avez pas déjà fait, vous devez enregistrer en tant que développeur Facebook. Pour ce faire, cliquez sur **Enregistrer** (dans le coin supérieur droit de la page), accepter les stratégies de Facebook et terminer la procédure d’inscription.
3. Cliquez sur **Mes applications** et puis cliquez sur **Ajouter une nouvelle application**. Choisissez le **site Web** , comme la plate-forme, puis cliquez sur **Ignorer et créer des ID de l’application**.

    ![Facebook - ajouter une nouvelle App](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook - ajouter une nouvelle App - site Web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook - créer des ID de l’application](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. Dans le formulaire, fournir un **Nom complet**, un valide **E-mail de Contact**, une **catégorie**, puis cliquez sur **Créer un ID App**. Vous devez accepter les stratégies de plate-forme Facebook et effectuer une vérification de sécurité en ligne.

    ![Facebook - créer un nouvel ID App](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. Dans la barre de navigation gauche, cliquez sur **paramètres** .
6. Cliquez sur **+ Ajouter plate-forme** , puis sélectionnez **site Web**.

    ![Facebook - paramètres](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Site Web de Facebook - paramètres-](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Entrez [https://login.microsoftonline.com/](https://login.microsoftonline.com/) dans le champ **URL du Site** , puis sur **Enregistrer les modifications**.

    ![Facebook - URL du Site](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

8. Copiez la valeur de **l’ID de l’application**. Cliquez sur **Afficher** et copier la valeur du **Secret de l’application**. Vous devez les deux pour configurer Facebook comme fournisseur d’identité de vos clients. **Secret de l’application** est une information d’identification de sécurité important.

    ![Facebook - ID de l’application et le Secret de l’application](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)

9. Cliquez sur **+ Ajouter produit** sur la navigation de gauche, puis sur le bouton **Démarrer** en regard de la **Connexion Facebook**.

    ![Facebook - connexion Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)

10. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **valide OAuth rediriger URI** dans la section **Paramètres de OAuth Client** . Remplacez **{client}** avec le nom de votre client (par exemple, contosob2c.onmicrosoft.com). Cliquez sur **Enregistrer les modifications** en bas de la page.

    ![Facebook - l’URI de redirection OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

11. Pour rendre votre application Facebook utilisable par Azure AD B2C, vous avez besoin pour le rendre accessible au public. Vous pouvez pour cela en cliquant sur **Révision de l’application** dans la barre de navigation gauche et en activant le commutateur en haut de la page à **Oui** et en cliquant sur **Confirmer**.

    ![Facebook - App public](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configurer Facebook comme fournisseur d’identité de vos clients

1. Procédez comme suit pour [accéder à la lame de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Sur la lame de fonctionnalités B2C, cliquez sur **fournisseurs d’identité**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Fournir le **nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « FB ».
5. Cliquez sur le **type de fournisseur d’identité**, sélectionnez **Facebook**et cliquez sur **OK**.
6. Cliquez sur **configuration de ce fournisseur d’identité** et entrez le code secret d’application (de l’application Facebook que vous avez créé précédemment) et ID de l’application dans **l’ID de Client** et le **secret Client** champs respectivement.
7. Cliquez sur **OK**, puis cliquez sur **créer** pour enregistrer votre configuration de Facebook.
