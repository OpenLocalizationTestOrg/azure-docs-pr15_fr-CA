<properties
    pageTitle="À l’aide de plusieurs fichiers d’entrée et les propriétés de composant avec le codeur de prime | Microsoft Azure"
    description="Cette rubrique explique comment utiliser setRuntimeProperties pour utiliser plusieurs fichiers d’entrée et de transmettre des données personnalisées au processeur de support Premium du flux de travail codeur multimédia."
    services="media-services"
    documentationCenter=""
    authors="xpouyat"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"  
    ms.author="xpouyat;anilmur;juliako"/>

# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>À l’aide de plusieurs fichiers d’entrée et les propriétés de composant avec le codeur de prime

## <a name="overview"></a>Vue d’ensemble

Il existe des scénarios dans lesquels vous deviez pour personnaliser les propriétés de composant, spécifier le contenu de la liste XML, ou envoyer plusieurs fichiers d’entrée lorsque vous envoyez une tâche avec le processeur de support **Premium du flux de travail codeur multimédia** . Quelques exemples sont :

- Superposition de texte sur la vidéo et la définition de la valeur de texte (par exemple, la date du jour) lors de l’exécution pour chaque vidéo d’entrée.
- Personnalisation de la liste XML (pour spécifier un ou plusieurs fichiers source, avec ou sans ajustement, etc..).
- Incrustation d’une image de logo sur l’entrée vidéo alors que la vidéo est encodée.

Pour informer le **Media Encoder Premium Workflow** que vous modifiez certaines propriétés du flux de travail lorsque vous créez la tâche ou que vous envoyez plusieurs fichiers d’entrée, vous devez utiliser une chaîne de configuration qui contient **setRuntimeProperties** ou **transcodeSource**. Cette rubrique explique comment les utiliser.


## <a name="configuration-string-syntax"></a>Syntaxe de chaîne de configuration

La chaîne de configuration à définir dans la tâche de codage utilise un document XML qui ressemble à ceci :

    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


Voici le code C# qui lit la configuration XML dans un fichier et le transmet à la tâche d’un projet :

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


## <a name="customizing-component-properties"></a>Personnalisation des propriétés de composant  

### <a name="property-with-a-simple-value"></a>Propriété avec une valeur simple
Dans certains cas, il est utile de personnaliser une propriété de composant avec le fichier de flux de travail qui doit être exécutée par Media Encoder Premium Workflow.

Supposons que vous avez conçu un flux de travail que le texte se superpose sur vos vidéos, et le texte (par exemple, la date du jour) est censé être définie au moment de l’exécution. Pour cela, vous pouvez envoyer le texte à définir comme la nouvelle valeur de la propriété de texte du composant de superposition de la tâche de codage. Vous pouvez utiliser ce mécanisme pour modifier d’autres propriétés d’un composant dans le flux de travail (par exemple, la position ou la couleur de l’incrustation, la vitesse de transmission du codeur de AVC, etc..).

**setRuntimeProperties** est utilisé pour substituer une propriété dans les composants de flux de travail.

Exemple :

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


### <a name="property-with-an-xml-value"></a>Propriété avec une valeur XML

Pour définir une propriété qui attend une valeur XML, encapsuler à l’aide de `<![CDATA[ and ]]>`.

Exemple :

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]Assurez-vous de placer un chariot retour juste après `<![CDATA[`.


### <a name="propertypath-value"></a>valeur de propertyPath

Dans les exemples précédents, le propertyPath a été « / fichier de média/nom de fichier d’entrée » ou « / inactiveTimeout » ou « clipListXml ».
Il s’agit, en général, le nom du composant, puis sur le nom de la propriété. Le chemin d’accès peut avoir des niveaux plus ou moins, comme « / primarySourceFile » (parce que la propriété est à la racine du flux de travail) ou « vidéo/opacité de traitement ou le graphisme » (car la superposition est dans un groupe).    

Pour vérifier le nom de chemin d’accès et de propriété, utilisez le bouton d’action qui se trouve immédiatement à côté de chaque propriété. Vous pouvez cliquer sur ce bouton action et sélectionnez **Modifier**. Vous verrez le nom réel de la propriété et immédiatement au-dessus de lui, l’espace de noms.

