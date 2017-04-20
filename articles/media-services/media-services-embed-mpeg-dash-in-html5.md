<properties 
    pageTitle="L’incorporation d’une vidéo de diffusion adaptative MPEG-tiret dans une Application HTML5 avec DASH.js | Microsoft Azure" 
    description="Cette rubrique montre comment incorporer une vidéo MPEG-tiret adaptative en continu dans une Application HTML5 avec DASH.js." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>L’incorporation d’une vidéo de diffusion adaptative MPEG-tiret dans une Application HTML5 avec DASH.js

##<a name="overview"></a>Vue d’ensemble

MPEG-tiret est une norme ISO pour la diffusion adaptative en continu de contenu vidéo, ce qui offre des avantages considérables pour ceux qui souhaitent diffuser de la vidéo de haute qualité, adaptive sortie de diffusion en continu. Avec MPEG-tiret, le flux vidéo automatiquement supprime à une définition inférieure lorsque le réseau est encombré. Cela réduit la probabilité de la visionneuse de voir une vidéo « suspendue », tandis que le lecteur télécharge les secondes suivant pour lire (également appelé tampon). Que la réduction de la congestion du réseau, renvoie à son tour le lecteur vidéo dans un flux de qualité supérieur. Cette capacité d’adaptation de la bande passante requise entraîne également un temps de démarrage plus rapide de vidéo. Cela signifie que les premières secondes peuvent être lus dans un segment de qualité inférieur-fast pour télécharger et puis passer à une teneur supérieure en qualité une fois suffisant a été mis en mémoire tampon.

Dash.js est un lecteur de vidéo MPEG-tiret open source écrit en JavaScript. Son objectif est de fournir un lecteur robuste, multiplates-formes qui peut être réutilisé librement dans les applications qui nécessitent la lecture de la vidéo. Il fournit la lecture MPEG-tiret dans n’importe quel navigateur prenant actuellement en charge les Extensions de Source Media W3C (MSE, Internet Connection Firewall), Chrome, Microsoft Edge et IE11 (l’autres navigateurs ont indiqué leur intention de prendre en charge de MSE). Pour plus d’informations sur DASH.js, js voir le référentiel de dash.js GitHub.


##<a name="creating-a-browser-based-streaming-video-player"></a>Création d’un lecteur de vidéo en continu à partir d’un navigateur

Pour créer une page web simple qui affiche un lecteur vidéo avec attendus contrôle ces play, pause, rembobiner, etc., vous devez :

1. Créer une page HTML
1. Ajoutez la balise vidéo
1. Ajouter le lecteur dash.js
1. Initialiser le lecteur
1. Ajouter du style CSS
1. Afficher les résultats dans un navigateur qui implémente MSE

Lors de l’initialisation du lecteur peuvent être réalisées en quelques lignes de code JavaScript. À l’aide de dash.js, il est vraiment simple d’incorporer les vidéos MPEG-tiret dans vos applications basé sur le navigateur.

##<a name="creating-the-html-page"></a>Création de la Page HTML

La première étape consiste à créer une page HTML standard contenant l’élément **vidéo** , enregistrez ce fichier sous basicPlayer.html, comme l’illustre l’exemple suivant :

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

##<a name="adding-the-dashjs-player"></a>Ajout du lecteur DASH.js

Pour ajouter l’implémentation de référence dash.js à l’application, vous devez extraire le fichier dash.all.js à partir de la 1.0 version de projet de dash.js. Il doit être enregistré dans le dossier JavaScript de votre application. Ce fichier est un fichier de commodité qui regroupe tout le code nécessaire dash.js dans un seul fichier. Si vous avez une apparence autour du référentiel dash.js, vous trouvez les fichiers individuels, code de test et bien plus encore, mais si vous souhaitez faire est dash.js d’utiliser, puis le fichier dash.all.js est ce dont vous avez besoin.

Pour ajouter le lecteur dash.js à vos applications, ajoutez une balise de script à la section head du basicPlayer.html :

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


Ensuite, créez une fonction pour initialiser le lecteur lors du charge de la page. Ajoutez le script suivant après la ligne dans laquelle vous chargez dash.all.js :

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

Cette fonction crée d’abord un DashContext. Cela est utilisé pour configurer l’application pour un environnement d’exécution spécifique. D’un point de vue technique, il définit les classes de l’infrastructure d’injection de dépendance doit utiliser lors de la construction de l’application. Dans la plupart des cas, vous allez utiliser les Dash.di.DashContext.

Ensuite, instancier la classe principale du framework dash.js, MediaPlayer. Cette classe contient les principales méthodes nécessaires telles que lire et pause, gère la relation avec l’élément vidéo et gère également l’interprétation du fichier de Description de présentation multimédia (MPD) qui décrit la vidéo à lire.

La fonction startup() de la classe de MediaPlayer est appelée pour s’assurer que le lecteur est prêt à lire la vidéo. Entre autres, cette fonction permet de s’assurer que toutes les classes nécessaires (tel que défini par le contexte) ont été chargés. Une fois que le lecteur est prêt, vous pouvez joindre l’élément vidéo à l’aide de la fonction attachView(). Ainsi, le lecteur multimédia injecter des flux de données vidéo dans l’élément et également de contrôler la lecture si nécessaire.

Passez l’URL du fichier MPD à MediaPlayer afin qu’il connaît la vidéo qu’il est supposé lire. La fonction setupVideo() venez devra être exécutée une fois que la page est totalement chargé. Cela à l’aide de l’événement onload de l’élément body. Modifier vos <body> élément à :

    <body onload="setupVideo()">

Enfin, définissez la taille de l’élément vidéo à l’aide de CSS. Dans un environnement de diffusion ADAPTATIF, il est particulièrement important car la taille de la vidéo en cours de lecture peut changer que la lecture s’adapte aux conditions changeantes du réseau. Dans cette démonstration simple simplement forcer l’élément vidéo à 80 % de la fenêtre du navigateur disponible en ajoutant le code CSS suivant à la section head de la page :
    
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

##<a name="playing-a-video"></a>Lecture d’une vidéo

Pour lire une vidéo, pointez votre navigateur sur le fichier basicPlayback.html et cliquez sur Lire sur le lecteur vidéo s’affiché.


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi

[Développer des applications de lecteur vidéo](media-services-develop-video-players.md)

[Référentiel de dash.js de GitHub](https://github.com/Dash-Industry-Forum/dash.js) 
