<properties 
    pageTitle="Application Web avec le stockage de table (Node.js) | Microsoft Azure" 
    description="Un didacticiel qui s’appuie sur l’application Web avec Express didacticiel en ajoutant des services de stockage Azure et le module Azure." 
    services="cloud-services, storage" 
    documentationCenter="nodejs" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="robmcm"/>

# <a name="nodejs-web-application-using-storage"></a>Application Web de Node.js l’utilisation du stockage

## <a name="overview"></a>Vue d’ensemble

Dans ce didacticiel, vous allez étendre l’application créé dans le didacticiel de [l’Application Web de Node.js utilisant Express] en utilisant les bibliothèques de Client Microsoft Azure pour Node.js pour travailler avec les services de gestion de données. Vous allez étendre votre application pour créer une application de liste de tâches sur le web que vous pouvez déployer sur Azure. La liste des tâches permet à un utilisateur de récupérer des tâches, ajouter de nouvelles tâches et marquer les tâches comme terminée.

Les éléments de tâche sont stockés dans le stockage Azure. Stockage Azure fournit le stockage de données non structurées à tolérance de pannes et de haute disponibilité. Stockage Azure inclut plusieurs structures de données dans laquelle vous pouvez stocker et accéder aux données et que vous pouvez exploiter les services de stockage à partir de l’API inclus dans le Kit de développement Azure pour Node.js ou via les API du reste. Pour plus d’informations, consultez [stockage et accès aux données dans Azure].

Ce didacticiel suppose que vous avez terminé les didacticiels [Node.js Web Application] et [Node.js avec Express][à l’aide de Node.js Web Application Express] .

Vous allez apprendre :

-   Comment travailler avec le moteur de modèle Jade
-   L’utilisation des services de gestion de données Azure

Une capture d’écran de l’application terminée est inférieure à :

![La page web terminée dans internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Configuration du stockage d’informations d’identification dans le fichier Web.Config

Pour accéder au stockage d’Azure, vous devez passer les informations d’identification de stockage. Pour ce faire, vous utilisez les paramètres web.config de l’application.
Ces paramètres seront passés en tant que variables d’environnement pour le nœud, qui sont ensuite lus par le Kit de développement Azure.

> [AZURE.NOTE] Informations d’identification de stockage ne sont utilisées que lorsque l’application est déployée sur Azure. Lors de l’exécution dans l’émulateur, l’application utilise l’émulateur de stockage.

Effectuez les étapes suivantes pour récupérer les informations d’identification du compte de stockage et de les ajouter aux paramètres web.config :

1.  S’il n’est pas déjà ouvrir démarrer PowerShell Azure dans le menu **Démarrer** en développant **tous les programmes, Azure**, droit **Azure PowerShell**et puis sélectionnez **Exécuter en tant qu’administrateur**.

2.  Accédez au dossier contenant votre application. Par exemple, C:\\nœud\\tasklist\\WebRole1.

3.  Dans la fenêtre Powershell d’Azure, entrez l’applet de commande suivante pour extraire les informations de compte de stockage :

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    Il récupère la liste des comptes de stockage et les clés associées à votre service hébergé de compte.

    > [AZURE.NOTE] Étant donné que le SDK Azure crée un compte de stockage lorsque vous déployez un service, un compte de stockage doit déjà exister de déployer votre application dans les guides des précédentes.

4.  Ouvrez le fichier **ServiceDefinition.csdef** qui contient les paramètres d’environnement qui sont utilisées lorsque l’application est déployée vers Azure :

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Insérez le bloc suivant sous l’élément **d’environnement** , en remplaçant {compte de stockage} et {clé d’accès de stockage} avec le nom de compte et de la clé primaire pour le compte de stockage que vous souhaitez utiliser pour le déploiement :

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![Le contenu du fichier web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Enregistrez le fichier et fermez le bloc-notes.

### <a name="install-additional-modules"></a>Installation des modules complémentaires

2. Utilisez la commande suivante pour installer le [azure], [uuid-nœud], [nconf] et [async] les modules localement sous pour enregistrer une entrée pour eux dans le fichier **package.json** :

        PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

    La sortie de cette commande doit ressembler à ce qui suit :

        node-uuid@1.4.1 node_modules\node-uuid

        nconf@0.6.9 node_modules\nconf
        ├── ini@1.1.0
        ├── async@0.2.9
        └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##<a name="using-the-table-service-in-a-node-application"></a>À l’aide du service de la Table dans une application de nœud

Dans cette section, vous allez étendre l’application de base créé par la commande **express** en ajoutant un fichier **task.js** qui contient le modèle pour vos tâches. Vous sera également modifier l' existante **app.js** et créer un fichier **tasklist.js** qui utilise le modèle.

### <a name="create-the-model"></a>Créer le modèle

1. Dans le répertoire **WebRole1** , créez un nouveau répertoire nommé **modèles**.

2. Dans le répertoire de **modèles** , créez un nouveau fichier nommé **task.js**. Ce fichier contient le modèle pour les tâches créées par votre application.

3. Au début du fichier **task.js** , ajoutez le code suivant pour référencer les bibliothèques requises :

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. Ensuite, vous ajouterez du code pour définir et exporter l’objet de la tâche. Cet objet est responsable de la connexion à la table.

        module.exports = Task;

        function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

5. Ensuite, ajoutez le code suivant pour définir d’autres méthodes sur l’objet de la tâche, qui permettent des interactions avec les données stockées dans la table :

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },

          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };

            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },

          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }

