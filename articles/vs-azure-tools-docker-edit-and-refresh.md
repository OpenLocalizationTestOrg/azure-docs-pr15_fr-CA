<properties
   pageTitle="Débogage d’applications dans un conteneur Docker local | Microsoft Azure"
   description="Apprendre comment modifier une application qui s’exécute dans un conteneur Docker local, actualiser le conteneur par l’intermédiaire de modifier et d’actualiser et de définir des points d’arrêt de débogage"
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
   ms.date="07/22/2016"
   ms.author="mlearned" />

# <a name="debugging-apps-in-a-local-docker-container"></a>Débogage d’applications dans un conteneur de Docker local

## <a name="overview"></a>Vue d’ensemble
Visual Studio Tools pour Docker fournit un moyen cohérent de développer dans un et valider votre application localement dans un conteneur de Docker de Linux.
Vous n’êtes pas obligé de redémarrer le conteneur chaque fois que vous modifiez un code.
Cet article illustre comment utiliser la fonction « Modifier et actualiser » pour démarrer une application Web de base ASP.NET dans un conteneur Docker local, apportez les modifications nécessaires, puis actualisez le navigateur pour voir les modifications.
Il va également vous montrer comment définir des points d’arrêt pour le débogage.

> [AZURE.NOTE] Prise en charge du conteneur de Windows sera bientôt dans une version ultérieure

## <a name="prerequisites"></a>Conditions préalables
Les outils suivants doivent être installés.

- [Mise à jour de Visual Studio 2015 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- Installez la [Mise à jour de Visual Studio 2015 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [Kit de développement Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Pour exécuter les conteneurs Docker localement, vous aurez besoin d’un client local docker.
Vous pouvez utiliser la [Boîte à outils de Docker](https://www.docker.com/products/overview#/docker_toolbox) lancé qui nécessite de Hyper-V doit être désactivé, ou vous pouvez utiliser [Docker pour Windows version bêta](https://beta.docker.com) qui utilise Hyper-V et requiert Windows 10.

Si vous utilisez la boîte à outils de Docker, vous devez [configurer le client Docker](./vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. création d’une application web

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Ajoutez la prise en charge de Docker

[AZURE.INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]


## <a name="3-edit-your-code-and-refresh"></a>3. modifier votre code et l’actualisation

Pour parcourir rapidement les modifications apportées, vous pouvez lancer votre application dans un conteneur et continuer à apporter des modifications, les afficher, comme vous le feriez avec IIS Express.

1. Définir la Configuration de Solution `Debug` et appuyez sur la touche ** &lt;CTRL + F5 >** pour créer votre image de docker et exécutez-le.

    Une fois l’image de conteneur a été généré et s’exécute dans un conteneur Docker, Visual Studio lance l’application Web dans votre navigateur par défaut.
    Si vous utilisez le navigateur de Microsoft Edge ou dans le cas contraire disposez erreurs, reportez-vous à la section [dépannage](vs-azure-tools-docker-troubleshooting-docker-errors.md) .

1. Accédez à la page à propos, qui est où nous allons apporter nos modifications.

1. Revenez à Visual Studio et ouvrez `Views\Home\About.cshtml`.

1. Ajouter le contenu HTML suivant à la fin du fichier et enregistrez les modifications.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```

1.  Affichage de la fenêtre Sortie, lors de la génération de .NET est terminée et vous voir ces lignes, revenez à votre navigateur et actualisez la page à propos.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.  Vos modifications ont été appliquées.

## <a name="4-debug-with-breakpoints"></a>4. à déboguer avec des points d’arrêt

Souvent, les modifications devez plus inspection, en exploitant les fonctionnalités de débogage de Visual Studio.

1.  Revenez à Visual Studio et ouvrez`Controllers\HomeController.cs`

1.  Remplacez le contenu de la méthode About() par le texte suivant :

    ```
    string message = "Your application description page from wthin a Container";
    ViewData["Message"] = message;
    ````

1.  Définir un point d’arrêt à gauche de le `string message`... ligne.

1.  Cliquez sur ** &lt;F5 >** pour démarrer le débogage.

1.  Accédez à la page à propos de positionnement de votre point d’arrêt.

1.  Basculez vers Visual Studio pour afficher le point d’arrêt et inspecter la valeur d’un message.

    ![][2]

##<a name="summary"></a>Résumé

Avec les [Outils de 2015 Visual Studio pour Docker](https://aka.ms/DockerToolsForVS), vous pouvez obtenir la productivité du travail en local, avec le réalisme de la production de développement au sein d’un conteneur de Docker.

## <a name="troubleshooting"></a>Résolution des problèmes

[Dépannage du développement Docker de Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>En savoir plus sur les Docker avec Visual Studio et Windows Azure

- [Outils de docker pour Visual Studio](http://aka.ms/dockertoolsforvs) - développement de votre code de base de .NET dans un conteneur
- [Outils de docker pour Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) - générer et déployer des conteneurs de docker
- [Docker Code Tools pour Visual Studio](http://aka.ms/dockertoolsforvscode) – pour modifier les fichiers de docker, avec plus de scénarios e2e provenant des services de langage
- [Les informations de conteneur de Windows](http://aka.ms/containers)- Windows Server et les informations de serveur de NANOPARTICULES
- [Service de conteneur Azure](https://azure.microsoft.com/services/container-service/) - [contenu d’un conteneur Azure Service](http://aka.ms/AzureContainerService)
-    Pour plus d’exemples de l’utilisation de Docker, consultez [utilisation de Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) à partir de la connexion de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [Démo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Pour plus des Démarrages rapides à partir de la démo de HealthClinic.biz, consultez les [Démarrages rapides outils de développeur de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Divers outils de Docker

[Certains outils de grande docker (blog de Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Articles de bonne

[Introduction à la Microservices de NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Présentations

- [Steve Lasker : VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduction à ASP.NET Core @ build 2016 - où vous en démo](https://channel9.msdn.com/Events/Build/2016/B810)
- [Développement d’applications .NET dans des conteneurs, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
