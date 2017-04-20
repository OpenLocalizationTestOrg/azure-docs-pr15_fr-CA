<properties 
    pageTitle="Comment autoriser des comptes de développeur à l’aide de OAuth 2.0 dans Azure API de gestion" 
    description="Découvrez comment autoriser des utilisateurs à l’aide de OAuth 2.0 dans Gestion de l’API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Comment autoriser des comptes de développeur à l’aide de OAuth 2.0 dans Azure API de gestion

De nombreuses API prend en charge [OAuth 2.0](http://oauth.net/2/) pour sécuriser l’API et de s’assurer que seuls les utilisateurs autorisés ont accès, et ils peuvent uniquement accéder des ressources auxquelles ils ont droit. Pour utiliser Console de développeur de gestion Azure API interactive avec ces API, le service vous permet de configurer l’instance de service pour travailler avec votre OAuth 2.0 API activé.

## <a name="prerequisites"> </a>Conditions préalables

Ce guide vous montre comment configurer votre instance de service de gestion de l’API pour utiliser l’autorisation de OAuth 2.0 pour les comptes du développeur, mais ne contient pas d’explication configurer un fournisseur OAuth 2.0. La configuration pour chaque fournisseur OAuth 2.0 est différente, bien que les étapes sont similaires et les éléments d’informations utilisées pour configurer OAuth 2.0 dans votre instance de service de gestion de l’API sont les mêmes. Cette rubrique présente des exemples d’utilisation d’Azure Active Directory comme un fournisseur OAuth 2.0.

>[AZURE.NOTE] Pour plus d’informations sur la configuration 2.0 OAuth à l’aide d’Azure Active Directory, consultez l’exemple de [WebApp-GraphAPI-DotNet][] .

## <a name="step1"> </a>Configurer un serveur d’autorisation de OAuth 2.0 dans Gestion de l’API

Pour commencer, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API. Cela vous amène sur le portail d’éditeur de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>[AZURE.NOTE] Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Cliquez sur **sécurité** dans le menu de **Gestion de l’API** de gauche et cliquez sur **OAuth 2.0**, puis cliquez sur **Ajouter un serveur d’autorisation**.

![OAuth 2.0][api-management-oauth2]

Après avoir cliqué sur **Ajouter un serveur d’autorisation**, le nouveau formulaire de serveur d’autorisation s’affiche.

![Nouveau serveur][api-management-oauth2-server-1]

Dans les champs **nom** et **Description** , entrez un nom et une description facultative. 

>[AZURE.NOTE] Ces champs sont utilisés pour identifier le serveur d’autorisation OAuth 2.0 dans l’instance de service de gestion de l’API en cours et leurs valeurs ne proviennent pas du serveur OAuth 2.0.

Entrez l' **URL de la page d’inscription Client**. Cette page est l’endroit où les utilisateurs peuvent créer et gérer leurs comptes et varie selon le fournisseur OAuth 2.0 utilisé. L' **URL de la page d’inscription Client** pointe vers la page que les utilisateurs peuvent utiliser pour créer et configurer leurs propres comptes pour les fournisseurs de OAuth 2.0 qui prend en charge la gestion de comptes d’utilisateur. Certaines organisations ne configurer ou utiliser cette fonctionnalité même si le fournisseur OAuth 2.0 prend en charge. Si votre fournisseur OAuth 2.0 n’a pas de gestion des utilisateurs de comptes configurés, entrez une espace réservé URL ici l’URL de votre société, ou une URL telle que `https://placeholder.contoso.com`.

La section suivante de l’écran contient les paramètres de **code d’autorisation accorder des types** **d’URL de point de terminaison de l’autorisation**et **méthode de la demande d’autorisation** .

![Nouveau serveur][api-management-oauth2-server-2]

Permet de spécifier **code d’autorisation accorder des types** en vérifiant les types souhaités. **Code d’autorisation** est spécifié par défaut.

Entrez l' **URL du point de terminaison d’autorisation**. Pour Azure Active Directory, cette URL est similaire à l’URL suivante, où `<client_id>` est remplacée par l’id de client qui identifie votre application sur le serveur OAuth 2.0.

    https://login.windows.net/<client_id>/oauth2/authorize

La **méthode de la demande d’autorisation** indique comment la demande d’autorisation est envoyée au serveur OAuth 2.0. **Obtenir** est sélectionné par défaut.

La section suivante est où les **URL de point de terminaison de jeton**, **méthodes d’authentification de Client**, **jeton d’accès envoyer de méthode**et **étendue par défaut** sont spécifiés.

![Nouveau serveur][api-management-oauth2-server-3]

Pour un serveur Azure Active Directory OAuth 2.0, le **jeton d’URL de point de terminaison** auront le format suivant, où `<APPID>` le format de `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

Le paramètre par défaut pour les **méthodes d’authentification de Client** est de **base**et **envoi de méthode jeton d’accès** est **l’en-tête d’autorisation**. Ces valeurs sont configurés sur cette section du formulaire, ainsi que l' **étendue par défaut**.

La section **informations d’identification Client** contient **l’ID Client** et le **secret Client**, qui sont obtenus au cours du processus de création et la configuration de votre serveur OAuth 2.0. Une fois **l’ID de Client** et le **secret du Client** sont spécifiés, la **redirect_uri** pour le **code d’autorisation** est généré. Cet URI est utilisé pour configurer l’URL de réponse dans votre configuration de serveur OAuth 2.0.

![Nouveau serveur][api-management-oauth2-server-4]

Si le **code d’autorisation accorder des types** valeur **ressource mot de passe de propriétaire**, la section **d’identification de mot de passe de propriétaire de ressources** est utilisée pour spécifier les informations d’identification ; Sinon, vous pouvez laisser vide.

![Nouveau serveur][api-management-oauth2-server-5]

Une fois que le formulaire est terminé, cliquez sur **Enregistrer** pour enregistrer la configuration du serveur API gestion OAuth 2.0 d’autorisation. Une fois la configuration du serveur est enregistrée, vous pouvez configurer des API pour utiliser cette configuration, comme indiqué dans la section suivante.

## <a name="step2"> </a>Configurer une API pour utiliser l’autorisation d’utilisateur OAuth 2.0

Cliquez sur **API** à partir du menu de **Gestion de l’API** sur la gauche et cliquez sur le nom de l’API de votre choix, cliquez sur **sécurité**, puis cochez la case pour **OAuth 2.0**.

![Autorisation de l’utilisateur][api-management-user-authorization]

Sélectionnez le **serveur d’autorisation** de votre choix dans la liste déroulante, puis cliquez sur **Enregistrer**.

![Autorisation de l’utilisateur][api-management-user-authorization-save]

## <a name="step3"> </a>Tester l’autorisation de l’utilisateur OAuth 2.0 dans le portail des développeurs

Une fois que vous avez configuré votre serveur d’autorisation OAuth 2.0 et configuré votre API pour utiliser ce serveur, vous pouvez le tester en ouvrant le portail des développeurs et appel d’une API.  Dans le menu supérieur de droit, cliquez sur **portail destiné aux développeurs** .

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur et sélectionnez des **API d’écho**.

![API d’écho][api-management-apis-echo-api]

>[AZURE.NOTE] Si vous avez une seule API configurée ou à visible à votre compte, puis en cliquant sur API prend directement aux opérations de cette API.

Sélectionnez l’opération **Obtenir les ressources** et cliquez sur **Ouvrir la Console**, puis sélectionnez le **code d’autorisation** dans la liste déroulante.

![Ouvrir la console][api-management-open-console]

Lorsque le **code d’autorisation** est sélectionné, une fenêtre contextuelle s’affiche avec le formulaire de connexion du fournisseur OAuth 2.0. Dans cet exemple, l’écran d’ouverture de session est fourni par Azure Active Directory.

>[AZURE.NOTE] Si vous avez des fenêtres pop-up désactivé vous serez invité à les activer par le navigateur. Une fois que vous les activez, sélectionnez un **code d’autorisation** à nouveau et le formulaire d’ouverture de session seront affiche.

![Connexion][api-management-oauth2-signin]

Une fois que vous êtes connecté, les **en-têtes de demande** sont remplis avec une `Authorization : Bearer` en-tête qui autorise la demande.

![Jeton d’en-tête de demande][api-management-request-header-token]

À ce stade, vous pouvez configurer les valeurs souhaitées pour les paramètres restants et soumettre la demande. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de OAuth 2.0 et la gestion de l’API, voir la vidéo suivante et qui accompagne [l’article](api-management-howto-protect-backend-with-aad.md).

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Mise en route de la gestion des API Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

