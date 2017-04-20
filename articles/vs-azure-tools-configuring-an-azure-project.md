<properties
   pageTitle="Configurer un projet de Service Cloud Azure avec Visual Studio | Microsoft Azure"
   description="Découvrez comment configurer un projet de service cloud Azure dans Visual Studio, en fonction de vos besoins pour ce projet."
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

# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Configurer un projet de Service Cloud Azure avec Visual Studio

Vous pouvez configurer un projet de service cloud Azure, selon vos besoins pour ce projet. Vous pouvez définir les propriétés du projet pour les catégories suivantes :

- **Publier un service en nuage vers Azure**

  Vous pouvez définir une propriété pour s’assurer qu’un service cloud existant déployé sur Azure n’est pas accidentellement supprimé.

- **Exécuter ou déboguer un service cloud sur l’ordinateur local**

  Vous pouvez sélectionner une configuration de service à utiliser et indiquez si vous souhaitez démarrer l’émulateur de stockage Azure.

- **Valider un package de service cloud lors de sa création**

  Vous pouvez décider de traiter tous les avertissements comme des erreurs de sorte que vous pouvez vous assurer que le package de service cloud déployer sans problème. Cela réduit le temps d’attente si vous déployez et puis découvrez qu’une défaillance s’est produite.

L’illustration suivante montre comment sélectionner une configuration à utiliser lorsque vous exécutez ou déboguez votre service cloud localement. Vous pouvez définir les propriétés de projet dont vous avez besoin à partir de cette fenêtre, comme indiqué dans l’illustration.

![Configurer un projet de Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## <a name="to-configure-an-azure-cloud-service-project"></a>Pour configurer un projet de service cloud Azure

1. Pour configurer un projet de service cloud à partir de **L’Explorateur de solutions**, ouvrez le menu contextuel pour le projet de service cloud, puis choisissez **Propriétés**.

  Une page avec le nom du projet de service cloud s’affiche dans l’éditeur de Visual Studio.

1. Cliquez sur l’onglet de **développement** .

1. Pour vous assurer que vous ne supprimez pas accidentellement un déploiement existant dans Azure, dans l’invite avant de supprimer une liste de déploiement existante, sélectionnez **True**.

1. Pour sélectionner la configuration du service que vous souhaitez utiliser lorsque vous exécutez ou déboguez votre service cloud localement, dans la liste de la **configuration du Service de** choisir la configuration du service.

  >[AZURE.NOTE] Si vous souhaitez créer une configuration de service à utiliser, consultez Comment : gérer des Configurations de Service et des profils. Si vous souhaitez modifier une configuration de service pour un rôle, consultez [comment configurer les rôles pour un service cloud Azure avec Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Pour démarrer l’émulateur de stockage Azure lorsque vous exécutez ou déboguez votre service cloud localement, dans l' **émulateur de stockage Azure de démarrer**, cliquez sur **True**.

1. Pour vous assurer que vous ne pouvez pas publier s’il existe des erreurs de validation de package, de **considérer les avertissements comme des erreurs**, cliquez sur **True**.

1. Pour vous assurer que votre rôle web utilise le même port chaque fois qu’il démarre localement dans IIS Express, **utiliser des ports de projet web**, cliquez sur **True**. Pour utiliser un port spécifique pour un projet web particulier, ouvrez le menu contextuel du projet web, cliquez sur l’onglet **Propriétés** , cliquez sur l’onglet **Web** et modifier le numéro de port dans le paramètre de **l’Url du projet** dans la section **IIS Express** . Par exemple, entrez `http://localhost:14020` en tant que l’URL du projet.

1. Pour enregistrer les modifications que vous avez apportées aux propriétés du projet de service cloud, cliquez sur le bouton **Enregistrer** dans la barre d’outils.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon de configurer des projets de service cloud Azure dans Visual Studio, reportez-vous à la section [projet Azure de votre configuration à l’aide de plusieurs configurations de service](vs-azure-tools-multiple-services-project-configurations.md).