6. Enregistrez et fermez le fichier **task.js** .

### <a name="create-the-controller"></a>Création du contrôleur

1. Dans le répertoire **WebRole1/itinéraires** , créer un nouveau fichier nommé **tasklist.js** et ouvrez-le dans un éditeur de texte.

2. Ajoutez le code suivant à **tasklist.js**. Il charge les modules azure et asynchrone, qui sont utilisés par **tasklist.js**. Il définit également la fonction de **liste des tâches** , qui est passée à une instance de l’objet de **tâche** que nous avons défini plus haut :

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

2. Continuez à ajouter au fichier **tasklist.js** en ajoutant les méthodes utilisées pour **showTasks**, **addTask**et **completeTasks**:

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this      
            var item = req.body.item;
            self.task.addItem(item, function itemAdded(error) {
              if(error) {
                throw error;
              }
              res.redirect('/');
            });
          },

          completeTask: function(req,res) {
            var self = this;
            var completedTasks = Object.keys(req.body);
            async.forEach(completedTasks, function taskIterator(completedTask, callback) {
              self.task.updateItem(completedTask, function itemsUpdated(error) {
                if(error){
                  callback(error);
                } else {
                  callback(null);
                }
              });
            }, function goHome(error){
              if(error) {
                throw error;
              } else {
               res.redirect('/');
              }
            });
          }
        }

3. Enregistrez le fichier **tasklist.js** .

### <a name="modify-appjs"></a>Modifier app.js

1. Dans le répertoire **WebRole1** , ouvrez le fichier **app.js** dans un éditeur de texte. 

2. Au début du fichier, ajoutez la commande suivante pour charger le module azure et de définir la clé de partition et de nom de table :

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3. Dans le fichier app.js, recherchez où vous voyez la ligne suivante :

        app.use('/', routes);
        app.use('/users', users);

    Remplacez les lignes précédentes par le code ci-dessous. Une instance de <strong>tâche</strong> avec une connexion à votre compte de stockage sera initialisé. Il est passé à la <strong>liste des tâches</strong>, qui utilisent pour communiquer avec le service de la Table :

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
    
4. Enregistrez le fichier **app.js** .

### <a name="modify-the-index-view"></a>Modifier l’affichage de l’index

1. Changez les répertoires pour le répertoire **views** et ouvrez le fichier **index.jade** dans un éditeur de texte.

2. Remplacez le contenu du fichier **index.jade** par le code ci-dessous. Définit l’affichage pour afficher les tâches existantes, mais aussi un formulaire permettant d’ajouter de nouvelles tâches et de marquage existants comme étant terminée.

        extends layout

        block content
          h1= title
          br
        
          form(action="/completetask", method="post")
            table.table.table-striped.table-bordered
              tr
                td Name
                td Category
                td Date
                td Complete
              if tasks != []
                tr
                  td 
              else
                each task in tasks
                  tr
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name: 
            input(name="item[name]", type="textbox")
            label Item Category: 
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item

