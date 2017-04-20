<properties
    pageTitle="Créer une plate-forme de Windows universel (UWP) qui utilise des applications Mobile | Microsoft Azure"
    description="Suivez ce didacticiel pour se familiariser avec les serveurs principaux de l’application mobile Azure pour le développement d’applications de plate-forme de Windows universel (UWP) dans C#, Visual Basic ou JavaScript."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-windows-app"></a>Créer une application Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment ajouter un service en nuage de back-end pour une application de plate-forme de Windows universel (UWP). Pour plus d’informations, voir [Quelles sont les applications mobiles](app-service-mobile-value-prop.md). Captures d’écran à partir de l’application terminée sont les suivantes :

![Application de bureau terminée](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
En cours d’exécution sur un ordinateur de bureau. 

![Application de téléphone terminée](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
En cours d’exécution sur un téléphone

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels application Mobile pour les applications de la série UWP. 

##<a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez les éléments suivants :

* Un compte Azure actif. Si vous n’avez pas un compte, vous pouvez vous inscrire pour un essai Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pouvez continuer à utiliser même après la fin de votre version d’évaluation. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/).

* [2015 de communauté de Visual Studio] ou une version ultérieure.

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant de vous inscrivez pour un compte Azure, accédez à [Essayer le Service application](https://tryappservice.azure.com/?appServiceName=mobile). Vous pouvez créer une application mobile de courte durée starter immédiatement dans le Service d’application : aucune carte de crédit requise et aucun des engagements.

##<a name="create-a-new-azure-mobile-app-backend"></a>Créer un nouveau backend Azure Mobile App

Suivez ces étapes pour créer un nouveau contexte d’application Mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant déployé un back-end Azure Mobile application qui peut être utilisé par les applications clientes mobiles. Ensuite, vous allez télécharger un projet de serveur pour une simple « liste todo » back-end et le publier dans Azure.

## <a name="configure-the-server-project"></a>Configurez le projet serveur

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-client-project"></a>Téléchargez et exécutez le projet client

Une fois que vous avez configuré votre back-end de l’application Mobile, vous pouvez créer une nouvelle application client ou modifier une application existante pour vous connecter à Azure. Dans cette section, vous téléchargez un projet de modèle d’application UWP qui est personnalisé pour vous connecter à votre back-end de l’application Mobile.

1. De retour dans la lame de **démarrage rapide** de votre back-end de l’application Mobile, cliquez sur **créer une nouvelle application** > **Télécharger**, puis extrait des fichiers de projet compressé sur votre ordinateur local.

    ![Télécharger le projet de démarrage rapide de Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

3. (Facultatif) Ajouter le projet d’application UWP à la même solution que le projet serveur. Cela rend plus facile à déboguer et tester l’application et le serveur principal dans la même solution Visual Studio, si vous choisissez de le faire. Pour ajouter un projet d’application UWP à la solution, vous devez utiliser Visual Studio 2015 ou une version ultérieure.

4. Avec l’application UWP comme projet de démarrage, appuyez sur la touche F5 pour déployer et exécuter l’application.

5. Dans l’application, tapez un texte explicite comme *terminé le didacticiel*, dans la zone de texte **Insérer un TodoItem** , puis cliquez sur **Enregistrer**.

    ![Bureau complet de démarrage rapide de Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Envoie une demande POST sur le nouveau serveur principal application mobile qui est hébergé dans Azure.

6. (Facultatif) Arrêter l’application et redémarrer sur un autre périphérique ou un émulateur mobile.

    ![Téléphone complet de démarrage rapide de Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

    Notez que les données enregistrées à partir de l’étape précédente sont chargées à partir d’Azure après le démarrage de l’application UWP. 

##<a name="next-steps"></a>Étapes suivantes

* [Ajouter l’authentification à votre application](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Apprenez à authentifier les utilisateurs de votre application avec un fournisseur d’identité.

* [Ajouter des notifications de type pousser à votre application](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Découvrez comment ajouter push notifications prennent en charge à votre application et configurez votre back-end de l’application Mobile pour utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type Pousser.

* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Découvrez comment ajouter la prise en charge en mode hors connexion de votre application à l’aide d’un back-end de l’application Mobile. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;affichage, ajout ou modification de données&mdash;même lorsqu’il n’y a pas de connexion réseau.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Communauté 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203
