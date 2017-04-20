<properties 
   pageTitle="Configuration et l’utilisation de l’émulateur de stockage avec Visual Studio | Microsoft Azure"
   description="Configuration et l’utilisation de l’émulateur de stockage avec Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configuration et l’utilisation de l’émulateur de stockage avec Visual Studio

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Vue d’ensemble
L’environnement de développement Azure SDK inclut l’émulateur de stockage, un utilitaire qui simule les services de stockage Blob et Table file d’attente disponibles dans Azure sur votre ordinateur de développement local. Si vous êtes création d’un service en nuage qui utilise les services de stockage Azure, ou écrivez n’importe quelle application externe qui appelle les services de stockage, vous pouvez tester votre code localement par rapport à l’émulateur de stockage. Les outils de Visual Studio de Microsoft Azure intégration de la gestion de l’émulateur de stockage dans Visual Studio. Les outils d’Azure initialiser l’émulateur de stockage base de données à la première utilisation, démarre le service d’émulateur de stockage lorsque vous exécutez ou déboguez votre code à partir de Visual Studio et fournit un accès en lecture seule aux données d’émulateur du stockage via l’Explorateur de stockage Azure.

Pour plus d’informations sur l’émulateur de stockage, y compris la configuration système requise et les instructions de configuration personnalisée, voir [utiliser l’émulateur de stockage Azure pour le développement et les tests](./storage/storage-use-emulator.md).

>[AZURE.NOTE] Il existe quelques différences de fonctionnalités entre la simulation d’émulateur de stockage et les services de stockage Azure. Voir les [différences entre l’émulateur de stockage et des Services de stockage Azure](./storage/storage-use-emulator.md) dans la documentation du Kit de développement logiciel Azure pour plus d’informations sur les différences spécifiques.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configuration d’une chaîne de connexion pour l’émulateur de stockage

Pour accéder à l’émulateur de stockage à partir de code au sein d’un rôle, vous devez configurer une chaîne de connexion qui pointe vers l’émulateur de stockage et qui peut être modifiée ultérieurement pour pointer vers un compte de stockage Azure. Une chaîne de connexion est un paramètre de configuration que votre rôle peut lire lors de l’exécution pour se connecter à un compte de stockage. Pour plus d’informations sur la façon de créer des chaînes de connexion, reportez-vous à la section [configuration de l’Application Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

>[AZURE.NOTE] Vous pouvez retourner une référence au compte de l’émulateur de stockage à partir de votre code à l’aide de la propriété **DevelopmentStorageAccount** . Cette approche fonctionne correctement si vous souhaitez accéder à l’émulateur de stockage à partir de votre code, mais si vous envisagez de publier votre application Azure, vous devez créer une chaîne de connexion pour accéder à votre compte de stockage Azure et modifiez votre code pour utiliser cette chaîne de connexion avant de le publier. Si vous basculez entre le compte de l’émulateur de stockage et d’un compte de stockage Azure fréquemment, une chaîne de connexion simplifie ce processus.

## <a name="initializing-and-running-the-storage-emulator"></a>Lors de l’initialisation et l’exécution de l’émulateur de stockage

Vous pouvez spécifier que lorsque vous exécutez ou déboguez votre service dans Visual Studio, Visual Studio lance automatiquement l’émulateur de stockage. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour votre projet **d’Azure** et choisissez **Propriétés**. Sous l’onglet **développement** , dans la liste de **l’Émulateur de stockage Azure démarrer** , choisissez **True** (si elle n’est pas déjà définie à cette valeur).

La première fois que vous exécutez ou déboguez votre service à partir de Visual Studio, l’émulateur de stockage lance un processus d’initialisation. Ce processus réserve des ports locaux pour l’émulateur de stockage et crée la base de données de stockage émulateur. Une fois terminé, ce processus n’a pas besoin d’exécuter à nouveau, à moins que la base de données de stockage émulateur est supprimé.

>[AZURE.NOTE] À partir de la version de juin 2012 des outils Azure, l’émulateur de stockage s’exécute, par défaut, dans SQL Express LocalDB. Dans les versions antérieures des outils Azure, l’émulateur de stockage s’exécute sur une instance par défaut de SQL Express 2005 ou 2008, que vous devez installer avant d’installer le SDK Azure. Vous pouvez également exécuter l’émulateur de stockage par rapport à une instance nommée de SQL Express ou portant un nom ou une instance par défaut de Microsoft SQL Server. Si vous avez besoin configurer l’émulateur de stockage exécute une instance autre que celle par défaut, voir [utilisation de l’émulateur de stockage Azure pour le développement et les tests](./storage/storage-use-emulator.md).

L’émulateur de stockage fournit une interface utilisateur pour afficher l’état des services de stockage local et de démarrer, arrêter et réinitialiser les. Une fois que le service d’émulateur de stockage a été démarré, vous pouvez afficher l’interface utilisateur ou démarrer ou arrêter le service en cliquant sur l’icône de zone de notification pour l’émulateur de Microsoft Azure dans la barre des tâches Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Affichage des données d’émulateur de stockage dans l’Explorateur de serveurs

Le nœud de stockage Azure dans l’Explorateur de serveurs vous permet d’afficher les données et modifier les paramètres de données blob et table dans vos comptes de stockage, y compris l’émulateur de stockage. Pour plus d’informations, reportez-vous à la section [de navigation et de gestion des ressources de stockage avec l’Explorateur de serveurs](https://msdn.microsoft.com/library/azure/ff683677.aspx) .
