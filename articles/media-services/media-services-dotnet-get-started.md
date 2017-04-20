<properties
    pageTitle="Mise en route de fourniture de contenu à la demande à l’aide de .NET | Azure"
    description="Ce didacticiel vous guide à travers les étapes de l’implémentation d’une application de diffusion de contenu sur demande avec Azure Media Services à l’aide de .NET."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/17/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Mise en route de fourniture de contenu à la demande à l’aide du Kit de développement .NET

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

>[AZURE.NOTE]
> Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 
 
##<a name="overview"></a>Vue d’ensemble 

Ce didacticiel vous guide à travers les étapes de l’implémentation d’une application de diffusion de contenu vidéo à la demande (VoD) à l’aide d’Azure Media Services (AMS) SDK pour .NET.


Le didacticiel présente le flux de travail des Services de support et des objets de programmation courants plus les tâches requises pour le développement de Services de support. À la fin de ce didacticiel, vous serez en mesure de flux ou de téléchargement progressif d’un exemple de fichier multimédia que vous avez téléchargé, codés, téléchargé.

## <a name="what-youll-learn"></a>Vous allez apprendre

Ce didacticiel montre comment accomplir les tâches suivantes :

1.  Créez un compte de Services de support (en utilisant le portail Azure).
2.  Configurer le point de terminaison en continu (en utilisant le portail Azure).
3.  Créer et configurer un projet Visual Studio.
5.  Se connecter sur le compte de Services de support.
6.  Créer une nouvelle immobilisation et télécharger un fichier vidéo.
7.  Encoder le fichier source dans un ensemble de fichiers MP4 de débit adaptatif.
8.  Publier l’actif et obtenir l’URL pour le téléchargement progressif et de transmission en continu.
9.  Test par la lecture du contenu.

## <a name="prerequisites"></a>Conditions préalables

La configuration suivante est requise pour ce didacticiel.

- Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. 
    
    Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Vous obtenez des crédits qui peuvent être utilisés pour tester des services Azure payés. Même après que les crédits sont épuisés, vous pouvez conserver le compte et utiliser des services Azure gratuits et des fonctionnalités, telles que la fonctionnalité d’applications Web dans le Service d’application Azure.
- Systèmes d’exploitation : Windows 8 ou version ultérieure, Windows 2008 R2, Windows 7.
- .NET Framework version 4.0 ou ultérieure
- Visual Studio 2010 SP1 (Professionnel, Premium, Ultimate ou Express) ou une version ultérieure.


##<a name="download-sample"></a>Télécharger l’exemple

Obtenir et exécuter un exemple à partir [d’ici](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Créer un compte Azure Media Services via le portail Azure

Les étapes de cette section montrent comment créer un compte de l’AMS.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ nouvelle** > **Media + CDN** > **Media Services**.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Dans **Créer un compte de SERVICES de support** , entrez les valeurs requises.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Dans la zone **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte de Services de support est de tous les nombres en minuscules ou lettres sans espace et 3 à 24 caractères.
    2. Abonnement, sélectionnez parmi les différents abonnements Azure auxquelles vous avez accès à.
    
    2. Dans le **Groupe de ressources**, sélectionnez la ressource nouvelle ou existante.  Un groupe de ressources est un ensemble de ressources qui partagent le cycle de vie, les autorisations et les stratégies. Pour en savoir plus [ici](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Dans **emplacement**, sélectionnez la région géographique est utilisé pour stocker les enregistrements des supports et des métadonnées pour votre compte de Services de support. Cette zone est utilisée pour traiter et de diffuser vos médias. Seules les régions de Media Services disponibles s’affichent dans la zone de liste déroulante. 
    
    3. Dans le **Compte de stockage**, sélectionnez un compte de stockage pour fournir le stockage blob du contenu multimédia à partir de votre compte de Services de support. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique, comme votre compte de Services de support, ou vous pouvez créer un compte de stockage. Un nouveau compte de stockage est créé dans la même région. Les règles pour les noms de compte de stockage sont les mêmes que pour les comptes de Services de support.

        En savoir plus sur le stockage [ici](storage-introduction.md).

    4. Sélectionnez **Ajouter au tableau de bord** pour voir la progression du déploiement compte.
    
7. Cliquez sur **créer** en bas de l’écran.

    Une fois que le compte est créé avec succès, le statut devient **en cours d’exécution**. 

    ![Paramètres des Services de support](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Pour gérer votre compte AMS (par exemple, télécharger des vidéos, coder actifs, surveiller la progression de la tâche) utilisez la fenêtre **paramètres** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurer des points de terminaison en continu via le portail Azure

Lorsque vous travaillez avec Azure Media Services est un des scénarios plus courants est offrant des performances vidéo via adaptive vitesse de transmission en continu à vos clients. Media Services prend en charge la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement).

Media Services fournit un emballage dynamique, ce qui vous permet de remettre votre contenu MP4 encodé en continu des formats pris en charge par les Services de support (MPEG tiret, TLS, diffusion en continu lisse, HDS) juste-à-temps, sans avoir à stocker des versions intégrées de chacun de ces formats de transmission en continu de vitesse de transmission adaptive.

Pour tirer parti de l’emballage dynamique, vous devez effectuer les opérations suivantes :

- Coder votre fichier mezzanine (source) dans un ensemble de fichiers MP4 de débit adaptatif (les étapes de codage sont expliqués plus loin dans ce didacticiel).  
- Créer au moins une unité de transmission en continu pour le *point de terminaison de la diffusion en continu* à partir de laquelle vous envisagez de livraison votre contenu. Les étapes ci-dessous montrent comment modifier le nombre d’unités de transmission en continu.

Avec emballage dynamique, il vous suffit de stocker et de payer pour les fichiers au format de stockage unique et Media Services génère et remplit la réponse appropriée en fonction de la demande du client.

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, effectuez les opérations suivantes :


1. Dans la fenêtre **paramètres** , cliquez sur **les points de terminaison en flux continu**. 

2. Cliquez sur la valeur par défaut de diffusion en continu de point de terminaison. 

    La fenêtre **Détails de point de terminaison de diffusion en continu par défaut** s’affiche.

3. Pour spécifier le nombre d’unités de transmission en continu, faites glisser le curseur de **l’unité de transmission en continu** .

    ![Unités de transmission en continu](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

    >[AZURE.NOTE]L’allocation de toutes nouvelles unités peut prendre jusqu'à 20 minutes.

##<a name="create-and-configure-a-visual-studio-project"></a>Créer et configurer un projet Visual Studio

1. Créez une nouvelle Application de Console C# dans Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1. Entrez les **nom**, **emplacement**et **nom de la Solution**, puis cliquez sur **OK**.

2. Utilisez le package NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) pour installer **Les Extensions .NET SDK Azure Media Services**.  Les Extensions de Media Services .NET SDK est un ensemble de méthodes d’extension et des fonctions d’assistance qui seront simplifier votre code et le rendre plus facile à développer avec Media Services. L’installation de ce package, également installe le **Kit de développement logiciel Media Services .NET** et ajoute toutes les autres dépendances requises.

3. Ajoutez une référence à l’assembly System.Configuration. Cet assembly contient la classe **System.Configuration.ConfigurationManager** qui est utilisée pour accéder aux fichiers de configuration, par exemple, App.config.

4. Ouvrez le fichier App.config (ajouter le fichier à votre projet si elle n’a pas été ajoutée par défaut) et ajouter une section *appSettings* du fichier. Définissez les valeurs pour votre Azure Media Services compte nom et compte la clé, comme illustré dans l’exemple suivant. Pour obtenir le nom de compte et les informations de clé, accédez au [portail Azure](https://portal.azure.com/) et sélectionnez votre compte de l’AMS. Puis, sélectionnez **paramètres** > **clés**. Les fenêtres clés de gestion indique le nom de compte et les clés primaires et secondaires s’affiche.

        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
          
        </configuration>

5. Remplacer les instructions **using** existantes au début du fichier Program.cs par le code suivant.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        

6. Créer un nouveau dossier sous le répertoire de projets et de copier un fichier que vous souhaitez encoder et diffuser ou télécharger progressivement .mp4 ou .wmv. Dans cet exemple, le chemin d’accès « C:\VideoFiles » est utilisé.

##<a name="connect-to-the-media-services-account"></a>Se connecter sur le compte de Services de support

Lorsque vous utilisez les Services de support avec .NET, vous devez utiliser la classe **CloudMediaContext** pour les Services de support la plupart des tâches de programmation : connexion à un compte de Services de support ; Création, mise à jour, accès et suppression des objets suivants : actifs, des fichiers, tâches, des stratégies d’accès, repères, etc..

Remplacer la classe du programme par défaut par le code suivant. Le code montre comment lire les valeurs de connexion dans le fichier App.config et la création de l’objet **CloudMediaContext** pour vous connecter à des Services multimédias. Pour plus d’informations sur la connexion à des Services multimédias, consultez [connexion à des Services multimédias avec le Kit de développement de Services de support pour .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

La fonction **Main** appelle les méthodes qui seront définies plus loin dans cette section.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##<a name="create-a-new-asset-and-upload-a-video-file"></a>Créer une nouvelle immobilisation et le téléchargement d’un fichier vidéo

Dans les Services de support, vous charger (ou d’acquisition) vos fichiers numériques dans une immobilisation. L’entité **Asset** peut contenir, vidéo, audio, images, collections de miniatures, texte pistes et sous-titres fichiers (et les métadonnées relatives à ces fichiers.)  Une fois que les fichiers sont téléchargés, votre contenu est stocké en toute sécurité dans le nuage pour la poursuite du traitement et la diffusion en continu. Les fichiers de l’actif sont appelés **Fichiers de composants**.

La méthode **UploadFile** définie ci-après appelle **CreateFromFile** (défini dans les Extensions du Kit de développement .NET). **CreateFromFile** crée une nouvelle immobilisation dans lequel le fichier source spécifié est chargé.

La méthode **CreateFromFile** prend **AssetCreationOptions** qui vous permet de spécifier l’une des options de création d’immobilisation suivantes :

- **Aucun** - aucun chiffrement n’est utilisé. Il s’agit de la valeur par défaut. Notez que lorsque vous utilisez cette option, votre contenu n’est pas protégé en transit ou au repos dans le stockage.
Si vous souhaitez fournir un MP4 utilisant le téléchargement progressif, utilisez cette option.
- **StorageEncrypted** - Utilisez cette option pour chiffrer votre contenu clair localement à l’aide du cryptage de-256 bits de Advanced Encryption Standard (AES), qui télécharge puis sur stockage Azure où il est stocké cryptée au repos. Les ressources protégées par un cryptage de stockage sont automatiquement et placés dans un système de fichiers cryptés avant le codage et non éventuellement cryptés avant de le télécharger comme une nouvelle immobilisation de sortie. Cas d’usage principal pour le cryptage du stockage est lorsque vous souhaitez sécuriser vos fichiers de média d’entrée de haute qualité avec le cryptage fort au repos sur le disque.
- **CommonEncryptionProtected** - Utilisez cette option si vous téléchargez le contenu qui a été déjà chiffré et protégé par DRM PlayReady (par exemple, diffusion en continu lisse protégé avec PlayReady DRM) ou de cryptage commun.
- **EnvelopeEncryptionProtected** – Utilisez cette option si vous téléchargez TLS cryptée avec AES. Notez que les fichiers doivent ont été codés et chiffrés par Gestionnaire de transformation.

La méthode **CreateFromFile** vous permet également de spécifier un rappel pour signaler la progression du téléchargement du fichier.

Dans l’exemple suivant, nous spécifier **None** pour les options de l’immobilisation.

Ajoutez la méthode suivante à la classe Program.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


##<a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Coder le fichier source dans un ensemble de fichiers MP4 de vitesse de transmission adaptive

Après ingestion actifs dans les Services de support, le support peut être codé, transmuxed, filigrane et ainsi de suite, avant sa livraison aux clients. Ces activités sont planifiées et exécutées par rapport à plusieurs instances de rôle en arrière-plan pour assurer la disponibilité et des performances élevées. Ces activités sont appelées tâches, et chaque tâche se compose de tâches atomiques qui effectuent le travail réel sur le fichier actif.

Comme mentionné précédemment, lorsque vous travaillez avec Azure Media Services, un des scénarios plus courants a décidé de vitesse de transmission adaptative en continu à vos clients. Media Services peut empaqueter dynamiquement d’un ensemble de fichiers de la vitesse de transmission adaptive MP4 dans un des formats suivants : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement).

Pour tirer parti de l’emballage dynamique, vous devez effectuer les opérations suivantes :

- Coder ou transcoder des fichiers votre mezzanine (source) dans un ensemble de fichiers de vitesse de transmission adaptive MP4 ou les fichiers de diffusion en continu lisse de débit adaptatif.  
- Obtenir au moins une unité de transmission en continu pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu.

Le code suivant montre comment soumettre une tâche de codage. Le projet contient une tâche qui spécifie le fichier de la mezzanine pour transcoder dans un ensemble de transmission adaptive MP4s à l’aide de **Media Encoder Standard**. Le code soumet le travail et attend jusqu'à ce qu’elle est terminée.

Une fois la tâche terminée, vous ne pourrez pas vos ressources de flux ou de télécharger des fichiers MP4 qui ont été créés à la suite de transcodage progressivement.
Notez que vous n’avez pas besoin d’avoir plus de 0 unités de transmission en continu pour télécharger progressivement les fichiers MP4.

Ajoutez la méthode suivante à la classe Program.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {
    
        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.
    
        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);
    
        Console.WriteLine("Submitting transcoding job...");
    
    
        // Submit the job and wait until it is completed.
        job.Submit();
    
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;
    
        Console.WriteLine("Transcoding job finished.");
    
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        return outputAsset;
    }

##<a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publier l’actif et obtenir l’URL pour le téléchargement progressif et de transmission en continu

Pour transmettre en continu ou télécharger un actif, vous devez d’abord « publier » en créant un localisateur. Localisateurs de fournissent l’accès aux fichiers contenus dans l’immobilisation. Media Services prend en charge deux types de localisateurs : localisateurs de OnDemandOrigin, utilisés pour diffuser des médias (par exemple, MPEG tiret, TLS ou diffusion en continu lisse) et localisateurs de Signature accès (SAS), utilisé pour télécharger des fichiers multimédias.

Après avoir créé les repères, vous pouvez créer les URL qui sont utilisées pour transmettre en continu ou télécharger vos fichiers.


Une URL de transmission en continu pour la diffusion en continu lisse a le format suivant :

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Une URL de transmission en continu pour TLS a le format suivant :

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Une URL de transmission en continu pour le MPEG tiret a le format suivant :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Une URL de SAS permet de télécharger des fichiers a le format suivant :

    {blob container name}/{asset name}/{file name}/{SAS signature}

Les extensions de Media Services SDK .NET fournissent des méthodes d’assistance pratique qui renvoient l’URL mise en forme pour la ressource publiée.

Le code suivant utilise les Extensions du Kit de développement .NET pour créer des repères et obtenir la diffusion en continu et l’URL de téléchargement progressif. Le code montre également comment faire pour télécharger des fichiers dans un dossier local.

Ajoutez la méthode suivante à la classe Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##<a name="test-by-playing-your-content"></a>Test par la lecture du contenu  

Une fois que vous exécutez le programme défini dans la section précédente, les URL semblables au suivant seront affichera dans la fenêtre de console.

URL de diffusion adaptative :

Diffusion en continu lisse

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

TLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

TIRET DE MPEG

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URL de téléchargement progressif (audio et vidéo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Pour diffuser une vidéo vous, utilisez [Le lecteur Media Services Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Pour tester le téléchargement progressif, collez une adresse URL dans un navigateur (par exemple, Internet Explorer, Chrome ou Safari).


##<a name="next-steps-media-services-learning-paths"></a>Les étapes suivantes : Media Services, voies de formation

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### <a name="looking-for-something-else"></a>Vous recherchez quelque chose d’autre ?

Si cette rubrique ne contenait pas ce que vous attendiez, il manque quelque chose, ou dans une autre façon n’a pas répondre à vos besoins, veuillez nous fournir vos commentaires à l’aide du thread de Disqus ci-dessous.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/
