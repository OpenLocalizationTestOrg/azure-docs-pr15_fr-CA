<properties
    pageTitle="Comment faire pour utiliser le stockage Blob de Xamarin | Microsoft Azure"
    description="La bibliothèque de Client de stockage Azure pour Xamarin permet aux développeurs de créer des iOS, Android et Windows applications Store avec leurs interfaces utilisateur natives. Ce didacticiel explique comment utiliser Xamarin pour créer une application qui utilise le stockage des objets Blob Azure."
    services="storage"
    documentationCenter="xamarin"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-xamarin"></a>Comment faire pour utiliser le stockage Blob à partir de Xamarin

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Vue d’ensemble

Xamarin, permet aux développeurs d’utiliser C# partagé codebase pour créer iOS, Android et Windows applications Store avec leurs interfaces utilisateur natives. Ce didacticiel vous montre comment utiliser le stockage des objets Blob Azure avec une application de Xamarin. Si vous souhaitez en savoir plus sur le stockage Azure, avant de plonger dans le code, consultez [Introduction au stockage Azure de Microsoft](storage-introduction.md).

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Créez une nouvelle Application de Xamarin

Pour cette mise en route, nous allons créer une application qui cible Windows, iOS et Android. Cette application sera simplement créer un conteneur et télécharger un blob dans ce conteneur. Nous utiliserons Visual Studio sous Windows pour cette mise en route, mais les apprentissages mêmes peuvent être appliquées lors de la création d’une application à l’aide de la Xamarin Studio sous Mac OS.

Suivez ces étapes pour créer votre application :

1. Si vous ne l’avez pas déjà fait, téléchargez et installez [Xamarin pour Visual Studio](https://www.xamarin.com/download).
2. Ouvrez Visual Studio et créer une application vide (partagé natif) : **fichier > Nouveau > projet > multiplates-> App(Native Shared) vide**.
3. Avec le bouton droit de votre solution dans le volet de l’Explorateur de solutions et sélectionnez **Manage NuGet Packages de Solution**. Recherchez **WindowsAzure.Storage** et installer la dernière version disponible pour tous les projets de votre solution.
4. Générez et exécutez votre projet.

Vous devez maintenant avoir une application qui vous permet de cliquer sur un bouton qui incrémente un compteur.

> [AZURE.NOTE] La bibliothèque de Client de stockage Azure pour Xamarin prend actuellement en charge les types de projet suivants : partagés natif, partagés de Xamarin.Forms, Xamarin.Android et Xamarin.iOS.

## <a name="create-container-and-upload-blob"></a>Conteneur de créer et de télécharger des blob

Ensuite, vous allez ajouter du code à la classe partagée `MyClass.cs` qui crée un conteneur et télécharge un blob dans ce conteneur. `MyClass.cs`doit se présenter comme suit :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;

    namespace XamarinApp
    {
        public class MyClass
        {
            public MyClass ()
            {
            }

            public static async Task createContainerAndUpload()
            {
                // Retrieve storage account from connection string.
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

                // Create the blob client.
                CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

                // Retrieve reference to a previously created container.
                CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

                // Create the container if it doesn't already exist.
                await container.CreateIfNotExistsAsync();

                // Retrieve reference to a blob named "myblob".
                CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

                // Create the "myblob" blob with the text "Hello, world!"
                await blockBlob.UploadTextAsync("Hello, world!");
            }
        }
    }

Veillez à remplacer « your_account_name_here » et « your_account_key_here » avec votre nom de compte et votre clé de compte. Vous pouvez ensuite utiliser cette classe partagée dans votre iOS, Android et Windows Phone application. Vous pouvez simplement ajouter `MyClass.createContainerAndUpload()` pour chaque projet. Par exemple :

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

    using Android.App;
    using Android.Widget;
    using Android.OS;

    namespace XamarinApp.Droid
    {
        [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
        public class MainActivity : Activity
        {
            int count = 1;

            protected override async void OnCreate (Bundle bundle)
            {
                base.OnCreate (bundle);

                // Set our view from the "main" layout resource
                SetContentView (Resource.Layout.Main);

                // Get our button from the layout resource,
                // and attach an event to it
                Button button = FindViewById<Button> (Resource.Id.myButton);

                button.Click += delegate {
                    button.Text = string.Format ("{0} clicks!", count++);
                };

              await MyClass.createContainerAndUpload();
            }
        }
    }

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

    using System;
    using UIKit;

    namespace XamarinApp.iOS
    {
        public partial class ViewController : UIViewController
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.createContainerAndUpload();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Navigation;

    // The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

    namespace XamarinApp.WinPhone
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.createContainerAndUpload();
            }
        }
    }


## <a name="run-the-application"></a>Exécution de l’application

Vous pouvez maintenant exécuter cette application dans un émulateur Android ou Windows Phone. Vous pouvez également exécuter cette application dans un émulateur d’e/s, mais cette opération nécessite un Mac. Pour obtenir des instructions spécifiques sur la façon de procéder, veuillez lire la documentation pour [connecter Visual Studio à un Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Une fois que vous exécutez votre application, il crée le conteneur `mycontainer` dans votre compte de stockage. Il doit contenir le blob, `myblob`, qui contient le texte, `Hello, world!`. Vous pouvez le vérifier à l’aide de l' [Explorateur de stockage Microsoft Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Étapes suivantes

Dans cette route, vous avez appris comment créer une application multi-plateforme dans Xamarin qui utilise le stockage Azure. Cette mise en route spécifiquement axée sur un seul scénario dans le stockage Blob. Toutefois, vous pouvez effectuer beaucoup plus, non seulement le stockage Blob, mais aussi avec la Table, de fichiers et de stockage de la file d’attente. Veuillez consulter les articles suivants pour en savoir plus :
- [Mise en route avec le stockage Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md)
- [Mise en route avec le stockage par Table Azure à l’aide de .NET](storage-dotnet-how-to-use-tables.md)
- [Mise en route avec le stockage Azure file d’attente à l’aide de .NET](storage-dotnet-how-to-use-queues.md)
- [Mise en route de stockage des fichiers sur Windows Azure](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]
