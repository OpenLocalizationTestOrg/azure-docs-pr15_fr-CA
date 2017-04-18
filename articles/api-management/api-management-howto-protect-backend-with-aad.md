<properties
    pageTitle="Comment protéger un back-end de l’API Web avec Azure Active Directory et de la gestion de l’API"
    description="Découvrez comment protéger un back-end de l’API Web avec Azure Active Directory et de la gestion de l’API." 
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

# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Comment protéger un back-end de l’API Web avec Azure Active Directory et de la gestion de l’API

La vidéo suivante montre comment générer un back-end Web API et protégez-le à l’aide du protocole OAuth 2.0 avec Azure Active Directory et de la gestion de l’API.  Cet article fournit une vue d’ensemble et les informations supplémentaires pour les étapes de la vidéo. Cette vidéo 24 minutes vous montre comment procéder pour :

-   Générer un back-end Web API et fixez-la avec DAS - commençant à 1 h 30
-   Importer l’API de gestion des API - commençant à 7:10
-   Configurer le portail des développeurs pour appeler l’API - commençant à 9:09
-   Configurer une application de bureau pour appeler l’API - commençant à 18:08
-   Configurer une stratégie de validation de JWT pour autoriser préalablement les demandes - commençant à 20:47

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## <a name="create-an-azure-ad-directory"></a>Créer un répertoire AD Azure

Pour sécuriser votre API Web sauvegardées à l’aide d’Azure Active Directory vous devez disposer d’un un locataire DAS. Dans cette vidéo, un client nommé **APIMDemo** est utilisé. Pour créer un locataire DAS, ouvrez une session dans le [Portail classique d’Azure](https://manage.windowsazure.com) , cliquez sur **Nouveau**->**Application Services**->**Active Directory**->**répertoire**->**Créer des personnalisées**. 

![Azure Active Directory][api-management-create-aad-menu]

Dans cet exemple, un répertoire nommé **APIMDemo** est créé avec un domaine par défaut nommé **DemoAPIM.onmicrosoft.com**. Ce répertoire est utilisé tout au long de la vidéo.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Créer un service Web API sécurisé par Azure Active Directory

Dans cette étape, un back-end de l’API Web est créé à l’aide de Visual Studio 2013. Cette étape de la vidéo démarre à 1 h 30. Pour créer des API Web de projet principal dans Visual Studio, cliquez sur **le fichier**->**Nouveau**->**projet**, puis choisissez **ASP.NET Web Application** à partir de la liste de modèles **Web** . Dans cette vidéo, le projet est nommé **APIMAADDemo**. Cliquez sur **OK** pour créer le projet. 

![Visual Studio][api-management-new-web-app]

Cliquez sur **Les API Web** de **Sélectionner un modèle** pour créer un projet Web API. Pour configurer l’authentification du répertoire Azure, cliquez sur **Modifier l’authentification**.

![Nouveau projet][api-management-new-project]

Cliquez sur **Comptes d’organisation**et spécifier le **domaine** de vos clients de DAS. Dans cet exemple, le domaine est **DemoAPIM.onmicrosoft.com**. Le domaine de votre annuaire peut être obtenu à partir de l’onglet **domaines** de votre répertoire.

![Domaines][api-management-aad-domains]

Configurez les paramètres souhaités dans la boîte de dialogue **Modifier l’authentification** , puis cliquez sur **OK**.

![Modifier l’authentification][api-management-change-authentication]

Lorsque vous cliquez sur **OK** , Visual Studio tente d’inscrire votre application avec votre annuaire AD Azure et vous pouvez être invité à vous connecter par Visual Studio. Ouvrez une session à l’aide d’un compte d’administrateur de votre annuaire.

![Se connecter à Visual Studio][api-management-sign-in-vidual-studio]

Pour configurer ce projet sous la forme d’une case à cocher de l’API de Web Azure pour **l’hôte dans le nuage** et puis cliquez sur **OK**.

![Nouveau projet][api-management-new-project-cloud]

Vous pouvez être invité à vous connecter à Azure, et vous pouvez ensuite configurer l’application Web.

![Configurer][api-management-configure-web-app]

Dans cet exemple, un nouveau **plan de Service d’application** nommé **APIMAADDemo** est spécifié.

Cliquez sur **OK** pour configurer l’application Web et de créer le projet.

## <a name="add-the-code-to-the-web-api-project"></a>Ajoutez le code au projet Web API

L’étape suivante de la vidéo ajoute le code au projet Web API. Cette étape démarre à 4:35.

Dans cet exemple, l’API de Web implémente un service de calculatrice de base à l’aide d’un modèle et un contrôleur. Pour ajouter le modèle de service, cliquez sur **modèles** dans **L’Explorateur de solutions** et choisissez **Ajouter**, **classe**. Nom de la classe `CalcInput` et cliquez sur **Ajouter**.

Ajoutez le code suivant `using` en haut de la `CalcInput.cs` fichier.

    using Newtonsoft.Json;

 Remplacez la classe générée par le code suivant.

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

Droit de **contrôleurs** dans **L’Explorateur de solutions** et choisissez **Ajouter**->**contrôleur**. Cliquez sur **Contrôleur de 2 API Web - vide** et cliquez sur **Ajouter**. Tapez **CalcController** pour le nom du contrôleur, puis cliquez sur **Ajouter**.

![Ajouter contrôleur][api-management-add-controller]

Ajoutez le code suivant `using` en haut de la `CalcController.cs` fichier.

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Remplacez la classe controller généré par le code suivant. Ce code implémente la `Add`, `Subtract`, `Multiply`, et `Divide` opérations de l’API de calculatrice de base.

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }
    }

