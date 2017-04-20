<properties
   pageTitle="Résolution des erreurs de Client Docker sur Windows à l’aide de Visual Studio | Microsoft Azure"
   description="Résoudre les problèmes que vous rencontrez lorsque vous utilisez Visual Studio pour créer et déployer des applications web à Docker sur Windows à l’aide de Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# <a name="troubleshooting-visual-studio-docker-development"></a>Dépannage du développement Docker de Visual Studio

Lorsque vous travaillez avec les outils Visual Studio pour Docker aperçu, vous pouvez rencontrer des problèmes en raison de la nature de l’aperçu.
Voici certains problèmes et résolutions courants.


## <a name="unable-to-validate-volume-mapping"></a>Impossible de valider le mappage de volume
Mappage de volume est requis pour partager le code source et les fichiers binaires de votre application avec le dossier de l’application dans le conteneur.  Mappages de volume spécifique sont contenues dans les fichiers docker-compose.dev.debug.yml et de docker-compose.dev.release.yml. Comme les fichiers sont modifiés sur votre ordinateur hôte, les conteneurs de répercuter ces modifications dans une structure de dossiers similaire.

Pour activer le mappage de volume, ouvrez **paramètres...** à partir de l’icône de barre d’état « moby » Docker pour Windows et puis sélectionnez l’onglet **Partage de lecteurs** .  Assurez-vous que la lettre du lecteur qui héberge votre projet ainsi que la lettre du lecteur où se trouve le % USERPROFILE% sont partagés, les archiver, puis cliquez sur **Appliquer**.

Pour vérifier si le mappage de volume fonctionne, une fois que l’ou les disques ont été partagé, reconstruction et sur F5 à partir de Visual Studio ou essayez les éléments suivants à partir d’une commande invite de commandes :

*Dans une invite de commande Windows*

*[Remarque : cela suppose que votre dossier utilisateurs se trouve sur le lecteur « C » et qu’il a été partagé.  Mise à jour si nécessaire si vous avez partagé d’un autre lecteur]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Dans le conteneur de Linux*

```
/ # ls
```

Vous devez voir une liste à partir du dossier utilisateurs/Public de répertoires.
Si aucun fichier ne s’affichent, et que votre dossier /c/Users/Public n’est pas vide, mappage de volume n’est pas configuré correctement. 

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Passez dans le répertoire repaire du ver géant pour voir le contenu de la `/c/Users/Public` répertoire :

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**Remarque :** *Lorsque vous travaillez avec des machines virtuelles de Linux, le système de fichiers conteneur respecte la casse.*

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Génération : Tâche de « PrepareForBuild » a échoué inopinément.

Microsoft.DotNet.Docker.CommandLine.ClientException : Une erreur s’est produite lors de la tentative de connexion :

Vérifiez que l’hôte docker par défaut est en cours d’exécution. Ouvrez une invite de commande et exécutez :

```
docker info
```

Si elle retourne une erreur, puis vous essayez de démarrer l’application de bureau **Docker pour Windows** .  Si l’application de bureau est en cours d’exécution puis l’icône **moby** dans la barre d’état doit être visible. Cliquez avec le bouton droit sur l’icône de la barre d’état et d’ouvrir les **paramètres**.  Cliquez sur l’onglet de **Réinitialiser** , puis **redémarrage Docker..**.

##<a name="manually-upgrading-from-version-031-to-040"></a>Mise à niveau manuelle à partir de la version de 0,31 à 0,40


1. Sauvegarde du projet
1. Supprimez les fichiers suivants dans le projet :

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Fermez la Solution et supprimez les lignes suivantes à partir du fichier .xproj :

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Rouvrez la Solution.
1. Supprimez les lignes suivantes à partir du fichier Properties\launchSettings.json :

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Supprimer les fichiers suivants de Docker de project.json dans les publishOptions :

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Désinstallez la version précédente et réinstallez Docker outils 0,40, puis sur **Ajouter -> prise en charge de Docker** dans le menu contextuel de votre Web de base ASP.Net ou une Application Console. Cela ajoutera le nouveau artefacts Docker requis à votre projet. 

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Une boîte de dialogue d’erreur se produit lorsque vous tentez **d’Ajouter -> prise en charge de la Docker** ou le débogage (F5) une Application de base de ASP.NET dans un conteneur

Nous avons vu parfois la désinstallation et l’installation des extensions, cache MEF (Managed Extensibility Framework) de Visual Studio peut être endommagé. Dans ce cas il peut provoquer une erreur diverses boîtes de dialogue lors de l’ajout de la prise en charge de la Docker et/ou tente d’exécuter ou de déboguer (F5) votre Application de base ASP.NET. Comme solution temporaire, exécutez les étapes suivantes pour supprimer et régénérer le cache MEF.

1. Fermez toutes les instances de Visual Studio
1. Ouvrir %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. Supprimez les dossiers suivants
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Ouvrez Visual Studio
1. Essayez à nouveau du scénario 
