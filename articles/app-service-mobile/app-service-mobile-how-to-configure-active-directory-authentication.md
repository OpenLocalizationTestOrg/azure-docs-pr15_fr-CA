<properties
    pageTitle="Comment faire pour configurer l’authentification pour votre application de Services d’application Azure Active Directory"
    description="Découvrez comment configurer l’authentification pour votre application de Services d’application Azure Active Directory."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
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

# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Comment faire pour configurer votre application de Service de l’application pour utiliser des connexion Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique vous indique comment configurer les Services d’application Azure pour utiliser Azure Active Directory en tant que fournisseur d’authentification.

## <a name="express"> </a>Configurer Azure Active Directory en utilisant les paramètres express

13. Dans le [portail Azure], accédez à votre application. Cliquez sur **paramètres**, puis sur **Authentification/autorisation**.

14. Si l’authentification de la fonctionnalité d’autorisation n’est pas activée, et activez le commutateur **on**.

15. Cliquez sur **Azure Active Directory**, puis cliquez sur **Express** sous le **Mode de gestion**.

16. Cliquez sur **OK** pour enregistrer l’application Azure Active Directory. Cela créera une nouvelle inscription. Si vous souhaitez choisir une inscription existante à la place, cliquez sur **Sélectionner une application existante** et recherchez le nom d’un enregistrement créé précédemment au sein de vos clients.
Cliquez sur l’enregistrement pour le sélectionner, puis cliquez sur **OK**. Cliquez ensuite sur **OK** sur la lame de paramètres Azure Active Directory.

    ![][0]

    Par défaut, le Service de l’application fournit une authentification mais ne restreint pas les autorisations d’accès au contenu de votre site et API. Vous devez autoriser les utilisateurs dans votre code d’application.

17. (Facultatif) Pour restreindre l’accès à votre site pour que les utilisateurs authentifiés par Azure Active Directory, définissez **Action à entreprendre lors de la demande n’est pas authentifié** à **l’aide d’Azure Active Directory**. Cela nécessite que toutes les demandes authentifiée, et toutes les demandes non authentifiées sont redirigées vers Azure Active Directory pour l’authentification.

17. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l’authentification dans votre application.

## <a name="advanced"> </a>(Autre méthode) configurer manuellement Azure Active Directory avec des paramètres avancés
Vous pouvez également choisir de fournir des paramètres de configuration manuellement. Il s’agit de la meilleure solution si le locataire DAS que vous souhaitez utiliser est différent de la cliente avec laquelle vous vous connectez dans Azure. Pour terminer la configuration, vous devez d’abord créer un enregistrement dans Azure Active Directory, et vous devez fournir des informations d’inscription au Service de l’application.

### <a name="register"> </a>Inscrire votre application dans Azure Active Directory

1. Ouvrez une session sur le [portail Azure]et accédez à votre application. Copiez l' **URL**. Vous l’utiliserez pour configurer votre application Azure Active Directory.

3. Connectez-vous à l' [Azure portal classique] et naviguez jusqu’au **Répertoire actif**.

    ![][2]

4. Sélectionnez votre répertoire, puis sélectionnez l’onglet **Applications** , dans la partie supérieure. Cliquez sur **Ajouter** en bas pour créer une nouvelle inscription de l’application.

5. Cliquez sur **Ajouter une application à mon entreprise se développe**.

6. Dans l’Assistant Ajout de l’Application, entrez un **nom** pour votre application et cliquez sur le type de **l’API de Web Application ou Web** . Cliquez ensuite sur pour continuer.

7. Dans la zone **URL de la session** , collez l’URL d’application que vous avez copié précédemment. Entrez cette même URL dans la zone **URI d’ID App** . Cliquez ensuite sur pour continuer.

8. Une fois que l’application a été ajoutée, cliquez sur l’onglet **configurer** . Modifier l' **URL de réponse** sous **l’ouverture de session unique** à l’URL de votre application ajoutée avec le chemin d’accès, _/.auth/login/aad/callback_. Par exemple, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Assurez-vous que vous utilisez le schéma HTTPS.

    ![][3]

9. Cliquez sur **Enregistrer**. Copiez ensuite l' **ID Client** pour l’application. Vous allez configurer votre application pour l’utiliser ultérieurement.