Appuyez sur **F6** pour générer et vérifier la solution.

## <a name="publish-the-project-to-azure"></a>Publiez le projet vers Azure

Dans cette étape, le Visual Studio projet est publié sur Azure. Cette étape de la vidéo commence à 5:45.

Pour publier le projet sur Azure, droit sur le projet **APIMAADDemo** dans Visual Studio et choisissez **Publier**. Conserver les paramètres par défaut dans la boîte de dialogue **Publier le site Web** , puis cliquez sur **Publier**.

![Publication sur le Web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Accorder des autorisations à l’application de service de publicité Azure back-end

Une nouvelle application pour le service du serveur principal est créée dans votre répertoire d’Azure AD dans le cadre du processus de publication et configuration de votre projet Web API. Dans cette étape de la vidéo, en commençant à 6:13, les autorisations sont accordées pour le back-end de l’API Web.

![Application][api-management-aad-backend-app]

Cliquez sur le nom de l’application pour configurer les autorisations requises. Accédez à l’onglet **configurer** et faites défiler jusqu'à la section **autorisations à d’autres applications** . Cliquez sur les **Autorisations de l’Application** vers le bas en regard de **Windows Azure** , **Active Directory**, la case à cocher pour **lire les données de répertoire**et cliquez sur **Enregistrer**.

![Ajouter des autorisations][api-management-aad-add-permissions]

>[AZURE.NOTE] Si **Windows Azure** , **Active Directory** n’est pas répertorié sous autorisations à d’autres applications, cliquez sur **Ajouter l’application** et l’ajouter dans la liste.

Prenez note de l' **Identificateur URI d’application** pour une utilisation dans une étape ultérieure lorsqu’une application Azure AD est configurée pour le portail de développement de gestion de l’API.

![Id de l’application URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importer de l’API Web dans Gestion de l’API

API est configurés à partir du portail de publisher API, qui est accessible via le portail classique d’Azure. Pour atteindre le portail de publisher, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API. Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel de [Gérer votre première API][] .

![Portail de Publisher][api-management-management-console]

Les opérations peuvent être [ajoutées manuellement aux API](api-management-howto-add-operations.md), ou ils peuvent être importés. Dans cette vidéo, les opérations sont importées au format Swagger commençant à 6:40.

Créez un fichier nommé `calcapi.json` avec le contenu suivant et enregistrez le fichier sur votre ordinateur. Assurez-vous que le `host` points d’attribut pour la principale API Web. Dans cet exemple `"host": "apimaaddemo.azurewebsites.net"` est utilisé.

{« swagger » : « 2.0 », « info » : {« title » : « Calculatrice », « description » : « Arithmétique sur HTTP ! », « version » : « 1.0 »}, « hôte » : « apimaaddemo.azurewebsites.net », « chemin » de base : « / api », « régimes » : [« http »], « tracés » : {« / ajouter ? un = {a} & b = {b} » : {« get » : {« description » : « Répond à une somme de deux nombres. », « operationId » : « Ajouter deux entiers », « paramètres » : [{« nom » : « a », « in » : « requêtes », « description » : « premier opérande. Valeur par défaut est <code>51</code>. », « obligatoire » : true, « par défaut » : « 51 », « enum » : [« 51 »]}, {« nom » : « b », « in » : « requêtes », « description » : « Second opérande. Valeur par défaut est <code>49</code>. », « obligatoire » : true, « par défaut » : « 49 », « enum » : [« 49 »]}], « réponses » : {}}}, « / sub?a = {a} & b = {b} » : {« get » : {« description » : « Répond avec une différence entre deux nombres. », « operationId » : « Soustraire deux nombres entiers », « paramètres » : [{« nom » : « a », « in » : « requêtes », « description » : « premier opérande. Valeur par défaut est <code>100</code>. », « obligatoire » : true, « par défaut » : « 100 », « enum » : [« 100 »]}, {« nom » : « b », « in » : « requêtes », « description » : « Second opérande. Valeur par défaut est <code>50</code>. », « obligatoire » : true, « par défaut » : « 50 », « enum » : [« 50 »]}], « réponses » : {}}}, « / div?a = {a} & b = {b} » : {« get » : {« description » : « Répond avec un quotient de deux nombres. », « operationId » : « Division de deux entiers », « paramètres » : [{« nom » : « a », « in » : « requêtes », « description » : « premier opérande. Valeur par défaut est <code>100</code>. », « obligatoire » : true, « par défaut » : « 100 », « enum » : [« 100 »]}, {« nom » : « b », « in » : « requêtes », « description » : « Second opérande. Valeur par défaut est <code>20</code>. », « obligatoire » : true, « par défaut » : « 20 », « enum » : [« 20 »]}], « réponses » : {}}}, « / mul?a = {a} & b = {b} » : {« get » : {« description » : « Répond avec un produit de deux nombres. », « operationId » : « Multiplier deux nombres entiers », « paramètres » : [{« nom » : « a », « in » : « requêtes », « description » : « premier opérande. Valeur par défaut est <code>20</code>. », « obligatoire » : true, « par défaut » : « 20 », « enum » : [« 20 »]}, {« nom » : « b », « in » : « requêtes », « description » : « Second opérande. La valeur par défaut est <code>5</code>. », « obligatoire » : true, « par défaut » : « 5 », « enum » : [« 5 »]}], « réponses » : {}}}}}

Pour importer la API de la Calculatrice, cliquez sur **API** à partir du menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Importer les API**.

![Bouton Importer API][api-management-import-api]

Procédez comme suit pour configurer la calculatrice API.

1. Cliquez sur **fichier**, recherchez le `calculator.json` vous avez enregistré le fichier, puis cliquez sur le bouton radio **Swagger** .
2. Dans la zone de texte **suffixe d’URL d’API Web** , tapez **calc** .
3. Cliquez dans la zone **produits (facultatifs)** et cliquez sur **démarrage**.
4. Cliquez sur **Enregistrer** pour importer l’API.

![Ajouter la nouvelle API][api-management-import-new-api]

Une fois que l’API est importé, la page Résumé de l’API est affichée dans le portail de publisher.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Appel de l’API sans succès à partir du portail de développement

À ce stade, l’API a été importé dans l’API de gestion, mais ne peut pas encore être appelé avec succès à partir du portail de développement, car le service back-end est protégé par l’authentification Active Directory Azure. Cela est illustré dans la vidéo commençant à 7:40 à l’aide de la procédure suivante.

Cliquez sur le **portail des développeurs** à partir d’en haut à droite du portail publisher.

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur les **API** et la **Calculatrice** API.

![Portail des développeurs][api-management-dev-portal-apis]

Cliquez sur **tester**.

![Essaie][api-management-dev-portal-try-it]

Cliquez sur **Envoyer** et notez l’état de la réponse de **401 non autorisé**.

![Envoyer][api-management-dev-portal-send-401]

La demande n’est pas autorisée car la principale API est protégée par Azure Active Directory. Avant d’appeler avec succès à l’API du développeur portail doit être configuré pour autoriser les développeurs utilisant OAuth 2.0. Ce processus est décrit dans les sections suivantes.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Enregistrer le portail des développeurs comme une application DAS

La première étape de la configuration du portail développeur pour autoriser les développeurs utilisant OAuth 2.0 est d’enregistrer le portail des développeurs comme une application DAS. Cela est démontré commençant à 8:27 dans la vidéo.

Accédez au locataire AD Azure à partir de la première étape de cette vidéo, dans cet exemple, **APIMDemo** et accédez à l’onglet **Applications** .

![Nouvelle application][api-management-aad-new-application-devportal]

Cliquez sur le bouton **Ajouter** pour créer une nouvelle application Azure Active Directory, puis sélectionnez **Ajouter une application à mon entreprise se développe**.

![Nouvelle application][api-management-new-aad-application-menu]

Choisissez **l’application Web et/ou des API Web**, entrez un nom et cliquez sur la flèche suivant. Dans cet exemple, **APIMDeveloperPortal** est utilisé.

![Nouvelle application][api-management-aad-new-application-devportal-1]

Pour **l’ouverture de session d’URL** , entrez l’URL de votre service de gestion de l’API et ajoutez `/signin`. Dans cet exemple, **https://contoso5.portal.azure-api.net/signin **est utilisé.

Pour **App Id URL** Entrez l’URL de votre service de gestion de l’API et ajout de caractères uniques. Il peut s’agir de n’importe quel caractère de votre choix et dans cet exemple, **https://contoso5.portal.azure-api.net/dp** est utilisé. Lorsque les **Propriétés de l’application** sont configurés, cliquez sur la case à cocher pour créer l’application.

![Nouvelle application][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configurer un serveur de l’autorisation Gestion d’API, OAuth 2.0

L’étape suivante consiste à configurer un serveur d’autorisation de OAuth 2.0 dans Gestion de l’API. Cette étape est écrite dans la vidéo commençant à 9:43.

Cliquez sur **sécurité** dans le menu de gestion de l’API de gauche et cliquez sur **OAuth 2.0**, puis sur **l’autorisation d’ajouter** le serveur.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server]

Dans les champs **nom** et **Description** , entrez un nom et une description facultative. Ces champs sont utilisés pour identifier le serveur d’autorisation OAuth 2.0 au sein de l’instance de service de gestion de l’API. Dans cet exemple, **démonstration de serveur d’autorisation** est utilisé. Plus tard lorsque vous spécifiez un serveur OAuth 2.0 à utiliser pour l’authentification d’une API, vous sélectionnerez ce nom.

Pour l' **URL de la page d’inscription Client** , entrez une valeur d’espace réservé tel que `http://localhost`.  L' **URL de la page d’inscription Client** pointe vers la page que les utilisateurs peuvent utiliser pour créer et configurer leurs propres comptes pour les fournisseurs de OAuth 2.0 qui prend en charge la gestion de comptes d’utilisateur. Dans cet exemple les utilisateurs créer et de configurer leurs propres comptes d’espace réservé est utilisé.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-1]

Ensuite, spécifiez les **URL de point de terminaison de l’autorisation** et le **jeton d’URL de point de terminaison**.

![Serveur d’autorisation][api-management-add-authorization-server-1a]

Ces valeurs peuvent être récupérées à partir de la page **d’Application de points de terminaison** de l’application DAS que vous avez créé pour le portail des développeurs. Pour accéder aux points de terminaison, accédez à l’onglet **configuration** de l’application DAS et cliquez sur les **points de terminaison de vue**.

![Application][api-management-aad-devportal-application]

![Affichage de points de terminaison][api-management-aad-view-endpoints]

**point de terminaison OAuth 2.0 l’autorisation** de copier et coller dans la zone de texte **URL de point de terminaison de l’autorisation** .

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-2]

