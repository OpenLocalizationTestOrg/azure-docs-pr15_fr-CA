<properties
    pageTitle="Mise à niveau à partir de Services mobiles au Service d’application Azure - Node.js"
    description="Découvrez comment facilement mettre à niveau votre application de Services mobiles à une application de Service Mobile App"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Mise à niveau de votre Service Mobile de Azure Node.js existant au Service de l’application

Service d’application Mobile est une nouvelle façon de générer des applications mobiles à l’aide de Microsoft Azure. Pour plus d’informations, consultez [Quelles sont les applications Mobile ?].

Cette rubrique décrit comment mettre à niveau une application back-end Node.js Azure Mobile Services vers une nouvelle App Service Mobile Apps. Lorsque vous effectuez cette mise à niveau, votre application Mobile Services existante peut continuer à fonctionner.  Si vous devez mettre à niveau une application back-end de Node.js, reportez-vous à [mise à niveau de vos Services mobiles de .NET](./app-service-mobile-net-upgrading-from-mobile-services.md).

Lorsqu’un back-end mobile est mis à niveau vers le Service d’application Azure, il a accès à toutes les fonctionnalités de Service de l’application et sont facturés en fonction de la [tarification Service de l’application], pas les Services mobiles de tarification.

## <a name="migrate-vs-upgrade"></a>Migration et mise à niveau

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Il est recommandé d' [effectuer une migration](app-service-mobile-migrating-from-mobile-services.md) avant de passer par une mise à niveau. De cette façon, vous pouvez placer les deux versions de votre application sur le même Plan de Service application et entraînent des frais supplémentaires.

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Améliorations de Node.js d’applications Mobile server SDK

