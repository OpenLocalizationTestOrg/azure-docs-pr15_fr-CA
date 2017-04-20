<properties
    pageTitle="Configurer et déployer microservices prévisible dans Azure"
    description="Découvrez comment déployer une application composée de microservices dans le Service d’application Azure comme une seule unité et de manière prévisible à l’aide des modèles de groupe de ressources JSON et l’écriture de scripts PowerShell."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/06/2016"
    ms.author="cephalin"/>


# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Configurer et déployer microservices prévisible dans Azure #

Ce didacticiel explique comment configurer et déployer une application composée de [microservices](https://en.wikipedia.org/wiki/Microservices) dans le [Service d’application Azure](/services/app-service/) comme une seule unité et de manière prévisible à l’aide des modèles de groupe de ressources JSON et l’écriture de scripts PowerShell. 

Lors de la mise en service et de déployer des applications de grande échelle qui sont composées de hautement découplée microservices, répétabilité et la prévisibilité sont essentielles à la réussite. [Azure Application Service](/services/app-service/) vous permet de créer des microservices qui incluent des applications web, les applications mobiles, applications d’API et applications de logique. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vous permet de gérer tous les microservices en tant qu’unité, ainsi que les dépendances de ressource comme base de données et les paramètres de contrôle de la source. Maintenant, vous pouvez également déployer une telle application à l’aide de modèles JSON et simple de script PowerShell. 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-do"></a>Ce que vous ferez ##

Dans le didacticiel, vous allez déployer une application qui inclut :

-   Deux web applications (c'est-à-dire deux microservices)
-   Un serveur principal de base de données SQL
-   Paramètres de l’application, de chaînes de connexion et de contrôle de code source
-   Aperçu de l’application, les alertes, les paramètres d’autoscaling

## <a name="tools-you-will-use"></a>Vous utiliserez des outils ##

Dans ce didacticiel, vous utiliserez les outils suivants. Dans la mesure où il n’est pas une discussion complète sur les outils, je vais vous respectez le scénario de bout en bout et simplement donne une brève présentation à chacun, et où vous trouverez plus d’informations sur celui-ci. 

### <a name="azure-resource-manager-templates-json"></a>Modèles de gestionnaire de ressources Azure (JSON) ###
 
Chaque fois que vous créez une application web dans le Service d’application Azure, par exemple, le Gestionnaire de ressources Azure utilise un modèle JSON pour créer le groupe de ressource entier avec les ressources du composant. Un modèle complexe d' [Azure Marketplace](/marketplace) comme l’application [Évolutive WordPress](/marketplace/partners/wordpress/scalablewordpress/) peut inclure la base de données MySQL, comptes de stockage, le plan de Service de l’application, l’application web elle-même, les règles d’alerte, paramètres d’application, les paramètres d’échelle et plus et tous ces modèles sont disponibles via PowerShell. Pour plus d’informations sur la façon de télécharger et d’utiliser ces modèles, reportez-vous [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).

Pour plus d’informations sur les modèles du Gestionnaire de ressources Azure, consultez [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 pour Visual Studio ###

Le Kit de développement logiciel plus récent contient des améliorations pour la prise en charge du modèle de gestionnaire de ressources dans l’éditeur de JSON. Vous pouvez utiliser cela pour rapidement créer un modèle de groupe de ressources ou ouvrir un modèle existant de JSON (par exemple, un modèle de galerie téléchargé) pour la modification, remplir le fichier de paramètres et même déployer directement à partir d’une solution de groupe de ressources d’Azure, le groupe de ressources.

Pour plus d’informations, reportez-vous à la section [2.6 du SDK Azure pour Visual Studio](/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>PowerShell Azure 0.8.0 ou version ultérieure ###

À partir de la version 0.8.0, l’installation d’Azure PowerShell inclut le module Gestionnaire de ressources Azure en plus du module Azure. Ce nouveau module vous permet le déploiement des groupes de ressources de script.

Pour plus d’informations, reportez-vous [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Explorateur de ressources Azure ###

Cet [outil d’aperçu](https://resources.azure.com) vous permet de vous permet d’Explorer les définitions JSON de tous les groupes de ressources dans votre abonnement et les ressources individuelles. Dans l’outil, vous pouvez modifier les définitions de JSON d’une ressource, supprimer l’ensemble de la hiérarchie des ressources et créer de nouvelles ressources.  Les informations disponibles dans cet outil sont très utiles pour la création de modèles, car il vous affiche les propriétés que vous devez définir pour un type particulier de ressource, des valeurs correctes, etc.. Vous pouvez même créer votre groupe de ressources dans le [Portail Azure](https://portal.azure.com/), puis vérifiez que ses définitions de JSON dans l’outil de l’Explorateur pour vous aider à modéliser le groupe de ressources.

### <a name="deploy-to-azure-button"></a>Déployer sur bouton Azure ###

Si vous utilisez GitHub pour contrôle de code source, vous pouvez placer un [déploiement d’Azure bouton](/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) dans votre fichier Lisez-moi. MD, qui permet un déploiement de clé en main l’interface utilisateur pour Azure. Alors que vous pouvez le faire pour n’importe quelle application web simple, vous pouvez étendre cette option pour activer le déploiement d’un groupe de ressources tout en plaçant un fichier azuredeploy.json dans la racine du référentiel. Ce fichier JSON qui contient le modèle de groupe de ressources, servir à la déployer au bouton Azure pour créer le groupe de ressources. Pour obtenir un exemple, consultez l’exemple [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) , que vous utiliserez dans ce didacticiel.

## <a name="get-the-sample-resource-group-template"></a>Obtenir l’exemple de modèle de groupe de ressources ##

Maintenant, nous allons apprendre vers la droite pour elle.

1.  Accédez à l’exemple de Service de l’application [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) .

2.   Dans readme.md, cliquez sur **déployer pour Azure**.
 
3.  Vous êtes redirigé vers le site de [déploiement d’azure](https://deploy.azure.com) et vous êtes invité à entrer des paramètres de déploiement. Notez que la plupart des champs est remplie avec le nom du référentiel et des chaînes aléatoires pour vous. Vous pouvez modifier tous les champs si vous le souhaitez, mais les seules choses que vous devez entrer sont la connexion d’administration SQL Server et le mot de passe, puis cliquez sur **suivant**.
 
    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)

4.  Ensuite, cliquez sur **déployer** pour lancer le processus de déploiement. Une fois que le processus s’exécute jusqu'à la fin, cliquez sur le http://todoapp*XXXX*. lien azurewebsites.net pour parcourir l’application déployée. 

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)

    L’interface utilisateur doit être un peu lent lorsque vous accédez d’abord à celle-ci, car les applications démarrent uniquement, mais vous convaincre qu’il s’agit d’une application entièrement fonctionnelle.

5.  Dans la page déployer, cliquez sur le lien **Gérer** pour voir la nouvelle application dans Azure Portal.

6.  Dans la liste déroulante **Essentials** , cliquez sur le lien de groupe de ressources. Notez également que l’application web est déjà connectée au référentiel GitHub sous le **Projet externe**. 

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
 
7.  De la lame de groupe ressources, notez qu’il existe déjà deux web applications et une base de données de SQL dans le groupe de ressources.

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)
 
Tout ce que vous venez de voir dans quelques minutes est une application totalement déployé deux-microservice, avec tous les composants, dépendances, paramètres, base de données et la publication continue, définie par une orchestration automatisée dans le Gestionnaire de ressources Azure. Tout ceci a été effectuée par deux choses :

-   Le déploiement d’Azure bouton
-   azuredeploy.JSON dans la racine mis en pension

Vous pouvez déployer cette application même des dizaines, des centaines ou des milliers de fois et ont exactement la même configuration chaque fois. La répétabilité et la prévisibilité de cette approche vous permet de déployer des applications de grande échelle avec facilité et en toute confiance.

## <a name="examine-or-edit-azuredeployjson"></a>Examiner (ou modifier) AZUREDEPLOY. JSON ##

Maintenant nous allons voir comment a été configuré le référentiel de GitHub. Vous serez à l’aide de l’éditeur de JSON dans le Kit de développement .NET Azure, donc si vous n’avez pas déjà installé [Azure .NET SDK 2.6](/downloads/), faites-le maintenant.

1.  Cloner le référentiel de [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) à l’aide de votre outil de git favoris. Dans la capture d’écran ci-dessous, je fais cela dans le Team Explorer dans Visual Studio 2013.

    ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)

2.  À partir de la racine du référentiel, ouvrez azuredeploy.json dans Visual Studio. Si vous ne voyez pas le volet du plan de JSON, vous devez installer le Kit de développement .NET Azure.

    ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Je ne vais pas décrire tous les détails du format JSON, mais la section [Plus de ressources](#resources) comporte des liens pour l’apprentissage du langage de modèle de groupe de ressources. Ici, je vais juste pour vous montrer les fonctionnalités intéressantes qui peuvent vous aider à démarrer dans la création de votre propre modèle personnalisé pour le déploiement de l’application.

### <a name="parameters"></a>Paramètres ###

Examinez la section Paramètres pour vérifier que la plupart de ces paramètres est ce que le bouton **déployer vers Azure** vous invite à entrer. Le site derrière le bouton **déployer vers Azure** remplit l’entrée de l’interface utilisateur à l’aide des paramètres définis dans azuredeploy.json. Ces paramètres sont utilisés dans les définitions de ressource, telles que les noms des ressources, les valeurs de propriété, etc..

### <a name="resources"></a>Ressources ###

Dans le nœud de ressources, vous constatez que les ressources de niveau supérieur 4 sont définis, y compris les applications web deux, un plan de Service d’application et une instance de SQL Server. 

#### <a name="app-service-plan"></a>Plan de Service d’application ####

Commençons par une simple ressource de niveau racine dans le JSON. Dans le plan de JSON, cliquez sur le plan de Service d’application nommé **[hostingPlanName]** pour mettre en surbrillance le code JSON correspondant. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Notez que la `type` élément spécifie la chaîne pour un plan de Service d’application (il s’appelait une batterie de serveurs une fois long et long il y a) et d’autres éléments et propriétés sont renseignées à l’aide des paramètres définis dans le fichier JSON, et cette ressource ne dispose pas des ressources imbriquées.

>[AZURE.NOTE] Notez également que la valeur de `apiVersion` indique à Azure quelle version de l’API REST pour utiliser la définition de ressource JSON avec et il peut affecter comment la ressource doit être mis en forme à l’intérieur de la `{}`. 

#### <a name="sql-server"></a>SQL Server ####

Ensuite, cliquez sur la ressource SQL Server appelée **SQLServer** dans le plan de JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)
 
Notez les points suivants concernant le code en surbrillance de JSON :

-   L’utilisation de paramètres garantit que les ressources créées sont nommées et configurés de façon à rend cohérente avec l’autre.
-   La ressource de SQL Server dispose de deux ressources imbriquées, chacune a une valeur différente de `type`.
-   Les ressources imbriquées à l’intérieur de `“resources”: […]`, où la base de données et les règles de pare-feu sont définis, ont un `dependsOn` élément qui spécifie l’ID de ressource de la ressource SQL Server de niveau racine. Cette option indique à Azure le Gestionnaire de ressources, « avant de créer cette ressource, autres ressources doit déjà exister. et si cette autre ressource est définie dans le modèle, puis créer un tout d’abord ».

    >[AZURE.NOTE] Pour plus d’informations sur l’utilisation de la `resourceId()` , consultez [Fonctions de modèle de gestionnaire de ressources Azure](../resource-group-template-functions.md).

-   L’effet de la `dependsOn` élément est que Azure le Gestionnaire de ressources peut connaître les ressources qui peuvent être créés en parallèle et les ressources doivent être créés de façon séquentielle. 

#### <a name="web-app"></a>Application Web ####

À présent, passons maintenant aux applications web réelles eux-mêmes, qui sont plus compliquées. Cliquez sur l’application web de [variables('apiSiteName')] dans le plan de JSON en surbrillance son code JSON. Vous remarquerez que les choses sont obtenir beaucoup plus intéressantes. Pour cela, je vais parler les fonctionnalités un par un :

##### <a name="root-resource"></a>Ressource racine #####

L’application web dépend des deux ressources différentes. Cela signifie que le Gestionnaire de ressources Azure va créer l’application web uniquement après que le plan de Service de l’application et l’instance de SQL Server sont créées.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Paramètres de l’application #####

Les paramètres d’application sont également définis comme une ressource imbriquée.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

Dans le `properties` élément de `config/appsettings`, vous avez deux paramètres de l’application au format `“<name>” : “<value>”`.

-   `PROJECT`est un [paramètre de KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) qui indique le déploiement d’Azure le projet à utiliser dans une solution Visual Studio de projet multiples. Je vous montrerai plus tard comment le contrôle de code source est configuré, mais étant donné que le code de ToDoApp est dans une solution Visual Studio de plusieurs projet, nous avons besoin de ce paramètre.
-   `clientUrl`est simplement une application qui définissant le code d’application utilise.

##### <a name="connection-strings"></a>Chaînes de connexion #####

Les chaînes de connexion sont également définis comme une ressource imbriquée.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

Dans le `properties` élément de `config/connectionstrings`, chaque chaîne de connexion est également définie comme une paire nom : valeur, avec le format spécifique de `“<name>” : {“value”: “…”, “type”: “…”}`. Pour le `type` élément, les valeurs possibles sont `MySql`, `SQLServer`, `SQLAzure`, et `Custom`.

>[AZURE.TIP] Pour une liste définitive des types de chaîne de connexion, exécutez la commande suivante dans Azure PowerShell : \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
    
##### <a name="source-control"></a>Contrôle de code source #####

Les paramètres de contrôle de code source sont également définis comme une ressource imbriquée. Le Gestionnaire de ressources Azure utilise cette ressource pour configurer la publication continue (voir inconvénient sur `IsManualIntegration` ultérieurement) et également à lancer le déploiement de code de l’application automatiquement lors du traitement du fichier JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`et `branch` doivent être très intuitive et doit pointer vers le référentiel Git et le nom de la branche à publier à partir de. Encore une fois, celles-ci sont définies par les paramètres d’entrée. 

Remarque dans le `dependsOn` élément qui, en plus de la ressource de l’application web elle-même, `sourcecontrols/web` dépend également de `config/appsettings` et `config/connectionstrings`. C’est parce qu’une fois `sourcecontrols/web` est configuré, le processus de déploiement d’Azure tentera automatiquement de déployer, de build et démarrer le code d’application. Par conséquent, l’insertion de cette dépendance permet de s’assurer que l’application a accès aux paramètres d’application requis et les chaînes de connexion avant d’exécute le code d’application. 

>[AZURE.NOTE] Notez également que `IsManualIntegration` est défini sur `true`. Cette propriété est nécessaire dans ce didacticiel, car vous ne possédez pas réellement le référentiel GitHub et par conséquent ne peut pas réellement accorder l’autorisation à Azure pour configurer la publication continue à partir de [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (c'est-à-dire Pousser mises à jour du référentiel automatique vers Azure). Vous pouvez utiliser la valeur par défaut `false` pour le référentiel spécifié uniquement si vous avez configuré les informations d’identification de l’utilisateur GitHub dans le [portail Azure](https://portal.azure.com/) avant. En d’autres termes, si vous avez configuré le contrôle de code source à GitHub ou BitBucket pour n’importe quelle application dans [Azure Portal](https://portal.azure.com/) précédemment, à l’aide de vos informations d’identification de l’utilisateur, puis Azure sera mémoriser les informations d’identification et les utiliser à chaque fois que vous déployez une application à partir de GitHub ou BitBucket à l’avenir. Toutefois, si vous n’avez pas encore fait, déploiement du modèle JSON va échouer Azure Resource Manager tente de configurer les paramètres de contrôle de code source de l’application web, car il ne peut pas ouvrir une session avec des informations d’identification du propriétaire référentiel dans GitHub ou BitBucket.

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Comparer le modèle JSON avec le groupe de ressources déployées ##

Ici, vous pouvez passer au travers des pales de tout le web application dans [Azure Portal](https://portal.azure.com/), mais il existe un autre outil qui est tout aussi utile, if pas plus. Accédez à l’outil Aperçu de [Explorateur de ressources Azure](https://resources.azure.com) , qui vous offre une représentation JSON de tous les groupes de ressources dans vos abonnements, tels qu’ils existent réellement dans le système principal Azure. Vous pouvez également voir comment la ressource hiérarchie du groupe JSON dans Azure correspond à la hiérarchie dans le fichier de modèle qui est utilisé pour le créer.

Par exemple, lorsque j’accéder à l’outil [Explorateur de ressources Azure](https://resources.azure.com) et développez les nœuds dans l’Explorateur de solutions, je peux voir le groupe de ressources et les ressources de niveau racine sont collectés sous leurs types de ressources respectif.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Si vous accédez à une application web, vous devez être en mesure de voir les détails de configuration d’une application de web similaires à le ci-dessous la capture d’écran :

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Là encore, les ressources imbriquées doivent avoir une hiérarchie très similaire à celles de votre fichier de modèle JSON, et vous devez voir les paramètres de l’application, les chaînes de connexion, etc., apparaissent correctement dans le volet JSON. L’absence de paramètres ici a sans doute un problème avec votre fichier JSON et peut vous aider à résoudre les problèmes liés à votre fichier de modèle JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Déployer le modèle de groupe de ressources vous-même ##

Le bouton **déployer vers Azure** est très utile, mais elle vous permet de déployer le modèle de groupe de ressources dans azuredeploy.json uniquement si vous avez déjà poussé azuredeploy.json à GitHub. Le Kit de développement .NET Azure fournit également les outils vous permettant de déployer un fichier de modèle JSON directement à partir de votre ordinateur local. Pour ce faire, suivez les étapes ci-dessous :

1.  Dans Visual Studio, cliquez sur **le fichier** > **Nouveau** > **projet**.

2.  Cliquez sur **Visual C#** > **nuage** > **Groupe de ressources d’Azure**, puis cliquez sur **OK**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)

3.  Dans **Sélectionner un modèle Azure**, sélectionnez le **Modèle vide** , puis cliquez sur **OK**.

4.  Faites glisser azuredeploy.json dans le dossier de **modèle** de votre nouveau projet.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)

5.  À partir de l’Explorateur de solutions, ouvrez le azuredeploy.json copié.

6.  Uniquement pour la démonstration, ajoutons quelques ressources d’Application Insight standard à notre fichier JSON, en cliquant sur **Ajouter une ressource**. Si vous êtes simplement intéressé dans le déploiement du fichier JSON, passez aux étapes de déploiement.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)

7.  Sélectionnez les **Informations d’Application pour les applications Web**, puis vous assurer un Service d’application existant planifier et web app est activée, puis cliquez sur **Ajouter**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)

    Vous pourrez désormais voir de nouvelles ressources qui, en fonction de la ressource et ce qu’il fait, avoir des dépendances sur le plan de Service de l’application ou l’application web. Ces ressources ne sont pas activées par leur définition existante et que vous souhaitez modifier.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
 
8.  Dans le plan de JSON, cliquez sur **échelle de appInsights** pour mettre en surbrillance de son code JSON. C’est le paramètre de mise à l’échelle de votre plan de Service de l’application.

9.  Dans le code en surbrillance de JSON, recherchez la `location` et `enabled` propriétés et les définir comme indiqué ci-dessous.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)

10. Dans le plan de JSON, cliquez sur **CPUHigh appInsights** pour mettre en surbrillance de son code JSON. Il s’agit d’une alerte.

11. Recherchez la `location` et `isEnabled` propriétés et les définir comme indiqué ci-dessous. Procédez de même pour les autres trois alertes (bulbes violets).

    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)

12. Vous êtes maintenant prêt à déployer. Cliquez sur le projet et sélectionnez **déployer** > **Nouveau déploiement**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)

13. Si vous ne l’avez pas déjà fait, connectez-vous à votre compte Azure.

14. Sélectionnez un groupe de ressource existant dans votre abonnement ou créer un nouveau **azuredeploy.json**et sélectionnez puis cliquez sur **Modifier les paramètres**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)

    Vous allez maintenant être en mesure de modifier tous les paramètres définis dans le fichier de modèle dans une table complète. Les paramètres qui définissent les valeurs par défaut déjà auront leurs valeurs par défaut et les paramètres qui définissent une liste de valeurs autorisées seront afficheront comme des listes déroulantes.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)

15. Renseignez tous les paramètres vides et utilisez l' [adresse mis en pension GitHub ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) dans **repoUrl**. Ensuite, cliquez sur **Enregistrer**.
 
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)

    >[AZURE.NOTE] AutoScaling est une fonctionnalité proposée dans la couche **Standard** ou supérieure et au niveau du plan d’alertes sont fonctionnalités proposées dans la couche de **base** ou supérieur, vous devez définir le paramètre de **point de stock** **Standard** ou **Premium** pour voir toutes vos idées App nouveau ressources s’allumer.
    
16. Cliquez sur **déployer**. Si vous avez sélectionné **Enregistrer les mots de passe**, le mot de passe sera enregistré dans le paramètre fichier **en texte brut**. Dans le cas contraire, vous serez invité à entrer le mot de passe de base de données pendant le processus de déploiement.

Voilà ! Maintenant, il vous suffit d’accéder au [Portail Azure](https://portal.azure.com/) et l’outil [Explorateur de ressources Azure](https://resources.azure.com) pour afficher les nouvelles alertes et paramètres autoscale ajoutés à votre JSON déploiement l’application.

Les étapes de cette section principalement accompli les tâches suivantes :

1.  Préparer le fichier de modèle
2.  Création d’un fichier de paramètres pour le fichier de modèle
3.  Déployer le fichier de modèle avec le fichier de paramètres

La dernière étape est facilement effectuée par une applet de commande PowerShell. Pour voir ce que fait Visual Studio lors du déploiement de votre application, ouvrez Scripts\Deploy-AzureResourceGroup.ps1. Il y a beaucoup de code, mais je vais juste sélectionner tout le code pertinent que vous devez déployer le fichier de modèle avec le fichier de paramètres.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

L’applet de commande dernier, `New-AzureResourceGroup`, est celle qui effectue l’action. Pour vous, tout cela doit démontrer que, à l’aide d’outils, il est relativement simple à déployer votre application en nuage prévisible. Chaque fois que vous exécutez l’applet de commande sur le même modèle avec le même fichier de paramètres, vous allez obtenir le même résultat.

## <a name="summary"></a>Résumé ##

Dans DevOps, répétabilité et la prévisibilité sont des clés pour un déploiement réussi d’une application de grande échelle composée de microservices. Dans ce didacticiel, vous avez déployé une application de deux-microservice pour Azure sous la forme d’un groupe de ressources unique en utilisant le modèle de gestionnaire de ressources Azure. Heureusement, il vous a donné les connaissances que nécessaires pour entamer la conversion de votre application dans Azure dans un modèle de mise en service et déployer de manière prévisible. 

## <a name="next-steps"></a>Étapes suivantes ##

Découvrez comment [appliquer des méthodologies agiles et publiez continuellement votre application microservices avec facilité](app-service-agile-software-development.md) et déploiement avancées techniques telles que le [déploiement flighting](app-service-web-test-in-production-controlled-test-flight.md) facilement.

<a name="resources"></a>
## <a name="more-resources"></a>Plus de ressources ##

-   [Langue du modèle de gestionnaire de ressources Azure](../resource-group-authoring-templates.md)
-   [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md)
-   [Fonctions de modèle de gestionnaire de ressources Azure](../resource-group-template-functions.md)
-   [Déployer une application avec le modèle de gestionnaire de ressources Azure](../resource-group-template-deploy.md)
-   [À l’aide de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md)
-   [Résolution des problèmes de déploiement de groupe de ressources dans Azure](../resource-manager-troubleshoot-deployments-portal.md)




 