Copier **point de terminaison jeton OAuth 2.0** et collez-le dans la zone de texte **URL de point de terminaison de jeton** .

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-2a]

En plus de la coller dans le point de terminaison de jeton, ajoutez un paramètre de corps supplémentaire nommé **ressource** et pour l’utilisation de la valeur **URI Id d’application** à partir de l’application DAS pour le service de serveur principal qui a été créé lorsque le projet Visual Studio a été publié.

![Id de l’application URI][api-management-aad-sso-uri]

Ensuite, spécifiez les informations d’identification du client. Ce sont les informations d’identification de la ressource que vous souhaitez accéder à, dans ce cas, le service back-end.

![Informations d’identification du client][api-management-client-credentials]

Pour obtenir l' **Id Client**, accédez à l’onglet **configuration** de l’application DAS pour le service back-end et copiez l' **Id de Client**.

Pour obtenir la **Secret Client** , cliquez sur **Sélectionnez durée** liste déroulante dans la section **touches** et spécifiez un intervalle. Dans cet exemple, 1 an est utilisé.

![ID de client][api-management-aad-client-id]

Cliquez sur **Enregistrer** pour enregistrer la configuration et afficher la clé. 

>[AZURE.IMPORTANT] Notez cette clé. Une fois que vous fermez la fenêtre de configuration Azure Active Directory, la clé ne s’affiche plus.