3. Enregistrez et fermez le fichier de **index.jade** .

### <a name="modify-the-global-layout"></a>Modifier la disposition globale

Le fichier **layout.jade** dans le répertoire **views** est utilisé en tant que modèle global pour les autres fichiers **.jade** . Dans cette étape, vous allez modifier pour utiliser [Twitter des données d’amorçage](https://github.com/twbs/bootstrap), qui est un jeu d’outils qui permet de concevoir un site Web de qualité bon.

1. Téléchargez et extrayez les fichiers [d’Amorçage de Twitter](http://getbootstrap.com/). Copiez le fichier **bootstrap.min.css** à partir de la **d’amorçage\\dist\\css** dossier pour le **public\\feuilles de style** répertoire de votre application de liste des tâches.

2. Dans le dossier **vues** , ouvrez le **layout.jade** dans votre éditeur de texte et remplacez le contenu par le texte suivant :

        doctype html
        html
          head
            title= title
            link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
            link(rel='stylesheet', href='/stylesheets/style.css')
          body.app
            nav.navbar.navbar-default
              div.navbar-header
                a.navbar-brand(href='/') My Tasks
            block content

3. Enregistrez le fichier **layout.jade** .

### <a name="running-the-application-in-the-emulator"></a>Exécution de l’Application dans l’émulateur

Utilisez la commande suivante pour démarrer l’application dans l’émulateur.

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

Le navigateur s’ouvre et affiche la page suivante :

![Un site web paginé intitulée Ma liste de tâches avec une table contenant les champs à ajouter une nouvelle tâche et les tâches.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

Utilisez le formulaire pour ajouter des éléments ou supprimer des éléments existants en les marquant comme étant terminée.

## <a name="publishing-the-application-to-azure"></a>Publication de l’Application vers Azure


Dans la fenêtre Windows PowerShell, appeler l’applet de commande suivante pour redéployer votre service hébergé sur Azure.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Remplacez **myuniquename** par un nom unique pour cette application. Remplacez **datacentername** par le nom d’un centre de données Azure, par exemple **US-ouest**.

Une fois le déploiement terminé, vous devez voir une réponse semblable à la suivante :

    PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
    WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
    WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
    WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
    WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
    WARNING: 2:19:01 PM - Connecting...
    WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
    WARNING: 2:19:40 PM - Upgrading...
    WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
    WARNING: 2:22:48 PM - Initializing...
    WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
    WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Comme avant, car vous avez spécifié le **-lancement** option, le navigateur s’ouvre et affiche votre application en cours d’exécution dans Azure lorsque la publication est terminée.

![Une fenêtre de navigateur affichant la page de ma liste de tâches. L’URL indique que la page est maintenant hébergée sur Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Arrêt et suppression de votre Application.

Après avoir déployé votre application, vous souhaiterez sans doute désactiver afin que vous pouvez éviter les coûts ou générer et déployer des applications pendant la période d’essai gratuite.

Échéances Azure web par heure de temps serveur consommé, les instances de rôles.
Heure du serveur est consommée une fois que votre application est déployée, même si les instances ne sont pas en cours d’exécution et à l’état arrêté.

Les étapes suivantes vous montrent comment arrêter et supprimer de votre application.

1.  Dans la fenêtre Windows PowerShell, arrêter le déploiement du service créé dans la section précédente avec l’applet de commande suivante :

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    Arrêt du service peut prendre plusieurs minutes. Lorsque le service est arrêté, vous recevez un message indiquant qu’il s’est arrêté.

3.  Pour supprimer le service, appeler l’applet de commande suivante :

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Lorsque vous y êtes invité, entrez **Y** pour supprimer le service.

    Suppression du service peut prendre plusieurs minutes. Une fois que le service a été supprimé, vous recevez un message indiquant que le service a été supprimé.

  [Application Web de Node.js à l’aide d’Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Stockage et accès aux données dans Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Application Web de Node.js]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
 
