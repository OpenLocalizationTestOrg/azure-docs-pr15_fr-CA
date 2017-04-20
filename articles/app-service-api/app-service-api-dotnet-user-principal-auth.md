<properties
    pageTitle="Authentification de l’utilisateur pour les applications d’API dans le Service d’application Azure | Microsoft Azure"
    description="Apprenez à protéger une application API dans le Service d’application Azure en autorisant l’accès uniquement aux utilisateurs authentifiés."
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

# <a name="user-authentication-for-api-apps-in-azure-app-service"></a>Authentification de l’utilisateur pour les applications d’API dans le Service d’application Azure

## <a name="overview"></a>Vue d’ensemble

Cet article explique comment faire pour protéger une application Azure API afin que seuls les utilisateurs authentifiés peuvent appeler. Cet article suppose que vous avez lu la [vue d’ensemble de l’authentification de Service d’application Azure](../app-service/app-service-authentication-overview.md).

Vous apprendrez à :

* Comment configurer un fournisseur d’authentification, avec les détails pour Azure Active Directory (AD Azure).
* L’utilisation d’une application API protégée à l’aide de l' [Active Directory d’authentification Library (ADAL) pour JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

L’article contient deux sections :

* La section [comment configurer l’authentification des utilisateurs dans le Service d’application Azure](#authconfig) explique la procédure pour configurer l’authentification utilisateur pour n’importe quelle application API en général et s’applique également à toutes les infrastructures sont pris en charge par le Service d’application, y compris .NET, Node.js et Java.

* Vous guide dans la configuration d’un exemple d’application avec un .NET à partir de la section de [poursuivre les didacticiels d’applications .NET à l’API](#tutorialstart) , les guides de l’article back-end et un avant de AngularJS de fin afin qu’elle utilise Azure Active Directory pour l’authentification utilisateur. 

## <a id="authconfig"></a>Comment faire pour configurer l’authentification des utilisateurs dans le Service d’application Azure

Cette section fournit des instructions générales qui s’appliquent à n’importe quelle application API. Pour connaître les étapes spécifiques à l’exemple d’application .NET de liste à faire, accédez à [poursuivre les didacticiels .NET mise en route](#tutorialstart).

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la lame de **paramètres** de l’application de l’API que vous souhaitez protéger, recherchez la section **fonctionnalités** , puis cliquez sur **l’authentification / autorisation**.

    ![Azure portal d’authentification/autorisation.](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Dans la **l’authentification / autorisation** lame, cliquez **sur**.

4. Sélectionnez une des options de la liste déroulante **Action à entreprendre lors de la demande n’est pas authentifié** .

    * Si vous ne souhaitez que les appels authentifiés à accéder à votre application d’API, choisissez une des options **d’ouverture de session...** . Cette option vous permet de protéger l’application API sans écrire de code qui s’exécute dans il.

    * Si vous souhaitez que tous les appels pour accéder à votre application d’API, choisissez **Autoriser les requêtes (aucune action)**. Vous pouvez utiliser cette option pour diriger les appelants non authentifiés à un choix de fournisseurs d’authentification. Avec cette option, vous devez écrire du code pour gérer l’autorisation.

    Pour plus d’informations, consultez [authentification et autorisation pour les applications d’API dans le Service d’application Azure](app-service-api-authentication.md#multiple-protection-options).

5. Sélectionnez une ou plusieurs des **Fournisseurs d’authentification**.

    L’image montre les choix qui nécessite de tous les appelants doivent être authentifiées par AD Azure.
 
    ![Lame de l’authentification/autorisation portail Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

    Lorsque vous choisissez un fournisseur d’authentification, le portail affiche une lame de configuration pour ce fournisseur. 

    Pour obtenir des instructions détaillées sur la façon de configurer les lames de configuration du fournisseur d’authentification, consultez [comment configurer votre application de Service de l’application pour utiliser des connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). (Le lien mène à un article sur l’annonce Azure, mais l’article lui-même contient des onglets définissant des liens vers les articles pour les autres fournisseurs d’authentification).

7. Lorsque vous avez terminé avec la lame de configuration du fournisseur d’authentification, cliquez sur **OK**.

7. Dans la **l’authentification / autorisation** lame, cliquez sur **Enregistrer**.

Dans ce cas, application Service authentifie tous les appels d’API avant qu’ils atteignent l’application API. Les services d’authentification fonctionnent de la même pour toutes les langues prises en charge du service de l’application, y compris .NET, Node.js et Java. 

Pour effectuer des appels d’API authentifiés, l’appelant inclut le jeton de support du fournisseur d’authentification OAuth 2.0 dans l’en-tête d’autorisation de demandes HTTP. Le jeton peut être acquis à l’aide du Kit de développement logiciel du fournisseur d’authentification.

## <a id="tutorialstart"></a>Poursuivre les didacticiels d’applications .NET à l’API

Si vous suivez les didacticiels Node.js ou Java pour les applications d’API, passez directement à l’article suivant, [authentification principal du service pour les applications d’API](app-service-api-dotnet-service-principal-auth.md). 

Si vous suivez la série de didacticiels .NET pour les applications d’API et avez déjà déployé l’exemple d’application comme indiqué dans les [premier](app-service-api-dotnet-get-started.md) et le [deuxième](app-service-api-cors-consume-javascript.md) didacticiels, passez à la section [configurer l’authentification dans le Service d’application et Azure AD](#azureauth) .

Si vous souhaitez suivre ce didacticiel sans passer par les didacticiels de la première et la deuxième, effectuez les étapes suivantes qui expliquent comment démarrer à l’aide d’un processus automatisé pour déployer l’exemple d’application.

>[AZURE.NOTE] Les étapes suivantes vous obtenir au même point de départ que si vous avez effectué les deux premiers didacticiels, à une exception près : Visual Studio ne savez déjà quel web app ou une application API que chaque projet obtient déployé sur. Cela signifie que vous n’aurez pas des instructions exactes de ce didacticiel qui expliquent comment déployer sur les cibles de droite. Si vous n’êtes pas à l’aise avec évertuent à suivre les étapes de déploiement sur votre propre, il est préférable de suivre la série de didacticiels à partir du [premier didacticiel](app-service-api-dotnet-get-started.md) que de commencer avec ce processus de déploiement automatisé.

1. Assurez-vous que vous disposez bien de tous les composants requis répertoriés dans le [premier didacticiel](app-service-api-dotnet-get-started.md). En plus des composants requis répertoriés, ces didacticiels d’authentification supposent que vous avez travaillé avec des applications de Service d’application web et les applications d’API dans Visual Studio et le portail Azure.

2. Cliquez sur le bouton **déployer vers Azure** dans le [fichier de liste des tâches exemple référentiel](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md) pour déployer les applications d’API et de l’application web. Prenez note du groupe ressources Azure qui est créé, comme vous pouvez l’utiliser ultérieurement pour rechercher les web app et les noms d’application API.
 
3. Téléchargez ou cloner le [référentiel d’exemple de liste des tâches](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) pour obtenir le code que vous allez utiliser localement dans Visual Studio.

## <a id="azureauth"></a>Configurer l’authentification dans le Service d’application et Azure AD

Vous avez maintenant l’application en cours d’exécution dans le Service d’application Azure sans exiger que les utilisateurs authentifiés. Dans cette section, vous ajoutez l’authentification en effectuant les tâches suivantes :

* Configurer le Service d’application pour exiger l’authentification Azure Active Directory (AD Azure) pour appeler l’application de niveau intermédiaire API.
* Créer une application Azure AD.
* Configurer l’application Azure AD pour envoyer le jeton de support après ouverture de session à la fin avant de AngularJS. 

Si vous rencontrez des problèmes en suivant les instructions du didacticiels, consultez la section [dépannage](#troubleshooting) à la fin du didacticiel. 
 
### <a name="configure-authentication-for-the-middle-tier-api-app"></a>Configurer l’authentification pour l’application d’API de niveau intermédiaire

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la lame de **paramètres** de l’application de l’API que vous avez créé pour le projet ToDoListAPI, recherchez la section **fonctionnalités** , puis cliquez sur **l’authentification / autorisation**.

    ![Azure portal d’authentification/autorisation.](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Dans la **l’authentification / autorisation** lame, cliquez **sur**.

4. Dans la liste déroulante **Action à entreprendre lors de la demande n’est pas authentifié** , sélectionnez **se connecter avec Azure Active Directory**.

    Cette option garantit qu’aucune demande unauathenticated n’atteindra l’application API. 

5. **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

    ![Lame de l’authentification/autorisation portail Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Dans la lame **Azure les paramètres Active Directory** , cliquez sur **Express**

    ![Option de l’authentification/autorisation Express de portail Azure](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

    Avec l’option **Express** , le Service d’application peut créer automatiquement une application Azure AD dans votre annonce Azure [locataire](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). 

    Vous n’êtes pas obligé de créer un client, car chaque compte Azure a automatiquement un.

7. Sous **mode d’administration**, cliquez sur **Créer une nouvelle application d’Active Directory** s’il n’est pas déjà sélectionné et notez la valeur qui se trouve dans la zone de texte de **Créer une application** ; vous allez rechercher ultérieurement cette application DAS dans Azure portal classique.

    ![Paramètres du portail Azure Azure AD](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

    Azure va créer automatiquement une application Azure AD portant le nom indiqué dans votre client AD Azure. Par défaut, l’application Azure AD est porter le même nom que l’application de l’API. Si vous préférez, vous pouvez entrer un autre nom.
 
7. Cliquez sur **OK**.

7. Dans la **l’authentification / autorisation** lame, cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/app-service-api-dotnet-user-principal-auth/authsave.png)

Seuls les utilisateurs de vos clients AD Azure peuvent à présent appeler l’application API.

### <a name="optional-test-the-api-app"></a>Facultatif : Test de l’application API

1. Dans un navigateur, accédez à l’URL de l’application API : la lame **API application** dans Azure portal, cliquez sur le lien sous **l’URL**.  

    Vous êtes redirigé vers un écran de connexion car les demandes non authentifiées ne sont pas autorisés à accéder à l’application de l’API.

    Si votre navigateur affiche la page « créée avec succès », le navigateur peut déjà être connecté--dans ce cas, ouvrez une fenêtre InPrivate ou Incognito et atteindre l’URL de l’application de l’API.

2. Ouvrez une session à l’aide des informations d’identification d’un utilisateur dans votre client AD Azure.

    Lorsque vous êtes connecté, la page « créée avec succès » s’affiche dans le navigateur.

9. Fermez le navigateur.

### <a name="configure-the-azure-ad-application"></a>Configurer l’application Azure AD

Lorsque vous avez configuré l’authentification Active Directory Azure, Service de l’application a créé une application Azure AD pour vous. Par défaut, la nouvelle annonce Azure application a été configurée pour fournir le jeton au porteur à l’URL de l’application de l’API. Dans cette section, vous configurez l’application Azure AD pour fournir le jeton de support à l’extrémité avant de la AngularJS à la place du directement à l’application de niveau intermédiaire API. AngularJS frontal envoie le jeton à l’application de l’API lorsqu’il appelle l’application API.

>[AZURE.NOTE] Conserver le processus simple que possible, ce didacticiel utilise une seule annonce Azure application front-end et le milieu de niveau application d’API. Une autre option consiste à utiliser deux applications Azure AD. Dans ce cas, vous devrez autoriser de la partie frontale AD Azure application à appeler application d’Azure AD de la couche intermédiaire. Cette approche de plusieurs application permet d’obtenir un contrôle plus précis sur les autorisations accordées à chaque niveau.

11. Dans [Azure portal classique](https://manage.windowsazure.com/), passez à **Azure Active Directory**.

    Vous devez utiliser le portail classique car certains paramètres Azure Active Directory auxquels vous devez accéder à ne sont pas encore disponibles dans le portail Azure en cours.

12. Dans l’onglet **répertoire** , cliquez sur vos clients de DAS.

    ![Annonce Azure Portal classique](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Cliquez sur **Applications > Applications propriétaire de ma société**, puis cliquez sur la case à cocher.

    Vous devrez peut-être également actualiser la page pour afficher la nouvelle application.

15. Dans la liste des applications, cliquez sur le nom de l’objet Azure créés pour vous lorsque vous avez activé l’authentification pour votre application d’API.

    ![Onglet de publicité Applications Azure](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Cliquez sur **configurer**.

    ![Onglet de configuration d’Active Directory Azure](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. Définir **l’URL de connexion** à l’URL de votre application web AngularJS, aucune barre oblique de fin.

    Par exemple : https://todolistangular.azurewebsites.net

    ![Ouverture de session d’URL](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. Définir **l’URL de la réponse** à l’URL de votre application web, aucune barre oblique de fin.

    Par exemple : https://todolistsangular.azurewebsites.net

16. Cliquez sur **Enregistrer**.

    ![URL de la réponse](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. En bas de la page, cliquez sur **manifeste de gérer > manifeste de téléchargement**.

    ![Télécharger le manifeste](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. Téléchargez le fichier vers un emplacement où vous pouvez les modifier.

16. Dans le fichier de manifeste téléchargé, recherchez le `oauth2AllowImplicitFlow` propriété. Modifiez la valeur de cette propriété à partir de `false` à `true`, puis enregistrez le fichier.

    Ce paramètre est requis pour l’accès à partir d’une application de page unique de JavaScript. Il permet le jeton de support Oauth 2.0 à retourner dans le fragment d’URL.

16. Cliquez sur **manifeste de gérer > manifeste de téléchargement**et de télécharger le fichier mis à jour à l’étape précédente.

    ![Télécharger le manifeste](./media/app-service-api-dotnet-user-principal-auth/uploadmanifest.png)

17. Copiez la valeur de **l’ID de Client** et l’enregistrer dans un endroit que vous pouvez l’obtenir à partir d’une version ultérieure.

## <a name="configure-the-todolistangular-project-to-use-authentication"></a>Configurer le projet pour utiliser l’authentification ToDoListAngular

Dans cette section, vous modifiez le frontal AngularJS afin qu’il utilise Active Directory d’authentification Library (ADAL) pour JS à acquérir un jeton de support pour l’utilisateur connecté à partir d’AD Azure. Le code inclut le jeton dans les demandes HTTP envoyées à la couche intermédiaire, comme illustré dans le diagramme suivant. 

![Schéma d’authentification utilisateur](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

Apportez les modifications suivantes aux fichiers dans le projet ToDoListAngular.

1. Ouvrez le fichier *index.html* .

2. Ne commentez pas les lignes qui font référence à la bibliothèque d’authentification répertoire actif (ADAL) pour les scripts JS.

        <script src="app/scripts/adal.js"></script>
        <script src="app/scripts/adal-angular.js"></script>

1. Ouvrez le fichier *app/scripts/app.js* .

2. Commentez le bloc de code marqué pour « sans authentification » et supprimez le bloc de code marqué pour « avec authentification ».

    Cette modification fait référence le fournisseur d’authentification de ADAL JS et fournit les paramètres de configuration. Dans les étapes suivantes, vous définissez les valeurs de configuration de votre application d’API et votre application d’Azure AD.

8. Dans le code qui définit les `endpoints` variable, définissez l’URL de l’API à l’URL de l’application API que vous créées pour le projet ToDoListAPI et la valeur de l’ID d’application Azure AD à l’ID du client que vous avez copié à partir du portail classique Azure.

    Le code étant maintenant identique à l’exemple suivant.

        var endpoints = {
            "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
        };

9. Dans l’appel à `adalProvider.init`, définissez `tenant` pour le nom de votre client et de `clientId` à la même valeur que vous avez utilisé à l’étape précédente.

    Le code étant maintenant identique à l’exemple suivant.

        adalProvider.init(
            {
                instance: 'https://login.microsoftonline.com/', 
                tenant: 'contoso.onmicrosoft.com',
                clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
                extraQueryParameter: 'nux=1',
                endpoints: endpoints
            },
            $httpProvider
            );

    Ces modifications à `app.js` spécifier que le code appelant et l’appelé API sont dans la même application Azure AD.

1. Ouvrez le fichier *app/scripts/homeCtrl.js* .

2. Commentez le bloc de code marqué pour « sans authentification » et supprimez le bloc de code marqué pour « avec authentification ».

1. Ouvrez le fichier *app/scripts/indexCtrl.js* .

2. Commentez le bloc de code marqué pour « sans authentification » et supprimez le bloc de code marqué pour « avec authentification ».

### <a name="deploy-the-todolistangular-project-to-azure"></a>Déployer le projet de ToDoListAngular vers Azure

8. Dans l' **Explorateur de solutions**, cliquez sur le projet ToDoListAngular, puis cliquez sur **Publier**.

9. Cliquez sur **Publier**.

    Visual Studio déploie le projet et ouvre un navigateur vers l’URL de base de l’application web. Vous verrez une page 403 erreur, ce qui est normale pour une tentative d’accéder à une URL de base d’API Web depuis un navigateur.

    Vous devrez apporter une modification à l’application d’API intermédiaire avant de tester l’application.

10. Fermez le navigateur.

## <a name="configure-the-todolistapi-project-to-use-authentication"></a>Configurer le projet pour utiliser l’authentification ToDoListAPI

Actuellement le projet ToDoListAPI envoie « * » comme le `owner` valeur à ToDoListDataAPI. Dans cette section, vous modifiez le code pour envoyer l’ID de l’utilisateur connecté.

Apportez les modifications suivantes dans le projet ToDoListAPI.

1. Ouvrez le fichier *Controllers/ToDoListController.cs* et supprimez les commentaires de la ligne de chaque méthode d’action qui définit `owner` à la publicité Azure `NameIdentifier` la valeur de revendication. Par exemple :

        owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

    **Important**: ne supprimez les commentaires de code dans la `ToDoListDataAPI` méthode ; Vous procéderez ultérieurement pour le didacticiel d’authentification principal de service.

### <a name="deploy-the-todolistapi-project-to-azure"></a>Déployer le projet de ToDoListAPI vers Azure

8. Dans l' **Explorateur de solutions**, cliquez sur le projet ToDoListAPI, puis cliquez sur **Publier**.

9. Cliquez sur **Publier**.

    Visual Studio déploie le projet et ouvre un navigateur vers l’URL de base de l’application de l’API.

10. Fermez le navigateur.

### <a name="test-the-application"></a>Test de l’application

9. Accédez à l’URL de l’application web, **à l’aide de HTTPS, non HTTP**.

8. Cliquez sur l’onglet de **Liste des tâches** .

    Vous êtes invité à ouvrir une session.

9. Connectez-vous avec les informations d’identification d’un utilisateur dans votre client de DAS.

10. La page de **Liste des tâches** s’affiche.

    ![À la page de liste des tâches](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

    Aucun élément d’action n’est affichées car jusqu'à présent qu’ils ont tous été propriétaire « * ». Maintenant le niveau intermédiaire demande les éléments de l’utilisateur connecté, et aucun n’a encore été créé.

11. Ajouter de nouveaux éléments de tâches pour vérifier que l’application fonctionne.

12. Dans une autre fenêtre de navigateur, accédez à l’URL de l’interface utilisateur de Swagger pour l’application de l’API de ToDoListDataAPI et cliquez sur **liste des tâches > obtenir**. Entrez un astérisque pour le `owner` paramètre, puis cliquez sur **tester**.

    La réponse indique que les nouveaux éléments de tâches ont les ID d’utilisateur AD Azure dans la propriété propriétaire.

    ![ID du propriétaire de la réponse de JSON](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## <a name="building-the-projects-from-scratch"></a>Création de projets à partir de zéro

Les deux projets d’API Web ont été créés en utilisant le modèle de projet **Application d’API Azure** et en remplaçant le contrôleur de valeurs par défaut avec un contrôleur de la liste des tâches. 

Pour plus d’informations sur la création d’une application de page unique de AngularJS avec un back-end Web API 2, reportez-vous à la section [mains sur Lab : créer une Application de Page unique (SPA) avec l’API de Web ASP.NET et Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Pour plus d’informations sur la façon d’ajouter du code d’authentification AD Azure, consultez les ressources suivantes :

* [Sécurisation des applications de Page AngularJS simple avec AD Azure](../active-directory/active-directory-devquickstarts-angular.md).
* [Introduction ADAL JS v1](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## <a name="troubleshooting"></a>Résolution des problèmes

[AZURE.INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* Assurez-vous que vous ne confondez pas ToDoListAPI (niveau intermédiaire) et ToDoListDataAPI (couche de données). Par exemple, vérifiez que vous avez ajouté l’authentification à l’application de niveau intermédiaire API, pas la couche de données. 
* Assurez-vous que le code source de AngularJS fait référence à l’URL d’application API de niveau intermédiaire (ToDoListAPI, pas ToDoListDataAPI) et la bonne Azure ID de client AD. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment utiliser l’authentification de Service d’application pour une application API et l’appel de l’application API à l’aide de la bibliothèque ADAL JS. Dans l’étape suivante du didacticiel, vous allez apprendre comment [sécuriser l’accès à votre application d’API pour les scénarios de service à service](app-service-api-dotnet-service-principal-auth.md).

