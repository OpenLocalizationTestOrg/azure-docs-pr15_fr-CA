<properties
    pageTitle="Se connecter au stockage Azure dans votre application de Xamarin.Forms"
    description="Ajouter des images à l’application de mobile Xamarin.Forms de liste todo en vous connectant au stockage d’objets blob Azure"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="connect-to-azure-storage-in-your-xamarinforms-app"></a>Se connecter au stockage Azure dans votre application de Xamarin.Forms

## <a name="overview"></a>Vue d’ensemble

Le client d’Azure Mobile Apps et le Kit de développement logiciel prend en charge la synchronisation hors connexion des données structurées avec des opérations sur le point de terminaison /tables. En règle générale, ces données sont stockées dans une base de données ou de similaire, et généralement ces magasins de données ne peut pas stocker efficacement des données binaires volumineuses. De même, certaines applications ont associé des données stockées ailleurs (par exemple, stockage d’objets blob, les partages de fichiers), et il est utile de pouvoir créer des associations entre des enregistrements dans le point de terminaison /tables et d’autres données.

Cette rubrique vous indique comment ajouter la prise en charge pour les images d’applications Mobile todo liste démarrage rapide. Vous devez tout d’abord terminer le didacticiel de [Création d’une application Xamarin.Forms].

Dans ce didacticiel, vous créez un compte de stockage et ajouter une chaîne de connexion à votre back-end de l’application Mobile. Vous ajouterez ensuite un héritage de nouveau à partir du nouveau type d’applications Mobile `StorageController<T>` à votre projet de serveur.

>[AZURE.TIP] Ce didacticiel présente un [exemple de guide](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/) disponibles, qui peut être déployé pour votre propre compte Azure. 

## <a name="prerequisites"></a>Conditions préalables

