<properties
    pageTitle="Mise en route avec les applications d’API et ASP.NET dans le Service d’application | Microsoft Azure"
    description="Découvrez comment créer, déployer et utiliser une application API d’ASP.NET dans le Service d’application Azure, à l’aide de Visual Studio 2015."
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
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="rachelap"/>

# <a name="get-started-with-api-apps-aspnet-and-swagger-in-azure-app-service"></a>Mise en route de Swagger dans le Service d’application Azure, ASP.NET et les applications API

[AZURE.INCLUDE [selector](../../includes/app-service-api-get-started-selector.md)]

Il s’agit de la première d’une série de didacticiels qui montrent comment utiliser les fonctionnalités de Service d’application Azure qui sont utiles pour le développement et l’hébergement des API RESTful.  Ce didacticiel décrit la prise en charge de l’API des métadonnées dans un format de Swagger.

Vous apprendrez à :

* Comment créer et déployer des [applications de l’API](app-service-api-apps-why-best-platform.md) dans le Service d’application Azure à l’aide d’outils intégrés à Visual Studio 2015.
* Comment automatiser la découverte de l’API à l’aide du package Swashbuckle NuGet pour générer dynamiquement des métadonnées de l’API de Swagger.
* Comment utiliser les métadonnées de l’API de Swagger pour générer automatiquement le code de client pour une application API.

## <a name="sample-application-overview"></a>Vue d’ensemble des applications exemple

Dans ce didacticiel, vous travaillez avec une application d’exemple de liste des tâches simples. L’application a un frontal de l’application d’une page (SPA), un niveau intermédiaire ASP.NET Web API et une couche de données API de Web ASP.NET.