Copiez la clé dans le Presse-papiers, basculer vers le portail de l’éditeur, collez la clé dans la zone de texte **Client Secret** et cliquez sur **Enregistrer**.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-3]

Immédiatement après les informations d’identification du client est un code d’octroi. Copiez le code d’autorisation et revenez à votre application de portail développeur AD Azure configurer la page et coller l’autorisation Autoriser dans le champ **URL de la réponse** et cliquez de nouveau sur **Enregistrer** .

![URL de la réponse][api-management-aad-reply-url]

L’étape suivante consiste à configurer les autorisations pour le portail de développement des applications de DAS. Cliquez sur **Autorisations d’Application** et la case à cocher pour **lire les données de répertoire**. Cliquez sur **Enregistrer** pour enregistrer la modification, puis cliquez sur **Ajouter application**.

![Ajouter des autorisations][api-management-add-devportal-permissions]

Cliquez sur l’icône de recherche, tapez **APIM** en le commençant par boîte, sélectionnez **APIMAADDemo**et cliquez sur la coche pour enregistrer.

![Ajouter des autorisations][api-management-aad-add-app-permissions]

Cliquez sur **Déléguer les autorisations** pour **APIMAADDemo** et cochez la case pour **APIMAADDemo d’accès**et cliquez sur **Enregistrer**. Cela permet au développeur d’application de portail pour accéder au service back-end.

