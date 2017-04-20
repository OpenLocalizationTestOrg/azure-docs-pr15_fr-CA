<properties 
    pageTitle="Créez des applications de logique dans Visual Studio | Microsoft Azure" 
    description="Créez un projet dans Visual Studio pour créer et déployer votre application logique." 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/> 
    
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Générer et déployer des applications de logique dans Visual Studio

Bien que le [Portail Azure](https://portal.azure.com/) vous donne un excellent moyen de concevoir et gérer vos applications logique, vous pouvez également concevoir et déployer votre application logique à partir de Visual Studio au lieu de cela.  Logique d’applications est fourni avec un ensemble riche de Visual Studio d’outils qui vous permet de créer une application de logique à l’aide du concepteur, configuration de modèles de déploiement et de l’automatisation et le déploiement dans n’importe quel environnement.  

## <a name="installation-steps"></a>Étapes d’installation

Voici les étapes pour installer et configurer les outils Visual Studio pour les applications de la logique.

### <a name="prerequisites"></a>Conditions préalables

- [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [Dernière version du SDK de Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou supérieur)
- [PowerShell azure] (https://github.com/Azure/azure-powershell#installation)
- Accès au web lorsque vous utilisez le concepteur intégré

### <a name="install-visual-studio-tools-for-logic-apps"></a>Installer les outils Visual Studio pour les applications de logique

Une fois que vous avez les conditions préalables à l’installation, 

1. Ouvrez Visual Studio 2015 au menu **Outils** et sélectionnez les **mises à jour et des Extensions**
1. Sélectionnez la catégorie **en ligne** pour effectuer une recherche en ligne
1. Rechercher **Logique d’applications** afficher les **Outils d’applications logique Azure pour Visual Studio**
1. Cliquez sur le bouton **Télécharger** pour télécharger et installer l’extension
1. Redémarrez Visual Studio après l’installation

> [AZURE.NOTE] Vous pouvez également télécharger l’extension directement à partir de [ce lien](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)

Une fois installé, vous ne pourrez pas utiliser le projet de groupe de ressources Azure avec le Concepteur d’application logique.

## <a name="create-a-project"></a>Créer un projet

1. Allez dans le menu **fichier** et sélectionnez **Nouveau** >  **projet** (ou vous pouvez accéder à **Ajouter** et cliquez sur **Nouveau projet** pour l’ajouter à une solution existante) :  ![menu fichier](./media/app-service-logic-deploy-from-vs/filemenu.png)

1. Dans la boîte de dialogue Rechercher le **nuage**et puis sélectionnez le **Groupe de ressources Azure**. Tapez un **nom** , puis sur **OK**.
    ![Ajouter le nouveau projet](./media/app-service-logic-deploy-from-vs/addnewproject.png)

1. Sélectionnez le modèle **d’application de la logique** . Cela créera pour commencer avec un modèle de déploiement d’application logique vide.
    ![Sélectionnez modèle Azure](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

1. Une fois que vous avez sélectionné votre **modèle**, cliquez sur **OK**.

    Maintenant, votre projet d’application de logique est ajouté à votre solution. Vous devez voir le fichier de déploiement dans l’Explorateur de solutions :  

    ![Déploiement](./media/app-service-logic-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>L’utilisation du Concepteur d’application logique

Une fois que vous avez un projet de groupe de ressources Azure qui contient une application de logique, vous pouvez ouvrir le concepteur dans Visual Studio pour vous aider à créer le flux de travail.  Le concepteur requiert une connexion internet pour les connecteurs pour les propriétés disponibles et les données de la requête (par exemple, si vous utilisez le connecteur de Dynamics CRM Online, le concepteur interroge votre instance CRM pour répertorier personnalisés disponibles et les propriétés par défaut).

1. Avec le bouton droit sur le `<template>.json` de fichiers et sélectionnez **Ouvrir avec le Concepteur d’application logique** (ou `Ctrl+L`)
1. Choisissez l’abonnement, un groupe de ressources et un emplacement pour le modèle de déploiement
    - Il est important de noter que la conception d’une application logique va créer les ressources de **Connexion de l’API** pour rechercher des propriétés lors de la conception.  Le groupe de ressources sélectionné sera le groupe de ressources utilisé pour créer des connexions au moment du design.  Vous pouvez afficher ou modifier des connexions de l’API en ouvrant le portail Azure et pour les **Connexions de l’API de**la navigation.
    ![Sélecteur d’abonnement](./media/app-service-logic-deploy-from-vs/designer_picker.png)
1. Le concepteur doit restituer basé sur la définition de la `<template>.json` fichier.
1. Vous pouvez désormais créer et concevoir votre application logique et mis à jour des modifications dans le modèle de déploiement.
    ![Concepteur dans Visual Studio](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

Vous verrez également `Microsoft.Web/connections` ressources ajoutées à votre fichier de ressources pour toutes les connexions nécessaires pour l’application de la logique de la fonction.  Ces propriétés de connexion pouvant être définies lorsque vous déployez et gérées après le déploiement de **Connexions d’API** dans le portail Azure.

### <a name="switching-to-the-json-code-view"></a>Basculer vers la vue code JSON

Vous pouvez sélectionner l’onglet du **Mode Code** au bas du concepteur pour basculer vers la représentation JSON de l’application de la logique.  Pour revenir à la ressource complète JSON, cliquez sur le `<template>.json` de fichiers et sélectionnez **Ouvrir**.

### <a name="saving-the-logic-app"></a>L’enregistrement de l’application logique

Vous pouvez enregistrer l’application logique à tout moment via le bouton **Enregistrer** ou `Ctrl+S`.  S’il y a des erreurs avec votre application logique au moment où que vous enregistrez, ils seront affiche dans la fenêtre **sortie** de Visual Studio.

## <a name="deploying-your-logic-app"></a>Déploiement de votre application logique

Enfin, une fois que vous avez configuré votre application, vous pouvez déployer directement à partir de Visual Studio en seulement quelques étapes. 

1. Avec le bouton droit sur le projet dans l’Explorateur de solutions et de passer au **déploiement** > **Nouveau déploiement...** 
     ![Nouveau déploiement](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Vous êtes invité à vous connecter à votre abonnement Azure. 

3. Vous devez maintenant choisir les détails du groupe de ressources que vous souhaitez déployer l’application logique. 
    ![Déployer vers le groupe de ressources](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

     > [AZURE.NOTE]    Veillez à sélectionner les fichiers de modèle et les paramètres corrects pour le groupe de ressources (par exemple si vous déployez dans un environnement de production vous voudrez choisir le fichier de paramètres production). 
4. Sélectionnez le bouton déployer
 
    
6. L’état du déploiement s’affiche dans la fenêtre de **sortie** (il se pouvez que vous deviez choisir **Azure mise en service**. 
    ![Sortie](./media/app-service-logic-deploy-from-vs/output.png)

À l’avenir, vous pouvez modifier votre application de logique de contrôle de code source et utiliser Visual Studio pour déployer de nouvelles versions. 

> [AZURE.NOTE] Si vous modifiez la définition dans le portail Azure directement, la prochaine fois que vous déployez ces modifications à partir de Visual Studio est remplacée.

## <a name="next-steps"></a>Étapes suivantes

- Pour vous familiariser avec les applications de logique, suivez le didacticiel de [créer une application de logique](app-service-logic-create-a-logic-app.md) .  
- [Afficher des exemples et des scénarios courants](app-service-logic-examples-and-scenarios.md)
- [Vous pouvez automatiser des processus d’entreprise avec les applications de logique](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Apprenez à intégrer vos systèmes avec les applications de logique](http://channel9.msdn.com/Events/Build/2016/P462)