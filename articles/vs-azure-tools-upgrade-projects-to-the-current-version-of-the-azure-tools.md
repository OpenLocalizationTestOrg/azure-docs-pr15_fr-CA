<properties
   pageTitle="La mise à niveau des projets vers la version actuelle des outils Azure | Microsoft Azure"
   description="Découvrez comment mettre à niveau un projet Azure dans Visual Studio à la version actuelle des outils Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Comment faire pour mettre à niveau des projets vers la version actuelle de l’Azure Tools pour Visual Studio

## <a name="overview"></a>Vue d’ensemble

Après l’installation de la version actuelle de la Azure Tools (ou une version précédente est plus récente que 1.6), tous les projets qui ont été créés à l’aide d’un Azure Tools version avant 1.6 (novembre 2011) va être mis à niveau automatiquement dès que vous les ouvrez. Si vous avez créé des projets à l’aide de la version 1.6 (novembre 2011) de ces outils et vous avez toujours cette version installée, vous pouvez ouvrir ces projets dans l’ancienne version et décidez ultérieurement si vous souhaitez mettre à niveau.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Comment votre projet change lorsque vous mettez à niveau

Si un projet est mis à niveau automatiquement ou si vous spécifiez que vous souhaitez mettre à niveau, votre projet est modifié pour fonctionner avec les versions actuelles de certains assemblys, et certaines propriétés sont également modifiées comme décrit dans cette section. Si votre projet requiert d’autres modifications pour être compatible avec la version la plus récente des outils, vous devez apporter ces modifications manuellement.

- Le fichier web.config pour les rôles web et le fichier app.config pour les rôles de travail sont mis à jour pour faire référence à la version la plus récente de Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.

- Les assemblys Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll et Microsoft.WindowsAzure.ServiceRuntime.dll sont mis à niveau vers les nouvelles versions.

- Publier les profils qui ont été stockées dans le fichier de projet Azure (.ccproj) sont déplacés vers un fichier distinct, avec l’extension .azurePubXml, dans le sous-répertoire de la **Publier** .

- Certaines propriétés dans le profil de publication sont mis à jour pour prendre en charge des fonctionnalités nouvelles et modifiées. **AllowUpgrade** est remplacé par **DeploymentReplacementMethod** dans la mesure où vous pouvez mettre à jour un service en nuage déployés simultanément ou de manière incrémentielle.

- La propriété **UseIISExpressByDefault** est ajoutée et la valeur false pour que le serveur web qui est utilisé pour le débogage ne changer pas automatiquement à partir d’Internet Information Services (IIS) pour IIS Express. IIS Express est le serveur web par défaut pour les projets créés avec les versions plus récentes des outils.

- Si la mise en cache d’Azure est hébergé dans une ou plusieurs des rôles de votre projet, certaines propriétés de la configuration de service (fichier .cscfg) et la définition de service (fichier .csdef) sont modifiées lorsqu’un projet est mis à niveau. Si le projet utilise le package NuGet la mise en cache d’Azure, le projet est mis à niveau vers la version la plus récente du package. Vous devez ouvrir le fichier web.config et vérifiez que la configuration du client a été correctement maintenue pendant le processus de mise à niveau. Si vous avez ajouté les références aux assemblys de client de mise en cache Azure sans utiliser le package NuGet, ces assemblys ne sont pas mis à jour ; Vous devez mettre à jour manuellement ces références vers les nouvelles versions.

>[AZURE.IMPORTANT] Pour les projets F#, vous devez mettre à jour manuellement les références aux assemblys Azure afin qu’ils référencent les versions plus récentes de ces assemblys.

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Comment mettre à niveau un projet Azure vers la version actuelle

1. Installer la version actuelle des outils Azure dans l’installation de Visual Studio que vous souhaitez utiliser pour le projet mis à niveau et ouvrez le projet que vous souhaitez mettre à niveau. Si le projet a été créé avec un Azure Tools version avant 1.6 (novembre 2011), le projet est mis à niveau automatiquement vers la version actuelle. Si le projet a été créé avec le novembre 2011 version et cette version est toujours installée, le projet s’ouvre dans cette version.

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour le nœud du projet, choisissez **Propriétés**et puis cliquez sur l’onglet de **l’Application** de la boîte de dialogue qui s’affiche.

    L’onglet **applications** affiche la version des outils associée à du projet. Si la version actuelle d’Azure Tools s’affiche, le projet a déjà été mis à niveau. Si vous avez installé une version plus récente des outils à ce que l’onglet indique, un bouton de **mise à niveau** s’affiche.

1. Cliquez sur le bouton de **mise à niveau** d’un projet de mise à niveau vers la version actuelle des outils.

1. Générez le projet, puis traitez toutes les erreurs qui résultent de modifications de l’API. Pour plus d’informations sur la façon de modifier votre code pour la nouvelle version, consultez la documentation de l’API.
