<properties
    pageTitle="Développement Agile avec le Service d’application Azure"
    description="Apprenez à créer des applications complexes de grande échelle avec le Service d’application Azure d’une manière qui prend en charge le développement agile."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>


# <a name="agile-software-development-with-azure-app-service"></a>Développement Agile avec le Service d’application Azure #

Dans ce didacticiel, vous allez apprendre à créer des applications complexes de grande échelle avec le [Service d’application Azure](/services/app-service/) d’une manière qui prend en charge le [développement Agile](https://en.wikipedia.org/wiki/Agile_software_development). Il suppose que vous savez déjà comment [déployer des applications complexes prévisible dans Azure](app-service-deploy-complex-application-predictably.md).

Limitations de procédés techniques peuvent souvent faire obstacle à la mise en œuvre réussie des méthodologies agiles. Service d’application Azure avec des fonctionnalités telles que la [publication continue](app-service-continuous-deployment.md), [mise en environnements](web-sites-staged-publishing.md) (emplacements) et la [surveillance](web-sites-monitor.md), associée avec soin à l’orchestration et la gestion du déploiement dans le [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md), peut être partie d’une solution idéale pour les développeurs qui adopter le développement logiciel agile.

Le tableau suivant est une courte liste des exigences associées de développement agile, et comment les services Azure permet d'entre eux.

| Exigence | Comment Azure permet |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -Concevoir avec chaque validation.<br>-Générez automatiquement et rapidement | Lorsqu’il est configuré avec un déploiement continu, Service d’application Azure peut fonctionner comme versions live-exécution basées sur une branche dev. Chaque fois que code est poussée vers la succursale, il est automatiquement généré et en cours d’exécution live dans Azure.|
| -Vérifiez les builds autodiagnostics | Charger les tests, les tests web, etc., peuvent être déployés avec le modèle de gestionnaire de ressources Azure.|
| -Effectuez des tests dans un clone de l’environnement de production | Les modèles de gestionnaire de ressources Azure peuvent être utilisés pour créer des clones de l’environnement de production Azure (y compris les paramètres de l’application, de modèles de chaîne de connexion, de mise à l’échelle, etc.) pour les tests rapidement et de manière prévisible.|
| -Permet d’afficher les résultats de la dernière version facilement | Le déploiement en continu vers Azure à partir d’un référentiel signifie que vous pouvez tester de nouveau code dans une application active immédiatement une fois que vous validez vos modifications. |
| -Valider dans la branche principale chaque jour<br>-Automatisez le déploiement | Intégration continue d’une application de production avec une branche du principal déploie automatiquement chaque commit et de fusion dans la branche principale à la production. |

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>Ce que vous ferez ##

Vous guidera dans un flux de travail de développement, test et stade-production typique pour publier de nouvelles modifications à l’application d’exemple de [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) , qui se compose de deux [applications web](/services/app-service/web/), un étant un site Web frontal (FE) et l’autre étant un back-end Web API (BE) et une [base de données SQL](/services/sql-database/). Vous allez travailler avec l’architecture de déploiement ci-dessous :

![](./media/app-service-agile-software-development/what-1-architecture.png)

Pour mettre l’image en mots :

-   L’architecture de déploiement est séparé en trois différents environnements (ou [groupes de ressources](../azure-resource-manager/resource-group-overview.md) dans Azure), chacune avec son propre [plan de Service d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), les paramètres de [mise à l’échelle](web-sites-scale.md) et de la base de données SQL. 
-   Chaque environnement peut être gérée séparément. Il peuvent exister même dans les différentes souscriptions.
-   Intermédiaire et production sont implémentées en tant que deux emplacements de l’application de Service de l’application même. La branche principale est le programme d’installation pour l’intégration continue avec l’emplacement intermédiaire.
-   Lorsqu’une instruction commit pour la branche principale est vérifiée sur l’emplacement intermédiaire (avec des données de production), l’application intermédiaire vérifiée est échangée dans la production emplacement [sans interruption de service](web-sites-staged-publishing.md).

L’environnement de production et de mise en attente est défini par le modèle à [ * &lt;repository_root >*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json).

Les environnements de développement et de test sont définis par le modèle à [ * &lt;repository_root >*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json).

Vous utiliserez également la stratégie de création de branche typique, avec code de déplacement de la branche dev à la branche de test, puis vers la branche principale (déplacement vers le haut de qualité, pour ainsi dire).

![](./media/app-service-agile-software-development/what-2-branches.png) 

## <a name="what-you-will-need"></a>Ce que vous devez ##

-   Un compte Azure
-   Un compte [GitHub](https://github.com/)
-   GIT Shell (installé avec [GitHub pour Windows](https://windows.github.com/)) - Cela vous permet d’exécuter des commandes à la fois le Git et PowerShell dans la même session 
-   Derniers bits de [PowerShell d’Azure](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi)
-   Connaissances de base des éléments suivants :
    -   Déploiement de modèle de [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md) (voir aussi [déploiement d’une application complexe prévisible dans Azure](app-service-deploy-complex-application-predictably.md))
    -   [GIT](http://git-scm.com/documentation)
    -   [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Vous avez besoin d’un compte Azure pour compléter ce didacticiel :
> + Vous pouvez [Ouvrir un compte Azure gratuitement](/pricing/free-trial/) , vous obtenez des crédits que vous pouvez utiliser pour tester des services Azure payés et même après leur utilisation vous pouvez conserver le compte et utilisation libre des services Azure, tels que les applications Web.
> + Vous pouvez [activer des avantages pour les abonnés de Visual Studio](/pricing/member-offers/msdn-benefits-details/) , de Visual Studio votre abonnement fournit les crédits que vous pouvez utiliser pour des services Azure payés chaque mois.
>
> Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="set-up-your-production-environment"></a>Configurer votre environnement de production ##

>[AZURE.NOTE] Le script utilisé dans ce didacticiel pour configurer automatiquement la publication continue à partir de votre référentiel de GitHub. Cette opération nécessite que vos informations d’identification de GitHub sont déjà stockées dans Azure, dans le cas contraire, les scripts de déploiement échoue lorsque vous essayez de configurer les paramètres de contrôle de code source pour les applications web. 
>
>Pour stocker vos informations d’identification GitHub dans Azure, créez une application web dans le [Portail Azure](https://portal.azure.com/) et [configurer le déploiement de GitHub](app-service-continuous-deployment.md). Vous devez uniquement effectuer cette opération qu’une seule fois. 

Dans un scénario classique de DevOps, vous avez une application qui s’exécute dans Azure et vous souhaitez apporter des modifications à la publication continue. Dans ce scénario, vous avez un modèle de développé, testé et utilisé pour déployer l’environnement de production. Vous allez configurer il dans cette section.

1.  Créer votre propre branche du référentiel [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) . Pour plus d’informations sur la création de votre branche, consultez [branche un mis en pension](https://help.github.com/articles/fork-a-repo/). Une fois votre branche créée, vous pouvez le voir dans votre navigateur.
 
    ![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.  Ouvrez une session Git Shell. Si vous n’avez pas encore Git Shell, installer [GitHub pour Windows](https://windows.github.com/) maintenant.

3.  Créer un clone local de votre branche en exécutant la commande suivante :

        git clone https://github.com/<your_fork>/ToDoApp.git 

4.  Une fois que vous avez votre clone local, accédez à * &lt;repository_root >*\ARMTemplates et exécuter la deploy.ps1 de script comme suit :

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git

4.  Lorsque vous y êtes invité, tapez dans le nom d’utilisateur souhaité et le mot de passe pour l’accès de la base de données.

    Vous devriez voir la progression de mise en service de diverses ressources Azure. Lorsque le déploiement est terminé, le script lance l’application dans le navigateur et vous donner un bip convivial.

    ![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
 
    >[AZURE.TIP] Jetez un coup * &lt;repository_root >*\ARMTemplates\Deploy.ps1, pour voir comment il génère les ressources avec des ID uniques. Vous pouvez utiliser la même approche pour créer des clones du même déploiement sans vous soucier des conflits de noms de ressources.
 
6.  Retourner dans votre session Git Shell, exécutez :

        .\swap –Name ToDoApp<unique_string>master

    ![](./media/app-service-agile-software-development/production-4-swap.png)

7.  Lorsque la fin du script, revenez en arrière pour naviguer jusqu'à l’adresse de la partie frontale (http://ToDoApp*&lt;unique_string >*master.azurewebsites.net/) pour voir l’application en cours d’exécution dans la production.
 
5.  Connectez-vous au [Portail Azure](https://portal.azure.com/) et observez ce qui est créé.

    Vous devez être en mesure de voir les deux applications web dans le même groupe de ressources, un avec le `Api` le suffixe dans le nom. Si vous examinez l’affichage des ressources de groupe, vous verrez également la base de données SQL server le plan de Service de l’application et que, les emplacements de zone de transit pour les applications web. Naviguer parmi les différentes ressources et de les comparer avec * &lt;repository_root >*\ARMTemplates\ProdAndStage.json pour voir comment ils sont configurés dans le modèle.

    ![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

Vous avez maintenant configurer l’environnement de production. Ensuite, vous va lancer une nouvelle mise à jour pour l’application.

## <a name="create-dev-and-test-branches"></a>Création de développement et test des branches ##

Maintenant que vous avez une application complexe en production dans Azure, vous effectuerez une mise à jour de votre application selon une méthodologie agile. Dans cette section, vous créerez le développement et test des branches dont vous aurez besoin pour effectuer les mises à jour requises.

1.  Créez d’abord l’environnement de test. Dans votre session Git Shell, exécutez les commandes suivantes pour créer l’environnement pour une nouvelle branche appelée **NewUpdate**. 

        git checkout -b NewUpdate
        git push origin NewUpdate 
        .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch NewUpdate

1.  Lorsque vous y êtes invité, tapez dans le nom d’utilisateur souhaité et le mot de passe pour l’accès de la base de données. 

    Lorsque le déploiement est terminé, le script lance l’application dans le navigateur et vous donner un bip convivial. Et comme cela, vous disposez maintenant d’une nouvelle branche avec son propre environnement de test. Prenez un instant pour examiner d’un peu plus sur cet environnement de test :

    -   Vous pouvez le créer dans n’importe quel abonnement Azure. Cela signifie que l’environnement de production peut être gérée séparément à partir de votre environnement de test.
    -   Votre environnement de test s’exécute dans Azure.
    -   Votre environnement de test est identique à l’environnement de production, sauf pour les emplacements de zone de transit et les paramètres de mise à l’échelle. Vous pouvez le savoir parce que ce sont les seules différences entre ProdandStage.json et Dev.json.
    -   Vous pouvez gérer votre environnement de test dans son propre plan de Service d’application, avec un niveau de prix différent (par exemple, **libre**).
    -   La suppression de cet environnement de test peut être aussi simple que de supprimer le groupe de ressources. Vous allez découvrir comment cette [suite](#delete).

2.  Passez à Création d’une branche dev en exécutant les commandes suivantes :

        git checkout -b Dev
        git push origin Dev
        .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch Dev

3.  Lorsque vous y êtes invité, tapez dans le nom d’utilisateur souhaité et le mot de passe pour l’accès de la base de données. 

    Prenez un instant pour examiner d’un peu plus sur cet environnement de développement : 

    -   Votre environnement de développement a une configuration identique à l’environnement de test, car il est déployé en utilisant le même modèle.
    -   Chaque environnement de développement peut être créé dans l’abonnement Azure du développeur, en laissant l’environnement de test pour être gérés séparément.
    -   Votre environnement de développement est en cours d’exécution dans Azure.
    -   La suppression de l’environnement de développement est aussi simple que de supprimer le groupe de ressources. Vous allez découvrir comment cette [suite](#delete).

>[AZURE.NOTE] Lorsque vous avez plusieurs développeurs travaillent sur la nouvelle mise à jour, chacun d’eux peut facilement créer une branche et l’environnement de développement dédiée en procédant comme suit :
>
>1. Créer leur propre branche du référentiel dans GitHub (voir la [branche un mis en pension](https://help.github.com/articles/fork-a-repo/)).
>2. Cloner la branche sur leur ordinateur local
>3. Exécuter les mêmes commandes pour créer leur propre environnement et la branche dev.

Lorsque vous avez terminé, votre GitHub de fourche doit avoir trois branches :

![](./media/app-service-agile-software-development/test-1-github-view.png)

Et vous devez avoir six web applications (trois ensembles de deux) dans les trois groupes de ressources distincts :

![](./media/app-service-agile-software-development/test-2-all-webapps.png)
 
>[AZURE.NOTE] Notez que ProdandStage.json Spécifie l’environnement de production pour utiliser le **Standard** tarification couche, qui est appropriée pour l’évolutivité de l’application de production.

## <a name="build-and-test-every-commit"></a>Générer et tester chaque commit ##

Les fichiers ProdAndStage.json et Dev.json du modèle précisent déjà les paramètres de contrôle de code source, qui par défaut configure continue de publication de l’application web. Par conséquent, chaque commit à la branche GitHub déclenche le déploiement automatique vers Azure de cette branche. Nous allons voir comment votre programme d’installation fonctionne maintenant.

1.  Assurez-vous que vous êtes dans la branche Dev du référentiel local. Pour ce faire, exécutez la commande suivante dans le Git Shell :

        git checkout Dev

2.  Apporter une modification simple à la couche d’interface utilisateur de l’application en modifiant le code pour utiliser des listes de [données d’amorçage](http://getbootstrap.com/components/) . Ouvrir * &lt;repository_root >*\src\MultiChannelToDo.Web\index.cshtml et que les modifications de mise en surbrillance ci-dessous :

    ![](./media/app-service-agile-software-development/commit-1-changes.png)

    >[AZURE.NOTE] Si vous ne pouvez pas lire l’image ci-dessus : 
    >
    >- Dans la ligne 18, modifiez `check-list` à `list-group`.
    >- Dans la ligne 19, modifiez `class="check-list-item"` à `class="list-group-item"`.

3.  Enregistrer les modifications. Revenir dans le Git Shell, exécutez les commandes suivantes :

        cd <repository_root>
        git add .
        git commit -m "changed to bootstrap style"
        git push origin Dev
 
    Ces commandes git sont similaires à « la vérification de votre code » dans un autre système de contrôle de code source comme TFS. Lorsque vous exécutez `git push`, la nouvelle validation déclenche une transmission automatique du code sur Azure, qui reconstruit ensuite l’application pour refléter la modification dans l’environnement de développement.

4.  Pour vérifier que cette émission de code dans votre environnement de développement s’est produite, accédez à lame d’application de votre environnement de développement web et consulter la partie du **déploiement** . Vous devez être en mesure de voir votre dernier message de validation il.

    ![](./media/app-service-agile-software-development/commit-2-deployed.png)

5.  À partir de là, cliquez sur **Parcourir** pour voir la nouvelle modification dans l’application direct dans Azure.

    ![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)

    Il s’agit d’une modification relativement mineure pour l’application. Toutefois, plusieurs fois les nouvelles modifications apportées à une application web complexe a des effets secondaires inattendus et indésirables. Pouvoir facilement tester chaque commit dans les générations live vous permet d’intercepter ces problèmes avant de les voient par vos clients.

À présent, vous devez être à l’aise avec la réalisation, en tant que développeur sur le projet **NewUpdate** , vous pourrez facilement créer un environnement de développement pour vous-même, puis générer chaque commit et tester chaque génération.

## <a name="merge-code-into-test-environment"></a>Fusionner le code dans l’environnement de test ##

Lorsque vous êtes prêt à envoyer votre code à partir de la branche Dev à la branche de NewUpdate, il est le processus git standard :

1.  Fusionner les nouvelles validations à NewUpdate dans la branche Dev dans GitHub, comme les validations créés par d’autres développeurs. Toute nouvelle validation sur GitHub va déclencher une diffusion de code et générer dans l’environnement de développement. Vous pouvez vous assurer ensuite que votre code dans la branche Dev fonctionne toujours dont les bits les plus récents à partir de la branche de NewUpdate.

2.  Fusionner tous vos validations de nouveau à partir de la branche Dev dans une branche de NewUpdate sur GitHub. Cette action déclenche un push de code et de la génération dans l’environnement de test. 

Notez à nouveau que parce que le déploiement en continu est déjà configuré avec ces branches git, vous n’avez pas besoin de prendre d’autres actions telles que l’intégration en cours d’exécution génère. Vous devez simplement effectuer des pratiques de contrôle source standard à l’aide de git et Azure effectue tous les processus de génération pour vous.

Maintenant, nous allons pousser votre code à la branche de **NewUpdate** . Dans le Git Shell, exécutez les commandes suivantes :

    git checkout NewUpdate
    git pull origin NewUpdate
    git merge Dev
    git push origin NewUpdate

Voilà ! 

Accédez à la lame d’application web pour votre environnement de test voir votre nouveau commit (fusionner dans la branche de NewUpdate), désormais envoyée vers l’environnement de test. Puis, cliquez sur **Parcourir** pour voir que la modification du style s’exécute dans Azure.

## <a name="deploy-update-to-production"></a>Déploiement de mise à jour pour la production ##

En exécutant un push de code dans l’environnement de test et de production doit se sentent pas différent de ce que vous avez déjà fait lorsque vous vous appuyez sur le code dans l’environnement de test. Il est très simple. 

Dans le Git Shell, exécutez les commandes suivantes :

    git checkout master
    git pull origin master
    git merge NewUpdate
    git push origin master

N’oubliez pas qu’en fonction de la façon dont l’environnement de test et de production est le programme d’installation dans ProdandStage.json, le nouveau code est basculé vers l’emplacement de **la zone de transit** et il exécute. Ainsi, si vous accédez à l’URL de l’emplacement mise en attente, vous verrez le nouveau code exécutant il. Pour ce faire, exécutez le `Show-AzureWebsite` cmdlet dans le Git Shell.

    Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging
 
Et maintenant, après que vous être assuré de la mise à jour dans l’emplacement intermédiaire, la seule chose qui reste à faire est de changer automatiquement en production. Dans le Git Shell, il suffit d’exécuter les commandes suivantes :

    cd <repository_root>\ARMTemplates
    .\swap.ps1 -Name ToDoApp<unique_string>master

Félicitations ! Vous avez publié une nouvelle mise à jour pour votre application web de production. Qui plus est que vous avez fait simplement en créant facilement des dev et environnements et de construire et de tester chaque validation de test. Il s’agit des blocs de construction essentiels pour agile software development.

<a name="delete"></a>
## <a name="delete-dev-and-test-enviroments"></a>Supprimer les dev et environnements de test ##

Car vous avez intentionnellement conçu vos environnements de test et développement et à des groupes de ressources autonome, il est très facile de les supprimer. Pour supprimer ceux que vous avez créé dans ce didacticiel, les branches de la GitHub et artefacts Azure, il suffit d’exécuter les commandes suivantes dans le Git Shell :

    git branch -d Dev
    git push origin :Dev
    git branch -d NewUpdate
    git push origin :NewUpdate
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## <a name="summary"></a>Résumé ##

Développement Agile est indispensable pour de nombreuses entreprises qui souhaitent adopter Azure comme leur plate-forme d’application. Dans ce didacticiel, vous avez appris à créer et à supprimer la réplique exacte ou proche de réplicas de l’environnement de production en toute simplicité, même pour des applications complexes. Vous avez également appris comment tirer parti de cette possibilité de créer un processus de développement qui peut générer et tester chaque validation unique dans Azure. Ce didacticiel a présenté espérons comment vous pouvez mieux utiliser Service d’application Azure et d’Azure Resource Manager ensemble pour créer une solution DevOps qui convient à des méthodologies agiles. Ensuite, vous pouvez créer sur ce scénario en effectuant des techniques DevOps avancées telles que [le test dans la production](app-service-web-test-in-production-get-start.md). Dans un scénario de test de production commun, consultez [déploiement de Flighting (test bêta) dans le Service d’application Azure](app-service-web-test-in-production-controlled-test-flight.md).

## <a name="more-resources"></a>Plus de ressources ##

-   [Déployer une application complexe prévisible dans Azure](app-service-deploy-complex-application-predictably.md)
-   [Développement Agile dans la pratique : trucs et astuces pour le Cycle de développement modernes](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)
-   [Stratégies de déploiement avancé pour Azure Web Apps à l’aide des modèles du Gestionnaire de ressources](http://channel9.msdn.com/Events/Build/2015/2-620)
-   [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md)
-   [JSONLint - le validateur JSON](http://jsonlint.com/)
-   [ARMClient – paramétrage de publication GitHub au site](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
-   [Branchement de GIT – base de branchement et de fusion](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-   [Blog de David Ebbo](http://blog.davidebbo.com/)
-   [PowerShell Azure](../powershell-install-configure.md)
-   [Outils de ligne de commande multiplateforme Azure](../xplat-cli-install.md)
-   [Créer ou modifier des utilisateurs dans Active Directory Azure](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
-   [Wiki de Kudu de projet](https://github.com/projectkudu/kudu/wiki)
