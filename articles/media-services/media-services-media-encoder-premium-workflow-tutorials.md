<properties 
    pageTitle="Didacticiels de convertisseur Media Encoder Premium Workflow" 
    description="Ce document contient des procédures pas à pas qui montrent comment effectuer des tâches avancées avec Media Encoder Premium Workflow et également comment créer un flux de travail complexes avec le Concepteur de flux de travail." 
    services="media-services" 
    documentationCenter="" 
    authors="xstof" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"  
    ms.author="xstof;xpouyat;juliako"/>

#<a name="advanced-media-encoder-premium-workflow-tutorials"></a>Didacticiels Media Encoder Premium Workflow avancés

##<a name="overview"></a>Vue d’ensemble 

Ce document contient des procédures pas à pas qui montrent comment personnaliser le flux de travail avec le **Concepteur de flux de travail**. Vous pouvez rechercher les fichiers de flux de travail réel [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

##<a name="toc"></a>TABLE DES MATIÈRES

Les rubriques suivantes sont traitées :

- [Codage MXF à une vitesse de transmission unique MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
    - [Démarrer un nouveau workflow](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new) 
    - [À l’aide de l’entrée du fichier multimédia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
    - [Inspecter les flux de données multimédia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
    - [Ajout d’un codeur vidéo pour. Génération de fichiers MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
    - [Encodage du flux audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
    - [Multiplexage des flux Audio et vidéo dans un conteneur MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
    - [Écriture du fichier MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
    - [Création d’une ressource de Services multimédia à partir du fichier de sortie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
    - [Tester le flux de travail terminé localement](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
- [Codage du MXF dans multibitrate MP4s - emballage dynamique activé](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
    - [Ajout d’un ou de plusieurs autres sorties MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
    - [Configurer les noms de fichier de sortie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
    - [Ajout d’une piste Audio distincte](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
    - [Ajout de la. Fichier SMIL de ISM](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
- [Codage du MXF dans multibitrate MP4 - plan améliorée](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
    - [Vue d’ensemble du flux de travail afin d’améliorer](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_overview)
    - [Conventions de dénomination de fichier](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
    - [Propriétés du composant sur la racine du flux de travail de la publication](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
    - [A généré le fichier de sortie noms comptent sur les valeurs de propriété publié](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
- [Ajout de miniatures à multibitrate sortie de MP4](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
    - [Vue d’ensemble du workflow pour ajouter des miniatures](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to_multibitrate_MP4_overview)
    - [Ajout de codage JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
    - [Concernant la conversion de l’espace de couleurs](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
    - [Écrire les miniatures](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
    - [Détection d’erreurs dans un flux de travail](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
    - [Flux de travail terminé](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
- [Ajustement de basé sur le temps de la sortie de multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
    - [Vue d’ensemble du flux de travail pour commencer à ajouter les ajustement de](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
    - [L’utilisation du découpage de flux](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
    - [Flux de travail terminé](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
- [Présentation du composant de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
    - [Script dans un flux de travail : Bonjour tout le monde](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
- [Ajustement de la trame de sortie de multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
    - [Vue d’ensemble de plan à ajouter ajustement](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
    - [À l’aide de la liste de l’élément XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
    - [Modification de la liste des images à partir d’un composant de script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
    - [Ajout d’une propriété de commodité ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

##<a id="MXF_to_MP4"></a>Codage MXF à une vitesse de transmission unique MP4
 
Dans cette procédure pas à pas, nous allons créer une vitesse de transmission unique. Fichier MP4 avec HE-AAC codé audio à partir d’un. Fichier d’entrée du MXF. 

###<a id="MXF_to_MP4_start_new"></a>Démarrer un nouveau workflow 

Ouvrez le Concepteur de flux de travail et sélectionnez « Plan de transcodage de fichier «-« nouvel espace de travail »- » » 

Le nouveau flux de travail affiche les 3 éléments : 

- Fichier Source principal
- Liste de l’élément XML
- Fichier de sortie/actif  

![Nouveau flux de travail de codage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nouveau flux de travail de codage*

###<a id="MXF_to_MP4_with_file_input"></a>À l’aide de l’entrée du fichier multimédia

Pour accepter notre fichier média d’entrée, un démarre avec l’ajout d’un composant du fichier de média d’entrée. Pour ajouter un composant dans le flux de travail, recherchez-le dans la zone de recherche du référentiel et faites glisser l’entrée souhaitée dans le volet de concepteur. Effectuez cette opération pour le fichier d’entrée de média et connecter le composant principal fichier de code Source à la broche d’entrée de nom de fichier à partir du fichier d’entrée de média.

![Entrée de fichier de support connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrée de fichier de support connecté*

Avant que nous pouvons faire beaucoup d’autres, il est d’abord nécessaire d’indiquer au Concepteur de flux de travail quel exemple de fichier que nous souhaitons utiliser pour concevoir notre flux de travail avec. Pour ce faire, cliquez sur l’arrière-plan du volet du concepteur et recherchez la propriété fichier de code Source principal dans le volet droit de propriété. Cliquez sur l’icône de dossier et sélectionnez le fichier souhaité pour tester le flux de travail avec. Dès que cela est fait, le composant de l’entrée de fichier de média inspecter le fichier et remplir ses broches de sortie afin de refléter le fichier qu'il inspecté.

![Support remplis de fichiers d’entrée](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Support remplis de fichiers d’entrée*

Alors que cela indique quelle entrée serait de travailler, il n’indique pas encore où la sortie codée doit atteindre. Semblable à la façon dont le fichier Source principal a été configuré, maintenant configurer la propriété variables du dossier de sortie, juste en dessous.

![Configuration des propriétés d’entrée et de sortie](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Configuration des propriétés d’entrée et de sortie*

###<a id="MXF_to_MP4_streams"></a>Inspecter les flux de données multimédia

Il a souvent nécessaire de savoir comment le flux de données se présente comme flux via le workflow. Pour vérifier si un flux de données à tout moment dans le flux de travail, cliquez simplement sur une sortie ou une broche d’entrée sur un des composants. Dans ce cas, essayez de cliquer sur la broche de sortie vidéo non compressées à partir de notre fichier média d’entrée. Une boîte de dialogue s’ouvre permettant d’inspecter la sortie vidéo.

![Inspection de la broche de sortie vidéo non compressée](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspection de la broche de sortie vidéo non compressée*

Dans notre cas, il indique par exemple que nous avons affaire à une entrée de 1920 x 1080 à 24 images par seconde en 4:2:2 l’échantillonnage pour une vidéo de près de 2 minutes.

###<a id="MXF_to_MP4_file_generation"></a>Ajout d’un codeur vidéo pour. Génération de fichiers MP4

Notez que maintenant, une vidéo non compressée et plusieurs broches de sortie Audio non compressé pour une utilisation sur notre entrée de fichier de média. Pour coder la vidéo entrante, nous devons un composant de codage - dans ce cas pour la génération. Fichiers MP4.

Pour encoder le flux vidéo à H.264, ajouter le composant de codeur vidéo d’AVC à l’aire du concepteur. Ce composant prend un flux vidéo de décompresser comme entrée et fournit un flux vidéo compressé AVC sur sa broche de sortie.

![Codeur AVC non connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Codeur AVC non connecté*

Ses propriétés déterminent comment le codage exactement. Nous allons avoir un aperçu de certains des paramètres plus importants :

- Largeur de la sortie et la hauteur de la sortie : ils déterminent la résolution de la vidéo encodée. Dans notre cas, commençons avec 640 x 360
- Fréquence d’images : lorsque la valeur passthrough il simplement adopte la cadence d’images source, il est possible cependant la remplacer. Notez que cette conversion de fréquence d’images n’est pas compensée en mouvement.
- Profil et niveau : ils déterminent le profil de AVC et le niveau. Pour obtenir plus d’informations sur les différents niveaux et les profils, cliquez sur l’icône point d’interrogation sur le composant AVC Video Encoder et affiche la page d’aide plus en détail sur chacun des niveaux. Pour notre exemple, utilisons le profil principal au niveau 3.2 (par défaut).
- Taux de contrôler le Mode et la vitesse de transmission (Kbits/s) : dans notre scénario, nous opter pour une vitesse de transmission constante (CBR) sortie Kbits/s 1200
- Format vidéo : il s’agit du VUI (informations sur la facilité d’utilisation vidéo) qui est écrit dans le flux de données H.264 (informations côté qui peuvent être utilisé par un décodeur pour améliorer l’affichage, mais pas essentielle pour décoder correctement) :
- NTSC (standard pour les États-Unis et au Japon, à l’aide de 30 i/s)
- PAL (standard pour l’Europe, à l’aide de 25 i/s)
- Mode de taille de groupe d’images : taille de groupe d’images fixes sera configuré pour nos besoins avec un intervalle de clé de 2 secondes avec les groupes d’images fermés. Cela garantit la compatibilité avec les Services de support dynamique emballage Azure fournit.

Aliments notre codeur AVC, pour connecter la broche de sortie vidéo non compressée à partir du composant fichiers de support d’entrée à la broche d’entrée de la vidéo non compressée à partir du codeur AVC.

![Codeur AVC connectés](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Codeur AVC principal connecté*

###<a id="MXF_to_MP4_audio"></a>Encodage du flux audio

À ce stade, nous avons encodé vidéo, mais le flux de données audio non compressé d’origine doit toujours être compressé. Pour cela, nous allons passer avec codage AAC par le composant de l’encodeur AAC (Dolby). Ajouter au flux de travail.

![Codeur AVC non connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Codeur AAC non connecté*

Maintenant il y a une incompatibilité : il existe uniquement une seule non compressée audio broche d’entrée à partir du codeur AAC pendant plus que probable que le fichier d’entrée de média aura deux différents non compressé flux audio disponibles : un pour le canal audio de gauche et une pour le droit. (Si vous travaillez avec un son surround, qui est 6 canaux). Il n’est donc pas possible de se connecter directement de l’audio à partir de la source d’entrée de fichier de média dans le codeur audio AAC. Le composant AAC s’attend à un flux de données audio « entrelacée » dite : un flux unique qui dispose de la gauche et les bons canaux entrelacés avec eux. Une fois que nous savons à partir de notre fichier source les pistes audio sont la position de la source, nous pouvons générer ce flux de données audio entrelacé avec les positions de haut-parleur correctement affectées pour gauche et droite.

Tout d’abord un souhaitez généré un flux de données entrelacée dans les canaux audio sources requis. Le composant d’ENTRELACEUR de flux Audio va gérer cela pour nous. Ajouter au flux de travail et connecter les sorties audio à partir de l’entrée de fichier de média dans celui-ci.

![ENTRELACEUR de flux Audio connectés](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*ENTRELACEUR de flux Audio connectés*

Maintenant que nous disposons d’un flux de données audio entrelacé, nous n’a pas toujours spécifier où affecter les postes du haut-parleur de gauche ou de droite. Pour cela, nous pouvons exploiter l’assigne de Position de haut-parleur.

![Ajout d’un assigne de Position de haut-parleur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Ajout d’un assigne de Position de haut-parleur*

Configurer l’assigne de Position de haut-parleur pour une utilisation avec un flux d’entrée stéréo via un filtre prédéfini de codeur de « Custom », et le canal prédéfini appelé « 2.0 (L, R) ». (Cela affecte la position de haut-parleur gauche à la chaîne 1 et la position de haut-parleur droit à canal 2.)

Connectez la sortie de l’assigne de Position de haut-parleur à l’entrée de l’encodeur AAC. Ensuite, indiquez à l’encodeur AAC pour travailler avec un « 2.0 (L, R) » canal prédéfinis, afin qu’il sache pour traiter les données audio stéréo en tant qu’entrée.

###<a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexage des flux Audio et vidéo dans un conteneur MP4

Étant donné notre AVC flux de vidéo codé nos AAC codés et flux de données audio, nous pouvons capturer dans un. Conteneur de MP4. Le processus de MELANGE de flux différents en un seul est appelé « multiplexage » (ou « muxing »). Dans ce cas, nous allons entrelacement le flux audio et les vidéo dans un seul cohérent. Package MP4. Le composant qui coordonne pour un. MP4 conteneur est appelé le multiplexeur ISO MPEG-4. Ajouté à l’aire du concepteur et de se connecter à la fois l’encodeur vidéo AVC et l’encodeur AAC à ses entrées.

![MULTIPLEXEUR de MPEG4 connectés](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*MULTIPLEXEUR de MPEG4 connectés*

###<a id="MXF_to_MP4_writing_mp4"></a>Écriture du fichier MP4

Lors de l’écriture d’un fichier de sortie, le composant de sortie du fichier est utilisé. Nous pouvons nous connecter ce à la sortie de l’ISO MPEG-4 multiplexeur afin que la sortie est écrite sur le disque. Pour ce faire, vous connecter la broche de sortie conteneur (MPEG-4) à la broche d’entrée d’écriture de la sortie du fichier.

![Connexion de sortie de fichier](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Connexion de sortie de fichier*

Le nom du fichier qui sera utilisé est déterminé par la propriété de fichier. Alors que cette propriété peut être codé en dur pour une valeur donnée, probablement l’un sera d’entrer dans une expression à la place.

Pour que le flux de travail à déterminer automatiquement la sortie fichier propriété nom à partir d’une expression, cliquez sur le bouton en regard du nom de fichier (à côté de l’icône de dossier). Dans le menu déroulant, puis sélectionnez « Expression ». L’éditeur d’expression s’affiche. Effacer le contenu de l’éditeur de tout d’abord.

![Éditeur d’Expression vide](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Éditeur d’Expression vide*

L’éditeur d’expression permet d’entrer une valeur littérale, mélangée avec une ou plusieurs variables. Variables commencent par un signe dollar. Lorsque vous appuyez sur la touche $, l’éditeur affiche une zone de liste déroulante avec un choix de variables disponibles. Dans notre cas, nous allons utiliser une combinaison de la variable de répertoire de sortie et la variable de nom de fichier d’entrée de base :

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Rempli par l’éditeur d’Expression](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Rempli par l’éditeur d’Expression*

>[AZURE.NOTE]Pour afficher un fichier de sortie de votre travail de codage dans Azure, vous devez fournir une valeur dans l’éditeur d’expression. 

Lorsque vous confirmez l’expression en appuyant sur OK, la fenêtre de propriété aperçu apparaît quelle valeur le résout de propriété du fichier à ce stade.

![Expression de fichier résout dir de sortie](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Expression de fichier résout dir de sortie*

###<a id="MXF_to_MP4_asset_from_output"></a>Création d’une ressource de Services multimédia à partir du fichier de sortie

Alors que nous avons écrit un fichier de sortie MP4, nous devons indiquer que ce fichier appartient à l’actif de sortie qui génèrent des services de support à la suite de l’exécution de ce flux de travail. À cette fin, le nœud du fichier de sortie/actif sur le canevas de workflow est utilisé. Tous les fichiers entrants dans ce nœud fera partie de l’immobilisation Azure Media Services qui en résulte.

Connecter le composant de sortie du fichier pour le composant de sortie fichier/actif à la fin du flux de travail.

![Flux de travail terminé](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Flux de travail terminé*

###<a id="MXF_to_MP4_test"></a>Tester le flux de travail terminé localement

Pour tester le flux de travail localement, appuyez sur le bouton lecture dans la barre d’outils du haut. Une fois que le flux de travail en cours d’exécution, vérifiez que la sortie générée dans le dossier de sortie configuré. Vous verrez le fichier de sortie MP4 fini qui a été codé à partir du fichier de la source d’entrée MXF.

##<a id="MXF_to_MP4_with_dyn_packaging"></a>Codage du MXF dans MP4 - multibitrate activé de packaging dynamique

Dans cette procédure pas à pas nous créerons un jeu de plusieurs fichiers MP4 de vitesse de transmission avec AAC codé audio à partir d’un seul. Fichier d’entrée du MXF.

Lorsqu’une sortie de ressources multi-bitrate est souhaitée à utiliser en combinaison avec les fonctionnalités d’emballage dynamique offertes par Azure Media Services, plusieurs fichiers MP4 de groupe d’images alignées de chacun un autre taux d’échantillonnage et la résolution doivent être générés. Pour ce faire, la procédure pas à pas [De codage de MXF à une vitesse de transmission unique MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) nous fournit un bon point de départ.

![Démarrage du flux de travail](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Démarrage du flux de travail*

###<a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Ajout d’un ou de plusieurs autres sorties MP4

Tous les fichiers MP4 dans nos actifs résultant de Azure Media Services prendra en charge un autre taux d’échantillonnage et la résolution. Ajoutons un ou plusieurs fichiers de sortie MP4 pour le flux de travail.

Pour vous assurer que nous avons tous notre codeurs vidéo créés avec les mêmes paramètres, il est plus pratique de dupliquer l’encodeur vidéo AVC déjà existant et de configurer une autre combinaison de résolution et de la vitesse de transmission (ajoutons une des 960 x 540 à 25 images par seconde à 2,5 Mbps). Pour dupliquer le codeur existant, copier coller sur l’aire du concepteur.

Se connecter à la broche de sortie vidéo non compressée de l’entrée de fichier de média dans notre nouveau composant AVC.

![Deuxième codeur AVC connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Deuxième codeur AVC connecté*

Maintenant, adapter la configuration pour notre nouvel encodeur AVC à 960 x 540 à 2,5 Mbps de sortie. (Utilisez ses propriétés « largeur de sortie », « Hauteur de sortie » et « Vitesse de transmission (Kbits/s) » pour cela).

Étant donné que nous souhaitons utiliser l’actif qui en résulte ainsi que de l’emballage dynamique d’Azure Media Services, le point de terminaison en flux continu doit être capable de générer à partir de ces fichiers MP4 fragments TLS/Fragmented MP4/tiret exactement alignés entre eux, de sorte que les clients sont de commutation entre les vitesses de transmission différentes obtiennent une vidéo en continu lisse et une expérience audio. Pour cela, nous devons garantir que, dans les propriétés des deux codeurs AVC le groupe d’images (« groupe d’images ») taille pour les deux fichiers MP4 est définie à 2 secondes, ce qui peut être effectuées par :

- définition du Mode de taille de groupe d’images à la taille du groupe d’images fixes et
- l’intervalle de trame clé à deux secondes.
- définir également le contrôle IDR groupe d’images fermé pour s’assurer du groupe d’images toutes les sont permanent sur leurs propre sans dépendances

Pour notre flux de travail permet de facilement comprendre, renommer le premier codeur AVC « AVC Video Encoder 640 x 360 Kbits/s 1200 » et l’encodeur AVC deuxième « AVC Video Encoder 960 x 540 Kbits/s 2500 ».

Maintenant ajouter un deuxième multiplexeur ISO MPEG-4 et un second fichier de sortie. Le multiplexage la connexion au nouveau AVC codeur et vérifiez que sa sortie est dirigée vers le fichier de sortie. Connectez-vous également d’entrée de l’AAC codeur audio sortie vers le nouveau de multiplexeur. La sortie du fichier à son tour peuvent être liée au nœud sortie fichier/actif pour l’ajouter à l’actif de Services média qui sera créé.

![Muxer de deuxième et de la sortie du fichier connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Muxer de deuxième et de la sortie du fichier connecté*

Pour la compatibilité avec emballage dynamique d’Azure Media Services, configurer le Mode Chunk de multiplexeur à la durée ou au nombre de groupe d’images et de définir les groupes d’images par segment 1. (Elle doit être la valeur par défaut.)

![Modes de segment de Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modes de segment de Muxer*

Remarque : vous souhaiterez peut-être répéter ce processus pour les autres combinaisons de résolution et de la vitesse de transmission que vous souhaitez ajouter à la sortie de l’actif.

###<a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurer les noms de fichier de sortie

Nous avons plus d’un seul fichier ajouté à l’élément de sortie. Cela fournit un besoin pour vous assurer que les noms de fichiers pour chacun des fichiers de sortie sont différents entre eux et peut-être même appliquer une convention d’affectation de noms de fichier afin qu’il devienne clair à partir du nom de fichier que vous avez affaire à.

Nom de sortie de fichier peut être contrôlée à travers des expressions dans le concepteur. Ouvrir le volet de propriété pour l’un des composants de la sortie du fichier et ouvrir l’éditeur d’expression pour la propriété de fichier. Notre premier fichier de sortie a été configuré par le biais de l’expression suivante (voir le didacticiel pour consulter du [MXF à une vitesse de transmission unique MP4 sortie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)) :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Cela signifie que notre nom de fichier est déterminée par deux variables : écrire le répertoire de sortie et le nom de base du fichier source. Le premier est exposé comme une propriété de la racine du flux de travail, et ce dernier est déterminé par le fichier entrant. Notez que le répertoire de sortie est ce que vous utilisez pour le test local ; Cette propriété sera remplacée par le moteur de flux de travail lorsque le flux de travail est exécuté par le processeur de média basées sur le nuage dans Azure Media Services.
Pour donner à la fois nos fichiers de sortie un résultat cohérent d’attribution de noms, modifiez le premier fichier expression d’affectation de noms :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

et le second pour :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Exécuter un test intermédiaire exécuter pour vous assurer que les deux fichiers de sortie MP4 sont correctement générés.

###<a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Ajout d’une piste Audio distincte

Nous verrons plus tard lorsque nous allons générer un fichier .ism pour accéder à nos fichiers de sortie MP4, nous nécessitera également un fichier MP4 audio uniquement en tant que la piste audio pour notre diffusion adaptative en continu. Pour créer ce fichier, ajouter une muxer supplémentaire pour le flux de travail (multiplexeur ISO-MPEG-4) et de connecter la broche de sortie de l’encodeur AAC avec sa broche d’entrée pour la piste 1.

![Muxer audio ajouté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Muxer audio ajouté*

Créez un troisième composant de fichier de sortie à la sortie le flux sortant à partir de la muxer et de configurer le fichier d’affectation de noms expression comme :
    
    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Création du fichier de sortie de Muxer audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Création du fichier de sortie de Muxer audio*

###<a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Ajout de la. Fichier SMIL de ISM

Pour l’emballage dynamique fonctionne en combinaison avec MP4 fichiers (et l’audio uniquement MP4) dans notre actif de Media Services, nous devons également un fichier manifeste (également appelé fichier « SMIL » : synchronisation Multimedia Integration Language). Ce fichier indique à Azure Media Services quels fichiers MP4 sont disponibles pour emballage dynamique et de celles à prendre en compte pour le streaming audio. Un fichier manifeste par défaut pour un ensemble du MP4 avec un seul flux audio ressemble à ceci :
    
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

Il contient le fichier .ism au sein d’une instruction switch, une référence à chacun des fichiers vidéo MP4 individuels et en plus de ces références de fichier audio une (ou plusieurs) à un MP4 qui ne contient que l’audio.

Génération du fichier manifest pour notre jeu de MP4 peut être effectuée via un composant appelé le « rédacteur de manifeste AMS ». Pour l’utiliser, faites-la glisser sur la surface et connecter les broches de sortie « Écriture complète » dans les trois composants du fichier de sortie à l’entrée du Générateur de manifeste AMS. Veillez à vous connecter à la sortie du Writer AMS manifeste pour la sortie fichier/actif.

Comme avec les autres composants de sortie de fichier, configurez le nom de sortie de fichier .ism avec une expression :

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

L’aspect de notre flux de travail terminé le ci-dessous :

![MXF fin au workflow de MP4 multibitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*MXF fin au workflow de MP4 multibitrate*

##<a id="MXF_to__multibitrate_MP4"></a>Codage du MXF dans multibitrate MP4 - plan améliorée

Dans cette [procédure pas à pas de flux de travail précédent](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) , nous avons vu comment un actif d’entrée MXF unique peut être converti en un élément de sortie avec les fichiers MP4 multi-bitrate, un fichier MP4 audio uniquement et un fichier manifest à utiliser conjointement avec emballage dynamique d’Azure Media Services.

Cette procédure pas à pas indique comment certains aspects peuvent être améliorée et rendue plus pratique.

###<a id="MXF_to_multibitrate_MP4_overview"></a>Vue d’ensemble du flux de travail afin d’améliorer

![Workflow Multibitrate MP4 pour améliorer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Workflow Multibitrate MP4 pour améliorer*

###<a id="MXF_to__multibitrate_MP4_file_naming"></a>Conventions de dénomination de fichier

Dans le workflow précédent, nous avons spécifié une expression simple comme base pour générer les noms de fichier de sortie. Nous avons des valeurs en double, bien que : tous les les composants de fichier de sortie individuelle spécifié cette expression.

Par exemple, notre composant de sortie de fichier pour le premier fichier vidéo est configuré avec cette expression :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Alors que pour la deuxième sortie vidéo, nous avons une expression telle que :

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Ne serait-il pas plus propre, moins source d’erreurs et plus pratique si nous pouvons supprimer une partie de cette duplication et rendre les choses plus configurable à la place ? Heureusement, nous pouvons : fonctionnalités d’expression valide du concepteur en combinaison avec la possibilité de créer des propriétés personnalisées sur la racine de notre workflow sont nous donneront une couche supplémentaire de commodité.

Supposons que nous allons lecteur configuration du nom de fichier à partir des vitesses de la transmission des fichiers MP4 individuels. Ces vitesses de transmission que nous allons visent à configurer dans un emplacement centralisé (à la racine de notre graphique), dans lequel ils vous sont accessibles pour configurer et génération de nom de fichier de lecteur. Pour ce faire, nous allons commencer par la publication de la propriété de la vitesse de transmission des codeurs AVC à la racine de notre flux de travail, afin qu’elle devienne accessible à partir de la racine ainsi aux encodeurs AVC. (Même si affichée dans deux zones différentes, il n'existe qu’une seule valeur sous-jacente.)

###<a id="MXF_to__multibitrate_MP4_publishing"></a>Propriétés du composant sur la racine du flux de travail de la publication

Ouvrez le premier encodeur AVC, accédez à la propriété vitesse de transmission (Kbits/s) et dans la liste déroulante, choisissez Publier.

![La propriété vitesse de transmission de publication](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*La propriété vitesse de transmission de publication*

Configurer la boîte de dialogue Publier pour publier à la racine du graphique de notre flux de travail, avec un nom publié de « video1bitrate » et un nom complet explicite de « Vitesse de transmission vidéo 1 ». Configurer un personnalisé nom de groupe appelée « Vitesses de transmission en continu » et cliquez sur Publier.

![La propriété vitesse de transmission de publication](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Boîte de dialogue publication de propriété de la vitesse de transmission*

Répétez cette opération pour la propriété vitesse de transmission de l’encodeur AVC deuxième et nommez-la « video2bitrate » avec un nom d’affichage de « Vitesse de transmission vidéo 2 », dans le même groupe personnalisé « Vitesses de transmission en continu ».

Si nous maintenant d’examiner les propriétés de la racine du flux de travail, nous allons voir notre groupe personnalisé avec les deux propriétés publiées s’affichent. Les deux sont reflétant la valeur de leur vitesse de transmission du codeur AVC respectif.

![Propriétés de débit publiée sur la racine du flux de travail](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Chaque fois que nous souhaitons accéder à ces propriétés à partir du code ou d’une expression, nous pouvons le faire comme suit :

- à partir du code en ligne à partir d’un composant vers la droite au-dessous de la racine : node.getPropertyAsString('.. / video1bitrate', null)
- dans une expression : ${ROOT_video1bitrate}
 
Nous allons terminer le groupe « Vitesses de transmission en continu » en publiant notre débit piste audio sur celui-ci. Dans les propriétés de l’encodeur AAC, rechercher le paramètre de vitesse de transmission et sélectionnez Publier dans la liste déroulante en regard de celui-ci. Publier dans la racine du graphique avec le nom « audio1bitrate » et afficher le nom de « Vitesse de transmission Audio 1 » au sein de notre groupe personnalisé « Vitesses de transmission en continu ».

![Boîte de dialogue publication pour une vitesse de transmission audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Boîte de dialogue publication pour une vitesse de transmission audio*

![Résultant des accessoires audio et vidéo sur la racine](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Résultant des accessoires audio et vidéo sur la racine*

Notez que la modification d’un de ces trois valeurs également ré-configure et modifie les valeurs sur les composants respectifs, ils sont liés à (et publiées à partir de).

###<a id="MXF_to__multibitrate_MP4_output_files"></a>A généré le fichier de sortie noms comptent sur les valeurs de propriété publié

Au lieu de coder nos noms du fichier généré, nous pouvons maintenant modifier les notre expression de nom de fichier sur chacun des composants de sortie de fichier s’appuyer sur les propriétés de la vitesse de transmission que nous vient d’être publiés sur la racine du graphique. En commençant par notre premier fichier de sortie, recherchez la propriété de fichier et modifier l’expression comme suit :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Les différents paramètres dans cette expression sont accessibles et entrées en appuyant sur le signe dollar du clavier dans la fenêtre d’expression. L’un des paramètres disponibles est notre propriété video1bitrate que nous avons publié précédemment.

![Pour accéder aux paramètres dans une expression](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Pour accéder aux paramètres dans une expression*

Procédez de même pour la sortie du fichier pour notre deuxième vidéo :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

et pour la sortie du fichier audio :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Si nous modifions maintenant la vitesse de transmission pour les fichiers audio ou vidéo, le codeur respectif est reconfiguré et la convention de nom de fichier basé sur le taux d’échantillonnage sera respectée automatique toutes les.

##<a id="thumbnails_to__multibitrate_MP4"></a>Ajout de miniatures à multibitrate sortie de MP4

À partir d’un flux de travail qui génère [un sortie MP4 à partir d’un format MXF du multibitrate d’entrée](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), nous allons maintenant l’examiner en ajoutant des miniatures à la sortie.

###<a id="thumbnails_to__multibitrate_MP4_overview"></a>Vue d’ensemble du workflow pour ajouter des miniatures

![Workflow Multibitrate MP4 pour démarrer à partir de](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Workflow Multibitrate MP4 pour démarrer à partir de*

###<a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Ajout de codage JPG

Au cœur de notre génération miniature sera le composant JPG codeur, produire des fichiers JPG.

![Codeur JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Codeur JPG*

Nous ne pouvons pas toutefois se connecter directement notre flux de vidéo non compressée à partir de l’entrée de fichier de média dans le codeur JPG. Au lieu de cela, il s’attend à être transmis des images individuelles. Cela, nous pouvons le faire via le composant de grille de trames vidéo.

![Connexion d’un portail de cadre à l’encodeur JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Connexion d’un portail de cadre à l’encodeur JPG*

La porte de l’image une seule fois toutes les secondes ou cadres autant autorise une image vidéo à passer. L’intervalle et le temps de décalage avec laquelle ceci se produit est configurable dans les propriétés.

![Propriétés de grille d’image vidéo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriétés de grille d’image vidéo*

Nous allons créer une miniature de chaque minute en configurant le mode pour la durée (en secondes) et l’intervalle de 60.

###<a id="thumbnails_to__multibitrate_MP4_color_space"></a>Concernant la conversion de l’espace de couleurs

Pendant qu’il semblerait logique de que deux broches de vidéo non compressée de la porte de la trame et l’entrée du fichier multimédia peuvent maintenant être connectés, nous aurions obtenu un message d’avertissement si vous nous faites.

![Erreur d’espace couleur d’entrée](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erreur d’espace couleur d’entrée*

C’est parce que la quelle couleur informations sont représentées dans notre d’origine non compressée vidéo flux brut, en provenance de notre MXF, est différent de ce qu’attend le codeur JPG. Plus précisément, un dite « espace de couleurs » de « RVB » ou « Gris » est attendus dans. Cela signifie que les flux vidéo entrant de la vidéo porte cadre devront dispose d’une conversion d’abord appliquée en ce qui concerne son espace colorimétrique.

Faire glisser sur le flux de travail le convertisseur d’espace couleur - Intel et le connecter à notre portail de cadre.

![Connexion d’un convertisseur d’espace couleur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Connexion d’un convertisseur d’espace couleur*

Dans la fenêtre Propriétés, sélectionnez l’entrée BVR 24 à partir de la liste prédéfinie.

###<a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Écrire les miniatures

Différents à partir de notre vidéo MP4, le composant JPG encodeur génère plus d’un fichier. Pour gérer cela, un composant de rédacteur de fichier JPG scène Search peut être utilisé : il prend les miniatures JPG entrantes et les écrire, chaque nom de fichier qui est suivie d’un numéro différent. (Le nombre généralement indiquant le nombre de secondes/unités dans le flux de données tirée de la miniature).


![Présentation de l’enregistreur de fichier JPG scène recherche](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Présentation de l’enregistreur de fichier JPG scène recherche*

Configurez la propriété de chemin d’accès du dossier de sortie avec l’expression : ${ROOT_outputWriteDirectory} 

et le préfixe de nom de fichier avec : 

    ${ROOT_sourceFileBaseName}_thumb_

Le préfixe déterminera la miniature en noms de fichier. Il seront suivi du suffixe indiquant la position du curseur de défilement dans le flux.


![Propriétés du rédacteur de fichier JPG recherche de scène](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propriétés du rédacteur de fichier JPG recherche de scène*

Connectez le Writer de fichier JPG scène recherche sur le nœud du fichier de sortie/actif.

###<a id="thumbnails_to__multibitrate_MP4_errors"></a>Détection d’erreurs dans un flux de travail

Se connecter à l’entrée du convertisseur espace couleur à la sortie vidéo non compressée brute. Maintenant effectuer un test local pour le flux de travail. Il existe de fortes chances que le flux de travail sera soudainement cesser de s’exécuter et indiquent avec un contour rouge sur le composant qui a rencontré une erreur :

![Erreur de conversion d’espace couleur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erreur de conversion d’espace couleur*

Cliquez sur la petite icône « E » rouge en haut à droite de l’angle du composant de conversion d’espace couleur pour voir quelle est la raison de la tentative de codage a échoué.

![Boîte de dialogue Erreur de conversion d’espace couleur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Boîte de dialogue Erreur de conversion d’espace couleur*

Il s’avère, comme vous pouvez le voir, que l’espace de couleurs entrants standard pour le convertisseur d’espace couleur doit être rec601 pour notre conversion demandée de YUV RVB. Apparemment nos flux n’indique pas son rec601. (REC. 601 est une norme de codage des signaux vidéo analogiques entrelacés sous forme de vidéo numérique. Il spécifie une région active couvrant 720 échantillons de luminance et des échantillons de chrominance 360 par ligne. La couleur système de codage est appelée YCbCr 4:2:2.)

Pour résoudre ce problème, nous allons indiquer sur les métadonnées de notre flux que nous avons affaire avec le contenu de rec601. Pour ce faire, nous allons utiliser un composant de mise à jour de Type de données vidéo, nous allons placer entre notre source brute et le composant de conversion d’espace couleur. Cette mise à jour du type de données permet la mise à jour manuelle de certaines données vidéo des propriétés de type. Configurer pour indiquer un espace de couleur Standard de « REC. 601 ». Ceci entraînera la mise à jour de Type de données vidéo baliser le flux avec l’espace de couleurs « REC. 601 » s’il y en avait pas encore défini d’espace couleur. (Il ne remplace pas les métadonnées existantes, à moins que la case à cocher de substitution a été activée.)

![Mise à jour Standard d’espace couleur sur la mise à jour du Type de données](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Mise à jour Standard d’espace couleur sur la mise à jour du Type de données*

###<a id="thumbnails_to__multibitrate_MP4_finish"></a>Flux de travail terminé

Maintenant que notre notre flux de travail est terminé, effectuez un autre test run Voir passer.

![Flux de travail terminé pour la sortie de multi-mp4 avec des miniatures](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Flux de travail terminé pour la sortie de multi-mp4 avec des miniatures*

##<a id="time_based_trim"></a>Ajustement de basé sur le temps de la sortie de multibitrate MP4

À partir d’un flux de travail qui génère [un sortie MP4 à partir d’un format MXF du multibitrate d’entrée](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), nous allons maintenant l’examiner dans le rognage de la vidéo source en fonction de l’horodatage.

###<a id="time_based_trim_start"></a>Vue d’ensemble du flux de travail pour commencer à ajouter les ajustement de

![Ajouter ajustement de flux de travail de démarrage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Ajouter ajustement de flux de travail de démarrage*

###<a id="time_based_trim_use_stream_trimmer"></a>L’utilisation du découpage de flux

Le composant de flux de données découpage permet de découper le début et la fin d’un flux d’entrée base sur minuterie informations (secondes, minutes,...). Le découpage ne gère pas l’ajustement de la trame.

![Découpage de flux](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Découpage de flux*

Au lieu de lier directement les codeurs de AVC et assigne de position de haut-parleur à l’entrée de fichier de support, nous mettrons entre celle du découpage de flux de données. (Un pour le signal vidéo et pour le signal audio entrelacé).

![Placez le découpage de flux de données entre les deux](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Placez le découpage de flux de données entre les deux*

Nous allons configurer le découpage afin que nous ne traitera que les données vidéo et audio entre 15 et 60 secondes dans la vidéo.

Accédez aux propriétés de l’organe de flux vidéo et configurer les propriétés de l’heure de fin (60 s) et l’heure de début (15 s). Pour vous assurer à la fois notre découpage audio et vidéo est toujours configuré pour les valeurs de fin et le début de même, nous publiera ceux à la racine du flux de travail.

![Publier la propriété au moment du début de découpage de flux](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publier la propriété au moment du début de découpage de flux*

![Publier la boîte de dialogue Propriétés de l’heure de début](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publier la boîte de dialogue Propriétés de l’heure de début*

![Publier la boîte de dialogue Propriétés de l’heure de fin](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publier la boîte de dialogue Propriétés de l’heure de fin*


Si nous Examinez maintenant la racine de notre flux de travail, les deux propriétés seront clairement affichés et configurables à partir de là.

![Propriétés publiées disponibles sur la racine](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Propriétés publiées disponibles sur la racine*

Ouvrez les propriétés de la fonction de suppression de découpage de l’audio et configurer les heures de début et de fin d’une expression qui fait référence aux propriétés de la racine de notre flux de travail publiées.

Pour l’heure de début d’ajustement audio :

    ${ROOT_TrimmingStartTime}

et pour son heure de fin :

    ${ROOT_TrimmingEndTime}

###<a id="time_based_trim_finish"></a>Flux de travail terminé

![Flux de travail terminé](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Flux de travail terminé*


##<a id="scripting"></a>Présentation du composant de script

Composants de script peuvent exécuter des scripts arbitraires au cours des phases d’exécution de notre flux de travail. Il y a quatre scripts différents qui peuvent être exécutées, chacun ayant des caractéristiques spécifiques et leur propre place dans le cycle de vie du flux de travail :

- **commandScript**
- **realizeScript**
- **processInputScript**
- **lifeCycleScript**

La documentation du composant script va plus en détail pour chacun des éléments ci-dessus. Dans [la section suivante](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), le composant de script **realizeScript** est utilisé pour construire un fichier xml de liste de séquences à la volée lorsque le workflow démarre. Ce script est appelé au cours de l’installation du composant, ce qui se produit une seule fois dans son cycle de vie.


###<a id="scripting_hello_world"></a>Script dans un flux de travail : Bonjour tout le monde

Faites glisser un composant de script sur l’aire du concepteur et renommez-le (par exemple, « SetClipListXML »).

![Ajout d’un composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Ajout d’un composant de script*

Lorsque vous examinez les propriétés du composant de script, les quatre types différents de script sera indiqué, chaque configurable pour un script différent.

![Propriétés du composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriétés du composant de script*

Désactivez le processInputScript et ouvrez l’éditeur de la realizeScript. Maintenant nous allons configurés et prêt à commencer l’écriture de scripts.

Les scripts sont écrits en Groovy, un langage de script compilé de manière dynamique pour la plate-forme Java qui conserve la compatibilité avec Java. En fait, la plupart du code Java est code sensationnelle valide.

Nous allons écrire un script de sensationnelle simple hello world dans le cadre de notre realizeScript. Entrez les informations suivantes dans l’éditeur :

    node.log("hello world");

Exécutez maintenant une série de tests locale. Après cette exécution, examinez la propriété journaux (par le biais de l’onglet système sur le composant de script).

![Sortie du journal de Hello world](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Sortie du journal de Hello world*

L’objet de nœud que nous appelons la méthode du journal, fait référence à notre « nœud » actuel ou le composant que nous allons script dans. Chaque composant possède la possibilité de sortie de données de journalisation disponibles via l’onglet système. Dans ce cas, nous la sortie le littéral de chaîne « hello world ». Il est important de comprendre ici est que cela peut s’avérer un outil précieux pour débogage, en vous fournissant un aperçu de ce que le script est en fait.

À partir de dans notre environnement de script, nous avons également avoir accès aux propriétés sur d’autres composants. Procédez comme suit :


    //inspect current node: 
    def nodepath = node.getNodePath(); 
    node.log("this node path: " + nodepath);
    
    //walking up to other nodes: 
    def parentnode = node.getParentNode(); 
    def parentnodepath = parentnode.getNodePath(); 
    node.log("parent node path: " + parentnodepath);
    
    //read properties from a node: 
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null ); 
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null); 
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

Notre fenêtre de journal permet de visualiser les éléments suivants :

![Sortie du journal pour l’accès à des chemins d’accès de nœud](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Sortie du journal pour l’accès à des chemins d’accès de nœud*


##<a id="frame_based_trim"></a>Ajustement de la trame de sortie de multibitrate MP4

À partir d’un flux de travail qui génère [un sortie MP4 à partir d’un format MXF du multibitrate d’entrée](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), nous allons maintenant l’examiner dans le rognage de la vidéo source basée sur le nombre d’images.

###<a id="frame_based_trim_start"></a>Vue d’ensemble de plan à ajouter ajustement

![Ajouter ajustement de flux de travail](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Ajouter ajustement de flux de travail*

###<a id="frame_based_trim_clip_list"></a>À l’aide de la liste de l’élément XML

Dans tous les flux de travail cours des didacticiels précédents, nous avons utilisé le composant fichiers de support d’entrée comme source d’entrée vidéo. Pour ce scénario spécifique, nous utilisons le composant Source de la liste à la place. Notez que cela ne soit pas le meilleur moyen de travail ; utiliser uniquement la Source de la liste élément lorsqu’il existe une raison de le faire (comme dans le dessous de cas, où nous faisons utiliser les fonctionnalités de suppression de la liste élément).

Pour passer de notre fichier média d’entrée à la liste Source, faites glisser le composant Source de liste d’élément sur l’aire de conception et connecter le code confidentiel de la liste XML pour le nœud XML de liste de Clip du Concepteur de flux de travail. Il doit remplir la liste Source avec les broches de sortie, en fonction de nos entrées vidéo. Maintenant connecter les broches vidéo non compressé et de fichier Audio non compressé à partir de la la liste Source respectives les encodeurs AVC les ENTRELACEUR de flux Audio. Supprimer l’entrée de fichier de média.

![Remplacer l’entrée du fichier Media avec la liste Source](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Remplacer l’entrée du fichier Media avec la liste Source*

Le composant de la liste Source prend comme entrée un « Clip liste XML ». Lorsque vous sélectionnez le fichier source pour le test localement, ce xml de liste de clip est remplie automatiquement pour vous.

![Propriété de la liste XML automatiquement](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriété de la liste XML automatiquement*

Recherche un peu plus près le code xml, c’est comment il ressemble à :

![Modifier la boîte de dialogue liste clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Modifier la boîte de dialogue liste clip*

Toutefois, cela ne reflète pas les capacités du clip liste xml. Il est une option que nous avons pour ajouter un élément « D’ajustement » sous la source vidéo et audio, comme suit :

![Ajout d’un élément de raccord à la liste des images](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Ajout d’un élément de raccord à la liste des images*

Si vous modifiez le xml de liste de clip comme ci-dessus et que vous effectuez une série de tests locale, vous verrez la vidéo correctement été retirés entre 10 et 20 secondes dans la vidéo.

Contrairement à ce qui se passe lorsque vous effectuez une exécution locale, cependant, cet même xml de liste de séquences n’aurait pas le même effet que lorsqu’elle est appliquée dans un workflow qui s’exécute dans Azure Media Services. Lors du démarrage de l’encodeur de prime d’Azure, le code xml de liste de séquences est généré à chaque fois, basé sur le fichier d’entrée, que la tâche de codage a été donnée. Cela signifie que toutes les modifications sur le code xml est malheureusement substituées.

Pour contrer le code xml de liste de séquences est effacé lors du démarrage d’une tâche de codage, nous pouvons générer de nouveau il à la volée juste après le début de notre flux de travail. Ces actions personnalisées peuvent être effectuées par le biais de ce que l'on appelle un « composant de script ». Pour plus d’informations, consultez [Présentation du composant de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).


Faites glisser un composant de script sur l’aire du concepteur et renommez-le « SetClipListXML ».

![Ajout d’un composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Ajout d’un composant de script*

Lorsque vous examinez les propriétés du composant de script, les quatre types différents de script sera indiqué, chaque configurable pour un script différent.

![Propriétés du composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriétés du composant de script*


###<a id="frame_based_trim_modify_clip_list"></a>Modification de la liste des images à partir d’un composant de script

Avant de nous pouvons réécrire le code xml de liste de séquences qui est généré au cours du démarrage du flux de travail, nous devez avoir accès à la propriété xml de liste de séquences et le contenu. Nous pouvons le faire comme suit :

    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![Liste des images entrantes en cours d’enregistrement](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Liste des images entrantes en cours d’enregistrement*

Tout d’abord, nous devons déterminer à partir de quel point jusqu’au point auquel vous souhaitez découper la vidéo. Pour rendre cette pratique pour l’utilisateur moins techniques du flux de travail, publier des deux propriétés à la racine du graphique. Pour ce faire, cliquez avec le bouton droit sur l’aire du concepteur et sélectionnez « Ajouter la propriété » :

- Première propriété : « ClippingTimeStart » du type : « Code temporel »
- Deuxième propriété : « ClippingTimeEnd » du type : « Code temporel »

![Ajouter la boîte de dialogue Propriétés de l’heure de début de découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Ajouter la boîte de dialogue Propriétés de l’heure de début de découpage*

![Publié le détourage props du temps sur la racine du flux de travail](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publié le détourage props du temps sur la racine du flux de travail*

Configurez les propriétés à une valeur appropriée :

![Configurer le démarrage de découpage et de fin des propriétés](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurer le démarrage de découpage et de fin des propriétés*

Maintenant, à partir de dans notre script, nous pouvons accéder à ces deux propriétés, comme suit :

    
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![Fenêtre journal indiquant les début et fin du découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Fenêtre journal indiquant les début et fin du découpage*

Nous allons analyser les chaînes de code temporel dans un plus pratique d’utiliser le formulaire, à l’aide d’une expression régulière simple :
    
    //parse the start timing: 
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1); 
    node.log("timecode start is: " + starttimecode); 
    def startframerate = startregresult.group(2); 
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend); 
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2); 
    node.log("framerate end is: " + endframerate);

![Fenêtre journal avec la sortie du code temporel analysé](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Fenêtre journal avec la sortie du code temporel analysé*

Grâce à ces informations à portée de main, nous pouvons maintenant modifier le code xml de liste de séquences pour refléter les heures de début et de fin pour le masque précis du film.

![Code de script pour ajouter des éléments](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Code de script pour ajouter des éléments*

Cela a été fait à travers des opérations de manipulation de chaîne normale. Le code xml de liste image clipart modifiée résultant est écrites à la propriété clipListXML à la racine du flux de travail par le biais de la méthode « setProperty ». La fenêtre journal après l’exécution d’un autre test serait indiquent les éléments suivants :

![Enregistrement de la liste des images qui en résulte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Enregistrement de la liste des images qui en résulte*

Effectuez une série de tests pour voir comment les flux de données vidéo et audio ont été écrêtées. Comme vous en ferez plus d’une série de tests avec des valeurs différentes pour les points d’ajustement, vous remarquerez que ceux ne seront pas tenu compte toutefois ! La raison en est que le concepteur, à la différence du runtime Azure, ne remplace pas le fichier xml de liste de séquences chaque exécution. Cela signifie que seulement la première fois que vous avez défini les points d’entrée /, entraîne le xml à transformer, tous les autres cas, notre clause de garde (si (clipListXML.indexOf ("<trim>") == -1)) empêche d’ajouter un autre élément de garniture lorsqu’il en existe déjà un présente le flux de travail.

Pour notre flux de travail permet de facilement tester localement, nous mieux ajouter du code de servitude vérifie si un élément de garniture était déjà présent. Dans ce cas, nous pouvons le supprimer avant de poursuivre en modifiant le fichier xml avec les nouvelles valeurs. Au lieu d’utiliser des manipulations de chaînes ordinaire, il est probablement plus sûr effectuer cette opération via le modèle objet xml réel de l’analyse.

Avant de nous pouvons ajouter un tel code cependant que nous devrons ajouter d’abord un certain nombre d’instructions import au début de notre script :
    
    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;

Après cela, nous pouvons ajouter le code de nettoyage requis :

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already: 
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes: 
    elementsToDelete.each{ 
        e -> e.getParentNode().removeChild(e);
    }; 
    node.log("deleted any existing trim nodes");
    
    //serialize the modified clip list xml dom into a string: 
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result); 
    clipListXML = result.getWriter().toString();
    
Ce code se place juste au-dessus du point auquel nous ajouter les éléments à la liste des séquences xml.

À ce stade, nous pouvons exécuter et modifier notre flux de travail que les temps d’autant que nous voulons tout en ayant les modifications appliquées à jamais.    

###<a id="frame_based_trim_clippingenabled_prop"></a>Ajout d’une propriété de commodité ClippingEnabled

Peut-être pas toujours souhaitée ajustement pour se produire, nous allons terminer notre flux de travail en ajoutant un indicateur booléen pratique qui indique ou non que nous comptons activer l’ajustement / coupe.

Comme avant, publiez une nouvelle propriété à la racine de notre workflow appelé « ClippingEnabled » de type « BOOLEAN ».

![Publié une propriété permettant de découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publié une propriété permettant de découpage*

Avec le sous la clause de protection simple, nous pouvons vérifier si l’ajustement est nécessaire et décider si notre liste des images en tant que tel doit être modifié ou non.

    //check if clipping is required: 
    def clippingrequired = node.getProperty("../ClippingEnabled"); 
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML); 
        node.log("no clipping required"); 
        return; 
    }


###<a id="code"></a>Code complet

    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;
    
    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping: 
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);
    
    //for local testing: delete any pre-existing trim elements 
    //from the clip list xml by parsing the xml into a DOM:
    
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder(); 
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath(); 
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }
    
    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e -> 
        e.getParentNode().removeChild(e); 
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return; 
    }

    //add trim elements to cliplist xml 
    if ( clipListXML.indexOf("<trim>") == -1 ) 
    {
        //trim video 
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode + 
            " </outPoint>\n </trim> \n"); 
        //trim audio 
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" + 
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML ); 
        node.setProperty("../clipListXml",clipListXML); 
    }


##<a name="also-see"></a>Voir aussi 

[Présentation de prime codage dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[L’utilisation de prime codage dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Encodage de contenu à la demande avec Azure Media Service](media-services-encode-asset.md#media_encoder_premium_workflow)

[Codecs et les Formats de flux de travail Media Codeur Premium](media-services-premium-workflow-encoder-formats.md)

[Exemples de fichiers de flux de travail](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Outil de Media Services Explorer Azure](http://aka.ms/amse)

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
