<properties 
    pageTitle="Comment faire pour configurer un ordinateur pour le développement avec .NET des Services multimédia" 
    description="Obtenir des informations sur les conditions requises pour les Services de support en utilisant le Kit de développement de Services de support pour .NET. Apprenez également comment créer une application Visual Studio." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/24/2016"  
    ms.author="juliako"/>

#<a name="media-services-development-with-net"></a>Développement de Services de support avec .NET

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Cette rubrique explique comment démarrer le développement d’applications de Services de support à l’aide de .NET.

La bibliothèque **Azure Media Services.NET SDK** vous permet de programmer des Services de support à l’aide de .NET. Pour le rendre encore plus facile à développer avec .NET, la bibliothèque **d’Extensions de Azure Media Services .NET SDK** est fournie. Cette bibliothèque contient un ensemble de méthodes d’extension et des fonctions d’assistance qui va simplifier votre code .NET. Les deux bibliothèques sont disponibles par le biais de **NuGet** et **GitHub**.


##<a name="prerequisites"></a>Conditions préalables

-   Un compte de Services de support dans un abonnement Azure nouvel ou existant. Reportez-vous à la rubrique [comment créer un compte de Services de support](media-services-portal-create-account.md).
-   Systèmes d’exploitation : Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
-   .NET Framework 4.5.
-    Visual Studio 2015, 2013 de Visual Studio, Visual Studio 2012 ou Visual Studio 2010 SP1 (Professionnel, Premium, Ultimate ou Express).


##<a name="create-and-configure-a-visual-studio-project"></a>Créer et configurer un projet Visual Studio

Cette section vous indique comment créer un projet dans Visual Studio et le configurer pour le développement de Services de support.  Dans ce cas le projet est une application de console C# Windows, mais les mêmes étapes d’installation indiqués ici s’appliquent à d’autres types de projets que vous pouvez créer des applications des Services de support (par exemple, une application Windows Forms ou une application Web d’ASP.NET).

Cette section indique comment utiliser **NuGet** pour ajouter des Media Services SDK .NET et autres bibliothèques dépendantes.

Vous pouvez également obtenir les derniers bits de Media Services SDK .NET à partir de GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) et [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), générez la solution et ajouter les références au projet client. Notez que toutes les dépendances nécessaires téléchargés et extrait automatiquement.

1. Créez une nouvelle Application de Console C# dans Visual Studio 2010 SP1 ou des versions ultérieures de VS. Entrez les **nom**, **emplacement**et **nom de la Solution**, puis cliquez sur OK.

2. Générez la solution.

2. Utilisez **NuGet** pour installer et ajouter **Les Extensions .NET SDK Azure Media Services**. L’installation de ce package, également installe le **Kit de développement logiciel Media Services .NET** et ajoute toutes les autres dépendances requises.

    Assurez-vous que vous disposez de la version la plus récente de NuGet installé. Pour plus d’informations et d’installation, consultez [NuGet](http://nuget.codeplex.com/).

2. Dans l’Explorateur de solutions, double-cliquez sur le nom du projet et choisissez packages NuGet de gérer...

    La boîte de dialogue Manage NuGet Packages s’affiche.

3. Dans la galerie en ligne, rechercher des Extensions de MediaServices d’Azure, choisissez les Extensions .NET SDK Azure Media Services et puis cliquez sur le bouton Installer.

    Le projet est modifié et les références aux autres assemblys dépendants, Media Services .NET SDK et les Extensions de Media Services .NET SDK sont ajoutées.

4. Pour promouvoir un environnement de développement plus propre, envisagez d’activer la restauration du Package NuGet. Pour plus d’informations, consultez [restaurer de Package NuGet »](http://docs.nuget.org/consume/package-restore).

3. Ajoutez une référence à l’assembly **System.Configuration** . Cet assembly contient l’API System.Configuration. **ConfigurationManager** de classe qui est utilisée pour accéder aux fichiers de configuration (par exemple, App.config).

    Pour ajouter des références à l’aide de la boîte de dialogue Gérer les références, cliquez droit sur le nom du projet dans l’Explorateur de solutions. Ensuite, sélectionnez Ajouter et références.

    La boîte de dialogue Gérer les références s’affiche.

4. Dans les assemblys du .NET framework, recherchez et sélectionnez l’assembly System.Configuration appuyez sur OK.
5. Ouvrez le fichier App.config (ajouter le fichier à votre projet si elle n’a pas été ajoutée par défaut) et ajouter une section *appSettings* du fichier.     
Définissez les valeurs pour votre Azure Media Services compte nom et compte la clé, comme illustré dans l’exemple suivant.

    Pour rechercher le nom et la clé des valeurs, accédez au portail Azure et sélectionnez votre compte. La fenêtre Paramètres s’affiche sur la droite. Dans la fenêtre Paramètres, sélectionnez clés. En cliquant sur l’icône en regard de chaque zone de texte de copie la valeur dans le Presse-papiers système.


        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

6. Remplacer les instructions **using** existantes au début du fichier Program.cs par le code suivant.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

À ce stade, vous êtes prêt à commencer à développer une application de Services de support.    


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