* Terminer le didacticiel de [créer une application de Xamarin.Forms] , qui répertorie les autres composants requis. Cet article utilise l’application terminée à partir de ce didacticiel.

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant de vous inscrivez pour un compte Azure, accédez à [Essayer le Service application](https://tryappservice.azure.com/?appServiceName=mobile). Vous pouvez créer une application mobile de courte durée starter immédiatement dans le Service d’application : aucune carte de crédit requise et aucun des engagements.

## <a name="create-a-storage-account"></a>Créer un compte de stockage

1. Créer un compte de stockage en suivant le didacticiel, [créez un compte de stockage Azure]. 

2. Dans le portail Azure, accédez à votre compte de stockage nouvellement créé et cliquez sur l’icône de **clés** . Copier la **chaîne de connexion principal**.

3. Naviguez jusqu'à votre back-end de l’application mobile. **Tous**ces paramètres -> **Paramètres de l’Application** -> de**Chaînes de connexion**, créez une nouvelle clé nommée `MS_AzureStorageAccountConnectionString` et utiliser la valeur copiée à partir de votre compte de stockage. Utiliser l’option **Personnaliser** comme le type de clé.

## <a name="add-a-storage-controller-to-the-server"></a>Ajouter un contrôleur de stockage au serveur

Vous devez ajouter un nouveau contrôleur à votre projet de serveur qui va répondre à des demandes pour un jeton SAS pour le stockage Azure, ainsi que retourner une liste de fichiers qui correspondent à un enregistrement :

- [Ajouter un contrôleur de stockage à votre projet de serveur](#add-controller-code)
- [Itinéraires inscrits par le contrôleur de stockage](#routes-registered)
- [Communication client-serveur](#client-communication)

###<a name="add-controller-code"></a>Ajouter un contrôleur de stockage à votre projet de serveur

1. Dans Visual Studio, ouvrez votre projet de serveur .NET. Ajoutez le package Nuget [Microsoft.Azure.Mobile.Server.Files]. Veillez à sélectionner **inclure la version préliminaire**.

2. Dans Visual Studio, ouvrez votre projet de serveur .NET. Cliquez sur le dossier **Controllers** et sélectionnez **Ajouter** -> **contrôleur** -> **Contrôleur de 2 API Web - vide**. Nommez le contrôleur `TodoItemStorageController`.

3. Ajoutez le code suivant à l’aide des instructions :

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. Modifier la classe de base de `StorageController`:
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. Ajoutez les méthodes suivantes à la classe :

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. Mettre à jour la configuration de l’API Web pour définir le routage de l’attribut. Dans **Startup.MobileApp.cs**, ajoutez la ligne suivante à la `ConfigureMobileApp()` méthode, après la définition de la `config` variable :

        config.MapHttpAttributeRoutes();

7. Publier votre projet serveur sur principale de votre application mobile.

###<a name="routes-registered"></a>Itinéraires inscrits par le contrôleur de stockage

Les nouveaux `TodoItemStorageController` expose deux ressources secondaires sous l’enregistrement qu’il gère :

- StorageToken

    + HTTP POST : Crée un jeton de stockage
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles

    + HTTP GET : Récupère une liste des fichiers associés à l’enregistrement
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + HTTP, supprimer : Supprime le fichier spécifié par l’identificateur de ressource de fichier
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###<a name="client-communication"></a>Communication client-serveur

Notez que `TodoItemStorageController` est *pas* dispose d’un itinéraire pour le transfert ou le téléchargement d’un objet blob. C’est parce qu’un client mobile interagit avec le blob stockage *directement* pour effectuer ces opérations, après la première mise en route d’un jeton SAS (Signature accès partagé) pour accéder de manière sécurisée un blob donné ou un conteneur. Il s’agit d’une conception architecturale importante, comme l’accès au stockage dans le cas contraire serait limité par l’évolutivité et la disponibilité du serveur principal mobile. Au lieu de cela, en vous connectant directement au stockage Azure, le client mobile peut profiter de ses fonctionnalités telles que le partitionnement automatique et la distribution géographique.

Une signature accès partagé fournit le délégué l’accès à des ressources dans votre compte de stockage. Cela signifie que vous pouvez accorder à qu'un client des autorisations limitées d’objets dans votre compte de stockage pour une période donnée de temps et avec un jeu d’autorisations spécifié sans avoir à partager les touches d’accès de votre compte. Pour plus d’informations, consultez [Présentation des Signatures accès partagé].

Le schéma ci-dessous illustre les interactions client et serveur. Avant de télécharger un fichier, le client demande un jeton d’associations de sécurité du service. Le service utilise la chaîne de connexion de stockage pour générer un nouveau SAS, qu’il retourne ensuite au client. Les associations de sécurité est limitée dans le temps et limite les autorisations pour qu’un fichier particulier ou un conteneur. Le client mobile utilise ensuite ce SAS et le client de stockage Azure SDK pour télécharger le fichier pour le stockage des objets blob.

![Demande un jeton SAS](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## <a name="update-your-client-app-to-add-image-support"></a>Mise à jour de votre application cliente pour ajouter la prise en charge de l’image

Xamarin Studio ou Visual Studio, ouvrez le projet de démarrage rapide de Xamarin.Forms. Vous installez les packages Nuget et mettre à jour le projet de bibliothèque portable et l’iOS, Android et Windows projets clients :

- [Ajouter des packages Nuget](#add-nuget)
- [Ajouter une IPlatform interface](#add-iplatform)
- [Ajouter une classe de FileHelper](#add-filehelper)
- [Ajouter un gestionnaire de synchronisation de fichiers](#file-sync-handler)
- [Mise à jour TodoItemManager](#update-todoitemmanager)
- [Ajouter une vue de détails](#add-details-view)
- [Mise à jour de la vue principale](#update-main-view)
- [Mettre à jour le projet Android](#update-android), [iOS projet](#update-ios), [projet de Windows](#update-windows)

>[AZURE.NOTE] Ce didacticiel contient uniquement des instructions des plates-formes Windows Store, pas de Windows Phone, iOS et Android.

###<a name="add-nuget"></a>Ajouter des packages Nuget

Avec le bouton droit de la solution et sélectionnez **Nuget de gérer les packages de solution**. Ajouter les packages Nuget suivants à **tous les** projets dans la solution. Veillez à **inclure la version préliminaire**.

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

Pour plus de facilité, cet exemple utilise la bibliothèque [PCLStorage] , mais il n’est pas requis par le client d’applications Mobile de Azure SDK.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###<a name="add-iplatform"></a>Ajouter une IPlatform interface

Créer une nouvelle interface `IPlatform` dans le projet de bibliothèque de portable principal. Cela suit le modèle de [Xamarin.Forms DependencyService] pour charger la classe de droit spécifique à la plateforme au moment de l’exécution. Vous ajouterez ultérieurement des implémentations spécifiques de la plate-forme dans chaque projet client.

1. Ajoutez le code suivant à l’aide des instructions :

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. Remplacer l’implémentation par le texte suivant :

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###<a name="add-filehelper"></a>Ajouter une classe de FileHelper

1. Créer une nouvelle classe `FileHelper` dans le projet de bibliothèque de portable principal. Ajoutez le code suivant à l’aide des instructions :

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. Ajoutez la définition de classe :

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

###<a name="file-sync-handler"></a>Ajouter un gestionnaire de synchronisation de fichiers

Créer une nouvelle classe `TodoItemFileSyncHandler` dans le projet de bibliothèque de portable principal. Cette classe contient des rappels à partir du SDK Azure pour notifier votre code lorsqu’un fichier est ajouté ou supprimé.

Le SDK de Client Mobile Azure ne stocke pas réellement les données du fichier : le Kit de développement logiciel de client appelle votre implémentation de `IFileSyncHandler` qui détermine ensuite si et comment les fichiers sont stockés sur le périphérique local.

1. Ajoutez le code suivant à l’aide des instructions :

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. Remplacez la définition de classe par le suivant : 

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###<a name="update-todoitemmanager"></a>Mise à jour TodoItemManager

1. Dans **TodoItemManager.cs**, Décommentez la ligne `#define OFFLINE_SYNC_ENABLED`.

2. Dans **TodoItemManager.cs**, ajoutez le code suivant à l’aide des instructions :

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. Dans le constructeur de `TodoItemManager`, ajoutez le code suivant après l’appel à `DefineTable()`:

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. Dans le constructeur, remplacez l’appel à `InitializeAsync` par ce qui suit. Cela garantit qu’il n’y a rappels lorsque des enregistrements sont modifiés dans le magasin local. La fonctionnalité de synchronisation de fichiers utilise ces rappels pour déclencher votre gestionnaire de synchronisation de fichier.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. Dans `SyncAsync()`, ajoutez le code suivant après l’appel à `PushAsync()`:

        await this.todoTable.PushFileChangesAsync();

6. Ajoutez les méthodes suivantes pour `TodoItemManager`:

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###<a name="add-details-view"></a>Ajouter une vue de détails

Dans cette section, vous allez ajouter une nouvelle vue de détails pour un élément todo. La vue est créée lorsque l’utilisateur sélectionne un élément todo et elle permet de nouvelles images à ajouter à un élément.

1. Ajoutez une nouvelle classe **TodoItemImage** pour le projet de bibliothèque portable avec l’implémentation suivante :

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. Modifier **App.cs**. Remplacer l’initialisation de `MainPage` par ce qui suit :
    
        MainPage = new NavigationPage(new TodoList());

3. Dans **App.cs**, ajoutez la propriété suivante :

        public static object UIContext { get; set; }

4. Le projet de bibliothèque portable d’avec le bouton droit et sélectionnez **Ajouter** -> **Un nouvel élément** -> **inter-plateformes** -> **Xaml Page de formulaires**. Nom de la vue `TodoItemDetailsView`.

5. Ouvrez **TodoItemDetailsView.xaml** et remplacez le corps de la ContentPage par le suivant :

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. Modifier **TodoItemDetailsView.xaml.cs** et ajoutez le code suivant à l’aide des instructions :

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. Remplacer l’implémentation de `TodoItemDetailsView` par ce qui suit :

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###<a name="update-main-view"></a>Mise à jour de la vue principale 

Mise à jour de la vue principale pour ouvrir le mode Détails lorsqu’un élément todo est sélectionné.

Dans **TodoList.xaml.cs**, remplacez l’implémentation de `OnSelected` par ce qui suit :

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###<a name="update-android"></a>Mettre à jour le projet Android

Ajouter le code spécifique à la plate-forme au projet Android, y compris le code pour le téléchargement d’un fichier et à l’aide de l’appareil photo pour capturer une nouvelle image. 

Ce code utilise le Xamarin.Forms [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) de charger la classe de droit spécifique à la plateforme au moment de l’exécution.

1. Ajoutez le composant **Xamarin.Mobile** au projet Android.

2. Ajoutez une nouvelle classe `DroidPlatform` avec l’implémentation suivante. Remplacez « Votre_espace_de_noms » avec l’espace de noms principal de votre projet.

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Modifier **MainActivity.cs**. Dans `OnCreate`, ajoutez le code suivant avant l’appel à `LoadApplication()`:

        App.UIContext = this;

###<a name="update-ios"></a>Mettre à jour le projet d’e/s

Ajouter code propre à la plate-forme pour le projet d’e/s.

1. Ajouter le composant **Xamarin.Mobile** dans le projet d’e/s.

2. Ajoutez une nouvelle classe `TouchPlatform` avec l’implémentation suivante. Remplacez « Votre_espace_de_noms » avec l’espace de noms principal de votre projet.

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Modifier **AppDelegate.cs** et supprimez les commentaires de l’appel à `SQLitePCL.CurrentPlatform.Init()`.

###<a name="update-windows"></a>Mettre à jour le projet Windows

1. Installez l’extension Visual Studio [SQLite pour Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). Pour plus d’informations, consultez le didacticiel [permettent la synchronisation hors connexion pour votre application Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md). 

2. Modifier **Package.appxmanifest** et vérifiez la capacité de la **Webcam** .

3. Ajoutez une nouvelle classe `WindowsStorePlatform` avec l’implémentation suivante. Remplacez « Votre_espace_de_noms » avec l’espace de noms principal de votre projet.

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##<a name="summary"></a>Résumé

Cet article décrit comment utiliser la nouvelle prise en charge des fichiers dans le client d’Azure Mobile et le Kit de développement logiciel avec stockage Azure. 

- Créer un compte de stockage et d’ajouter la chaîne de connexion à votre back-end de l’application mobile. Seul le serveur principal dispose de la clé de stockage Azure : le client mobile demande un jeton SAS (Signature de l’accès partagé) chaque fois qu’il doit accéder au stockage d’Azure. Pour en savoir plus sur les jetons de SAS dans le stockage Azure, consultez [Présentation des Signatures accès partagé].

- Création d’un contrôleur qui sous-classe `StorageController` pour traiter les demandes de jeton d’associations de sécurité et pour obtenir les fichiers qui sont associés à un enregistrement. Par défaut, les fichiers sont associés à un enregistrement à l’aide de l’ID d’enregistrement en tant que partie du nom de conteneur ; le comportement peut être personnalisé en spécifiant une implémentation de `IContainerNameResolver`. La stratégie de jeton SAS peut également être personnalisée.

- Le SDK de Client Mobile Azure ne stocke pas réellement de stocker les données de fichier. Au lieu de cela, le client SDK appelle votre `IFileSyncHandler`, qui décide comment (et si) les fichiers sont stockés sur le périphérique local. Le Gestionnaire de synchronisation est enregistré comme suit :

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + `IFileSyncHandler.GetDataSource`est appelé lorsque le SDK de Client Mobile Azure doit les données du fichier (par exemple, dans le cadre du processus de téléchargement). Cela vous donne la possibilité de gérer comment (et si) les fichiers sont stockés sur le périphérique local et retourner cette information lorsque cela est nécessaire.

      + `IFileSyncHandler.ProcessFileSynchronizationAction`est appelé dans le cadre du flux de synchronisation de fichier. Une référence de fichier et une valeur d’énumération FileSynchronizationAction sont fournies afin que vous puissiez décider comment votre application doit gérer cet événement (par exemple télécharger automatiquement un fichier lorsqu’il est créé ou mis à jour, la suppression d’un fichier à partir du périphérique local lorsque ce fichier est supprimé sur le serveur).

- A `MobileServiceFile` peut être utilisé en mode en ligne ou hors ligne, à l’aide d’un `IMobileServiceTable` ou `IMobileServiceSyncTable`, respectivement. Dans le scénario en mode hors connexion, le téléchargement se produit lorsque l’application appelle `PushFileChangesAsync`. Dans ce cas, la file d’attente de l’opération hors connexion être traitée ; pour chaque opération de fichier, le client Mobile d’Azure SDK appellera le `GetDataSource` méthode sur le `IFileSyncHandler` instance pour récupérer le contenu du fichier pour le téléchargement.

- Pour extraire les fichiers d’un élément, appelez la « GetFilesAsync` method on the  `IMobileServiceTable<T> ` or IMobileServiceSyncTable<T>` instance. Cette méthode retourne une liste des fichiers associés à l’élément de données. (Remarque : il s’agit d’une opération *locale* et retourne les fichiers en fonction de l’état de l’objet lors de la dernière synchronisation. Pour obtenir une liste mise à jour des fichiers à partir du serveur, vous devez lancer une opération de synchronisation tout d’abord.)

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- La fonctionnalité de synchronisation de fichier utilise des notifications de modification de l’enregistrement dans le magasin local afin de récupérer les enregistrements que le client a reçu dans le cadre d’une opération d’envoi ou d’extraction. Pour cela, en activant les notifications locale et serveur du contexte de synchronisation à l’aide de la `StoreTrackingOptions` paramètre. 

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + Autres options de suivi de banque d’informations sont disponibles, telles que les notifications local uniquement ou serveur uniquement. Vous pouvez ajouter ou propre rappel personnalisé à l’aide de la `EventManager` propriété du `IMobileServiceClient`:

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- Il est possible d’ajouter ou de supprimer des fichiers à partir d’un enregistrement en modifiant le stockage blob directement, étant donné que l’association est réalisée via une convention d’affectation de noms. Toutefois, dans ce cas vous devez toujours **mise à jour de l’horodatage d’enregistrement lorsque les blobs associés sont modifiés**. Le Kit de développement logiciel du client Mobile d’Azure actualise toujours un enregistrement lors de l’ajout ou la suppression d’un fichier. 

    La raison à cela est que certains clients mobiles aura déjà l’enregistrement dans le stockage local. Lorsque les clients effectuent une extraction incrémentielle, cet enregistrement ne sera pas retourné et le client n’interrogera pas pour les nouveaux fichiers associés. Pour éviter ce problème, il est recommandé de mettre à jour le datage d’enregistrement lors de l’exécution de toute modification du stockage blob qui n’utilise pas le Kit de développement logiciel du client Mobile d’Azure.

- Le projet client utilise le modèle de [DependencyService de Xamarin.Forms] de charger la classe de droit spécifique à la plateforme au moment de l’exécution. Dans cet exemple, nous avons défini une interface `IPlatform` avec les implémentations dans chacun des projets propres à la plate-forme.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Créer une application de Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Présentation des Signatures d’accès partagés]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Créer un compte de stockage Azure]:  ../storage/storage-create-storage-account.md#create-a-storage-account