![Ajouter des autorisations][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Activer l’autorisation de l’utilisateur OAuth 2.0 pour l’API de la calculatrice

Maintenant que le serveur OAuth 2.0 est configuré, vous pouvez le spécifier dans les paramètres de sécurité de votre API. Cette étape est écrite dans la vidéo à partir de 14:30.

Cliquez sur **API** dans le menu de gauche, puis cliquez sur **Calculatrice** pour afficher et configurer ses paramètres.

![Calculatrice API][api-management-calc-api]

Accédez à l’onglet **sécurité** , activez la case à cocher **OAuth 2.0** , sélectionnez le serveur de l’autorisation souhaitée dans la liste déroulante **serveur d’autorisation** , cliquez sur **Enregistrer**.

![Calculatrice API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Appeler l’API calculatrice à partir du portail de développement

Maintenant que l’autorisation OAuth 2.0 est configurée sur l’interface API, ses opérations peuvent être appelées avec succès à partir du centre de développement. Cette étape est écrite dans la vidéo à partir de 15:00.

Revenir à l’opération **ajouter deux entiers** du service calculatrice dans le portail destiné aux développeurs, cliquez sur **essayer**. Remarque le nouvel élément dans la section **autorisation** correspondant au serveur d’autorisation que vous venez d’ajouter.

![Calculatrice API][api-management-calc-authorization-server]

Sélectionnez un **code d’autorisation** dans la liste déroulante de l’autorisation et entrez les informations d’identification du compte à utiliser. Si vous êtes déjà connecté avec le compte, vous ne pouvez pas invité.

![Calculatrice API][api-management-devportal-authorization-code]

Cliquez sur **Envoyer** et notez l' **état de la réponse** de **200 OK** et les résultats de l’opération dans le contenu de la réponse.

![Calculatrice API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configurer une application de bureau pour appeler l’API

La procédure suivante dans la vidéo commence à 16:30 et configure une application de bureau simple d’appeler l’API. La première étape consiste à enregistrer l’application de bureau Azure AD et de lui donner accès au répertoire et au service back-end. À 18:25 est une démonstration de l’application de bureau appelant une opération sur l’API de la calculatrice.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurer une stratégie de validation de JWT pour autoriser les demandes avant

La procédure finale de la vidéo commence à 20:48 et vous montre comment utiliser la stratégie de [Validation de JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) au préalable autoriser les demandes en validant les jetons d’accès de chaque demande entrante. Si la demande n’est pas validée par la stratégie de validation de JWT, la demande est bloquée par la gestion de l’API et n’est pas transmise sur le serveur principal.

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

Pour une autre démonstration de la configuration et l’utilisation de cette stratégie, reportez-vous à la section [177 épisode de couvrir Cloud : plus les fonctions de gestion API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) et avance rapide à 13:50. Avance rapide à 15:00 pour voir les stratégies configurées dans l’éditeur de stratégie, puis à 18:50 pour une démonstration de l’appel d’une opération à partir du portail développeur avec et sans le jeton d’autorisation requis.

## <a name="next-steps"></a>Étapes suivantes
-   Découvrez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur la gestion de l’API.
-   Pour d’autres méthodes sécuriser votre service back-end, consultez [authentification de certificat Mutual](api-management-howto-mutual-certificates.md) et [se connecter via VPN ou ExpressRoute](api-management-howto-setup-vpn.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Gérer votre première API]: api-management-get-started.md
