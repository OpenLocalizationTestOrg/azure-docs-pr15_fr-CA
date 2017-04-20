<properties
   pageTitle="Création d’un projet Azure avec Visual Studio | Microsoft Azure"
   description="Création d’un projet Azure avec Visual Studio"
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

# <a name="creating-an-azure-project-with-visual-studio"></a>Création d’un projet Azure avec Visual Studio

L’Azure Tools pour Visual Studio fournit un modèle qui vous permet de créer un service en nuage pour Azure. Les outils également vous aident à configurer, déboguer et déployer le service en nuage sur Azure.

Une solution de service cloud Azure contient les types de projets suivants :

- **Projet Azure**

    Le projet Azure a des associations pour les projets de rôle de la solution. Il inclut également la définition de service et les fichiers de configuration de service. Le fichier de définition de service définit les paramètres d’exécution pour votre application, y compris les rôles qui sont requis, points de terminaison et la taille de la machine virtuelle. Le fichier de configuration de service configure le nombre d’instances d’un rôle est exécuté et les valeurs des paramètres définis pour un rôle. Pour plus d’informations sur ces paramètres, reportez-vous à la section [Comment : configurer les rôles pour un Service de Cloud Azure avec Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

- **Projet de rôle Web**

    Un rôle de travail effectue le traitement en arrière-plan. Un rôle worker peut communiquer avec les services de stockage et avec d’autres services basés sur Internet. Un rôle worker peut avoir n’importe quel nombre de points de terminaison HTTP, HTTPS ou TCP.

    - **Rôle de Web ASP.NET**, pour la création d’une application ASP.NET avec un frontal web
    - **Rôle de Web ASP.NET MVC5**
    - **Rôle de Web ASP.NET MVC4**
    - **Rôle de Web ASP.NET MVC3**
    - **Rôle de Web de Service WCF**, pour la création d’un service WCF
    - **Rôle de Silverlight Business Application Web** (requiert Visual Studio 2012)

- **Rôle de travail du cache**

    Un rôle qui fournit un cache dédié à votre application.

- **Rôle de travail avec la file d’attente de Bus de Service**

    Une file de bus de service qui fournit la fonctionnalité message queuing de communiquer avec le processus de travail. Pour plus d’informations, voir [comment des files d’attente de Bus de Service utiliser](http://go.microsoft.com/fwlink/?LinkId=260560).

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>Pour créer un projet de service cloud Azure dans Visual Studio

1. Démarrez Visual Studio de Microsoft en tant qu’administrateur.

1. Dans la barre de menus, cliquez sur **fichier**, **Nouveau**, **projet**.

1. Dans le volet **Types de projets** , choisissez **Cloud** dans le projet Visual C# ou Visual Basic nœuds du modèle.

1. Dans le volet **modèles** , choisissez **Azure Cloud Service**.

1. Spécifier la version de la de.NET Framework que vous souhaitez utiliser pour développer votre projet.

1. Entrez un nom et un emplacement pour votre projet et un nom pour la solution. Cliquez sur le bouton **OK** .

1. Dans la boîte de dialogue **Nouveau projet Azure** , sélectionnez les rôles que vous souhaitez ajouter et cliquez sur le bouton flèche droite pour les ajouter à votre solution. Vous pouvez ajouter autant de rôles que nécessaire.

1. Pour renommer un rôle que vous avez ajoutés à votre projet, pointez sur le rôle dans la boîte de dialogue **Nouveau projet Azure** et sélectionnez l’icône **Renommer** à droite du rôle. Vous pouvez également renommer un rôle au sein de votre solution après que qu’il a été ajouté.