Mise à niveau vers le nouveau [SDK de Apps Mobile](https://www.npmjs.com/package/azure-mobile-apps) fournit de nombreuses améliorations, notamment :

- Basé sur [framework de Express](http://expressjs.com/en/index.html), le nouveau SDK de nœud est léger et conçu pour suivre le rythme des nouvelles versions de nœud comme ils sortent. Vous pouvez personnaliser le comportement des applications avec le middleware d’Express.

- Amélioration significative des performances par rapport au Kit de développement de Services mobiles.

- Vous pouvez désormais héberger un site Web avec votre back-end mobile ; de la même façon, il est facile d’ajouter le Kit de développement Mobile Azure à n’importe quelle application express.v4 existant.

- Conçu pour le développement multiplateforme et local, le Kit de développement des applications mobiles peuvent être développé et s’exécutent localement sur les plates-formes Windows, Linux et OSX. Il est désormais facile à utiliser nœud développement des techniques courantes telles que l’exécution de tests [Mocha](https://mochajs.org/) avant le déploiement.

## <a name="overview"></a>Présentation de mise à niveau de base

Pour vous aider dans la mise à niveau d’un back-end Node.js, Service d’application Azure a fourni un package de compatibilité.  Après la mise à niveau, vous aurez un site niew qui peut être déployé vers un nouveau site de Service d’application.

Le client des Services de Mobile SDK sont **pas** compatible avec le nouveau serveur d’applications Mobile SDK. Afin d’assurer la continuité de service pour votre application, vous ne devez pas publier des modifications sur un site actuellement desservant des clients publiés. Au lieu de cela, vous devez créer une nouvelle application mobile qui sert un doublon. Vous pouvez placer cette application sur le même plan de Service de l’application pour éviter de supporter des charges financières.

Vous aurez alors deux versions de l’application : reste la même et sert publié des applications dans la nature et l’autre qui vous pouvez ensuite mettre à niveau et la cible avec une nouvelle version du client. Vous pouvez déplacer et à votre rythme à tester votre code, mais vous devez vous assurer que les correctifs de bogues que vous rendre appliquées à la fois. Lorsque vous vous sentez le nombre désiré de client applications dans la nature ont mis à jour vers la dernière version, supprimer l’application migrée d’origine si vous le souhaitez. Il n’entraînent des coûts monétaires supplémentaires, si hébergé dans le même plan de Service d’application que votre application Mobile.

Le plan complet pour le processus de mise à niveau est la suivante :

1. Téléchargez votre Service Mobile de Azure (migré) existant.
2. Convertir le projet pour une application de Mobile Azure en utilisant le package de compatibilité.
3. Corrigez les éventuelles différences (par exemple, les paramètres d’authentification).
4. Déployer votre projet de Mobile d’Azure App converti vers un nouveau Service d’application.
4. Lancer une nouvelle version de votre application client qui utilisent la nouvelle application Mobile.
5. (Facultatif) Supprimer votre application de service mobile ayant migré d’origine.

Suppression peut se produire lorsque vous ne voyez pas le trafic sur votre service mobile ayant migré d’origine.

## <a name="install-npm-package"></a>Installer les composants requis

Vous devez installer [Node] sur votre ordinateur local.  Vous devez également installer le package de compatibilité.  Une fois que le nœud est installé, vous pouvez exécuter la commande suivante d’une nouvelle commande ou d’invite PowerShell :

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a>Obtenir vos Scripts Azure Services mobiles

- Connectez-vous au [portail Azure].
- À l’aide de **Toutes les ressources** ou les **Services d’application**, de trouver votre site de Services mobiles.
- Dans le site, cliquez sur **Outils** -> **Kudu** -> **Aller** pour ouvrir le site Kudu.
- Cliquez sur la **Console de débogage** -> **PowerShell** pour ouvrir la console de débogage.
- Accédez à `site/wwwroot/App_Data/config` en cliquant sur chaque répertoire de la tour
- Cliquez sur l’icône de téléchargement suivant pour le `scripts` répertoire.

Cela va télécharger les scripts au format ZIP.  Créez un répertoire sur votre ordinateur local et décompresser la `scripts.ZIP` le fichier dans le répertoire.  Cette opération va créer un `scripts` répertoire.

## <a name="scaffold-app"></a>Le nouveau back-end d’Azure Mobile applications d’ECHAFAUDAGE

Exécutez la commande suivante à partir du répertoire contenant le répertoire scripts :

```scaffold-mobile-app scripts out```

Cette opération va créer un back-end des applications de Mobile Azure avec génération de modèles dans le `out` répertoire.  Bien que non obligatoire, il est judicieux de vérifier les `out` répertoire dans un référentiel de code source de votre choix.

## <a name="deploy-ama-app"></a>Déployer votre back-end les applications Azure Mobile

Au cours du déploiement, vous devez effectuer les opérations suivantes :

1. Créez une nouvelle application Mobile dans le [Portail Azure].
2. Exécuter le `createViews.sql` script sur votre base de données connectée.
3. Lier à la base de données qui est lié à votre Service Mobile à votre nouveau Service d’application.
4. Lier les autres ressources (telles que les concentrateurs de Notification) pour le nouveau Service d’application.
5. Déployer le code généré vers votre nouveau site.

### <a name="create-a-new-mobile-app"></a>Créer une nouvelle application Mobile

1. Ouvrez une session sur le [portail Azure].

2. Cliquez sur **+ Nouveau** > **Web + Mobile** > **Mobile App**, puis donnez un nom à votre back-end de l’application Mobile.

3. Pour le **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un nouveau (à l’aide du même nom que votre application.) 
 
    Vous pouvez sélectionner un autre plan de Service de l’application ou créer un nouveau. Pour plus d’informations sur les Services d’application plans et comment créer un plan dans un autre système de tarification de niveau et dans votre emplacement de votre choix, consultez [présentation approfondie des plans de Service d’application Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Pour le **service d’application**, le plan par défaut (dans la [couche Standard](https://azure.microsoft.com/pricing/details/app-service/)) est sélectionné. Vous pouvez également sélectionner un autre plan, ou [créer un nouveau](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Du plan Service de l’application déterminent l' [emplacement, de fonctionnalités, de coûts et les ressources de calcul](https://azure.microsoft.com/pricing/details/app-service/) associés à votre application. 

    Après avoir décidé du plan, cliquez sur **créer**. Cette opération crée le back-end de l’application Mobile. 


### <a name="run-createviewssql"></a>Exécutez CreateViews.SQL

L’application avec génération de modèles contient un fichier nommé `createViews.sql`.  Ce script doit être exécuté sur la base de données cible.  La chaîne de connexion pour la base de données cible peut être obtenue à partir de votre service mobile ayant migré à partir de la blade de **paramètres** sous **Chaînes de connexion**.  Il est nommé `MS_TableConnectionString`.

Vous pouvez exécuter ce script à partir de SQL Server Management Studio ou Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Lier la base de données à votre application de Service

Lier la base de données existante à votre application de Service :

- Dans le [Portail Azure], ouvrez votre application de Service.
- Sélectionnez **tous les paramètres** -> **des connexions de données**.
- Cliquez sur **+ Ajouter**.
- Dans la liste déroulante, sélectionnez la **Base de données de SQL**
- Sous **Base de données de SQL**, sélectionnez votre base de données existante, puis cliquez sur **Sélectionner**.
- Dans la **chaîne de connexion**, entrez le nom d’utilisateur et le mot de passe pour la base de données, puis cliquez sur **OK**.
- Dans la blade **d’Ajouter des connexions de données** , cliquez sur **OK**.

Le nom d’utilisateur et le mot de passe, vous pouvez trouver en affichant la chaîne de connexion pour la base de données cible dans votre Service Mobile ayant migré.


### <a name="set-up-authentication"></a>Configurer l’authentification

Les applications Azure Mobile vous permet configurer Azure Active Directory, Facebook, Google, Microsoft et Twitter l’authentification dans le service.  L’authentification personnalisée devra être développé séparément.  Reportez-vous à la documentation de [Concepts d’authentification] et [D’authentification Quickstart] pour plus d’informations.  

## <a name="updating-clients"></a>Mise à jour des clients mobiles

Une fois que vous avez un back-end Mobile application opérationnelle, vous pouvez travailler sur une nouvelle version de votre application client qui la consomme. Applications Mobile inclut également une nouvelle version du SDK de client, et comme pour la mise à niveau de serveur ci-dessus, vous devez supprimer toutes les références aux kits de développement logiciel de Mobile Services avant d’installer les versions d’applications Mobile.

Un des principaux changements entre les versions est que les constructeurs ne nécessitent plus une clé d’application. Vous maintenant simplement passer dans l’URL de votre application Mobile. Par exemple, sur les clients .NET, les `MobileServiceClient` constructeur est maintenant :

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Vous pouvez lire sur l’installation de nouveaux kits de développement logiciel et à l’aide de la nouvelle structure via les liens ci-dessous :

- [Android 2.2 ou version ultérieure](app-service-mobile-android-how-to-use-client-library.md)
- [iOS version 3.0.0 ou version ultérieure](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) version 2.0.0 ou ultérieur](app-service-mobile-dotnet-how-to-use-client-library.md)
- [Cordova d’Apache 2.0 ou version ultérieure](app-service-mobile-cordova-how-to-use-client-library.md)

Si votre application effectue l’utilisation de notifications de type Pousser, notez les instructions d’enregistrement spécifique pour chaque plate-forme, comme l’ont été certaines modifications ainsi.

Une fois que la nouvelle version de client prête, essayez-le par rapport à votre projet de serveur mis à niveau. Après avoir validé que cela fonctionne, vous pouvez lancer une nouvelle version de votre application à des clients. Finalement, une fois que vos clients ont eu la possibilité de recevoir ces mises à jour, vous pouvez supprimer la version de Services mobiles de votre application. À ce stade, vous avez entièrement mis à niveau vers une application Mobile de Service application utilisant le tout dernier serveur d’applications Mobile SDK.

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Quelles sont les applications Mobile ?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Service d’application de tarification]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Concepts d’authentification]: ../app-service/app-service-authentication-overview.md
[Démarrage rapide de l’authentification]: app-service-mobile-auth.md

[Azure Portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
