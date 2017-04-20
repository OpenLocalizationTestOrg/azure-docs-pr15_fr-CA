<properties
    pageTitle="Comment faire pour configurer l’authentification pour votre application de Services d’application Facebook"
    description="Découvrez comment configurer l’authentification Facebook pour votre application de Services d’application."
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Comment faire pour configurer votre application de Service de l’application pour utiliser la connexion Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique vous indique comment configurer le Service d’application Azure pour utiliser Facebook comme fournisseur d’authentification.

Pour terminer la procédure décrite dans cette rubrique, vous devez disposer d’un compte Facebook qui a une adresse vérifié et un numéro de téléphone portable. Pour créer un nouveau compte Facebook, accédez à [facebook.com].

## <a name="register"> </a>Votre application vous inscrire sur Facebook

1. Ouvrez une session sur le [portail Azure]et accédez à votre application. Copiez l' **URL**. Vous l’utiliserez pour configurer votre application Facebook.

2. Dans une autre fenêtre de navigateur, accédez au site Web [Des développeurs de Facebook] et à la connexion avec vos informations d’identification de compte Facebook.

3. (Facultatif) Si vous n’êtes pas déjà inscrit, cliquez sur **applications** > **Enregistrer en tant que développeur**, accepter la stratégie, puis suivez la procédure d’inscription.

4. Cliquez sur **Mes applications** > **Ajouter une nouvelle App** > **site Web** > **Ignorer et créer des ID de l’application**. 

5. Dans la zone **Nom complet**, tapez un nom unique pour votre application, votre **E-mail de Contact**, choisissez une **catégorie** pour votre application, puis cliquez sur **Créer un ID App** et terminer la vérification de la sécurité. Vous accédez au tableau de bord du développeur pour votre nouvelle application Facebook.

6. Sous « Connexion de Facebook », cliquez sur **Mise en route**. Ajout de votre application **Rediriger l’URI** **valide OAuth**rediriger URI, puis cliquez sur **Enregistrer les modifications**. 

    > [AZURE.NOTE] Votre URI de redirection est l’URL de votre application ajoutée avec le chemin d’accès, _/.auth/login/facebook/callback_. Par exemple, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Assurez-vous que vous utilisez le schéma HTTPS.

6. Dans le cadre de gauche, cliquez sur **paramètres**. Dans le champ **Application Secret** , cliquez sur **Afficher**, fournir votre mot de passe si demandé, puis prenez note des valeurs de **l’ID de l’application** et le **Secret de l’application**. Vous les utilisez ultérieurement pour configurer votre application dans Azure.

    > [AZURE.IMPORTANT] Le secret de l’application est une information d’identification de sécurité important. Ne pas partager ce secret avec n’importe qui ou distribuer dans une application cliente.

7. Le compte a été utilisé pour enregistrer l’application Facebook est un administrateur de l’application. À ce stade, seuls les administrateurs peuvent se connecter dans cette application. Pour authentifier d’autres comptes de Facebook, cliquez sur la **Révision de l’application** et activez les **rendent < votre-application-name > publics** activer l’accès public général à l’aide de l’authentification Facebook.

## <a name="secrets"> </a>Informations de Facebook d’ajouter à votre application

1. Dans le [portail Azure], accédez à votre application. Cliquez sur **paramètres** > **l’authentification / autorisation**et assurez-vous que **Authentification de Service d’application** est **activé**.

2. Cliquez sur **Facebook**, coller les valeurs d’ID de l’application et le Secret de l’application que vous avez obtenu précédemment, éventuellement permettre à toutes les étendues nécessaires à votre application, puis cliquez sur **OK**.

    ![][0]

    Par défaut, le Service de l’application fournit une authentification mais ne restreint pas les autorisations d’accès au contenu de votre site et API. Vous devez autoriser les utilisateurs dans votre code d’application.

3. (Facultatif) Pour restreindre l’accès à votre site pour que les utilisateurs authentifiés par Facebook, définir **l’Action à effectuer lorsque la demande n’est pas authentifié** à **Facebook**. Cela nécessite que toutes les demandes authentifiée, et toutes les demandes non authentifiées sont redirigées à Facebook pour l’authentification.

4. Lorsque vous avez terminé de configurer l’authentification, cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Facebook pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu associé

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Développeurs de Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure portal]: https://portal.azure.com/
