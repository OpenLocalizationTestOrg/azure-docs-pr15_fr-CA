<properties
    pageTitle="Comment travailler avec le Kit de développement logiciel du serveur principal Node.js pour applications Mobile | Service d’application Azure"
    description="Apprenez à travailler avec le Kit de développement logiciel du serveur principal Node.js pour le Service Azure App Apps Mobile."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-azure-mobile-apps-nodejs-sdk"></a>Comment faire pour utiliser le SDK de Node.js applications Azure Mobile

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Cet article fournit des informations détaillées et des exemples montrant comment travailler avec un back-end Node.js dans Azure App Service Mobile Apps.

## <a name="Introduction"></a>Introduction

Azure App Service Mobile Apps fournit la possibilité d’ajouter optimisé pour les mobiles d’accès aux données API Web à une application web.  Le SDK d’applications Azure Application Service Mobile est fourni pour les applications web ASP.NET et Node.js.  Le SDK fournit les opérations suivantes :

- Opérations de table (lecture, Insert, Update, Delete) pour accéder aux données
- Opérations de Custom API

Les deux opérations de fournissent pour l’authentification entre tous les fournisseurs d’identité autorisées par le Service d’application Azure, y compris les fournisseurs d’identité sociaux tels que Facebook, Twitter, Google et Microsoft comme Azure Active Directory pour l’identité de l’entreprise.

Vous trouverez des exemples de chaque cas d’usage dans le [répertoire d’exemples GitHub].

## <a name="supported-platforms"></a>Plates-formes prises en charge

Le Kit de développement du nœud d’applications Mobile Azure prend en charge la version LTS actuelle du nœud et versions ultérieures.  Au moment de la rédaction, la dernière version LTS est v4.5.0 de nœud.  Autres versions de nœud peuvent fonctionner, mais ne sont pas pris en charge.

Le Kit de développement du nœud d’applications Mobile Azure prend en charge les deux pilotes de base de données - le pilote mssql-nœud prend en charge SQL Azure et les instances locales de SQL Server.  Le pilote sqlite3 prend en charge les bases de données SQLite sur une seule instance.

### <a name="howto-cmdline-basicapp"></a>Comment : créer un back-end Node.js de base à l’aide de la ligne de commande

Chaque back-end Azure Application Service Mobile application Node.js démarre comme une application ExpressJS.  ExpressJS est l’infrastructure de service web les plus populaires pour Node.js.  Vous pouvez créer un basic [Express] d’application comme suit :

1. Dans une fenêtre PowerShell ou la commande, créez un répertoire pour votre projet.

        mkdir basicapp

2. Exécutez init npm pour initialiser la structure de package.

        cd basicapp
        npm init

    La commande init npm demande un jeu de questions pour initialiser le projet.  Consultez l’exemple de sortie :

    ![La sortie d’init npm][0]

3. Installez les bibliothèques express et applications azure-mobile à partir du référentiel npm.

        npm install --save express azure-mobile-apps

4. Créez un fichier app.js pour implémenter la base serveur mobile.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table
        mobile.tables.add('TodoItem');

        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);

Cette application crée un WebAPI mobile optimisé avec un seul point de terminaison (`/tables/TodoItem`) qui fournit un accès non authentifié pour le magasin de données sous-jacent SQL à l’aide d’un schéma dynamique.  Il convient d’après les Démarrages rapides de bibliothèque client :

- [Démarrage rapide de Client Android]
- [Apache Cordova Client QuickStart]
- [iOS QuickStart du Client]
- [Démarrage rapide de Windows Store Client]
- [Démarrage rapide de Xamarin.iOS Client]
- [Démarrage rapide de Xamarin.Android Client]
- [Démarrage rapide de Xamarin.Forms Client]

Vous pouvez trouver le code de cette application de base de l' [exemple basicapp sur GitHub].

### <a name="howto-vs2015-basicapp"></a>Comment : créer un système de nœud principal avec Visual Studio 2015

Visual Studio 2015 requiert une extension pour développer des applications dans l’environnement IDE Node.js.  Pour commencer, installez la version [1.1 d’outils Node.js pour Visual Studio].  Une fois les outils Node.js pour Visual Studio sont installées, créer une application de 4.x Express :

1. Ouvrez la boîte de dialogue **Nouveau projet** (à partir du **fichier** > **Nouveau** > **projet...**).

2. **Modèles de** > **JavaScript** > **Node.js**.

3. Sélectionnez l' **Application de base Azure Node.js Express 4**.

4. Renseignez le nom du projet.  Cliquez sur *OK*.

    ![Nouveau projet de Visual Studio 2015][1]

5. Cliquez sur le nœud de **npm** et sélectionnez **installer de nouveaux packages de npm...**.

6. Vous devrez peut-être actualiser le catalogue npm sur la création de votre première application Node.js.  Si nécessaire, cliquez sur **Actualiser** .

7. Dans la zone Rechercher, entrez _applications azure-mobile_ .  Cliquez sur le package **d’applications 2.0.0 azure-mobile** , puis cliquez sur **Installer un Package**.

    ![Installez les nouveaux packages de npm][2]

8. Cliquez sur **Fermer**.

