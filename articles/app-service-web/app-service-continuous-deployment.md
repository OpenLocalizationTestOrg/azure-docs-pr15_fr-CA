<properties
    pageTitle="Déploiement continue à un Service d’application Azure | Microsoft Azure"
    description="Apprenez à activer le déploiement continue à un Service d’application Azure."
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
    ms.date="10/28/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="continuous-deployment-to-azure-app-service"></a>Déploiement continue à un Service d’application Azure

Ce didacticiel vous montre comment configurer un flux de travail continue de déploiement pour votre application de [Service d’application Azure] . Intégration du Service d’application avec Visual Studio Team Services (VSTS), GitHub et BitBucket permet à un workflow de déploiement continu où Azure extrait les dernières mises à jour à partir de votre projet publié à l’un de ces services. DEPLOIEMENT continu est une option intéressante pour les projets où plusieurs et fréquentes contributions sont intégrées.

## <a name="overview"></a>Activer le déploiement en continu

Pour activer le déploiement en continu, 

1. Publier le contenu de votre application dans le référentiel qui sera utilisé pour le déploiement en continu.  
    Pour plus d’informations sur la publication de votre projet à ces services, consultez [créer un mis en pension (GitHub)], [créer un mis en pension (BitBucket)]et [mise en route de VSTS].

2. Dans une carte de menu de votre application dans [Azure portal], cliquez sur **APP DEPLOYMENT > options de déploiement**. Cliquez sur **Choisir la Source**, puis sélectionnez la source du déploiement.  

    ![](./media/app-service-continuous-deployment/cd_options.png)
    
    > [AZURE.NOTE] Pour configurer un VSTS compte pour le déploiement du Service d’application, consultez de ce [didacticiel](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
    
3. Terminer le processus d’autorisation. 

4. Dans la lame **source de déploiement** , cliquez sur le projet et les succursales pour déployer à partir. Lorsque vous avez terminé, cliquez sur **OK**.
  
    ![](./media/app-service-continuous-deployment/github_option.png)

    > [AZURE.NOTE] Lorsque vous activez un déploiement continu avec GitHub ou BitBucket, projets publics et privés seront affiche.

    Service d’application crée une association avec le référentiel sélectionné, extrait les fichiers à partir de la branche spécifiée et conserve un clone de votre référentiel pour votre application de Service de l’application. Lorsque vous configurez le déploiement continu de VSTS à partir du portail Azure, l’intégration utilise le Service d’application [moteur de déploiement de Kudu](https://github.com/projectkudu/kudu/wiki), qui automatise les tâches de génération et de déploiement avec déjà toutes `git push`. Vous n’avez pas besoin de définir séparément le déploiement continue dans VSTS. Une fois ce processus terminé, la lame d’application **des options de déploiement** affiche un déploiement actif qui indique le déploiement a réussi.

5. Pour vérifier que l’application est déployée avec succès, cliquez sur l' **URL** en haut de la lame de l’application dans Azure portal. 

6. Pour vérifier que le déploiement en continu se produit à partir du référentiel de votre choix, appuyez sur une modification dans le référentiel. Votre application doit mettre à jour pour refléter les modifications peu de temps après la fin de la campagne dans le référentiel. Vous pouvez vérifier qu’il a tiré la mise à jour de la lame **d’options de déploiement** de votre application.

## <a name="VSsolution"></a>Déploiement en continu d’une solution Visual Studio 

En exécutant un push d’une solution Visual Studio Azure application service est aussi simple que de transmettre un fichier index.html simple. Le processus de déploiement du Service de l’application simplifie tous les détails, y compris la restauration des dépendances de NuGet et générer les fichiers binaires d’application. Vous pouvez suivre les source contrôle les meilleures pratiques de gestion du code uniquement dans votre référentiel Git et déploiement de services d’application vous permettent de prendre en charge le reste.

Les étapes pour pousser votre solution Visual Studio au Service de l’application sont les mêmes que dans la [section précédente](#overview), autant que vous configurez votre solution et le référentiel comme suit :

-   Utilisez l’option de contrôle de code source de Visual Studio pour générer un `.gitignore` de fichiers telles que l’image ci-dessous ou manuellement ajouter un `.gitignore` fichier dans la racine de votre référentiel avec du contenu similaire à cet [exemple de .gitignore](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore). 

    ![](./media/app-service-continuous-deployment/VS_source_control.png)
 
-   Ajouter arborescence du répertoire de la solution complète pour votre référentiel, avec le fichier .sln dans la racine du référentiel.

Une fois que vous avez configuré votre référentiel comme décrit et configuré votre application dans Azure pour la publication en continu à partir d’un des référentiels Git en ligne, vous pouvez développer votre application ASP.NET localement dans Visual Studio et déployer en continu de votre code en poussant vos modifications dans votre référentiel Git en ligne.

## <a name="disableCD"></a>Désactiver le déploiement en continu

Pour désactiver le déploiement en continu, 

1. Dans une carte de menu de votre application dans [Azure portal], cliquez sur **APP DEPLOYMENT > options de déploiement**. Puis cliquez sur **Déconnecter** dans la blade **d’options de déploiement** .

    ![](./media/app-service-continuous-deployment/cd_disconnect.png)    

2. Après avoir répondu **Oui** au message de confirmation, vous pouvez revenir à la lame de votre application et cliquez sur **APP DEPLOYMENT > options de déploiement** si vous souhaitez configurer la publication à partir d’une autre source.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Comment faire pour étudier des problèmes courants liés au déploiement en continu](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [L’utilisation de PowerShell pour Azure]
* [Comment utiliser les outils de ligne de commande de Azure pour Mac et Linux]
* [Documentation de GIT]
* [Kudu de projet](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

[Service d’application Azure]: https://azure.microsoft.com/en-us/documentation/articles/app-service-changes-existing-services/ 
[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[L’utilisation de PowerShell pour Azure]: ../articles/powershell-install-configure.md
[Comment utiliser les outils de ligne de commande de Azure pour Mac et Linux]: ../articles/xplat-cli-install.md
[Documentation de GIT]: http://git-scm.com/documentation

[Créer un mis en pension (GitHub)]: https://help.github.com/articles/create-a-repo
[Créer un mis en pension (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Mise en route de VSTS]: https://www.visualstudio.com/get-started/overview-of-get-started-tasks-vs
[Continuous delivery to Azure using Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md
