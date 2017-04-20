<properties
    pageTitle="Comment rogner une vidéo | Microsoft Azure"
    description="Cet article indique comment rogner des vidéos avec Media Encoder Standard."
    services="media-services"
    documentationCenter=""
    authors="anilmur"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"  
    ms.author="anilmur;juliako;"/>

# <a name="crop-videos-with-media-encoder-standard"></a>Vidéos de recadrage avec Media Encoder Standard

Vous pouvez utiliser Media Encoder Standard (MES) pour rogner votre entrée vidéo. Le recadrage consiste à sélectionner une fenêtre rectangulaire dans l’image et le codage uniquement les pixels dans cette fenêtre. Le diagramme suivant permet d’illustrer le processus.

![Rogner une vidéo](./media/media-services-crop-video/media-services-crop-video01.png)

Supposons que vous ayez comme entrée une vidéo qui a une résolution de 1920 x 1080 pixels (format d’image 16:9), mais des bandes noires (zones de pilier) à gauche et à droite, pour qu’une fenêtre de 4:3 ou de 1440 x 1080 pixels contienne vidéo active. Vous pouvez utiliser MES rogner ou modifiez les barres noires et coder la région 1440 x 1080.

Découpage de l’économie de marché est une étape de prétraitement, les paramètres de rognage dans le préréglage de codage s’appliquent à la vidéo d’entrée d’origine. Le codage est à un stade ultérieur, et les paramètres de largeur/hauteur s’appliquent pour la *Pre-traitement* vidéo et pas la vidéo d’origine. Lorsque vous concevez votre préconfiguration que vous devez effectuer les opérations suivantes : (un), sélectionnez les paramètres de culture en fonction de la vidéo d’entrée d’origine et (b), sélectionnez votre encode les paramètres en fonction de la vidéo rognée. Si vous ne correspondent pas à vos réglages vidéo rognée de codage, la sortie ne sera pas comme prévu.

La rubrique [suivante](media-services-advanced-encoding-with-mes.md#encoding_with_dotnet) montre comment créer une tâche de codage avec une économie de marché et comment spécifier un paramètre prédéfini de la tâche de codage. 

## <a name="creating-a-custom-preset"></a>Création d’une préconfiguration personnalisée

Dans l’exemple illustré dans le diagramme :

1. Entrée d’origine est 1920 x 1080.
1. Il doit être tronqué à une sortie de 1440 x 1080, qui est centrée dans le cadre d’entrée
1. Cela signifie que l’offset X (1920 – 1440) / 2 = 240 et Y offset de zéro
1. La largeur et la hauteur du rectangle de recadrage sont respectivement 1440 et 1080,
1. Dans la phase de codage, la poser est de produire de trois couches, sont respectivement les résolutions 1440 x 1080, 960 x 720 et 480 x 360,

###<a name="json-preset"></a>Préréglage JSON


    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
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
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
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
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
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


##<a name="restrictions-on-cropping"></a>Restrictions sur le rognage

La fonctionnalité de rognage est censée être manuelle. Vous devrez charger votre vidéo d’entrée dans un outil d’édition approprié qui vous permet de sélectionner des images d’intérêt, positionnez le curseur afin de déterminer les décalages pour le rectangle de recadrage, pour déterminer le préréglage de codage est réglé pour que des vidéos, etc. particulier. Cette fonctionnalité n’est pas conçue pour permettre des éléments tels que : détection automatique et la suppression des bordures noires letterbox/pillarbox dans votre entrée vidéo.

Les contraintes suivantes s’appliquent à la fonction de rognage. Si elles ne sont pas remplies, la tâche de codage peut échouer ou produire un résultat inattendu.

1. Les coordonnées et la taille du rectangle de recadrage ont tenir dans la vidéo d’entrée
1. Comme mentionné ci-dessus, la largeur et la hauteur dans les paramètres d’encodage doivent correspondre à la vidéo rognée
1. Rognage s’applique aux vidéos capturées en mode paysage (c'est-à-dire détenus ne s’applique pas à des vidéos enregistrées avec un smartphone, verticalement ou en mode portrait)
1. Utilisation optimale avec la vidéo progressive capturée avec des pixels carrés

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Étape suivante
 
Consultez Azure Media Services parcours de formation pour vous aider à en savoir plus sur les fonctionnalités offertes par l’AMS.  

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
