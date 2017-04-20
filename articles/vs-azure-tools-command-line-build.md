<properties
   pageTitle="Génération de ligne de commande pour Azure | Microsoft Azure"
   description="Génération de ligne de commande pour Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="command-line-build-for-azure"></a>Génération de ligne de commande pour Azure

## <a name="overview"></a>Vue d’ensemble

Vous pouvez créer un package de déploiement d’Azure en exécutant MSBuild à l’invite de commande. Vous pouvez configurer et définir des builds pour le débogage, intermédiaire et de production, en plus de l’automatisation de certaines du processus de génération.


## <a name="microsoft-build-engine-msbuild"></a>Microsoft Build Engine (MSBuild)

À l’aide de Microsoft Build Engine (MSBuild), vous pouvez plate-forme de génération des environnements lab où Visual Studio n’est pas installé. MSBuild utilise un format XML pour les fichiers de projet de l’extensible et entièrement pris en charge par Microsoft. Dans ce format de fichier, vous pouvez décrire les éléments devant être pour un ou plusieurs plates-formes et configurations.

Vous pouvez également exécuter MSBuild à l’invite de commande, et que cette rubrique décrit cette approche. En définissant des propriétés à une invite de commande, vous pouvez créer des configurations spécifiques d’un projet. De même, vous pouvez également définir les cibles de la commande MSBuild va générer. Pour plus d’informations sur les paramètres de ligne de commande MSBuild, consultez [Référence de la ligne de commande MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="installation"></a>Installation

Comme décrit dans la procédure suivante, vous devez installer les outils et les logiciels sur le serveur de build avant de pouvoir créer un package d’Azure à l’aide de MSBuild :

1. Installez.NET Framework 4 ou version ultérieure, qui inclut des MSBuild.

1. Installez les [Outils de création de Azure](http://go.microsoft.com/fwlink/?LinkId=394615) (recherchez MicrosoftAzureAuthoringTools-x64.msi ou MicrosoftAzureAuthoringTools-x86.msi.

1. Installez les [Bibliothèques Azure pour .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (recherchez MicrosoftAzureLibsForNet-x64.msi ou MicrosoftAzureLibs-x86.msi.

1. Copiez le fichier Microsoft.WebApplication.targets à partir d’une installation de Visual Studio sur un autre ordinateur.

    Le fichier se trouve dans le répertoire C:\Program Files (x86) \MSBuild\Microsoft\Visual Studio\v12.0\WebApplications (v11.0 pour Visual Studio 2012) et vous devez le copier dans le même répertoire sur le serveur de build.

1. Installez les [Outils Azure pour Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).

    Recherchez WindowsAzureTools.vs120.exe générer des projets de Visual Studio 2013.

## <a name="msbuild-parameters"></a>Paramètres de MSBuild

La façon la plus simple pour créer un package est d’exécuter MSBuild avec le `/t:Publish` option. Par défaut, cette commande crée un répertoire pour le dossier racine du projet, tels que les ProjectDir\bin\Configuration\app.publish\. lorsque vous générez un projet Azure, vous générez deux fichiers, le fichier du package et le fichier de configuration qui l’accompagne :

- Project.cspkg

- ServiceConfiguration.TargetProfile.cscfg

Par défaut, chaque projet Azure comprend un fichier de configuration de service local (débogage) génère et un autre pour les builds de nuage (intermédiaire ou production), mais vous pouvez ajouter ou supprimer des fichiers de configuration de service selon vos besoins. Lorsque vous créez un package dans Visual Studio, vous devez le fichier de configuration de service à inclure avec le package. Lorsque vous créez un package à l’aide de MSBuild, le fichier de configuration du service local est inclus par défaut. Pour inclure un autre fichier de configuration du service, définissez les `TargetProfile` propriétés de la commande MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Si vous souhaitez utiliser un autre répertoire pour les fichiers de configuration et le package stocké, définir le chemin d’accès à l’aide de la `/p:PublishDir=Directory\` option, y compris le séparateur de barre oblique inverse à droite.

## <a name="deployment"></a>Déploiement

Une fois le package créé, vous pouvez la déployer vers Azure. Pour un didacticiel illustrant ce processus, consultez le site Web d’Azure. Pour plus d’informations sur la façon d’automatiser ce processus, consultez [Fourniture continue de Services en nuage dans Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).
