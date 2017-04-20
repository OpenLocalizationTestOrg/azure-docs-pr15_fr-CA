<properties
   pageTitle="Créer un modèle de déploiement d’application logique | Microsoft Azure"
   description="Apprenez à créer un modèle de déploiement d’application logique et l’utiliser pour la gestion des versions"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="create-a-logic-app-deployment-template"></a>Créer un modèle de déploiement d’application logique

Après avoir créé une application de logique, vous pouvez souhaiter créer sous la forme d’un modèle de gestionnaire de ressources Azure. De cette façon, vous pouvez facilement déployer l’application logique à n’importe quel environnement ou d’un groupe de ressources où vous en aurez besoin. Pour une introduction aux modèles du Gestionnaire de ressources, n’oubliez pas de consulter les articles sur la [Création de modèles d’Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md) et de [déploiement de ressources à l’aide des modèles du Gestionnaire de ressources Azure](../resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modèle de déploiement d’application logique

Une application de logique comporte trois composants de base :

* **Ressource d’application logique**. Cette ressource contient des informations sur des éléments tels que les prix de plan, l’emplacement et la définition de flux de travail.
* **Définition de flux de travail**. C’est ce qui s’affiche en mode code. Il comprend la définition des étapes de flux et comment le moteur doit exécuter. Il s’agit de la `definition` propriétés de la ressource de l’application logique.
* **Les connexions**. Il s’agit des ressources distinctes qui stockent les métadonnées sur toutes les connexions connecteur, par exemple une chaîne de connexion et un jeton d’accès de manière sécurisée. Vous faites référence à ces éléments dans une application de logique dans les `parameters` section de la ressource de l’application logique.

Vous pouvez afficher ces éléments pour les applications de logique existant à l’aide d’un outil comme [l’Explorateur de ressources Azure](http://resources.azure.com).

Pour créer un modèle pour une application de logique à utiliser avec le déploiement de groupe de ressources, vous devez définir les ressources et paramétrer en fonction des besoins. Par exemple, si vous déployez dans un environnement de production, de test et de développement, vous souhaiterez probablement utiliser les différentes chaînes de connexion à une base de données SQL dans chaque environnement. Ou, vous pouvez déployer dans différents abonnements ou les groupes de ressources.  

## <a name="create-a-logic-app-deployment-template"></a>Créer un modèle de déploiement d’application logique

Pour avoir un modèle de déploiement d’application logique valide le plus simple est d’utiliser les [Outils Visual Studio pour applications de logique](./app-service-logic-deploy-from-vs.md).  Les outils de Visual Studio génèrent un modèle de déploiement valide qui peut être utilisé sur n’importe quel abonnement ou l’emplacement.

Quelques autres outils peuvent vous aider lorsque vous créez un modèle de déploiement d’application logique. Vous pouvez créer d’à la main, c'est-à-dire, en utilisant les ressources déjà décrites ici pour créer des paramètres en fonction des besoins. Une autre option consiste à utiliser un module PowerShell de [créateur de modèle logique app](https://github.com/jeffhollan/LogicAppTemplateCreator) . Ce module open source évalue d’abord l’application logique et toutes les connexions qu’il utilise et génère ensuite les ressources de modèle avec les paramètres nécessaires pour le déploiement. Par exemple, si vous avez une application de logique qui reçoit un message d’une file d’attente du Bus des services Azure et ajoute des données à une base de données SQL d’Azure, l’outil de conserver l’ensemble de la logique d’orchestration et paramétrer les chaînes de connexion SQL et le Bus des services afin qu’elles peuvent être définies au moment du déploiement.

>[AZURE.NOTE] Les connexions doivent être dans le même groupe de ressources que l’application de la logique.

### <a name="install-the-logic-app-template-powershell-module"></a>Installez le module PowerShell de logique application modèle

La façon la plus simple pour installer le module est via la [Galerie de PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), à l’aide de la commande `Install-Module -Name LogicAppTemplate`.  

Vous pouvez également installer le module PowerShell manuellement :

1. Télécharger la version la plus récente du [créateur de modèle logique app](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Extraire le dossier dans votre dossier de module PowerShell (généralement `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Pour le module fonctionne avec tout accès clients et abonnement jeton, nous vous recommandons d’utiliser l’outil de ligne de commande [ARMClient](https://github.com/projectkudu/ARMClient) .  Ce [billet de blog](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) explique les ARMClient plus en détail.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Générer un modèle d’application logique à l’aide de PowerShell

Une fois que PowerShell est installé, vous pouvez générer un modèle à l’aide de la commande suivante :

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId`est l’ID d’abonnement Azure. Cette ligne obtient d’abord un accès à un jeton via ARMClient, tuyaux à l’aide du script PowerShell puis crée ensuite le modèle dans un fichier JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Ajouter des paramètres à un modèle d’application logique

Après avoir créé votre modèle d’application logique, vous pouvez continuer à ajouter ou modifier les paramètres que vous devrez peut-être. Par exemple, si votre définition inclut un ID de ressource à une fonction Azure ou le workflow imbriqué que vous envisagez de déployer dans un seul déploiement, vous pouvez ajouter des ressources à votre modèle d’et paramétrer l’ID en fonction des besoins. Le même s’applique à toutes les références à l’API personnalisés ou des Swagger les points de terminaison que vous pensez déployer avec chaque groupe de ressources.

## <a name="deploy-a-logic-app-template"></a>Déployer un modèle d’application logique

Vous pouvez déployer votre modèle à l’aide de n’importe quel nombre d’outils, y compris le déploiement du modèle Azure Portal, la gestion de version Visual Studio, PowerShell et API REST. Consultez cet article sur le [déploiement des ressources à l’aide des modèles du Gestionnaire de ressources Azure](../resource-group-template-deploy.md) pour plus d’informations. En outre, nous vous recommandons que vous créez un [fichier de paramètres](../resource-group-template-deploy.md#parameter-file) pour stocker les valeurs pour le paramètre.

### <a name="authorize-oauth-connections"></a>Autoriser les connexions OAuth

Après le déploiement, l’application logique fonctionne de bout en bout avec des paramètres valides. Toutefois, OAuth connexions toujours devront être autorisé à générer un jeton d’accès valide. Vous pouvez pour cela en ouvrant l’application logique dans le concepteur, puis autoriser les connexions. Ou bien, si vous souhaitez automatiser, vous pouvez utiliser un script pour consentir à chaque connexion OAuth. GitHub sous le projet [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) est un exemple de script.

## <a name="visual-studio-release-management"></a>Gestion des versions de Visual Studio

Un scénario courant pour le déploiement et la gestion d’un environnement consiste à utiliser un outil tel que la gestion de version Visual Studio, avec un modèle de déploiement d’application logique. Visual Studio Team Services inclut une tâche de [Déploiement de groupe de ressources Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que vous pouvez l’ajouter à n’importe quelle build ou la version du pipeline. Vous devez disposer d’un [service principal](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) pour l’autorisation de déploiement, et vous pouvez ensuite générer la définition de la version.

1. Dans la gestion des versions, pour créer une nouvelle définition, sélectionnez **vide** pour démarrer avec une définition vide.

    ![Créer une définition de vide][1]   

1. Sélectionnez les ressources que vous avez besoin pour cela. Il s’agit probablement pas le modèle d’application logique généré manuellement ou dans le cadre du processus de génération.
1. Ajouter une tâche de **Déploiement de groupe de ressources Azure** .
1. Configurer avec un [service principal](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)et les fichiers de modèle et les paramètres de modèle de référence.
1. Continuer à générer des étapes du processus de publication pour n’importe quel environnement, test automatisé ou approbateurs en fonction des besoins.

<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG
