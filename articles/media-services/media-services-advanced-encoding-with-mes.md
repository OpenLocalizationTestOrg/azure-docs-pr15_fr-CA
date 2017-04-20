<properties 
    pageTitle="Avancée de codage avec Media Encoder Standard" 
    description="Cette rubrique montre comment effectuer un codage avancé en personnalisant Media Encoder Standard prédéfinis de tâche. La rubrique Comment Media Services SDK .NET permet de créer une tâche de codage et de travail. Il montre également comment fournir des paramètres prédéfinis personnalisés à la tâche de codage." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/25/2016"   
    ms.author="juliako"/>


#<a name="advanced-encoding-with-media-encoder-standard"></a>Avancée de codage avec Media Encoder Standard

##<a name="overview"></a>Vue d’ensemble

Cette rubrique montre comment effectuer des tâches de codage avancées avec Media Encoder Standard. La rubrique indique [comment utiliser .NET pour créer une tâche de codage et une tâche qui exécute cette tâche](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet). Il montre également comment fournir des paramètres prédéfinis personnalisés à la tâche de codage. Pour obtenir une description des éléments qui sont utilisés par les paramètres prédéfinis, consultez [ce document](https://msdn.microsoft.com/library/mt269962.aspx). 

Les paramètres prédéfinis personnalisés qui effectuent les tâches de codage suivants sont illustrés :

- [Générer des miniatures](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [Rogner une vidéo (découpage)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [Créer une superposition](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [Insérer une piste audio en mode silencieux lors de l’entrée ne comportant aucune séquence audio](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [Désactivation automatique de désentrelacement](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
- [Présélections audio uniquement](media-services-custom-mes-presets-with-dotnet.md#audio_only)
- [Concaténation de deux ou plusieurs fichiers vidéo](media-services-custom-mes-presets-with-dotnet.md#concatenate)
- [Vidéos de recadrage avec Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#crop)
- [Insérer une piste vidéo lorsque l’entrée n’affiche aucune vidéo](media-services-custom-mes-presets-with-dotnet.md#no_video)
- [Faire pivoter une vidéo](media-services-custom-mes-presets-with-dotnet.md#rotate_video)

##<a id="encoding_with_dotnet"></a>Codage avec Media Services SDK .NET

L’exemple de code suivant utilise Media Services .NET SDK pour effectuer les tâches suivantes :

- Créer une tâche de codage.
- Obtenir une référence à l’encodeur Media Encoder Standard.
- Charger la préconfiguration personnalisée XML ou JSON. Vous pouvez enregistrer le code XML ou JSON (par exemple, [XML](media-services-custom-mes-presets-with-dotnet.md#xml) ou [JSON](media-services-custom-mes-presets-with-dotnet.md#json) dans un fichier et l’utiliser le code suivant pour charger le fichier.

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
- Ajouter une tâche à la tâche. 
- Permet de spécifier la ressource d’entrée à coder.
- Créer un élément de sortie qui contient la ressource codée.
- Ajoutez un gestionnaire d’événements pour vérifier la progression de la tâche.
- Soumettre la tâche.
    
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace CustomizeMESPresests
        {
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
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();
        
                    // Encode and generate the output using custom presets.
                    EncodeToAdaptiveBitrateMP4Set(asset);
        
                    Console.ReadLine();
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
                
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");
                
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
                
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
                
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
                
                    return job.OutputMediaAssets[0];
                }
        
                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
        
                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes 
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;
        
                        af.Update();
                    }
        
                    return asset;
                }
        
        
                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);
        
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);
        
                    // Add an output asset to contain the results of the job. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        

                private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
        
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
        
                            // Display or log error details as needed.
                            break;
                        default:
                            break;
                    }
                }
        
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
            }
        }


##<a name="support-for-relative-sizes"></a>Prise en charge des tailles relatives

Lors de la génération de vignettes de celui-ci, vous n’êtes pas obligé de toujours spécifier la largeur de sortie et la hauteur en pixels. Vous pouvez les spécifier en pourcentages, dans l’intervalle [% 1,..., 100 %].

###<a name="json-preset"></a>Préréglage JSON 
    
    "Width": "100%",
    "Height": "100%"

###<a name="xml-preset"></a>Préréglage XML
    
    <Width>100%</Width>
    <Height>100%</Height>
    
##<a id="thumbnails"></a>Générer des miniatures

Cette section indique comment personnaliser une préconfiguration qui génère des miniatures. Le préréglage défini ci-dessous contient des informations sur la façon dont vous souhaitez coder votre fichier, ainsi que les informations nécessaires pour générer les miniatures. Vous pouvez prendre l’une de l’économie de marché prédéfinis documenté [ici](https://msdn.microsoft.com/library/mt269960.aspx) et ajouter du code qui génère des miniatures.  

>[AZURE.NOTE]Le **SceneChangeDetection** de la prédéfini suivante peut uniquement être défini sur true si l ' encodage à une seule vitesse de transmission vidéo. Si vous encodez une vidéo multi-bitrate et **SceneChangeDetection** la valeur true, l’encodeur renvoie une erreur.  


Pour plus d’informations sur les schémas, consultez [cette](https://msdn.microsoft.com/library/mt269962.aspx) rubrique.

Veillez à passer en revue la [Considérations relatives à la](media-services-custom-mes-presets-with-dotnet.md#considerations) section.

###<a id="json"></a>Préréglage JSON


    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
       
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a id="xml"></a>Préréglage XML


    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

###<a name="considerations"></a>Considérations relatives à la

Les considérations suivantes s’appliquent :

- L’utilisation d’horodatages explicites pour démarrer/étape/plage suppose que la source d’entrée est 1 minute au moins.
- Jpg/Png/BmpImage éléments Démarrer, étape et la plage des attributs de chaîne, il peuvent être interprétés comme :

    - Numéro de l’image s’ils sont des entiers non négatifs, par exemple « démarrer » : « 120 »,
    - Relatif au code source de durée si exprimée comme suffixe de %, par exemple « démarrer » : « 15 % », ou
    - Timestamp, si elle est exprimée sous la forme hh : mm :... mettre en forme, par exemple « démarrer » : « 00 : 01:00 »

    Vous pouvez mélanger et correspondent à des notations que vous Veuillez.
    
    En outre, début prend également en charge une Macro spéciale : {meilleur}, qui tente de déterminer la première image « intéressante » de la NOTE de contenu : (étape et plage sont ignorés lorsque début optimale est sélectionnée pour {})
    
    - Valeurs par défaut : Démarrer : {meilleures}
- Format de sortie doit être fourni explicitement pour chaque format d’Image : Jpg/Png/BmpFormat. Le cas échéant, MES correspond JpgVideo à JpgFormat et ainsi de suite. OutputFormat introduit une nouvelle Macro spécifique codec d’image : {Index}, qui doit être présente (une fois et une seule fois) pour les formats de sortie d’image.

##<a id="trim_video"></a>Rogner une vidéo (découpage)

Cette section parle de modifier les paramètres prédéfinis du codeur pour découper ou rogner la vidéo d’entrée dans laquelle l’entrée est un fichier de ce que l'on appelle mezzanine ou à la demande. L’encodeur peut également servir à découper ou rogner une immobilisation, ce qui est capturée ou archivée à partir d’un flux en direct – les détails de ce sont disponibles dans [ce blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Pour découper vos vidéos, vous pouvez prendre l’une de l’économie de marché prédéfinis documenté [ici](https://msdn.microsoft.com/library/mt269960.aspx) et modifier l’élément **Sources** (comme indiqué ci-dessous). La valeur de l’heure de début doit correspondre à l’horodatage absolue de la vidéo d’entrée. Par exemple, si la première image de la vidéo d’entrée a un horodatage de 12:00:10.000, puis StartTime doit être au moins 12:00:10.000 et supérieur. Dans l’exemple ci-dessous, nous partons du principe que la vidéo d’entrée a un horodatage de début de zéro. **Sources** doit être placé au début de la présélection. 
 
###<a id="json"></a>Préréglage JSON
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    } 

###<a name="xml-preset"></a>Préréglage XML
    
Pour découper vos vidéos, vous pouvez prendre l’une de l’économie de marché prédéfinis documenté [ici](https://msdn.microsoft.com/library/mt269960.aspx) et modifier l’élément **Sources** (comme indiqué ci-dessous).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

##<a id="overlay"></a>Créer une superposition

Le Standard Media Encoder vous permet superposer une image sur une vidéo existante. Actuellement, les formats suivants sont pris en charge : png, jpg, gif et bmp. Le préréglage défini ci-dessous est un exemple simple d’une superposition vidéo.

En plus de définir un fichier de présélections, vous devez également informer les Services de support quel fichier dans l’actif est l’image de superposition et quel fichier est à la source vidéo sur lequel vous souhaitez recouvrir l’image. Le fichier vidéo doit être le fichier **principal** . 

L’exemple .NET ci-dessus définit deux fonctions : **UploadMediaFilesFromFolder** et **EncodeWithOverlay**. La fonction UploadMediaFilesFromFolder télécharge les fichiers d’un dossier (par exemple, BigBuckBunny.mp4 et Image001.png) et définit le fichier mp4 pour être le fichier principal de l’actif. La fonction **EncodeWithOverlay** utilise le fichier de présélection personnalisé qui lui a été passé (par exemple, le préréglage qui suit) pour créer la tâche de codage. 

>[AZURE.NOTE]Limites actuelles :
>
>Le paramètre d’opacité superposition n’est pas pris en charge.
>
>Le fichier vidéo source et le fichier d’image de superposition doivent être dans le même actif, et le fichier vidéo doit être défini en tant que fichier primaire de cet actif.

###<a name="json-preset"></a>Préréglage JSON
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a name="xml-preset"></a>Préréglage XML
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


##<a id="silent_audio"></a>Insérer une piste audio en mode silencieux lors de l’entrée ne comportant aucune séquence audio

Par défaut, si vous envoyez une entrée à l’encodeur qui contient la vidéo uniquement et pas d’audio, puis l’actif de sortie contient des fichiers qui contiennent des données vidéo uniquement. Certains lecteurs ne sont peut-être pas en mesure de gérer les flux de sortie. Vous pouvez utiliser ce paramètre pour forcer l’encodeur pour ajouter une piste audio en mode silencieux à la sortie dans ce scénario.

Pour forcer l’encodeur pour produire un actif contenant une piste audio en mode silencieux lors de l’entrée ne comportant aucune séquence audio, spécifiez la valeur « InsertSilenceIfNoAudio ».

Vous pouvez prendre l’une de l’économie de marché prédéfinis documenté [ici](https://msdn.microsoft.com/library/mt269960.aspx)et apporter la modification suivante :

###<a name="json-preset"></a>Préréglage JSON

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###<a name="xml-preset"></a>Préréglage XML

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a id="deinterlacing"></a>Désactivation automatique de désentrelacement

Clients n’ont rien à faire s’ils le souhaitent le contenu entrelacé à être automatiquement déduplication entrelacé. Lors de l’auto désentrelacement est activée (par défaut) l’économie de marché est la détection automatique des images entrelacées et uniquement en interlaces des trames marquées comme entrelacé.

Vous pouvez désactiver l’auto désentrelacement. Cette option n’est pas recommandée.

###<a name="json-preset"></a>Préréglage JSON
    
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

###<a name="xml-preset"></a>Préréglage XML
    
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


##<a id="audio_only"></a>Présélections audio uniquement

Cette section montre deux Présélections d’économie de marché uniquement audio : Audio AAC et AAC une bonne qualité Audio.

###<a name="aac-audio"></a>AAC Audio 

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

###<a name="aac-good-quality-audio"></a>Audio de bonne qualité AAC

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="concatenate"></a>Concaténation de deux ou plusieurs fichiers vidéo

L’exemple suivant illustre comment vous pouvez générer un préréglage pour la concaténation de deux ou plusieurs fichiers vidéo. Le scénario le plus courant est lorsque vous souhaitez ajouter un en-tête ou une remorque à la vidéo principale. L’utilisation prévue est lorsque les fichiers vidéo en cours de modification entre eux partagent des propriétés (résolution, fréquence d’images, nombre de pistes audio, etc.). Vous devez prendre soin de ne pas pour mélanger les vidéos de différentes cadences d’images, ou avec un nombre différent de pistes audio.

###<a name="requirements-and-considerations"></a>Configuration requise et remarques

- Vidéos d’entrée doivent avoir qu’une seule piste audio.
- Vidéos d’entrée doivent avoir la même fréquence d’images.
- Vous devez télécharger vos vidéos dans des actifs distincts et définir les vidéos comme le fichier primaire dans chaque ressource.
- Vous devez connaître la durée de vos vidéos.
- Les exemples prédéfinis ci-dessous suppose que toutes les vidéos d’entrée démarrer avec un horodatage de zéro. Vous devez modifier les valeurs d’heure de début si les vidéos ont l’horodatage de début différente, comme c’est généralement le cas avec les archives live.
- Le préréglage JSON rend les références explicites aux valeurs AssetID des actifs d’entrée.
- L’exemple de code suppose que le préréglage JSON a été enregistré dans un fichier local, par exemple « C:\supportFiles\preset.json ». Il suppose également que les deux actifs ont été créés par le téléchargement de deux fichiers vidéo, et que vous connaissez les valeurs AssetID résultants.
- L’extrait de code et JSON préréglage montre un exemple de la concaténation de deux fichiers vidéo. Vous pouvez l’étendre à plus de deux vidéos par :

    1. Tâche d’appel. InputAssets.Add() à plusieurs reprises pour ajouter plus de vidéos, dans l’ordre.
    2. Modifie de fabrication correspondant à l’élément « Sources » dans le JSON, en ajoutant des entrées de plus, dans le même ordre. 


###<a name="net-code"></a>Code .NET

    
    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();
    
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");
    
    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);
    
    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    
    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

###<a name="json-preset"></a>Préréglage JSON

Mettre à jour votre paramètre prédéfini avec les ID des actifs à concaténer et le segment approprié pour chaque vidéo.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="crop"></a>Vidéos de recadrage avec Media Encoder Standard

Consultez la rubrique de [vidéos de recadrage avec Media Encoder Standard](media-services-crop-video.md) .

##<a id="no_video"></a>Insérer une piste vidéo lorsque l’entrée n’affiche aucune vidéo

Par défaut, si vous envoyez une entrée à l’encodeur qui contient uniquement les données audio et aucune vidéo, puis l’actif de sortie contient des fichiers qui contiennent des données audio uniquement. Certains lecteurs, y compris le lecteur Media Azure (voir [ce](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) peut-être pas en mesure de gérer ces flux. Vous pouvez utiliser ce paramètre pour forcer l’encodeur pour ajouter une piste vidéo monochrome à la sortie dans ce scénario. 

>[AZURE.NOTE]Forcer l’encodeur pour insérer une piste vidéo de sortie augmente la taille de la sortie de l’immobilisation, et donc le coût engagé pour la tâche de codage. Vous devez effectuer des tests pour vérifier que cette augmentation résultante a uniquement un minimisant l’impact sur votre facture mensuelle.

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Insertion de contenu vidéo avec uniquement le débit le plus faible

Supposons que vous utilisez un codage à débit plusieurs prédéfini tel que [« H264 débit plusieurs 720p »](https://msdn.microsoft.com/library/mt269960.aspx) pour coder votre catalogue d’entrée entière de diffusion en continu, qui contient une combinaison de fichiers vidéo et audio uniquement. Dans ce scénario, lorsque l’entrée n’a aucune vidéo, vous souhaiterez peut-être forcer l’encodeur pour insérer une piste vidéo monochrome à simplement le débit le plus faible, et non de l’insertion de contenu vidéo à chaque vitesse de transmission de sortie. Pour ce faire, vous devez spécifier l’indicateur « InsertBlackIfNoVideoBottomLayerOnly ».

Vous pouvez prendre l’une de l’économie de marché prédéfinis documenté [ici](https://msdn.microsoft.com/library/mt269960.aspx)et apporter la modification suivante :

#### <a name="json-preset"></a>Préréglage JSON

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Préréglage XML

    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideoBottomLayerOnly</Condition>

### <a name="inserting-video-at-all-output-bitrates"></a>Insertion de contenu vidéo à vitesses de transmission de sortie

Supposons que vous utilisez un codage à débit plusieurs prédéfini tel que [« H264 720p de débit plusieurs](https://msdn.microsoft.com/library/mt269960.aspx) pour coder votre catalogue d’entrée entière de diffusion en continu, qui contient une combinaison de fichiers vidéo et audio uniquement. Dans ce scénario, lorsque l’entrée n’a aucun signal vidéo, vous souhaiterez peut-être forcer l’encodeur pour insérer une piste vidéo monochrome à toutes les vitesses de transmission de sortie. Cela garantit que votre sortie actifs sont tous homogènes en ce qui concerne le nombre de pistes audio et des pistes vidéo. Pour ce faire, vous devez spécifier l’indicateur « InsertBlackIfNoVideo ».

Vous pouvez prendre l’une de l’économie de marché prédéfinis documenté [ici](https://msdn.microsoft.com/library/mt269960.aspx)et apporter la modification suivante :

#### <a name="json-preset"></a>Préréglage JSON

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Préréglage XML
    
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideo</Condition>

##<a id="rotate_video"></a>Faire pivoter une vidéo

Le [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) prend en charge la rotation à des angles de 0/90/180/270. Le comportement par défaut est « Auto », où il essaie de détecter la rotation de métadonnées dans le fichier vidéo entrant et compensation. **Sources de** l’élément suivant à l’un des présélections définies [ici](http://msdn.microsoft.com/library/azure/mt269960.aspx)sont les suivantes :

### <a name="json-preset"></a>Préréglage JSON

    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...
### <a name="xml-preset"></a>Préréglage XML

    <Sources>
        <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Voir aussi de [cette](https://msdn.microsoft.com/library/azure/mt269962.aspx#PreserveResolutionAfterRotation) rubrique pour plus d’informations sur comment l’encodeur interprète les paramètres de largeur et de hauteur dans le préréglage, lors du déclenche de la compensation de la rotation.

Vous pouvez utiliser la valeur « 0 » pour indiquer à l’encodeur pour ignorer les métadonnées de la rotation, le cas échéant, de l’entrée vidéo. 


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi 

[Vue d’ensemble du codage des Services multimédia](media-services-encode-asset.md)