![Exemple de diagramme d’application API Apps](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Voici une capture d’écran de [AngularJS](https://angularjs.org/) front-end.

![Applications d’API exemple d’application à la liste des tâches](./media/app-service-api-dotnet-get-started/todospa.png)

La solution Visual Studio comprend trois projets :

![](./media/app-service-api-dotnet-get-started/projectsinse.png)

* **ToDoListAngular** - front-end : un SPA AngularJS qui appelle la couche intermédiaire.

* **ToDoListAPI** - le niveau intermédiaire : un projet de l’API de Web ASP.NET qui appelle la couche de données pour effectuer des opérations sur des éléments d’action.

* **ToDoListDataAPI** - la couche données : un projet ASP.NET Web API qui effectue des opérations sur des éléments d’action.

L’architecture à trois niveaux est une des nombreuses architectures que vous pouvez mettre en œuvre à l’aide d’applications de l’API et êtes utilisé ici uniquement à des fins de démonstration. Le code de chaque niveau est aussi simple que possible démontrer les fonctionnalités des applications d’API ; par exemple, la couche de données utilise la mémoire serveur plutôt que sur une base de données comme mécanisme de persistance.

À la fin de ce didacticiel, vous avez les deux projets d’API Web haut et s’exécutant dans le nuage dans les applications de l’API de Service d’application.

Le didacticiel suivant dans la série déploie le front-end SPA vers le nuage.

## <a name="prerequisites"></a>Conditions préalables

* API de Web ASP.NET - didacticiels instructions supposent une connaissance de base de l’utilisation de ASP.NET [Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) dans Visual Studio.

* Compte Azure - vous pouvez [Ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F) ou [avantages d’abonné activation de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

    Si vous souhaitez commencer avec le Service d’application Azure avant de vous inscrivez pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pouvez créer une application de courte durée starter immédiatement dans le Service d’application : **Aucune carte de crédit est obligatoire**et aucun des engagements.

* Visual Studio 2015 avec le [Kit de développement logiciel Azure pour .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) - le Kit de développement installe Visual Studio 2015 automatiquement si vous ne l’avez déjà pas.

    * Dans Visual Studio, cliquez sur Aide -> à propos de Microsoft Visual Studio et vérifiez que vous avez « Outils de Service d’application Azure v2.9.1 » ou ultérieur est installé.

    ![Version d’outils d’application Azure](./media/app-service-api-dotnet-get-started/apiversion.png)

    >[AZURE.NOTE] Selon le nombre de dépendances SDK vous disposez sur votre ordinateur, l’installation du Kit de développement peut prendre du temps, de quelques minutes à une demi-heure ou plus.

## <a name="download-the-sample-application"></a>Télécharger l’exemple d’application

1. Télécharger le référentiel de [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) .

    Vous pouvez cliquez sur le bouton **Télécharger ZIP** ou cloner le référentiel sur votre ordinateur local.

2. Ouvrez la solution de la liste des tâches dans Visual Studio 2015 ou 2013.
   1. Vous devez approuver chaque solution.
        ![Avertissement de sécurité](./media/app-service-api-dotnet-get-started/securitywarning.png)

3. Générez la solution (CTRL + MAJ + B) pour restaurer les packages NuGet.

    Si vous souhaitez voir l’application dans l’opération avant de le déployer, vous pouvez l’exécuter localement. Assurez-vous que ToDoListDataAPI est votre projet de démarrage et exécuter la solution. Vous devriez voir une erreur HTTP 403 dans votre navigateur.

## <a name="use-swagger-api-metadata-and-ui"></a>Utilisez des métadonnées de l’API de Swagger et de l’interface utilisateur

Prise en charge des métadonnées de API [Swagger](http://swagger.io/) 2.0 est intégré dans les Service d’application Azure. Chaque application API peut spécifier un point de terminaison URL retourne les métadonnées pour l’API au format JSON de Swagger. Métadonnées retournées à partir de ce point de terminaison peuvent être utilisée pour générer le code client.

Un projet ASP.NET Web API peut générer dynamiquement des métadonnées de Swagger à l’aide du package NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) . Le package Swashbuckle NuGet est déjà installé dans les projets ToDoListDataAPI et ToDoListAPI que vous avez téléchargé.

Dans cette section du didacticiel, vous examinez les métadonnées 2.0 de Swagger générée et puis essayer un test de l’interface utilisateur qui est basée sur les métadonnées de Swagger.

1. Définissez le projet de ToDoListDataAPI (**pas** le projet ToDoListAPI) en tant que projet de démarrage.

    ![ToDoDataAPI de définir comme projet de démarrage](./media/app-service-api-dotnet-get-started/startupproject.png)

2. Appuyez sur F5 ou cliquez sur **de débogage > Démarrer le débogage** pour exécuter le projet en mode débogage.

    Le navigateur s’ouvre et affiche la page d’erreur HTTP 403.

3. Dans la barre d’adresse de votre navigateur, ajoutez `swagger/docs/v1` à la fin de la ligne, puis appuyez sur retour. (L’URL est `http://localhost:45914/swagger/docs/v1`.)

    Il s’agit de l’URL par défaut utilisée par Swashbuckle pour retourner des métadonnées de JSON de 2.0 Swagger de l’API.

    Si vous utilisez Internet Explorer, le navigateur vous invite à télécharger un fichier *v1.json* .

    ![Téléchargement de métadonnées JSON dans Internet Explorer](./media/app-service-api-dotnet-get-started/iev1json.png)

    Si vous utilisez Edge, Firefox ou Chrome, le navigateur affiche le JSON dans la fenêtre du navigateur. Différents navigateurs gèrent différemment les JSON, et la fenêtre de votre navigateur ne semble pas exactement comme dans l’exemple.

    ![Métadonnées JSON dans Chrome](./media/app-service-api-dotnet-get-started/chromev1json.png)

    L’exemple suivant montre la première section de métadonnées Swagger pour l’API, la définition de la méthode Get. Ces métadonnées sont les disques de l’interface utilisateur Swagger que vous utilisez dans les étapes suivantes, et que vous l’utiliser dans une section ultérieure de ce didacticiel pour générer automatiquement le code client.

        {
          "swagger": "2.0",
          "info": {
            "version": "v1",
            "title": "ToDoListDataAPI"
          },
          "host": "localhost:45914",
          "schemes": [ "http" ],
          "paths": {
            "/api/ToDoList": {
              "get": {
                "tags": [ "ToDoList" ],
                "operationId": "ToDoList_GetByOwner",
                "consumes": [ ],
                "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
                "parameters": [
                  {
                    "name": "owner",
                    "in": "query",
                    "required": true,
                    "type": "string"
                  }
                ],
                "responses": {
                  "200": {
                    "description": "OK",
                    "schema": {
                      "type": "array",
                      "items": { "$ref": "#/definitions/ToDoItem" }
                    }
                  }
                },
                "deprecated": false
              },

4. Fermez le navigateur et arrêter le débogage de Visual Studio.

5. Dans le projet ToDoListDataAPI dans **L’Explorateur de solutions**, ouvrez le fichier *App_Start\SwaggerConfig.cs* , puis faites défiler jusqu'à la ligne 174 et ne commentez pas le code suivant.

        /*
            })
        .EnableSwaggerUi(c =>
            {
        */

    Le fichier *SwaggerConfig.cs* est créé lorsque vous installez le package de la Swashbuckle dans un projet. Le fichier fournit un certain nombre de façons de configurer les Swashbuckle.

    Le code que vous avez supprimées permet à l’interface utilisateur Swagger que vous utilisez dans les étapes suivantes. Lorsque vous créez un projet Web API en utilisant le modèle de projet d’application API, ce code est commenté par défaut comme mesure de sécurité.

6. Exécutez de nouveau le projet.

7. Dans la barre d’adresse de votre navigateur, ajoutez `swagger` à la fin de la ligne, puis appuyez sur retour. (L’URL est `http://localhost:45914/swagger`.)

8. Lorsque la page de Swagger de l’interface utilisateur s’affiche, cliquez sur **liste des tâches** pour afficher les méthodes disponibles.

    ![Méthodes disponibles de l’interface utilisateur de swagger](./media/app-service-api-dotnet-get-started/methods.png)

9. Cliquez sur le bouton **obtenir le** premier dans la liste.

10. Dans la section **paramètres** , entrez un astérisque en tant que la valeur de la `owner` paramètre, puis cliquez sur **tester**.

    Lorsque vous ajoutez l’authentification dans les didacticiels suivants, le niveau intermédiaire fournit l’ID utilisateur réels à la couche données. Pour le moment, toutes les tâches ont astérisque comme leur ID de propriétaire, alors que l’application s’exécute sans l’authentification est activée.

    ![Essayer l’interface utilisateur de swagger](./media/app-service-api-dotnet-get-started/gettryitout1.png)

    L’interface utilisateur de Swagger appelle l’obtention de la liste des tâches, méthode et affiche le code de réponse et JSON.

    ![Swagger interface utilisateur essayer des résultats](./media/app-service-api-dotnet-get-started/gettryitout.png)

11. Cliquez sur **Valider**, puis cliquez sur la zone sous le **Modèle de schéma**.

    Cliquez sur le schéma de modèle indique automatiquement la boîte d’entrée où vous pouvez spécifier la valeur du paramètre de la méthode Post. (Si cela ne fonctionne pas dans Internet Explorer, utilisez un autre navigateur ou saisir la valeur de paramètre à l’étape suivante.)  

    ![Swagger interface utilisateur essayez-le Post](./media/app-service-api-dotnet-get-started/post.png)

12. Modifier le JSON dans le `todo` paramètre d’entrée de zone afin qu’il ressemble à l’exemple suivant, ou le remplacer par votre propre texte de description :

        {
          "ID": 2,
          "Description": "buy the dog a toy",
          "Owner": "*"
        }

13. Cliquez sur **tester**.

    L’API de liste des tâches retourne un code de réponse HTTP 204 qui indique la réussite.

14. Cliquez sur le bouton **obtenir le** premier, puis cliquez sur le bouton **tester** dans cette section de la page.

    La réponse de la méthode Get inclut désormais la nouvelle tâche.

15. Facultatif : Essayez également la placer, supprimer et obtenir par les méthodes de code.

16. Fermez le navigateur et arrêter le débogage de Visual Studio.

Swashbuckle fonctionne avec n’importe quel projet d’API de Web ASP.NET. Si vous souhaitez ajouter la génération de métadonnées de Swagger à un projet existant, installez le package Swashbuckle.

>[AZURE.NOTE] Métadonnées de swagger incluent un ID unique pour chaque opération de l’API. Par défaut, Swashbuckle peut générer d’opération Swagger en double ID pour vos méthodes de contrôleur API Web. Cela se produit si votre contrôleur possède HTTP des méthodes surchargées, tel que `Get()` et `Get(id)`. Pour plus d’informations sur la façon de gérer les surcharges, consultez les [définitions généré par Swashbuckle de personnaliser des API](app-service-api-dotnet-swashbuckle-customize.md). Si vous créez un projet Web API dans Visual Studio en utilisant le modèle d’application de API d’Azure, le code qui génère l’opération unique ID est automatiquement ajouté dans le fichier *SwaggerConfig.cs* .  

## <a id="createapiapp"></a>Créer une application API dans Azure et déployer le code

Dans cette section, vous utilisez les outils Azure qui sont intégrés dans l’Assistant de Visual Studio, **Publier le site Web** pour créer une nouvelle application API dans Azure. Puis, vous déployez le projet de ToDoListDataAPI sur la nouvelle application API et appelez l’API en exécutant l’interface utilisateur de Swagger.

1. Dans l' **Explorateur de solutions**, cliquez sur le projet ToDoListDataAPI, puis cliquez sur **Publier**.

    ![Cliquez sur Publier dans Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu.png)

2.  Dans le **profil** de l’Assistant **Publication sur le Web** , cliquez sur **Service d’application Microsoft Azure**.

    ![Cliquez sur Azure Service application de publication Web](./media/app-service-api-dotnet-get-started/selectappservice.png)

3. Connectez-vous à votre compte Azure si vous ne le n'avez pas déjà fait, ou actualiser vos informations d’identification si elles vous expiré.

4. Dans la boîte de dialogue Service de l’application, choisissez l' Azure **abonnement** que vous souhaitez utiliser, puis cliquez sur **Nouveau**.

    ![Cliquez sur Nouveau dans la boîte de dialogue Service de l’application](./media/app-service-api-dotnet-get-started/clicknew.png)

    L’onglet de **l’hébergement** de la boîte de dialogue de **Création de Service application** s’affiche.

    Vous déployez un projet Web API qui a Swashbuckle installé, Visual Studio suppose que vous souhaitez créer une application API. Cela est indiqué par le **Nom de l’application API** de titre et par le fait que la liste déroulante **Type de modification** est définie pour **l’Application de l’API**.

    ![Type d’application dans la boîte de dialogue Service de l’application](./media/app-service-api-dotnet-get-started/apptype.png)

5. Entrez un **Nom d’application API** qui est unique dans le domaine *azurewebsites.net* . Vous pouvez accepter le nom par défaut proposé par Visual Studio.

    Si vous entrez un nom de quelqu'un d’autre a déjà utilisé, vous voyez un point d’exclamation rouge à droite.

    L’URL de l’application API sera `{API app name}.azurewebsites.net`.

6. Dans la liste déroulante **Groupe de ressources** , cliquez sur **Nouveau**et entrez « ToDoListGroup » ou un autre nom si vous le souhaitez.

    Un groupe de ressources est un ensemble de ressources Azure comme API applications, bases de données, machines virtuelles et ainsi de suite. Pour ce didacticiel, il est préférable de créer un nouveau groupe de ressources, car cela facilite à supprimer en une seule étape toutes les ressources Azure que vous créez pour le didacticiel.

    Cette zone vous permet de sélectionner un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) de existant ou créez-en un nouveau en tapant un nom qui est différent de n’importe quel groupe de ressources existant dans votre abonnement.

7. Cliquez sur le bouton **Nouveau** du **Plan de Service d’application** vers le bas.

    La capture d’écran affiche des exemples de valeurs pour le **Nom de l’application API**, **abonnements**et **Groupe de ressources** --vos valeurs seront différents.

    ![Créer la boîte de dialogue Service de l’application](./media/app-service-api-dotnet-get-started/createas.png)

    Dans les étapes suivantes, vous créez un plan de Service d’application pour le nouveau groupe de ressources. Un plan de Service de l’application spécifie les ressources de calcul fonctionnant sous votre application API. Par exemple, si vous choisissez la couche libre, votre application API s’exécute sur VMs partagés, alors que pour certains niveaux payées Il s’exécute sur les ordinateurs virtuels dédiés. Pour plus d’informations sur les plans de Service d’application, consultez [vue d’ensemble des plans de Service d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

8. Dans la boîte de dialogue **Configurer un Plan de Service App** , entrez « ToDoListPlan » ou un autre nom si vous le souhaitez.

9. Dans la liste déroulante **emplacement** , sélectionnez l’emplacement le plus proche de vous.

    Ce paramètre spécifie le centre de données Azure votre application s’exécutera dans. Choisissez un emplacement proche de vous permet de minimiser la [latence](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

10. Dans le menu déroulant **taille** , cliquez sur **libre**.

    Pour ce didacticiel, le niveau de tarification libre offre des performances suffisantes.

11. Dans la boîte de dialogue **Configurer un Plan de Service App** , cliquez sur **OK**.

    ![Cliquez sur OK dans configurer le Plan de Service d’application](./media/app-service-api-dotnet-get-started/configasp.png)

12. Dans la boîte de dialogue de **Création de Service App** , cliquez sur **créer**.

    ![Cliquez sur Créer dans la boîte de dialogue Créer un Service App](./media/app-service-api-dotnet-get-started/clickcreate.png)

    Visual Studio crée l’application API et un profil de publication qui possède tous les paramètres requis pour l’application de l’API. Puis, il ouvre l’Assistant de **Publication sur le Web** , que vous utiliserez pour déployer le projet.

    L’Assistant de **Publication Web** s’ouvre sur l’onglet **connexion** (voir ci-dessous).

    Sous l’onglet **connexion** , les paramètres de **serveur** et le **nom de Site** point à votre application d’API. Le **nom d’utilisateur** et le **mot de passe** sont les informations d’identification de déploiement Azure crée pour vous. Après le déploiement, Visual Studio ouvre un navigateur vers l' **URL de Destination** (ce qui est le seul objectif de **l’URL de Destination**).  

13. Cliquez sur **suivant**.

    ![Cliquez sur suivant dans l’onglet Connexion de la publication sur le Web](./media/app-service-api-dotnet-get-started/connnext.png)

    L’onglet suivant est l’onglet **paramètres** (voir ci-dessous). Ici, vous pouvez modifier l’onglet de configuration de build pour déployer une version debug pour le [débogage distant](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). L’onglet propose également plusieurs **Options de publication du fichier**:

    * Supprimer des fichiers supplémentaires à la destination
    * Précompiler pendant la publication
    * Exclure les fichiers du dossier App_Data

    Ce didacticiel vous n’ont pas besoin de ces. Pour obtenir des explications détaillées de ce qu’elles font, consultez [Comment : déployer un projet à l’aide d’un seul clic publication Web dans Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

14. Cliquez sur **suivant**.

    ![Cliquez sur suivant dans l’onglet Paramètres de publication sur le Web](./media/app-service-api-dotnet-get-started/settingsnext.png)

    Next est l’onglet **Aperçu** (illustré ci-dessous), qui permet de vous permet de voir quels fichiers vont être copiées à partir de votre projet pour l’application de l’API. Lorsque vous déployez un projet pour une application API que vous avez déjà déployé sur plus haut, seuls les fichiers modifiés sont copiés. Si vous souhaitez afficher une liste de ce que sera copié, vous pouvez cliquer sur le bouton **Démarrer l’aperçu** .

15. Cliquez sur **Publier**.

    ![Cliquez sur Publier dans l’onglet Aperçu de publication sur le Web](./media/app-service-api-dotnet-get-started/clickpublish.png)

    Visual Studio déploie le projet ToDoListDataAPI dans la nouvelle application API. La fenêtre **sortie** connecte le déploiement réussi, et une page « réussi » s’affiche dans une fenêtre de navigateur ouverte vers l’URL de l’application API.

    ![Déploiement de la fenêtre de sortie](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

    ![Nouvelle page d’application a été correctement créée API](./media/app-service-api-dotnet-get-started/appcreated.png)

16. Ajouter « swagger » à l’URL dans la barre d’adresse du navigateur et appuyez sur ENTRÉE. (L’URL est `http://{apiappname}.azurewebsites.net/swagger`.)

    Le navigateur affiche le même Swagger l’interface utilisateur que vous avez vu plus haut, mais il est en cours d’exécution dans le nuage. Essayez la méthode Get, et vous voyez que vous êtes sur les éléments d’action 2 par défaut. Les modifications que vous avez apportées précédemment ont été enregistrées dans la mémoire de l’ordinateur local.

17. Ouvrez le [portail Azure](https://portal.azure.com/).

    Le portail Azure est une interface web pour la gestion des ressources telles que les applications d’API Azure.

18. Cliquez sur **plus de Services > Services de l’application**.

    ![Parcourir les Services d’application](./media/app-service-api-dotnet-get-started/browseas.png)

19. Dans la lame de **Services d’application** , recherchez et cliquez sur votre nouvelle application API. (Dans le portail Azure, fenêtres qui s’ouvrent à droite sont appelés *lames*).

    ![Lame de Services d’application](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

    Deux lames ouvrir. Une blade a une vue d’ensemble de l’application API, et a une longue liste de paramètres que vous pouvez afficher et modifier.

20. Dans la lame de **paramètres** , recherchez la section de **l’API** , puis cliquez sur **Définition de l’API**.

    ![Définition d’API de lame de paramètres](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

    La lame de la **Définition de l’API** vous permet de spécifier l’URL qui renvoie des métadonnées 2.0 de Swagger au format JSON. Lorsque Visual Studio crée l’application API, il définit l’URL de définition d’API à la valeur par défaut Swashbuckle métadonnées générées que vous avez vu précédemment, qui est l’application de l’API de base URL plu `/swagger/docs/v1`.

    ![URL de définition d’API](./media/app-service-api-dotnet-get-started/apidefurl.png)

    Lorsque vous sélectionnez une application API pour générer le code client pour lui, Visual Studio extrait les métadonnées à partir de cette URL.

## <a id="codegen"></a>Générer le code client pour la couche données

Un des avantages de l’intégration Swagger dans les applications Azure API est la génération de code automatique. Classes client généré le rendent plus facile d’écrire du code qui appelle une application API.

Le projet ToDoListAPI a déjà le code client généré, mais dans les étapes suivantes, vous allez supprimer et régénérer pour voir comment effectuer la génération de code.

1. Dans Visual Studio **L’Explorateur de solutions**, dans le projet ToDoListAPI, supprimez le dossier *ToDoListDataAPI* . **Attention : Supprimer uniquement le dossier, et non sur le projet ToDoListDataAPI.**

    ![Supprimer le code client généré](./media/app-service-api-dotnet-get-started/deletecodegen.png)

    Ce dossier a été créé à l’aide du processus de génération de code que vous êtes sur le point de passer par.

2. Cliquez sur le projet ToDoListAPI, puis cliquez sur **Ajouter > reste Client de l’API**.

    ![Ajouter un client de l’API REST dans Visual Studio](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Dans la boîte de dialogue **Ajouter un Client API reste** , cliquez sur **L’URL Swagger**, puis cliquez sur **Sélectionner les actifs Azure**.

    ![Sélectionnez actif Azure](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

4. Dans la boîte de dialogue **Service de l’application** , développez le groupe de ressources, vous utilisez ce didacticiel et sélectionnez votre application API, puis cliquez sur **OK**.

    ![Sélectionnez l’application d’API pour la génération de code](./media/app-service-api-dotnet-get-started/codegenselect.png)

    Notez que lorsque vous revenez à la boîte de dialogue **Ajouter un Client API reste** , la zone de texte a été renseignée avec la définition de l’API valeur de l’URL que vous avez vu précédemment dans le portail.

    ![URL de définition d’API](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

    >[AZURE.TIP] Un autre moyen d’obtenir des métadonnées pour la génération de code est d’entrer l’URL directement au lieu de passer par la boîte de dialogue Parcourir. Ou, si vous souhaitez générer le code client avant de le déployer sur Azure, vous pourrait exécuter le projet API Web localement, accédez à l’URL qui fournit le fichier Swagger de JSON, enregistrez le fichier, puis utiliser l’option **Sélectionner un fichier de métadonnées existant Swagger** .

5. Dans la boîte de dialogue **Ajouter un Client API reste** , cliquez sur **OK**.

    Visual Studio crée un dossier nommé d’après l’application de l’API et génère des classes de client.

    ![Fichiers de code de client générée](./media/app-service-api-dotnet-get-started/codegenfiles.png)

6. Dans le projet ToDoListAPI, ouvrez *Controllers\ToDoListController.cs* pour visualiser le code au niveau de la ligne 40 qui appelle l’API en utilisant le client généré.

    L’extrait de code suivant montre comment le code instancie l’objet client appelle la méthode Get.

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
            return client;
        }

        public async Task<IEnumerable<ToDoItem>> Get()
        {
            using (var client = NewDataAPIClient())
            {
                var results = await client.ToDoList.GetByOwnerAsync(owner);
                return results.Select(m => new ToDoItem
                {
                    Description = m.Description,
                    ID = (int)m.ID,
                    Owner = m.Owner
                });
            }
        }

    Le paramètre de constructeur obtient l’URL du point de terminaison à partir de la `toDoListDataAPIURL` paramètre d’application. Dans le fichier Web.config, cette valeur est définie à IIS Express URL locale du projet API afin que vous pouvez exécuter l’application localement. Si vous omettez le paramètre de constructeur, le point de terminaison par défaut est l’URL que vous avez généré le code.

7. Votre classe de client est généré avec un nom différent selon le nom de votre application d’API ; Modifiez le code dans *Controllers\ToDoListController.cs* afin que le nom de type correspond à ce qui a été généré dans votre projet. Par exemple, si vous avez nommé votre API App ToDoListDataAPI071316, vous devez remplacer ce code :

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));

à cet effet :

        private static ToDoListDataAPI071316 NewDataAPIClient()
        {
            var client = new ToDoListDataAPI071316(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));


## <a name="create-an-api-app-to-host-the-middle-tier"></a>Créer une application API pour héberger la couche intermédiaire

Antérieure vous [créé l’application API de couche données et déployé le code il](#createapiapp).  Maintenant, vous suivez la même procédure pour l’application d’API de niveau intermédiaire.

1. Dans l' **Explorateur de solutions**, cliquez sur la couche intermédiaire ToDoListAPI (pas la couche de données ToDoListDataAPI) du projet, puis cliquez sur **Publier**.

    ![Cliquez sur Publier dans Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu2.png)

2.  Dans l’onglet **profil** de l’Assistant **Publication sur le Web** , cliquez sur **Service d’application Microsoft Azure**.

3. Dans la boîte de dialogue **Service de l’application** , cliquez sur **Nouveau**.

4. Dans l’onglet **hébergement** de la boîte de dialogue de **Création de Service App** , acceptez le **Nom de l’application API** par défaut ou entrez un nom qui est unique dans le domaine *azurewebsites.net* .

5. Choisissez l' Azure **abonnement** que vous avez utilisé.

6. Dans la liste déroulante **Groupe de ressources** , choisissez le même groupe de ressources que vous avez créé précédemment.

7. Dans la liste déroulante **Plan de Service d’application** , choisissez le même plan que vous avez créé précédemment. Il utilise par défaut cette valeur.

8. Cliquez sur **créer**.

    Visual Studio crée l’application API crée un profil de publication pour celui-ci et affiche l’étape de **connexion** de l’Assistant **Publication sur le Web** .

9.  Dans l’étape de **connexion** de l’Assistant **Publication sur le Web** , cliquez sur **Publier**.

    Visual Studio déploie le projet ToDoListAPI pour la nouvelle application API et ouvre un navigateur vers l’URL de l’application API. La page « créée avec succès » s’affiche.

## <a name="configure-the-middle-tier-to-call-the-data-tier"></a>Configurer le niveau intermédiaire pour appeler la couche données

Si vous avez appelé l’application de niveau intermédiaire API maintenant, il serait essayez d’appeler la couche de données à l’aide de l’URL de l’hôte local qui est toujours dans le fichier Web.config. Dans cette section vous permet d’entrer l’URL d’application données couche API dans un paramètre d’environnement dans l’API d’application de couche intermédiaire. Lorsque le code dans l’application de niveau intermédiaire API récupère le paramètre URL de couche données, le paramètre d’environnement remplace ce qui est dans le fichier Web.config.

1. Accédez au [portail Azure](https://portal.azure.com/)et accédez à la lame **d’Application de l’API** pour l’application de l’API que vous avez créé pour héberger le projet TodoListAPI (niveau intermédiaire).

2. De lames de **paramètres** de l’application API, cliquez sur **paramètres de l’Application**.

3. Lame de **Paramètres d’Application** de l’application API, faites défiler jusqu'à la section **paramètres d’application** et ajouter la clé et la valeur suivantes. La valeur correspond à l’URL de l’application API premier que vous avez publié dans ce didacticiel.

  	| **Clé** | toDoListDataAPIURL |
  	|---|---|
  	| **Valeur** | nom de https://{Your données couche API app} .azurewebsites .net |
  	| **Exemple** | https://todolistdataapi.azurewebsites.NET |

4. Cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer pour les paramètres de l’application](./media/app-service-api-dotnet-get-started/asinportal.png)

    Lorsque le code s’exécute dans Azure, cette valeur remplace maintenant l’URL de l’hôte local qui se trouve dans le fichier Web.config.

## <a name="test"></a>Test

1. Dans une fenêtre du navigateur, accédez à l’URL de la nouvelle API application de niveau intermédiaire que vous venez de créer pour ToDoListAPI. Vous pouvez y accéder en cliquant sur l’URL dans la lame principale de l’application de l’API dans le portail.

2. Ajouter « swagger » à l’URL dans la barre d’adresse du navigateur et appuyez sur ENTRÉE. (L’URL est `http://{apiappname}.azurewebsites.net/swagger`.)

    Le navigateur affiche le même Swagger l’interface utilisateur que vous avez vu précédemment pour ToDoListDataAPI, mais `owner` n’est pas un champ obligatoire pour l’opération, car l’application de niveau intermédiaire API envoie cette valeur à l’application d’API de couche données pour vous. (Lorsque vous effectuez les didacticiels de l’authentification, le niveau intermédiaire enverra les ID utilisateur réel le `owner` paramètre ; pour maintenant il est coder en dur un astérisque.)

3. Essayez la méthode Get et d’autres méthodes pour valider que l’application d’API intermédiaire appelle avec succès l’application API de couche données.

    ![Méthode d’obtenir de l’interface utilisateur swagger](./media/app-service-api-dotnet-get-started/midtierget.png)

## <a name="troubleshooting"></a>Résolution des problèmes

Dans le cas où vous rencontrez un problème, tout au long de ce didacticiel ici sont quelques idées de dépannage :

* Assurez-vous que vous utilisez la version la plus récente du [Kit de développement logiciel Azure pour .NET](http://go.microsoft.com/fwlink/?linkid=518003).

* Deux des noms de projet sont similaire (ToDoListAPI, ToDoListDataAPI). Si tout vous semble ne pas comme décrit dans les instructions lorsque vous travaillez avec un projet, assurez-vous que vous avez ouvert le projet approprié.

* Si vous êtes sur un réseau d’entreprise et que vous essayez de déployer le service d’application Azure à travers un pare-feu, assurez-vous que les ports 443 et 8172 sont ouverts pour le déploiement Web. Si vous ne pouvez pas ouvrir ces ports, vous pouvez utiliser d’autres méthodes de déploiement.  Consultez [déploiement de votre application au Service d’application Azure](../app-service-web/web-sites-deploy.md).

* Erreurs « noms d’itinéraire doivent être uniques » : vous pouvez obtenir ces si vous accidentellement déployez le projet incorrect pour une application API, puis déployez ultérieurement pour elle. Pour résoudre ce problème, le projet approprié à l’application de l’API et sur l’onglet **paramètres** de l’Assistant de **Publication Web** de redéploiement sélectionnez **Supprimer des fichiers supplémentaires à la destination**.

Une fois que votre application API d’ASP.NET en cours d’exécution dans le Service d’application Azure, vous souhaiterez en savoir plus sur les fonctionnalités de Visual Studio qui simplifient la résolution des problèmes. Pour plus d’informations sur la journalisation, le débogage distant et bien plus encore, voir [résolution des problèmes de Service d’application Azure des applications dans Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Étapes suivantes

Vous avez vu comment déployer des projets d’API Web existants pour les applications d’API, générer le code client pour les applications d’API et utiliser les API des applications à partir de clients .NET. Le didacticiel suivant de cette série indique comment [utiliser les CORS pour consommer les API des applications à partir de clients de JavaScript](app-service-api-cors-consume-javascript.md).

Pour plus d’informations sur la génération de code client, consultez le référentiel [Azure/AutoRest](https://github.com/azure/autorest) sur GitHub.com. Pour une aide sur des problèmes en utilisant le client généré, ouvrez un [problème dans le référentiel de AutoRest](https://github.com/azure/autorest/issues).

Si vous souhaitez créer de nouveaux projets d’application API à partir de zéro, utilisez le modèle **Application d’API Azure** .

![Modèle API application dans Visual Studio](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

Le modèle de projet **Application d’API Azure** revient à choisir le **vide** ASP.NET 4.5.2 modèle, cliquez sur la case à cocher pour ajouter la prise en charge de l’API Web et l’installation du package Swashbuckle NuGet. En outre, le modèle ajoute du code de configuration de Swashbuckle conçu pour empêcher la création de doublons Swagger opération ID. Une fois que vous avez créé un projet d’application de l’API, vous pouvez la déployer vers une application API la même manière que vous l’avez vu dans ce didacticiel.
