<properties
    pageTitle="Prend en charge les CORS dans le Service d’application | Microsoft Azure"
    description="Apprenez à utiliser le support CORS dans le Service d’application Azure Azure."
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
    ms.topic="get-started-article"
    ms.date="08/27/2016"
    ms.author="rachelap"/>

# <a name="consume-an-api-app-from-javascript-using-cors"></a>Utiliser une application API à partir de JavaScript à l’aide de CORS

Service d’application offre une prise en charge intégrée pour le [Partage de ressources origine croisée (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing), qui permet aux clients d’effectuer des appels de domaines à des API qui est hébergés dans les applications de l’API JavaScript. Service d’application vous permet de configurer l’accès à votre API CORS sans écrire de code dans votre API.

Cet article contient deux sections :

* La section [comment configurer CORS](#corsconfig) décrit en général la configuration CORS API app, application web, ou une application mobile. Il s’applique également à toutes les infrastructures qui sont pris en charge par le Service d’application, y compris .NET, Node.js et Java. 

* À partir de la section de [poursuivre les didacticiels .NET mise en route](#tutorialstart) , l’article est un didacticiel qui montre les que Cors prend en charge en s’appuyant sur ce que vous l’avez fait dans [les applications API première mise en route de didacticiel](app-service-api-dotnet-get-started.md). 

## <a id="corsconfig"></a>Comment faire pour configurer les CORS dans le Service d’application Azure

Vous pouvez configurer les CORS dans le portail Azure, ou à l’aide des outils du [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md) .

#### <a name="configure-cors-in-the-azure-portal"></a>Configurer les CORS dans le portail Azure

8. Dans un navigateur, accédez au [portail Azure](https://portal.azure.com/).

2. Cliquez sur les **Services d’application**, puis cliquez sur le nom de votre application d’API.

    ![Sélectionnez l’API application portail](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. De la lame de **paramètres** qui s’ouvre à droite de la lame **d’API app** , recherchez la section de **l’API** , puis cliquez sur **CORS**.

    ![Sélectionnez CORS de lame de paramètres](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. Dans le texte, saisissez l’URL ou les URL que vous souhaitez autoriser les appels provenant de JavaScript.


    Par exemple, si vous avez déployé votre application JavaScript pour une application web nommée todolistangular, entrez « https://todolistangular.azurewebsites.net ». Comme alternative, vous pouvez entrer un astérisque (*) pour spécifier que tous les domaines d’origine sont acceptés.


13. Cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    Après avoir cliqué sur **Enregistrer**, l’API application accepte les appels JavaScript depuis l’URL spécifiées.

#### <a name="configure-cors-by-using-azure-resource-manager-tools"></a>Configurer les CORS à l’aide des outils du Gestionnaire de ressources Azure

Vous pouvez également configurer les CORS pour une application API à l’aide de [modèles d’Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md) dans les outils de ligne de commande, tels que [PowerShell d’Azure](../powershell-install-configure.md) et d' [Azure CLI](../xplat-cli-install.md). 

Pour obtenir un exemple d’un modèle de gestionnaire de ressources Azure qui définit la propriété CORS, ouvrez le [fichier azuredeploy.json dans le référentiel pour l’exemple d’application de ce didacticiel](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Recherchez la section du modèle qui ressemble à l’exemple suivant :

        "cors": {
            "allowedOrigins": [
                "todolistangular.azurewebsites.net"
            ]
        }

## <a id="tutorialstart"></a>Poursuivre le didacticiel mise en route de .NET

Si vous suivez le Node.js ou Java-mise en route la série pour les applications d’API, vous avez terminé la série démarrée lors de l’obtention. Passez à la section [étapes suivantes](#next-steps) pour rechercher des suggestions pour l’apprentissage supplémentaires sur les applications de l’API.

Le reste de cet article est la suite de la série de mise en route de .NET et suppose que [le premier didacticiel](app-service-api-dotnet-get-started.md)est terminé.

## <a name="deploy-the-todolistangular-project-to-a-new-web-app"></a>Déployer le projet de ToDoListAngular pour une nouvelle application web

Dans [le premier didacticiel](app-service-api-dotnet-get-started.md), vous avez créé une application API de couche intermédiaire et une application d’API de couche données. Dans ce didacticiel vous créez une application web de page unique application (SPA) cette application appelle l’API de niveau intermédiaire. Pour le SPA à utiliser vous devez activer les CORS sur l’application de niveau intermédiaire API. 

Dans [liste des tâches, exemple d’application](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list), le projet ToDoListAngular est un simple client de AngularJS qui appelle le projet ToDoListAPI Web API de niveau intermédiaire. Le code JavaScript dans le fichier *app/scripts/todoListSvc.js* appelle l’API en utilisant le fournisseur de AngularJS HTTP. 

        angular.module('todoApp')
        .factory('todoListSvc', ['$http', function ($http) {

            $http.defaults.useXDomain = true;
            delete $http.defaults.headers.common['X-Requested-With']; 
        
            return {
                getItems : function(){
                    return $http.get(apiEndpoint + '/api/TodoList');
                },

                /* Get by ID, Put, and Delete methods not shown */

                postItem : function(item){
                    return $http.post(apiEndpoint + '/api/TodoList', item);
                }
            };
        }]);

### <a name="create-a-new-web-app-for-the-todolistangular-project"></a>Créer une nouvelle application web pour le projet ToDoListAngular

La procédure pour créer une nouvelle application de web Service de l’application et de déploiement d’un projet pour qu’il ressemble à l’écran pour [créer et déployer une application API dans le premier didacticiel dans cette série](app-service-api-dotnet-get-started.md#createapiapp). La seule différence est que le type d’application **Web App** au lieu de **l’Application de l’API**.  Pour les captures d’écran des boîtes de dialogue, reportez-vous à la section. 

1. Dans l' **Explorateur de solutions**, cliquez sur le projet ToDoListAngular, puis cliquez sur **Publier**.

3.  Dans l’onglet **profil** de l’Assistant **Publication sur le Web** , cliquez sur **Service d’application Microsoft Azure**.

5. Dans la boîte de dialogue **Service de l’application** , cliquez sur **Nouveau**.

3. Dans l’onglet **hébergement** de la boîte de dialogue de **Création de Service App** , entrez un **Nom de l’application Web** qui est unique dans le domaine *azurewebsites.net* . 

5. Choisissez l' Azure **abonnement** que vous souhaitez utiliser.

6. Dans la liste déroulante **Groupe de ressources** , choisissez le même groupe de ressources que vous avez créé précédemment.

4. Dans la liste déroulante **Plan de Service d’application** , cliquez sur le même plan que vous avez créé précédemment. 

7. Cliquez sur **créer**.

    Visual Studio crée l’application web crée un profil de publication pour celui-ci et affiche l’étape de **connexion** de l’Assistant **Publication sur le Web** .

    Ne cliquez pas encore sur **Publier** . Dans la section suivante, vous configurez la nouvelle application web pour appeler l’application API de niveau intermédiaire qui s’exécute dans une application de Service. 

### <a name="set-the-middle-tier-url-in-web-app-settings"></a>Définir l’URL de la couche intermédiaire dans les paramètres de l’application web

1. Accédez au [portail Azure](https://portal.azure.com/)et accédez à la blade **d’Application Web** pour l’application web que vous avez créé pour héberger le projet TodoListAngular (front-end).

2. Cliquez sur **les paramètres > Paramètres de l’Application**.

3. Dans la section **paramètres de l’application** , ajoutez la clé et la valeur suivantes :

  	|Clé|Valeur|Exemple
  	|---|---|---|
  	|toDoListAPIURL|nom de l’application intermédiaire API https://{Your} .azurewebsites .net|https://todolistapi0121.azurewebsites.NET|

4. Cliquez sur **Enregistrer**.

    Lorsque le code s’exécute dans Azure, cette valeur remplace l’URL de l’hôte local qui se trouve dans le fichier *Web.config* . 

    Le code obtient la valeur de paramètre se trouve dans *index.cshtml*:

        <script type="text/javascript">
            var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
        </script>
        <script src="app/scripts/todoListSvc.js"></script>

    Le code de *todoListSvc.js* utilise le paramètre :

        return {
            getItems : function(){
                return $http.get(apiEndpoint + '/api/TodoList');
            },
            getItem : function(id){
                return $http.get(apiEndpoint + '/api/TodoList/' + id);
            },
            postItem : function(item){
                return $http.post(apiEndpoint + '/api/TodoList', item);
            },
            putItem : function(item){
                return $http.put(apiEndpoint + '/api/TodoList/', item);
            },
            deleteItem : function(id){
                return $http({
                    method: 'DELETE',
                    url: apiEndpoint + '/api/TodoList/' + id
                });
            }
        };

### <a name="deploy-the-todolistangular-web-project-to-the-new-web-app"></a>Déployer le projet web ToDoListAngular pour la nouvelle application web

*  Dans Visual Studio, dans l’étape de **connexion** de l’Assistant **Publication sur le Web** , cliquez sur **Publier**.

    Visual Studio déploie le projet ToDoListAngular pour la nouvelle application web et ouvre un navigateur vers l’URL de l’application web. 

### <a name="test-the-application-without-cors-enabled"></a>Tester l’application sans CORS activé 

2. Dans votre navigateur, outils de développement, ouvrez la fenêtre de Console.

3. Dans la fenêtre du navigateur qui affiche le AngularJS UI, cliquez sur le lien de la **Liste des tâches** .

    Le code JavaScript essaie d’appeler l’application de niveau intermédiaire API, mais l’appel échoue car le serveur frontal est en cours d’exécution dans un autre domaine que le serveur principal. Fenêtre de la Console d’outils de développeur du navigateur affiche un message d’erreur d’origine croisée.

    ![Message d’erreur d’origine croisée](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## <a name="configure-cors-for-the-middle-tier-api-app"></a>Configurer les CORS pour l’application d’API de niveau intermédiaire

Dans cette section, vous configurez le paramètre CORS dans Azure le ToDoListAPI API application de niveau intermédiaire. Ce paramètre permet au niveau intermédiaire, application API pour recevoir un appel de JavaScript à partir de l’application web que vous avez créé pour le projet ToDoListAngular.

8. Dans un navigateur, accédez au [portail Azure](https://portal.azure.com/).

2. Cliquez sur les **Services d’application**, puis cliquez sur l’application de API ToDoListAPI (niveau intermédiaire).

    ![Sélectionnez l’API application portail](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. De la lame de **paramètres** qui s’ouvre à droite de la lame **d’API app** , recherchez la section de **l’API** , puis cliquez sur **CORS**.

    ![Sélectionnez CORS de portail](./media/app-service-api-cors-consume-javascript/clicksettings.png)

12. Dans la zone de texte, entrez l’URL pour l’application web ToDoListAngular (front-end). Par exemple, si vous avez déployé le projet ToDoListAngular pour une application web nommée todolistangular0121, autoriser des appels à partir de l’URL `https://todolistangular0121.azurewebsites.net`.

    Comme alternative, vous pouvez entrer un astérisque (*) pour spécifier que tous les domaines d’origine sont acceptés.

13. Cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    Après avoir cliqué sur **Enregistrer**, l’API application accepte les appels JavaScript depuis l’URL spécifiée. Dans cette capture d’écran, l’application API de ToDoListAPI0223 accepte les appels client JavaScript à partir de l’application web ToDoListAngular.

### <a name="test-the-application-with-cors-enabled"></a>Test de l’application avec CORS activé

* Ouvrez un navigateur vers l’URL HTTPS de l’application web. 

    Cette fois l’application vous permet d’afficher, d’ajouter, de modifier et de supprimer des éléments d’action. 

    ![À la page de liste des tâches de l’exemple d’application](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## <a name="app-service-cors-versus-web-api-cors"></a>Service application CORS et Web API CORS

Dans un projet Web API, vous pouvez installer le package NuGet de [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) pour spécifier dans le code les domaines accepte votre API JavaScript appelle à partir de.
 
Prise en charge des API CORS Web est plus flexible que la prise en charge de l’application Service CORS. Par exemple, dans le code, vous pouvez spécifier différentes origines acceptées pour les méthodes d’action différentes, alors que pour l’application de Service CORS vous spécifiez un seul ensemble d’origines acceptées pour toutes les méthodes de l’API application de.

> [AZURE.NOTE] N’essayez pas de l’utiliser Web API CORS et CORS de Service d’application dans une application API. Service d’application CORS est prioritaire et Web API CORS n’a aucun effet. Par exemple, si vous activez un domaine d’origine dans le Service d’application et activez tous les domaines d’origine dans votre code de l’API Web, votre application Azure API n’accepte les appels à partir du domaine spécifié dans Azure.

### <a name="how-to-enable-cors-in-web-api-code"></a>Comment faire pour activer les CORS dans le code de l’API Web

Les étapes suivantes résument le processus d’activation de la prise en charge Web API CORS. Pour plus d’informations, consultez [Activation des demandes de Cross-Origin dans ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. Dans un projet Web API, installez le package NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) .

1. Inclure un `config.EnableCors()` ligne de code dans la méthode **Register** de la classe **WebApiConfig** , comme dans l’exemple suivant. 

        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                // Web API configuration and services
                
                // The following line enables you to control CORS by using Web API code
                config.EnableCors();
    
                // Web API routes
                config.MapHttpAttributeRoutes();
    
                config.Routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { id = RouteParameter.Optional }
                );
            }
        }

1. Dans votre contrôleur API Web, ajouter un `using` instruction pour le `System.Web.Http.Cors` espace de noms et ajouter la `EnableCors` attribut à la classe controller ou à méthodes d’action individuelles. Dans l’exemple suivant, prise en charge CORS s’applique au contrôleur entière.

        namespace ToDoListAPI.Controllers 
        {
            [HttpOperationExceptionFilterAttribute]
            [EnableCors(origins:"https://todolistangular0121.azurewebsites.net", headers:"accept,content-type,origin,x-my-header", methods: "get,post")]
            public class ToDoListController : ApiController
 
## <a name="using-azure-api-management-with-api-apps"></a>À l’aide de la gestion de l’API Azure avec des applications de l’API

Si vous utilisez Gestion des API Azure avec une application API, configurer CORS dans Gestion de l’API et non dans l’API d’application. Pour plus d’informations, consultez les ressources suivantes :

* [Vue d’ensemble de gestion API Azure (vidéo : CORS commence à 12:10)](https://azure.microsoft.com/documentation/videos/azure-api-management-overview/)
* [Gestion d’API entre les stratégies de domaine](https://msdn.microsoft.com/library/azure/dn894084.aspx#CORS)
 
## <a name="troubleshooting"></a>Résolution des problèmes

Dans le cas où vous rencontrez un problème tout en parcourant ce didacticiel, voici quelques suggestions de résolution des problèmes.

* Assurez-vous que vous utilisez la dernière version du [SDK Azure pour .NET de Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003).

* Assurez-vous que vous avez entré `https` dans le paramètre CORS et assurez-vous que vous utilisez `https` pour exécuter l’application web frontale.

* Assurez-vous que vous avez entré le paramètre CORS dans l’API d’application de couche intermédiaire, et non dans l’application web frontale.

* Si vous configurez CORS dans le code d’application et de Service d’application Azure, notez que le paramètre application Service CORS remplacera tout ce que vous faites dans le code de l’application. 

Pour en savoir plus sur les fonctionnalités de Visual Studio qui simplifient la résolution des problèmes, voir [résolution des problèmes de Service d’application Azure des applications dans Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Étapes suivantes 

Dans cet article, vous avez vu comment activer la prise en charge de l’application Service CORS afin que le code JavaScript client peut appeler une API dans un autre domaine. Pour en savoir plus sur les applications d’API, lire l' [introduction à l’authentification dans le Service d’application](../app-service/app-service-authentication-overview.md)et passez le didacticiel [d’authentification de l’utilisateur pour les applications d’API](app-service-api-dotnet-user-principal-auth.md) .