9. Ouvrez le fichier _app.js_ pour prendre en charge les applications de Mobile Azure SDK.  Au niveau de la ligne 6 at au bas de la bibliothèque de demander des instructions, ajoutez le code suivant :

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    En ligne environ 27 après les autres instructions de app.use, ajoutez le code suivant :

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    Enregistrez le fichier.

10. Exécutez l’application localement (l’API est pris en charge sur http://localhost:3000) ou publier sur Azure.

### <a name="create-node-backend-portal"></a>Comment : créer un back-end Node.js via le portail Azure

Vous pouvez créer un droit de back-end de Mobile application dans [Azure portal]. Vous pouvez suivre les étapes suivantes ou créer un client et un serveur ensemble en suivant le didacticiel [créer une application mobile](app-service-mobile-ios-get-started.md) . Le didacticiel contient une version simplifiée de ces instructions et est plus adapté à la preuve des projets de concept.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Dans la lame de _mise en route_ , sous **créer une API de table**, choisissez **Node.js** comme **langue de back-end**. Cochez la case pour «**j’ai conscience que cette action supprimera tout contenu de site.**», puis cliquez sur **TodoItem de créer une table**.

### <a name="download-quickstart"></a>Comment : télécharger le projet de code quickstart Node.js back-end à l’aide de Git

Lorsque vous créez un back-end Node.js Mobile application via le portail de lame de **démarrage rapide** , un projet Node.js est créé et déployé pour votre site. Vous pouvez ajouter des tables et des API et modifier les fichiers de code pour le back-end Node.js dans le portail. Vous pouvez également utiliser les outils de déploiement différents pour télécharger le projet principal afin que vous pouvez ajouter ou modifier des tables et des API, puis publier à nouveau le projet. Pour plus d’informations, consultez le [Guide de déploiement de Service application Azure]. la procédure suivante utilise un référentiel Git pour télécharger le code de projet quickstart.

1. Installez le Git, si ce n’est déjà fait. Les étapes requises pour installer Git varient selon les systèmes d’exploitation. Pour les distributions du système d’exploitation spécifique et les instructions d’installation, consultez [Git de l’installation](http://git-scm.com/book/en/Getting-Started-Installing-Git) .

2. Suivez les étapes décrites dans [Activer le référentiel d’application de Service d’application](../app-service-web/app-service-deploy-local-git.md#Step3) pour activer le référentiel Git pour votre site principal, une annotation de déploiement username et password.

3. De la lame pour la principale application Mobile, notez le paramètre **URL Git du clone** .

4. Exécuter le `git clone` commande en utilisant le Git clone des URL, entrez votre mot de passe lorsque cela est nécessaire, comme dans l’exemple suivant :

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Accédez au répertoire local, qui dans l’exemple précédent est /todolist et notez que les fichiers du projet ont été téléchargés. Recherchez la `todoitem.json` de fichiers dans le `/tables` répertoire.  Ce fichier définit les autorisations sur la table.  Rechercher également les `todoitem.js` le fichier dans le même répertoire, qui définit cette opération CRUD de scripts pour la table.

6. Une fois que vous avez apporté des modifications aux fichiers projet, exécutez les commandes suivantes pour ajouter, valider, puis télécharger les modifications vers le site :

        $ git commit -m "updated the table script"
        $ git push origin master

    Lorsque vous ajoutez de nouveaux fichiers au projet, vous devez tout d’abord exécuter le `git add .` commande.

Le site est republié chaque fois qu’un nouvel ensemble de validations est poussé vers le site.

### <a name="howto-publish-to-azure"></a>Comment : publier votre back-end Node.js vers Azure

Microsoft Azure fournit de nombreux mécanismes pour la publication de votre back-end Azure Application Service Mobile Apps Node.js au service Azure.  Citons notamment utilisant les outils de déploiement intégrés dans Visual Studio, des outils de ligne de commande et les options de déploiement continue en fonction de contrôle de code source.  Pour plus d’informations sur ce sujet, consultez le [Guide de déploiement de Service application Azure].

Service d’application Azure a pour application Node.js que vous devez consulter avant de déployer des conseils spécifiques :

- Comment [spécifier la Version de nœud]
- Comment [utiliser des modules de nœud]

### <a name="howto-enable-homepage"></a>Comment : activer la Page d’accueil de votre application.

De nombreuses applications sont une combinaison d’applications mobiles et le web et l’infrastructure ExpressJS vous permet de combiner les deux facettes.  Parfois, cependant, vous pouvez souhaiter n'implémenter qu’une interface mobile.  Il est utile de fournir une page d’accueil pour assurer le service de l’application est en cours d’exécution.  Vous pouvez fournir votre propre page d’accueil ou activer une page d’accueil temporaire.  Pour activer une page d’accueil temporaire, utilisez ce qui suit pour instancier les applications Azure Mobile :

    var mobile = azureMobileApps({ homePage: true });

Si vous ne souhaitez que cette option soit disponible lorsque vous développez localement, vous pouvez ajouter ce paramètre à votre `azureMobile.js` fichier.

## <a name="TableOperations"></a>Opérations de table 

Le Kit de développement du serveur Node.js applications azure-mobile fournit des mécanismes pour exposer des tables de données stockées dans la base de données de SQL Azure comme un WebAPI.  Cinq opérations sont fournies.

| Opération | Description |
| --------- | ----------- |
| OBTENIR /tables/_tablename_ | Obtenez tous les enregistrements de la table |
| OBTENIR /tables/_tablename_/:id | Obtenir un enregistrement spécifique dans la table |
| POST /tables/_tablename_ | Créer un enregistrement dans la table |
| CORRECTIF /tables/_tablename_/:id | Mettre à jour un enregistrement dans la table |
| SUPPRIMER les /:id de_tablename_/tables/ | Supprimer un enregistrement dans la table |

Cette WebAPI prend en charge les [OData] et étend le schéma de la table pour prendre en charge la [synchronisation de données hors connexion].

### <a name="howto-dynamicschema"></a>Comment : définir des tables à l’aide d’un schéma dynamique

Avant de pouvoir utiliser une table, il doit être défini.  Tables peuvent être définies avec un schéma statique (dans lequel le développeur définit les colonnes dans le schéma) ou dynamique (où le Kit de développement de contrôler le schéma basé sur les requêtes entrantes). En outre, le développeur peut contrôler des aspects spécifiques de le WebAPI en ajoutant le code Javascript à la définition.

Pour obtenir les meilleurs résultats, vous devez définir chaque table dans un fichier Javascript dans le répertoire de tables, puis utilisez la méthode tables.import() pour importer les tables.  Extension de l’application de basic, le fichier app.js seront ajusté :

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Définir la table de. / tables/TodoItem.js :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

Tables utilisent le schéma dynamique par défaut.  Pour désactiver globalement schéma dynamique, la valeur du paramètre d’application **MS_DynamicSchema** false au sein du portail Azure.

Vous trouverez un exemple complet de l' [exemple todo sur GitHub].

### <a name="howto-staticschema"></a>Comment : définir des tables à l’aide d’un schéma statique

Vous pouvez définir explicitement les colonnes à exposer via la WebAPI.  Le Kit de développement logiciel Node.js applications azure-mobile ajoute automatiquement toutes les autres colonnes requises pour la synchronisation de données hors connexion à la liste que vous fournissez.  Par exemple, les démarrage rapide pour les applications clientes nécessitent une table contenant deux colonnes : texte (une chaîne) et terminer (valeur booléenne).  
La table peut être définie dans le tableau JavaScript fichier de définition (situé dans le répertoire de tables) comme suit :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Si vous définissez des tables de manière statique, vous devez également appeler la méthode tables.initialize() pour créer le schéma de base de données au démarrage.  La méthode tables.initialize() renvoie une [promesse] afin que le service web ne sert pas de requêtes avant de la base de données en cours d’initialisation.

### <a name="howto-sqlexpress-setup"></a>Comment : utiliser SQL Express comme banque de données de développement sur votre ordinateur local

Le Azure Mobile applications la AzureMobile applications nœud SDK fournit trois options pour traiter les données de la zone : SDK fournit trois options pour traiter les données de la zone :

- Utilisez le pilote de la **mémoire** pour fournir un magasin non persistants
- Utilisez le pilote de **mssql** pour fournir un magasin de données SQL Express pour le développement
- Utilisez le pilote de **mssql** pour fournir un magasin de données de base de données de SQL Azure pour la production

Le SDK de Node.js applications Azure Mobile utilise le [package Node.js de mssql] à établir et à utiliser une connexion à SQL Express et la base de données de SQL.  Ce package nécessite que vous activez les connexions TCP sur votre instance de SQL Express.

> [AZURE.TIP]Le pilote de mémoire ne fournit pas un ensemble complet de fonctionnalités pour le test.  Si vous souhaitez tester localement votre back-end, nous vous recommandons d’utiliser un magasin de données SQL Express et le pilote de mssql.

1. Téléchargez et installez [Express 2014 de Microsoft SQL Server].  Assurez-vous que vous installez le 2014 de de SQL Server Express Edition d’outils.  Sauf si vous avez besoin explicitement la prise en charge 64 bits, la version 32 bits consomme moins de mémoire lors de l’exécution.

2. Exécutez le Gestionnaire de Configuration SQL Server 2014.

  1. Développez le nœud de **Configuration du réseau SQL Server** dans le menu de l’arborescence à gauche.
  2. Cliquez sur **protocoles pour SQLEXPRESS**.
  3. Cliquez sur **TCP/IP** et sélectionnez **Activer**.  Cliquez sur **OK** dans la boîte de dialogue contextuelle.
  4. Cliquez sur **TCP/IP** et sélectionnez **Propriétés**.
  5. Cliquez sur l’onglet **Adresses IP** .
  6. Recherchez le nœud **IPAll** .  Dans le champ **Port TCP** , entrez **1433**.

         ![Configure SQL Express for TCP/IP][3]

  7. Cliquez sur **OK**.  Cliquez sur **OK** dans la boîte de dialogue contextuelle.
  8. Dans le menu de l’arborescence de gauche, cliquez sur **Services de SQL Server** .
  9. Cliquez sur **SQL Server (SQLEXPRESS)** , puis sélectionnez **redémarrer**
  10. Fermez le Gestionnaire de Configuration SQL Server 2014.

3. Exécuter le SQL Server 2014 Management Studio et connectez-vous à votre instance locale de SQL Express

  1. Avec le bouton droit de votre instance dans l’Explorateur d’objets et sélectionnez **Propriétés**
  2. Sélectionnez la page de **sécurité** .
  3. Assurez-vous **SQL Server et le mode d’authentification Windows** est sélectionnée.
  4. Cliquez sur **OK**

        ![Configurer l’authentification SQL Express][4]

  5. Développez **sécurité** > **connexion** dans l’Explorateur d’objets
  6. Cliquez sur **connexions** et sélectionnez **Nouvelle connexion...**
  7. Entrez un nom de connexion.  Sélectionnez **l’authentification SQL Server**.  Entrez un mot de passe, puis entrez le même mot de passe dans **Confirmer le mot de passe**.  Le mot de passe doit répondre aux exigences de complexité de Windows.
  8. Cliquez sur **OK**

        ![Ajouter un nouvel utilisateur pour SQL Express][5]

  9. Avec le bouton droit de votre nouvelle connexion et sélectionnez **Propriétés**
  10. Sélectionnez la page **Rôles de serveur**
  11. Cochez la case en regard du rôle de serveur **dbcreator**
  12. Cliquez sur **OK**
  13. Fermer le Management Studio de 2015 SQL Server

Assurez-vous que vous enregistrez le nom d’utilisateur et le mot de passe sélectionné.  Vous devrez attribuer des rôles serveur supplémentaires et des autorisations en fonction de vos besoins en matière de base de données spécifique.

L’application Node.js lit la variable d’environnement **SQLCONNSTR_MS_TableConnectionString** pour la chaîne de connexion pour cette base de données.  Vous pouvez définir cette variable au sein de votre environnement.  Par exemple, vous pouvez utiliser PowerShell pour définir cette variable d’environnement :

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Accéder à la base de données via une connexion TCP/IP et fournir un nom d’utilisateur et le mot de passe pour la connexion.

### <a name="howto-config-localdev"></a>Comment : configurer votre projet pour le développement local

Les applications Mobile Azure lit un fichier JavaScript appelé _azureMobile.js_ dans le système de fichiers local.  N’utilisez pas ce fichier pour configurer les applications de Mobile Azure SDK en production - utilisez à la place des paramètres de l’application au sein du [portail Azure] .  Le fichier _azureMobile.js_ doit exporter un objet de configuration.  Les plus courantes sont les suivantes :

- Paramètres de la base de données
- Paramètres d’enregistrement des Diagnostics
- Paramètres de remplacement CORS

Un exemple de fichier _azureMobile.js_ les paramètres précédents de la base de données de mise en œuvre suit :

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Nous vous conseillons _azureMobile.js_ dans votre fichier _.gitignore_ (ou tout autre contrôle de code source ignorer fichier) pour empêcher le stockage dans le nuage de mots de passe.  Toujours configurer les paramètres de production dans les paramètres de l’application au sein du [portail Azure].

### <a name="howto-appsettings"></a>Comment : Configurer les paramètres de l’application pour votre application Mobile

La plupart des paramètres dans le fichier _azureMobile.js_ ont un paramètre équivalent d’application dans [Azure portal].  Pour configurer votre application dans les paramètres de l’application, utilisez la liste suivante :

| Paramètre d’application                 | Paramètre de _azureMobile.js_  | Description                               | Valeurs valides                                |
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS_MobileAppName**        | nom                      | Le nom de l’application                       | chaîne                                      |
| **MS_MobileLoggingLevel**   | Logging.Level             | Niveau du journal minimum de messages à enregistrer      | erreur, avertissement, information, commentaires, déboguer, stupide |
| **MS_DebugMode**            | débogage                     | Activer ou désactiver le mode débogage              | True, false                                 |
| **MS_TableSchema**          | Data.Schema               | Nom du schéma par défaut pour les tables SQL        | chaîne (par défaut : dbo)                       |
| **MS_DynamicSchema**        | data.dynamicSchema        | Activer ou désactiver le mode débogage              | True, false                                 |
| **MS_DisableVersionHeader** | version (définie à non définie)| Désactive l’en-tête X-ZUMO-Server-Version | True, false                                 |
| **MS_SkipVersionCheck**     | skipversioncheck          | Désactive la vérification de la version client API     | True, false                                 |

Pour définir un paramètre d’application :

1. Connectez-vous au [portail Azure].
2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre application Mobile.
3. La lame de paramètres s’ouvre par défaut. Si elle n’est pas le cas, cliquez sur **paramètres**.
4. Dans le menu général, cliquez sur **paramètres de l’Application** .
5. Faites défiler jusqu'à la section Paramètres de l’application.
6. Si votre application configuration déjà existe, cliquez sur la valeur du paramètre d’application pour modifier la valeur.
7. Si votre paramètre d’application n’existe pas, entrez le paramètre d’application dans la zone de la clé et la valeur dans la zone valeur.
8. Une fois que vous avez terminé, cliquez sur **Enregistrer**.

Modification de la plupart des paramètres de l’application nécessite un redémarrage du service.

### <a name="howto-use-sqlazure"></a>Comment : base de données SQL d’utiliser comme magasin de données de production

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

À l’aide de la base de données de SQL Azure comme un magasin de données est identique pour tous les types d’application de Service d’application Azure. Si vous ne le n'avez pas déjà fait, suivez ces étapes pour créer un back-end de l’application Mobile.

1. Connectez-vous au [portail Azure].

2. Dans l’angle supérieur gauche de la fenêtre, cliquez sur le bouton **+ Nouveau** > **Web + Mobile** > **Mobile App**, puis donnez un nom à votre back-end de l’application Mobile.

3. Dans la zone de **Groupe de ressource** , entrez le même nom que votre application.

4. Le plan de Service d’application par défaut est sélectionné.  Si vous souhaitez modifier votre plan de Service de l’application, vous pouvez le faire en cliquant sur le Plan de Service d’application > **+ créer de nouveaux**.  Fournir un nom de la nouvelle planification de Service de l’application et sélectionnez un emplacement approprié.  Cliquez sur le niveau de prix et sélectionnez un niveau de tarification approprié pour le service. Sélectionnez **Afficher tout** pour afficher les options de tarification plus, tels que **Free** et **Shared**.  Une fois que vous avez sélectionné le niveau de tarification, cliquez sur le bouton **Sélectionner** .  Dans la lame de **plan de Service de l’application** , cliquez sur **OK**.

5. Cliquez sur **créer**. Mise en service d’un back-end de l’application Mobile peut prendre quelques minutes.  Une fois le serveur principal chargé de l’application Mobile est mis en service, le portail s’ouvre à la lame de **paramètres** pour le back-end de l’application Mobile.

Une fois le serveur principal chargé de l’application Mobile est créé, vous pouvez choisir connecter une base de données SQL existante à votre back-end de l’application Mobile ou en créer une nouvelle base de données SQL.  Dans cette section, nous créons une base de données SQL.

> [AZURE.NOTE]Si vous disposez déjà d’une base de données dans le même emplacement que le serveur principal d’une application mobile, vous pouvez choisissez plutôt **d’utiliser une base de données existante** et sélectionnez cette base de données. L’utilisation d’une base de données dans un autre emplacement n’est pas recommandée en raison de latences supérieures.

6. Dans le serveur principal d’une nouvel application Mobile, cliquez sur **paramètres** > **Application Mobile** > **données** > **+ Ajouter**.

7. De la lame **d’Ajouter de connexion de données** , cliquez sur **Une base de données SQL - configurer les paramètres requis** > **créer une nouvelle base de données**.  Dans le champ **nom** , entrez le nom de la nouvelle base de données.

8. Cliquez sur **le serveur**.  Dans le **nouveau serveur** lame, entrez un nom unique dans le champ **nom du serveur** et fournir un approprié **connexion d’admin Server** et un **mot de passe**.  Vérifiez que **services autoriser azure pour accéder à server** est sélectionnée.  Cliquez sur **OK**.

    ![Créer une base de données SQL Azure][6]

9. Sur la lame de la **nouvelle base de données** , cliquez sur **OK**.

10. Sur la lame **Ajouter de connexion de données** , sélectionnez la **chaîne de connexion**, saisissez le login et le mot de passe que vous avez fournies lors de la création de la base de données.  Si vous utilisez une base de données existante, fournir les informations d’identification de connexion pour cette base de données.  Une fois saisies, cliquez sur **OK**.

11. Revenir sur la blade **d’Ajouter de connexion de données** , cliquez sur **OK** pour créer la base de données.

<!--- END OF ALTERNATE INCLUDE -->

Création de la base de données peut prendre quelques minutes.  Utilisez la zone de **notification** pour surveiller la progression du déploiement.  Ne progressent pas jusqu'à ce que la base de données a été déployé avec succès.  Une fois déployée, une chaîne de connexion est créée pour l’instance de base de données SQL dans votre back-end Mobile de paramètres d’application.  Vous pouvez voir ce paramètre d’application dans les **paramètres** > **paramètres de l’Application** > **les chaînes de connexion**.

### <a name="howto-tables-auth"></a>Comment : demander l’authentification pour l’accès aux tables

Si vous souhaitez utiliser l’authentification de Service d’application avec le point de terminaison de tables, vous devez configurer l’authentification du Service application dans [Azure portal] tout d’abord.  Pour plus d’informations sur la configuration de l’authentification dans un Service d’application Azure, consultez le Guide de Configuration pour le fournisseur d’identité que vous souhaitez utiliser :

- [Comment faire pour configurer l’authentification Active Directory Azure]
- [Comment faire pour configurer l’authentification Facebook]
- [Comment configurer l’authentification de Google]
- [Comment faire pour configurer Microsoft Authentication]
- [Comment configurer l’authentification de Twitter]

Chaque table possède une propriété access qui peut être utilisée pour contrôler l’accès à la table.  L’exemple suivant montre un tableau défini de façon statique avec authentification requise.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

La propriété access peut prendre une des trois valeurs

  - *anonyme* indique que l’application client est autorisée à lire des données sans authentification
  - *authentifié* indique que l’application cliente doit envoyer un jeton d’authentification valide avec la demande
  - *désactivé* indique que cette table est actuellement désactivée

Si la propriété d’accès n’est pas définie, l’accès non authentifié est autorisé.

### <a name="howto-tables-getidentity"></a>Comment : utiliser l’authentification par revendications avec vos tables

Vous pouvez définir différentes revendications qui vous sont demandées lors de l’authentification est configurée.  Ces demandes ne sont pas normalement disponibles via le `context.user` objet.  Toutefois, ils peuvent être récupérés à l’aide de la `context.user.getIdentity()` méthode.  Le `getIdentity()` méthode retourne une promesse qui se résout en un objet.  L’objet est indexée par la méthode d’authentification (facebook, google, twitter, microsoftaccount ou DAS).

Par exemple, si vous configurez l’authentification de Microsoft Account et demande les adresses de messagerie, vous pouvez ajouter l’adresse de messagerie à l’enregistrement avec le contrôleur du tableau suivant :

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

Pour voir quelles demandes sont disponibles, utilisez un navigateur web pour afficher les `/.auth/me` point de terminaison de votre site.

### <a name="howto-tables-disabled"></a>Comment : désactiver l’accès à des opérations de table spécifique

Outre figurant dans le tableau, la propriété d’accès permet de contrôler les opérations individuelles.  Il existe quatre opérations :

  - *lire* est l’opération obtenir RESTful sur la table
  - *INSERT* est l’opération POST RESTful sur la table
  - *mise à jour* est l’opération correctif RESTful sur la table
  - *Supprimer* est l’opération Supprimer RESTful sur la table

Par exemple, vous pouvez souhaiter fournir une table non authentifiée en lecture seule :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Comment : ajuster la requête qui est utilisée avec les opérations de table

Une exigence courante pour les opérations de table est de fournir un affichage restreint des données.  Par exemple, vous pouvez fournir une table qui est marquée avec l’ID de l’utilisateur authentifié, tel que vous pouvez uniquement lire ou mettre à jour vos propres enregistrements.  La définition de table suivante fournit cette fonctionnalité :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Les opérations qui normalement exécuter une requête ont une propriété de la requête que vous pouvez ajuster avec un emplacement clause. La propriété query est un objet [QueryJS] qui est utilisé pour convertir une requête OData à quelque chose que le back-end de données peut traiter.  Pour les cas d’égalité simples (voir ci-dessus), un mappage peut être utilisé. Vous pouvez également ajouter des clauses SQL spécifiques :

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Comment : configurer la suppression temporaire sur une table

Suppression temporaire ne supprime pas réellement les enregistrements.  À la place il les marque comme supprimés dans la base de données en définissant la colonne supprimée sur true.  Le Kit de développement logiciel applications Azure Mobile supprime automatiquement les enregistrements supprimés à partir des résultats, à moins que le Kit de développement Client Mobile utilise IncludeDeleted().  Pour configurer une table pour suppression temporaire, définissez la `softDelete` propriété dans le fichier de définition de table :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Turn on Soft Delete
    table.softDelete = true;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Vous devez établir un mécanisme pour purger les enregistrements - à partir d’une application cliente, via un WebJob, une fonction d’Azure, ou via une API personnalisée.

### <a name="howto-tables-seeding"></a>Comment : amorcer votre base de données avec des données

Lors de la création d’une application, vous souhaiterez peut-être amorcer une table avec des données.  Pour ce faire dans le fichier JavaScript de définition de table comme suit :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Distribution des données est effectuée uniquement lorsque la table est créée par le Kit de développement logiciel applications Azure Mobile.  Si la table existe déjà dans la base de données, aucune donnée n’est injectée dans le tableau.  Si le schéma dynamique est activée, le schéma est déduit à partir des données amorcées.

Il est recommandé que vous appeliez explicitement la `tables.initialize()` méthode pour créer la table lors du démarrage du service en cours d’exécution.

### <a name="Swagger"></a>Comment : activer la prise en charge de Swagger

Azure App Service Mobile Apps est fourni avec intégré [Swagger] la prise en charge.  Pour activer la prise en charge de Swagger, vous devez tout d’abord installer le swagger-interface utilisateur en tant que dépendance :

    npm install --save swagger-ui

Une fois installé, vous pouvez activer la prise en charge de Swagger dans le constructeur d’applications Mobile de Azure :

    var mobile = azureMobileApps({ swagger: true });

Vous probablement souhaitez activer la prise en charge dans les éditions de développement Swagger.  Cela à l’aide de la `NODE_ENV` paramètre d’application :

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

Le point de terminaison swagger se trouve à http://_yoursite_.azurewebsites.net/swagger.  Vous pouvez accéder à l’interface utilisateur de Swagger via la `/swagger/ui` point de terminaison.  Si vous choisissez d’exiger l’authentification au sein de votre application dans son intégralité, Swagger génère une erreur.  Pour de meilleurs résultats, choisissez permettre les demandes non authentifiées par le biais de l’authentification de Service d’application Azure / paramètres d’autorisation, puis le contrôle à l’aide de l’authentification par le `table.access` propriété.

Vous pouvez également ajouter l’option Swagger pour votre `azureMobile.js` de fichiers si vous souhaitez uniquement prise en charge de Swagger lors du développement localement.

## <a name="a-namepushpush-notifications"></a><a name="push">Notifications de type Pousser

Applications Mobile s’intègre dans Azure des concentrateurs de Notification pour vous permettre d’envoyer des notifications de type Pousser ciblée à des millions de périphériques toutes les principales plates-formes. À l’aide de concentrateurs de Notification, vous pouvez envoyer des notifications de type Pousser pour iOS, Android et Windows des périphériques. Pour en savoir que plus sur tout cela vous peuvent faire avec les concentrateurs de Notification, consultez [Vue d’ensemble des concentrateurs de Notification](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Comment : envoyer des notifications de transmission

Le code suivant montre comment utiliser l’objet push pour envoyer une notification de transmission de diffusion pour les e/s des périphériques :

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured
    if (context.push) {
        // Send a push notification using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

En créant un enregistrement de commande du modèle à partir du client, vous pouvez plutôt envoyer un message de type Pousser du modèle pour les périphériques sur toutes les plates-formes prises en charge. Le code suivant montre comment envoyer une notification de modèle :

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


###<a name="push-user"></a>Comment : envoyer des notifications de transmission pour un utilisateur authentifié à l’aide de balises

Lorsqu’un utilisateur authentifié enregistre pour les notifications de type Pousser, un identificateur d’utilisateur est automatiquement ajouté à l’enregistrement. À l’aide de cette balise, vous pouvez envoyer des notifications de type pousser à tous les périphériques sont enregistrés par un utilisateur spécifique. Le code suivant obtient le SID de l’utilisateur effectuant la demande et envoie une notification de transmission modèle à chaque enregistrement de dispositif pour cet utilisateur :

    // Only do the push if configured
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Lors de l’enregistrement pour les notifications de transmission à partir d’un client authentifié, assurez-vous que l’authentification est terminée avant d’essayer de l’enregistrement.

## <a name="CustomAPI"></a>API personnalisé

###  <a name="howto-customapi-basic"></a>Comment : définir une API personnalisée


En plus de l’accès aux données API via le point de terminaison /tables, applications de Mobile Azure peut couvrir personnalisé API.  Personnalisé API sont définies de la même façon pour les définitions de table et pouvez accéder à tous les mêmes installations, y compris l’authentification.

Si vous souhaitez utiliser l’authentification du Service application avec une API personnalisé, vous devez configurer l’authentification du Service application dans [Azure portal] tout d’abord.  Pour plus d’informations sur la configuration de l’authentification dans un Service d’application Azure, consultez le Guide de Configuration pour le fournisseur d’identité que vous souhaitez utiliser :

- [Comment faire pour configurer l’authentification Active Directory Azure]
- [Comment faire pour configurer l’authentification Facebook]
- [Comment configurer l’authentification de Google]
- [Comment faire pour configurer Microsoft Authentication]
- [Comment configurer l’authentification de Twitter]

API personnalisé sont définies de la même façon que l’API de Tables.

1. Créer un répertoire de **l’api**
2. Créer un fichier de définition du JavaScript API dans le répertoire de **l’api** .
3. Utilisez la méthode import pour importer le répertoire de **l’api** .

Voici la définition d’api de prototype basée sur l’exemple d’application-basic utilisé précédemment.

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Prenons un exemple API qui renvoie la date du serveur à l’aide de la méthode _Date.now()_ .  Voici le fichier api/date.js :

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Chaque paramètre est un des verbes standard RESTful - GET, POST, corriger ou supprimer.  La méthode est une fonction standard [ExpressJS Middleware] qui envoie la sortie requise.

### <a name="howto-customapi-auth"></a>Comment : demander l’authentification pour l’accès à une API personnalisée

Le Kit de développement d’applications Azure Mobile implémente l’authentification de la même façon pour les API personnalisé et le point de terminaison de tables.  Pour ajouter l’authentification à l’API de développé dans la section précédente, ajouter une propriété **d’access** :

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

Vous pouvez également spécifier l’authentification sur des opérations spécifiques :

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

Le même jeton qui est utilisé pour le point de terminaison de tables doit être utilisé pour les API personnalisés nécessitant une authentification.

### <a name="howto-customapi-auth"></a>Comment : gérer les téléchargements de fichiers volumineux

Azure SDK d’applications Mobile utilise l' [Analyseur corps middleware](https://github.com/expressjs/body-parser) d’accepter et de décoder le contenu du corps de votre envoi.  Vous pouvez préconfigurer les corps-analyseur pour accepter les téléchargements de fichiers plus grande :

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Le fichier est codé avant la transmission en base 64.  Cela augmente la taille du téléchargement réel (et donc la taille vous devez prendre en compte pour).

### <a name="howto-customapi-sql"></a>Comment : exécuter des instructions SQL personnalisées

Le Kit de développement logiciel applications Azure Mobile permet l’accès à l’ensemble du contexte par le biais de l’objet de la demande, ce qui vous permet d’exécuter des instructions SQL paramétrées pour le fournisseur de données définies facilement :

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Débogage, Tables faciles et simple API

### <a name="howto-diagnostic-logs"></a>Comment : déboguer, diagnostiquer et résoudre les problèmes des applications Azure Mobile

Le Service d’application Azure fournit plusieurs de débogage et de résolution de problèmes pour les applications de Node.js.
Consultez les articles suivants pour commencer à dépanner la principale Node.js Mobile :

- [Surveillance d’un Service d’application Azure]
- [Activer l’enregistrement des diagnostics dans le Service d’application Azure]
- [Résoudre les problèmes liés à un Service d’application Azure dans Visual Studio]

Node.js applications ont accès à une large gamme d’outils de diagnostic de journal.  En interne, le SDK de Node.js applications Azure Mobile utilise [Winston] pour l’enregistrement des Diagnostics.  La journalisation est activée automatiquement en activant le mode débogage ou en définissant le paramètre d’application de **MS_DebugMode** sur true dans le [portail Azure]. Journaux générés s’affichent dans les journaux de Diagnostic sur le [portail Azure].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Comment : travailler avec des Tables simples dans le portail Azure

Tables faciles dans le portail vous permettent de créer et travailler sur la droite de tables dans le portail. Vous pouvez même modifier les opérations de table à l’aide de l’éditeur de Service d’application.

Lorsque vous cliquez sur **tables faciles** dans vos paramètres de site principal, vous pouvez ajouter, modifier ou supprimer une table. Vous pouvez également voir les données de la table.

![Utiliser des tableaux simples](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Les commandes suivantes sont disponibles dans la barre de commande d’une table :

+ **Modifier les autorisations** - modifier l’autorisation de lecture, insérer, mise à jour et supprimer des opérations sur la table. 
  Options consistent à autoriser l’accès anonyme, pour exiger une authentification ou de désactiver tous les accès à l’opération. 
+ **Modifier le script** - le fichier de script pour la table s’ouvre dans l’éditeur de Service App..
+ **Schéma de gestion** - ajouter ou supprimer des colonnes ou modifier l’index de la table.
+ **Effacer le tableau** - tronque une table existante, la suppression de toutes les lignes de données mais en laissant le schéma reste la même.
+ **Supprimer des lignes** : suppression des lignes de données individuelles.
+ **Mode de diffusion en continu de journaux** - vous connecte au service de journal de transmission en continu pour votre site.

###<a name="work-easy-apis"></a>Comment : travailler avec les API facile dans le portail Azure

API facile dans le portail vous permettre de créer et manipuler un droit personnalisé API dans le portail. Vous pouvez modifier des scripts d’API à l’aide de l’éditeur de Service d’application.

Lorsque vous cliquez sur **API facile** dans vos paramètres de site principal, vous pouvez ajouter, modifier ou supprimer un point de terminaison personnalisé de l’API.

![Travail avec les API facile](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

Dans le portail, vous pouvez modifier les autorisations d’accès pour une action donnée de HTTP, modifier le fichier de script dans l’Éditeur du Service application API ou afficher les journaux en continu.

###<a name="online-editor"></a>Comment : modifier le code dans l’éditeur de Service App.

Le portail Azure permet de modifier vos fichiers de script Node.js back-end dans l’Éditeur du Service application sans devoir télécharger le projet sur votre ordinateur local. Pour modifier les fichiers de script dans l’éditeur en ligne :

1. Dans votre lame principale application Mobile, cliquez sur **tous les paramètres** > **tables faciles** ou **API facile**, une table ou une API, puis cliquez sur **Modifier le script**. Le fichier de script est ouvert dans l’Éditeur du Service App.

    ![Éditeur de Service d’application](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)

2. Apportez vos modifications au fichier de code dans l’éditeur en ligne. Les modifications sont enregistrées automatiquement en cours de frappe.


<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Démarrage rapide de Client Android]: app-service-mobile-android-get-started.md
[Apache Cordova Client QuickStart]: app-service-mobile-cordova-get-started.md
[iOS QuickStart du Client]: app-service-mobile-ios-get-started.md
[Démarrage rapide de Xamarin.iOS Client]: app-service-mobile-xamarin-ios-get-started.md
[Démarrage rapide de Xamarin.Android Client]: app-service-mobile-xamarin-android-get-started.md
[Démarrage rapide de Xamarin.Forms Client]: app-service-mobile-xamarin-forms-get-started.md
[Démarrage rapide de Windows Store Client]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/Javascript Client QuickStart]: app-service-html-get-started.md
[synchronisation de données hors connexion]: app-service-mobile-offline-data-sync.md
[Comment faire pour configurer l’authentification Active Directory Azure]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Comment faire pour configurer l’authentification Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Comment configurer l’authentification de Google]: app-service-mobile-how-to-configure-google-authentication.md
[Comment faire pour configurer Microsoft Authentication]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Comment configurer l’authentification de Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
[Guide de déploiement de Service d’application Azure]: ../app-service-web/web-sites-deploy.md
[Surveillance d’un Service d’application Azure]: ../app-service-web/web-sites-monitor.md
[Activer l’enregistrement des diagnostics dans le Service d’application Azure]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Résoudre les problèmes liés à un Service d’application Azure dans Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[spécifier la Version de nœud]: ../nodejs-specify-node-version-azure-apps.md
[utiliser des modules de nœud]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Azure portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Simulation de délai]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[exemple de basicapp sur GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[exemple TODO sur GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[répertoire d’exemples GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js outils 1.1 pour Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[package de Node.js MSSQL]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[Middleware de ExpressJS]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
