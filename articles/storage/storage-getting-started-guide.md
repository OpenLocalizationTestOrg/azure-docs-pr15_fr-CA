<properties
    pageTitle="Mise en route avec le stockage Azure dans cinq minutes | Microsoft Azure"
    description="Rapidement une rampe sur Microsoft Azure BLOB, Table et files d’attente à l’aide des Démarrages rapides de stockage Azure, Visual Studio et l’émulateur de stockage Azure. Exécutez votre première application Azure Storage dans cinq minutes."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="get-started-with-azure-storage-in-five-minutes"></a>Mise en route avec le stockage Azure en cinq minutes

## <a name="overview"></a>Vue d’ensemble

Il est facile de commencer à développer avec stockage Azure. Ce didacticiel vous montre comment une application Azure Storage et faire fonctionner rapidement. Vous allez utiliser les modèles de démarrage rapide inclus avec le SDK Azure pour .NET. Ces Démarrages rapides contiennent des prêts à l’exécution de code qui illustre quelques scénarios de programmation de base avec le stockage Azure.

Pour plus d’informations sur le stockage Azure avant de plonger dans le code, consultez les [Étapes suivantes](#next-steps).

## <a name="prerequisites"></a>Conditions préalables

Vous devez les conditions préalables suivantes avant de commencer :

1. Pour compiler et générer l’application, vous aurez besoin d’une version de [Visual Studio](https://www.visualstudio.com/) est installé sur votre ordinateur.

2. Installez la dernière version de [SDK Azure pour .NET](https://azure.microsoft.com/downloads/). Le SDK inclut des exemples de démarrage rapide d’Azure projet, l’émulateur de stockage Azure et la [Bibliothèque de Client de stockage Azure pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Assurez-vous que vous avez installé sur votre ordinateur, le [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) telle qu’elle est requise par les exemples de démarrage rapide d’Azure projet que nous utiliserons dans ce didacticiel.

    Si vous n’êtes pas sûr de la version du.NET Framework est installée sur votre ordinateur, reportez-vous à la section [Comment : déterminer lequel.NET Framework Versions sont installés](https://msdn.microsoft.com/vstudio/hh925568.aspx). Ou bien, appuyez sur le bouton **Démarrer** ou sur la touche Windows, tapez **Control Panel**. Puis, cliquez sur **programmes** > **programmes et fonctionnalités**et déterminez si le 4.5 de.NET Framework est répertorié parmi les programmes installés.

4. Vous aurez besoin d’un abonnement Azure et un compte de stockage Azure.

    - Pour obtenir un abonnement Azure, consultez [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/), [Options d’achat](https://azure.microsoft.com/pricing/purchase-options/)et [Offre des membres](https://azure.microsoft.com/pricing/member-offers/) (pour les membres de MSDN, Microsoft Partner Network, BizSpark et des autres programmes de Microsoft).
    - Pour créer un compte de stockage dans Azure, reportez-vous à la section [comment créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account).

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>Exécutez votre première application Azure Storage stockage Azure dans le nuage

Une fois que vous avez un compte, vous pouvez créer une simple application de stockage Azure à l’aide d’un des exemples de Démarrages rapides en Azure projet dans Visual Studio. Ce didacticiel se concentre sur les exemples de projets pour le stockage Azure : **le stockage Azure : BLOB**, **le stockage Azure : fichiers**, **stockage Azure : files d’attente de**, et **le stockage Azure : Tables**:

1. Démarrez Visual Studio.
2. Dans le menu **fichier** , cliquez sur **Nouveau projet**.
3. Dans la boîte de dialogue **Nouveau projet** , cliquez sur **installer** > **modèles de** > **Visual C#** > **nuage** > **Démarrages rapides** > **Les Services de données**.
    une barre d’outils. Choisissez parmi les modèles suivants : **le stockage Azure : BLOB**, **stockage Azure : fichiers**, **stockage Azure : files d’attente de**, ou **le stockage Azure : Tables**.
    b. Assurez-vous que le **.NET Framework 4.5** est sélectionné comme framework cible.
    - 3 c. Spécifiez un nom pour votre projet et créer la solution Visual Studio, comme indiqué :

    ![Démarrages rapides Azure][Image1]

Vous souhaiterez peut-être examiner le code source avant d’exécuter l’application. Pour examiner le code, sélectionnez **Explorateur de solutions** , dans le menu **affichage** dans Visual Studio. Ensuite, double-cliquez sur le fichier Program.cs.

Ensuite, exécutez l’exemple d’application :

1.  Dans Visual Studio, sélectionnez **Explorateur de solutions** , dans le menu **affichage** . Ouvrez le fichier App.config et le commentaire à la chaîne de connexion pour l’émulateur de stockage Azure :

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  Ne commentez pas la chaîne de connexion pour le Service de stockage Azure et fournir le compte accès et le nom clé de stockage dans le fichier App.config :`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    Pour récupérer votre clé d’accès de stockage compte, voir [gérer vos clés d’accès de stockage](storage-create-storage-account.md#manage-your-storage-access-keys).

3.  Une fois que vous fournissez le nom de compte de stockage et touche d’accès rapide dans le fichier App.config, dans le menu **fichier** , cliquez sur **Enregistrer tout** pour enregistrer tous les fichiers projet.
4.  Dans le menu **Générer** , cliquez sur **Générer la Solution**.
5.  Dans le menu **Déboguer** , appuyez sur **F11** pour exécuter la solution étape par étape ou appuyez sur **F5** pour exécuter la solution.


## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>Exécuter localement votre première application Azure Storage l’émulateur de stockage Azure

L' [Émulateur de stockage Azure](storage-use-emulator.md) fournit un environnement qui émule les services Azure Blob, file d’attente et Table à des fins de développement local. Vous pouvez utiliser l’émulateur de stockage pour tester votre application de stockage de localement, sans création d’un abonnement Azure ou un compte de stockage et sans supporter aucun coût.

Pour l’essayer, nous allons créer une simple application de stockage Azure à l’aide d’un des exemples de Démarrages rapides en Azure projet dans Visual Studio. Ce didacticiel se concentre sur les exemples de projets **Azure file d’attente de stockage** , **Stockage par Table Azure**et **Le stockage Blob Azure**:

1. Démarrez Visual Studio.
2. Dans le menu **fichier** , cliquez sur **Nouveau projet**.
3. Dans la boîte de dialogue **Nouveau projet** , cliquez sur **installer** > **modèles de** > **Visual C#** > **nuage** > **Démarrages rapides** > **Les Services de données**.
    une barre d’outils. Choisissez parmi les modèles suivants : **le stockage Azure : BLOB**, **stockage Azure : fichiers**, **stockage Azure : files d’attente de**, ou **le stockage Azure : Tables**.
    b. Assurez-vous que le **.NET Framework 4.5** est sélectionné comme framework cible.
    c. Spécifiez un nom pour votre projet et créer la solution Visual Studio, comme indiqué :

    ![Démarrages rapides Azure][Image1]

4.  Dans Visual Studio, sélectionnez **Explorateur de solutions** , dans le menu **affichage** . Si vous avez déjà ajouté une, ouvrez le fichier App.config et le commentaire à la chaîne de connexion pour votre compte de stockage Azure. Ensuite, ne commentez pas la chaîne de connexion pour l’émulateur de stockage Azure :

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Vous souhaiterez peut-être examiner le code source avant d’exécuter l’application. Pour examiner le code, sélectionnez **Explorateur de solutions** , dans le menu **affichage** dans Visual Studio. Ensuite, double-cliquez sur le fichier Program.cs.

Ensuite, exécutez l’exemple d’application dans l’émulateur de stockage Azure :

1.  Appuyez sur le bouton **Démarrer** ou Windows, recherche de l' *émulateur de stockage de Microsoft Azure*et de démarrer l’application. Lors du démarrage de l’émulateur, vous verrez une icône et une notification dans la zone d’affichage des tâches de Windows.
2.  Dans Visual Studio, cliquez sur **Générer la Solution** dans le menu **Générer** .
3.  Dans le menu **Déboguer** , appuyez sur **F11** pour exécuter la solution étape par étape, ou appuyez sur **F5** pour exécuter la solution de bout en bout.

## <a name="next-steps"></a>Étapes suivantes

Consultez ces ressources pour en savoir plus sur le stockage Azure :

* [Introduction au stockage de Microsoft Azure](storage-introduction.md)
* [Mise en route avec Explorateur de stockage Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Mise en route de stockage Azure Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md)
* [Mise en route de stockage Azure Table à l’aide de .NET](storage-dotnet-how-to-use-tables.md)
* [Mise en route avec le stockage Azure file d’attente à l’aide de .NET](storage-dotnet-how-to-use-queues.md)
* [Mise en route de stockage Azure sous Windows](storage-dotnet-how-to-use-files.md)
* [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
* [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Bibliothèque de Client de stockage Microsoft Azure pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
