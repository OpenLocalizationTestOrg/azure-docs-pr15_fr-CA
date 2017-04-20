<properties
    pageTitle="Authentification principale du service pour les applications d’API dans le Service d’application Azure | Microsoft Azure"
    description="Apprenez à protéger une application API dans le Service d’application Azure pour les scénarios de service à service."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016" 
    ms.author="rachelap"/>

# <a name="service-principal-authentication-for-api-apps-in-azure-app-service"></a>Authentification principale du service pour les applications d’API dans le Service d’application Azure

## <a name="overview"></a>Vue d’ensemble

Cet article explique comment faire pour utiliser l’authentification du Service d’application pour l’accès *interne* aux applications d’API. Un scénario interne est où vous disposez d’une application API que vous souhaitez être consommable uniquement par votre propre code d’application. La méthode recommandée pour implémenter ce scénario dans le Service d’application consiste à utiliser des annonces Azure pour protéger l’application API appelée. Vous appelez l’application API protégée avec un jeton de support que vous obtenez à partir d’AD Azure grâce à l’identité de l’application d’informations d’identification (service principal). Pour des alternatives à l’utilisation d’AD Azure, reportez-vous à la section **authentification de Service au service** de la [vue d’ensemble de l’authentification de Service d’application Azure](../app-service/app-service-authentication-overview.md#service-to-service-authentication).

Dans cet article, vous apprendrez à :

* Comment utiliser Azure Active Directory (AD Azure) pour empêcher l’accès non authentifié une application API.
* L’utilisation d’une application API protégée à partir d’une application d’API, les applications web application mobile avec informations d’identification de publicité Azure service principal (identité d’application). Pour plus d’informations sur l’utilisation d’une application de logique, reportez-vous [à l’aide de votre API personnalisé hébergé sur le Service d’application avec des applications de logique](../app-service-logic/app-service-logic-custom-hosted-api.md).
* Comment s’assurer que l’application API protégée ne peut pas être appelée à partir d’un navigateur par les utilisateurs connectés.
* Comment faire pour vous assurer que l’application API protégée ne peut être appelée que par un spécifique Azure AD service principal.

L’article contient deux sections :

* La section [comment configurer l’authentification principal du service dans le Service d’application Azure](#authconfig) s’explique en général comment configurer l’authentification pour n’importe quelle application API et l’utilisation de l’application API protégée. Cette section s’applique également à toutes les infrastructures sont pris en charge par le Service d’application, y compris .NET, Node.js et Java.

* À partir de la section de [poursuivre les didacticiels .NET mise en route](#tutorialstart) , le didacticiel vous guide à travers la configuration d’un scénario « accès interne » pour un exemple d’application .NET en cours d’exécution dans le Service d’application. 

## <a id="authconfig"></a>Comment faire pour configurer l’authentification principal du service dans le Service d’application Azure

Cette section fournit des instructions générales qui s’appliquent à n’importe quelle application API. Pour connaître les étapes spécifiques à l’exemple d’application .NET de liste à faire, accédez à [poursuivre la série de didacticiels d’applications .NET à l’API](#tutorialstart).

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la lame de **paramètres** de l’application de l’API que vous souhaitez protéger, puis recherchez la section **fonctionnalités** et cliquez sur **l’authentification / autorisation**.

    ![Authentification/autorisation dans Azure portal](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Dans la **l’authentification / autorisation** lame, cliquez **sur**.

4. Dans la liste déroulante **Action à entreprendre lors de la demande n’est pas authentifié** , sélectionnez **se connecter avec Azure Active Directory** .

5. **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

    ![Lame d’authentification/autorisation dans Azure portal](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Configurer la lame **Azure les paramètres Active Directory** pour créer une nouvelle application de AD Azure ou utilisez une application Azure annonce existante si vous disposez déjà d’un objet que vous souhaitez utiliser.

    Scénarios internes impliquent généralement une application API appelant une application API. Vous pouvez utiliser distinct Azure applications AD pour chaque application API ou qu’une application de AD Azure.

    Pour obtenir des instructions détaillées sur cette blade, reportez-vous à la section [comment configurer votre application de Service de l’application pour utiliser des connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

7. Lorsque vous avez terminé avec la lame de configuration du fournisseur d’authentification, cliquez sur **OK**.

7. Dans la **l’authentification / autorisation** lame, cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/app-service-api-dotnet-service-principal-auth/authsave.png)

Dans ce cas, application Service autorise uniquement les demandes provenant d’appelants dans la configuration client d’Azure AD. Aucun code d’authentification ou d’autorisation n’est nécessaire dans l’API d’application protégé. Le jeton de support est passé à l’application de l’API avec couramment des revendications utilisées dans les en-têtes HTTP, et vous pouvez lire les informations dans le code permet de contrôler les demandes provenant d’un appelant particulier, tel qu’une entité de sécurité du service.

Cette fonctionnalité d’authentification fonctionne de la même manière pour toutes les langues prises en charge du service de l’application, y compris .NET, Node.js et Java. 

#### <a name="how-to-consume-the-protected-api-app"></a>L’utilisation de l’application API protégée

L’appelant doit fournir un jeton de support Azure annonce des appels API. Pour obtenir un jeton de support à l’aide des informations d’identification principal de service, l’appelant utilise la bibliothèque d’authentification Active Directory (ADAL pour [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)ou [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)). Pour obtenir un jeton, le code qui appelle ADAL fournit à ADAL les informations suivantes :

* Le nom de vos clients AD Azure.
* L’ID de client et le secret de client (clé app) de l’application Azure publicité associée à l’appelant.
* L’ID client de l’application Azure AD associée à l’application API protégée. (Si une application de AD Azure est utilisée, il est le même ID de client comme celui de l’appelant.)

Ces valeurs sont disponibles dans les pages d’annonce Azure d' [Azure portal classique](https://manage.windowsazure.com/).

Une fois que le jeton a été acquis, l’appelant inclut les requêtes HTTP dans l’en-tête d’autorisation.  Service de l’application valide le jeton et autorise les requêtes accéder à l’application API protégée.

#### <a name="how-to-protect-the-api-app-from-access-by-users-in-the-same-tenant"></a>Comment faire pour empêcher l’accès par les utilisateurs dans le locataire même l’application API

Jetons de support pour les utilisateurs dans le même locataire sont considérées comme valides pour l’application API protégée.  Si vous souhaitez vous assurer que seulement une entité de service peut appeler l’application API protégée, ajoutez le code dans l’API d’application protégé pour valider les demandes suivantes à partir du jeton :

* `appid`doit être l’ID client de l’application Azure AD qui est associée à l’appelant. 
* `oid`(`objectidentifier`) doit être l’ID d’entité de service de l’appelant. 

Service d’application fournit également les `objectidentifier` dans l’en-tête X-MS-CLIENT-PRINCIPAL-ID de revendication.

### <a name="how-to-protect-the-api-app-from-browser-access"></a>Comment faire pour protéger l’application de l’API de l’accès par navigateur

Si vous ne validez pas les revendications dans le code dans l’API d’application protégé, et si vous utilisez une nouvelle application Azure AD pour l’API d’application protégée, assurez-vous que réponse URL l’application Azure AD n’est pas le même que l’URL de base de l’application de l’API. Si l’URL de réponse pointe directement vers l’application API protégée, un utilisateur dans le locataire AD Azure même pourrait accéder à l’application de l’API, session et appeler l’API.

## <a id="tutorialstart"></a>Poursuivre la série de didacticiels d’applications .NET à l’API

Si vous suivez la série de didacticiels Node.js ou Java pour les applications d’API, passez à la section [étapes suivantes](#next-steps) . 

Le reste de cet article poursuit de la série de didacticiels d’applications .NET à l’API et suppose que vous avez terminé le [didacticiel de l’authentification utilisateur](app-service-api-dotnet-user-principal-auth.md) et que vous disposez de l’exemple d’application en cours d’exécution dans Azure avec l’authentification utilisateur est activée.

## <a name="set-up-authentication-in-azure"></a>Configurer l’authentification dans Azure

Dans cette section vous configurez le Service de l’application afin que les requêtes HTTP uniquement, elle permet d’atteindre l’application API de couche données sont ceux qui ont Azure valide les jetons AD au porteur. 

Dans la section suivante, vous configurez l’application API de niveau intermédiaire pour envoyer les informations d’identification de l’application à AD Azure, obtenir un jeton de support et le jeton de support à l’application d’API de couche données. Ce processus est illustré dans le diagramme.

![Diagramme du service d’authentification](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

Si vous rencontrez des problèmes en suivant les instructions du didacticiels, consultez la section [dépannage](#troubleshooting) à la fin du didacticiel. 

1. Dans [Azure portal](https://portal.azure.com/), accédez à la lame de **paramètres** de l’application de l’API que vous avez créé pour l’application de API ToDoListDataAPI (couche de données), puis cliquez sur **paramètres**.

2. De la lame de **paramètres** , recherchez la section **fonctionnalités** , puis cliquez sur **l’authentification / autorisation**.

    ![Authentification/autorisation dans Azure portal](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Dans la **l’authentification / autorisation** lame, cliquez **sur**.

4. Dans la liste déroulante **Action à entreprendre lors de la demande n’est pas authentifié** , sélectionnez **se connecter avec Azure Active Directory**.

    C’est le paramètre qui entraîne l’application Service s’assure authentifiés uniquement les demandes portée l’application API. Pour les demandes qui ont des jetons de support valide, application Service transmet les jetons le long de l’application API et remplit des en-têtes HTTP avec des revendications utilisées pour rendre ces informations plus facilement accessibles à votre code.

5. **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

    ![Lame d’authentification/autorisation dans Azure portal](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. De la lame **Azure les paramètres Active Directory** , cliquez sur **Express**.

    Avec l' **Express** option Azure peut créer automatiquement une application DAS dans votre annonce Azure [locataire](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). 

    Vous n’êtes pas obligé de créer un client, car chaque compte Azure a automatiquement un.

7. Sous **mode d’administration**, cliquez sur **Créer une nouvelle application d’Active Directory** s’il n’est pas déjà sélectionné.

    Le portail branche à la zone d’entrée de **Créer une application** avec une valeur par défaut. Par défaut, l’application Azure AD est porter le même nom que l’application de l’API. Si vous préférez, vous pouvez entrer un autre nom.
    
    ![Paramètres d’annonces Azure](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

    **Remarque**: en guise d’alternative, vous pouvez utiliser une publicité Azure application pour l’application API appelante et de l’application API protégée. Si vous choisissez cette solution, vous devez pas l’option **Créer une nouvelle application de AD** ici car vous déjà créé une application Azure AD précédemment dans le didacticiel de l’authentification utilisateur. Pour ce didacticiel, vous utiliserez des applications Azure AD pour l’application API appelante et de l’application API protégée distinctes.

8. Prenez note de la valeur qui est dans la zone d’entrée de **Créer l’application** . vous allez rechercher ultérieurement cette application DAS dans Azure portal classique.

7. Cliquez sur **OK**.

10. Dans la **l’authentification / autorisation** lame, cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

    Service d’application crée une application Azure Active Directory avec **l’ouverture de session d’URL** et **l’URL de réponse** est automatiquement définie sur l’URL de votre application d’API. Cette valeur permet aux utilisateurs de vos clients DAS pour vous connecter et accéder à l’application de l’API.

### <a name="verify-that-the-api-app-is-protected"></a>Vérifiez que l’application API est protégée.

1. Dans un navigateur, accédez à l’URL de l’application API : la lame **API application** dans Azure portal, cliquez sur le lien sous **l’URL**. 

    Vous êtes redirigé vers un écran de connexion car les demandes non authentifiées ne sont pas autorisés à accéder à l’application de l’API. 

    Si votre navigateur sont acheminés vers l’interface utilisateur de Swagger, votre navigateur peut déjà être connecté--dans ce cas, ouvrez une fenêtre InPrivate ou Incognito et accédez à l’URL de l’interface utilisateur Swagger.

18. Connectez-vous avec les informations d’identification d’un utilisateur dans votre client de DAS.

    Lorsque vous êtes connecté, la page « créée avec succès » s’affiche dans le navigateur.

## <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Configurez le projet ToDoListAPI pour acquérir et envoyer le jeton Azure AD

Dans cette section, vous effectuez les tâches suivantes :

* Ajoutez le code dans l’application d’API de niveau intermédiaire qui utilise des informations d’identification des applications Azure AD pour acquérir un jeton et envoyer les requêtes HTTP à l’application d’API de couche données.
* Obtenir les informations d’identification dont vous avez besoin à partir d’AD Azure.
* Entrez les informations d’identification dans les paramètres d’environnement de Service d’application Azure runtime dans l’API application de niveau intermédiaire. 

### <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Configurez le projet ToDoListAPI pour acquérir et envoyer le jeton Azure AD

Apportez les modifications suivantes dans le projet ToDoListAPI dans Visual Studio.

1. Supprimez tout le code dans le fichier *ServicePrincipal.cs* .

    Il s’agit du code qui utilise ADAL pour .NET pour obtenir le jeton de support AD Azure.  Il utilise plusieurs valeurs de configuration que vous allez définir ultérieurement dans l’environnement d’exécution Azure. Voici le code : 

        public static class ServicePrincipal
        {
            static string authority = ConfigurationManager.AppSettings["ida:Authority"];
            static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
            static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
            static string resource = ConfigurationManager.AppSettings["ida:Resource"];
        
            public static AuthenticationResult GetS2SAccessTokenForProdMSA()
            {
                return GetS2SAccessToken(authority, resource, clientId, clientSecret);
            }
        
            static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
            {
                var clientCredential = new ClientCredential(clientId, clientSecret);
                AuthenticationContext context = new AuthenticationContext(authority, false);
                AuthenticationResult authenticationResult = context.AcquireToken(
                    resource,
                    clientCredential);
                return authenticationResult;
            }
        }

    **Remarque :** Ce code nécessite la ADAL pour le package NuGet de .NET (Microsoft.IdentityModel.Clients.ActiveDirectory), qui est déjà installé dans le projet. Si vous créez ce projet de toutes pièces, vous devrez installer ce package. Ce package n’est pas installé automatiquement par le modèle de nouveau projet d’application API.

2. Dans *Contrôleurs de/ToDoListController*, supprimez les commentaires de code dans la `NewDataAPIClient` méthode qui ajoute le jeton à HTTP demande dans l’en-tête d’autorisation.

        client.HttpClient.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. Déployer le projet ToDoListAPI. (Droit sur le projet, puis cliquez sur **publication > publier**.)

    Visual Studio déploie le projet et ouvre un navigateur vers l’URL de base de l’application web. Vous verrez une page 403 erreur, ce qui est normale pour une tentative d’accéder à une URL de base d’API Web depuis un navigateur.

4. Fermez le navigateur.

### <a name="get-azure-ad-configuration-values"></a>Obtenir les valeurs de configuration AD Azure

11. Dans [Azure portal classique](https://manage.windowsazure.com/), passez à **Azure Active Directory**.

12. Dans l’onglet **répertoire** , cliquez sur vos clients de DAS.

14. Cliquez sur **Applications > Applications propriétaire de ma société**, puis cliquez sur la case à cocher.

15. Dans la liste des applications, cliquez sur le nom de l’objet Azure créés pour vous lorsque vous avez activé l’authentification de l’application de API ToDoListDataAPI (couche de données).

16. Cliquez sur l’onglet **configurer** .

5. Copiez la valeur de **l’ID de Client** et enregistrer un endroit où que vous pouvez l’obtenir à partir d’une version ultérieure. 

8. Dans le portail classique Azure revenir à la liste des **Applications de mon entreprise est propriétaire**, puis cliquez sur l’application DAS que vous avez créé pour l’application de ToDoListAPI API de niveau intermédiaire (celui que vous avez créé dans le didacticiel précédent, pas celle que vous avez créé dans ce didacticiel).

16. Cliquez sur l’onglet **configurer** .

5. Copiez la valeur de **l’ID de Client** et enregistrer un endroit où que vous pouvez l’obtenir à partir d’une version ultérieure.

6. Sous **les clés**, sélectionnez **1 an** à partir de la liste déroulante **Sélectionnez durée** .

6. Cliquez sur **Enregistrer**.

    ![Générer la clé de l’application](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copier la valeur de clé et enregistrer un endroit où que vous pouvez l’obtenir à partir d’une version ultérieure.

    ![Copier la nouvelle clé app](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### <a name="configure-azure-ad-settings-in-the-middle-tier-api-apps-runtime-environment"></a>Configurer les paramètres d’Azure AD dans un environnement d’exécution de l’application de niveau intermédiaire API

1. Accédez au [portail Azure](https://portal.azure.com/)et accédez à la blade **App de l’API** de l’application API qui héberge le projet TodoListAPI (niveau intermédiaire).

2. Cliquez sur **les paramètres > Paramètres de l’Application**.

3. Dans la section **paramètres de l’application** , ajoutez les clés et les valeurs suivantes :

  	| **Clé** | IDA : autorité |
  	|---|---|
  	| **Valeur** | https://login.microsoftonline.com/ {votre nom du locataire AD Azure} |
  	| **Exemple** | https://login.microsoftonline.com/contoso.onmicrosoft.com |

  	| **Clé** | IDA : ClientId |
  	|---|---|
  	| **Valeur** | ID de client de l’application appelante (niveau intermédiaire - ToDoListAPI) |
  	| **Exemple** | 960adec2-b74a-484a-960adec2-b74a-484a |

  	| **Clé** | IDA : ClientSecret |
  	|---|---|
  	| **Valeur** | Clé de l’application de l’application appelante (niveau intermédiaire - ToDoListAPI) |
  	| **Exemple** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

  	| **Clé** | IDA : ressources |
  	|---|---|
  	| **Valeur** | ID de client de l’application appelée (couche données - ToDoListDataAPI) |
  	| **Exemple** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

    **Remarque**: pour `ida:Resource`, assurez-vous que vous utilisez de l’application appelée **ID de client** et pas son **URI d’ID App**.

    `ida:ClientId`et `ida:Resource` sont différentes valeurs pour ce didacticiel, car vous utilisez séparent applicaations AD Azure pour la couche intermédiaire et une couche données. Si vous utilisez une seule application d’Azure AD pour l’application API appelante et l’application API protégée, vous utilisez la même valeur dans les deux `ida:ClientId` et `ida:Resource`.

    Le code utilise ConfigurationManager pour obtenir ces valeurs, afin qu’elles pourraient être stockées dans le fichier Web.config du projet ou dans l’environnement d’exécution Azure. Pendant l’exécution d’une application ASP.NET dans le Service d’application Azure, les paramètres d’environnement substituent automatiquement les paramètres à partir de Web.config. Paramètres d’environnement sont généralement un [moyen plus sûr de stocker des informations sensibles par rapport à un fichier Web.config](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

6. Cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### <a name="test-the-application"></a>Test de l’application

1. Dans un navigateur, accédez à l’URL HTTPS de l’application web de front-end AngularJS.

2. Cliquez sur l’onglet de **Liste des tâches** et se connecter avec des informations d’identification d’un utilisateur dans votre client AD Azure. 

4. Ajouter des éléments d’action pour vérifier que l’application fonctionne.

    ![À la page de liste des tâches](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

    Si l’application ne fonctionne pas comme prévu, vérifiez de nouveau tous les paramètres que vous avez entré dans le portail Azure. Si tous les paramètres semblent être corrects, reportez-vous à la section de [résolution des problèmes](#troubleshooting) plus loin dans ce didacticiel.

## <a name="protect-the-api-app-from-browser-access"></a>Protéger l’application de l’API de l’accès par navigateur

Pour ce didacticiel, vous avez créé une nouvelle application Azure AD pour l’application de l’API de ToDoListDataAPI (couche de données). Comme vous l’avez vu, lorsque le Service d’application crée une application DAS, il configure l’application DAS d’une façon qui permet à un utilisateur d’accéder à l’URL de l’application de l’API dans un navigateur et connectez-vous. Cela signifie qu’il est possible pour un utilisateur final dans votre client AD Azure, pas seulement un service principal, pour accéder à l’API. 

Si vous souhaitez empêcher l’accès de navigateur sans écrire de code dans l’API d’application protégé, vous pouvez modifier l' **URL de la réponse** de l’application DAS pour qu’elle soit différente de l’API de l’application de base. 

### <a name="disable-browser-access"></a>Désactiver l’accès du navigateur

1. Dans l’onglet de **configuration** du portail classique pour l’application DAS qui a été créée pour le TodoListService, modifiez la valeur dans le champ **URL de réponse** afin qu’elle soit une URL valide, mais pas l’URL de l’application API.
 
2. Cliquez sur **Enregistrer**.

### <a name="verify-browser-access-no-longer-works"></a>Vérifiez l’accès par navigateur ne fonctionne plus

Plus tôt vous vérifié que vous pouvez accéder à l’URL d’application API à partir d’un navigateur en ouvrant une session avec des informations d’identification d’un utilisateur individuel. Dans cette section, vous vérifiez que ce n’est plus possible. 

1. Dans une nouvelle fenêtre de navigateur, accédez à l’URL de l’application de l’API à nouveau.

2. Vous connecter lorsque vous êtes invité à le faire.

3. Ouverture de session réussit mais mène à une page d’erreur.

    Vous avez configuré l’application DAS afin que les utilisateurs du locataire DAS ne peut pas se connecter et accéder à l’API à partir d’un navigateur. Vous pouvez toujours accéder à l’application de l’API à l’aide d’un jeton principal service, ce que vous pouvez vérifier en accédant à l’URL de l’application web en ajoutant plus de choses.

## <a name="restrict-access-to-a-particular-service-principal"></a>Restreindre l’accès à une entité de service spécifique  

Pour l’instant, tout appelant qui peut d’obtenir un jeton pour un utilisateur ou principal du service dans votre client AD Azure peut appeler l’application de l’API de TodoListDataAPI (couche de données). Vous pouvez souhaiter vous assurer que l’application d’API de couche données n’accepte que les appels à partir de TodoListAPI (niveau intermédiaire) API app et uniquement à partir d’une entité de service particulier. 

Vous pouvez ajouter ces restrictions en ajoutant du code pour valider la `appid` et `objectidentifier` des créances sur les appels entrants.

Pour ce didacticiel, vous placez le code qui valide l’ID de l’application et l’ID principal de service directement dans vos actions du contrôleur.  Sont des alternatives à utiliser personnalisé `Authorize` d’attribut ou d’effectuer cette validation votre démarrage séquences (par exemple, OWIN middleware). Pour obtenir un exemple de ce dernier, consultez [Cet exemple d’application](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs). 

Apportez les modifications suivantes au projet TodoListDataAPI.

2. Ouvrez le fichier *Controllers/TodoListController.cs* .

3. Supprimez les lignes qui définissent `trustedCallerClientId` et `trustedCallerServicePrincipalId`.

        private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
        private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. Ne commentez pas le code dans la méthode CheckCallerId. Cette méthode est appelée au début de chaque méthode d’action dans le contrôleur. 

        private static void CheckCallerId()
        {
            string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
            string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
            {
                throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
            }
        }

5. Redéployer le projet ToDoListDataAPI au Service d’application Azure.

6. Dans votre navigateur, atteindre l’URL HTTPS de l’AngularJS front-end web application et dans la page d’accueil, cliquez sur l’onglet de **Liste des tâches** .

    L’application ne fonctionne pas parce que l’échec des appels pour le back-end. Le nouveau code est vérification appid réel et objectidentifier, mais il ne dispose pas encore les valeurs correctes pour les vérifier. La Console outils de développeur de navigateur signale que le serveur retourne une erreur HTTP 401.

    ![Erreur dans le développement des outils Console](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

    Dans les étapes suivantes, vous configurez les valeurs attendues.

8. À l’aide de PowerShell d’AD Azure, d’obtenir la valeur de l’entité du service pour l’application Azure annonce que vous avez créé pour le projet TodoListWebApp.

    une barre d’outils. Pour obtenir des instructions sur la façon d’installer PowerShell d’Azure et de se connecter à votre abonnement, consultez [L’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).

    b. Pour obtenir une liste des noms principaux de service, exécutez le `Login-AzureRmAccount` commande, puis le `Get-AzureRmADServicePrincipal` commande.

    c. Rechercher des objectid pour l’entité du service de l’application de TodoListAPI et enregistrez-le dans un emplacement que vous pouvez copier à partir d’une version ultérieure.

7. Dans le portail Azure, accédez à la lame d’application API pour l’application de l’API que vous avez déployée le projet ToDoListDataAPI.

9. Cliquez sur **les paramètres > Paramètres de l’Application**.

3. Dans la section **paramètres de l’application** , ajoutez les clés et les valeurs suivantes :

  	| **Clé** | TODO:TrustedCallerServicePrincipalId |
  	|---|---|
  	| **Valeur** | Id du service principal de l’appel d’application |
  	| **Exemple** | 4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072 |

  	| **Clé** | TODO:TrustedCallerClientId |
  	|---|---|
  	| **Valeur** | ID de client de l’appel d’application - copiée à partir de l’application TodoListAPI AD Azure |
  	| **Exemple** | 960adec2-b74a-484a-960adec2-b74a-484a |

6. Cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. Dans votre navigateur, revenir à l’URL de l’application web et dans la page d’accueil, cliquez sur l’onglet de **Liste des tâches** .

    Ce temps que l’application fonctionne comme prévu, car l’application de l’appelant de confiance ID et l’ID principal de service sont les valeurs attendues.

    ![À la page de liste des tâches](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## <a name="building-the-projects-from-scratch"></a>Création de projets à partir de zéro

Les deux projets d’API Web ont été créés en utilisant le modèle de projet **Application d’API Azure** et en remplaçant le contrôleur de valeurs par défaut avec un contrôleur de la liste des tâches. Pour acquérir les jetons de principal du service AD Azure dans le projet ToDoListAPI, le package NuGet [Active Directory d’authentification Library (ADAL) pour .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) a été installé.
 
Pour plus d’informations sur la création d’une application de page unique de AngularJS avec un API Web back-end tels que ToDoListAngular, reportez-vous à la section [mains sur Lab : créer une Application de Page unique (SPA) avec l’API de Web ASP.NET et Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Pour plus d’informations sur la façon d’ajouter du code d’authentification AD Azure, consultez [Sécurisation AngularJS unique Page applications Azure AD](../active-directory/active-directory-devquickstarts-angular.md).

## <a name="troubleshooting"></a>Résolution des problèmes

[AZURE.INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* Assurez-vous que vous ne confondez pas ToDoListAPI (niveau intermédiaire) et ToDoListDataAPI (couche de données). Par exemple, dans ce didacticiel vous ajoutez une authentification à l’application API de la couche de données, **mais la clé de l’application doit provenir de l’application Azure annonce que vous avez créé pour l’application de l’API de niveau intermédiaire**.

## <a name="next-steps"></a>Étapes suivantes

C’est le dernier didacticiel dans la série d’applications d’API. 

Pour plus d’informations sur Azure Active Directory, consultez les ressources suivantes.

* [Guide d’Azure annonces destinées aux développeurs](http://aka.ms/aaddev)
* [Scénarios AD Azure](http://aka.ms/aadscenarios)
* [Exemples d’annonces Azure](http://aka.ms/aadsamples)

    L’exemple de [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) est similaire à celui présenté dans ce didacticiel, mais sans l’aide de l’authentification du Service de l’application.

Pour plus d’informations sur les autres méthodes de déploiement des projets de Visual Studio pour applications d’API, à l’aide de Visual Studio ou grâce à l' [automatisation du déploiement](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) d’un [système de contrôle de code source](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consultez [comment déployer une application de Service d’application Azure](../app-service-web/web-sites-deploy.md).
