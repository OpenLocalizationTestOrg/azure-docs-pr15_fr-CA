<properties
    pageTitle="Contenu de la synchronisation à partir d’un dossier de nuage au Service d’application Azure"
    description="Apprenez à déployer votre application, au Service d’application Azure, par l’intermédiaire de synchronisation de contenu à partir d’un dossier de nuage."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Contenu de la synchronisation à partir d’un dossier de nuage au Service d’application Azure

Ce didacticiel vous montre comment déployer au [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714) en synchronisant votre contenu à partir des services de stockage cloud populaires tels que boîte de dépôt et de OneDrive. 

## <a name="overview"></a>Vue d’ensemble du déploiement de la synchronisation de contenu

Le déploiement de la synchronisation de contenu à la demande est alimenté par le [moteur de déploiement Kudu](https://github.com/projectkudu/kudu/wiki) intégré avec le Service d’application. Dans le [Portail Azure](https://portal.azure.com), vous pouvez désigner un dossier dans votre stockage en nuage, travailler avec votre code d’application et le contenu de ce dossier et au Service de l’application de synchronisation en cliquant sur un bouton. Synchronisation de contenu utilise le processus de Kudu de génération et de déploiement. 
    
## <a name="contentsync"></a>Comment faire pour activer le déploiement de la synchronisation de contenu
Pour activer la synchronisation de contenu à partir du [Portail Azure](https://portal.azure.com), procédez comme suit :

1. Dans les lames de votre application dans Azure Portal, cliquez sur **paramètres** > **Source du déploiement**. **Choisir une Source**, puis sur **OneDrive** ou **échange** comme source pour le déploiement. 

    ![Synchronisation de contenu](./media/app-service-deploy-content-sync/deployment_source.png)

    >[AZURE.NOTE] En raison des différences sous-jacentes qui existent dans les API, **OneDrive pour les entreprises** n’est pas pris en charge pour l’instant. 

2. Terminer le flux de travail de l’autorisation pour activer le Service d’application pour accéder à un chemin désigné prédéfini spécifique de OneDrive ou d’échange où tout votre contenu de Service d’application sera stocké.  
    Après l’application de Service d’autorisation plate-forme vous donnera l’option pour créer un dossier de contenu sous le chemin d’accès de contenu désigné ou pour choisir un dossier de contenu existant sous ce chemin d’accès de contenu désigné. Les chemins d’accès de contenu désignés sous vos comptes de stockage cloud utilisés pour la synchronisation de Service de l’application sont les suivantes :  
    * **OneDrive**:`Apps\Azure Web Apps` 
    * **Boîte de dépôt**:`Dropbox\Apps\Azure`

3. Après la synchronisation initiale du contenu, la synchronisation de contenu peut être initiée sur la demande à partir du portail Azure. L’historique de déploiement est disponible avec la lame de **déploiements** .

    ![Historique de déploiement](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
Plus d’informations pour le déploiement de la boîte de dépôt sont disponibles sous le [déploiement à partir de la boîte de dépôt](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). 


