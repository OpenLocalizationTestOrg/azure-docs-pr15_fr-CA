<properties
    pageTitle="Comment faire pour configurer l’authentification de Google pour votre application de Services d’application"
    description="Découvrez comment configurer l’authentification de Google pour votre application de Services d’application."
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

# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Comment faire pour configurer votre application de Service de l’application pour utiliser la connexion de Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique vous indique comment configurer le Service d’application Azure pour utiliser Google comme fournisseur d’authentification.

Pour terminer la procédure décrite dans cette rubrique, vous devez avoir un compte Google qui a une adresse vérifié. Pour créer un nouveau compte Google, accédez à [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Enregistrer votre application avec Google

1. Ouvrez une session sur le [portail Azure]et accédez à votre application. Copiez votre **URL**, que vous utilisez ultérieurement pour configurer votre application de Google.

2. Accédez au site Web [API de Google](http://go.microsoft.com/fwlink/p/?LinkId=268303) , connectez-vous avec vos informations d’identification de compte Google, cliquez sur **Créer un projet**, fournir un **nom de projet**, puis cliquez sur **créer**.

3. **API Social** cliquez sur **Des API Google +** , puis sur **Activer**.

4. De navigation de gauche, les **informations d’identification** > **OAuth consentement écran**, sélectionnez votre **adresse de messagerie**, entrez un **Nom de produit**, puis cliquez sur **Enregistrer**.

5. Dans l’onglet **identification** , cliquez sur **informations d’identification de créer** > **ID de client OAuth**, puis sélectionnez **application Web**.

6. Collez l' application Service **URL** que vous avez copié précédemment dans **Autorisé les origines de JavaScript**, puis collez votre URI de redirection dans **l’URI de redirection autorisé**. La redirection URI correspond à l’URL de votre application ajoutée avec le chemin d’accès, _/.auth/login/google/callback_. Par exemple, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Assurez-vous que vous utilisez le schéma HTTPS. Puis cliquez sur **créer**.

7. Sur l’écran suivant, prenez note des valeurs de l’ID de client et le secret de client.


    > [AZURE.IMPORTANT]
    Le secret de client est une information d’identification de sécurité important. Ne pas partager ce secret avec n’importe qui ou distribuer dans une application cliente.


## <a name="secrets"> </a>Informations de Google d’ajouter à votre application

8. Dans le [portail Azure], accédez à votre application. Cliquez sur **paramètres**, puis **l’authentification / autorisation**.

9. Si l’authentification de la fonctionnalité d’autorisation n’est pas activée, et activez le commutateur **on**.

10. Cliquez sur **Google**. Coller les valeurs d’ID de l’application et le Secret de l’application que vous avez obtenu précédemment et éventuellement activer des étendues de que votre application a besoin. Puis cliquez sur **OK**.

    ![][1]

    Par défaut, le Service de l’application fournit une authentification mais ne restreint pas les autorisations d’accès au contenu de votre site et API. Vous devez autoriser les utilisateurs dans votre code d’application.

17. (Facultatif) Pour restreindre l’accès à votre site pour que les utilisateurs authentifiés par Google, définir **l’Action à prendre lors de la demande n’est pas authentifié** **Google**. Cela nécessite que toutes les demandes authentifiée, et toutes les demandes non authentifiées sont redirigées vers Google pour l’authentification.

12. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Google pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu associé

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure portal]: https://portal.azure.com/

