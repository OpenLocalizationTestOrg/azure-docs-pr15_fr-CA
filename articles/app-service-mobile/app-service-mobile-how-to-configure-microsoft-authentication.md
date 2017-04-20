<properties
    pageTitle="Comment faire pour configurer l’authentification de Microsoft Account pour votre application de Services d’application"
    description="Découvrez comment configurer l’authentification de Microsoft Account pour votre application de Services d’application."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Comment faire pour configurer votre application de Service de l’application pour utiliser la connexion de Microsoft Account

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique vous indique comment configurer le Service d’application Azure pour utiliser Microsoft Account comme fournisseur d’authentification. 

## <a name="register-microsoft-account"> </a>Inscrire votre application avec un compte Microsoft

1. Ouvrez une session sur le [portail Azure]et accédez à votre application. Copiez votre **URL**, plus tard vous permet de configurer votre application avec Account de Microsoft.

2. Accédez à la page [My Applications] dans le composant Microsoft Account Developer Center et ouvrez une session avec votre compte Microsoft, si nécessaire.

3. Cliquez sur **Ajouter une application**, tapez un nom d’application, puis cliquez sur **créer une application**.

4. Prenez note de l' **ID de l’Application**, car vous en aurez besoin ultérieurement. 

5. Sous « Plates-formes », cliquez sur **Ajouter une plate-forme** , sélectionnez « Web ».

6. Sous « Rediriger URI » fournir le point de terminaison de votre application, puis cliquez sur **Enregistrer**. 
 
    >[AZURE.NOTE]Votre URI de redirection est l’URL de votre application ajoutée avec le chemin d’accès, _/.auth/login/microsoftaccount/callback_. Par exemple, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
    >Assurez-vous que vous utilisez le schéma HTTPS.

7. Sous « Secrets d’Application », cliquez sur **Générer un nouveau mot de passe**. Notez la valeur qui s’affiche. Une fois que vous quittez la page, il affichera pas à nouveau.


    > [AZURE.IMPORTANT] Le mot de passe est une information d’identification de sécurité important. Ne pas partager le mot de passe avec n’importe qui ou distribuer dans une application cliente.

## <a name="secrets"> </a>Informations d’ajouter un compte Microsoft à votre application de Service de l’application

1. Précédent dans le [portail Azure], accédez à votre application, cliquez sur **paramètres** > **l’authentification / autorisation**.

2. Si l’authentification / la fonctionnalité d’autorisation n’est pas activée, sa mise **sous**tension.

3. Cliquez sur le **Compte de Microsoft**. Coller les valeurs d’ID de l’Application et le mot de passe que vous avez obtenu précédemment et éventuellement activer des étendues de que votre application a besoin. Puis cliquez sur **OK**.

    ![][1]

    Par défaut, le Service de l’application fournit une authentification mais ne restreint pas les autorisations d’accès au contenu de votre site et API. Vous devez autoriser les utilisateurs dans votre code d’application.

4. (Facultatif) Pour restreindre l’accès à votre site pour que les utilisateurs authentifiés par compte Microsoft, définir **l’Action à entreprendre lors de la demande n’est pas authentifié** au **Compte Microsoft**. Cela nécessite que toutes les demandes authentifiée, et toutes les demandes non authentifiées sont redirigées vers compte Microsoft pour l’authentification.

5. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Microsoft Account pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu associé

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portal]: https://portal.azure.com/
