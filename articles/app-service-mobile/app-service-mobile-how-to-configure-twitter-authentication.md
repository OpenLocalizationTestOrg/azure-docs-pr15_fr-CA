<properties
    pageTitle="Comment faire pour configurer l’authentification pour votre application de Services d’application Twitter"
    description="Découvrez comment configurer l’authentification pour votre application de Services d’application Twitter."
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

# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Comment faire pour configurer votre application de Service de l’application pour utiliser la connexion de Twitter

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique vous indique comment configurer le Service d’application Azure pour utiliser Twitter comme fournisseur d’authentification.

Pour terminer la procédure décrite dans cette rubrique, vous devez disposer d’un compte Twitter qui possède un numéro de téléphone et adresse e-mail vérifié. Pour créer un nouveau compte Twitter, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Enregistrer votre application avec Twitter


1. Ouvrez une session sur le [portail Azure]et accédez à votre application. Copiez l' **URL**. Vous l’utiliserez pour configurer votre application Twitter.

2. Accédez au site Web de [Développeurs de Twitter] , connectez-vous avec vos informations d’identification de compte Twitter et cliquez sur **Créer une nouvelle application**.

3. Tapez le **nom** et une **Description** pour votre nouvelle application. **URL** de le coller dans votre application pour la valeur du **site Web** . Ensuite, pour l' **URL de rappel**, collez l' **URL de rappel** que vous avez copié précédemment. Il s’agit de la passerelle d’application Mobile ajoutée avec le chemin d’accès, _/.auth/login/twitter/callback_. Par exemple, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Assurez-vous que vous utilisez le schéma HTTPS.

3.  Dans le bas de la page, lire et accepter les termes du contrat. Cliquez ensuite sur **créer votre application Twitter**. Cela permet d’enregistrer l’application s’affiche les détails de l’application.

4. Cliquez sur l’onglet **paramètres** , **Autoriser cette application à utiliser pour vous connecter avec Twitter**, puis cliquez sur **Paramètres de mise à jour**.

5. Sélectionnez l’onglet **clés et jetons d’accès** . Prenez note des valeurs de **Clé de consommateur (clé de l’API)** et **le secret de consommateur (API Secret)**.

    > [AZURE.NOTE] Le secret de consommateur est une information d’identification de sécurité important. Ne pas partager ce secret avec n’importe qui ou distribuer avec votre application.


## <a name="secrets"> </a>Informations d’Ajouter Twitter à votre application

13. Dans le [portail Azure], accédez à votre application. Cliquez sur **paramètres**, puis **l’authentification / autorisation**.

14. Si l’authentification de la fonctionnalité d’autorisation n’est pas activée, et activez le commutateur **on**.

15. Cliquez sur **Twitter**. Coller les valeurs que vous avez obtenu précédemment dans l’ID de l’application et le Secret de l’application. Puis cliquez sur **OK**.

    ![][1]

    Par défaut, le Service de l’application fournit une authentification mais ne restreint pas les autorisations d’accès au contenu de votre site et API. Vous devez autoriser les utilisateurs dans votre code d’application.

17. (Facultatif) Pour restreindre l’accès à votre site pour que les utilisateurs authentifiés par Twitter, définir **l’Action à effectuer lorsque la demande n’est pas authentifié** à **Twitter**. Cela nécessite que toutes les demandes authentifiée, et toutes les demandes non authentifiées sont redirigées vers Twitter pour l’authentification.

17. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Twitter pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu associé

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Développeurs de Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