10. Dans la barre de commande, cliquez sur **Affichage de points de terminaison**, puis copiez l’URL du **Document des métadonnées de fédération** et télécharger ce document ou y accéder dans un navigateur.

11. Dans l’élément **EntityDescriptor** de la racine, il doit être un attribut **entityID** du formulaire `https://sts.windows.net/` suivi d’un GUID spécifique pour vos clients (appelé un « ID client »). Copiez cette valeur - il servira de votre **URL d’émetteur**. Vous allez configurer votre application pour l’utiliser ultérieurement.

### <a name="secrets"> </a>Informations Azure ajouter Active Directory à votre application

13. Dans le [portail Azure], accédez à votre application. Cliquez sur **paramètres**, puis sur **Authentification/autorisation**.

14. Si la fonctionnalité d’authentification / d’autorisation n’est pas activée, activez le commutateur **on**.

15. Cliquez sur **Azure Active Directory**, puis cliquez sur **Avancé** sous le **Mode de gestion**. Coller dans la valeur d’ID de Client et l’URL de l’émetteur que vous avez obtenu précédemment. Puis cliquez sur **OK**.

    ![][1]

    Par défaut, le Service de l’application fournit une authentification mais ne restreint pas les autorisations d’accès au contenu de votre site et API. Vous devez autoriser les utilisateurs dans votre code d’application.

17. (Facultatif) Pour restreindre l’accès à votre site pour que les utilisateurs authentifiés par Azure Active Directory, définissez **Action à entreprendre lors de la demande n’est pas authentifié** à **l’aide d’Azure Active Directory**. Cela nécessite que toutes les demandes authentifiée, et toutes les demandes non authentifiées sont redirigées vers Azure Active Directory pour l’authentification.

17. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l’authentification dans votre application.

## <a name="optional-configure-a-native-client-application"></a>(Facultatif) Configuration d’une application client natif

Azure Active Directory vous permet également d’enregistrer les clients natifs, qui offre un meilleur contrôle sur les autorisations de mappage. Vous besoin si vous souhaitez effectuer des connexions à l’aide d’une bibliothèque de la **Bibliothèque de l’authentification Active Directory**.

1. Accédez à **Active Directory** dans [Azure portal classique].

2. Sélectionnez votre répertoire, puis sélectionnez l’onglet **Applications** , dans la partie supérieure. Cliquez sur **Ajouter** en bas pour créer une nouvelle inscription de l’application.

3. Cliquez sur **Ajouter une application à mon entreprise se développe**.

4. Dans l’Assistant Ajout de l’Application, entrez un **nom** pour votre application, puis cliquez sur le type **d’Application Client d’origine** . Cliquez ensuite sur pour continuer.

5. Dans la zone **Rediriger les URI** , entrez le point de terminaison de votre site _/.auth/login/done_ , à l’aide du schéma HTTPS. Cette valeur doit être similaire à _https://contoso.azurewebsites.net/.auth/login/done_. Si vous créez une application Windows, utilisez plutôt le [package SID](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) comme URI.

6. Une fois l’application native a été ajoutée, cliquez sur l’onglet **configurer** . Rechercher l' **ID de Client** et prenez note de cette valeur.

7. Faites défiler la page jusqu'à la section **autorisations à d’autres applications** , puis cliquez sur **Ajouter application**.

8. Recherchez l’application web que vous avez enregistré précédemment et cliquez sur l’icône de signe plu. Cliquez ensuite sur la vérification, pour fermer la boîte de dialogue. Si l’application web ne peut pas être trouvé, accédez à son enregistrement et ajouter une nouvelle URL de réponse (par exemple, la version HTTP de l’URL de votre en cours), cliquez sur Enregistrer, puis répétez ces étapes, l’application doit s’afficher dans la liste.

9. Sur la nouvelle entrée que vous venez d’ajouter, ouvrez la liste déroulante **Autorisations de délégué** et sélectionnez **accès (appName)**. Puis cliquez sur **Enregistrer**.

Vous avez configuré une application native client qui peut accéder à votre application de Service de l’application.

## <a name="related-content"> </a>Contenu associé

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure portal classique]: https://manage.windowsazure.com/
[alternative method]:#advanced