![Action/Modifier](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriété](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Plusieurs fichiers d’entrée

Chaque tâche que vous soumettez au **Workflow de prime Media Encoder** nécessite deux éléments :

- Le premier est un *Flux de travail actif* qui contient un fichier de flux de travail. Vous pouvez créer des fichiers de flux de travail à l’aide du [Concepteur de flux de travail](media-services-workflow-designer.md).
- Le second est un *Actif de support* contenant l’ou les fichiers média que vous voulez coder.

Lorsque vous envoyez plusieurs fichiers de support à l’encodeur de **Media Encoder Premium Workflow** , les contraintes suivantes s’appliquent :

- Tous les fichiers multimédias doivent être dans la même *Ressource multimédia*. À l’aide de plusieurs actifs de support n’est pas pris en charge.
- Vous devez définir le fichier primaire dans cette ressource multimédia (dans l’idéal, il s’agit du fichier vidéo principal que le codeur doit traiter).
- Il est nécessaire de passer des données de configuration qui inclut l’élément **setRuntimeProperties** et/ou **transcodeSource** pour le processeur.
  - **setRuntimeProperties** est utilisé pour substituer la propriété de nom de fichier ou d’une autre propriété dans les composants de flux de travail.
  - **transcodeSource** est utilisé pour spécifier le contenu de la liste XML.

Connexions dans le flux de travail :

 - Si vous utilisez un ou plusieurs composants de l’entrée de fichier de média et plan d’utiliser **setRuntimeProperties** pour spécifier le nom de fichier, puis ne connectez pas le pin de composant principal fichier leur. Assurez-vous qu’il n’existe aucune connexion entre l’objet de fichier principal et l’entrée du fichier média.
 - Si vous préférez utiliser la liste XML et un composant de la Source de média, puis vous pouvez connecter les deux ensemble.

![Pas de connexion à partir du fichier de code Source principal pour l’entrée de fichier de média](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Il n’existe aucune connexion à partir du fichier principal ou aux composants de l’entrée de fichier de média si vous utilisez setRuntimeProperties pour définir la propriété de nom de fichier.*

![Connexion à partir de la liste XML pour l’élément Source de la liste des images](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Vous connectez la liste XML à la Source du média, utilisez transcodeSource.*


### <a name="clip-list-xml-customization"></a>Personnalisation de la liste XML du clip
Vous pouvez spécifier la liste XML dans le flux de travail lors de l’exécution à l’aide de **transcodeSource** dans la chaîne de configuration XML. Cette opération nécessite le code confidentiel de la liste XML à être connectés au composant Source de média dans le flux de travail.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Si vous souhaitez spécifier /primarySourceFile pour utiliser cette propriété pour nommer les fichiers de sortie à l’aide de « Expressions », nous vous recommandons en passant le XML de liste de Clip comme une propriété de *une fois que* la propriété /primarySourceFile, pour éviter d’avoir à la liste des images d’être remplacé par le paramètre /primarySourceFile.

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Avec ajustement précis de la supplémentaire :

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


## <a name="example"></a>Exemple

Prenons l’exemple dans lequel vous souhaitez superposer une image de logo sur l’entrée vidéo alors que la vidéo est encodée. Dans cet exemple, la vidéo d’entrée est appelée « MyInputVideo.mp4 » et le logo est « MyLogo.png ». Vous devez effectuer les opérations suivantes :

- Créer une ressource de flux de travail avec le fichier de workflow (voir l’exemple suivant).
- Créer une ressource multimédia, qui contient deux fichiers : MyInputVideo.mp4 comme le fichier primaire et MyLogo.png.
- Envoyer une tâche au processeur de support Premium du flux de travail codeur multimédia avec les ressources d’entrée ci-dessus et spécifiez la chaîne de configuration suivantes.

Configuration :

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


Dans l’exemple ci-dessus, le nom du fichier vidéo est envoyé pour le composant de l’entrée de fichier de média et de la propriété primarySourceFile. Le nom du fichier de logo est envoyé à une autre entrée de fichier média auquel est connecté le composant graphique de superposition.

>[AZURE.NOTE]Le nom du fichier vidéo est envoyé à la propriété primarySourceFile. La raison pour cela est d’utiliser cette propriété dans le flux de travail pour générer le nom de fichier de sortie correcte à l’aide d’Expressions, par exemple.


### <a name="step-by-step-workflow-creation-that-overlays-a-logo-on-top-of-the-video"></a>Création de la séquence de travail détaillée qui recouvre un logo en haut de la vidéo     

Voici les étapes pour créer un workflow qui prend deux fichiers en entrée : une vidéo et une image. Il recouvre alors l’image sur la vidéo.

Ouvrez le **Concepteur de flux de travail** et sélectionnez le **fichier** > **Nouvel espace de travail** > **Plan de transcodage**.

Le nouveau flux de travail montre trois éléments :

- Fichier Source principal
- Liste de l’élément XML
- Fichier de sortie/actif  

![Nouveau flux de travail de codage](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nouveau flux de travail de codage*


Pour accepter le fichier de média d’entrée, démarrer avec l’ajout d’un composant Media fichier d’entrée. Pour ajouter un composant dans le flux de travail, recherchez-le dans la zone de recherche du référentiel et faites glisser l’entrée souhaitée dans le volet de concepteur.

Ensuite, ajoutez le fichier vidéo à utiliser pour la conception de votre flux de travail. Pour ce faire, cliquez sur le volet de l’arrière-plan dans le Concepteur de flux de travail, puis recherchez la propriété fichier de code Source principal dans le volet droit de propriété. Cliquez sur l’icône de dossier et sélectionnez le fichier vidéo approprié.

![Source de fichier principal](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Source de fichier principal*


Ensuite, spécifiez le fichier vidéo dans le composant d’entrée de fichier de média.   

![Source d’entrée du fichier multimédia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Source d’entrée du fichier multimédia*


Dès que cela est fait, le composant de l’entrée de fichier de média inspecter le fichier et remplir ses broches de sortie afin de refléter le fichier il inspecté.

L’étape suivante consiste à ajouter un « vidéo Type Actualiseur de données » pour spécifier l’espace de couleurs à Rec.709. Ajouter un « vidéo convertisseur de Format » qui est défini pour le type de données présentation/disposition = Configurable plane. Cela va convertir le flux vidéo à un format qui peut être considéré comme une source du composant de la superposition.

![mise à jour du Type de données et du convertisseur de formats de vidéo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Mise à jour du Type de données vidéo et convertisseur de Format*

![Le type de disposition = Configurable plane](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Le type de disposition est Configurable plane*

Ensuite, ajouter un composant de superposition vidéo et le code confidentiel de vidéo (non compressé) de se connecter à la broche de vidéo (non compressée) de l’entrée de fichier de média.

Ajouter une autre entrée de fichier Media (pour charger le fichier du logo), cliquez sur ce composant et renommez-le « Media fichier entrée logo » et sélectionnez une image (un fichier .png par exemple) dans la propriété de fichier. Se connecter à la broche d’images non compressées à la broche d’images non compressées de la superposition.

![Source de fichier composant et l’image de superposition](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Source de fichier composant et l’image de superposition*


Si vous souhaitez modifier la position du logo sur la vidéo (par exemple, vous pouvez souhaiter placer 10 pour cent sur le coin supérieur gauche de la vidéo), désactivez la case à cocher « Entrée manuelle ». Pour cela, car vous utilisez un fichier de média d’entrée pour fournir le fichier du logo pour le composant de la superposition.

![Position de superposition](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Position de superposition*


Pour encoder le flux vidéo à H.264, ajoutez les composants encoder le Codeur vidéo AVC et AAC à l’aire du concepteur. Connecter les broches.
Définissez le codage AAC et sélectionnez son Format/paramètre prédéfini de Conversion : 2.0 (L, R).

![Encodeurs audio et vidéo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Encodeurs audio et vidéo*


Maintenant ajouter les composants **Multiplexeur de ISO Mpeg-4** et le **Fichier de sortie** et de connecter les broches comme indiqué.

![MP4 multiplexeur et le fichier de sortie](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexeur et le fichier de sortie*


Vous devez définir le nom du fichier de sortie. Cliquez sur le composant de **Sortie du fichier** et modifiez l’expression pour le fichier :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nom du fichier de sortie](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nom du fichier de sortie*

Vous pouvez exécuter localement pour vérifier qu’il s’exécute correctement, le flux de travail.

Après que qu’il a terminé, vous pouvez l’exécuter dans Azure Media Services.

Tout d’abord, préparer un actif dans Azure Media Services avec deux fichiers : le fichier vidéo et le logo. Pour cela, à l’aide de l’API REST ou .NET. Vous pouvez également ce faire utiliser le Azure portal ou l' [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Ce didacticiel vous montre comment gérer les ressources avec AMSE. Il existe deux façons d’ajouter des fichiers à un actif :

- Créez un dossier local, copiez les deux fichiers et glissez le dossier à l’onglet **actif** .
- Télécharger le fichier vidéo en tant qu’actif, afficher les informations, accédez à l’onglet fichiers et télécharger un fichier supplémentaire (logo).

>[AZURE.NOTE]Veillez à définir un fichier principal de l’actif (le fichier vidéo principal).

![Fichiers de composants dans la AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Fichiers de composants dans la AMSE*


Sélectionnez l’élément et choisissez pour le coder avec le codeur de la prime. Télécharger le flux de travail et le sélectionner.

Cliquez sur le bouton pour passer des données au processeur et ajoutez le code XML suivant pour définir les propriétés d’exécution :

![Encodeur de prime dans AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Encodeur de prime dans AMSE*


Ensuite, collez les données XML suivantes. Vous devez spécifier le nom du fichier vidéo pour l’entrée de fichier de média et primarySourceFile. Spécifier le nom du nom de fichier pour le logo.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


Si vous utilisez le Kit de développement .NET pour créer et exécuter la tâche, ces données XML doit être passé comme chaîne de configuration.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);

Une fois la tâche terminée, le fichier MP4 dans le capital de sortie affiche la superposition !

![Superposition sur la vidéo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Superposition sur la vidéo*


Vous pouvez télécharger le flux de travail d’exemple à partir de [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


## <a name="see-also"></a>Voir aussi

- [Présentation de prime codage dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

- [Comment faire pour utiliser le codage de la prime dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

- [Encodage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md#media_encoder_premium_workflow)

- [Les codecs et les formats de média codeur Premium Workflow](media-services-premium-workflow-encoder-formats.md)

- [Exemples de fichiers de flux de travail](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

- [Outil de Media Services Explorer Azure](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
